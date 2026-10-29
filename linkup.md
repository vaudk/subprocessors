# Linkup (Web Search API) – Data Residency & Sub-processor Notes

_Researched: 2026-10-29. Research notes, not legal advice; have DPO/legal review before use._

## Summary

- Linkup is a web search API for AI applications, with its own search index. It is run by **Linkup Technologies SAS**, Fresnes (near Paris), France. Neither its own documents nor the French register mention a US parent.
- **By default, queries are not kept in the EU.** The security docs say: "By default, queries may be processed across US, EU, Canada, and APAC regions based on load allocation. Local query processing is not guaranteed." Guaranteed processing in one geography is **enterprise-only**.
- The **privacy policy** says "We process and store your Data within the EU". That policy only covers Linkup's own controller data (website visitors, client contacts, suppliers), **not API queries**. Don't rely on it for query residency.
- **Training on prompts:** the terms on linkup.so (dated August 2025) give Linkup "a worldwide right to use, reproduce and modify the Client Data, including prompts, for the purposes of providing, maintaining, developing, training and improving the Linkup Solution" (§6.5).
- **Narrow processor role:** in the same terms (Art. 10), Linkup is processor only for "data related to Client's Operators: name and email". Search queries aren't treated as personal data. A **GDPR Art. 28 DPA** is available separately, signed via DocuSign or requested at privacy@linkup.so.
- **Zero data retention (ZDR) is available on request, not by default.** With ZDR, queries and results are processed in memory only and never written to persistent storage.
- **Hosting:** Microsoft Azure ("United States, France, European Union") and Google Cloud ("European Union"), per the trust center. Both are US-owned, so the CLOUD Act caveat applies.

## Query processing options

| Option | Where queries are processed | Availability |
|---|---|---|
| Default | "Across US, EU, Canada, and APAC regions based on load allocation" | All plans. **Not acceptable for EU-only data.** |
| Guaranteed local processing | One specified geography (e.g. EU) | Enterprise agreement |
| Multi-routing | Queries from each region processed locally | Enterprise agreement |
| Bring Your Own Cloud (BYOC) | Index and processing run in the customer's environment | "Selected high-tier partner deployments" |
| ZDR (combinable) | No query logging, no result retention; in-memory only | On request ("book a call") |

## Sub-processors

