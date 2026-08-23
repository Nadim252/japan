# 09 — Datenmodell

## Grundidee

Das zentrale Objekt ist der **Trip**. Er besteht aus unabhängig editierbaren
Modulen. Jedes Modul kennt seine Herkunft (`source: ai | user | template`) und
seinen Sperrzustand (`locked`) — das ist die technische Grundlage dafür, dass
sich AI-Generierung und manuelles Bauen beliebig mischen lassen.

```
Trip
├── brief          (die Onboarding-Antworten, jederzeit editierbar)
├── flights        (outbound, return | null)
├── services       (eSIM, Versicherung, Transfers, Pässe)
├── destinations[] (geordnet)
│   ├── accommodation
│   ├── inbound_transport   (wie komme ich hierher)
│   └── areas[]
│       └── items[]
├── budget         (berechnet, nicht gespeichert – ausser Snapshot)
└── meta           (Titel, Ersteller, Sichtbarkeit, Version)
```

## Trip (JSON)

```jsonc
{
  "id": "trp_8f2k...",
  "version": 42,                        // steigt bei jeder Änderung (Optimistic Locking)
  "owner_id": "usr_...",                // null = anonymer Draft
  "title": "Anime, Ramen & Berge",
  "status": "draft | active | archived",
  "visibility": "private | link | public",
  "created_at": "2026-03-01T10:12:00Z",
  "updated_at": "2026-03-04T18:02:11Z",
  "generation": {
    "seed": 918273,
    "brief_hash": "sha256:...",
    "model_version": "gen-2026-03",
    "db_snapshot": "2026-02-28"
  },

  "brief": {
    "duration_days": 14,
    "start_date": "2026-04-12",
    "end_date": "2026-04-26",
    "date_precision": "exact",
    "origin_airport": "ZRH",
    "arrival_airport": "NRT",
    "departure_airport": "KIX",
    "budget_total": 300000,             // minor units in `currency`
    "currency": "CHF",
    "budget_mode": "fixed",
    "party_type": "couple",
    "party_adults": 2,
    "party_children": [],
    "route_control": "mixed",
    "fixed_destinations": ["tokyo"],
    "interests": [
      { "id": "anime", "weight": 2.0 },
      { "id": "food",  "weight": 1.0 },
      { "id": "nature","weight": 1.0 }
    ],
    "anime_titles": ["one_piece", "your_name"],
    "tcg_games": ["pokemon"],
    "dietary": [],
    "pace": "relaxed",
    "classics_level": "some",
    "local_level": "mixed",
    "include_hotels": true,
    "hotel_autoselect": true,
    "hotel_styles": ["business", "ryokan"],
    "hotel_priority": 0.6,              // 0 = Preis, 1 = Lage
    "include_flights": true,
    "include_intercity": true,
    "jr_pass_check": true,
    "booking_level": "required_only",
    "output_depth": "complete",
    "freeform_notes": "Wir wollen unbedingt einmal in einem Ryokan schlafen."
  },

  "flights": {
    "included": true,
    "status": "suggested | selected | self_booked | skipped",
    "selected_option_id": "flt_...",
    "options": [ /* FlightOption[] */ ],
    "self_booked_details": null
  },

  "services": [
    { "type": "esim",      "status": "suggested", "provider_id": "prov_...", "price_jpy": 3200 },
    { "type": "jr_pass",   "status": "rejected",  "reason": "not_worth_it", "savings_jpy": -8560 },
    { "type": "insurance", "status": "self_booked" }
  ],

  "destinations": [ /* Destination[] */ ],

  "unassigned_nights": 0,
  "warnings": [
    { "code": "closed_on_date", "item_id": "itm_...", "message": "Mo geschlossen" }
  ]
}
```

## Destination

```jsonc
{
  "id": "dst_1",
  "city_id": "tokyo",
  "nights": 5,
  "arrival_date": "2026-04-12",         // abgeleitet, nicht gespeichert wenn flexible
  "order": 0,
  "source": "ai",
  "locked": false,
  "intro": "Tokyo ist dein Anime- und Food-Anker …",

  "inbound_transport": {
    "id": "trn_...",
    "mode": "shinkansen | train | bus | flight | ferry | airport_transfer",
    "from": "airport:NRT",
    "to": "city:tokyo",
    "operator": "JR East",
    "duration_min": 63,
    "price_jpy": 3070,
    "jr_pass_covered": true,
    "reservation": "recommended",
    "status": "suggested | selected | self_booked | skipped"
  },

  "accommodation": {
    "id": "acc_...",
    "status": "suggested | selected | self_booked | skipped | none",
    "selected_hotel_id": "htl_...",
    "check_in": "2026-04-12",
    "check_out": "2026-04-17",
    "rooms": 1,
    "price_per_night_jpy": 21000,
    "total_jpy": 105000,
    "price_fetched_at": "2026-03-04T09:00:00Z",
    "options": [
      { "hotel_id": "htl_a", "label": "best_overall",  "score": 0.91 },
      { "hotel_id": "htl_b", "label": "best_budget",   "score": 0.78 },
      { "hotel_id": "htl_c", "label": "best_location", "score": 0.88 }
    ],
    "self_booked_details": null,
    "locked": false
  },

  "areas": [ /* Area[] */ ]
}
```

## Area

