# 01 — Informationsarchitektur, Routing & Navigations-Flows

## Sitemap

```
/                               Homepage (zwei Einstiege)
/plan                           Onboarding-Wizard („Plan my trip for me“)
/plan/generating                Generator-Zwischenscreen (Streaming-Progress)
/build                          Leerer Trip-Builder („Build my own trip“)

/trip/:tripId                   Trip-Dashboard (Hauptansicht)
/trip/:tripId/map               Kartenansicht des gesamten Trips
/trip/:tripId/budget            Budget-Detailansicht
/trip/:tripId/flights           Flug-Modul
/trip/:tripId/transport         Intercity-Transport (Shinkansen, Bus, Inlandsflug)
/trip/:tripId/services          Globale Services (eSIM, Versicherung, Airport-Transfer)
/trip/:tripId/d/:destinationId  Destination-Seite (z. B. Tokyo)
/trip/:tripId/d/:destinationId/hotel   Hotel-Auswahl für diese Destination
/trip/:tripId/checklist         Reservierungs-Checkliste (was muss vorab gebucht werden)
/trip/:tripId/share/:token      Öffentliche, schreibgeschützte Ansicht

/explore                        Explore-Datenbank (global, ohne Trip-Kontext)
/explore/:citySlug              z. B. /explore/tokyo
/explore/:citySlug/:areaSlug    z. B. /explore/tokyo/akihabara
/poi/:poiSlug                   POI-Detailseite (SEO-relevant)
/city/:citySlug                 Stadt-Guide-Seite (SEO-relevant)

/account                        Kontoübersicht
/account/trips                  Alle Trips des Nutzers
/account/bookings               Als gebucht markierte Elemente
/account/settings               Währung, Sprache, Einheiten, Benachrichtigungen

/legal/affiliate-disclosure     Pflicht-Offenlegung
/legal/privacy, /legal/terms
```

## Zwei Kontexte für dieselben Daten

Die Explore-Datenbank existiert in zwei Modi:

| Modus | Einstieg | Verhalten |
|---|---|---|
| **Trip-Kontext** | Aus dem Trip heraus (`Explore more`) | Als Overlay/Drawer über dem Trip. Primäraktion: `+ Add to my trip`. Bereits im Trip enthaltene POIs sind markiert. |
| **Standalone** | `/explore` direkt, meist über SEO/Google | Vollseite. Primäraktion: `+ Add to trip` → falls kein Trip existiert, „Start a trip with this“ (erzeugt Trip-Draft mit diesem POI). |

Der Standalone-Modus ist der wichtigste Akquisitionskanal: POI- und Stadt-Seiten
sind statisch renderbar und ranken auf Long-Tail-Suchen („one piece store
akihabara“, „anime locations shinjuku“).

## Navigations-Hierarchie im Trip

```
Trip
├── Trip-Header        (Titel, Daten, Reisende, Budget-Leiste)   — immer sichtbar
├── ✈️ Flights          (global)
├── 🌐 Global services  (eSIM, Versicherung, Airport-Transfer)
├── Destination 1 — Tokyo, 5 Nächte
│   ├── 🏨 Accommodation
│   ├── 🚄 Anreise-Transport (aus vorheriger Destination bzw. Flughafen)
│   └── Areas / Days
│       ├── Area 1 — Shibuya + Harajuku
│       │   └── Items (POIs, Restaurants, Shops, Aktivitäten)
│       └── Area 2 — Akihabara
├── Destination 2 — Kyoto, 4 Nächte
└── Destination 3 — Osaka, 3 Nächte
```

Es gibt bewusst **nur drei Ebenen** unterhalb des Trips: Destination → Area/Day →
Item. Tiefere Verschachtelung macht Drag & Drop und mobile Bedienung unbrauchbar.

## „Area“ vs. „Day“ — der zentrale Kniff

Standardmässig heisst der Container **Area** („Shibuya + Harajuku“) und ist
*nicht* an ein Datum gebunden. Der Nutzer kann pro Trip umschalten:

- **Flexible mode (Default):** Areas ohne Datum, Reihenfolge frei, „ca. 1 Tag“.
- **Day mode:** Areas werden auf konkrete Kalendertage gemappt (Tag 3 = 12. April).
- **Schedule mode (Opt-in):** Innerhalb eines Tages Uhrzeiten. Nur für Nutzer, die
  das ausdrücklich wollen — und nur mit Öffnungszeiten-Validierung.

Der Umschalter liegt im Trip-Header (`Layout: Flexible · Days · Schedule`) und
ändert nur die Darstellung, nicht die Datenstruktur (siehe `09-datenmodell.md`).

## Globale UI-Elemente

- **Trip-Header (sticky):** Trip-Titel, Datum-Range, Reisendenanzahl, Budget-Leiste, `Improve with AI`, `Share`, `Export`.
- **Command-Bar (`⌘K`):** Suche über POIs, Städte, eigene Trip-Elemente, plus Aktionen („Add Kyoto“, „Remove Osaka“).
- **Undo-Toast:** Jede destruktive Aktion erzeugt einen Toast mit `Undo` (10 s) — zusätzlich Undo-History im Trip-Menü.
- **AI-Sidebar:** Kontextuelle Vorschläge („Diese 5 Dinge lassen sich geografisch gut an einem Tag kombinieren“), immer verwerfbar.

## Responsive-Verhalten

| Breakpoint | Trip-Dashboard | Explore |
|---|---|---|
| Desktop ≥ 1280 px | Zweispaltig: Plan links, Karte/Details rechts | Filter-Sidebar + Grid + Karte |
| Tablet 768–1279 px | Einspaltig, Karte als Toggle | Filter als Drawer |
| Mobile < 768 px | Einspaltig, Destination-Blöcke collapsible, Bottom-Sheet für Aktionen | Vollbild-Liste, Karte als Tab |

Mobile ist der Primär-Case für die *On-Trip*-Nutzung, Desktop für die Planung.
Beide müssen den vollen Funktionsumfang haben — insbesondere `Replace` und
`Explore more` dürfen mobil nicht wegfallen.
