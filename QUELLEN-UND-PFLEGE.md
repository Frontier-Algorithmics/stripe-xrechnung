# Quellen und Pflege

Stand der Recherche: **6. September 2026**. Es wurden öffentliche Primärquellen verwendet. Lokale Validierung verwendete die unten festgehaltenen Artefakte; ein echtes Stripe-Konto wurde nicht aufgerufen.

| Quelle | Wofür sie verwendet wurde |
|---|---|
| [Stripe Invoice object](https://docs.stripe.com/api/invoices/object) | Rechnungsfelder, Rechnungs-Snapshots, Datum und Beträge |
| [Stripe Invoice Line Item](https://docs.stripe.com/api/invoice-line-item/object) | Positionsdaten, Menge und Leistungszeitraum |
| [Stripe Pagination](https://docs.stripe.com/api/pagination) | Vollständiges Laden von Listen |
| [Stripe Webhooks](https://docs.stripe.com/webhooks) | Signatur, Wiederholung, Reihenfolge und asynchrone Verarbeitung |
| [Stripe Rechnungsübergänge](https://docs.stripe.com/invoicing/integration/workflow-transitions) | Lebenszyklus und Finalisierung |
| [Stripe API-Versionierung](https://docs.stripe.com/api/versioning) | Eigene Version festhalten und Vertrag testen |
| [Stripe Währungen](https://docs.stripe.com/currencies) | Kleinste Währungseinheiten und Sonderfälle |
| [Stripe E-Invoicing](https://support.stripe.com/questions/what-is-e-invoicing-and-how-to-do-it?locale=de-DE) | Einordnung vorhandener spezialisierter Erweiterungen |
| [KoSIT/XStandards Versionen und Bundles](https://xeinkauf.de/xrechnung/versionen-und-bundles/) | Standard XRechnung 3.0.2 |
| [KoSIT/XStandards FAQ](https://xeinkauf.de/xrechnung/faq) | Profil- und Versionshinweise |
| [Offizielles UBL-Syntaxbinding](https://xeinkauf.de/app/uploads/2024/06/XRechnung302-Syntaxbinding-UBL2.1-2024-06-20.pdf) | Business Terms und UBL-Struktur |
| [KoSIT-Konfiguration v2026-08-31](https://github.com/itplr-kosit/validator-configuration-xrechnung/releases/tag/v2026-08-31) | Tatsächlich eingesetzte aktuelle technische Prüfregeln |
| [KoSIT Validator v1.6.3](https://github.com/itplr-kosit/validator/releases/tag/v1.6.3) | Tatsächlich ausgeführter Validator |
| [KoSIT-Testbeispiel](https://github.com/itplr-kosit/xrechnung-testsuite/blob/master/src/test/business-cases/standard/01.01a-INVOICE_ubl.xml) | Gegenprüfung der Struktur sowie ausdrücklich nicht existierende Test-IBAN |

Das Normenübersichtsportal führte beim Abruf noch das Winter-Bugfix-Bundle vom Januar 2026. Die offizielle technische Release-Seite enthielt bereits v2026-08-31, veröffentlicht am 2. September. Deshalb ist im Prüfprotokoll die konkret heruntergeladene technische Konfiguration dokumentiert, statt beide Versionsdaten gleichzusetzen.

Das Mapping und die SaaS-Beispiele sind für diese Ressource verfasst. Der Validator und seine Regeln werden nicht mitgeliefert; laden Sie deren Originalartefakte über die Primärquellen und beachten Sie die dortigen Lizenzen. Standardisierte XML-Namen und Profilkennungen sind unverändert zu verwenden.

## Vor einer Aktualisierung oder erneuten Veröffentlichung

- Stripe-Feldänderungen mit der **tatsächlich verwendeten** API-/Webhook-Version vergleichen. Die hier gelesene Live-Dokumentation ist keine Versionsgarantie für Ihr Konto.
- Neue KoSIT-Konfiguration separat testen. Vorherige Ergebnisse samt Datei-Hash aufbewahren; bestehende Nachweise nicht mit neuen Ergebnissen überschreiben.
- Das gültige Beispiel sowie beide absichtlich ungültigen XML-Dateien erneut ausführen. Bei geänderten Regeln die erwarteten Ergebnisse überprüfen.
- Falls der Beispielfall erweitert wird, neue Fälle ausdrücklich benennen: etwa Rabatte, Reverse Charge, Proration oder Teilzahlung. Den bestehenden Nachweis nicht auf diese Erweiterungen ausdehnen.
- Paketversion, Prüfdatum, Quellen und Manifest gemeinsam aktualisieren. Die Landingpage darf weiterhin nur die tatsächlich enthaltenen Dateien und tatsächlich ausgeführten Prüfungen versprechen.

Es ist kein automatischer Aktualisierungsdienst eingerichtet. Dieses Paket dokumentiert einen überprüfbaren Stand.
