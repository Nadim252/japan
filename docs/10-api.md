# 10 — API-Skizze

REST + JSON, `Content-Type: application/json`. Alle Trip-schreibenden Endpunkte
erwarten `If-Match: <trip.version>` (Optimistic Locking) und antworten mit dem
neuen `version` plus dem erzeugten Event.

## Auth

- Anonyme Nutzung über `draft_token` (HttpOnly-Cookie), Trips ohne `owner_id`.
- Login: E-Mail-Magic-Link + OAuth (Google, Apple). Session als JWT/Cookie.
- `POST /auth/claim-drafts` ordnet nach Login alle Draft-Trips dem Konto zu.

## Onboarding & Generierung

```
POST   /api/briefs                       → { brief_id }           Brief anlegen/aktualisieren
PATCH  /api/briefs/:id                                            Einzelantwort speichern
POST   /api/briefs/:id/generate          → { job_id, trip_id }    Generierung starten
GET    /api/generate/:job_id/stream      → text/event-stream      Fortschritt + Teilergebnisse
POST   /api/trips/:id/regenerate         { mode: "reseed"|"adventurous"|"cheaper" }
```

SSE-Events des Generators:
`step` (Schritt begonnen/fertig) · `route` (Städte + Nächte) ·
`destination` (fertiger Destination-Block) · `budget` · `done` · `error`.

## Trips

```
GET    /api/trips                        Liste des Nutzers
POST   /api/trips                        Leerer Trip („Build my own“)
GET    /api/trips/:id                    Vollständiges Trip-Objekt
PATCH  /api/trips/:id                    Titel, Daten, Reisende, brief-Felder
DELETE /api/trips/:id
POST   /api/trips/:id/duplicate
GET    /api/trips/:id/events?since=:seq  Event-Log (Undo/Sync)
POST   /api/trips/:id/undo               { to_seq }
POST   /api/trips/:id/share              { visibility } → { share_url }
GET    /api/trips/:id/export?format=pdf|ics|csv|gmaps
```

## Destinations

```
POST   /api/trips/:id/destinations              { city_id, nights, position }
PATCH  /api/trips/:id/destinations/:did         { nights, order, locked }
DELETE /api/trips/:id/destinations/:did         → { freed_nights, redistribution_options[] }
POST   /api/trips/:id/nights/redistribute       { plan: [{ destination_id, nights }] | { new_city, nights } | { shorten: true } }
POST   /api/trips/:id/destinations/reorder      { order: [dst_2, dst_1, dst_3] }
GET    /api/trips/:id/destinations/suggestions  → passende Städte + Begründung
```

`DELETE` liefert die im Dashboard angezeigten Umverteilungsoptionen direkt mit —
inklusive AI-Empfehlung und Kostendelta pro Option.

## Areas & Items

```
POST   /api/trips/:id/destinations/:did/areas        { area_ref | title, type, position }
PATCH  .../areas/:aid                                { title, order, date, locked }
DELETE .../areas/:aid
POST   .../areas/:aid/merge                          { with_area_id }
POST   .../areas/:aid/split                          { at_index }
POST   .../areas/:aid/optimize-order                 → Reihenfolge nach Geo + Öffnungszeiten

POST   .../areas/:aid/items                          { poi_id | custom, position }
PATCH  .../items/:iid                                { order, locked, must_do, status, user_note }
DELETE .../items/:iid
GET    .../items/:iid/replacements                   → 3 Alternativen (aus Cache, kein LLM)
POST   .../items/:iid/replace                        { poi_id }
POST   .../items/move                                { item_id, to_area_id, position }
```

## Unterkunft, Flug, Transport, Services

