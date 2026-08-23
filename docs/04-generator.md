# 04 — Der Generator

## Grundsatz: Der Generator ist kein LLM, sondern eine Pipeline

Ein LLM allein halluziniert Orte, Preise und Zugverbindungen. Deshalb gilt:

> **Das LLM wählt aus und formuliert. Es erfindet nichts.**

Alle Orte, Hotels, Preise, Fahrzeiten und Öffnungszeiten kommen aus unserer
Datenbank bzw. von Partner-APIs. Das LLM bekommt Kandidatenlisten mit IDs und
darf nur IDs zurückgeben. Jede ID wird nach der Antwort validiert; unbekannte IDs
werden verworfen und nachgezogen.

## Pipeline-Übersicht

```
Onboarding-Antworten (TripBrief)
        │
   ┌────▼─────────────────────────────────────────────┐
   │ 1. Normalisieren & Ableiten                      │  deterministisch
   │    Saison, Nächte-Budget, Zielgewichte           │
   ├──────────────────────────────────────────────────┤
   │ 2. Routenwahl  (Städte + Nächteverteilung)       │  Solver + LLM
   ├──────────────────────────────────────────────────┤
   │ 3. Intercity-Transport & Reihenfolge             │  deterministisch
   ├──────────────────────────────────────────────────┤
   │ 4. Area-Aufteilung pro Stadt                     │  Clustering
   ├──────────────────────────────────────────────────┤
   │ 5. POI-Auswahl je Area                           │  Scoring + LLM
   ├──────────────────────────────────────────────────┤
   │ 6. Unterkunft je Stadt                           │  Filter + Ranking
   ├──────────────────────────────────────────────────┤
   │ 7. Flüge                                         │  API + Ranking
   ├──────────────────────────────────────────────────┤
   │ 8. Budget-Berechnung                             │  deterministisch
   ├──────────────────────────────────────────────────┤
   │ 9. Validierung & Reparatur                       │  Regelwerk
   ├──────────────────────────────────────────────────┤
   │ 10. Texte & Begründungen                         │  LLM
   └────┬─────────────────────────────────────────────┘
        ▼
     Trip-Objekt (siehe 09-datenmodell.md)
```

---

## Schritt 1 — Normalisieren & Ableiten

Aus dem `TripBrief` werden deterministisch abgeleitet:

| Ableitung | Regel |
|---|---|
| `season` | aus `start_date` bzw. Monat: `sakura`, `spring`, `rainy` (Jun), `summer`, `typhoon` (Sep), `autumn`, `momiji` (Nov), `winter` |
| `usable_nights` | `duration_days - 1` (letzte Nacht = Abflug), minus 1 wenn Ankunft nach 18:00 |
| `max_destinations` | `relaxed`: ⌊nights/4⌋ · `normal`: ⌊nights/3⌋ · `packed`: ⌊nights/2.5⌋, hart gedeckelt auf 6 |
| `items_per_area` | `relaxed`: 3–4 · `normal`: 5 · `packed`: 6–7 |
| `budget_split` | Startverteilung der Kostenkategorien, siehe `08-budget-engine.md` |
| `interest_vector` | normalisierter Gewichtsvektor über alle Interessens-Tags |

## Schritt 2 — Routenwahl

**Fall A — `route_control = user`:** Städte übernehmen, nur Nächte verteilen.

**Fall B — `route_control = mixed`:** Fixe Städte gesetzt, Rest auffüllen bis
`max_destinations` erreicht ist.

**Fall C — `route_control = ai`:** Vollständige Routenwahl.

### Städte-Scoring

Jede Stadt hat in der DB einen Interessens-Fitvektor (`city.interest_fit`, 0–1
pro Tag) sowie Saison-Modifikatoren.

```
city_score =
      Σ (interest_weight_i × city.interest_fit_i)          // Interessenspassung
    + season_bonus(city, season)                            // z. B. Hokkaido/Winter +0.4
    + classics_modifier(city, classics_level)               // Kyoto bei „few classics“ −0.2
    − travel_cost_penalty(city, current_route)              // Umwege bestrafen
    − budget_penalty(city, budget_level)                     // teure Regionen bei Sparbudget
    + freeform_bonus(city)                                  // aus Q16 extrahierte Wünsche
```

**Harte Regeln (nicht verhandelbar):**

- Eine Stadt bekommt nie weniger als 2 Nächte (Ausnahme: Hakone/Nara als 1-Nacht-Stopp, explizit als `stopover` typisiert).
- Route ist geografisch monoton: keine Zickzack-Fahrten (Tokyo → Hiroshima → Kyoto → Osaka ist verboten). Umsetzung: Städte auf der Nord-Süd-Achse sortieren und nur benachbarte Sprünge zulassen, ausser die Route ist explizit als Open-Jaw geplant.
- Ankunftsstadt = Stadt beim Ankunftsflughafen; Abflugstadt = Stadt beim Abflughafen (Open-Jaw NRT→KIX wird aktiv vorgeschlagen, wenn es Reisezeit spart).
- Maximal ein Intercity-Transfer pro Tag.
- Letzte Nacht in Flughafennähe, wenn Abflug vor 10:00.

