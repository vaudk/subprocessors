# Staan (European Search API) – Data Residency & Sub-processor Notes

_Researched: 2026-10-29. Research notes, not legal advice; have DPO/legal review before use._

## Summary

- Staan is a web search API (Web Search and Web Search for AI). It is run by **European Perspective SAS** in Paris, the joint venture of **Qwant** (Paris) and **Ecosia** (Berlin) also known as "European Search Perspective" (EUSP). Neither has a US parent.
- **Hosting is OVHcloud in France**, according to the privacy policy, the FAQ and the legal notice. `api.staan.ai` resolves to an OVH SAS IP (AS16276, FR). No region choice is needed or offered.
- **Exception: a US "fallback solution".** The FAQ says: "It is an American backup mechanism that steps in to supplement certain results only when necessary."
  - It is **not named** anywhere and is **not in the privacy policy's list of recipients**.
  - Staan says queries sent to it "are not retained by it" and are not stored in the US.
  - You can **switch it off** by asking Staan ("EU-only routing on request"). Staan warns that some results may get worse.
- **Staan's position:** "requests sent to the Staan search API do not constitute personal data within the meaning of the GDPR". The client is "solely responsible" for data protection compliance. The DPA is only "available upon request". If our search queries can contain personal data (for example, queries built from user prompts), this position needs to be challenged.
- **Training:** the FAQ says data is "never shared with third parties for commercial purposes, nor used to train a language model (LLM)".

## API

| Item | Detail |
|---|---|
| Base URL | `https://api.staan.ai/v2` |
| Hosting (observed) | `api.staan.ai` and `docs.staan.ai` → 51.91.208.86; `staan.ai` → 51.91.151.171. Both belong to OVH SAS (AS16276) in France. IP geolocation gives Strasbourg, which is not reliable at city level. |
| Products | Web Search: €2 per 1,000 requests, up to 20 QPS. Web Search for AI: €4 per 1,000 requests, with extra snippets. The first 1,000 requests each month are free. Custom plans can include dedicated infrastructure or custom endpoints. |
| Search stack | "Runs on the same search stack as Qwant Search", with its own European index |

## Hosting and sub-processors (from the privacy policy, June 2026)

The table below is the recipients table from the privacy policy, in both the English and French versions.

| Sub-processor | Purpose | Data | Location | Transfer mechanism |
|---|---|---|---|---|
| OVH France | Infrastructure for the search API | Logs, account data, "Market Search ID" | France | n/a |
| Batch | Client account management | ID, name, email, phone | France | n/a |
| Datadome | Abuse detection | IP address, device data, mouse and click signals | France | n/a |
| Piwik Pro | Website analytics and A/B testing | Browsing events, obfuscated IP | EU | n/a |
| **Notion** | Support tickets | Name, first name, contact details | **USA** | SCCs ("clauses contractuelles types") |
| **Slack** | Internal communication | ID, name, email | **USA** | SCCs |
| **Unnamed US fallback** | Supplements search results "only when necessary" | Search queries (assumed) | **USA** | **Not stated.** It is not in the privacy policy table. |

The website's OVH hosting entity is OVH SAS, 2 rue Kellermann, 59100 Roubaix, France (RCS Lille Métropole 424 761 419).

**Who the US fallback is:** Staan doesn't say. Qwant and Ecosia have historically taken results from Microsoft Bing, so Bing is a plausible candidate, but **this is not confirmed**. **TODO: ask Staan in writing to name it.**

**Payment provider:** not named in the privacy policy, which mentions "payment method" under billing. **TODO: ask Staan.** Stripe would be a typical choice and would mean a US transfer.

## Retention

| Category | Retention (per privacy policy) |
|---|---|
| Abuse detection data (IP, device signals) | 30 days |
| Anonymized search queries (website search interface) | 25 months |
| Audience measurement (Piwik Pro) | 25 months |
| Contact form data | 2 years from the last interaction |
| API account data | 1 year after account deletion |
| Support records | 1 year after account deletion |
| Billing data | 10 years (French Commercial Code) |
| **API request logs** | **Not stated.** OVH processes "logs", but no retention period is given. |

- **Use of query data:** "solely to better understand usage patterns and improve the quality of our services". It is not used for LLM training and not shared for commercial purposes.
- **Fallback:** queries sent to the US fallback "are not retained by it". This is Staan's claim; there are no contract terms behind it that we have seen.
- **Zero data retention:** no ZDR option is documented.

## Controller / processor question

- Staan's privacy policy treats API requests as **not personal data**, comparing them to how a search engine processes a search. Under that view Staan is neither our processor nor a controller of the query content.
- **Why it matters to us:** queries we send can contain personal data, for example when an agent turns user prompts into search queries that include names. In that case Staan processes personal data on our behalf and we need an **Art. 28 DPA**. Request the DPA ("available upon request") and check that it actually covers query content, not only account data.
- To reduce the risk on our side: avoid putting personal data in search queries where possible, for example by stripping or pseudonymizing names before the search call.

## Contracting entity

**European Perspective**, a French SAS (société par actions simplifiée):
- Address: 42 avenue de la Porte de Clichy, 75017 Paris, France
- RCS Paris 928 439 785 (SIREN 928439785); VAT FR 63 928 439 785; share capital €66,574; created 2024-04-29; active
- Owned 50/50 by Qwant and Ecosia (per press reports)
- DPO: dpo@eu-searchperspective.com
- Legal contact: legal@qwant.com
- Publication director: Boris Lecoeur

