# 03 — Onboarding-Fragebogen

## Ziel

Ein Nutzer, der nichts über Japan weiss, soll in **30–60 Sekunden** genug
Informationen liefern, damit der Generator einen glaubwürdigen Plan bauen kann.
Jede Frage muss sich rechtfertigen: *Ändert die Antwort das Ergebnis messbar?*
Wenn nein → raus oder in die Nachbearbeitung verschieben.

## Prinzipien

- **Ein Screen = eine Frage** (mobil), max. zwei zusammengehörige Fragen (Desktop).
- **Immer überspringbar.** Jede Frage hat einen sinnvollen Default. `Skip` ist
  nie eine Sackgasse — die AI trifft dann eine Annahme und markiert sie im Plan
  mit „Angenommen: …“, änderbar mit einem Klick.
- **Progress sichtbar**, aber ohne exakte Zahl („Schritt 4 von ~9“), weil der
  Flow konditional ist.
- **Antworten sind reversibel.** Jede Antwort ist später im Trip unter
  `Trip settings` editierbar und triggert dort ggf. eine Neuberechnung.
- **Kein Account-Zwang.** Der Fragebogen läuft gegen einen anonymen Draft-Trip.

## Frageablauf

### Q1 — Reisedauer

```
How long is your trip?
[ 7 ] [ 10 ] [ 14 ] [ 21 ] [ Custom: __ days ]
```
- Feld: `trip.duration_days` (int, 3–30)
- Pflicht: ja (oder aus Q2 abgeleitet)
- Validierung: < 5 Tage → Hinweis „Wir empfehlen dann max. 2 Städte“; > 21 Tage → Hinweis auf Visa/Aufenthaltsdauer (90 Tage visumfrei für CH/EU).

### Q2 — Reisedaten

```
When are you going?
( ) Exact dates      → Datepicker (Range)
( ) Rough month      → Monatsauswahl + „ca. Anfang/Mitte/Ende“
( ) Not decided yet  → wir zeigen dir die beste Reisezeit
```
- Felder: `trip.start_date`, `trip.end_date` (ISO), `trip.date_precision` = `exact` | `month` | `unknown`
- Wirkung: Saison bestimmt Empfehlungen massiv (Sakura Ende März–April, Momiji November, Schnee/Ski Dez–Feb, Sommer schwül + Taifunrisiko Aug/Sept). Bei `unknown` schlägt der Generator zwei Zeitfenster vor.

### Q3 — Startflughafen (nur wenn Flüge geplant werden, siehe Q12)

```
Where do you fly from?
[ Autocomplete: ZRH, GVA, BSL, MUC, FRA, VIE … ]
```
- Feld: `trip.origin_airport` (IATA)
- Default: aus IP-Region geraten, vorbelegt und sichtbar änderbar.

### Q4 — Budget

```
What's your budget?
Per person, everything included (flights, hotels, transport, food, activities)

[ Slider: CHF 1'500 ——●—— CHF 12'000 ]     [ ] I don't have a fixed budget

Currency: [CHF ▾]
```
- Felder: `trip.budget_total` (int, minor units), `trip.currency` (ISO 4217), `trip.budget_mode` = `fixed` | `flexible`
- Live-Feedback unter dem Slider (ehrlich, nicht schönfärberisch):
  *„CHF 3'000 für 14 Tage ab ZRH ≈ Mittelklasse-Hotels, Shinkansen 2. Klasse, gutes Essen. Realistisch.“*
  *„CHF 1'800 für 14 Tage ist knapp: Hostels/Business-Hotels, wenig Intercity-Transport.“*
- Diese Kalibrierung ist ein Kernvorteil gegenüber generischen Planern.

### Q5 — Reisegruppe

```
Who's going?
( ) Solo   ( ) Couple   ( ) Friends   ( ) Family
```
- Feld: `trip.party_type`
- Follow-up bei `friends`/`family`: `trip.party_adults`, `trip.party_children` (+ Alter der Kinder, weil das POI-Auswahl und Hotelzimmer beeinflusst).
- Wirkung: Zimmerkonfiguration, Nightlife-Gewichtung, kinderfreundliche POIs, Kapselhotel ja/nein.

