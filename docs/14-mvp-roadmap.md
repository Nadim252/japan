# 14 — MVP-Scope, Roadmap & Tech-Stack

## Der ehrliche MVP-Schnitt

Die vollständige Spezifikation ist ein Zwei-Jahres-Produkt. Der MVP muss die
**eine These** beweisen:

> Ein AI-generierter Japan-Trip, den man anschliessend komplett umbauen kann,
> ist deutlich besser als alles, was Nutzer heute mit ChatGPT + 40 Browser-Tabs
> machen.

Alles, was diese These nicht beweist, wird verschoben.

### v0.1 — Interner Prototyp (4–6 Wochen)

| Drin | Draussen |
|---|---|
| Onboarding Q1–Q10 + Q16 (Freitext) | Flüge, Hotelpreise live, Affiliate |
| Generator: Route + Areas + POIs, 3 Städte (Tokyo/Kyoto/Osaka) | Intercity-Buchung, JR-Pass-Rechner |
| Trip-Dashboard mit Destination-Blöcken | Karte, Schedule-Modus |
| `Keep / Remove / Replace / Explore more` | Kollaboration, Export |
| Explore mit Filtern | Konten (nur Draft-Cookie) |
| Budget-Schätzung ohne Live-Preise | Budget-Optimierer |

Ziel: 20 Testnutzer, die einen Trip generieren und mindestens 5 Änderungen machen.
Gemessen wird die Edit-Rate und die Frage „Würdest du damit wirklich reisen?“.

### v1.0 — Öffentlicher Launch (3–4 Monate)

- Onboarding vollständig (Q1–Q16)
- Generator vollständig inkl. Validierungs-Regelwerk und Regenerierungs-Modi
- **Hotels mit Live-Preisen + Affiliate** (Booking/Agoda) — der Haupterlöskanal
- **Aktivitäten-Affiliate** (Klook/GetYourGuide) für reservierungspflichtige Orte
- eSIM-Affiliate
- Intercity-Transport + JR-Pass-Rechner
- Budget-Engine live inkl. Über-Budget-Flow und Optimierer
- Reservierungs-Checkliste + E-Mail-Erinnerungen
- Konten, Teilen (read-only), PDF-Export
- Karte im Dashboard und in Explore
- Städte: Tokyo, Kyoto, Osaka, Hakone, Nara, Hiroshima, Kanazawa, Nikko
- SEO-Seiten: `/city/*`, `/poi/*`, `/explore/*`
- DE + EN

### v1.5 — Verdichtung (+2 Monate)

- Flug-Integration
- „Improve my trip with AI“ mit vollem Diff-System
- Schedule-Modus mit Öffnungszeiten-Validierung
- ICS-/Google-Maps-Export
- Saisonale Events im Generator
- Städte Phase 3

### v2.0 — Gruppen & Reise

- Kollaboration (Editor-Rollen, Kommentare, Abstimmung)
- On-Trip-Modus (Tagesansicht, offline, Ausgaben-Tracking)
- Pro-Abo
- Preis-Alerts für Hotels

### v3 — Ausbau

- Weitere Sprachen (FR, IT, ES)
- Community-Beiträge mit Moderation
- Zweites Land (Korea oder Taiwan) — **erst**, wenn Japan profitabel ist

## Tech-Stack-Empfehlung

| Ebene | Wahl | Begründung |
|---|---|---|
| Frontend | **Next.js (App Router) + TypeScript** | SSG für SEO-Seiten, SSR/Streaming für den Generator, eine Codebasis |
| State | **TanStack Query + Zustand** | Server-State getrennt vom UI-State; optimistic Updates für Item-Mutationen |
| UI | Tailwind + Radix Primitives | Schnell, zugänglich, konsistente Aktionsleisten |
| Karte | MapLibre + eigene Vektor-Tiles oder Mapbox | Kostenkontrolle bei vielen Karten-Views |
| Backend | **Node/TypeScript (Fastify oder NestJS)** oder Python (FastAPI) | TS spart Typen-Duplikate mit dem Frontend; Python bei starkem Daten-/ML-Fokus |
| DB | **PostgreSQL + PostGIS** | Geo-Abfragen sind Kernfunktionalität |
| Suche | Typesense oder Meilisearch | Facetten + Tippfehlertoleranz + JP-Unterstützung |
| Cache/Queue | Redis + BullMQ | Preis-Caches, Generator-Jobs, Rate-Limits |
| LLM | Claude (Auswahl + Texte), strukturierte JSON-Ausgabe | Siehe `04-generator.md` — nur Auswahl, keine Fakten |
| Hosting | Vercel (Frontend) + Fly.io/Railway/AWS (Backend, DB) | |
| Analytics | PostHog (self-hosted) oder Plausible | Datenschutz |
| Fehler | Sentry | |

## Risiken und Gegenmassnahmen

| Risiko | Gegenmassnahme |
|---|---|
| **Datenqualität** — falsche Öffnungszeiten zerstören Vertrauen | Tier-System, `hours_checked_at`, Nutzer-Meldungen, Stand-Datum sichtbar |
| **LLM-Halluzination** | LLM wählt nur aus IDs; Validierungs-Regelwerk; nichts Erfundenes gelangt in die UI |
| **Generator-Kosten** | Deterministische Schritte ohne LLM, kompakte Kandidatenlisten, `brief_hash`-Cache |
| **Affiliate-Abhängigkeit** | Mehrere Partner pro Kategorie, Pro-Abo als zweites Standbein |
| **SEO braucht 6–12 Monate** | Früh mit `/poi/*` und `/city/*` starten, parallel Reddit/YouTube/Communities (r/JapanTravel, Anime-Communities) |
| **Saisonale Nachfrage** (Sakura, Herbst) | Content und Kampagnen 4–6 Monate vor der Saison, wenn geplant wird |
| **Scope Creep** | Diese Roadmap ist der Vertrag; „Build my own trip“ und der Generator dürfen nie auseinanderlaufen (ein Datenmodell) |

## Was zuerst gebaut werden sollte (Reihenfolge für die Umsetzung)

1. **Datenmodell + DB-Schema** (`09-datenmodell.md`) — alles andere hängt daran.
2. **Referenzdaten für Tokyo** (150 POIs, 8 Areas) — ohne Daten ist der Generator nicht testbar.
3. **Trip-Dashboard mit statischem Beispiel-Trip** — die vier Aktionen zuerst, weil sie die Interaktionsgrammatik definieren.
4. **Explore + Filter** — nutzt dieselben Daten, macht Editieren erst sinnvoll.
5. **Generator-Pipeline** — Schritt 1–5 zuerst (Route, Areas, POIs), Rest folgt.
6. **Onboarding** — kann bis hierhin ein simples Formular sein.
7. **Budget-Engine.**
8. **Hotels + Affiliate.**
9. **Konten, Teilen, Export.**

Diese Reihenfolge stellt sicher, dass der modulare Builder **vor** dem Generator
existiert. So kann der Generator gar nichts produzieren, was der Nutzer nicht
auch selbst bauen oder ändern könnte — genau das ist der Produktgrundsatz.