The legal notice on staan.ai names this entity, and the French company register (recherche-entreprises.api.gouv.fr) confirms the address and creation date. **TODO: confirm the contracting entity in the Terms of Service**, which are only provided at registration.

## Sub-processor list entry (draft)

| Field | Entry |
|---|---|
| Sub-processor | European Perspective SAS (Staan) |
| Address | 42 avenue de la Porte de Clichy, 75017 Paris, France (RCS Paris 928 439 785) |
| Service | Staan Web Search API (**TODO: Web Search or Web Search for AI tier**) |
| Purpose | Web search for AI features (processing of search queries, returning results) |
| Processing location | France (OVHcloud) |
| Data categories | **TODO**: search queries (may contain personal data if derived from user input), API account and billing contact data |
| Third-country transfers | Unless disabled: search queries may go to an unnamed US "fallback" search provider (Staan says the fallback does not retain them). Support and internal communication run on Notion and Slack (USA, SCCs); these cover account and contact data only. **TODO: get the fallback disabled ("EU-only routing") or document it with its safeguard.** |
| Further sub-processors | Listed in the privacy policy: https://staan.ai/privacy-policy (no separate sub-processor page) |

## Notes

- **CLOUD Act:** Staan claims "no exposure to the US CLOUD Act". This holds for the core stack: European Perspective, Qwant and Ecosia are EU companies, and OVH is French-owned. It does **not** hold for the US fallback, Notion or Slack.
- **Sub-processor change notice:** no notification process or notice period is documented. **TODO: check the DPA.**
- **Content removal:** https://staan.ai/removal handles index removal requests. This is relevant if people ask us about search results; it doesn't affect our own processing.
- **Docs portal:** the docs are built with Zudoku, Zuplo's open-source docs framework, but are served from the same OVH IP as the API. There's no sign of a third-party API gateway SaaS in the request path. The `server: istio-envoy` header points to self-run Kubernetes.

## Open items

- [ ] Request the DPA (only "available upon request") and check that it covers search query content and names all sub-processors, including the fallback.
- [ ] Ask Staan in writing who the US fallback provider is, what data it receives, and what transfer safeguard applies.
- [ ] Ask Staan to switch off the fallback for our account (EU-only routing), and test whether result quality is still good enough.
- [ ] Ask how long API request logs are kept and whether they're tied to our client ID.
- [ ] Ask which payment provider is used and where it processes data.
- [ ] Get the Terms of Service (not public; "provided upon registration") and file them with this note. Confirm the contracting entity from them.
- [ ] Ask whether Staan has a security overview or certifications (none published).
- [ ] Decide internally whether search queries we send can contain personal data; if so, consider stripping or pseudonymizing before the call.
- [ ] Ask how sub-processor changes are announced and subscribe to the notices.

## Key documents

_Links checked: 2026-10-29._

| Document | Link | Status |
|---|---|---|
| Sub-processor list | [Staan – Privacy Policy, recipients table](https://staan.ai/privacy-policy) | **No separate public list.** The recipients table in the privacy policy (June 2026) is the only list, and it leaves out the US fallback. |
| Privacy policy | [Staan – Privacy Policy (EN)](https://staan.ai/privacy-policy) / [FR](https://staan.ai/fr/privacy-policy) | Public. "Last updated: June 2026". |
| Terms of service | n/a | **No public link.** The FAQ says the Terms of Service "are provided upon registration". |
| Data processing agreement (DPA) | n/a | **No public link.** The FAQ says the DPA "is available upon request". |
| Transfer safeguards (SCCs / DPF) | [Staan – Privacy Policy](https://staan.ai/privacy-policy) | **No public link** to SCCs. The privacy policy names SCCs for Notion and Slack only, and no safeguard for the US fallback. |
| Security / trust center | n/a | **No public link.** No security page, certifications or trust center found. |
| Legal notice / imprint | [Staan – Legal mentions](https://staan.ai/legal) | Public. Names European Perspective SAS, RCS Paris 928 439 785. |
| Sub-processor change notifications | n/a | **Not documented.** **TODO: check the DPA.** |

## Sources

- [Staan – Homepage](https://staan.ai/)
- [Staan – FAQ](https://staan.ai/faq)
- [Staan – Privacy Policy (EN)](https://staan.ai/privacy-policy) / [FR](https://staan.ai/fr/privacy-policy)
- [Staan – Legal mentions](https://staan.ai/legal)
- [Staan – Documentation](https://docs.staan.ai/introduction)
- [Recherche d'entreprises (French government register API) – SIREN 928439785](https://recherche-entreprises.api.gouv.fr/search?q=928439785)
- [European Search Perspective](https://www.eu-searchperspective.com/)
- [Ecosia blog – Teaming up with Qwant (EUSP)](https://blog.ecosia.org/eusp/)
- [Ecosia blog – European search index goes live](https://blog.ecosia.org/launching-our-european-search-index/)
- [TechCrunch – Qwant and Ecosia debut Staan](https://techcrunch.com/2025/08/06/qwant-and-ecosia-debut-staan-a-european-search-index-that-aims-to-take-on-big-tech)
- [Scalevise – Staan: The European search engine (third party)](https://scalevise.com/resources/staan-european-search-engine/)
