# Produktspezifikation — Japan Trip Planner

**AI-generierter Japan-Trip + vollständig modularer Trip-Builder.**

> Die AI macht dir den ersten Entwurf. Du besitzt danach den Plan.

Diese Spezifikation beschreibt das gesamte Produkt vom Einstieg bis zur
Datenbank — detailliert genug, um sie direkt als Bauplan an eine Umsetzung
(Mensch oder Coding-Agent) zu geben.

## Dokumente

| # | Dokument | Inhalt |
|---|---|---|
| 00 | [Produktvision & Prinzipien](00-produkt-vision.md) | Positionierung, Zielgruppen, Nicht-Ziele, Produktprinzipien, Metriken |
| 01 | [Informationsarchitektur](01-informationsarchitektur.md) | Sitemap, Routing, Trip-Hierarchie, Flexible/Days/Schedule-Modi, Responsive |
| 02 | [Homepage](02-homepage.md) | Zwei Einstiege, Sections, Live-Beispiel-Trip, SEO, Copy-Regeln |
| 03 | [Onboarding](03-onboarding.md) | Alle 16 Fragen mit Feldnamen, Defaults, konditionaler Logik |
| 04 | [Generator](04-generator.md) | 10-stufige Pipeline, Scoring-Formeln, Validierungs-Regelwerk, Determinismus, Sicherheit |
| 05 | [Trip-Dashboard](05-trip-dashboard.md) | Layout, Block-Typen, Nächte-Umverteilung, „Improve with AI“, Undo |
| 06 | [Destination & Items](06-destination-und-items.md) | Hotel-Modul, Areas, Item-Karten, `Keep/Remove/Replace/Explore more`, Reservierungsstufen |
| 07 | [Explore-Datenbank](07-explore-datenbank.md) | Filter-Facetten, Karte, POI- und Stadt-Seiten, eigene Einträge |
| 08 | [Budget-Engine](08-budget-engine.md) | Kostenkategorien, Food-Modell, Live-Deltas, Optimierer, JR-Pass-Rechner |
| 09 | [Datenmodell](09-datenmodell.md) | Trip-JSON, Referenzdaten, Event-Log, Persistenz |
| 10 | [API](10-api.md) | Endpunkte, SSE-Generator-Stream, Fehlerformat, Latenzziele |
| 11 | [Booking & Affiliate](11-booking-affiliate.md) | Partnermodell, drei Buchungszustände, Offenlegung, Reservierungs-Checkliste |
| 12 | [Nutzerkonto](12-nutzerkonto.md) | Anonyme Drafts, Registrierungsmomente, Teilen, Export |
| 13 | [Content-Pipeline](13-content-pipeline.md) | Datenquellen, Qualitätsstufen, Redaktion, Feedback-Loops |
| 14 | [MVP & Roadmap](14-mvp-roadmap.md) | Scope-Schnitte v0.1 → v3, Tech-Stack, Risiken, Baureihenfolge |
| 15 | [Japan-Grundwissen](15-japan-wissen.md) | Kontextuelle Hinweise am Item, generiertes Trip-Briefing, Wissens-Bibliothek |
| 16 | [Touren & Vorlagen](16-touren-und-vorlagen.md) | Fertige Routen als dritter Einstieg, buchbare Guided Tours als Item-Typ |
| 17 | [Monetarisierung](17-monetarisierung.md) | Alle Erlöskanäle mit Richtwerten, Berührungspunkte im Produkt, Nicht-Affiliate-Quellen |
| 18 | [Bildquellen](18-bildquellen.md) | Woher echte Fotos legal kommen, Quelle je Kategorie, Rechtslage, Vorgehen für Phase 1 |

## Prototyp

Unter [`prototype/`](../prototype/index.html) liegt ein klickbarer Prototyp:
Fragebogen → generierte Route → Übersicht → Stadtansicht mit Bildraster.
Orte werden durch Antippen ausgewählt, das Budget läuft live mit, und ein
Schalter markiert alle Monetarisierungspunkte. Eine Datei, keine
Abhängigkeiten — im Browser öffnen genügt.

## Die acht Kernideen in Kurzform

1. **Drei Einstiege, ein Produkt.** „Plan my trip for me“, „Build my own trip“
   und „Fertige Route übernehmen“ führen in dasselbe Trip-Objekt und lassen sich
   jederzeit mischen.
2. **Destination Blocks statt Uhrzeiten.** Standard ist „Tokyo — 5 Nächte,
   Area 1 Shibuya + Harajuku“, nicht „10:15 Tempel“. Zeitpläne nur auf Wunsch.
3. **Vier Aktionen überall.** `Keep` · `Remove` · `Replace` · `Explore more` —
   für Items, Areas, Hotels, Transporte, Städte.
4. **Nie eine Sackgasse.** Stadt entfernen → sofort die Frage, wohin die freien
   Nächte gehen, inklusive AI-Empfehlung und Kostendelta.
5. **Budget läuft live mit.** Jede Änderung aktualisiert die Kostenschätzung;
   bei Überschreitung schlägt das System konkrete Einsparungen als Diff vor.
6. **Die AI erfindet nichts.** Sie wählt aus einer kuratierten Datenbank aus und
   formuliert — Preise, Orte, Zeiten kommen nie aus dem Modell.
7. **Buchen ist optional.** „I'll book this myself“ verliert nie Daten; der
   Reservierungspflicht-Check ist der eigentliche Japan-Mehrwert.
8. **Der Nutzer besitzt den Plan.** Gepinnte Elemente überleben jede
   Regenerierung, jeder AI-Vorschlag ist ein annehmbarer Diff.
9. **Wissen kommt kontextuell, nicht als Ratgeber.** Der Tattoo-Hinweis erscheint
   am Onsen, die Bargeld-Warnung am Laden, der Rest im generierten Trip-Briefing.
