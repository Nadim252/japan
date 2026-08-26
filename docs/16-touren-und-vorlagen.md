# 16 — Fertige Touren, Vorlagen & buchbare Guided Tours

Das Wort „Tour“ meint im Konzept zwei verschiedene Dinge. Beide sind wertvoll,
aber sie sind unterschiedliche Objekte und müssen getrennt gebaut werden.

| | **Trip-Vorlage** | **Buchbare Tour** |
|---|---|---|
| Was | Kompletter fertiger Reiseplan (10 Tage Anime-Japan) | Einzelne geführte Aktivität (3h Food-Tour Osaka, Tagesausflug Nikko) |
| Dauer | Tage bis Wochen | Stunden bis 1 Tag |
| Rolle im Produkt | Startpunkt statt Onboarding | Item innerhalb einer Area |
| Geld | Indirekt (führt zu Hotel-/Aktivitätsbuchungen) | Direkt (Affiliate, 5–10 % CPA) |
| Wer erstellt | Redaktion, später Nutzer | Partner (Klook, GetYourGuide, lokale Anbieter) |

---

## Teil 1 — Trip-Vorlagen („Die Seite macht die Touren selber“)

### Der dritte Einstieg

Neben „Plan my trip for me“ und „Build my own trip“ gibt es einen dritten,
niedrigschwelligeren Weg: **einen fertigen Trip nehmen und umbauen.**

Für viele Nutzer ist das der angenehmste Start — ein leeres Formular macht
Entscheidungsdruck, ein fertiger Plan lädt zum Verändern ein.

```
/tours

Fertige Japan-Routen                              Filter: [Dauer] [Interesse] [Budget]

┌──────────────────────────┐ ┌──────────────────────────┐ ┌──────────────────────────┐
│ [Bild Akihabara Nacht]   │ │ [Bild Fushimi Inari]     │ │ [Bild verschneiter Onsen]│
│                          │ │                          │ │                          │
│ Anime & Otaku Japan      │ │ Klassisches Japan        │ │ Winter, Schnee & Onsen   │
│ 12 Tage · ab CHF 2'800   │ │ 10 Tage · ab CHF 2'400   │ │ 11 Tage · ab CHF 3'100   │
│ Tokyo · Nagoya · Osaka   │ │ Tokyo · Hakone · Kyoto   │ │ Tokyo · Nagano · Sapporo │
│ 🎌 Anime 🎴 TCG 🍜 Food   │ │ ⛩️ Tradition 🏯 Geschichte│ │ ♨️ Onsen ❄️ Natur         │
│                          │ │                          │ │                          │
│ [Ansehen] [Als meinen ✏️]│ │ [Ansehen] [Als meinen ✏️]│ │ [Ansehen] [Als meinen ✏️]│
└──────────────────────────┘ └──────────────────────────┘ └──────────────────────────┘

Weitere: Erste Japanreise (14 T) · Japan mit Kindern (12 T) · Food-Japan (10 T) ·
Kurztrip Tokyo (6 T) · Japan zu zweit (14 T) · Sakura-Route (13 T) ·
Japan abseits der Touristen (16 T) · Retro-Gaming-Route (9 T)
```

### Wie eine Vorlage technisch funktioniert

Eine Vorlage ist **ein normaler Trip** mit `is_template: true` und ohne Daten,
Preise und Reisendenzahl. Kein zweites Datenmodell.

`Als meinen übernehmen` löst aus:

1. Kurzer Dialog: *Wann reist du? · Wie viele Personen? · Dein Budget? · Flug und
   Hotel dazuplanen?* (4 Fragen statt 16 — das ist der ganze Punkt)
2. Vorlage wird als neuer Trip kopiert (`source: "template"` an jedem Item)
3. Datumsabhängiges wird konkretisiert: Hotelpreise, Flüge, Saison-Warnungen
   („Diese Route ist auf Kirschblüte ausgelegt — du reist im November. 4 Orte
   ersetzen?“)
4. Budgetanpassung: liegt die Vorlage über dem Budget, läuft direkt der
   Budget-Optimierer aus `08-budget-engine.md`
5. Danach ist es ein ganz normaler Trip — voll editierbar

### Warum Vorlagen strategisch wichtig sind

- **SEO:** „10 Tage Japan Reiseroute“ ist ein Suchvolumen-Monster. Eine
  Vorlagen-Seite ist eine statisch renderbare, inhaltlich starke Landingpage,
  die direkt in das Produkt führt — nicht in einen Blogartikel mit Sackgasse.
- **Kaltstart:** Vorlagen funktionieren, bevor der Generator gut ist. Sie sind
  redaktionell kuratiert und damit ab Tag 1 in hoher Qualität lieferbar.
