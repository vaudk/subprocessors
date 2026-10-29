# Scaleway (Generative APIs / Cloud) – Data Residency & Sub-processor Notes

_Researched: 2026-10-28. Research notes, not legal advice; have DPO/legal review before use._

## Summary

- Scaleway is a **French company** (Scaleway SAS, Paris), a majority-owned subsidiary of the **Iliad group**. Its cloud regions are all in the EU: **Paris, Amsterdam, Warsaw, Milan**.
- **Generative APIs (Serverless)** runs only in **Paris, France** (`fr-par`), in an OPCORE data center. Scaleway reserves the right to add "other European regions" later, with notice to customers.
- **Generative APIs – Dedicated Deployment** (called **Managed Inference** until 1 May 2026) is pinned to a region. The official SDK lists `fr-par` as its only region.
- Default **zero data retention** for prompts and outputs. Exceptions: request content kept for up to 2 weeks when debugging errors or abuse, and Batch inputs kept for 24 h.
- The DPA says services are "located within the European Union by default". Outside the EU, Scaleway must inform the customer in advance and apply SCCs.
- Known non-EU links: three **US-headquartered colocation providers** (EU sites only) and one **Canadian** sub-processor for Dedibox VPS. Neither affects Generative APIs in Paris (see below).

## Regions and Availability Zones

From Scaleway's documentation and SDK. Scaleway publishes region-level cities, not individual data-center addresses per AZ.

| Region | Location | Availability Zones |
|---|---|---|
| `fr-par` | Paris, France | fr-par-1, fr-par-2, fr-par-3 |
| `nl-ams` | Amsterdam, Netherlands | nl-ams-1, nl-ams-2, nl-ams-3 |
| `pl-waw` | Warsaw, Poland | pl-waw-1, pl-waw-2, pl-waw-3 |
| `it-mil` | Milan, Italy | it-mil-1. it-mil-2 and it-mil-3 were announced in the changelog on 2026-10-12. |

- Docs mention French data centers **DC2, DC3, DC4, DC5**. These are the HDS-certified sites; Scaleway's environmental-footprint page cites DC5 as an example of a site hosting Dedicated Deployment.
- A **Sweden** region is described as "planned". atNorth (Sweden) appears on the sub-processor list (search results only).
- **Dedibox VPS** uses BrainStorm Network Inc. (**Canada**). This is a separate product line, not used for Generative APIs.

## Generative APIs – where processing happens

| Mode | Endpoint | Location |
|---|---|---|
| Serverless | `api.scaleway.ai` | Paris, France (OPCORE data center). Scaleway says it "may add further locations in other European regions" and will notify customers before moving from France-only to Europe. |
| Dedicated Deployment (ex-Managed Inference) | `<uuid>.ifr.fr-par.scaleway.com` | The region chosen at deployment. `fr-par` is the only region in the SDK, so Paris, France. Scaleway recommends this mode "to ensure that processing stays in a single region". |

Data handling (from the Generative APIs privacy policy and the AI Services Specific Conditions, 07/04/2026):

- Prompts and outputs aren't logged, aren't used for training, and aren't accessible to model creators or third parties.
- Exception: on abuse or errors such as HTTP 500, the full HTTP request content may be stored for **up to 2 weeks** for debugging.
- **Batch** inputs are stored for 24 h.
- The prompt cache (prefix hashes or KV values) is ephemeral and can't be disabled.
- Anonymized usage metadata is kept for up to 6 months.
- Account data falls under Scaleway's general Privacy Policy.

## Sub-processors (official list – not directly verified)

`scaleway.com/en/subprocessorlist/` and its change history returned **403** during research. The entries below come from search-result summaries and third-party pages.

| Sub-processor | HQ | Purpose | Processing location |
|---|---|---|---|
| Op Core (OPCORE) | EU | Colocation | France |
| atNorth | EU | Colocation | Sweden |
| 3S Data Center S.A. | EU | Colocation | Poland |
| Atman sp. z o.o. | EU | Colocation | Poland |
| **Equinix EMEA** | **US** | Colocation | Italy, Netherlands, Poland |
| **Iron Mountain** | **US** | Colocation | Netherlands |
| **Digital Realty** (replaced "Interxion") | **US** | Colocation | Netherlands |
| Atempo | FR | Object Storage cold-storage archiving | TODO: verify |
| Clever Cloud | FR | Web Platform product | TODO: verify |
| Confia | FR | Recycling of IT equipment (WEEE), all services | TODO: verify |
| **BrainStorm Network Inc.** | **Canada** | Dedibox VPS | TODO: verify (Canada assumed) |

