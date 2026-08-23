# 02 — Homepage

## Aufgabe der Seite

Die Homepage hat genau eine Aufgabe: den Nutzer in **unter 10 Sekunden** in einen
der beiden Einstiege zu bringen — ohne dass er verstehen muss, was das Produkt
technisch ist.

## Above the fold

```
                Build your perfect Japan trip

     Tell us what you like. Get a complete route in 60 seconds.
                 Then change absolutely everything.

        ┌──────────────────────────┐   ┌──────────────────────────┐
        │  ✨ Plan my trip for me   │   │  🧩 Build my own trip     │
        │  60 seconds, AI-generated │   │  Start empty, add cities  │
        └──────────────────────────┘   └──────────────────────────┘

                   No account needed to start
```

- **Primär-CTA:** „Plan my trip for me“ (gefüllt, akzentfarben) → `/plan`
- **Sekundär-CTA:** „Build my own trip“ (Outline) → `/build`
- Beide CTAs sind gleich prominent platziert, aber visuell klar hierarchisiert.
  Ca. 80 % der Nutzer werden Variante 1 wählen; Variante 2 darf trotzdem nicht
  versteckt sein, weil sie die Power-User hält.
- Hintergrund: ein einziges, ruhiges Video/Bild-Loop (Shibuya Crossing bei Nacht,
  Fushimi Inari im Nebel). Keine Karussells.

## Section 2 — „So funktioniert es“ (drei Schritte)

| Schritt | Titel | Text |
|---|---|---|
| 1 | **Tell us about you** | 30–60 Sekunden: Dauer, Budget, Interessen, Reisetempo. |
| 2 | **Get your Japan route** | Wir bauen Route, Städte, Areas, Hotelvorschläge und Kostenschätzung. |
| 3 | **Make it yours** | Alles austauschbar, verschiebbar, ergänzbar. Der Plan gehört dir. |

Darunter, klein und ehrlich: *„Wir buchen nichts automatisch. Du entscheidest,
was du wo buchst.“*

## Section 3 — Live-Beispiel (das wichtigste Verkaufselement)

Ein **echter, interaktiver Beispiel-Trip** als eingebettetes Widget — kein
Screenshot. Der Besucher kann:

- zwischen 3 vorgefertigten Beispielen wechseln: *„14 Tage Anime & Food“*,
  *„10 Tage Klassiker“*, *„12 Tage Nature & Onsen (Winter)“*,
- einen Destination-Block aufklappen,
- ein Item entfernen und die Budget-Leiste live reagieren sehen.

Das kommuniziert die Kernidee (Modularität) besser als jeder Marketingtext.
CTA im Widget: „Make this trip mine“ → kopiert das Beispiel als Draft-Trip und
öffnet direkt das Onboarding mit vorbelegten Antworten.

## Section 4 — Interessens-Einstiege (SEO + Selbstselektion)

Kachel-Grid mit Themen, die je auf eine Landingpage/Explore-Filter führen:

`Anime & Manga` · `TCG & Pokémon` · `Food & Ramen` · `Tempel & Tradition` ·
`Natur & Wandern` · `Onsen` · `Retro Gaming` · `Shopping` · `Nightlife` ·
`Fotografie` · `Geschichte`

Jede Kachel → `/explore?interest=anime` bzw. eine kuratierte Themenseite.

## Section 5 — Datenbank-Beweis

Konkrete Zahlen statt Adjektive:

> **1.200+ kuratierte Orte** in 18 Städten · **340 Anime-Drehorte** mit Szenen-Referenz ·
> **Reservierungspflicht** für jeden Ort markiert · Preise in CHF, EUR, USD, JPY.

(Zahlen erst zeigen, wenn sie stimmen — siehe `13-content-pipeline.md`.)

## Section 6 — FAQ (aufklappbar, SEO)

- Ist das kostenlos? → Ja, Planung ist kostenlos.
- Bucht ihr für mich? → Nein. Wir verlinken Angebote; du buchst selbst.
- Brauche ich einen JR Pass? → kurze, ehrliche Antwort mit Link auf Transport-Doku.
- Kann ich den Plan später ändern? → Das ist der Punkt des Produkts.
- Funktioniert das auch für 5 Tage / 4 Wochen? → Ja, 3–30 Tage.

## Footer

Explore-Links nach Städten (Tokyo, Kyoto, Osaka, Hokkaido, Hiroshima, Kanazawa,
Hakone, Nara, Nagano, Fukuoka …), Themen-Links, Legal, Affiliate-Disclosure,
Sprache/Währung-Switcher.

## Technische Anforderungen

- Statisch gerendert (SSG), Hero-Bild < 200 kB, LCP < 1,5 s auf 4G.
- Keine Blocking-Third-Party-Scripts über dem Fold.
- Der Beispiel-Trip lädt sein JSON lazy nach dem Fold.
- Sprache: automatische Erkennung (DE/EN), manuell umschaltbar; Währung aus
  IP-Region vorbelegt (CHF für CH, EUR für EU, sonst USD), immer überschreibbar.

## Copy-Regeln

- Keine Superlative ohne Beleg („beste“, „perfekteste“).
- Nie „AI plant deine Reise“ ohne den Halbsatz „…und du änderst alles“.
- Aktive, kurze Sätze. Deutsch und Englisch werden getrennt getextet, nicht übersetzt.
