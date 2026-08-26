# 18 — Woher die Fotos kommen

## Warum das eine Produktfrage ist, keine Fleissarbeit

Bei einer bildgetriebenen Oberfläche entscheidet das Foto, ob jemand einen Ort
versteht, ohne den Namen zu lesen. Ohne Bilder ist die Seite ein Verzeichnis;
mit Bildern ist sie ein Reisekatalog. Deshalb steht die Bildbeschaffung hier
neben der POI-Recherche und nicht darunter.

## Was grosse Reiseseiten tatsächlich machen

Die verbreitete Annahme ist, dass die grossen Portale sich einfach im Netz
bedienen. Das stimmt so nicht. Sie beziehen ihre Bilder aus fünf Quellen:

| Quelle | Wer nutzt das | Rechtsgrundlage |
|---|---|---|
| **Partner-/Affiliate-APIs** | Vergleichsportale, Blogs mit Buchungslinks | Der Partnervertrag räumt die Nutzung ausdrücklich ein |
| **Nutzerfotos** | TripAdvisor, Google Maps | Die Nutzungsbedingungen holen sich beim Hochladen eine Lizenz |
| **Bezahltes Stockmaterial** | Redaktionelle Portale | Lizenzvertrag mit Getty, Adobe Stock u. a. |
| **Eigene Fotografie** | Grössere Anbieter mit Redaktion | Eigenes Urheberrecht |
| **Pressematerial** | Alle | Hotels, Museen, Betreiber geben Bilder frei, oft mit Bedingungen |

Der entscheidende Punkt für uns ist die erste Zeile.

## Der Affiliate-Weg deckt den grössten Teil ab

Wer Partner von Booking, Agoda, Klook oder GetYourGuide ist, bekommt über deren
Schnittstellen **die Bilder mitgeliefert — mit ausdrücklicher Nutzungserlaubnis
für die Bewerbung der Angebote**. Genau deshalb dürfen Vergleichsseiten
Hotelfotos zeigen.

Da wir ohnehin Affiliate werden (siehe `17-monetarisierung.md`), fällt damit ein
grosser Teil des Bildbedarfs kostenlos und rechtlich sauber an:

- **Unterkünfte** — vollständig abgedeckt, meist 20–40 Fotos pro Haus
- **Aktivitäten und Tickets** — teamLab, Themenparks, Museen, Touren, Kochkurse
- **Geführte Touren** — inklusive

Das ist der wichtigste Satz dieses Dokuments: **Die Bildbeschaffung ist genau
dort gelöst, wo auch das Geld verdient wird.**

Zu prüfen bleibt pro Programm, ob die Bildnutzung an einen aktiven Buchungslink
gebunden ist. In der Regel ja — was für uns kein Problem ist, weil neben jedem
Hotel und jeder Aktivität ohnehin ein Angebotslink steht.

## Was der Affiliate-Weg nicht abdeckt

Übrig bleiben Schreine, Tempel, Parks, Aussichtspunkte, Strassen, Läden und
Restaurants ohne Ticketverkauf. Also ausgerechnet der Teil, der unsere Nische
ausmacht. Dafür je nach Typ:

### Sehenswürdigkeiten, Tempel, Natur, Stadtansichten

**Wikimedia Commons.** Sehr gute Abdeckung für alles Bekannte — Meiji-Schrein,
Fushimi Inari, Sensō-ji, Bambuswald, Dōtonbori, Burg Osaka. Lizenzen sind
meist CC BY oder CC BY-SA, teils gemeinfrei.

Bedingung ist die Namensnennung: Urheber, Lizenz und idealerweise ein Link zur
Quelle. Das Foto-Register im Prototyp speichert dafür bereits `by` und `lic` und
setzt den Nachweis automatisch in die Fusszeile.

Ein verbreitetes Missverständnis: **CC BY-SA zwingt nicht die ganze Webseite
unter dieselbe Lizenz.** Die Weitergabebedingung greift bei Bearbeitungen des
Bildes, nicht beim blossen Einbinden auf einer Seite. Zuschnitt und
Grössenanpassung gelten allerdings als Bearbeitung — im Zweifel unverändert
einbinden.

### Kostenlose Stockportale

Unsplash, Pexels und Pixabay erlauben kommerzielle Nutzung ohne Namensnennung.
Für Stimmungsbilder (Strassenszenen, Essen allgemein, Landschaft) brauchbar,
für konkrete Orte meist zu unspezifisch — und ein austauschbares Stockfoto
untergräbt genau den Eindruck, den wir wollen.

