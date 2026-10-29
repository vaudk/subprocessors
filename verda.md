# Verda Cloud (formerly DataCrunch) – Data Residency & Sub-processor Notes

_Researched: 2026-10-29. Research notes, not legal advice; have DPO/legal review before use._

## Summary

- Verda is a Finnish GPU cloud: GPU instances, clusters, serverless containers, inference and storage. It was called **DataCrunch** until November 2025. The legal entity is **Verda Cloud Oy** (formerly DataCrunch Oy), Helsinki, with business ID 2886809-8. The Terms of Service still call it "Verda (DataCrunch Oy)". There is no US parent.
- **Data centres:** the docs list three sites, all in Helsinki (FIN-01, FIN-02, FIN-03). Press reports also mention Iceland, and a 2026 funding round to expand into the US, UK and Asia. **TODO: confirm which locations our workloads use.**
- **The DPA is built into the Terms of Service** as "Data Processing Terms" (section 10, terms last updated 2025-09-30), so it's public and agreed on sign-up:
  - Verda is processor for customer data.
  - Data is stored and processed "within the EEA" as a priority. Transfers outside the EEA only "on an exceptional basis", with an adequacy decision or SCCs.
  - Verda must tell us in advance about any processing outside the EEA, and we can object (§10.6.4–10.6.5).
  - 15 days' notice of new sub-processors (§10.7.3).
  - Breach notice within 48 hours.
- **Sub-processors:** 13 listed in the trust center. Most handle support, billing and marketing data, not compute workloads. **Exception:** Black Forest Labs processes "FLUX.1 Kontext content" in the EU and US. That applies only if we use Verda's FLUX inference endpoints. See `black-forest-labs.md`.
- **Certifications** (trust center): ISO 27001:2022, ISO 27017, ISO 27018, ISO 27701, SOC 2 Type II (ISAE 3000) and C5 Type 2. Reports are behind "Request access".
- **No CLOUD Act exposure for the core service.** Verda is a Finnish company with its own data centres. US-owned sub-processors (Cloudflare, Google, Stripe, AWS, Front, DocuSign, Dropbox) only see support, billing, DNS or analytics data.

## Locations

| Location | Site | Notes |
|---|---|---|
| FIN-01 | Helsinki, Finland | 50% hydro, 50% wind |
| FIN-02 | Helsinki, Finland | 50% hydro, 50% wind |
| FIN-03 | Helsinki, Finland | 100% renewable |
| Iceland | Not in the docs | Mentioned in press and third-party pages. **TODO: verify.** |

