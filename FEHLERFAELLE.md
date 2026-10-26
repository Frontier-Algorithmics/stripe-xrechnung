# Drei Fehlerfälle zum Nachbauen

Die beiden XML-Dateien sind absichtlich beschädigte Varianten des gültigen Beispiels. Sie dürfen nicht ausgeliefert werden. Beide wurden am 6. September 2026 mit derselben KoSIT-Konfiguration wie das Ausgangsbeispiel geprüft.

## 1. Elektronische Käuferadresse fehlt

**Datei:** [02-missing-buyer-endpoint.xml](failures/02-missing-buyer-endpoint.xml)

**Änderung:** `AccountingCustomerParty/Party/EndpointID` entfernt. Die Kontaktadresse im übrigen Datensatz ersetzt dieses Element nicht.

**Tatsächliches Ergebnis:** XML-Schema bestanden, Schematron abgelehnt, Regel `PEPPOL-EN16931-R010`.

**Reaktion der Integration:** Ausgabe/Versand blockieren und fehlenden Käufer-Endpunkt mit Kennungsschema benennen. Den Käufer oder die gepflegten Rechnungsstammdaten als Quelle verwenden; keinen beliebigen Login oder eine Stripe-ID einsetzen.

**Nachweis:** [KoSIT-Bericht](validation/02-missing-buyer-endpoint-report.html)

## 2. Bruttosumme um einen Cent verändert

**Datei:** [03-wrong-tax-inclusive-total.xml](failures/03-wrong-tax-inclusive-total.xml)

**Änderung:** `TaxInclusiveAmount` von 119,00 auf 118,99 EUR geändert; netto 100,00 EUR, Steuer 19,00 EUR und Zahlbetrag 119,00 EUR bleiben unverändert.

**Tatsächliches Ergebnis:** XML-Schema bestanden, Schematron abgelehnt, Regeln `BR-CO-15` und `BR-CO-16`.

**Reaktion der Integration:** Keine automatische Korrektur nur eines Summenfelds. Berechnungsweg, Rabatte, Steuer-Aufschlüsselung und Rundung nachvollziehen; anschließend sämtliche abhängigen Summen konsistent erzeugen. Den fehlerhaften Datensatz bis zur Klärung erhalten.

**Nachweis:** [KoSIT-Bericht](validation/03-wrong-tax-inclusive-total-report.html)

## 3. Derselbe Rechnungsauftrag kommt mehrfach an

**Datei:** [04-duplicate-event-sequence.json](failures/04-duplicate-event-sequence.json)

**Eingang:** Dasselbe `invoice.finalized`-Testereignis zweimal, anschließend eine andere Testereignis-ID für dieselbe Rechnung im selben Kontokontext. Die Objekte sind vereinfachte Testbeschreibungen, keine signierten Stripe-Payloads.

**Erwartung an Ihre Implementierung:** Ein gespeichertes Rechnungsdokument, ein Versandauftrag. Eine neue legitime Dokumentrevision darf dadurch nicht unterdrückt werden; legen Sie deshalb die Versionsidentität ausdrücklich fest.

**Reaktion:** Ereignis-ID deduplizieren und Dokument-/Versandidentität zusätzlich dauerhaft absichern. Erproben Sie das auch mit parallelen Workern und einem Abbruch zwischen Erzeugung und Queue-Bestätigung.

**Prüfstatus:** Testvertrag bereitgestellt; kein produktiver Worker, keine echte Stripe-Zustellung und kein Versand getestet. Ein XML-Validator kann diesen Fehler nicht erkennen. [Stripe: doppelte Ereignisse](https://docs.stripe.com/webhooks#handle-duplicate-events)