### Q6 — Ortskenntnis / Kontrolle über die Route

```
Do you know where you want to go?
( ) Yes      → I'll pick the cities
( ) Somewhat → I have some fixed, you fill the rest
( ) No       → Build a route based on my interests
```
- Feld: `trip.route_control` = `user` | `mixed` | `ai`
- Bei `yes`/`somewhat`: Städte-Picker (Suchfeld + Karte + Chips der 18 unterstützten Städte). Gewählte Städte → `trip.fixed_destinations[]`.
- Bei `somewhat` zusätzlich: „Welche sind gesetzt?“ (Pin-Symbol) vs. „nice to have“.

### Q7 — Interessen (Mehrfachauswahl, gewichtet)

```
What are you into?  (pick as many as you like)

[Anime & Manga] [TCG / Pokémon] [Food] [Tradition & Temples]
[Nature & Hiking] [Shopping] [Nightlife] [Onsen]
[Gaming & Arcades] [History] [Photography] [Art & Design]
[Music] [Sports] [Craft & Ceramics]
```
- Feld: `trip.interests[] = { id, weight }`
- Interaktion: Erster Klick = ausgewählt (weight 1.0). Zweiter Klick = „Das ist mein Hauptgrund“ (weight 2.0, Stern-Badge). Dritter Klick = abgewählt.
- Max. 3 Hauptgründe, sonst gewichtet nichts mehr.
- Bei `anime`: optionales Follow-up „Welche Serien?“ (Freitext/Autocomplete) → `trip.anime_titles[]`, ermöglicht echte Drehort-Zuordnung.
- Bei `tcg`: Follow-up „Pokémon · Yu-Gi-Oh! · One Piece · Magic · Duel Masters“ → `trip.tcg_games[]`.
- Bei `food`: Follow-up „Allergien / vegetarisch / vegan / halal / kein Fisch“ → `trip.dietary[]` (in Japan planungsrelevant, nicht kosmetisch).

### Q8 — Reisetempo

```
How fast do you want to travel?
( ) Relaxed   — 2–3 things a day, lots of café time
( ) Normal    — 4–5 things a day
( ) Packed    — see as much as possible
```
- Feld: `trip.pace` = `relaxed` | `normal` | `packed`
- Wirkung: Items pro Area (3 / 5 / 7), Mindestnächte pro Stadt, Anzahl Städte, Gehdistanz pro Tag.

### Q9 — Touristenklassiker

```
How much of the classic stuff do you want?
( ) A lot — it's my first time, I want the icons
( ) Some  — the best ones, plus lesser-known places
( ) As little as possible — I want local
```
- Feld: `trip.classics_level` = `many` | `some` | `few`

### Q10 — Local/Authentic-Level

```
How adventurous are you?
( ) Comfortable — English menus, easy access
( ) Mixed       — some local places, some tourist-friendly
( ) Deep local  — no English, cash only, tiny places — bring it on
```
- Feld: `trip.local_level` = `comfortable` | `mixed` | `deep`
- Wirkung: Filter auf POI-Attribute `english_support`, `cash_only`, `tourist_density`, `booking_difficulty`.
- Q9 und Q10 sind bewusst getrennt: „wenig Touristenklassiker“ ≠ „ich komme mit japanisch-only zurecht“.

### Q11 — Unterkunft

```
Should we plan accommodation?
( ) Yes, suggest hotels
( ) Yes, but I only want to see options — no pre-selection
( ) No, I'll handle it
```
- Felder: `trip.include_hotels` (bool), `trip.hotel_autoselect` (bool)
- Follow-up bei Yes: Stilpräferenz `[Business] [Boutique] [Ryokan] [Hostel] [Apartment]` (Mehrfach) → `trip.hotel_styles[]`, und Prioritäts-Slider `Preis ←→ Lage` → `trip.hotel_priority` (0–1).

### Q12 — Flug

