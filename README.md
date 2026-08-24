# japan

Japan-Reiseplaner — **AI-generierter Japan-Trip + vollständig modularer Trip-Builder.**

> Die AI macht dir den ersten Entwurf. Du besitzt danach den Plan.

Der Nutzer beantwortet einen kurzen Fragebogen und bekommt eine komplette Route
mit Städten, Areas, Orten, Unterkünften, Transport und Budget. Danach ist jedes
einzelne Element austauschbar: `Keep` · `Remove` · `Replace` · `Explore more`.

## Dokumentation

Die vollständige Produktspezifikation liegt in [`docs/`](docs/README.md) —
Homepage, Onboarding, Generator, Trip-Dashboard, Destination-Seite,
Explore-Datenbank, Budget-Engine, Datenmodell, API, Booking/Affiliate,
Nutzerkonto, Content-Pipeline und MVP-Roadmap.

Einstieg: **[docs/README.md](docs/README.md)**

## Prototyp

[`prototype/index.html`](prototype/index.html) — klickbarer Prototyp, eine
einzelne Datei ohne Abhängigkeiten. Einfach im Browser öffnen.

Enthalten: Fragebogen, generierte Route aus den angegebenen Interessen,
Übersicht der Städte als Bildkacheln, Stadtansicht mit Bildraster zum Antippen
(auswählen, abwählen, fixieren, ersetzen), live mitlaufendes Budget,
Stadt entfernen mit Nächte-Umverteilung und ein Schalter, der alle Stellen
markiert, an denen die Seite Geld verdient.

Gestaltung: weisse Oberfläche, rote Akzente, Karten mit Bild oben und Text
darunter. Dunkles Thema als Umkehrung vorhanden.

Die Bilder sind auf Canvas erzeugte Motive je Kategorie — Platzhalter für die
späteren Fotos.

## Stand

Konzept- und Spezifikationsphase. Noch kein Code.
Empfohlene Baureihenfolge siehe [docs/14-mvp-roadmap.md](docs/14-mvp-roadmap.md).
