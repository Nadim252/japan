# 07 — Explore-Datenbank

## Zweck

Explore ist die Antwort auf „Ich will noch mehr / etwas anderes“. Es ist
gleichzeitig:

1. **Das Werkzeug im Trip** — jedes `Explore more` landet hier, vorgefiltert.
2. **Der SEO-Kanal** — `/explore/tokyo/akihabara` und `/poi/super-potato-akihabara`
   sind öffentliche, statisch renderbare Seiten.
3. **Der Beweis für Tiefe** — wer hier stöbert, versteht, dass hinter dem Produkt
   eine echte Datenbank steckt und nicht nur ein Chatbot.

## Layout

```
🔍 More in Akihabara                                    [Karte] [Liste] [Bilder] [✕]

Suche: [ pokémon cards________ ]                       247 Orte

Kategorie   [Anime] [Manga] [Pokémon] [One Piece] [TCG] [Gaming] [Retro]
            [Food] [Ramen] [Cafés] [Shopping] [Tempel] [Natur] [Onsen]
            [Nightlife] [Museen] [Fotospots] [Hidden gems]

Filter      Preis: [gratis][¥][¥¥][¥¥¥]   Reservierung: [nicht nötig][empfohlen][nötig]
            Jetzt offen ☐   Englisch gesprochen ☐   Karte akzeptiert ☐
            Kinderfreundlich ☐   Barrierefrei ☐   Am 14. April offen ☐

Sortierung  [Empfohlen für dich ▾] Beliebtheit · Distanz · Preis · Bewertung · Geheimtipps

┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│ [Bild]       │ │ [Bild]       │ │ [Bild]       │
│ Card Shop    │ │ Super Potato │ │ Mandarake    │
│ Yellow Sub   │ │ Retro Gaming │ │ Complex      │
│ TCG · ¥¥     │ │ Gaming · ¥¥  │ │ Anime · ¥    │
│ 🟢 walk-in   │ │ 🟢 walk-in   │ │ 🟢 walk-in   │
│ 8 Min zu Fuss│ │ 4 Min        │ │ 6 Min        │
│ ★ 4.6 (1.2k) │ │ ★ 4.5        │ │ ★ 4.7        │
│ ✓ Im Trip    │ │ [+ Add]      │ │ [+ Add]      │
└──────────────┘ └──────────────┘ └──────────────┘
```

## Filter-Facetten (vollständig)

| Gruppe | Werte |
|---|---|
| **Kategorie** | guided_tour, attraction, food, café, bar, shopping, anime, manga, tcg, gaming, temple, shrine, museum, nature, park, onsen, viewpoint, photo_spot, experience, nightlife, market, event |
| **Interessen-Tags** | anime, one_piece, pokemon, ghibli, yugioh, magic, retro_gaming, arcade, ramen, sushi, izakaya, street_food, kaiseki, vegetarian, vegan, coffee, matcha, craft_beer, sake, whisky, tradition, history, architecture, art, design, fashion, vintage, nature, hiking, onsen, snow, sakura, momiji, photography, music, sports, sumo, baseball |
| **Preis** | free, ¥ (< 1'000), ¥¥ (1'000–3'000), ¥¥¥ (3'000–10'000), ¥¥¥¥ (> 10'000) |
| **Reservierung** | required, recommended, walk_in |
| **Praktisch** | open_now, open_on_date, english_support, card_accepted, cash_only, kid_friendly, wheelchair_accessible, luggage_storage, tattoo_friendly (Onsen!), late_night, early_morning |
| **Charakter** | tourist_classic, local_favourite, hidden_gem, instagram_famous, quiet |
| **Distanz** | Radius um eine Area, ein Hotel oder ein anderes Item |

`tattoo_friendly` und `cash_only` sind keine Spielereien — beide sind in Japan
regelmässig der Unterschied zwischen „funktioniert“ und „steht vor verschlossener Tür“.

## Sortierung „Empfohlen für dich“

Verwendet dieselbe `poi_score`-Formel wie der Generator (`04-generator.md`), aber
mit dem aktuellen Trip als Kontext: Distanz zu bereits geplanten Items, bereits
abgedeckte Kategorien (Diversität belohnen), entfernte Items abwerten.

Ohne Trip-Kontext (Standalone-Explore): Beliebtheit × redaktionelle Bewertung,
mit einem Anteil rotierender Hidden Gems.

## Kartenmodus