```jsonc
{
  "id": "area_1",
  "area_ref": "tokyo_shibuya_harajuku",  // null bei frei benannten Areas
  "title": "Shibuya + Harajuku",
  "type": "city_area | day_trip | transit_day | free_day",
  "order": 0,
  "date": "2026-04-13",                  // nur in Day/Schedule-Modus
  "source": "ai",
  "locked": false,
  "note": "💡 Diese 5 Dinge lassen sich gut an einem Tag kombinieren — 2,1 km.",
  "stats": { "walk_km": 2.1, "duration_min": 380, "cost_jpy": 3700 },
  "items": [ /* Item[] */ ],
  "alternatives": ["poi_...", "poi_..."] // vorberechnete Ersatzkandidaten
}
```

## Item

```jsonc
{
  "id": "itm_...",
  "poi_id": "poi_meiji_jingu",           // null bei custom_poi
  "custom": null,                        // { name, category, address, url } bei eigenen Einträgen
  "order": 0,
  "source": "ai | user | template",
  "locked": false,                        // = „Keep“
  "must_do": false,
  "status": "planned | booked | self_booked | done | removed",
  "reason": "Weil du Fotografie angegeben hast — morgens ist es hier fast leer.",
  "user_note": null,
  "booking": {
    "required": "required | recommended | walk_in",
    "booked_at": null,
    "reference": null,
    "url": "https://…",
    "lead_time_days": 30
  },
  "schedule": { "start": "09:00", "duration_min": 90 },  // nur im Schedule-Modus
  "price_jpy": 0,
  "replaced_from": "poi_…"               // für „Undo Replace“ und Analytics
}
```

## Referenzdaten (globale Datenbank, nicht Teil des Trips)

### `cities`

```
id, slug, name_en, name_de, name_jp, region, prefecture,
lat, lng, timezone,
min_nights, ideal_nights, max_nights,
interest_fit: { anime: 0.95, food: 0.9, nature: 0.2, ... },
season_modifiers: { winter: -0.1, sakura: 0.3, ... },
local_transport_cost_per_day_jpy,
airport_refs: ["NRT","HND"],
intro_md, hero_image, published
```

### `areas`

```
id, city_id, slug, title, polygon (GeoJSON),
character: ["anime","electronics","otaku"],
nearest_stations[], intro_md, typical_duration_min
```

### `pois`

```
id, slug, city_id, area_id,
name_en, name_jp, name_romaji,
category, tags[],                      -- Interessens-Tags
lat, lng, address_jp, address_en,
nearest_station, walk_min_from_station,
price_level, price_jpy, price_child_jpy,
duration_min,
opening_hours (JSON, inkl. Ausnahmen/Feiertage), closed_days[], hours_checked_at,
reservation_level, reservation_url, reservation_lead_time_days, reservation_notes,
english_support, cash_only, card_accepted, kid_friendly, wheelchair_accessible,
tattoo_friendly, luggage_storage,
tourist_density (0–1), popularity_rank, editorial_rating (0–5), review_score, review_count,
best_time_of_day, best_season[],
description_md, tips_md,
anime_refs: [{ title, episode, scene, image_compare }],
images[], official_url, source_refs[],
status: draft | published | closed_permanently | needs_review,
quality_tier: 1 | 2 | 3,
created_at, updated_at, verified_at
```

### `hotels`

```
id, city_id, area_id, name, style (business|boutique|ryokan|hostel|apartment|capsule),
lat, lng, stars, review_score, review_count,
price_band, amenities[], family_friendly, onsen_on_site,
partner_refs: { booking: "...", agoda: "...", rakuten: "..." },
location_score_cache (pro Area)
```

### `connections` (Intercity)

```
from_city, to_city, mode, operator, duration_min, price_jpy,
transfers, frequency_per_day, jr_pass_covered, pass_refs[],
reservation_required, notes
```

## Event-Log (Undo/Redo, Analytics, AI-Feedback)

Statt Snapshots wird jede Änderung als Event gespeichert:

```jsonc
{
  "id": "evt_...",
  "trip_id": "trp_...",
  "user_id": "usr_...",
  "seq": 128,
  "type": "item.remove | item.add | item.replace | item.lock | area.reorder |
           destination.remove | destination.add | nights.redistribute |
           hotel.select | flight.select | ai.improve.apply | budget.optimize.apply",
  "payload": { /* typabhängig */ },
  "inverse": { /* für Undo */ },
  "created_at": "..."
}
```

Nutzen:
1. **Undo/Redo** ohne Speicherexplosion.
2. **Kollaboration** (v2): Events sind bereits die Synchronisationseinheit.
3. **AI-Feedback:** Welche Vorschläge werden systematisch entfernt oder ersetzt?
   Fliesst in `poi.editorial_rating` und die Scoring-Gewichte zurück.

## Persistenz-Empfehlung

- **PostgreSQL** als Primärspeicher. `trips` mit `jsonb`-Spalte für den
  Trip-Body (schnelle, atomare Speicherung des gesamten Objekts) plus
  normalisierte Spalten für Abfragen (`owner_id`, `status`, `updated_at`,
  `city_ids[]`).
- Referenzdaten (`pois`, `cities`, `hotels`, `connections`) vollständig
  relational + **PostGIS** für Geo-Abfragen (Radius, Polygon, Nearest).
- **Suchindex** (Typesense/Meilisearch) für Explore, aus Postgres gespeist.
- **Redis** für Preis-Caches, Rate-Limits, Generator-Job-Status.
- **Objektspeicher** (S3-kompatibel) für Bilder, mit CDN und aggressivem Resizing.

## Versionierung & Migration

- `trip.schema_version` in jedem Trip. Migrationen laufen lazy beim Laden.
- Referenzdaten haben `db_snapshot`-Versionen, damit ein alter Trip
  nachvollziehbar bleibt, auch wenn ein POI später geschlossen wird (der Trip
  behält die Referenz und zeigt eine Warnung).
