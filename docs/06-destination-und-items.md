# 06 — Destination-Seite, Item-Karten & die vier Aktionen

## Destination-Seite (`/trip/:tripId/d/:destinationId`)

```
← Zurück zum Trip

🇯🇵 TOKYO — 5 Nächte · 12.–17. April
Tokyo ist dein Anime- und Food-Anker. Fünf Nächte reichen für vier Stadtteile
plus einen Tagesausflug.                                       [Intro der AI]

┌─ 🏨 ACCOMMODATION ────────────────────────────────────────────────────┐
│ ✓ Hotel Gracery Shinjuku          CHF 128/Nacht · CHF 640 gesamt      │
│   8.6 ★ · 400 m zur Station · 12 Min zu deinen Areas im Schnitt       │
│   [View deal ↗]  [I'll book this myself]  [Change]  [Remove]          │
│                                                                       │
│   Alternativen:                                                       │
│   Best budget    APA Shinjuku Kabukicho      CHF 76/N   8.1★  [Wählen]│
│   Best location  Shinjuku Granbell           CHF 145/N  8.9★  [Wählen]│
│   Ryokan         Ryokan Kamogawa (Asakusa)   CHF 168/N  9.1★  [Wählen]│
│   Apartment      MIMARU Ueno (4 Pers.)       CHF 152/N  8.8★  [Wählen]│
│                                       [Mehr Unterkünfte ansehen →]    │
└───────────────────────────────────────────────────────────────────────┘

RECOMMENDED AREAS                              [+ Add area]  [🔍 Explore Tokyo]

┌─ Area 1 — Shibuya + Harajuku ──────────────── ca. 1 Tag · 5 Stops ────┐
│ 💡 Diese 5 Dinge lassen sich geografisch gut an einem Tag kombinieren │
│    — 2,1 km Gehweg, keine Umstiege.                                   │
│                                                                       │
│  1  Meiji Shrine                🕗 5:10–17:20 · gratis · walk-in      │
│     Ruhiger Waldweg mitten in der Stadt, morgens fast leer.           │
│     [Keep] [Remove] [Replace] [Explore more]                  ⋮       │
│                                                                       │
│  2  Takeshita Street            Shopping · gratis · walk-in           │
│  3  Shibuya Sky                 🎟 Reservierung empfohlen · ¥2'500    │
│  4  Mandarake Shibuya           Anime · gratis                        │
│  5  Ramen Nagi Shibuya          Food · ¥1'200 · 🕐 oft 20 Min Warten  │
│                                                                       │
│                                        [+ Add something to this area] │
└───────────────────────────────────────────────────────────────────────┘

┌─ Area 2 — Akihabara ───────────────────────── ca. 1 Tag · 6 Stops ────┐
│  1  One Piece Mugiwara Store   2  Pokémon Card Lounge                 │
│  3  Super Potato (Retro Gaming) 4  Mandarake Complex                  │
│  5  Maid Café (optional)        6  Kikanbo Ramen                      │
└───────────────────────────────────────────────────────────────────────┘

[+ Add area]        [✨ Suggest another area]        [🔍 Explore Tokyo]
```

## Die vier Aktionen — exakte Semantik

Jedes Item, jede Area, jedes Hotel und jeder Transport hat dieselben vier
Aktionen. Das ist die zentrale Interaktions-Grammatik des Produkts.

### `Keep`

- **Bedeutung:** „Das bleibt, egal was die AI später vorschlägt.“
- **Effekt:** Setzt `item.locked = true`. Gepinnte Elemente überleben
  Regenerierung, „Improve with AI“ und Budget-Optimierung.
- **Visuell:** Pin-Icon, leicht hervorgehobener Rand.
- **Wichtig:** `Keep` ist nicht „gesehen/erledigt“ — dafür gibt es im On-Trip-Modus separat `Done`.

### `Remove`

- **Effekt:** Item raus, Undo-Toast 10 s.
- **Folgeverhalten:** Sinkt eine Area unter 2 Items, schlägt das System sofort
  Ersatz aus `alternatives` vor („Area 1 hat nur noch 1 Stop — 3 Vorschläge?“).
- Wird ein Item mehrfach von verschiedenen Nutzern entfernt, sinkt sein globaler
  Empfehlungs-Score (Feedback-Loop, siehe `13-content-pipeline.md`).

### `Replace`

- Öffnet ein kompaktes Panel mit **3 Alternativen aus derselben Kategorie und
  Gegend**, sofort und ohne LLM-Call (vorberechnete `alternatives`).

```
Ersetze „Ramen Nagi Shibuya“ durch:
  ○ Ichiran Shibuya          Food · ¥1'100 · 350 m · touristischer
  ○ Kaikaya by the Sea       Food · ¥3'400 · 700 m · Reservierung nötig
  ○ Coffee Supreme Tokyo     Café · ¥800   · 400 m · anderer Typ
  [Andere Kategorie wählen ▾]              [Explore more →]
```

