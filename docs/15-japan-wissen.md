# 15 — Japan-Grundwissen („Know before you go“)

## Warum das ein Produktmodul ist und kein Blog

Fast jeder Japan-Reisende stolpert über dieselben zehn Dinge: kein Bargeld dabei,
Tattoo im Onsen, Trinkgeld gegeben, im Zug telefoniert, Müll in der Hand durch
halb Tokyo getragen, IC-Karte nicht gekauft, Steckdose falsch, Schuhe im Ryokan
angelassen.

Diese Informationen existieren tausendfach im Netz — als generische Listicles,
die man vor der Reise einmal liest und im entscheidenden Moment vergessen hat.

**Unser Vorteil: Wir wissen, wohin der Nutzer geht und was er dort vorhat.**
Deshalb wird Wissen nicht als Ratgeber-Seite ausgeliefert, sondern **kontextuell
an das Trip-Element geheftet, für das es relevant ist**.

## Drei Auslieferungsformen

### 1. Kontext-Hinweise am Item (der wichtigste Kanal)

Ein Hinweis erscheint dort, wo er gebraucht wird — nicht in einem Kapitel, das
niemand liest.

```
┌─ Ryokan Kamogawa (Hakone) ────────────────────────────────┐
│ 🏨 1 Nacht · Kaiseki-Dinner inkl. · ¥42'000               │
│                                                            │
│ ℹ️  Gut zu wissen für diesen Ort                           │
│    • Schuhe werden im Eingangsbereich ausgezogen           │
│    • Abendessen ist auf 18:00 fixiert — plant den Tag so   │
│    • Onsen: Tattoos sind hier nicht erlaubt  ⚠️            │
│      → 3 tattoo-freundliche Alternativen in Hakone         │
└────────────────────────────────────────────────────────────┘
```

Diese Hinweise sind **Datenfelder am POI**, keine Fliesstexte: `poi.notices[]`
mit `{ code, severity, text_de, text_en, applies_when }`. Dadurch sind sie
filterbar, übersetzbar und lösen konkrete Folgeaktionen aus (im Beispiel: der
Tattoo-Hinweis bietet direkt Alternativen an).

**Wichtig:** `applies_when` verhindert Rauschen. Der Tattoo-Hinweis erscheint nur,
wenn der Nutzer im Onboarding `tattoo_friendly` als Bedürfnis markiert hat oder
einen Onsen im Plan hat. Ein Hinweis, den 90 % der Nutzer wegklicken, ist ein
Bug, kein Feature.

### 2. Trip-Briefing (automatisch generiert, druckbar)

Ein Abschnitt im Trip, der sich **aus dem konkreten Plan** zusammensetzt:

```
🎒 Für deinen Trip wichtig                        [Als PDF] [Auf Handy senden]

Geld
  Du hast 6 Orte im Plan, die nur Bargeld nehmen (Super Potato, Omoide
  Yokocho, 4 weitere). Rechne mit ¥30'000–50'000 Bargeld für 14 Tage.
  Abheben: 7-Eleven- und Japan-Post-Automaten akzeptieren ausländische Karten.

Transport
  Hol dir am Flughafen eine Suica oder Pasmo (IC-Karte) — funktioniert in
  Tokyo, Kyoto und Osaka. Dein JR Pass lohnt sich nicht (siehe Budget).
  Deine 3 Shinkansen-Fahrten brauchen keine Reservierung, ausser in der
  Golden Week — du reist am 12.–26. April, das passt.

Konnektivität
  eSIM vor dem Abflug aktivieren, kostenloses WLAN ist in Japan schlechter
  als sein Ruf.                                        [Angebote ansehen]

Für dich speziell
  • Du hast 4 TCG-Shops im Plan: Karten werden fast überall nur bar bezahlt.
  • Du hast 2 Onsen im Plan → Tattoo-Regeln beachten.
  • Kaiseki-Dinner in Hakone: Absagen weniger als 3 Tage vorher kosten voll.

Immer gut zu wissen
  Kein Trinkgeld · Nicht im Zug telefonieren · Müll mitnehmen, es gibt kaum
  Abfalleimer · Schuhe aus, wo Tatami liegt · Links stehen auf der Rolltreppe
  (Tokyo), rechts in Osaka
```

