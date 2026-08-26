# 12 — Nutzerkonto, Teilen & Export

## Grundhaltung: Account so spät wie möglich

Der Nutzer muss den kompletten Wert des Produkts sehen, bevor er ein Konto
anlegt. Onboarding, Generierung und freies Editieren funktionieren anonym gegen
einen Draft-Trip (Cookie `draft_token`, 30 Tage gültig).

## Die richtigen Momente für die Registrierung

Nicht ein Popup nach 20 Sekunden, sondern kontextuelle Prompts an Stellen mit
echtem Nutzen:

| Auslöser | Prompt |
|---|---|
| Nutzer klickt `Share` | „Konto erstellen, damit dein Link bestehen bleibt.“ |
| Nutzer klickt `Export` (PDF/ICS) | „Kurz anmelden, dann bekommst du dein PDF.“ |
| Erste Buchung markiert | „Sichere deine Buchungen — Konto in 10 Sekunden.“ |
| Zweiter Trip wird angelegt | „Speichere beide Trips an einem Ort.“ |
| Checkliste mit `lead_time` < 45 Tage | „Sollen wir dich an den Ghibli-Verkauf erinnern?“ |
| 5 Minuten Bearbeitungszeit erreicht | Dezenter Header-Hinweis: „Trip sichern“ |

Login-Methoden: **E-Mail-Magic-Link** (Standard), Google, Apple. Kein Passwort
in v1 — weniger Support, weniger Angriffsfläche.

Nach dem Login: `POST /auth/claim-drafts` ordnet alle im Browser vorhandenen
Draft-Tokens dem Konto zu. Der Nutzer verliert nie Arbeit.

## Kontobereiche

### `/account/trips`

Karten je Trip: Titel, Daten, Städte-Chips, Reisende, Budgetstand, Status
(`draft` / `active` / `past`), Thumbnail-Karte. Aktionen: Öffnen, Duplizieren,
Umbenennen, Teilen, Archivieren, Löschen.

„Trip duplizieren“ ist wertvoller als es klingt: Nutzer bauen Varianten
(„14 Tage Variante A / B“) und vergleichen sie.

### `/account/bookings`

Alle als `booked` oder `self_booked` markierten Elemente, gruppiert nach Trip und
Typ, mit Referenznummern, Preisen und Links. Das ist faktisch ein leichtgewichtiges
Reise-Dossier — und für den Nutzer ein Grund, während der Reise zurückzukommen.

### `/account/settings`

Währung, Sprache (DE/EN, später FR/IT), Einheiten, Zeitformat,
Benachrichtigungen (Buchungserinnerungen, Preisänderungen, Produkt-News getrennt
schaltbar), verbundene Konten, Datenexport (DSGVO), Kontolöschung.

## Teilen

```
Share this trip
( ) Nur Link mit Ansicht      → jeder mit dem Link sieht den Plan (read-only)
( ) Link mit Kommentaren      → Betrachter können Items kommentieren   (v2)
( ) Link mit Bearbeitung      → Mitreisende können ändern              (v2)

https://…/trip/abc/share/8f2k…            [Kopieren]

Sichtbar: Route, Orte, Hotels, Aktivitäten
Nicht sichtbar: Budget, Buchungsreferenzen   [ ] Budget mitteilen
```

- Geteilte Ansicht ist eine eigene, schnelle, statisch cachebare Seite mit
  Open-Graph-Bild (Karte + Städte + Dauer) — gutes organisches Wachstum.
- Der Betrachter sieht überall `[Diesen Trip als meinen kopieren]` → erzeugt
  einen eigenen Draft. Das ist der stärkste virale Loop im Produkt.

## Collaboration (v2)

- Mitreisende einladen (E-Mail/Link), Rollen `viewer` / `editor`.
- Kommentare pro Item („Muss das wirklich sein?“).
- Abstimmung: Jeder Mitreisende kann Items mit 👍/👎 markieren; Items mit
  Mehrheits-👎 werden dem Ersteller als Entfernungsvorschlag angezeigt.
- Technisch bereits vorbereitet durch den Event-Log (`09-datenmodell.md`).

## Export

| Format | Inhalt |
|---|---|
| **PDF** | Vollständiger Reiseplan: Route, Karten pro Stadt, Areas mit Items, Adressen **auf Japanisch** (für Taxi/Passanten), Öffnungszeiten, Buchungsreferenzen, Notfallnummern. Druckoptimiert, offline nutzbar. |
| **ICS** | Kalendereinträge für Flüge, Hotel-Check-ins, reservierungspflichtige Aktivitäten, Zugfahrten |
| **Google Maps** | Eine Karten-Liste pro Stadt mit allen Items (Deep-Link/Import-Datei) |
| **CSV** | Budget und Item-Liste für Tabellen-Nutzer |
| **Wallet** (v2) | Reservierungen als Wallet-Pässe |

Das PDF mit japanischen Adressen ist eines der Features, die Nutzer weiterempfehlen.

## On-Trip-Modus (Roadmap v2)

Während der Reise wechselt die Oberfläche auf Mobile automatisch in einen
Tagesmodus: heutige Area, nächster Stop, Öffnungszeiten jetzt, Wegzeit,
„Erledigt“-Häkchen, Offline-Karte, Ausgaben nachtragen. Der geplante Trip wird
dabei zum Live-Begleiter — und liefert uns Signale darüber, welche Vorschläge
tatsächlich besucht wurden.

## Datenschutz

- Datenminimierung: keine Passwörter, keine Zahlungsdaten, keine Passdaten.
- Anonyme Drafts werden nach 30 Tagen ohne Aktivität gelöscht.
- Analytics: eigengehostet und cookiefrei wo möglich; Produktanalytics
  pseudonymisiert.
- Kein Verkauf von Nutzerdaten. Partner erhalten nur den Klick, keine Profile.
- Vollständiger Datenexport und harte Kontolöschung (inkl. Trips) auf Knopfdruck.
