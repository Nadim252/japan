# 00 — Produktvision & Prinzipien

## Einzeiler

**AI-generierter Japan-Trip + vollständig modularer Trip-Builder.**

Nicht „Japan Travel AI“. Der Unterschied ist entscheidend: Die AI liefert den
ersten Entwurf, danach besitzt der Nutzer den Plan.

## Produktgrundsatz

> Die AI macht dir den ersten Entwurf. Du besitzt danach den Plan.

Jede Design-Entscheidung wird an diesem Satz gemessen. Wenn eine Funktion dem
Nutzer Kontrolle wegnimmt (automatische Umbuchung, unveränderbare Zeitpläne,
„die AI weiss es besser“-Dialoge), fliegt sie raus.

## Warum das Produkt existiert

Japan ist das Land mit dem grössten Delta zwischen *Reiselust* und
*Planungsaufwand*:

- Riesige Distanzen, aber exzellenter, komplizierter Nahverkehr (JR Pass, IC-Karten, Shinkansen-Reservierungen).
- Extrem dichte Interessens-Nischen (Anime, TCG, Retro-Gaming, Onsen, Küchenregionen), die in Standard-Reiseführern kaum abgebildet sind.
- Sehr viele Dinge müssen vorab reserviert werden (teamLab, Ghibli Museum, Shibuya Sky, bestimmte Restaurants) — und der Nutzer erfährt das oft zu spät.
- Sprachbarriere bei der Recherche: Die besten Quellen sind japanisch.

Bestehende Lösungen sind entweder starre Templates („10 Tage Japan Standard“)
oder ChatGPT-Ausgaben ohne Datenbank, Preise, Buchbarkeit und Editierbarkeit.

## Zielgruppen

| Persona | Situation | Erwartung an das Produkt |
|---|---|---|
| **Der Überforderte** | Erste Japanreise, 2–3 Wochen, weiss nur „Tokyo und Kyoto“ | Will in 60 Sekunden einen kompletten, realistischen Plan. Ändert danach wenig. |
| **Der Optimierer** | Plant seit Monaten, hat Notion-Dokumente und 40 Browser-Tabs | Will den Baukasten. Nutzt die AI nur zum Auffüllen von Lücken. |
| **Der Themenreisende** | Anime / TCG / Onsen / Food als Hauptmotiv | Will Nischen-POIs, die kein Reiseführer hat, und Filter nach Thema. |
| **Die Gruppe** | Paar oder 3–5 Freunde, unterschiedliche Interessen | Will teilen, kommentieren, gemeinsame Entscheidungen (v2). |

## Nicht-Ziele (bewusst)

- **Kein Minutenplan.** Keine „10:15 Tempel / 11:35 Restaurant“-Ausgabe, ausser
  der Nutzer schaltet explizit den Zeitplan-Modus ein.
- **Kein eigenes OTA.** Wir sind kein Buchungsanbieter, wir nehmen kein Geld für
  Hotels/Flüge entgegen und tragen kein Reiseveranstalter-Risiko. Wir verlinken
  (Affiliate) oder der Nutzer bucht selbst.
- **Kein Social Network.** Kein Feed, keine Follower. Teilen ja, Community v3.
- **Kein Live-Reisebegleiter in v1.** Der Fokus liegt auf Planung vor der Reise;
  On-Trip-Modus ist Roadmap.
- **Kein Weltweit-Planer.** Japan-only. Die Tiefe ist das Produkt.

## Produktprinzipien

1. **Vorschlag statt Zwang.** Die AI schlägt vor, der Nutzer entscheidet. Jede
   AI-Ausgabe ist ein Vorschlag mit sichtbarer Ablehnmöglichkeit.
2. **Alles ist ein Block.** Städte, Tage, Areas, POIs, Hotels, Flüge, Transfers —
   alles sind austauschbare Module mit identischer Interaktionslogik.
3. **Vier Aktionen, überall gleich.** `Keep` · `Remove` · `Replace` · `Explore more`.
   Der Nutzer lernt die Interaktion einmal und kann danach alles bedienen.
4. **Nie eine Sackgasse.** Wer etwas löscht, bekommt sofort die Folgefrage
   („Du hast 3 freie Nächte — wohin?“). Der Plan bleibt immer konsistent.
5. **Budget läuft immer mit.** Jede Änderung aktualisiert die Kostenschätzung in
   Echtzeit, inklusive Warnung bei Überschreitung.
6. **Buchbarkeit ist optional, Planung nie.** Ein Element bleibt vollwertig im
   Trip, auch wenn der Nutzer „I'll book this myself“ wählt.
7. **Die zwei Modi vermischen sich.** „Plan for me“ und „Build my own“ sind
   Einstiege in dasselbe Objekt, keine getrennten Produkte.
8. **Ehrlichkeit über Unsicherheit.** Preise sind Schätzungen und werden als
   solche gekennzeichnet, mit Datum. Öffnungszeiten mit Stand-Datum.

## Die zwei Einstiege

```
                 ┌────────────────────────┐
                 │       Homepage         │
                 └───────────┬────────────┘
             ┌───────────────┴───────────────┐
   ✨ Plan my trip for me            🧩 Build my own trip
             │                               │
        Onboarding (Q&A)              Leerer Trip
             │                               │
        Generator (AI)              + Add destination
             │                               │
             └───────────────┬───────────────┘
                             ▼
                     ┌──────────────┐
                     │ Trip-Dashboard│  ← ein einziges Objekt
                     └──────────────┘
                             │
        ┌────────────────────┼────────────────────┐
   Destination-Seite    Explore-DB        „Improve with AI“
```

Ein Nutzer kann jederzeit: generieren lassen → 30 % löschen → eigenes ergänzen →
Stadt tauschen → Hotel selbst buchen → AI den Rest neu optimieren lassen.

## Erfolgsmetriken (Nordstern & Guardrails)

**Nordstern:** Anzahl Trips, die den Zustand *„committed“* erreichen (mindestens
eine Buchung markiert ODER Trip exportiert/geteilt ODER 7 Tage nach Erstellung
erneut geöffnet).

Unterstützende Metriken:

- Onboarding-Completion-Rate (Ziel > 70 %).
- Time-to-first-plan (Ziel < 45 s inkl. Generierung).
- Edit-Rate: Anteil Trips mit ≥ 1 manueller Änderung (Ziel > 60 % — beweist, dass der Baukasten benutzt wird).
- Explore-Add-Rate: POIs, die aus der Explore-DB hinzugefügt werden, pro Trip.
- Affiliate-Klickrate und Conversion pro Kategorie.
- Regenerierungs-Rate: Wie oft wird der komplette Plan verworfen (hoch = schlechte Generator-Qualität).

## Monetarisierung (Überblick)

1. **Affiliate** (Hotels, Aktivitäten, eSIM, Versicherung, Flug, JR-Produkte) — Haupterlös v1. Details in `11-booking-affiliate.md`.
2. **Pro-Abo** (später): unbegrenzte Trips, Offline-PDF/Export, Collaboration, Preis-Alerts.
3. **Kein Paywall vor dem ersten Plan.** Der erste vollständige Trip ist immer gratis und ohne Account einsehbar.
