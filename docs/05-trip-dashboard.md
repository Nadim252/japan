# 05 — Trip-Dashboard

Das Dashboard ist das Produkt. Alles andere führt hierhin oder geht von hier weg.

## Layout (Desktop)

```
┌──────────────────────────────────────────────────────────────────────────┐
│ 🇯🇵 Anime, Ramen & Berge          12.–26. April · 2 Reisende             │
│ Budget CHF 3'000 ·  ▓▓▓▓▓▓▓▓▓▓▓▓▓▓░░░  CHF 2'740 · Rest CHF 260         │
│ [Layout: Flexible ▾]  [✨ Improve with AI]  [Share]  [Export]  [⋯]       │
├────────────────────────────────────────────┬─────────────────────────────┤
│                                            │                             │
│  ✈️  FLIGHT                                 │                             │
│      ZRH → NRT · 12. Apr · 14h05 · CHF 748 │        [ K A R T E ]        │
│      [Change]  [Skip — I'll book myself]   │                             │
│                                            │   Route mit Städten,        │
│  🌐  BEFORE YOU GO                          │   Areas und POIs als        │
│      eSIM · Versicherung · Airport-Transfer│   nummerierte Pins          │
│                                            │                             │
│  ┌──────────────────────────────────────┐  │   Hover auf ein Item        │
│  │ 🇯🇵 TOKYO — 5 Nächte      12.–17. Apr │  │   highlightet den Pin       │
│  │ 🏨 Hotel Gracery Shinjuku CHF 128/N  │  │                             │
│  │    [Change]                          │  │                             │
│  │ Area 1 — Shibuya + Harajuku      (5) │  │                             │
│  │ Area 2 — Akihabara               (6) │  │                             │
│  │ Area 3 — Asakusa + Ueno          (4) │  │                             │
│  │ Area 4 — Shinjuku                (5) │  │                             │
│  │ Area 5 — Daytrip: Kamakura       (3) │  │                             │
│  │                          [Open Tokyo]│  │                             │
│  └──────────────────────────────────────┘  │                             │
│         🚄 Shinkansen · 2h13 · CHF 98      │                             │
│  ┌──────────────────────────────────────┐  │                             │
│  │ 🇯🇵 KYOTO — 4 Nächte                  │  │                             │
│  └──────────────────────────────────────┘  │                             │
│                                            │                             │
│  [+ Add destination]                       │                             │
└────────────────────────────────────────────┴─────────────────────────────┘
```

## Trip-Header

| Element | Verhalten |
|---|---|
| **Titel** | Vom Generator vorgeschlagen, inline editierbar |
| **Datum** | Klick öffnet Datepicker; Änderung triggert Preis-Refresh + Saison-Warnung („Du verschiebst aus der Kirschblüte heraus“) |
| **Reisende** | Änderung triggert Hotel-Neuberechnung (Zimmer) und Budget × Personen |
| **Budget-Leiste** | Live; Farbe grün < 90 %, gelb 90–100 %, rot > 100 %. Klick → `/budget` |
| **Layout-Switch** | `Flexible` · `Days` · `Schedule` (siehe `01-informationsarchitektur.md`) |
| **Improve with AI** | Öffnet den Verbesserungs-Flow (siehe unten) |
| **Share / Export** | Link, PDF, Kalender (.ics), Google Maps-Liste |
| **⋯** | Duplizieren, Regenerieren, Undo-History, Trip-Settings (= Onboarding-Antworten), Löschen |

## Block-Typen

### 1. Flug-Block (nur wenn `include_flights`)

```
✈️ FLIGHT
Outbound   ZRH → NRT   12 Apr, 12:55 → 08:40+1   1 stop (DOH)   14h05
Return     KIX → ZRH   26 Apr, 10:30 → 18:05     1 stop (HEL)   14h35
                                                       CHF 748 p.P.
[View deal ↗]   [Show other options]   [Skip — I'll book myself]
```

Drei Optionen (Cheapest / Fastest / Best value) hinter `Show other options`.
Nach `Skip` bleibt ein Platzhalter mit `[Add my flight details]` (manuelle
Eingabe von Flugnummer und Zeiten) — der Trip braucht Ankunfts-/Abflugzeiten für
die Tagesplanung, auch wenn wir den Flug nicht vermittelt haben.

### 2. Globale Services

eSIM, Reiseversicherung, Airport-Transfer (Narita Express / Haruka / Limousine
Bus), Suica/Pasmo-Hinweis, JR-Pass-Empfehlung mit Rechnung
(„Deine Route kostet einzeln CHF 312 — der 7-Tage-Pass kostet CHF 375. Lohnt
sich für dich **nicht**.“). Ehrliche Negativ-Empfehlungen bauen Vertrauen auf
und kosten uns nur eine Affiliate-Provision, die ohnehin nicht konvertiert hätte.

### 3. Destination-Block

Collapsed zeigt: Flagge/Emoji, Stadtname, Nächte, Datumsbereich, gewähltes Hotel
mit Preis, Liste der Areas mit Item-Anzahl, `Open`.