- Cluster-Pins, Farbe nach Kategorie, Trip-Items in Akzentfarbe.
- Polygon-Highlight der aktuellen Area.
- „Zeichne einen Bereich“ → Filter auf frei gezeichnetes Polygon.
- Klick auf Pin → Karten-Popover mit `+ Add to my trip`.
- Layer: geplante Route, Bahnlinien (JR / Metro), Gehradius 10/20 Minuten vom Hotel.

## Visuelle Entdeckung („zeig mir bildlich, wo was ist“)

Explore hat drei gleichwertige Ansichten, umschaltbar oben rechts:

| Ansicht | Wofür |
|---|---|
| **Karte** | „Was ist in meiner Nähe / in dieser Gegend?“ |
| **Liste** | Vergleichen: Preis, Öffnungszeiten, Reservierung, Distanz |
| **Bilder** | Stöbern ohne Ziel — der eigentliche Inspirationsmodus |

Der **Bildermodus** ist ein dichtes, randloses Foto-Raster (Masonry, 3–5 Spalten),
in dem der Ort erst beim Hover/Tap benannt wird. Kein Kartentext, keine Metadaten
— nur das Bild. Wer nicht weiss, wonach er sucht, sucht visuell.

- Wischen/Scrollen lädt endlos nach, gefiltert nach den Trip-Interessen.
- Tap auf ein Bild → Vollbild mit Ortsname, Gegend, einer Zeile Beschreibung und
  den zwei Aktionen `+ Add to my trip` und `Mehr davon`.
- `Mehr davon` ist der wichtigste Knopf: Er setzt die Tags dieses Ortes als
  Filter — visuelles Weitersuchen statt Filterformular.
- Auf der Karte hat jeder Pin ab Zoomstufe 15 ein Foto-Thumbnail statt eines
  Icons. Eine Karte aus Bildern liest sich schneller als eine Karte aus Punkten.

**Bildqualität ist hier eine Produktanforderung, keine Ästhetik.** Ein POI ohne
gutes Bild erscheint im Bildermodus nicht — lieber 400 Orte mit starken Fotos als
1'200 mit Platzhaltern. Entsprechend ist `has_hero_image` ein Kriterium für
Tier 1 in `13-content-pipeline.md`.

## POI-Detailseite (`/poi/:slug`)

Öffentlich, SEO-optimiert, gleichzeitig als Drawer im Trip nutzbar:

- Galerie, Name (JP + Romaji + DE/EN), Kategorie, Tags
- Beschreibung (redaktionell, 80–150 Wörter, kein generischer LLM-Brei)
- Praktisches: Adresse (JP für Taxifahrer!), nächste Station, Öffnungszeiten mit Stand, Preise, Website, Telefon
- Reservierung: Stufe, wie und wo buchen, Vorlaufzeit, Affiliate-Link falls vorhanden
- „Gut kombinierbar mit“: 3–5 nahe POIs (das treibt die Trip-Ergänzung)
- Beste Zeit / Tipps (z. B. „vor 8:00 wegen Reisegruppen“)
- Bei Anime-Locations: Serie, Episode/Szene, Vergleichsbild Original ↔ real
- `+ Add to my trip` (oder „Start a trip with this“ ohne aktiven Trip)
- Strukturierte Daten (schema.org `TouristAttraction` / `Restaurant`) für Google

## Stadt-Guide-Seite (`/city/:slug`)

Redaktioneller Überblick, Areas mit Kurzcharakterisierung, „X Nächte reichen für
…“, beste Reisezeit, typische Kosten, Transportgrundlagen, Top-POIs nach Thema,
CTA „Plane deine Tage in Tokyo“ → Onboarding mit vorbelegter Stadt.

## Nutzer-eigene Einträge

Findet der Nutzer etwas, das wir nicht haben:

```
+ Add your own
Name: ____________  Kategorie: [▾]  Adresse/Link: ____________
Notiz: ____________
```

- Wird als `custom_poi` gespeichert, gehört dem Trip, nicht der globalen DB.
- Optional: „Für alle vorschlagen“ → landet im redaktionellen Review-Queue
  (siehe `13-content-pipeline.md`).
- Ohne diese Funktion wäre der „Build my own trip“-Modus wertlos, sobald jemand
  einen Ort will, den wir nicht kennen.

## Performance-Anforderungen

- Filterwechsel < 150 ms (clientseitige Facetten auf vorgeladenem Area-Index).
- Volltextsuche über Suchindex (Meilisearch/Typesense/Postgres FTS), Tippfehler-tolerant, JP + Romaji + DE/EN.
- Explore-Drawer öffnet ohne Full-Page-Reload und behält den Trip-Zustand.