Der Abschnitt „Für dich speziell“ ist der Punkt, an dem sich das Produkt von
jedem Ratgeber unterscheidet: Er ist aus dem Plan abgeleitet, nicht aus einer
Liste.

### 3. Wissens-Bibliothek (SEO)

Öffentliche, redaktionelle Seiten unter `/guide/*` — dieselben Datensätze, nur
als vollständige Artikel gerendert:

```
/guide/geld-und-bezahlen        /guide/ic-karten-suica-pasmo
/guide/jr-pass-lohnt-sich       /guide/onsen-regeln
/guide/tattoos-in-japan         /guide/etikette-und-benimmregeln
/guide/essen-bestellen          /guide/vegetarisch-vegan-in-japan
/guide/beste-reisezeit          /guide/golden-week-obon-neujahr
/guide/internet-esim-wlan       /guide/gepaecktransfer-takkyubin
/guide/mit-kindern-in-japan     /guide/erdbeben-und-notfall
/guide/shopping-tax-free        /guide/anime-orte-in-echt
```

Diese Seiten sind hochwertiger SEO-Content mit hoher Suchintention und führen
über `[Diesen Trip planen]` zurück ins Onboarding.

## Datenmodell

```jsonc
// knowledge_notices — global, wiederverwendbar
{
  "code": "onsen_tattoo",
  "category": "etiquette | money | transport | connectivity | food | health | safety | shopping | culture",
  "severity": "info | important | critical",
  "title_de": "Tattoos im Onsen",
  "body_de": "…",
  "applies_when": {
    "poi_tags": ["onsen"],
    "user_flags": ["has_tattoo"],
    "cities": null,
    "seasons": null,
    "party": null
  },
  "actions": [
    { "label": "Tattoo-freundliche Onsen zeigen", "type": "explore", "filter": { "tattoo_friendly": true } }
  ],
  "guide_slug": "tattoos-in-japan",
  "verified_at": "2026-02-01"
}
```

- Ein Notice kann an einem POI hängen (`poi.notices[]`), an einer Stadt, an einer
  Saison oder an einem Nutzerprofil-Flag.
- Der Trip-Briefing-Generator sammelt alle zutreffenden Notices, dedupliziert sie
  und sortiert nach `severity`.
- `verified_at` ist Pflicht: Regeln ändern sich (Tax-Free-Grenzen, IC-Karten-
  Verfügbarkeit, Visabestimmungen). Ein Hinweis älter als 12 Monate geht in den
  Review-Queue (siehe `13-content-pipeline.md`).

## Ein zusätzliches Onboarding-Feld

Für die Personalisierung braucht es genau eine zusätzliche Frage — optional und
ganz am Ende, weil sie sonst zu persönlich wirkt:

```
Noch etwas, das wir berücksichtigen sollen?  (optional)
[ ] Ich habe Tattoos          [ ] Ich bin Vegetarier/Veganer
[ ] Ich reise mit Kindern     [ ] Eingeschränkte Mobilität
[ ] Ich spreche kein Englisch [ ] Erste Fernreise
```

→ `brief.user_flags[]`. Jedes Flag schaltet konkrete Notices und POI-Filter
scharf. „Erste Fernreise“ erhöht die Ausführlichkeit des Briefings spürbar.

## Abgrenzung: kein Rechtsrat

Visa, Impfungen, Zoll und Medikamenteneinfuhr (in Japan streng — z. B.
pseudoephedrinhaltige Erkältungsmittel) werden **nur mit Quellenangabe, Datum
und Link auf die offizielle Stelle** ausgeliefert, nie als eigene Aussage. Ein
falscher Hinweis zur Medikamenteneinfuhr ist kein Content-Fehler, sondern ein
echtes Problem für den Nutzer.