### Läden und Restaurants — der harte Teil

Von Super Potato, Mandarake oder einem bestimmten Ramen-Laden gibt es kaum frei
lizenziertes Material. Vier gangbare Wege:

1. **Beim Betrieb anfragen.** Kostet eine E-Mail, funktioniert überraschend oft.
   Viele japanische Läden geben Bilder frei, wenn verlinkt wird. Antwort und
   Bedingungen dokumentieren.
2. **Google Places Photos API.** Technisch die einfachste Abdeckung, aber an die
   Nutzungsbedingungen der Google Maps Platform gebunden — unter anderem
   Attributionspflicht und Einschränkungen beim dauerhaften Speichern. Kostenpflichtig
   ab einem Kontingent. Vor dem Einsatz die aktuellen Bedingungen prüfen; sie ändern sich.
3. **Eigene Fotos.** Auf der nächsten Japanreise gezielt fotografieren. Für die
   Phase-1-Städte durchaus machbar und langfristig das wertvollste Material,
   weil es sonst niemand hat.
4. **Nutzerfotos** (später). Wer den Ort besucht hat, lädt ein Bild hoch. Braucht
   Moderation und eine Lizenzklausel in den Nutzungsbedingungen.

### Wo nichts vorliegt

Bleibt das erzeugte Motiv aus dem Prototyp. Das ist kein Notbehelf, sondern eine
bewusste Entscheidung: eine Kachel mit Torii-Umriss und Kategoriefarbe wirkt
gepflegter als ein graues Rechteck oder ein beliebiges Stockfoto, das den Ort
nicht zeigt.

## Die rechtliche Lage, nüchtern

Ein Foto ohne Erlaubnis zu verwenden, ist eine Urheberrechtsverletzung — auch
dann, wenn es frei im Netz auffindbar war, auch mit Quellenangabe, auch bei
kleiner Darstellung.

Im deutschsprachigen Raum ist das praktisch relevant, weil Fotografen und
Agenturen automatisiert per Rückwärtssuche nach ihren Bildern fahnden.
Grössenordnung einer Abmahnung: einige hundert bis einige tausend Franken oder
Euro pro Bild, plus Unterlassungserklärung.

Das eigentliche Risiko ist aber nicht das einzelne Bild, sondern die Skalierung:
Bei 800 Orten mit ungeklärten Bildern reicht ein Fund, um die gesamte Seite ins
Visier zu nehmen. Und die Bilder wären dann alle zu ersetzen — genau dann, wenn
die Seite Reichweite hat.

Dass manche kleine Seite damit eine Weile durchkommt, stimmt. Es ist nur eine
schlechte Wette, wenn es für den grössten Teil des Bedarfs kostenlose, saubere
Quellen gibt.

**Wichtige Unterscheidung:** Ein privater Prototyp, den niemand ausser dir sieht,
ist praktisch kein Risiko — Risiko entsteht durch Veröffentlichung. Zum Ausprobieren
des Aussehens ist also alles egal; für die Livefassung muss die Herkunft stehen.

## Arbeitsregel

> Kein Bild geht live, ohne dass Quelle und Lizenz im Datensatz stehen.

Das Feld existiert bereits (`pois.images[]` in `09-datenmodell.md`, `by`/`lic`
im Foto-Register des Prototyps). Es kostet beim Erfassen zehn Sekunden pro Bild
und erspart später eine vollständige Nachrecherche.

## Vorgehen für Phase 1

| Schritt | Umfang | Aufwand |
|---|---|---|
| 1. Affiliate-Programme beantragen | Booking, Klook, GetYourGuide | Wartezeit, kein Aufwand |
| 2. Bilder aus den Partner-APIs übernehmen | Alle Hotels, alle Ticket-Orte | automatisiert |
| 3. Wikimedia für Sehenswürdigkeiten | ca. 60–80 Orte in Tokyo, Kyoto, Osaka | 1–2 Tage, inkl. Nachweisen |
| 4. Läden anschreiben | ca. 30 Betriebe | verteilt über Wochen |
| 5. Rest mit Motiv | alles Übrige | bereits erledigt |

Nach Schritt 2 und 3 dürfte der grösste Teil der sichtbaren Fläche echte Fotos
zeigen — ohne einen Franken für Bildmaterial und ohne rechtliches Restrisiko.
