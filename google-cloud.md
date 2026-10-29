# Google Cloud (Vertex AI / Gemini) – Data Residency & Sub-processor Notes

_Researched: 2026-10-28. Research notes, not legal advice; have DPO/legal review before use._

## Summary

- The Vertex AI **`eu` multi-region** endpoint (`https://aiplatform.eu.rep.googleapis.com`) keeps data at rest **and** ML processing within **EU member states**.
- The **UK and Switzerland are explicitly excluded** from `eu`, which makes it stricter than OpenAI's Europe region (EEA + CH).
- Google does **not** publish which individual regions make up the `eu` pool.
- Only some models support `eu`. Check each model's row in the data residency table.

Google's wording:

> "The European Union multi-region (`eu`) endpoint strictly covers data residency within EU member states. Geographies outside the European Union political boundary, including the United Kingdom and Switzerland, are excluded from this endpoint."

## Candidate regions behind `eu`

The European regions on Google's Gemini locations page. Google doesn't name the `eu` members, so these are candidates, not confirmed members.

| Region | Location | Can be in the `eu` pool? |
|---|---|---|
| europe-west1 | Belgium (St. Ghislain) | Yes |
| europe-west3 | Frankfurt, Germany | Yes |
| europe-west4 | Netherlands | Yes |
| europe-west8 | Milan, Italy | Yes |
| europe-west9 | Paris, France | Yes |
| europe-southwest1 | Madrid, Spain | Yes |
| europe-north1 | Finland | Yes |
| europe-central2 | Warsaw, Poland | Yes |
| europe-west2 | London, UK | No (excluded) |
| europe-west6 | Zürich, Switzerland | No (excluded) |

Other Google Cloud EU regions exist (e.g. Stockholm, Berlin, Turin) but are not listed for Gemini.

## Endpoint types

| Endpoint | Residency |
|---|---|
| `eu` multi-region | Guarantees EU-only storage **and** ML processing for supported models |
| Single region (e.g. `europe-west3`) | Exact country known. Google warns regional endpoints "don't guarantee data residency or in-region ML processing" unless the model is listed as supporting it. |
| `global` | **No** residency guarantee; requests can be routed anywhere. Don't use it if processing location matters. |

To name one specific country (e.g. for a DPIA), pin a single region with a model that supports data residency there.

## Retention, ZDR, caching and abuse monitoring

| Topic | Vertex AI (Gemini) |
|---|---|
| Training on our data | No without our prior permission or instruction |
| Abuse monitoring (prompt logging) | Applies only to customers under the GCP Terms of Service **without an invoiced billing account**. Prompts flagged by safety classifiers may be kept **up to 30 days** in our selected region or multi-region. (From search results; Google's abuse-monitoring page didn't load.) |
| Turning abuse logging off | Move to **invoiced billing**, or request an **exception** via Google's form. Applies to Google's own models only, **not** partner models (Claude, Mistral, etc.). |
| Data caching | **On by default** for Gemini: an in-memory tier and a durable tier, each deleted within **24 h**, in the region that served the request. Can be switched off per project (all caching, or durable only). **ZDR requires durable caching (or all caching) to be off.** |
| Grounding with Google Search | Queries and context logged for **up to 3 days**; can't be switched off. (Older sources say 30 days; the current page says 3.) |
| Grounding with Google Maps | Stored **30 days**; can't be switched off |
| Live API session resumption | Off by default; if enabled, prompts and outputs kept **up to 24 h** |
| Interactions API | `store=true` by default, so data is kept unless set to `false` |
| Request-response logging | Off by default; if enabled, stores prompts and outputs in our project |
| Deep Research agent, CodeMender | Session data kept **7 days** |

### Steps to get ZDR

1. Use an **invoiced billing account**, or get the abuse-monitoring exception approved.
2. **Switch off durable caching** (or all caching) for the project.
3. Set `store=false` in the Interactions API.
4. Don't enable request-response logging or Live API session resumption.
5. Don't use Grounding with Google Search or Maps; use Web Grounding for Enterprise instead.
6. Don't use Deep Research, CodeMender or the Sandbox API with default storage.
7. For **Claude on Vertex**, the abuse-monitoring exception reportedly doesn't apply. See `anthropic.md`.

## Contracting entity

The contracting entity depends on the customer's billing country:

| Customer's billing country | Contracting entity |
|---|---|
| Most of EMEA (Germany, Netherlands, Austria, Ireland, Switzerland, UK, …) | **Google Cloud EMEA Limited** |
| France | Google Cloud France SARL |
| Italy | Google Cloud Italy S.r.l. |
| Poland | Google Cloud Poland sp. z o.o. |

**Google Cloud EMEA Limited:** 70 Sir John Rogerson's Quay, Dublin 2, D02 R296, Ireland. Irish company registration no. 660412 (LEI 98450052CF14CFEB6435).

Check the header of our Google Cloud invoice (Billing → Documents) or the payments profile to confirm. The entity on the invoice is the one to list.

## Sub-processor list entry (draft)

