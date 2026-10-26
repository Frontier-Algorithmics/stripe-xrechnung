# Vom Stripe-Rechnungsdatensatz zur XRechnung

Stand: 6. September 2026. Ziel: Standard-XRechnung 3.0.2 in UBL 2.1; keine XRechnung-Extension, kein ZUGFeRD-PDF und kein Peppol-Transport.

## 1. Legen Sie die Rechnungsquelle fest

Für diesen Leitfaden bleibt die finale Stripe-Rechnung die Quelle für Nummer, Positionen und Beträge. Die strukturierte Ausgabe ist eine weitere Darstellung derselben Rechnung. Legen Sie vor dem Bau fest, welches System die verbindliche Nummer und Änderungen verantwortet. Erzeugen Sie aus einem Zahlungsereignis nicht unbemerkt eine zweite, unabhängig nummerierte Rechnung.

Eine andere Architektur – Kontier stellt die Rechnung, Stripe wickelt nur Zahlungen ab – braucht einen eigenen Datenvertrag. Dieses Beispiel beweist diese Architektur nicht.

Stripe unterscheidet Entwurf, Finalisierung und weitere Rechnungszustände. Verwenden Sie einen bewusst gewählten Finalisierungspunkt, nicht den veränderbaren Entwurf. Korrekturen, Stornierung und Gutschriften gehören in eigene Abläufe; ein bereits ausgeliefertes Dokument wird nicht still überschrieben. [Stripe: Rechnungsübergänge](https://docs.stripe.com/invoicing/integration/workflow-transitions)

## 2. Prüfen Sie zuerst diese sechs Lücken

- **Verkäufer:** rechtlicher Name, Anschrift, passende Steuerkennung und vollständiger Ansprechpartner.
- **Käufer:** Rechnungsname, Anschrift und die vom Empfänger bestätigte elektronische Adresse einschließlich Kennungsschema.
- **Käuferreferenz:** vom Käufer vorgegebene Referenz; bei entsprechenden öffentlichen Empfängern kann dies die Leitweg-ID sein. Eine Stripe-Kunden-ID ist kein automatischer Ersatz.
- **Geschäftsprozess:** mit dem Empfänger vereinbartes Profil. Der Beispielwert darf nicht ungeprüft in jeden Kanal übernommen werden.
- **Zahlweg:** tatsächlich vereinbarte Zahlungsart und dazugehörige Angaben. `send_invoice` und `charge_automatically` sind Stripe-Ablaufwerte, keine UBL-Zahlungsmittelcodes.
- **Steuerfall:** Kategorie, Satz, Bemessungsgrundlage und gegebenenfalls Begründungen. Ein numerischer Satz von null unterscheidet beispielsweise noch nicht zwischen verschiedenen steuerlichen Gründen.

Im XRechnung-3.0-Profil sind Geschäftsprozess sowie elektronische Adressen beider Parteien zu berücksichtigen. `EM` bezeichnet im Beispiel eine E-Mail-Adresse; Kontakt-E-Mail und Rechnungs-Endpunkt sind unterschiedliche Felder, auch wenn der Wert hier gleich ist. [KoSIT/XStandards: FAQ und Pflichtangaben](https://xeinkauf.de/xrechnung/faq), [offizielles UBL-Syntaxbinding](https://xeinkauf.de/app/uploads/2024/06/XRechnung302-Syntaxbinding-UBL2.1-2024-06-20.pdf)

Nutzen Sie [field-map.csv](field-map.csv) als Arbeitsliste: Die Spalte **Herkunft** trennt Stripe-Daten, Stammdaten und eigene Integrationsentscheidungen. Sie ist ein Ausschnitt für den Beispielfall, keine vollständige Spezifikation aller zulässigen Rechnungen.

## 3. Frieren Sie den Datensatz ein

Speichern Sie finale Rechnungsdaten und die zugehörigen Ergänzungen gemeinsam. Bei finalisierten Stripe-Rechnungen können Rechnungsfelder einen anderen Stand als das heute bearbeitete Kundenprofil haben. Verwenden Sie den zum Dokument gehörenden Stand.

Für das Rechnungsdatum prüfen Sie `effective_at` beziehungsweise den Finalisierungszeitpunkt nach Ihrer Rechnungspolitik; `created` ist nicht automatisch das Ausstellungsdatum. `number` und nicht `id` ist der naheliegende Ausgangspunkt für die sichtbare Rechnungsnummer. [Stripe: Invoice object](https://docs.stripe.com/api/invoices/object)

Die Menge einer Position kann Dezimalstellen besitzen. Erhalten Sie diese Präzision; verwenden Sie in Ihrer API-Version das dafür vorgesehene Feld. Leistungszeiträume gehören zu den Positionen und müssen in eindeutige Kalenderdaten mit festgelegter Zeitzone überführt werden. Im Beispiel sind September-Anfang und -Ende bereits explizit als inklusive Daten hinterlegt. [Stripe: Invoice Line Item](https://docs.stripe.com/api/invoice-line-item/object)

Prüfen Sie außerdem, ob alle Positionen geladen wurden. Eine eingebettete Liste mit `has_more: true` ist keine vollständige Rechnung. Für paginierte Ressourcen müssen die Folgeseiten abgeholt werden. [Stripe: Pagination](https://docs.stripe.com/api/pagination)

## 4. Rechnen Sie vor dem Serialisieren nach

Für **diesen** einfachen EUR-Fall gilt:

| Prüfung | Erwarteter Wert |
|---|---:|
| Menge × Nettopreis | 2 × 50,00 = 100,00 EUR |
| Summe der Nettopositionen | 100,00 EUR |
| Bemessungsgrundlage Kategorie S / 19 % | 100,00 EUR |
| Steuerbetrag | 19,00 EUR |
| Netto + Steuer | 119,00 EUR |
| Vorauszahlung / Rundung | 0,00 / 0,00 EUR |
| Zahlbetrag | 119,00 EUR |

Die Testdaten verwenden EUR-Cent als Eingangsbeträge und Dezimalbeträge mit Punkt in XML. Verwenden Sie Integer oder Dezimalarithmetik, keine unkontrollierte binäre Gleitkomma-Rundung. Nicht jede Stripe-Währung besitzt zwei Nachkommastellen. [Stripe: Währungen und Betragsdarstellung](https://docs.stripe.com/currencies)

Für Rabatte, Proration, inklusive Steuern, Kunden-Guthaben, Teilzahlungen oder mehrere Steuersätze reicht diese Rechnung nicht. Definieren Sie dafür zusätzliche Testfälle. Der Stripe-Zahlbetrag kann durch Kundenguthaben beeinflusst sein: Bilden Sie Bruttobetrag, gezahlten Betrag und offenen Betrag fachlich getrennt ab. Kopieren Sie keinen verfügbaren Gesamtbetrag pauschal in sämtliche UBL-Summen.

## 5. Verarbeiten Sie Ereignisse wiederholbar

Empfohlene Architektur, kein lauffertiger Server:

1. Stripe-Signatur am unveränderten Request-Body mit der offiziellen Bibliothek prüfen.
2. Ereignis dauerhaft entgegennehmen, danach zügig bestätigen; Arbeit in eine Queue legen.
3. Wiederholungen über Ereignis-ID erkennen. Zusätzlich Rechnungs-Ausgabe pro Konto, Dokumentidentität und Version eindeutig halten.
4. Autoritativen Rechnungsstand beziehen; Reihenfolge von Ereignissen nicht voraussetzen.
5. Ergänzungen und eigene Vorprüfungen ausführen; bei fehlenden Angaben in einen bearbeitbaren Fehlerzustand wechseln.
6. XML erzeugen, validieren und erst nach fachlicher Freigabe einen eindeutigen Versandauftrag anlegen.

Stripe weist auf wiederholte und nicht zwingend geordnete Zustellung hin. Eine `invoice.finalized`-Nachricht ist daher ein Arbeitsauslöser, kein Beweis, dass die Arbeit noch nicht stattgefunden hat. Bei einem Absturz zwischen Dokumenterzeugung und Versand verhindert ein dauerhaft gespeicherter Versandauftrag mit stabiler Identität unnötige Wiederholungen. [Stripe: Webhook-Verhalten](https://docs.stripe.com/webhooks)

Die dritte Fehlerdatei beschreibt die erwartete Reaktion auf drei Testereignisse. Ob Ihre Datenbank-, Queue- und Versandlösung diese Eigenschaft erfüllt, müssen Sie separat unter Konkurrenz und Wiederholungen testen.

## 6. Validieren und liefern Sie bewusst getrennt

Das Beispiel nutzt `CustomizationID` mit dem Suffix `xrechnung_3.0` – nicht eine selbst erfundene Kennung `xrechnung_3.0.2`. Die technische Regelkonfiguration besitzt zusätzlich eine eigene Release-Version. Zum Paket gehören die tatsächlich erzeugten Prüfberichte und SHA-256-Werte. [KoSIT: geprüfte Konfiguration v2026-08-31](https://github.com/itplr-kosit/validator-configuration-xrechnung/releases/tag/v2026-08-31)

Für den Versand klären Sie Empfänger, Kanal und erforderliche Referenzen separat. Eine erfolgreich validierte Datei ist noch nicht übermittelt oder vom Geschäftspartner angenommen. Erfassen Sie diese Zustände einzeln: **erzeugt → validiert → zum Versand bereit → übermittelt → Rückmeldung verarbeitet**. „Versendet“ ist kein Ersatz für einen Annahmenachweis.

## Vor dem ersten echten Dokument

- [ ] Eigene Stripe-API- und Webhook-Version dokumentiert; vorhandene Payloads dagegen getestet.
- [ ] Verkäufer- und Empfängerdaten einschließlich Referenz und Endpunktschema bestätigt.
- [ ] Eindeutige Rechnungsquelle und Korrekturabläufe festgelegt.
- [ ] Vollständige Positionen, Präzision, Steuern und Summen überprüft.
- [ ] Doppelereignisse, Wiederholung nach Absturz und verspätete Ereignisse getestet.
- [ ] Eigene Testrechnungen mit der vereinbarten Regelversion geprüft; Berichte archiviert.
- [ ] Empfängerkanal sowie Fehler- und Rückmeldebehandlung getestet.

Die Live-Dokumentation wurde am 6. September 2026 geprüft. Das Paket setzt keine konkrete Stripe-Kontoversion voraus und enthält keine mit einem echten Stripe-Konto ausgeführten API-Aufrufe. Halten Sie Ihre verwendete Version fest, statt die jeweils neueste Dokumentation ungeprüft als Datenvertrag zu behandeln. [Stripe: API-Versionierung](https://docs.stripe.com/api/versioning)
