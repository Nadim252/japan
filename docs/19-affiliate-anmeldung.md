# 19 — Affiliate-Programme: wo anmelden und in welcher Reihenfolge

> **Hinweis zur Verlässlichkeit:** Programmnamen und Struktur sind stabil,
> Provisionssätze, Bedingungen und Anmeldewege ändern sich laufend. Alle
> konkreten Zahlen und Anforderungen unten sind Orientierung — vor der
> Entscheidung beim jeweiligen Programm selbst prüfen.

## Zwei Wege hinein

**Direkt beim Anbieter.** Man beantragt bei Booking, Klook oder GetYourGuide
einzeln ein Partnerkonto. Bessere Konditionen, mehr Datenzugang, dafür je ein
eigener Antrag, eigene Freigabe, eigene Abrechnung.

**Über ein Netzwerk.** Ein Konto, viele Händler. Im Reisebereich sind das vor
allem Travelpayouts (reisespezifisch), Awin, CJ, Impact, Rakuten Advertising,
Partnerize. Schnellere Freigabe, etwas geringere Sätze, eine einzige Abrechnung.

**Für den Start ist das Netzwerk der richtige Weg**, weil die Freigabe leichter
fällt und man mit einer Integration mehrere Kategorien abdeckt. Direktverträge
lohnen sich, sobald messbarer Umsatz da ist — dann kann man auch verhandeln.

## Das Henne-Ei-Problem, das dich zuerst treffen wird

Fast alle Programme prüfen den Antrag manuell und verlangen eine **bestehende,
inhaltlich gefüllte Webseite**. Eine leere Domain oder eine „Demnächst"-Seite
wird in der Regel abgelehnt.

Das kollidiert mit dem Plan, die Bilder aus den Partnerprogrammen zu beziehen:
Für die Bilder braucht es die Freigabe, für die Freigabe braucht es die Seite.

Auflösung — in dieser Reihenfolge:

1. **Erste Version ohne Affiliate bauen.** Inhalte, Orte, Stadtseiten,
   Bilder aus Wikimedia (siehe `18-bildquellen.md`), erzeugte Motive als Rückfall.
   20–30 echte Inhaltsseiten reichen für die meisten Anträge.
2. **Bei einem Netzwerk mit niedriger Hürde anfangen.** Travelpayouts nimmt
   auch kleine, neue Seiten und deckt Flug, Hotel, eSIM und Versicherung ab.
3. **Nach Freigabe die Bilder und Produktdaten übernehmen** und die Seite damit
   auffüllen.
4. **Mit gewachsenem Traffic direkt beantragen** bei Klook, GetYourGuide,
   Booking und Agoda.

## Was vor dem ersten Antrag stehen muss

| Voraussetzung | Warum |
|---|---|
| Eigene Domain mit echter Seite | Wird manuell angeschaut |
| 20–30 Inhaltsseiten | Beleg, dass es kein Linkfriedhof wird |
| Impressum | In CH/DE/EU Pflicht, wird geprüft |
| Datenschutzerklärung | Ebenso Pflicht |
| Affiliate-Offenlegung | Von den Programmen verlangt (siehe `11-booking-affiliate.md`) |
| Kontaktmöglichkeit | Wird geprüft |
| Bankverbindung / Auszahlungsweg | Für die Abrechnung |

Traffic wird oft abgefragt, ist aber selten hartes Ausschlusskriterium. Ehrlich
antworten — eine erfundene Zahl fällt bei der ersten Abrechnung auf.

## Empfohlene Reihenfolge für dieses Projekt

### Stufe 1 — sofort nach der ersten Inhaltsversion

**Travelpayouts.** Reisespezifisches Netzwerk, bekannt für unkomplizierte
Freigabe auch bei kleinen Seiten. Bündelt Flug (Aviasales, Kiwi), Hotel
(Booking, Agoda über das Netzwerk), eSIM, Versicherung und Transfers. Eine
Anmeldung, mehrere Kategorien, eine Auszahlung.