- Beim Ersetzen bleibt die Position in der Area erhalten.
- Der Grund für die Alternative wird angezeigt („weniger touristisch“, „günstiger“, „näher am nächsten Stop“).

### `Explore more`

- Öffnet die Explore-Datenbank **vorgefiltert auf den Kontext**: gleiche Area,
  passende Kategorien, Trip-Interessen als aktive Filter (siehe `07-explore-datenbank.md`).
- Aus Explore heraus fügt `+ Add to my trip` das Item direkt an dieser Stelle ein.

### Sekundäraktionen (`⋮`)

`Move to another area` · `Move to another destination` · `Mark as must-do` ·
`Add note` · `Add booking reference` · `Show on map` · `Open official website ↗` ·
`Report a problem` (geschlossen/umgezogen/falsche Info → Content-Feedback).

## Item-Karte — Datenfelder

| Feld | Beispiel | Anzeige |
|---|---|---|
| Name (lokal + lat.) | 明治神宮 / Meiji Shrine | Titel + Original für Taxi/Karten |
| Kategorie & Tags | `shrine`, `nature`, `photo` | Chips |
| Kosten | gratis / ¥2'500 / ¥¥ | Preis oder Preisstufe |
| Dauer | ca. 1–1,5 h | für Tagesplanung |
| Öffnungszeiten | 5:10–17:20, Mo geschlossen | mit Stand-Datum |
| Reservierungsstatus | `required` / `recommended` / `walk-in` | farbiges Badge |
| Beste Zeit | „früh morgens“, „bei Nacht“ | Hinweiszeile |
| Zugang | nächste Station + Gehminuten | |
| Barrierefreiheit | Kinderwagen, Aufzug, Rollstuhl | Icons |
| Zahlung | `cash only` | Icon (in Japan relevant) |
| `reason` | „Weil du Fotografie angegeben hast“ | kursive Zeile der AI |
| Anime-Referenz | „Your Name — Treppe Suga-Schrein“ | Sonderfeld für Anime-Locations |

## Reservierungs-Kategorien

Drei Stufen, überall konsistent farbcodiert:

| Stufe | Badge | Bedeutung | Beispiele |
|---|---|---|---|
| **Reservation required** | 🔴 rot | Ohne Vorabbuchung kein Eintritt | teamLab Planets, Ghibli Museum, Universal Studios Express, Kaiseki-Restaurants, Sumo-Turnier |
| **Reservation recommended** | 🟡 gelb | Möglich ohne, aber Wartezeit/Ausverkauf-Risiko | Shibuya Sky Sonnenuntergang, Pokémon Café, beliebte Ramen-Läden, Onsen-Ryokan |
| **Walk-in / free** | 🟢 grün | Einfach hingehen | Schreine, Parks, Shops, die meisten Restaurants |

Alle roten und gelben Items landen automatisch in der **Reservierungs-Checkliste**
(`/trip/:tripId/checklist`), sortiert nach „muss wie viele Tage vorher gebucht
werden“ — z. B. *Ghibli Museum: Verkauf startet am 10. des Vormonats, 10:00 JST*.
Diese Checkliste ist eines der wertvollsten Features für Japan speziell.

## Area-Ebene

- **Umbenennen** (Freitext), **Umsortieren** (Drag), **Zusammenlegen** (Merge, wenn geografisch nah), **Aufteilen** (Split).
- **Typ:** `city_area` | `day_trip` | `transit_day` | `free_day`.
- `free_day` ist ein bewusst leerer Tag („Puffer / freier Tag“) — bei `relaxed`
  setzt der Generator ab 10 Nächten automatisch einen.
- **Area-Statistik:** Gehdistanz gesamt, geschätzte Dauer, Kosten der Items,
  Warnung bei Überfüllung („7 Stops bei ‚relaxed‘ — das wird sportlich“).
- **Karte pro Area** mit nummerierter Route und vorgeschlagener Reihenfolge
  (Nearest-Neighbour + Öffnungszeiten), immer als Vorschlag, frei überschreibbar.

## Optionaler Schedule-Modus

Nur wenn der Nutzer ihn aktiviert. Dann werden Uhrzeiten aus Dauer,
Öffnungszeiten und Wegzeiten berechnet und als **bearbeitbare** Zeitspalte
angezeigt:

```
09:00  Meiji Shrine            1,5 h
10:45  Takeshita Street        1 h        🚶 8 Min
12:00  Ramen Nagi              45 Min     🚶 12 Min
...
```

Konflikte (geschlossen, zu knapp, Öffnungszeit verpasst) werden rot markiert mit
`[Fix]`. Der Nutzer kann jederzeit zurück auf `Flexible` schalten — die Uhrzeiten
bleiben gespeichert, werden nur nicht angezeigt.
