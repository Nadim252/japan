# 11 — Booking- & Affiliate-System

## Position im Markt

Wir sind **kein Reisebüro und kein OTA**. Wir vermitteln keinen Vertrag, halten
kein Inventar und tragen kein Veranstalterrisiko. Wir sind ein Planungswerkzeug,
das an den passenden Stellen Angebote verlinkt.

Das ist rechtlich sauber und produktseitig sogar besser: Der Plan bleibt
vollständig, egal wo der Nutzer bucht.

## Die drei Zustände jedes buchbaren Elements

| Zustand | Bedeutung | UI |
|---|---|---|
| `suggested` | Vorschlag, noch nichts entschieden | `[View deal ↗] [I'll book this myself] [Change] [Remove]` |
| `self_booked` | Nutzer hat woanders gebucht | Häkchen, optional Referenz/Preis; bleibt im Trip und im Budget |
| `booked` | Über unseren Partnerlink gebucht (soweit erkennbar) | Häkchen + Partnerhinweis |

**Zentrale Regel:** `I'll book this myself` darf nie zum Verlust von Daten
führen. Das Element bleibt im Plan, im Budget, in der Karte und in der
Checkliste. Optional kann der Nutzer Buchungsnummer, echten Preis und Uhrzeit
nachtragen — das verbessert die Budgetgenauigkeit.

## Partnerkategorien

| Kategorie | Partnertyp | Modell |
|---|---|---|
| Hotels | Booking.com, Agoda, Rakuten Travel, Expedia | CPA, 4–7 % |
| Aktivitäten/Tickets | Klook, GetYourGuide, Voyagin, KKday | CPA, 5–10 % |
| Flüge | Kiwi, Skyscanner, Aviasales | CPC/CPA, niedrige Marge |
| Bahn & Pässe | JR-Pass-Reseller, Klook (IC-Karten, Regionalpässe) | CPA |
| eSIM | Airalo, Ubigi, Holafly | CPA, hohe Marge |
| Versicherung | regionale Anbieter | CPA |
| Gepäcktransfer / Airport-Transfer | Klook, lokale Anbieter | CPA |

Priorisierung für die MVP-Integration: **Hotels + Aktivitäten + eSIM** (hohe
Marge, einfache APIs, hohe Trip-Abdeckung).

## Preisdarstellung — Regeln

1. Preise immer mit Quelle und Zeitstempel: *„ab CHF 128/Nacht · Booking.com · Stand 4. März, 09:00“*.
2. Cache-Zeiten: Hotels 6 h, Aktivitäten 24 h, Flüge 1 h. Danach als
   „Preis prüfen“ statt als Zahl anzeigen.
3. Bei Klick auf `View deal` wird der Preis live nachgeladen; weicht er > 10 %
   ab, wird das **vor** der Weiterleitung angezeigt.
4. Nie durchgestrichene Fantasie-Vergleichspreise, nie künstliche Dringlichkeit
   („Nur noch 1 Zimmer!“), auch wenn Partner-APIs das anbieten.
5. Alle Preise in Nutzerwährung mit Hinweis, dass die Abrechnung beim Partner in
   JPY erfolgen kann.

## Klick-Tracking

```
POST /api/clicks { trip_id, entity_type, entity_id, partner }
     → { redirect_url, click_id }
```

- Die Ziel-URL inklusive Affiliate-Parametern wird **serverseitig** erzeugt.
  Affiliate-IDs sind nie im Client-Bundle.
- Wir speichern: `click_id`, `trip_id`, `user_id|anon_id`, `entity`, `partner`,
  `created_at`, `country`, `device`. Kein Weiterreichen personenbezogener Daten
  an Partner über das technisch Notwendige hinaus.
- Öffnen in neuem Tab, damit der Trip erhalten bleibt.
- Rückkehr-Erkennung: Kommt der Nutzer zurück, fragen wir dezent:
  *„Hast du gebucht?“* `[Ja, gebucht] [Noch nicht]` → setzt den Status.

## Offenlegung (Pflicht)

- Neben jedem Affiliate-Element ein kleines, klickbares `ⓘ Partnerlink`.
- Footer-Link `/legal/affiliate-disclosure` mit Klartext:
  *„Wenn du über einen Link bei uns buchst, erhalten wir eine Provision. Für dich
  ändert sich der Preis dadurch nicht. Die Reihenfolge unserer Empfehlungen wird
  davon nicht beeinflusst.“*
- Der letzte Satz ist eine **Produktverpflichtung**: Das Ranking (`best overall`,
  `best budget`, `best location`) darf niemals nach Provisionshöhe sortieren.
  Wenn zwei Angebote gleichwertig sind, darf der Partner mit besserer Provision
  gewinnen — das ist die einzige zulässige Einflussnahme, und sie steht so in
  der Offenlegung.

## Reservierungs-Checkliste (`/trip/:tripId/checklist`)

Der eigentliche Killer für Japan:

```
Muss vorab gebucht werden

⚠ JETZT     Ghibli Museum · Verkauf 10. des Vormonats, 10:00 JST · ausverkauft in Minuten
⚠ 30 Tage   teamLab Planets · 14. April, Zeitfenster wählen          [Buchen ↗] [Erledigt]
⚠ 14 Tage   Sumo-Turnier Ryogoku                                     [Buchen ↗] [Erledigt]
   7 Tage   Shibuya Sky Sonnenuntergang                              [Buchen ↗] [Erledigt]
   7 Tage   Ryokan Hakone (Kaiseki-Dinner)                            ✓ gebucht

Empfohlen
   3 Tage   Pokémon Café Tokyo · Buchung 31 Tage vorher, 18:00 JST
   1 Tag    Restaurant Kaikaya by the Sea

Optional per E-Mail erinnern lassen  ☐
```

- Sortiert nach Dringlichkeit (`lead_time_days` gegen Reisedatum gerechnet).
- Optionale E-Mail-Erinnerungen („In 3 Tagen öffnet der Ghibli-Verkauf“) — das
  ist ein starker Grund, ein Konto zu erstellen, und der beste Reaktivierungs-Trigger.

## Fehlerfälle

| Fall | Verhalten |
|---|---|
| Partner-API down | Gecachte Preise mit Hinweis „Preis evtl. veraltet“; `View deal` funktioniert weiter |
| Hotel ausgebucht | Als nicht verfügbar markieren, 3 Alternativen vorschlagen, Nutzer entscheidet |
| POI dauerhaft geschlossen | Im Trip als Warnung, Ersatzvorschlag, redaktioneller Review-Task |
| Preis stark gestiegen | Budget-Warnung mit Delta und Alternativvorschlag |

## Rechtliches (Checkliste)

- Impressum, AGB, Datenschutzerklärung (DSGVO/revDSG, da Schweizer Kontext).
- Klare Abgrenzung: keine Pauschalreise-Vermittlung im Sinne der EU-Pauschalreise-Richtlinie. Kein Bündeln von Flug + Hotel zu einem Gesamtpreis in einem Buchungsvorgang — wir verlinken nur getrennt.
- Cookie-Consent für Tracking; Affiliate-Cookies erst nach Zustimmung bzw. serverseitige Weiterleitung ohne Drittanbieter-Cookie.
- Preisangaben mit Währung und Hinweis auf mögliche Fremdwährungsgebühren.
- Keine Reiseberatung mit Rechtscharakter (Visa, Impfungen) ohne Quellenangabe und Datum.