Damit ist die Seite grundsätzlich monetarisiert, während der Rest wächst.

### Stufe 2 — sobald Inhalte für Japan stehen

**Klook.** Für Japan der wichtigste einzelne Partner. Riesiges Angebot an
Tickets, JR-Pässen, IC-Karten, Flughafentransfers, teamLab, Universal Studios,
Touren. Partnerprogramm mit Produktdaten. Wenn du nur ein Direktprogramm
beantragst, dann dieses.

**GetYourGuide.** Zweiter Standbein für Touren und Aktivitäten, in Europa
bekannter, gute Partnerbetreuung, saubere Produktdaten.

### Stufe 3 — sobald Buchungen laufen

**Booking.com** und **Agoda** direkt für bessere Hotelkonditionen.
**Rakuten Travel** ist für Japan besonders interessant, weil dort Ryokan und
kleinere japanische Häuser gelistet sind, die bei Booking teils fehlen.
**Airalo** oder ein anderer eSIM-Anbieter direkt — hohe Marge, kleine Beträge.

Mehr als drei bis vier Programme gleichzeitig lohnt sich am Anfang nicht: Jedes
kostet Integrationsarbeit, und ein Programm ohne Umsatz wird nach einiger Zeit
ohnehin wieder geschlossen.

## Zu den Bildern — Präzisierung

Ich hatte in `18-bildquellen.md` geschrieben, die Partnerprogramme lieferten die
Bilder mit. Das gilt uneingeschränkt für **Aktivitäten und Touren**: Klook und
GetYourGuide stellen Produktdaten inklusive Bildern bereit, ausdrücklich zur
Bewerbung der Angebote.

Bei **Hotels** ist es differenzierter. Der Zugang zu vollständigen Produktdaten
mit Bildern hängt von der Programmstufe ab; kleinere Partner bekommen teils nur
Widgets und Deeplinks. Widgets zeigen die Fotos allerdings ebenfalls an — sie
werden dann vom Partner ausgeliefert statt von uns, was rechtlich sogar der
einfachere Weg ist.

**Beim Antrag ausdrücklich nach Produktdaten und Bildnutzung fragen.** Das
entscheidet, ob wir eigene Hotelkarten bauen können oder mit eingebetteten
Widgets arbeiten.

## Praktisches zur Umsetzung

- **Tracking-Links serverseitig erzeugen.** Jedes Programm gibt eine Partner-ID.
  Die gehört nie ins Frontend-Bundle — siehe `11-booking-affiliate.md`.
- **Pro Kategorie zwei Partner** anstreben, damit ein gekündigtes Programm nicht
  eine ganze Kategorie leert.
- **Auszahlungsschwellen beachten.** Üblich sind 50–100 Einheiten Mindestbetrag.
  Bei drei Programmen mit je knapp erreichter Schwelle liegt Geld lange fest.
- **Cookie-Laufzeiten** unterscheiden sich stark (von Stunden bis 30 Tagen). Das
  ist mitentscheidend, weil Reiseplanung Wochen dauert — bei der Auswahl mit
  ansehen.

## Zur Rechtsform (Schweiz)

Für den Anfang genügt eine Einzelfirma; eine Eintragung ins Handelsregister ist
erst ab einem bestimmten Jahresumsatz nötig, und die Mehrwertsteuerpflicht
beginnt ebenfalls erst ab einer Schwelle. Affiliate-Einnahmen aus dem Ausland
haben eigene Regeln.

**Das ist keine Steuer- oder Rechtsberatung** — die Schwellenwerte und die
Behandlung ausländischer Einnahmen mit einem Treuhänder klären, bevor
nennenswerte Beträge fliessen. Einige Programme verlangen ohnehin eine
Steuernummer oder Firmenangaben bei der Anmeldung.