From the [trust center](https://trust.linkup.so/) (Subprocessors tab, browser only; transcribed on 2026-10-29):

| Sub-processor | Purpose (as stated) | Location (as stated) |
|---|---|---|
| Microsoft Azure | "Used to host our main infrastructure" | United States, France, European Union |
| Google Cloud | "Used to host our main infrastructure" | European Union |

The privacy policy (controller data only) also names Google Analytics, Attio (CRM), Google Mail, Google Drive and Vercel (website hosting). The trust center doesn't name the infrastructure behind the Canada and APAC processing locations. **TODO: ask Linkup.**

The terms' subcontracting clause lets Linkup "subcontract all or part of its obligations ... to the subcontractor of its choice". Notice or objection rights would have to come from the DPA.

## Retention and training

| Topic | What Linkup says |
|---|---|
| Training | Terms §6.5 (August 2025): right to use Client Data "including prompts" for "developing, training and improving the Linkup Solution". **TODO: check the August 2026 terms and the DPA, which may restrict this.** |
| Default retention of queries | Not stated. ZDR is "not enabled by default", which implies queries are logged otherwise. |
| ZDR | On request. "Search queries are not logged or stored on Linkup systems"; "results are not retained after delivery to the client". |
| Personal data at contract end | Returned or deleted, unless the law requires keeping it (terms Art. 10). |
| Breach notification | 24 hours under the terms (Art. 10). The security docs say 72 hours, with "24-hour capability for critical incidents". |

## Transfers

- Under the default routing, queries can go to the **US, Canada and APAC**. That is a third-country transfer for any personal data in queries. Canada has an EU adequacy decision for commercial organisations; the US and APAC regions don't (unless DPF-certified).
- No transfer mechanism (SCCs/DPF) is named in the terms or the public docs. **TODO: check the DPA.**
- Azure is listed with a US location, so even with EU routing, some infrastructure is in the US.

## Contracting entity

**Linkup Technologies**, a French SAS:
- Address: 28 avenue des Pépinières, 94260 Fresnes, France
- RCS Créteil 930 910 740 (SIREN 930910740); created 2024-07-05; active
- Contacts: privacy@linkup.so (privacy and DPA), contact@linkup.so (ZDR, enterprise options), support@linkup.so (data subject rights per privacy policy)

The terms and the privacy policy name this entity, and the French company register (recherche-entreprises.api.gouv.fr) confirms the address and creation date. There's no legal-notice page on linkup.so.

## Sub-processor list entry (draft)

| Field | Entry |
|---|---|
| Sub-processor | Linkup Technologies SAS |
| Address | 28 avenue des Pépinières, 94260 Fresnes, France (RCS Créteil 930 910 740) |
| Service | Linkup web search API |
| Purpose | Web search for AI features (processing of search queries, returning results) |
| Processing location | Default: US, EU, Canada, APAC (load-based). EU only with an enterprise agreement for guaranteed local processing (**TODO**). |
| Data categories | **TODO**: search queries (may contain personal data if derived from user input), account and operator contact data |
| Third-country transfers | Yes under default routing (US, Canada, APAC). Hosting on Microsoft Azure (incl. US) and Google Cloud. Safeguards: **TODO: confirm in DPA** |
| Further sub-processors | https://trust.linkup.so/ (Subprocessors tab) |

## Notes

- **Two versions of the terms:** linkup.so/terms-of-use shows the version of August 2025. The docs link to a Notion page dated **August 2026**, which couldn't be captured completely. Check which one applies to us, and whether §6.5 (training) is still in the newer version.
- **Certifications:** the docs say SOC 2 Type II, ISO 27001 and HIPAA. The trust center lists ISO 27001:2022 as **"(In progress)"**. The SOC 2 Type II report is downloadable from the trust center.
- **Similar to Staan:** both search APIs treat queries as not personal data. See `staan.md`. If our queries are built from user prompts, they can contain personal data, and we need the DPA to cover them.

## Open items

- [ ] Sign or request the DPA (DocuSign or privacy@linkup.so). Check that it covers query content, restricts training, names sub-processors and the transfer mechanism, and sets a change-notice process.
- [ ] Get an enterprise agreement with guaranteed EU query processing, or don't send personal data in queries.
- [ ] Request ZDR (contact@linkup.so).
- [ ] Get the August 2026 terms (Notion page) in full and check §6.5 (training on prompts).
- [ ] Ask which infrastructure serves the Canada and APAC regions (not on the trust center's sub-processor list).
- [ ] Ask about the ISO 27001 status (trust center: "in progress"; docs: certified).
- [ ] Download the SOC 2 Type II report from the trust center.

## Key documents

_Links checked: 2026-10-29._

| Document | Link | Status |
|---|---|---|
| Sub-processor list | [Linkup Trust Center](https://trust.linkup.so/) (Subprocessors tab) | Public, browser only. Two entries: Microsoft Azure, Google Cloud. |
| Privacy policy | [Linkup Privacy Policy](https://www.linkup.so/privacy-policy) | Public. "Last update: April 2025" (in force 2025-03-25). Covers controller data, not API queries. |
| Terms of service | [Client General Terms and Conditions (Notion)](https://linkup-platform.notion.site/Linkup-Client-General-Terms-and-Conditions-13f161ecef69806784dfe808b4e162a1); [Terms of Use (linkup.so)](https://www.linkup.so/terms-of-use) | Public. Notion version "Last update: August 2026" (only partly captured); linkup.so version "Last update: August 2025". |
| Data processing agreement (DPA) | [DPA via DocuSign](https://eu.docusign.net/Member/PowerFormSigning.aspx?PowerFormId=31e557f6-9921-4b64-a3cf-03ac25d3a403&env=eu&acct=c4a5ec7f-e68e-48e6-a860-245c445a98b1&v=2) | **No public text.** The DocuSign form only shows the text after you start signing; also "available on request" (privacy@linkup.so). |
| Transfer safeguards (SCCs / DPF) | n/a | **No public link.** No mechanism named in the terms or docs. |
| Security / trust center | [Linkup Trust Center](https://trust.linkup.so/); [Security and compliance docs](https://docs.linkup.so/pages/security-and-privacy/security-compliance) | Public. SOC 2 Type II, HIPAA; ISO 27001 "(In progress)" on the trust center. |
| Legal notice / imprint | n/a | **No legal-notice page found.** Entity details are in the privacy policy and terms. |
| Sub-processor change notifications | n/a | **Not documented.** |

## Sources

- [Linkup – Privacy Policy](https://www.linkup.so/privacy-policy)
- [Linkup – Terms of Use (Client General Terms, August 2025)](https://www.linkup.so/terms-of-use)
- [Linkup – Client General Terms and Conditions (Notion, August 2026)](https://linkup-platform.notion.site/Linkup-Client-General-Terms-and-Conditions-13f161ecef69806784dfe808b4e162a1)
- [Linkup – DPA (DocuSign PowerForm)](https://eu.docusign.net/Member/PowerFormSigning.aspx?PowerFormId=31e557f6-9921-4b64-a3cf-03ac25d3a403&env=eu&acct=c4a5ec7f-e68e-48e6-a860-245c445a98b1&v=2)
- [Linkup Trust Center](https://trust.linkup.so/)
- [Linkup Docs – Security and privacy overview](https://docs.linkup.so/pages/security-and-privacy/overview)
- [Linkup Docs – Data processing and privacy](https://docs.linkup.so/pages/security-and-privacy/data-processing-privacy)
- [Linkup Docs – Security and compliance](https://docs.linkup.so/pages/security-and-privacy/security-compliance)
- [Linkup Docs – Security FAQ](https://docs.linkup.so/pages/security-and-privacy/faq)
- [Recherche d'entreprises (French government register API) – SIREN 930910740](https://recherche-entreprises.api.gouv.fr/search?q=930910740)