```
GET    /api/trips/:id/destinations/:did/hotels?refresh=false   → 3 Labels + Liste
POST   /api/trips/:id/destinations/:did/hotel                 { hotel_id } | { self_booked: {...} } | { skip: true }
GET    /api/trips/:id/flights                                  → Optionen (cheapest/fastest/value)
POST   /api/trips/:id/flights                                  { option_id } | { self_booked } | { skip }
GET    /api/trips/:id/transport/:leg                           → Verbindungsoptionen
POST   /api/trips/:id/transport/:leg                           { option_id }
GET    /api/trips/:id/passes                                   → JR-Pass-Rechnung
POST   /api/trips/:id/services                                 { type, action }
```

## Budget

```
GET    /api/trips/:id/budget                        → Kategorien, Total, Remaining, Annahmen
PATCH  /api/trips/:id/budget/assumptions            { food_level, souvenirs_jpy, local_transport_override }
POST   /api/trips/:id/budget/optimize               { target_jpy } → Diff-Vorschlag (nicht angewandt)
POST   /api/trips/:id/budget/optimize/apply         { suggestion_ids[] }
```

## AI-Verbesserung

```
POST   /api/trips/:id/improve            → { suggestions: [{ id, type, title, body, patch, impact }] }
POST   /api/trips/:id/improve/apply      { suggestion_ids[] }   // erzeugt EINEN Undo-Punkt
```

`patch` ist ein JSON-Patch auf das Trip-Objekt — die UI kann Vorschläge dadurch
vor dem Anwenden vollständig als Diff rendern.

## Explore & Referenzdaten

```
GET  /api/explore?city=tokyo&area=akihabara&tags=anime,tcg&price=1,2
     &reservation=walk_in&open_on=2026-04-14&near=35.69,139.77&radius=1200
     &sort=recommended&trip_id=trp_...&cursor=...
GET  /api/pois/:slug
GET  /api/cities/:slug
GET  /api/cities/:slug/areas
GET  /api/search?q=pokémon+center&city=tokyo          Volltext (JP/Romaji/EN/DE)
POST /api/trips/:id/custom-pois                        eigener Eintrag
POST /api/pois/:id/report                              { reason, comment }
```

`trip_id` im Explore-Call sorgt für kontextuelles Ranking und markiert bereits
enthaltene POIs.

## Buchung & Affiliate

```
GET  /api/offers/hotel/:hotel_id?checkin=&checkout=&rooms=&adults=
GET  /api/offers/activity/:poi_id?date=&pax=
POST /api/clicks                        { trip_id, entity_type, entity_id, partner }  → { redirect_url }
POST /api/trips/:id/bookings            { entity_type, entity_id, status, reference, price, currency }
GET  /api/trips/:id/checklist           → Reservierungspflichten, sortiert nach Vorlaufzeit
```

`POST /api/clicks` erzeugt die Tracking-ID **serverseitig** und liefert die
Redirect-URL — nie im Client zusammengebaut (siehe `11-booking-affiliate.md`).

## Konto

```
GET    /api/me
PATCH  /api/me                      { currency, language, units, notifications }
GET    /api/me/trips
GET    /api/me/bookings
POST   /api/me/export               DSGVO-Datenexport
DELETE /api/me                      Kontolöschung
```

## Fehlerformat

```jsonc
{
  "error": {
    "code": "trip_version_conflict",
    "message": "Der Trip wurde inzwischen geändert.",
    "details": { "current_version": 43 },
    "retryable": true
  }
}
```

Wichtige Codes: `trip_version_conflict`, `validation_failed`,
`generation_failed`, `partner_unavailable`, `rate_limited`, `not_found`,
`draft_expired`.

## Nichtfunktionale Anforderungen

| Endpunkt-Klasse | Ziel-Latenz (p95) |
|---|---|
| Trip lesen | < 200 ms |
| Item-Mutation | < 150 ms (optimistic UI schreibt sofort) |
| Explore-Suche | < 250 ms |
| Hotelpreise (Cache-Hit) | < 300 ms |
| Hotelpreise (live) | < 2,5 s, mit Skeleton |
| Generierung gesamt | < 25 s, erster Block < 5 s |
