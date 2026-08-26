# 20 — App oder Webseite?

> **Kurzfassung:** Webseite zuerst, und zwar über längere Zeit. Danach eine
> installierbare Web-App (PWA). Eine native App erst, wenn es messbaren Traffic
> und einen echten Anwendungsfall während der Reise gibt. Eine App früher zu
> bauen kostet Geld und Reichweite, statt beides zu bringen.

## Der eine Grund, der alles andere schlägt

**Das Geschäft läuft über Google.** Wer eine Japanreise plant, sucht „10 Tage
Japan Route", „Anime Orte Tokyo", „lohnt sich der JR Pass". Genau darauf ist die
ganze Struktur ausgelegt: Stadtseiten, Ortsseiten, Vorlagenrouten, strukturierte
Daten.

**Apps ranken nicht bei Google.** Eine App hat keinen organischen Kanal. Sie hat
den App Store — wo niemand nach „Japan Reiseplaner" sucht, bevor er die Marke
kennt — und bezahlte Installationen, die im Reisebereich zwischen zwei und sechs
Franken pro Installation kosten. Bei einem erwarteten Erlös von wenigen Rappen
bis etwa einem Franken pro Besucher (`17-monetarisierung.md`) rechnet sich das
nicht.

Anders gesagt: Die App kann erst Nutzer haben, wenn die Webseite sie liefert.

## Die Geldfrage, ehrlich gerechnet

### Was sich bei den Affiliate-Einnahmen ändert: nichts

Provisionssätze sind in der App dieselben. Reisen sind eine reale Dienstleistung,
keine digitale Ware — Apple und Google verlangen dafür **keine** Beteiligung, und
das Verlinken nach aussen zur Buchung ist ausdrücklich erlaubt. Der Affiliate-Teil
wird also weder besser noch schlechter.

Er wird aber **weniger**, weil weniger Leute kommen.

### Was sich beim Abo ändert: es wird schlechter

Ein Pro-Abo ist eine digitale Leistung und muss in der App über die Bezahlsysteme
der Stores laufen. Deren Anteil liegt bei rund 30 %, im ersten Jahr bzw. bei
kleinen Anbietern eher bei 15 %.

```
Abo CHF 5/Monat über die Webseite      → CHF 5,00 abzüglich Zahlungsgebühr ≈ 4,70
Abo CHF 5/Monat über den App Store     → abzüglich 15 %            ≈ 4,25
                                          abzüglich 30 %            ≈ 3,50
```

Die App macht den einzigen Erlöskanal teurer, den du selbst kontrollierst.

### Was die App tatsächlich besser kann: Wiederkehr

Der eine echte Vorteil sind **Push-Nachrichten**. In `17-monetarisierung.md` steht,
dass die Erinnerung an Reservierungsfristen der wertvollste Kanal ist, weil sie
den Nutzer genau dann zurückholt, wenn er tatsächlich bucht. Push erreicht dabei
deutlich mehr Leute als E-Mail.

Das ist ein realer Vorteil — er greift nur eben erst, wenn es Nutzer gibt.

## Wo eine App wirklich gewinnt

Nicht bei der Planung. Bei der **Reise selbst**:

- Tagesansicht offline, ohne Datenverbindung
- Karte offline
- Erledigt-Haken vor Ort
- Ausgaben nachtragen
- Erinnerung, wenn der reservierte Zeitpunkt näher rückt
- Adressen auf Japanisch zum Vorzeigen im Taxi

Das ist der On-Trip-Modus aus `12-nutzerkonto.md`, und dafür ist eine App das
richtige Werkzeug. Nur ist das eine Funktion für Nutzer, die **bereits geplant
haben** — also wieder: erst die Webseite.

## Die Zwischenstufe: installierbare Web-App (PWA)

Bevor eine native App überhaupt zur Debatte steht, holt eine PWA den grössten
Teil des Nutzens für einen Bruchteil des Aufwands:

| Funktion | PWA | Native App |
|---|---|---|
| Symbol auf dem Startbildschirm | ✓ | ✓ |
| Vollbild ohne Browserleiste | ✓ | ✓ |
| Offline lesen | ✓ | ✓ |
| Push-Nachrichten | ✓ Android; iOS nur wenn installiert | ✓ |
| Offline-Karten | eingeschränkt | ✓ |
| Kamera, Standort im Hintergrund | eingeschränkt | ✓ |
| Auffindbar bei Google | ✓ | ✗ |
| App-Store-Prüfung | entfällt | 1–7 Tage je Version |
| Zusätzliche Entwicklung | gering | erheblich |

