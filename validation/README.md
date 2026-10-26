# Was genau geprüft wurde

**Prüfdatum:** 6. September 2026

| Datei | XML-Schema | Schematron | Gesamtergebnis |
|---|---|---|---|
| `01-saas-standard-ubl.xml` | bestanden | bestanden | ACCEPTABLE; keine gemeldeten Fehler/Warnungen |
| `02-missing-buyer-endpoint.xml` | bestanden | nicht bestanden | REJECT; `PEPPOL-EN16931-R010` |
| `03-wrong-tax-inclusive-total.xml` | bestanden | nicht bestanden | REJECT; `BR-CO-15`, `BR-CO-16` |

Verwendet wurden **KoSIT Validator 1.6.3**, die offizielle **XRechnung-Konfiguration v2026-08-31** und das Szenario **EN16931 XRechnung (UBL Invoice)**. Die Konfigurationsdatei selbst nennt als Metadaten-Datum den 2. September 2026. Standardversion: XRechnung 3.0.2; Syntax: UBL 2.1.

Die zugehörigen HTML- und XML-Prüfberichte liegen in diesem Verzeichnis. Ihre maschinenspezifischen Eingabepfade wurden für die Weitergabe durch Paketpfade ersetzt. Prüfergebnisse, Meldungen und geprüfte Rechnungsdateien wurden nicht verändert. [manifest.json](manifest.json) enthält SHA-256-Prüfsummen der Rechnungsdateien, des Validators und des Konfigurationsarchivs sowie die Ergebnisse.

## Prüfung wiederholen

Sie benötigen ein kompatibles Java. In dieser Prüfung wurde das bereits installierte Java 23 verwendet. Prüfen Sie für Ihre Umgebung die Systemanforderungen des Validators.

1. Laden Sie den [Validator 1.6.3 als Standalone-JAR](https://github.com/itplr-kosit/validator/releases/download/v1.6.3/validator-1.6.3-standalone.jar) herunter.
2. Laden und entpacken Sie die [Konfiguration v2026-08-31](https://github.com/itplr-kosit/validator-configuration-xrechnung/releases/download/v2026-08-31/xrechnung-3.0.2-validator-configuration-2026-08-31.zip) vollständig in einen Ordner `config`.
3. Legen Sie JAR, Ordner `config` und dieses Paket nebeneinander. Führen Sie aus dem übergeordneten Ordner aus:

```sh
java -jar validator-1.6.3-standalone.jar \
  -s config/scenarios.xml \
  -r config \
  -h \
  -o stripe-xrechnung-integrationspaket/validation \
  stripe-xrechnung-integrationspaket/examples/01-saas-standard-ubl.xml
```

Die absichtlich fehlerhaften XML-Dateien können Sie danach auf dieselbe Weise prüfen. Bei diesen ist ein Ablehnungsergebnis erwünscht. Die offizielle [KoSIT-Nutzungsanleitung](https://github.com/itplr-kosit/validator-configuration-xrechnung/blob/master/docs/usage.md) erklärt den grundlegenden Ablauf; dieses Paket hält die tatsächlich eingesetzten Versionen ausdrücklich fest.

## Grenze dieses Nachweises

XML-/Schematron-Validierung ersetzt keine Prüfung realer Adressen, Steuerkennungen, Bankverbindungen oder steuerlicher Sachverhalte. Sie beweist auch keinen erfolgreichen Versand. Die absichtlich ungültigen Beispiele zeigen einzelne Fehler; sie decken nicht sämtliche Regeln ab.

Die JSON-Doppelereignisfolge ist eine Testvorgabe für Ihre Anwendung. Sie wurde weder an Stripe geschickt noch gegen eine Queue-/Datenbankimplementierung ausgeführt. Die gesamte Ressource verwendet ausschließlich synthetische Daten.