Aktionen im Kontextmenü des Blocks:
`Add night` · `Remove night` · `Change hotel` · `Reorder` (Drag) · `Remove destination` · `Explore this city`

### 4. Transport-Konnektor (zwischen zwei Destinations)

Schmaler Streifen mit Verkehrsmittel, Dauer, Preis, JR-Pass-Abdeckung
(`✓ im JR Pass enthalten`), `Change` (Alternativen: Shinkansen Nozomi/Hikari,
Nachtbus, Inlandsflug), `Reserve seat ↗`.

## Kernaktionen

### Destination entfernen → Nächte-Dialog

Das im Konzept beschriebene Verhalten, exakt spezifiziert:

```
Osaka entfernt. Du hast jetzt 3 freie Nächte.

Wohin damit?
( ) +2 Tokyo, +1 Kyoto            ← AI-Empfehlung (Standard-Auswahl)
( ) +3 Kyoto
( ) Neue Stadt: [ Hiroshima ] [ Kanazawa ] [ Hakone ] [ Hokkaido ] [ Nara ] [ Suche… ]
( ) Trip um 3 Nächte verkürzen    → Enddatum ändert sich
( ) Später entscheiden            → Nächte bleiben als „unassigned“ sichtbar

                                   [ Übernehmen ]
```

- Die AI-Empfehlung wird begründet („Tokyo, weil 4 deiner Top-Interessen dort liegen“).
- Bei „Neue Stadt“ werden Transportkosten und Routenlogik sofort neu berechnet und die Differenz angezeigt.
- `Später entscheiden` erzeugt einen sichtbaren, klickbaren `⚠ 3 unassigned nights`-Chip im Header. Der Trip bleibt gültig, aber unvollständig.

### Destination hinzufügen

`+ Add destination` → Suchfeld + Karte + AI-Vorschläge („Passt zu deinem Trip:
Kanazawa, Hakone, Hiroshima“). Nach Auswahl: „Wie viele Nächte?“ mit
Vorschlagswert und Hinweis, woher die Nächte kommen (verlängern vs. umverteilen).

### Reihenfolge ändern

Drag & Drop der Destination-Blöcke. Nach dem Drop prüft das System die
Geografie und warnt bei Zickzack: *„Kyoto → Hiroshima → Tokyo bedeutet 3h
zusätzliche Fahrzeit. Trotzdem so lassen?“* — Warnung, kein Verbot.

### „Improve my trip with AI“

Analysiert den aktuellen Trip (auch einen komplett selbstgebauten) und liefert
Vorschläge als **Diff**, nie als stille Änderung:

```
Vorschläge für deinen Trip

+ Tag in Kyoto: Fushimi Inari früh morgens (6:00) — du hast Fotografie
  angegeben, ab 9:00 ist es dort sehr voll.                    [Add] [Ignore]

⚠ Du hast 3 Anime-Shops in Shibuya, aber Nakano Broadway fehlt —
  besseres Angebot für Vintage-Figuren.                        [Add] [Ignore]

⚠ Dein Tag 6 hat 4 Stops in 3 verschiedenen Stadtteilen
  (≈ 2h Fahrzeit). Umsortieren?                                [Fix]  [Ignore]

⚠ teamLab Planets ist am 14. April ausgebucht — 15. April ist frei. [Move] [Ignore]

− Nakameguro und Daikanyama liegen 900 m auseinander. Zusammenlegen? [Merge] [Ignore]

                                      [Alle übernehmen]  [Schliessen]
```

Jeder Vorschlag ist einzeln annehmbar. `Alle übernehmen` erzeugt **einen**
Undo-Punkt.

## Zustandslogik & Persistenz

- **Autosave** nach jeder Änderung (optimistic UI, Server-Bestätigung im Hintergrund).
- **Undo/Redo** über einen Event-Log (`trip_events`), nicht über Snapshots — siehe `09-datenmodell.md`. `⌘Z` funktioniert überall.
- **Konfliktbehandlung** bei geteilten Trips: Last-write-wins pro Feld, mit Hinweis („Anna hat vor 2 Minuten das Hotel in Kyoto geändert“).
- **Offline:** Lesen funktioniert offline (Service Worker), Schreiben wird gequeued.

## Leerzustände

| Zustand | Anzeige |
|---|---|
| Neuer Trip (`/build`) | Grosse Fläche mit `+ Add destination` und „Oder lass die AI starten →“ |
| Destination ohne Items | „Noch nichts geplant für Kyoto. [✨ Vorschläge holen] [🔍 Explore Kyoto]“ |
| Kein Hotel gewählt | „Kein Hotel für diese Stadt. [Vorschläge] [Ich buche selbst] [Ich bleibe woanders]“ |
| Über Budget | Roter Balken + „CHF 180 über Budget. [Günstiger machen]“ |

## Tastaturkürzel

`⌘K` Command-Bar · `⌘Z`/`⇧⌘Z` Undo/Redo · `E` Explore für fokussierten Block ·
`R` Replace für fokussiertes Item · `Del` Remove · `M` Karte ein/aus ·
`?` Kürzel-Übersicht.
