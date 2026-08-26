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

### Als App auf dem Startbildschirm (PWA)

Der Prototyp ist eine installierbare Web-App. Über einen beliebigen
HTTPS-Server ausgeliefert, lässt er sich auf dem Handy zum Startbildschirm
hinzufügen, startet im Vollbild ohne Browserleiste und **funktioniert offline**.

Dazu gehören `manifest.webmanifest`, `sw.js` und die drei Symboldateien im
selben Ordner. Lokal ausprobieren:

```bash
cd prototype && python3 -m http.server 8000
# dann http://localhost:8000 öffnen — localhost gilt als sichere Herkunft
```

Der Service Worker legt die App-Hülle im Zwischenspeicher ab: Seitenaufrufe
gehen zuerst ans Netz und fallen bei Ausfall auf die gespeicherte Fassung
zurück, alles andere kommt zuerst aus dem Zwischenspeicher.

Geprüft: Registrierung und Aktivierung, gültiges Manifest, alle Symbole,
vollständiger Ablauf bei getrennter Verbindung.

Ohne verlinktes Manifest — etwa wenn die Datei einzeln geöffnet wird — bleibt
die PWA-Schicht vollständig inaktiv, und der Installationsknopf erscheint nicht.

### Fotos einsetzen

Der Prototyp zeigt echte Fotos, sobald welche hinterlegt sind, und zeichnet nur
ersatzweise ein Motiv, wo noch keins existiert. Beides mischt sich beliebig.

Ein Script-Tag vor `index.html` genügt:

```html
<script>
window.MEGURI_PHOTOS = {
  'meiji':      { src:'/img/meiji.jpg',  by:'Fotograf', lic:'CC BY-SA 4.0', url:'https://…' },
  'city:tokyo': { src:'/img/tokyo.jpg',  by:'Fotograf', lic:'CC BY-SA 4.0', url:'https://…' }
};
</script>
```

Schlüssel ist die Orts-ID aus dem Datensatz, für Städte `city:<id>`. `src` darf
eine URL sein (eigene Seite) oder ein eingebettetes `data:`-Bild. Die Angaben
`by` und `lic` erscheinen automatisch als Bildnachweis in der Fusszeile — bei
CC-lizenzierten Fotos ist das Pflicht.

## Stand

Konzept- und Spezifikationsphase. Noch kein Code.
Empfohlene Baureihenfolge siehe [docs/14-mvp-roadmap.md](docs/14-mvp-roadmap.md).