```
Should we plan flights?
( ) Yes   ( ) No, I'll book flights myself
```
- Feld: `trip.include_flights` (bool). Bei `yes` → Q3 wird gestellt.
- Bei `no` wird die gesamte Flug-UI aus dem Trip ausgeblendet, aber ein Ankunfts-/Abflug-Flughafen wird trotzdem abgefragt (für Routenlogik: Ankunft NRT/HND, Abflug ab KIX spart Rückweg) → `trip.arrival_airport`, `trip.departure_airport`.

### Q13 — Intercity-Transport

```
Should we plan transport between cities?
( ) Yes — with JR Pass check
( ) Yes — without pass, single tickets
( ) No
```
- Felder: `trip.include_intercity` (bool), `trip.jr_pass_check` (bool)
- Der JR-Pass-Rechner (rentiert er sich bei dieser Route?) ist ein starkes, teilbares Feature — siehe `08-budget-engine.md`.

### Q14 — Aktivitäten buchbar machen

```
Do you want bookable activities?
( ) Yes — show me what I can book in advance
( ) Only where it's required (teamLab, Ghibli, theme parks …)
( ) No — just tell me what's there
```
- Feld: `trip.booking_level` = `all` | `required_only` | `none`

### Q15 — Ergebnistiefe

```
What do you want out of this?
( ) Inspiration — ideas and a rough route
( ) A complete trip — route, hotels, transport, activities, budget
```
- Feld: `trip.output_depth` = `inspiration` | `complete`
- Bei `inspiration` erzeugt der Generator Destination-Blöcke + POI-Vorschläge, aber keine Hotel-/Flugpreise. Upgrade jederzeit per Button im Dashboard („Turn this into a complete trip“).

### Q16 — Optionaler Freitext (der Joker)

```
Anything else we should know?   (optional)
z. B. „Ich will unbedingt zum Sumo“, „Meine Freundin hasst Menschenmengen“,
      „Wir haben einen 3-jährigen“, „Ich war schon zweimal in Tokyo“
```
- Feld: `trip.freeform_notes` (text, max 500 Zeichen)
- Wird als zusätzlicher Kontext an das Generator-LLM gegeben (nach Sanitizing, siehe `04-generator.md` → Prompt-Injection).
- Erfahrungsgemäss das Feld mit dem höchsten Qualitätsgewinn pro Aufwand.

## Konditionale Logik (Zusammenfassung)

| Bedingung | Effekt |
|---|---|
| `include_flights = false` | Q3 entfällt, stattdessen Ankunfts-/Abflughafen |
| `route_control = ai` | Städte-Picker entfällt |
| `output_depth = inspiration` | Q11–Q14 werden zu einem einzigen Screen zusammengefasst |
| `duration_days < 6` | Hinweis + Generator begrenzt auf max. 2 Destinationen |
| `party_children > 0` | Nightlife-Gewicht auf 0, Kinder-POI-Filter aktiv |
| `date_precision = unknown` | Generator liefert Saisonempfehlung statt Fixdaten |

## Abschluss-Screen

```
                    Ready.

  14 days · Zurich → Japan · CHF 3'000 · Anime, Food, Nature
  Relaxed pace · Some classics · Hotels included

              [ Generate my trip ]

              Change something first ↑
```

Die Zusammenfassung ist klickbar: Jeder Chip springt zur jeweiligen Frage zurück.

## Persistenz & Wiederaufnahme

- Antworten werden nach jeder Frage in `localStorage` **und** serverseitig gegen
  einen anonymen `draft_id` gespeichert (Cookie).
- Abbruch und Rückkehr innerhalb von 30 Tagen → „Weitermachen, wo du aufgehört hast?“
- Nach Account-Erstellung wird der Draft dem Nutzer zugeordnet (`draft_id` → `user_id`).

## Barrierefreiheit

- Vollständige Tastaturbedienung, Zahlen 1–9 wählen Optionen direkt.
- Jede Frage hat ein `<fieldset>` + `<legend>`, Fokus springt beim Screenwechsel auf die Frage.
- Slider haben immer ein gleichwertiges Zahlen-Eingabefeld.