### Nächteverteilung

Startverteilung nach `city.min_nights` / `city.ideal_nights`, dann Restnächte an
die Städte mit dem höchsten `city_score` verteilen. Tokyo/Kyoto absorbieren
zusätzliche Nächte gut, Hakone/Nara nicht (Deckel in `city.max_nights`).

Das LLM bekommt am Ende Routenvorschlag + Alternativen und darf **eine** Route
wählen und begründen — aber nur aus der vom Solver erzeugten Kandidatenmenge
(typisch 3–5 Routen).

## Schritt 3 — Intercity-Transport

Deterministisch aus einer Verbindungstabelle (`connections`): Für jedes
aufeinanderfolgende Stadtpaar Shinkansen/Express/Bus/Inlandsflug mit Dauer,
Preis, Umsteigen, JR-Pass-Abdeckung. Auswahl nach `pace` (packed → schnellste)
und Budget (sparsam → Bus/Nachtbus als Option, aber nie als Default für Familien).

Der JR-Pass-Rechner läuft hier: Summe der Einzelfahrten vs. Pass-Preise (7/14/21
Tage, regionale Pässe). Ergebnis wird als eigener Block im Trip gespeichert.

## Schritt 4 — Area-Aufteilung

Pro Stadt werden POIs geografisch geclustert (vordefinierte Areas mit Polygonen
in der DB, z. B. Tokyo: Shibuya/Harajuku, Shinjuku, Akihabara, Asakusa/Ueno,
Ginza/Tsukiji, Odaiba, Nakameguro/Daikanyama, Shimokitazawa, Yanaka …).

Anzahl Areas = Anzahl Nächte (bzw. Nächte + 1 bei `packed`). Areas werden nach
Interessenspassung ausgewählt, nicht nach Bekanntheit:

- Anime/TCG-Nutzer bekommt Akihabara + Nakano + Ikebukuro.
- Food-Nutzer bekommt Tsukiji + Shinjuku Omoide Yokocho + Ebisu.
- Tradition-Nutzer bekommt Asakusa + Yanaka.

Tagesausflüge (Nikko, Kamakura, Hakone ab Tokyo; Nara, Uji ab Kyoto) sind Areas
vom Typ `day_trip` mit eigener Transportangabe und werden nur bei `pace ≠ relaxed`
oder ausdrücklichem Interesse gesetzt.

## Schritt 5 — POI-Auswahl

Für jede Area wird eine Kandidatenliste (Top ~40 nach Score) gebildet:

```
poi_score =
      1.00 × interest_match(poi.tags, interest_vector)
    + 0.35 × quality(poi.editorial_rating, poi.review_score)
    + 0.25 × classics_fit(poi.tourist_density, classics_level)
    + 0.25 × local_fit(poi.english_support, poi.cash_only, local_level)
    + 0.20 × season_fit(poi, season)          // Kirschblüte, Herbstlaub, Illumination
    + 0.15 × novelty(poi.popularity_rank)      // Hidden Gems leicht bevorzugen
    − 0.30 × budget_conflict(poi.price_level, budget_level)
    − 0.50 × accessibility_conflict(poi, party)  // Kinderwagen, Kleinkinder, Mobilität
    − 1.00 × hard_block(poi)                   // geschlossen im Zeitraum, dauerhaft zu, Alterslimit
```

Aus den Kandidaten wählt das LLM `items_per_area` Stück mit dem Auftrag:

- **Mischung erzwingen:** pro Area mindestens 1 Food-Item, max. 2 Items derselben Unterkategorie (nicht 5 Anime-Shops hintereinander), es sei denn `interest_weight = 2.0`.
- **Gehbarkeit:** Alle Items einer Area innerhalb ~2,5 km Gehradius oder 2 Stationen.
- **Öffnungszeiten-Konflikte vermeiden:** kein Ort, der am geplanten Wochentag geschlossen ist (nur relevant in `Day mode`; im `Flexible mode` als Hinweis „Mo geschlossen“).
- **Begründungspflicht:** Zu jedem Item ein Satz, warum es zu *diesem* Nutzer passt (`item.reason`), z. B. „Weil du One Piece angegeben hast — hier steht die grösste Filiale Japans.“

Nicht gewählte Kandidaten werden als `area.alternatives[]` gespeichert. Das macht
`Replace` sofort schnell und ohne neuen LLM-Call.

## Schritt 6 — Unterkunft

Filter: Stadt, Datum, Reisendenzahl, Zimmerkonfiguration, `hotel_styles`,
Preisband aus dem Budget-Split. Ranking erzeugt genau drei Vorschläge:

| Label | Optimierung |
|---|---|
| **Best overall** | Gewichtete Kombination aus Preis, Lage, Bewertung |
| **Best budget** | Günstigstes Hotel mit Bewertung ≥ 8.0 und akzeptabler Lage |
| **Best location** | Minimale Summe der Wege zu den geplanten Areas |