- Recent changes: Cloudflare was removed, and Loxy was removed and replaced by Confia on 2025-04-22.
- Search results also show a change on **2025-07-17**: a processing-location column was added, atNorth was added, and Interxion was replaced by Digital Realty. This matches the entries above but has not been checked on the history page (403). Scaleway also has a Technical and Organizational Measures PDF dated 2024-07-17 ([www-uploads.scaleway.com](https://www-uploads.scaleway.com/Technical_and_Organizational_Measures_v17072024_a74508104e.pdf), not fetched).
- One third-party page (euvetted) counts 11 sub-processors and calls the US colocation providers "physical-only, no data access". This is not confirmed by Scaleway.
- **Iliad group:** no Iliad entity was seen as a sub-processor in the available excerpts, but Scaleway's DPO is shared with the group (`dpo@iliad.fr`). Press reports describe OpCore as Iliad's former data-center business, now a joint venture (not verified here).

## Third-country access / jurisdiction

| Item | Detail |
|---|---|
| Ownership | French/EU-owned (Iliad group). Scaleway states its AI services are "not subject to extraterritorial laws such as the American Cloud Act". |
| US-HQ colocation | Equinix, Iron Mountain and Digital Realty host sites in NL, IT and PL. The US parents could in theory be subject to US legal process; risk is limited to physical/facility access. Not relevant to `fr-par` Generative APIs (OpCore, France). |
| Canada | BrainStorm Network Inc. for Dedibox VPS only. Canada has a partial EU adequacy decision. |
| Support staff | Location not stated in the Support Services conditions or docs. Support works in English and French; hotline hours are UTC+1. **TODO: confirm.** |

## Contracting entity

Under General Terms of Services v. 07/04/2026, Art. 23, the entity depends on the **customer's billing address**:

| Billing address | Contracting entity |
|---|---|
| France and **all other countries** except Italy | **Scaleway SAS**, 8 rue de la Ville l'Évêque, 75008 Paris, France. RCS Paris **433 115 904**. Governed by French law, Paris courts. |
| Italy | Scaleway Italia S.R.L., 3 Piazza Filippo Meda, Milan, Italy. Registro delle Imprese Milano 143 047 909 68. Governed by Italian law. |

Scaleway's documents give different share-capital figures:

- EUR 142,050 in the GTS and AI conditions of 07/04/2026
- EUR 214,410.50 in the DPA of June 2024
- EUR 141,420 in the Support conditions of 07/2025

The RCS number is the same in all of them. Use the address and RCS number; capital isn't needed.

## Sub-processor list entry (draft)

| Field | Entry |
|---|---|
| Sub-processor | Scaleway SAS (**TODO: confirm on invoice**; Scaleway Italia S.R.L. only applies to an Italian billing address) |
| Address | 8 rue de la Ville l'Évêque, 75008 Paris, France (RCS Paris 433 115 904) |
| Service | Scaleway Generative APIs (Serverless and/or Dedicated Deployment); **TODO** add other Scaleway products if used |
| Purpose | AI model inference (processing of prompts and outputs); hosting if other products are used |
| Processing location | France (Paris, `fr-par`). Serverless could later extend to other EU regions after notice; Dedicated Deployment stays in the chosen region. |
| Data categories | **TODO**: what we actually send, e.g. user content and prompts, account data |
| Third-country transfers | None for Generative APIs in `fr-par` according to Scaleway docs. DPA Art. 11: EU by default, prior notice and SCCs (Decision 2021/914, C2P/P2P) for any non-EU transfer. **TODO: confirm support-staff location.** |
| Further sub-processors | https://www.scaleway.com/en/subprocessorlist/ |

## Notes

- The DPA (version of 1 June 2024) gives a **general authorization** for sub-processors and **30 days' notice** of changes, but only if we subscribe to notifications on the sub-processor page. We can object; if unresolved, we can terminate, stop using the affected service, or switch to an equivalent offer.
- **SecNumCloud:** Scaleway entered the ANSSI qualification process in 2025. It is still "en cours" (in progress, not qualified) as of October 2026, according to a third-party article dated 2026-10-22.
  - Announced scope is IaaS (Instances, GPU, storage, VPC, IAM, and others) in one Paris AZ.
  - Generative APIs is **not** listed in the announced scope.
- **HDS** (French health-data hosting) certification covers selected products in the French data centers, not Generative APIs.
- Scaleway docs were read from the official `scaleway/docs-content` GitHub repository (commit of 2026-10-28) because scaleway.com returned 403.

## Open items

- [ ] Confirm the contracting entity on our Scaleway invoice (Scaleway SAS unless billed to Italy).
- [ ] Verify the sub-processor list directly on scaleway.com. It returned 403, so the table comes from search snippets and third-party pages, and locations for Atempo, Clever Cloud and Confia are unverified.
- [ ] Subscribe to sub-processor change notifications. Without this, the 30-day notice in DPA Art. 7.4 doesn't apply.
- [ ] Ask Scaleway in writing where support staff with access to customer data are located, and whether any Iliad group entity acts as a sub-processor.
- [ ] Check whether a DPA newer than 1 June 2024 exists on the contracts page.
- [ ] Decide between Serverless and Dedicated Deployment. Serverless may move to "Europe" after notice, while Dedicated stays in `fr-par`.
- [ ] Confirm the `it-mil-2` and `it-mil-3` AZs and Dedicated Deployment region availability on the "Product availability by region" page (403 during research).

## Key documents

_Links checked: 2026-10-29._

| Document | Link | Status |
|---|---|---|
| Sub-processor list | [Scaleway – Sub-processor list](https://www.scaleway.com/en/subprocessorlist/) | Public, but returned 403 on 2026-10-28; not re-checked on 2026-10-29 (link check blocked in this session). The Trust Center also lists "Subprocessors" under Legal. |
| Privacy policy | [Scaleway – Privacy policy](https://www.scaleway.com/en/privacy-policy/); [Generative APIs Privacy Policy (docs)](https://www.scaleway.com/en/docs/generative-apis/reference-content/data-privacy/) | Public; the main policy returned 403 when fetched, so no version date seen. |
| Terms of service | [Scaleway – General Terms of Services, 07/04/2026 (PDF)](https://www-uploads.scaleway.com/Conditions_Generales_de_Services_General_Terms_of_Services_07042026_f7d4a2f613.pdf); [Specific Conditions AI Services, 07/04/2026 (PDF)](https://www-uploads.scaleway.com/Specific_Conditions_AI_Services_275907180f.pdf); [Contracts page](https://www.scaleway.com/en/contracts/) | Public PDFs, version 07/04/2026; PDFs not re-checked on 2026-10-29 (link check blocked), contracts page returned 403. |
| Data processing agreement (DPA) | [Scaleway – Data Processing Agreement, 1 June 2024 (PDF)](https://www-uploads.scaleway.com/DPA_2024_ENG_b0abb5cc26.pdf) | Public PDF, version of 1 June 2024; not re-checked on 2026-10-29 (link check blocked). |
| Transfer safeguards (SCCs / DPF) | [SCCs Controller-to-Processor (PDF)](https://www-uploads.scaleway.com/CCT_Controller_to_Processor_0b5e47bb5c.pdf); [SCCs Processor-to-Processor (PDF)](https://www-uploads.scaleway.com/CCT_Processor_to_Processor_a1299cdbfc.pdf) | SCCs published separately and referenced in DPA Art. 11; not re-checked on 2026-10-29 (link check blocked). No DPF listing found (French company, DPF not relevant). |
| Security / trust center | [Scaleway Trust Center](https://security.scaleway.com/); [Security and compliance](https://www.scaleway.com/en/security-and-compliance/) | Trust Center is public and loads (SafeBase); most documents (ISO 27001, SoA, HDS, CAIQ) sit behind "Get access". The security-and-compliance page returned 403. |
| Legal notice / imprint | [Scaleway – Legal Notice](https://www.scaleway.com/en/legal-notice/) | Public, but returned 403 when fetched; company identity is also in GTS Art. 23. |
| Sub-processor change notifications | [Sub-processor change history](https://www.scaleway.com/en/subprocessorlist/history/); [Trust Center](https://security.scaleway.com/) | DPA Art. 7.4: subscribe on the sub-processor page (403, so the form wasn't seen); the Trust Center has a "Subscribe" button for updates, scope not stated. |

## Sources

- [Scaleway – Generative APIs Privacy Policy (docs)](https://www.scaleway.com/en/docs/generative-apis/reference-content/data-privacy/) ([GitHub source](https://github.com/scaleway/docs-content/blob/main/pages/generative-apis/reference-content/data-privacy.mdx))
- [Scaleway – Generative APIs FAQ (docs)](https://www.scaleway.com/en/docs/generative-apis/faq/) ([GitHub source](https://github.com/scaleway/docs-content/blob/main/pages/generative-apis/faq.mdx))
- [Scaleway changelog – Managed Inference becomes Generative APIs – Dedicated Deployment](https://github.com/scaleway/docs-content/blob/main/changelog/april2026/2026-04-29-managed-inference-changed-managed-inference-becomes-gen.mdx)
- [Scaleway changelog – Full 3-AZ region in Milan](https://github.com/scaleway/docs-content/blob/main/changelog/october2026/2026-10-08-partner-space-added-a-full-3-az-region-in-milan.mdx)
- [Scaleway Go SDK – Inference API regions](https://github.com/scaleway/scaleway-sdk-go/blob/master/api/inference/v1/inference_sdk.go) and [locality.go](https://github.com/scaleway/scaleway-sdk-go/blob/master/scw/locality.go)
- [Scaleway – Product availability by region](https://www.scaleway.com/en/product-availability-by-region/) (403 during research)
- [Scaleway – Data Processing Agreement, version of 1 June 2024 (PDF)](https://www-uploads.scaleway.com/DPA_2024_ENG_b0abb5cc26.pdf)
- [Scaleway – General Terms of Services, version of 07/04/2026 (PDF)](https://www-uploads.scaleway.com/Conditions_Generales_de_Services_General_Terms_of_Services_07042026_f7d4a2f613.pdf)
- [Scaleway – Specific Conditions AI Services, version of 07/04/2026 (PDF)](https://www-uploads.scaleway.com/Specific_Conditions_AI_Services_275907180f.pdf)
- [Scaleway – Specific Conditions Support Services, 01/07/2025 (PDF)](https://www-uploads.scaleway.com/Specific_Conditions_Support_Services_VF_ef8f879c83.pdf)
- [Scaleway – SCCs Controller-to-Processor (PDF)](https://www-uploads.scaleway.com/CCT_Controller_to_Processor_0b5e47bb5c.pdf) / [Processor-to-Processor (PDF)](https://www-uploads.scaleway.com/CCT_Processor_to_Processor_a1299cdbfc.pdf)
- [Scaleway Sub-processor list (official)](https://www.scaleway.com/en/subprocessorlist/) and [change history](https://www.scaleway.com/en/subprocessorlist/history/) (both 403 during research)
- [Scaleway – SecNumCloud page](https://www.scaleway.com/en/security-and-compliance/secnumcloud/) (not fetched)
- [Scaleway – Privacy policy](https://www.scaleway.com/en/privacy-policy/) (403 on 2026-10-29)
- [Scaleway – Contracts page](https://www.scaleway.com/en/contracts/) (403 on 2026-10-29)
- [Scaleway – Legal Notice](https://www.scaleway.com/en/legal-notice/) (403 on 2026-10-29)
- [Scaleway – Security and compliance](https://www.scaleway.com/en/security-and-compliance/) (403 on 2026-10-29)
- [Scaleway – Trust Center](https://security.scaleway.com/)
- [euvetted – Scaleway (third party)](https://euvetted.com/p/scaleway)
- [Qovery – SecNumCloud comparison, 2026-10-22 (third party)](https://www.qovery.com/blog/numspot-ovhcloud-scaleway-secnumcloud-sovereign-cloud-comparison)
- [Pappers – Scaleway SAS, SIREN 433115904](https://www.pappers.fr/entreprise/scaleway-433115904)
