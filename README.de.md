# Stripe → XRechnung: Integrationspaket für SaaS-Teams

**Version 1.0 · geprüft am 6. September 2026 · Sprache: Deutsch**

Ein praktischer Ausgangspunkt für eine Integration: Welche Rechnungsdaten können aus Stripe kommen, welche Angaben müssen Sie ergänzen, und welche Fehler sollte Ihr System vor dem Versand erkennen?

Das Paket enthält ein **synthetisches UBL-Beispiel, das mit KoSIT Validator 1.6.3 und XRechnung-Konfiguration v2026-08-31 erfolgreich geprüft wurde**. Dieser Nachweis gilt für genau diese Beispieldatei. Er bestätigt weder echte Identitäten noch Steuerentscheidungen, Zustellbarkeit oder die Konformität Ihrer späteren Integration.

## In zehn Minuten durcharbeiten

1. Öffnen Sie [den Integrationsleitfaden](INTEGRATIONSLEITFADEN.md).
2. Vergleichen Sie [den Feldplan](field-map.csv) mit Ihren vorhandenen Rechnungs- und Stammdaten.
3. Lesen Sie [Eingangsdaten und Ergänzungen](examples/01-source-and-enrichment.json) neben [der UBL-Ausgabe](examples/01-saas-standard-ubl.xml).
4. Prüfen Sie [die drei Fehlerfälle](FEHLERFAELLE.md) gegen Ihre Architektur.
5. Öffnen Sie [den Prüfbericht des Beispiels](validation/01-saas-standard-ubl-report.html) und [den Versionsnachweis](validation/README.md).

## Der bewusst kleine Beispielfall

Zwei SaaS-Zugänge à 50,00 EUR netto, angenommene 19 % Umsatzsteuer: **100,00 EUR netto + 19,00 EUR Steuer = 119,00 EUR zahlbar**. Kein Rabatt, keine Teilzahlung, keine Gutschrift und keine Währungsumrechnung. Die Steuerbehandlung ist eine vorgegebene Testannahme, keine aus Stripe oder einem Steuerprüfdienst ermittelte Entscheidung.

Die JSON-Datei enthält ausgewählte, synthetische Stripe-Felder sowie einen deutlich getrennten Ergänzungsblock. Sie ist keine vollständige Stripe-API-Antwort. Die XML-Datei ist eine eigenständig erstellte Demonstration; das Paket enthält keinen produktiven Stripe-Konverter und keine erfundenen Kontier-API-Aufrufe.

Alle Namen, Kontaktdaten, Steuerkennungen und Rechnungsdaten sind Testdaten. Die Beispiel-IBAN ist der von KoSIT verwendete nicht existierende Testwert. **Die Dateien dürfen nicht als echte Rechnung versendet oder bezahlt werden.**

## Für welche Entscheidung hilft das?

Wenn nur das strukturierte Ausgabeformat fehlt, vergleichen Sie auch spezialisierte E-Invoicing-Erweiterungen. Stripe beschreibt solche Integrationen selbst. Wenn zusätzlich Kataloge, wiederkehrende Abrechnung oder verbrauchsabhängige Preise zusammengeführt werden müssen, ist eine weitergehende Billing-Integration zu prüfen. Das Paket setzt keine Entscheidung für Kontier voraus. [Stripe zu E-Invoicing](https://support.stripe.com/questions/what-is-e-invoicing-and-how-to-do-it?locale=de-DE)

Technischer Leitfaden, keine Steuer- oder Rechtsberatung. Die tatsächliche Rechnung, Steuerbehandlung und Empfängeranforderungen müssen für Ihren Anwendungsfall geprüft werden. Quellen und Grenzen stehen bei den jeweiligen Schritten.

## Arbeitsvorlage

Nutzen Sie [ENTSCHEIDUNGEN-UND-ABNAHME.md](ENTSCHEIDUNGEN-UND-ABNAHME.md) als Team-Arbeitsvorlage für Verantwortlichkeiten, Architekturentscheidungen und weitere Testfälle. Die zusätzlichen Szenarien sind Testvorgaben; ausgeführte Nachweise bleiben auf die dokumentierten XML-Dateien begrenzt.

Einen gestalteten PDF-Praxisleitfaden zu diesem Paket gibt es auf [kontier.eu/stripe-xrechnung](https://kontier.eu/stripe-xrechnung/).
