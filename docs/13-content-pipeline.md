# 13 — Content- & Datenpipeline

## Warum das der eigentliche Burggraben ist

Die Oberfläche kann jeder nachbauen. Der Generator ohne Datenbank ist ein
Chatbot. Was das Produkt verteidigbar macht, ist eine **kuratierte, aktuelle,
tief getaggte Japan-Datenbank** — insbesondere in den Nischen, die niemand pflegt
(Anime-Drehorte mit Szenenreferenz, TCG-Läden, Retro-Gaming, tattoo-freundliche
Onsen, Läden mit englischem Service).

## Qualitätsstufen

| Tier | Bedeutung | Anforderung |
|---|---|---|
| **Tier 1** | Redaktionell geprüft | Eigener Text, verifizierte Öffnungszeiten (< 6 Monate), verifizierte Reservierungsinfo, eigenes oder lizenziertes Bild |
| **Tier 2** | Aggregiert + geprüft | Aus Quellen zusammengeführt, automatisch plausibilisiert, Stichprobe geprüft |
| **Tier 3** | Aggregiert | Nur maschinell, nicht generatorfähig für Top-Empfehlungen |

**Regel:** Der Generator darf nur Tier 1 und Tier 2 als Kern-Empfehlungen
setzen. Tier 3 erscheint nur in Explore, klar als „ungeprüft“ markiert.

## Quellen

| Datentyp | Quelle |
|---|---|
| Basis-POI (Name, Geo, Kategorie, Öffnungszeiten) | OpenStreetMap, Google/Places (lizenzkonform), offizielle Websites |
| Bewertungen | Aggregierte Scores mit Quellenangabe (keine Volltext-Kopien) |
| Öffnungszeiten & Feiertage | Offizielle Sites, jap. Feiertagskalender, saisonale Ausnahmen |
| Reservierungspflicht/-fristen | Manuell recherchiert, pro Ort dokumentiert — der wertvollste Datensatz |
| Anime-Locations | Fan-Wikis, Location-Hunting-Blogs, jap. Quellen — **immer** manuell verifiziert (Geo + Szene) |
| TCG/Gaming/Hobby | Fachforen, jap. Ladenverzeichnisse, eigene Recherche |
| Hotels & Preise | Partner-APIs (live) |
| Verbindungen & Preise | Bahnbetreiber, Fahrplandaten, manuell gepflegte Kernstrecken |
| Bilder | Lizenziert (Unsplash/Partner), eigene, offizielle Pressebilder — Lizenz je Bild dokumentiert |

Lizenz-Compliance ist kein Nebenschauplatz: Pro Datensatz werden `source_refs`
und Lizenz gespeichert, sodass ein Audit jederzeit möglich ist.

## Pipeline

```
Ingest        → Normalisieren → Deduplizieren (Geo + Name-Fuzzy)
              → Auto-Tagging (Regeln + LLM-Vorschlag)
              → Qualitätsprüfung (Regelwerk)
              → Redaktionelle Freigabe (nur Tier 1/2)
              → Publish → Suchindex + Cache-Invalidierung
```

### Auto-Tagging

Ein LLM schlägt Tags, Kategorie, `tourist_density`, `best_time_of_day` und einen
Beschreibungsentwurf vor. **Nichts davon geht ungeprüft live** — der Vorschlag
landet im Review-Queue. Der Mensch bestätigt oder korrigiert; die Korrekturen
sind gleichzeitig Trainingsmaterial für bessere Prompts.

### Automatische Qualitätsprüfungen

- Geo innerhalb Japans und innerhalb des zugeordneten Area-Polygons
- Öffnungszeiten syntaktisch valide, `hours_checked_at` nicht älter als 12 Monate
- Kategorie ↔ Tags konsistent (kein `temple` mit Tag `nightlife`)
- Preis plausibel für Kategorie
- Duplikat-Score < Schwelle
- Bildlizenz vorhanden
- Ort noch existent (periodischer Check gegen Quellen; „permanently closed“-Signale)

## Redaktions-Backoffice

Interne Oberfläche mit:

- Review-Queue (neue POIs, Nutzer-Meldungen, abgelaufene Öffnungszeiten)
- Bulk-Editor für Tags
- Area-Editor (Polygone zeichnen)
- Stadt-Konfiguration (`min/ideal/max_nights`, `interest_fit`, Saisonmodifikatoren)
- Vorschau: „Wie sieht ein generierter Trip mit Profil X aus?“ (Generator-Sandbox)
- Publish-Workflow mit Snapshot-Versionierung

## Feedback-Loops

Das Produkt verbessert seine eigenen Daten:

| Signal | Wirkung |
|---|---|
| Item wird häufig entfernt (Remove-Rate hoch) | `editorial_rating` sinkt, Review-Task |
| Item wird häufig gepinnt (`Keep`) | Score-Bonus |
| Item wird häufig aus Explore *hinzugefügt* | Kandidat für Generator-Aufwertung |
| „Report a problem“-Meldungen | Direkt in die Queue, ab 3 Meldungen automatisch `needs_review` |
| Custom-POI mehrfach von verschiedenen Nutzern angelegt | Kandidat für die globale DB |
| Nutzer markiert `done` (On-Trip, v2) | Stärkstes Qualitätssignal überhaupt |

Wichtig: Feedback verschiebt **Gewichte**, nie harte Fakten. Öffnungszeiten
ändern sich nur durch verifizierte Quellen.

## Aufbau-Reihenfolge (Content-Roadmap)

| Phase | Umfang |
|---|---|
| **Phase 1 (MVP)** | Tokyo, Kyoto, Osaka — je 150–250 Tier-1/2-POIs, alle Areas, alle reservierungspflichtigen Orte, Kernstrecken |
| **Phase 2** | Hakone, Nara, Hiroshima/Miyajima, Kanazawa, Nikko, Kamakura |
| **Phase 3** | Sapporo/Hokkaido, Nagano/Japanische Alpen, Fukuoka, Okinawa, Takayama, Shirakawa-go |
| **Laufend** | Anime-Locations, saisonale Events (Matsuri, Illuminationen, Sakura-Prognose), neue Läden |

Drei Städte in exzellenter Qualität schlagen 20 Städte in mittelmässiger — der
Generator kann mit `max_destinations = 3` in Phase 1 vollwertige 7–14-Tage-Trips
bauen.

## Saisonale Daten

Eigener Datentyp `events`: Matsuri, Feuerwerke, Illuminationen, Sumo-Turniere,
Sakura-/Momiji-Prognosen, Feiertage (Golden Week, Obon, Neujahr — mit expliziter
Warnung wegen Preisen und Andrang). Der Generator prüft für jedes Reisedatum, ob
Events in der Nähe stattfinden, und schlägt sie aktiv vor — das ist einer der
Momente, in denen sich das Produkt „intelligent“ anfühlt.
