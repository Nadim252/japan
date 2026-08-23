# 08 — Budget-Engine

## Anspruch

Das Budget ist kein Anzeige-Gimmick, sondern ein **aktiver Bestandteil der
Planung**. Es läuft bei jeder Änderung live mit und beeinflusst die Vorschläge
der AI.

## Anzeige im Header

```
Estimated trip cost                          pro Person · 2 Reisende

Flights          CHF   750    ●●●●●●●●●●●●●●●●●
Hotels           CHF   940    ●●●●●●●●●●●●●●●●●●●●●
Transport        CHF   260    ●●●●●●
Activities       CHF   240    ●●●●●
Food (estimate)  CHF   550    ●●●●●●●●●●●●
Extras           CHF     0

Total            CHF 2'740
Budget           CHF 3'000
Remaining        CHF   260                            [Details ansehen →]
```

## Kostenkategorien und Berechnung

| Kategorie | Quelle | Berechnung |
|---|---|---|
| **Flights** | Flug-API bzw. Schätztabelle je Origin/Saison | Gewählte Option × Personen; ohne Auswahl: Median der Suchergebnisse, markiert als Schätzung |
| **Hotels** | Hotel-API (Live-Preis für die Daten) | Preis/Nacht × Nächte × Zimmeranzahl. Zimmeranzahl aus Party (2 Erw. = 1 Zimmer, 3–4 = 2 Zimmer, Kinder je nach Alter) |
| **Intercity transport** | Verbindungstabelle (gepflegt, nicht live) | Summe der Strecken × Personen, JR-Pass-Alternative wird gegengerechnet |
| **Local transport** | Pauschale pro Stadt/Tag | Tokyo ¥900/Tag, Kyoto ¥800, Kleinstadt ¥400; × Tage × Personen |
| **Activities** | POI-Preise | Summe der Eintritte geplanter Items × Personen |
| **Food** | Schätzmodell (siehe unten) | Tage × Tagessatz × Personen |
| **Extras** | eSIM, Versicherung, Gepäcktransfer, Souvenirs (opt-in) | Summe |

### Food-Schätzmodell

Tagessatz pro Person, abhängig von `budget_level` und den Food-Interessen:

| Stufe | Frühstück | Mittag | Abend | Snacks/Kaffee | Tagessatz ≈ |
|---|---|---|---|---|---|
| Sparsam (Konbini/Ramen/Gyudon) | ¥400 | ¥900 | ¥1'400 | ¥400 | **¥3'100** |
| Normal | ¥800 | ¥1'400 | ¥2'800 | ¥800 | **¥5'800** |
| Foodie (Interesse `food` = Hauptgrund) | ¥1'000 | ¥2'200 | ¥6'000 | ¥1'200 | **¥10'400** |

Bereits eingeplante Restaurants mit konkretem Preis ersetzen den jeweiligen
Anteil, damit nicht doppelt gezählt wird. Bei Hotels mit Frühstück entfällt der
Frühstücksanteil. Das Modell ist transparent einsehbar und pro Trip anpassbar
(`Budget → Food-Annahmen ändern`).

### Weitere Regeln

- Alles wird intern in **JPY** gerechnet und erst zur Anzeige in die
  Nutzerwährung konvertiert (FX-Kurs täglich, Kurs + Datum werden angezeigt).
- Preise von Partner-APIs werden mit Zeitstempel gecacht (Hotels max. 6 h, Flüge max. 1 h) und als *Schätzung, Stand …* gekennzeichnet.
- Kinder: Aktivitäten und Transport mit Kinderpreis, sofern in der DB hinterlegt;
  sonst 50 % mit Hinweis.
- Alle Zahlen sind **pro Person**, mit Umschalter auf **Gesamt für die Gruppe**.

## Live-Reaktion auf Änderungen

Jede Änderung erzeugt sofort ein Delta-Feedback:

```
Hotel geändert: Gracery → Granbell
Neue Gesamtsumme: CHF 3'180  (+CHF 440)

⚠ Du liegst CHF 180 über deinem Budget.
   [Günstigere Hotels suchen]  [Aktivitäten anpassen]  [Budget erhöhen]  [Ist ok]
```

`[Ist ok]` ist gleichwertig platziert — das Produkt bevormundet nicht.

### Budget-Optimierer

Wählt der Nutzer „Günstiger machen“, erzeugt das System einen **Diff-Vorschlag**,
niemals eine stille Änderung:

```
So sparst du CHF 210:

− Hotel Kyoto: Granbell → Hotel Vista        −CHF 132   [Übernehmen]
− Shinkansen Nozomi → Hikari (+22 Min)       −CHF  38   [Übernehmen]
− Teuerstes Abendessen (Kaiseki) → Izakaya   −CHF  40   [Übernehmen]

Nicht angerührt: gepinnte Elemente (3)
                                          [Alle übernehmen]  [Abbrechen]
```

Gepinnte (`locked`) Elemente werden nie vom Optimierer angefasst.

## JR-Pass-Rechner

Eigenes, prominentes Modul, weil es für Japan-Reisende die häufigste offene
Frage ist:

```
🚄 Lohnt sich der JR Pass für dich?

Deine Strecken:
  Tokyo → Kyoto (Hikari)        ¥13'320   ✓ im Pass
  Kyoto → Hiroshima             ¥11'400   ✓ im Pass
  Hiroshima → Osaka             ¥10'620   ✓ im Pass
  Osaka → Kansai Airport (HARUKA) ¥2'900  ✓ im Pass
  Nahverkehr JR (geschätzt)      ¥3'200   ✓ teilweise

  Einzeln gesamt:               ¥41'440
  JR Pass 7 Tage:               ¥50'000

  → Der Pass lohnt sich für dich NICHT. Du sparst ¥8'560 mit Einzeltickets.

  Alternative: Kansai-Hiroshima Area Pass (5 Tage, ¥17'000) deckt 2 deiner
  Strecken ab → spart ¥5'020.                     [In den Trip übernehmen]
```

Ehrliche Negativempfehlungen sind hier ein Feature. Die Regionalpässe sind
zusätzlich ein guter Affiliate-Kanal.

## Budget-Detailseite (`/trip/:tripId/budget`)

- Aufschlüsselung pro Kategorie und pro Destination (Tokyo CHF 1'240, Kyoto CHF 890 …).
- Umschalter „pro Person / Gruppe“ und „geplant / bereits gebucht“.
- Was ist Schätzung, was ist ein echter Preis (Symbol + Stand-Datum).
- Editierbare Annahmen: Food-Level, Souvenir-Budget, lokaler Transport.
- Export als CSV.
- Optionaler Reisekassen-Modus (v2): tatsächliche Ausgaben nachtragen, Soll/Ist.

## Budget → Generator-Rückkopplung

Der `budget_level` (abgeleitet aus Budget pro Person und Nacht) steuert direkt:

| Budget/Nacht/Person | Level | Hotelklasse | Transport | Food-Annahme |
|---|---|---|---|---|
| < CHF 90 | `shoestring` | Hostel, Kapsel, Business | Bus, langsame Züge | sparsam |
| CHF 90–160 | `standard` | 3★ Business, gute Lage | Shinkansen 2. Klasse | normal |
| CHF 160–280 | `comfort` | 4★, Boutique, 1× Ryokan | Shinkansen, ggf. Green Car | normal–foodie |
| > CHF 280 | `premium` | 4–5★, Ryokan mit Kaiseki | Green Car, Transfers | foodie |

Das Level fliesst als Penalty/Bonus in `poi_score` und in die Hotelauswahl ein —
so bleiben Plan und Budget von Anfang an konsistent statt erst am Ende korrigiert.