- **Qualitäts-Benchmark:** Eine handkuratierte Vorlage ist der Massstab, an dem
  der Generator gemessen wird. Konkreter Test: *Kommt der Generator bei
  vergleichbarem Brief in die Nähe der Vorlage?* Wenn nicht, ist das Scoring falsch.
- **Vertrauen:** Ein Nutzer, der eine durchdachte fertige Route sieht, glaubt
  eher, dass auch die generierte etwas taugt.

### Nutzer-Vorlagen (v2)

Jeder abgeschlossene Trip kann als Vorlage veröffentlicht werden
(`Trip teilen → Als Route veröffentlichen`). Mit Moderation, Autorenprofil und
„X mal übernommen“-Zähler. Das ist der Weg zu Community-Content ohne
Community-Verwaltung — und die Vorlagen sind auf Anhieb echte, gereiste Routen.

---

## Teil 2 — Buchbare Guided Tours als Item-Typ

Bisher kennt das Datenmodell Items vom Typ POI. Geführte Touren sind aber etwas
anderes und brauchen eigene Felder:

```jsonc
{
  "id": "tour_osaka_food_night",
  "type": "guided_tour",
  "title": "Osaka Street Food bei Nacht (Dotonbori & Shinsekai)",
  "city_id": "osaka",
  "duration_min": 180,
  "start_point": { "lat": …, "lng": …, "description": "Ausgang 5, Namba Station" },
  "start_times": ["17:00", "18:30"],
  "days_of_week": [2,3,4,5,6],
  "languages": ["en", "de"],
  "group_size_max": 12,
  "price_jpy": 12000,
  "price_child_jpy": 8000,
  "includes": ["8 Verkostungen", "1 Getränk", "Guide"],
  "excludes": ["Transport zum Treffpunkt"],
  "cancellation": "kostenlos bis 24h vorher",
  "partner": { "provider": "klook", "product_id": "…", "commission_tier": "standard" },
  "blocks_area": true,          // belegt einen halben/ganzen Tag in der Area
  "reservation_level": "required",
  "lead_time_days": 3
}
```

### Regeln für Touren im Plan

- **`blocks_area`:** Eine 3-Stunden-Abendtour belegt den Abend dieser Area. Der
  Generator plant dann kein zweites Abendessen daneben — sonst entstehen die
  typischen unrealistischen KI-Pläne.
- **Überschneidungsprüfung:** Zwei Touren am selben Tag mit überlappenden Zeiten
  werden als Konflikt markiert, auch im `Flexible`-Modus.
- **Sprachfilter:** Touren, die es nicht in einer Sprache des Nutzers gibt,
  werden nicht empfohlen (häufigster Frustpunkt bei Aktivitätsbuchungen).
- **Nie automatisch setzen bei `booking_level: none`.** Wer im Onboarding gesagt
  hat „nur zeigen, was es gibt“, bekommt keine Bezahltouren vorgeschlagen.
- **Höchstens eine bezahlte Tour pro 3 Reisetage** im automatischen Plan. Ohne
  diese Regel optimiert sich der Generator schleichend auf Provision statt auf
  Reisequalität — und das merkt der Nutzer.

Die letzte Regel ist die wichtigste im ganzen Dokument. Sie steht bewusst hier
und nicht in der Affiliate-Doku: Der Provisionsdruck greift immer über die
Empfehlungslogik an, nicht über die Preisanzeige.

### Wo Touren auftauchen

| Ort | Darstellung |
|---|---|
| Explore | Eigener Filter `Geführte Touren`, mit Dauer, Sprache, Preis |
| Destination-Seite | Vorschlagszeile unter den Areas: „Geführte Touren in Osaka (6)“ |
| Item in einer Area | Wie ein POI, aber mit Uhrzeit, Treffpunkt und Stornofrist |
| Checkliste | Automatisch, wegen `lead_time_days` |

### Sinnvolle Tour-Kategorien für Japan

Food-Touren (Osaka, Tokyo), Sake-/Whisky-Tastings, geführte Wanderungen
(Kumano Kodo, Nakasendo), Tagesausflüge (Nikko, Kamakura, Fuji), Kochkurse,
Teezeremonie, Kimono-Verleih mit Fotoshooting, Anime-/Filmlocation-Touren mit
Guide, Kart-Touren, Tsukiji-/Toyosu-Frühmarkt-Touren, Onsen-Hopping mit Transport,
Sumo-Morgentraining.

Anime-Location-Touren mit Guide sind hier der interessanteste Fall: hohe Marge,
kaum Wettbewerb, und sie passen exakt zu unserer stärksten Nischen-Datenbank.
