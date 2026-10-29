# Anthropic (Claude API) – Data Residency & Sub-processor Notes

_Researched: 2026-10-28. Research notes, not legal advice; have DPO/legal review before use._

## Summary

- The first-party Claude API (`api.anthropic.com`) has **no EU data residency option**. Data at rest is stored in the **US**. The only workspace geo is `"us"`.
- Inference location is set per request with `inference_geo`. The only values are `"global"` (default, may run "in any available geography") and `"us"` (US only, 1.1x price). **There is no `"eu"` value.**
- With `"global"`, Anthropic says it "may route customer traffic to select countries in the US, Europe, Asia and Australia". It does not name the countries.
- For EU-only processing, use Claude through **AWS Bedrock** (`eu.` inference profile) or **Google Cloud Vertex AI, Google managed** (`eu` multi-region; single EU regions only for Claude Sonnet 4.6 and earlier). There the cloud provider, not Anthropic, is the processor. The **Anthropic managed** Claude on Google Cloud does not give EU residency.
- EEA, Swiss and UK customers contract with **Anthropic Ireland, Limited**. Transfers rely on **SCCs**. Anthropic does **not** appear to be certified under the EU-US Data Privacy Framework (third-party sources only, see Open items).

## First-party Claude API – location controls

| Setting | Values | What it controls |
|---|---|---|
| `inference_geo` (per request, or workspace default) | `"global"` (default), `"us"` | Where model inference runs. Supported on Claude 4.6 and later models; older models return an error. |
| `allowed_inference_geos` / `default_inference_geo` (workspace) | `"global"`, `"us"` | Which geos a workspace may use, and the fallback. Lets an admin enforce US-only. |
| Workspace geo (set at creation, can't be changed) | `"us"` only | Where data is stored at rest, and where endpoint processing (e.g. image transcoding, code execution) happens. |

Anthropic's docs list as current limitations: "Only `"us"` and `"global"` are available" for inference geo, and "Only `"us"` is currently available" for workspace geo.

The same controls apply to **Claude Platform on AWS** and **Claude Models on Google Cloud (Anthropic managed)**. On these, Anthropic stays the processor and the workspace geo is fixed to `"us"`, so they do **not** give EU residency.

## Exceptions – data that leaves the EEA

| Exception | Detail |
|---|---|
| Storage at rest | Always US (workspace geo `"us"`). Covers stored data such as Files API, Batch API and code execution containers. |
| Global inference | `"global"` may route to "select countries in the US, Europe, Asia and Australia". No EU-only option. |
| Internal processing | Anthropic says safety reviews, support and incident response may happen in countries where Anthropic or its affiliates operate. |
| Sub-processors | Cloud providers (AWS, Google Cloud, Microsoft Azure) and other US vendors. See below. |
| Legal jurisdiction | Anthropic, PBC is a US company, so data can be subject to US legal process (e.g. the CLOUD Act). The DPA has no dedicated clause on handling government access requests. |

## Alternative: Claude via AWS Bedrock or Google Vertex AI (EU)

| Route | EU option | Countries data may be processed in |
|---|---|---|
| AWS Bedrock, `eu.anthropic.<model>` geo inference profile, called from an EU source region | EU geo cross-region inference | Germany (Frankfurt), Sweden (Stockholm), Italy (Milan), Spain, Ireland, France (Paris) |
| Same, called from **eu-west-2 (London)** or **eu-central-2 (Zurich)** | Adds the source region | Above plus **UK** or **Switzerland** |
| AWS Bedrock, `global.` profile | None | Any commercial AWS region worldwide. Don't use it. |
| Google Vertex AI (Google managed), `eu` multi-region | EU member states only (UK and CH excluded); 10% price premium over global | Not disclosed. See `google-cloud.md`. The only EU option for models newer than Claude Sonnet 4.6. |
| Google Vertex AI (Google managed), single region | europe-west1 (Belgium), europe-west4 (Netherlands) | Belgium or Netherlands. **Regional endpoints serve Claude Sonnet 4.6 and earlier models only** (per Anthropic docs, 2026-10-28). |

- The Bedrock destination regions above are from the Claude Sonnet 5.5 model card. AWS says geo profile destinations "will never change", but they can differ per source region and model. AWS advises calling `GetInferenceProfile` from each source region to check.
- **Bedrock abuse detection (checked 2026-10-29):** for **Claude Fable 5 / 5.1**, AWS keeps **all** inputs and outputs for up to 30 days, and flagged traffic may get human review by AWS. It is not shared with Anthropic. Older Claude models are not on that list. See `aws.md`.
- AWS warns that requests can be routed to opt-in regions even if not enabled, and prompts/outputs "may be stored in the opt-in Regions for abuse detection".
- Claude Sonnet 5.5 on Bedrock has **no** single-region ("In-Region") option in any region, only inference profiles, so a single country can't be pinned. Check the card of the model we use.
- On Bedrock and Vertex (Google managed), the processor is **AWS or Google**, not Anthropic (per Anthropic's docs). The sub-processor entry would then be the AWS or Google contracting entity. AWS and Google are also US-owned, so the CLOUD Act caveat still applies.

## Claude on Google Cloud – the two offerings

Google Cloud offers Claude in two ways that differ in who processes the data. For EU residency, only **Google managed** works.

| | **Google managed** (Claude in Vertex AI Model Garden) | **Anthropic managed** (Claude Platform on Google Cloud) |
|---|---|---|
| Who runs it | Google, as part of Vertex AI / Agent Platform | Anthropic runs its Claude API on Google infrastructure |
| **Data processor** | **Google**, under our existing Google Cloud agreement and Cloud Data Processing Addendum | **Anthropic**, under Anthropic's terms |
| Sub-processor list entry | Google Cloud EMEA Limited (existing Google entry, see `google-cloud.md`) | Anthropic Ireland, Limited (this file) |
| Location | `global`, **`eu` multi-region**, or a single region | `global` only; workspace geo `"us"`, so storage is in the US |
| Billing | Google Cloud, as Agent Platform usage | Google Cloud Marketplace |
| Retention / ZDR | **Google's** data governance applies | Anthropic's (see Notes) |
| Features | Messages API, prompt caching, extended thinking, tool use (incl. web search), citations, structured outputs | Full Claude API, new features on release day |
| How to recognise it | Host `aiplatform.*.googleapis.com`, path `.../publishers/anthropic/models/{model}:rawPredict`; SDK client `AnthropicVertex` | Host `claude.googleapis.com`; SDK client `AnthropicGoogleCloud`; IAM roles `roles/claude.*` |

### Endpoints (Google managed)

| Endpoint | Host | Where requests go | Price |
|---|---|---|---|
| `global` | `aiplatform.googleapis.com` | Any region with capacity, including outside the EU | Base |
| **`eu`** | `aiplatform.eu.rep.googleapis.com` | Any region in **EU member states** (UK and CH excluded) | +10% |
| Regional (e.g. `europe-west1` Belgium, `europe-west4` Netherlands) | `{region}-aiplatform.googleapis.com` | Only that region | +10% |

- **Regional endpoints serve Claude Sonnet 4.6 and earlier models only.** For newer models (Opus 4.7+, Sonnet 5/5.5, Haiku 5.5, Fable), `eu` is the only EU option, so a single country can't be pinned.
- The 10% premium applies to Claude Sonnet 4.5 and later models. Provisioned throughput needs a regional endpoint.
- Which models are offered where: Model Garden, or Google's "Anthropic Claude models on Agent Platform" page.

### Features not available (Google managed)

- Files API, and URL sources for images and documents
- Message Batches API, Models API, Admin API, Compliance API, Usage and Cost API
- Code execution, web fetch, advisor
- Agent Skills, MCP connector, programmatic tool calling
- Claude Managed Agents
- Server-side `fallbacks` (use client-side fallback)
- Requests are limited to **30 MB**, which can be reached before the 1M-token context window.

### ZDR and abuse monitoring (Google managed)

- Google's data governance applies, per Anthropic's docs. See `google-cloud.md` for Google's ZDR steps.
- **Prompt caching** runs on Google infrastructure. Google doesn't document whether its project-level "disable durable caching" setting covers partner models. **TODO: confirm with Google.**
- **Abuse monitoring:** per search results, Google's exception to prompt logging covers only Google's own models (Gemini), **not** partner models like Claude. Not confirmed on Google's abuse monitoring page. **TODO: get written confirmation from Google.**
- Anthropic recommends turning on Agent Platform request-response logging and keeping at least 30 days of logs. Per Anthropic, this doesn't give Google or Anthropic access to the content. It is still stored data, so weigh it against ZDR goals.

### Recommended setup for EU residency

1. Use **Google managed** with the **`eu`** endpoint, not `global`.
2. Use the `AnthropicVertex` SDK client with `region="eu"`.
3. Check that the chosen model is offered in `eu`.
4. Don't add a separate Anthropic row to the sub-processor list. Use the Google Cloud EMEA Limited entry, with processing location "EU member states (Vertex AI `eu` multi-region)".
5. Get the caching and abuse-monitoring points confirmed by Google in writing.

## Retention, ZDR, caching and abuse monitoring

Applies where **Anthropic is the processor**: the Claude API, Claude Platform on AWS, **Claude in Microsoft Foundry**, and Claude Models on Google Cloud (Anthropic managed). On Bedrock and Google managed Vertex, the cloud provider's rules apply instead.

| Topic | Claude API |
|---|---|
| Training on our data | No, without express permission |
| Default retention | **Inconsistent sources.** The API docs say prompts and outputs are "not retained by default", except for Covered Models (30 days). The Privacy Center says inputs and outputs are deleted **within 30 days**. Treat 30 days as the documented maximum. **TODO: confirm with Anthropic.** |
| Covered Models | Claude **Fable 5.1, Mythos 5.1, Fable 5, Mythos 5** require **30-day retention** and aren't available under ZDR unless Anthropic expressly authorizes it. With ZDR, retention can be switched on for a single workspace (not the Default Workspace). |
| Flagged content | Even with ZDR: inputs and outputs flagged by automated trust and safety systems kept **up to 2 years**. Trust and safety scores up to **7 years** (Privacy Center). Also retained under legal holds. |
| Getting ZDR | On request via Anthropic sales. Enabled **per organization**; each new organization needs separate enablement. Also available for Claude Platform on AWS and Anthropic managed Claude on Google Cloud. |
| ZDR covers | Messages and Token Counting APIs, for eligible features: thinking, tool use, citations, PDF support, web search, `inference_geo`, fast mode, and client-side tools (bash, text editor, computer use, memory) |
| ZDR does **not** cover | **Batch** (29-day retention), **Files API** (until deleted), **code execution** and programmatic tool calling (containers up to 30 days), Agent Skills, **MCP connector**, MCP tunnels, user profiles, **Managed Agents** (until deleted), the **Console** (incl. playground), Claude for Work interfaces, third-party integrations. CORS isn't supported under ZDR. |
| Qualified ZDR features | **Structured outputs** / strict tool use: the JSON schema (not prompts or outputs) cached up to **24 h** after last use. **Cache diagnostics**: hashes and token counts kept briefly. |
| ZDR is not enforced | The API does **not** block non-eligible features under ZDR. Using one steps outside ZDR for that data. |

### Prompt caching

| Aspect | Detail |
|---|---|
| Default | **Opt-in** with `cache_control` (automatic at the top level, or explicit breakpoints on blocks) |
| Lifetime | **5 minutes** (default) or **1 hour** (`"ttl": "1h"`, costs 2x the input price) |
| What's held | KV cache representations and cryptographic hashes, **in memory** only; prompts and outputs are not stored. Deleted promptly after the TTL. |
| ZDR | **Eligible** |
| Isolation | By organization, and also by **workspace** on the Claude API, Claude Platform on AWS and Microsoft Foundry. On Bedrock and Google managed Vertex, **organization level only**. |

### Steps for ZDR

1. Request ZDR from Anthropic sales, for each organization.
2. Use only eligible features: Messages API, without Batch, Files, code execution, MCP connector or Skills.
3. Don't use Covered Models (Fable/Mythos 5.x) in ZDR workspaces.
4. Don't use the Console with personal data.
5. Be aware that flagged content can still be kept for up to 2 years.

## Contracting entity

Anthropic's Commercial Terms (effective 17 June 2025): "'Anthropic' means Anthropic Ireland, Limited if Customer resides in the European Economic Area ('EEA'), Switzerland or UK, and Anthropic, PBC if Customer resides anywhere else." The DPA makes "the Anthropic entity that executed the Agreement" the data importer.

| Entity | Address | Role |
|---|---|---|
| **Anthropic Ireland, Limited** | 6th Floor, South Bank House, Barrow Street, Dublin 4, D04 TR29, Ireland | Contracting entity for EEA, CH and UK customers. Irish CRO no. **760497**, incorporated 22 March 2024 (from third-party register sites, not verified on CRO). |
| Anthropic, PBC | 548 Market St, PMB 90375, San Francisco, CA 94104, USA | US parent; operates the service and infrastructure |

Addresses are from Anthropic's Privacy Policy (effective 10 September 2026). DPO: dpo@anthropic.com.

Third-party sites report that some Claude invoices still name Anthropic, PBC as the seller. Check our invoice and signed terms.

## Sub-processors

The official list is at `trust.anthropic.com/subprocessors` (the DPA links `anthropic.com/subprocessors`, which redirects there). The page renders client-side and could not be read during research. Third-party information:

| Sub-processor | Purpose | Source |
|---|---|---|
| Amazon Web Services, Google Cloud Platform, Microsoft Azure | Cloud infrastructure, all products, worldwide | CompanyScope (checked 2026-10-04); Registora lists GCP and Azure only |
| Cloudflare | Network / CDN | CompanyScope |
| Stripe, WorkOS | Payments; authentication/SSO | CompanyScope, Registora |
| Intercom, Twilio, Sentry | Support, messaging, error monitoring | CompanyScope |
| Boldr, Nutun | Purpose not found (**TODO**) | Registora; search results |
| ElevenLabs, Brave Search, TurboPuffer | Text to speech; web search | Search results (Simon Willison blog, trust center news) |

CompanyScope counts **20** sub-processors and notes that all three clouds can process data for all products. Locations per sub-processor were not available.

## Sub-processor list entry (draft)

| Field | Entry |
|---|---|
| Sub-processor | Anthropic Ireland, Limited (**TODO: verify from signed terms/invoice**) |
| Address | 6th Floor, South Bank House, Barrow Street, Dublin 4, D04 TR29, Ireland |
| Service | Claude API (Claude Developer Platform) |
| Purpose | AI model inference (processing of prompts and outputs) |
| Processing location | USA (storage at rest). Inference: USA with `inference_geo: "us"`; otherwise "select countries in the US, Europe, Asia and Australia". **TODO: decide on `"us"` vs `"global"`.** No EU-only option. |
| Data categories | **TODO**: what we actually send, e.g. user content and prompts, account data |
| Third-country transfers | **Yes, by design**: storage and processing in the USA by Anthropic, PBC; global routing to Asia/Australia possible; US cloud and other sub-processors. Safeguards: EU SCCs (Module 2/3), UK Addendum and Swiss Addendum in the Anthropic DPA. No DPF certification found. A transfer impact assessment is needed. |
| Further sub-processors | https://trust.anthropic.com/subprocessors |

## Notes

- **Retention, ZDR and caching:** see the section above.
- **Sub-processor changes:** "reasonable notice" before a new sub-processor, with **15 days** to object (DPA). Subscribe to the Trust Center news feed.
- **Claude for Work (Team/Enterprise):** Same entity and DPA. Not ZDR-eligible (except Claude Code on Enterprise). No EU residency found either.

## Open items

- [ ] Confirm the contracting entity from our signed terms and invoice (Anthropic Ireland, Limited vs Anthropic, PBC).
- [ ] Verify CRO no. 760497 on the Irish CRO register (core.cro.ie). SoloCheck returned 403; the number comes from search results and third-party registers.
- [ ] Read the official sub-processor list on trust.anthropic.com (didn't render during research) and record entity, purpose and location for each. The table above is from third-party sites that disagree on the count (5 vs 20).
- [ ] Confirm Anthropic's DPF status on dataprivacyframework.gov. Only third parties say "not certified"; the registry didn't render during research.
- [x] Check the DPA effective date. Anthropic's DPA page still shows 24 February 2025 (checked 2026-10-29). The 10 September 2026 date CompanyScope gives is the Privacy Policy's effective date, so CompanyScope probably confused the two documents.
- [ ] Decide: first-party API (US/global, TIA required) vs Bedrock `eu.` profile or Vertex `eu` (EU only, AWS/Google as processor). Confirm the chosen model is available there.
- [ ] If Vertex AI: verify the Claude rows in Google's data residency table for our model; they were read via a summary.
- [ ] If Vertex AI: check our code doesn't use features missing from Google managed (Files, Batch, code execution, MCP connector, etc.).
- [ ] If Vertex AI: get written confirmation from Google on (a) whether the abuse-monitoring exception covers Claude and (b) whether disabling caching covers partner models.
- [ ] If ZDR is needed: request it from Anthropic in writing.
- [ ] Clarify the default retention with Anthropic: API docs say "not retained by default", the Privacy Center says "deleted within 30 days".
- [ ] If ZDR: check our code doesn't use non-eligible features (Batch, Files, code execution, MCP connector, Skills) or Covered Models (Fable/Mythos 5.x).

## Key documents

_Links checked: 2026-10-29._

| Document | Link | Status |
|---|---|---|
| Sub-processor list | [Anthropic Trust Center – Subprocessors](https://trust.anthropic.com/subprocessors) | Public, JS-rendered (no content when fetched). `anthropic.com/subprocessors` (linked in the DPA) redirects here. |
| Privacy policy | [Anthropic Privacy Policy](https://www.anthropic.com/legal/privacy) | Public. Effective 2026-09-10. Says it does not apply to content processed for business customers. |
| Terms of service | [Anthropic Commercial Terms of Service](https://www.anthropic.com/legal/commercial-terms) | Public. Effective 2025-06-17. Govern API use. |
| Data processing agreement (DPA) | [Anthropic Data Processing Addendum](https://www.anthropic.com/legal/data-processing-addendum) | Public. Effective 2025-02-24. |
| Transfer safeguards (SCCs / DPF) | [Anthropic Data Processing Addendum](https://www.anthropic.com/legal/data-processing-addendum); [DPF participant list](https://www.dataprivacyframework.gov/list) | Included in the DPA: SCCs Module 2/3 (Section I), UK and Swiss Addenda (Schedule 3). No DPF listing found; the registry is JS-rendered. |
| Security / trust center | [Anthropic Trust Center](https://trust.anthropic.com/) | Public, JS-rendered (no content when fetched). |
| Legal notice / imprint | [Anthropic Privacy Policy](https://www.anthropic.com/legal/privacy) | No imprint page found. Entity names and addresses are in the Privacy Policy. |
| Sub-processor change notifications | [Anthropic Trust Center – Updates](https://trust.anthropic.com/updates) | Privacy Center: "please subscribe to our Trust Center News Feed". JS-rendered. DPA gives "reasonable notice" and 15 days to object. |

## Sources

- [Claude Docs – Data residency](https://platform.claude.com/docs/en/manage-claude/data-residency)
- [Claude Docs – API and data retention](https://platform.claude.com/docs/en/manage-claude/api-and-data-retention)
- [Anthropic Privacy Center – Where are your servers located?](https://privacy.claude.com/en/articles/7996890-where-are-your-servers-located-do-you-host-your-models-on-eu-servers)
- [Anthropic Privacy Center – How long do you store my organization's data?](https://privacy.claude.com/en/articles/7996866-how-long-do-you-store-my-organization-s-data)
- [Anthropic Commercial Terms of Service](https://www.anthropic.com/legal/commercial-terms)
- [Anthropic Data Processing Addendum](https://www.anthropic.com/legal/data-processing-addendum)
- [Anthropic Privacy Policy](https://www.anthropic.com/legal/privacy)
- [Anthropic Trust Center – Subprocessors (official)](https://trust.anthropic.com/subprocessors)
- [AWS Bedrock – Claude Sonnet 5.5 model card (EU geo profile)](https://docs.aws.amazon.com/bedrock/latest/userguide/model-card-anthropic-claude-sonnet-5-5.html)
- [AWS Bedrock – Supported regions for inference profiles](https://docs.aws.amazon.com/bedrock/latest/userguide/inference-profiles-support.html)
- [Google Vertex AI – Data residency](https://docs.cloud.google.com/gemini-enterprise-agent-platform/resources/data-residency)
- [Claude Docs – Claude Models on Google Cloud (overview and comparison)](https://platform.claude.com/docs/en/build-with-claude/claude-models-on-google-cloud/overview)
- [Claude Docs – Claude Models on Google Cloud (Google managed)](https://platform.claude.com/docs/en/build-with-claude/claude-models-on-google-cloud/google-managed)
- [Google – Anthropic Claude models on Agent Platform](https://docs.cloud.google.com/gemini-enterprise-agent-platform/models/partner-models/claude)
- [Google – Vertex AI and zero data retention](https://docs.cloud.google.com/gemini-enterprise-agent-platform/resources/zero-data-retention)
- [Google – Abuse monitoring](https://docs.cloud.google.com/gemini-enterprise-agent-platform/models/abuse-monitoring)
- [Claude Platform is now available on Google Cloud](https://claude.com/resources/articles/claude-models-on-google-cloud-anthropic-managed)
- [CompanyScope – Anthropic](https://companyscope.io/vendors/anthropic)
- [Registora – Anthropic subprocessors (mirror)](https://registora.com/providers/anthropic)
- [SoloCheck – Anthropic Ireland, Limited (CRO 760497)](https://www.solocheck.ie/Irish-Company/Anthropic-Ireland-Limited-760497)
- [Simon Willison – Brave Search added as a subprocessor](https://simonwillison.net/2025/Mar/21/anthropic-use-brave/)
- [Claude Docs – Prompt caching](https://platform.claude.com/docs/en/build-with-claude/prompt-caching)
- [Anthropic Trust Center](https://trust.anthropic.com/)
- [Anthropic Trust Center – Updates (news feed)](https://trust.anthropic.com/updates)
- [Data Privacy Framework – Participant list](https://www.dataprivacyframework.gov/list)