„Lage“ ist keine Bauchentscheidung: `location_score` = gewichtete Reisezeit vom
Hotel zu allen geplanten Areas dieser Stadt (ÖV-Matrix), plus Nähe zum
Hauptbahnhof für An-/Abreise.

Bei `hotel_autoselect = true` wird **Best overall** vorausgewählt, die anderen
bleiben als `Change`-Optionen sichtbar.

## Schritt 7 — Flüge

Nur bei `include_flights`. Suche über Flug-API (siehe `11-booking-affiliate.md`)
mit `origin_airport` → Japan (NRT/HND/KIX/ITM/CTS/FUK), Datumsbereich ±2 Tage bei
`date_precision ≠ exact`. Drei Optionen: **Cheapest**, **Fastest/Direct**,
**Best value**. Open-Jaw wird geprüft und vorgeschlagen, wenn es die Route
vereinfacht.

## Schritt 8 — Budget

Vollständig deterministisch, siehe `08-budget-engine.md`. Ergebnis ist eine
Kostenaufstellung pro Kategorie plus `remaining`.

## Schritt 9 — Validierung & Reparatur

Ein Regelwerk prüft den generierten Trip **vor** der Ausgabe. Verstösse werden
automatisch repariert oder als Warnung im Trip markiert:

| Regel | Verstoss → Aktion |
|---|---|
| Summe Nächte = `usable_nights` | Nächte neu verteilen |
| Jede Stadt hat ≥ `min_nights` | Stadt entfernen, Nächte umverteilen |
| Jede Area hat ≥ 2 Items | Aus `alternatives` auffüllen |
| Kein POI doppelt im Trip | Duplikat entfernen, ersetzen |
| Alle POI-IDs existieren | Unbekannte IDs verwerfen + nachziehen |
| Transfer-Zeit pro Tag ≤ 5 h | Route glätten |
| Reservierungspflichtige POIs sind markiert | Flag setzen, in Checkliste aufnehmen |
| Budget ≤ 115 % des Ziels | Günstigere Hotels/Aktivitäten wählen, dann warnen |
| Keine geschlossenen/abgerissenen POIs | Hart blockieren |

Ein Trip, der die Validierung nach zwei Reparaturrunden nicht besteht, wird nicht
ausgeliefert — stattdessen ein reduzierter, sicherer Plan plus Hinweis.

## Schritt 10 — Texte

Das LLM schreibt am Ende: Trip-Titel („14 Tage Anime, Ramen und Berge“),
Destination-Intros (2–3 Sätze), `item.reason` pro POI, und die
Kombinationshinweise („Diese 5 Dinge lassen sich geografisch gut an einem Tag
kombinieren“). Alles auf Basis bereits validierter Daten.

## Generator-UI (Streaming)

Der Nutzer sieht keinen Spinner, sondern den Aufbau:

```
✓ Analysiere deine Interessen
✓ Wähle Route: Tokyo → Hakone → Kyoto → Osaka
✓ Verteile 13 Nächte
◐ Suche Orte in Tokyo …            (Areas erscheinen einzeln)
○ Unterkünfte
○ Budget
```

Die Destination-Blöcke werden progressiv gerendert, sobald sie fertig sind
(Server-Sent Events). Ziel: erster sichtbarer Inhalt < 3 s, vollständig < 25 s.

## Determinismus & Regenerierung

- Jeder Generierungslauf speichert `seed`, `brief_hash`, `model_version`,
  `db_snapshot_version` → identische Eingaben ergeben identische Ausgaben.
- **Regenerate** bietet drei Varianten:
  `Same brief, different picks` (neuer Seed) ·
  `More adventurous` (local_level +1, novelty-Gewicht ×2) ·
  `Cheaper` (Budget-Ziel −20 %).
- **Gepinnte Elemente bleiben immer erhalten** (`locked: true`) — auch bei
  vollständiger Regenerierung. Das ist die Brücke zwischen den beiden Modi.

## Prompt-Injection & Sicherheit

- `freeform_notes` und alle nutzergenerierten Texte werden dem LLM als klar
  markierte Daten übergeben (`<user_notes>` … `</user_notes>`), nie als Instruktion.
- Das LLM hat keine Tool-Rechte ausser der Kandidatenauswahl; es kann weder
  buchen noch Daten schreiben.
- Ausgabe wird gegen ein striktes JSON-Schema validiert; freie Textfelder werden
  längenbegrenzt und HTML-escaped.
- Rate-Limits pro IP/Account, weil jeder Generierungslauf Geld kostet.

## Kostenkontrolle

- Kandidatenlisten werden vor dem LLM-Call auf das Nötige reduziert (IDs + Name +
  Tags + 1 Zeile, nicht ganze Datensätze).
- Schritte 1, 3, 4, 8, 9 laufen ohne LLM.
- Ergebnis-Caching auf `brief_hash`: identische Briefs (häufig bei „14 Tage
  Standard-Klassiker“) werden aus dem Cache bedient und nur variiert.