| Field | Entry |
|---|---|
| Sub-processor | Google Cloud EMEA Limited |
| Address | 70 Sir John Rogerson's Quay, Dublin 2, D02 R296, Ireland |
| Service | Google Cloud Platform – Vertex AI (Gemini models); cloud hosting |
| Purpose | Hosting and AI model inference (processing of prompts and outputs) |
| Processing location | EU. **TODO: pick one**: "EU member states (Vertex AI `eu` multi-region)" or the pinned region, e.g. "Germany (europe-west3, Frankfurt)" |
| Data categories | **TODO**: what we actually send, e.g. user content and prompts, account data |
| Third-country transfers | Possible access by Google LLC (USA) and other Google affiliates or sub-processors for support and operations. Safeguards: SCCs ([published separately](https://cloud.google.com/terms/sccs), applied via the Cloud Data Processing Addendum, Appendix 3, §4.1). Google LLC is also listed under the EU-US Data Privacy Framework. The addendum itself doesn't name the DPF: it says the SCCs apply unless Google has adopted an "Alternative Transfer Solution". |
| Further sub-processors | https://cloud.google.com/terms/subprocessors |

## Notes

- List the **contracting entity**, not Google LLC. Google LLC belongs under third-country transfers.
- The Gemini API via **AI Studio** (not Vertex AI) and **Google Workspace** have their own terms and possibly a different entity. Check them separately if used.
- Google is a US-owned company, so the CLOUD Act caveat applies.
- **Sub-processor changes:** no subscription is needed. Google emails the notification address set in the Admin Console, the Order Form or the "Legal" category of Essential Contacts. It gives at least **30 days' notice** before a new sub-processor starts, and we have 90 days to object by terminating (Cloud Data Processing Addendum §11.4). Make sure that address is a monitored mailbox.

## Open items

- [ ] Confirm the contracting entity from our invoice or payments profile.
- [ ] Decide between the `eu` multi-region and a pinned single region; confirm the models we use support it.
- [ ] Verify the France, Italy and Poland exceptions directly on the Google contracting-entity page. It was truncated during research, so these come from search results.
- [ ] Confirm the transfer mechanism in our signed Cloud Data Processing Addendum.
- [ ] Confirm we have an invoiced billing account, or request the abuse-monitoring exception.
- [ ] Switch off durable caching (or all caching) for the projects that need ZDR.
- [ ] Verify the abuse-monitoring details (invoiced-billing rule, 30 days, partner models excluded) on Google's abuse monitoring page; they come from search results.

## Key documents

_Links checked: 2026-10-29._

| Document | Link | Status |
|---|---|---|
| Sub-processor list | [Google Cloud Platform Subprocessors](https://cloud.google.com/terms/subprocessors) | Public. Last modified 2026-10-19. |
| Privacy policy | [Google Cloud Privacy Notice](https://cloud.google.com/terms/cloud-privacy-notice) | Public. Effective 2026-10-15. |
| Terms of service | [Google Cloud Platform Terms of Service](https://cloud.google.com/terms); [Service Specific Terms](https://cloud.google.com/terms/service-terms) | Public. ToS last modified 2026-10-12; Service Specific Terms last modified 2026-10-27. |
| Data processing agreement (DPA) | [Cloud Data Processing Addendum](https://cloud.google.com/terms/data-processing-addendum) | Public. Last modified 2026-06-08. |
| Transfer safeguards (SCCs / DPF) | [Google Cloud Standard Contractual Clauses](https://cloud.google.com/terms/sccs); [DPF list – Google LLC](https://www.dataprivacyframework.gov/participant/5780) | SCCs public (applied via CDPA Appendix 3, Section 4); DPF page is JS-rendered (no content when fetched). |
| Security / trust center | [Trust Center](https://cloud.google.com/trust-center); [Compliance resource center](https://cloud.google.com/compliance) | Public. Audit reports via [Compliance Reports Manager](https://cloud.google.com/security/compliance/compliance-reports-manager). |
| Legal notice / imprint | [Google Contracting Entity](https://cloud.google.com/terms/google-entity) | No separate Google Cloud imprint found; this page lists the entities and addresses. Last modified 2026-06-30. |
| Sub-processor change notifications | [Subprocessors page FAQ](https://cloud.google.com/terms/subprocessors) | No subscription needed: Google emails the Notification Email Address (Admin Console, Order Form or Essential Contacts "Legal" category) at least 30 days ahead (CDPA 11.4). |

## Sources

- [Vertex AI – Data residency](https://docs.cloud.google.com/gemini-enterprise-agent-platform/resources/data-residency)
- [Vertex AI – Locations / endpoints](https://docs.cloud.google.com/gemini-enterprise-agent-platform/resources/locations)
- [Google Contracting Entity](https://cloud.google.com/terms/google-entity)
- [LEI record – Google Cloud EMEA Limited](https://lei.bloomberg.com/leis/view/98450052CF14CFEB6435)
- [Google Cloud sub-processors](https://cloud.google.com/terms/subprocessors)
- [Google – Vertex AI and zero data retention](https://docs.cloud.google.com/gemini-enterprise-agent-platform/resources/zero-data-retention)
- [Google – Abuse monitoring](https://docs.cloud.google.com/gemini-enterprise-agent-platform/models/abuse-monitoring)
- [Google Cloud – Privacy Notice](https://cloud.google.com/terms/cloud-privacy-notice)
- [Google Cloud – Platform Terms of Service](https://cloud.google.com/terms)
- [Google Cloud – Service Specific Terms](https://cloud.google.com/terms/service-terms)
- [Google Cloud – Cloud Data Processing Addendum](https://cloud.google.com/terms/data-processing-addendum)
- [Google Cloud – Standard Contractual Clauses](https://cloud.google.com/terms/sccs)
- [DPF list – Google LLC](https://www.dataprivacyframework.gov/participant/5780)
- [Google Cloud – Trust Center](https://cloud.google.com/trust-center)
- [Google Cloud – Compliance resource center](https://cloud.google.com/compliance)
- [Google Cloud – Compliance Reports Manager](https://cloud.google.com/security/compliance/compliance-reports-manager)
