# Entscheidungen und Abnahme für Ihre Integration

Arbeitsvorlage, Stand 6. September 2026. Die zusätzlichen Szenarien unten sind noch nicht ausgeführte Testvorgaben für Ihre Anwendung.

Projekt: ____________________
Verantwortliche Person Engineering: ____________________
Verantwortliche Person Finance: ____________________
Geplanter erster Empfänger und Kanal: ____________________
Stripe-API- und Webhook-Version: ____________________
Validator- und Konfigurationsversion: ____________________

## 1. Vor dem Implementieren entscheiden

| Entscheidung | Ihre Festlegung | Verantwortung | Nachweis / offene Frage |
|---|---|---|---|
| Welches System vergibt die verbindliche Rechnungsnummer? | | | |
| Wann wird der Rechnungsstand eingefroren? | | | |
| Woher kommen Käuferreferenz, Endpunkt und Schema? | | | |
| Wie werden Steuerkategorien und Zahlungsart bestimmt? | | | |
| Wie unterscheiden wir gleiche Ereignisse und legitime Revisionen? | | | |
| Wer übernimmt Versand, Fehlerbehebung und Rückmeldungen? | | | |
| Wie bleiben Dokument, Hash, Mapping-Version und Prüfbericht zusammen? | | | |
| Wie werden Korrekturen, Gutschriften und bereits versandte Dokumente behandelt? | | | |

Empfehlung: Verwenden Sie echte fachliche Anforderungen und synthetische Beispieldaten. Eine technische Stripe-ID ersetzt nicht automatisch eine Empfängerreferenz oder elektronische Rechnungsadresse.

## 2. Ausführbarer Abnahmeplan für Ihr Team

Tragen Sie nach jedem Lauf das tatsächliche Ergebnis und einen Link zum Nachweis ein. Eine leere Ergebniszelle bedeutet nicht bestanden.

| Szenario | Erwartung | Tatsächliches Ergebnis | Nachweis | Freigabe |
|---|---|---|---|---|
| Standardbeispiel des Pakets | XML und Regeln akzeptieren den Beleg | Im Paket geprüft; validation/README.md | Mitgelieferter Bericht | Nur für diese Datei |
| Fehlender Käufer-Endpunkt | Ablehnung; keine Freigabe zum Versand | Im Paket geprüft: PEPPOL-EN16931-R010 | Mitgelieferter Bericht | Nur für diese Datei |
| Bruttosumme 118,99 statt 119,00 EUR | Ablehnung der widersprüchlichen Summen | Im Paket geprüft: BR-CO-15 und BR-CO-16 | Mitgelieferter Bericht | Nur für diese Datei |
| Gleiches Ereignis zweimal | Ein Dokument und ein Versandauftrag für dieselbe Version | | | |
| Zwei Ereignis-IDs für dieselbe Rechnung | Dokumentidentität verhindert ungewollte doppelte Ausgabe | | | |
| Zwei parallele Worker | Dauerhafte Eindeutigkeit, auch unter Konkurrenz | | | |
| Neustart nach Speicherung, vor Queue-Bestätigung | Wiederholung übernimmt vorhandenen Zustand | | | |
| Ereignisse in anderer Reihenfolge | Gewählter Dokumentstand bleibt maßgeblich | | | |
| Fehlende Stammdaten werden ergänzt | Kontrollierter erneuter Lauf nach sichtbarer Korrektur | | | |
| Mehrere Positionsseiten | Alle Positionen erscheinen vollständig in den Summen | | | |
| Rabatt, Proration oder mehrere Steuersätze | Positionen, Steuergruppen und Summen bleiben konsistent | | | |
| Guthaben, Voraus- oder Teilzahlung | Bruttobetrag, Zahlung und Restforderung getrennt | | | |
| Legitime Korrektur nach Versand | Alte und neue Revision bleiben nachvollziehbar | | | |
| Temporärer Versandfehler | Sicherer Wiederanlauf desselben Versandauftrags | | | |
| Empfänger lehnt fachlich ab | Sichtbarer Fehlerzustand, Zuständigkeit und Klärungsweg | | | |

Die drei Ereignisse in failures/04-duplicate-event-sequence.json sind vereinfachte Testbeschreibungen, keine signierten Stripe-Payloads. Sie wurden nicht gegen einen echten Worker ausgeführt. XML-Validierung prüft diese Betriebsfälle nicht.

## 3. Übergabe in den Betrieb

- [ ] Offene Pflichtangaben sind geschlossen; Empfänger und Kanal sind abgestimmt.
- [ ] Eigene Standard- und Sonderfälle wurden tatsächlich ausgeführt.
- [ ] Wiederholungen, Abstürze, Parallelität und legitime Revisionen sind getestet.
- [ ] Dokument, Mapping-Version, Validator-Ergebnis und Versandstatus bleiben zuordenbar.
- [ ] Ein Fehler hat einen verständlichen Status und eine verantwortliche Person.
- [ ] Ein tatsächlicher Testempfang wurde bestätigt; ein Validator-Erfolg ersetzt diesen nicht.

Freigabe Engineering / Datum: ____________________
Freigabe Finance / Datum: ____________________
Verbleibende Einschränkungen: ____________________

## 4. Kontier konkret prüfen

Für eine Produktprüfung bereithalten: anonymisierte Beispielrechnung, gewünschter Empfängerkanal, relevanter Steuer-/Zahlungsfall und die offenen Entscheidungen oben.

- [Pläne und aktuelle Testbedingungen](https://kontier.eu/pricing/?intent=api&utm_source=resource&utm_medium=worksheet&utm_campaign=stripe_xrechnung_pack)
- [Kontier im Billing-Stack](https://kontier.eu/billing-deutschland?intent=api&utm_source=resource&utm_medium=worksheet&utm_campaign=stripe_xrechnung_pack)

Die Struktur dieses Pakets lässt Stripe die finale Rechnung verantworten. Eine Architektur mit Kontier als Rechnungsquelle benötigt einen gesondert geprüften Datenvertrag. Das Paket ist kein Nachweis für einen fertigen Stripe-Kontier-Connector.