Für dieses Produkt deckt eine PWA fast alles ab. Der Aufwand liegt im Bereich
von Tagen, nicht Monaten.

## Wenn es doch eine native App wird: was zu beachten ist

### Eine reine Webseiten-Hülle wird abgelehnt

Apple lehnt Apps ab, die im Wesentlichen nur die Webseite anzeigen
(Richtlinie zur Mindestfunktionalität). Die App muss etwas können, das der
Browser nicht kann — Offline-Karten, Push, Hintergrundstandort. Wer nur
verpackt, verliert Wochen im Prüfverfahren.

### Affiliate-Nachverfolgung ist in Apps heikler

Das ist der am meisten unterschätzte Punkt. Öffnet die App einen Partnerlink im
eingebetteten Browserfenster, kann die Zuordnung verloren gehen — Cookies werden
dort teils isoliert, und manche Programme zählen App-Traffic anders oder gar
nicht. **Vor dem Bau bei jedem Partner klären, ob und wie App-Traffic
nachverfolgt wird**, sonst arbeitet die App ohne Erlös.

Praktische Lösung: Partnerlinks im **externen** Browser öffnen, nicht im
eingebetteten Fenster.

### Weitere Punkte

- **Kosten:** Apple-Entwicklerkonto rund 99 USD pro Jahr, Google Play einmalig
  rund 25 USD. Dazu Zeit für Store-Texte, Bildschirmfotos, Antworten auf Bewertungen.
- **Prüfzeiten:** Jede Version durchläuft eine Prüfung. Ein dringender Fehler ist
  nicht in einer Stunde behoben wie im Web.
- **Datenschutzangaben** in beiden Stores, und unter iOS die Nachfrage zur
  Nachverfolgung — beides betrifft die Zuordnung von Buchungen.
- **Wartung:** Jedes Betriebssystem-Update erzwingt Nacharbeit. Rechne mit
  laufendem Aufwand, nicht mit einmaligem.
- **Zwei Plattformen:** Auch mit plattformübergreifenden Werkzeugen
  (React Native, Flutter) bleiben zwei Build-Ketten, zwei Store-Auftritte,
  zwei Fehlerquellen.

> Store-Regeln und Beteiligungssätze ändern sich regelmässig. Die Angaben hier
> sind Orientierung — vor einer Entscheidung beim jeweiligen Anbieter prüfen.

## Woran du merkst, dass die App dran ist

Nicht am Gefühl, sondern an Zahlen. Sinnvolle Schwellen:

- **10 000+ Besucher im Monat** über die Webseite, überwiegend organisch
- **Wiederkehrende Nutzer:** ein spürbarer Anteil öffnet seinen Trip mehrfach
- **Nachfrage aus der Nutzung:** Leute fragen von sich aus nach Offline-Zugriff
- **Erste Reisende:** Nutzer, die tatsächlich in Japan sind und die Seite dort öffnen

Sind drei dieser vier erfüllt, lohnt die On-Trip-App. Vorher baust du ein
Werkzeug für Nutzer, die es noch nicht gibt.

## Empfehlung als Reihenfolge

| Phase | Was | Warum |
|---|---|---|
| **Jetzt** | Webseite, mobil sauber bedienbar | Der einzige Kanal, der Nutzer bringt |
| **+3 Monate** | PWA-Fähigkeiten ergänzen | Startbildschirm, Offline-Lesen, Push — geringer Aufwand |
| **Bei Traffic** | On-Trip-Modus im Web bauen | Zeigt, ob der Anwendungsfall überhaupt genutzt wird |
| **Wenn genutzt** | Native App nur für den On-Trip-Teil | Offline-Karten und Push rechtfertigen dann den Aufwand |

Der Prototyp ist bereits so gebaut, dass er auf dem Handy vollwertig funktioniert
— zweispaltiges Raster, keine abgeschnittenen Funktionen. Der Weg über die PWA
ist damit schon vorbereitet.