The docs say: "All data is handled and stored securely within Europe" ([Locations and Sustainability](https://docs.verda.com/welcome-to-verda/locations-and-sustainability), revision 2026-08-04). If Verda adds US or Asian sites, picking the location will matter. **TODO: check how a location is selected per instance or container.**

## Sub-processors

From the [trust center](https://trust.verda.com/subprocessors) (browser only; transcribed on 2026-10-29):

| Sub-processor | Purpose | Location | Relevance for us |
|---|---|---|---|
| Black Forest Labs | Processing FLUX.1 Kontext content | EU & US | **Only if we use Verda's FLUX inference.** Content may go to the US. |
| Hetzner | Website host and infrastructure provider | EU | Website; "infrastructure" not specified further |
| Cloudflare | DNS services | EU & US | DNS |
| Amazon Web Services | Customer communication services | EU | Emails to customers |
| Front | Customer support | US | Support conversations |
| PagerDuty | Cloud monitoring | EU | Monitoring |
| Checkmk | Cloud monitoring | EU | Monitoring |
| DocuSign | Document management | EU & US | Contracts |
| Dropbox | Document management | EU, US, UK, AUS or JPN | Documents |
| Google | Data and web analytics | EU & US | Analytics |
| Hotjar | Data analytics | EU | Website analytics |
| HubSpot | Marketing and CRM | EU | CRM |
| Stripe | Payment processing | EU & US | Billing |

The terms also allow Verda to use any of its **affiliates** as sub-processors (§10.7.1) without listing them.

## Data processing terms (ToS section 10)

| Topic | What the terms say |
|---|---|
| Role | Customer is controller (or processor for a third party); Verda is processor |
| Location | "Prioritize to store and process all Personal Data within the EEA" (§10.6.1) |
| Transfers | Only "on an exceptional basis, where such transfer is strictly necessary", with an adequacy decision or the 2021 SCCs plus extra measures (§10.6.2–10.6.3) |
| Notice of transfers | Verda informs us **in advance** of storage or processing outside the EEA, with location and sub-processor. We can object, and Verda then must not transfer; we can terminate without penalty (§10.6.4–10.6.5). |
| New sub-processors | At least **15 days'** notice, naming the sub-processor, data, purpose and location. If we object, we can terminate within two weeks; silence counts as approval (§10.7.3–10.7.4). |
| Breach notification | Without undue delay, at most **48 hours** (§10.5.1) |
| Deletion | We delete data ourselves. Deleted data is recoverable for **96 hours** unless "delete permanently" is used (§10.9.2). |
| Training / own use | Privacy policy: Verda does "not use this data" (customer-uploaded data) for its own purposes and only processes it under our instructions. |
| Governing law | Finnish law; arbitration in Helsinki |

## Contracting entity

**Verda Cloud Oy** (formerly DataCrunch Oy):
- Business ID (Y-tunnus): 2886809-8; registered 2018-02-08
- Address: Töölönlahdenkatu 2, 00100 Helsinki, Finland
- Contacts: compliance@verda.com, support@verda.com; vulnerability reports via vdp.verda.com

Source: the Finnish trade register (PRH open data API), which shows the name change. The Terms of Service (2025-09-30) still name "DataCrunch Oy". It's the same company, but use the current name in our records.

## Sub-processor list entry (draft)

| Field | Entry |
|---|---|
| Sub-processor | Verda Cloud Oy (formerly DataCrunch Oy) |
| Address | Töölönlahdenkatu 2, 00100 Helsinki, Finland (Business ID 2886809-8) |
| Service | Verda GPU cloud (**TODO: instances / containers / inference — which do we use?**) |
| Purpose | GPU compute and hosting for AI workloads |
| Processing location | Finland (Helsinki data centres FIN-01 to FIN-03); EEA by contract |
| Data categories | **TODO** |
| Third-country transfers | None by design for compute. Possible: support (Front, USA), billing (Stripe), DNS (Cloudflare), analytics (Google), and FLUX inference via Black Forest Labs (EU & US) if used. Safeguards: adequacy decision or 2021 SCCs (ToS §10.6.3). |
| Further sub-processors | https://trust.verda.com/subprocessors |

## Notes

- **FLUX via Verda:** if we use Verda's FLUX.1 Kontext endpoints, Black Forest Labs becomes a sub-sub-processor with EU & US locations. Check under which BFL terms Verda passes content on (training, retention). See `black-forest-labs.md`.
- **Affiliates as sub-processors:** allowed without listing. Ask whether any non-EEA affiliates exist (e.g. after the US expansion).
- **Privacy policy PDF:** the retention table printed with cut-off columns. Read the live page for exact retention periods (billing data: "minimum 6 years").

## Open items

- [ ] Confirm which Verda locations our workloads run in (Helsinki only?) and how location is chosen.
- [ ] If we use Verda's FLUX inference: confirm what Black Forest Labs receives, whether it stays in the EU, and whether BFL trains on it.
- [ ] Ask whether Verda has affiliates outside the EEA that act as sub-processors.
- [ ] Find out how sub-processor change notices are sent (email to the account owner?) and make sure that mailbox is monitored.
- [ ] Request the ISO 27001 / SOC 2 / C5 reports from the trust center.
- [ ] Use "Verda Cloud Oy" in our records; check our invoices for the entity name.

## Key documents

_Links checked: 2026-10-29._

| Document | Link | Status |
|---|---|---|
| Sub-processor list | [Verda Trust Center – Subprocessors](https://trust.verda.com/subprocessors) | Public, browser only. 13 entries (transcribed to PDF). |
| Privacy policy | [Verda Privacy Policy](https://verda.com/privacy-policy) | Public. The "Last updated" date is blank on the page; older versions in the [archive](https://verda.com/privacy-policy/archive). |
| Terms of service | [Verda Terms and Conditions](https://verda.com/terms-and-conditions) | Public. Last updated 2025-09-30. |
| Data processing agreement (DPA) | [Terms and Conditions, section 10 "Data Processing Terms"](https://verda.com/terms-and-conditions) | Public, part of the terms. |
| Transfer safeguards (SCCs / DPF) | [Terms and Conditions, §10.6](https://verda.com/terms-and-conditions) | Included in the terms: adequacy decision or the 2021 SCCs. No separate SCC document. |
| Security / trust center | [Verda Trust Center](https://trust.verda.com/) | Public. ISO 27001/27017/27018/27701, SOC 2 Type II, C5 Type 2; reports via "Request access". |
| Legal notice / imprint | n/a | **No legal-notice page found.** Entity details from the Finnish trade register. |
| Sub-processor change notifications | [Terms and Conditions, §10.7.3](https://verda.com/terms-and-conditions) | 15 days' notice by Verda; no subscription page found. |

## Sources

- [Verda – Terms and Conditions](https://verda.com/terms-and-conditions)
- [Verda – Privacy Policy](https://verda.com/privacy-policy) and [archive](https://verda.com/privacy-policy/archive)
- [Verda – Cookie Policy](https://verda.com/cookie-policy)
- [Verda Trust Center](https://trust.verda.com/) and [Subprocessors](https://trust.verda.com/subprocessors)
- [Verda Docs – Locations and Sustainability](https://docs.verda.com/welcome-to-verda/locations-and-sustainability)
- [PRH open data (Finnish trade register) – Verda Cloud Oy, 2886809-8](https://avoindata.prh.fi/opendata-ytj-api/v3/companies?businessId=2886809-8)
- [The Next Web – Verda raises $117 million (third party)](https://thenextweb.com/news/verda-117m-funding-lifeline-ventures-ai-cloud)
