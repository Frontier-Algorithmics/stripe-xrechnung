# Stripe → XRechnung: integration starter kit for SaaS teams

Germany requires structured e-invoices (XRechnung / EN 16931) between businesses from 2027 on, depending on revenue. If you bill with Stripe, you have most of the invoice data already. What is missing is a set of fields Stripe does not hold, a mapping onto the UBL syntax, and a pipeline that refuses to send an invoice the official validator would reject.

This repository is a small, honest starting point for that work:

| File | What it is |
| --- | --- |
| [`field-map.csv`](field-map.csv) | Every XRechnung business term (BT-…) a SaaS invoice needs: where the value comes from (Stripe field, your master data, an agreement with the buyer), the UBL path, and the check that catches a wrong value |
| [`examples/01-source-and-enrichment.json`](examples/01-source-and-enrichment.json) | Selected, synthetic Stripe invoice fields plus a clearly separated enrichment block |
| [`examples/01-saas-standard-ubl.xml`](examples/01-saas-standard-ubl.xml) | The resulting UBL 2.1 invoice. **Passes the official KoSIT validator** |
| [`failures/`](failures/) | Two deliberately broken variants (missing buyer endpoint; tax-inclusive total off by one cent) that the validator **rejects**, plus a duplicate-webhook test contract |
| [`validation/`](validation/) | The validator reports, versions and SHA-256 hashes of everything that was checked |
| [`INTEGRATIONSLEITFADEN.md`](INTEGRATIONSLEITFADEN.md), [`FEHLERFAELLE.md`](FEHLERFAELLE.md), [`ENTSCHEIDUNGEN-UND-ABNAHME.md`](ENTSCHEIDUNGEN-UND-ABNAHME.md), [`QUELLEN-UND-PFLEGE.md`](QUELLEN-UND-PFLEGE.md) | Integration guide, failure cases, decision and acceptance template, and sources (German; [README.de.md](README.de.md) is the German overview) |

## Checked, not claimed

The example and both failure files are validated with **KoSIT Validator 1.6.3** and the official **XRechnung configuration v2026-08-31** (XRechnung 3.0.2, scenario "EN16931 XRechnung (UBL Invoice)"):

| File | Result |
| --- | --- |
| `examples/01-saas-standard-ubl.xml` | ACCEPTABLE, no errors or warnings |
| `failures/02-missing-buyer-endpoint.xml` | REJECT, `PEPPOL-EN16931-R010` |
| `failures/03-wrong-tax-inclusive-total.xml` | REJECT, `BR-CO-15`, `BR-CO-16` |

[`validation/manifest.json`](validation/manifest.json) pins the validator and configuration downloads and the invoice files by SHA-256, so a re-run checks exactly what was checked here. Last re-run: 26 October 2026, same results.

Run it yourself with Java 17+:

```sh
curl -sSLO https://github.com/itplr-kosit/validator/releases/download/v1.6.3/validator-1.6.3-standalone.jar
curl -sSLo cfg.zip https://github.com/itplr-kosit/validator-configuration-xrechnung/releases/download/v2026-08-31/xrechnung-3.0.2-validator-configuration-2026-08-31.zip
mkdir config && (cd config && unzip -q ../cfg.zip)
java -jar validator-1.6.3-standalone.jar -s config/scenarios.xml -r config -o out examples/01-saas-standard-ubl.xml
```

## What this is not

- Not a converter. There is no production Stripe-to-XRechnung code here, only a mapping, examples and tests to build one against.
- Not tax or legal advice. The example assumes 19 % German VAT as a test input; your tax treatment, buyer requirements and delivery channel must be checked for your case.
- Not real data. All names, tax IDs and bank details are synthetic test values (the IBAN is the non-existent KoSIT test value). **Never send or pay these files.**
- XML validation does not prove delivery, real identities or a correct tax decision.

## Who made this

[Kontier](https://kontier.eu/) is billing infrastructure for European SaaS: subscriptions, usage-based billing, EU VAT and XRechnung/ZUGFeRD e-invoicing in one API, built by Frontier Algorithmics in Regensburg. This kit does not assume you use Kontier; if Stripe Billing plus an e-invoicing extension covers your case, that is a fine answer too. A designed PDF version of the guide is on [kontier.eu/stripe-xrechnung](https://kontier.eu/stripe-xrechnung/), and the Kontier API reference is at [docs.kontier.eu](https://docs.kontier.eu/introduction).

Issues and corrections are welcome, especially when a newer KoSIT configuration changes a result.

## License

Documentation, mapping and examples: [CC BY 4.0](LICENSE). The KoSIT validator and its configuration are not included; they are downloaded from their official releases under their own licenses (Apache 2.0).
