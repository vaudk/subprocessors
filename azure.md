# Microsoft Azure (incl. Azure OpenAI / Foundry) – Data Residency & Sub-processor Notes

_Researched: 2026-10-28. Research notes, not legal advice; have DPO/legal review before use._

## Summary

- Azure is **region-based**. The customer picks the region, and for regional services the data stays in that region's Azure geography.
- The **EU Data Boundary (EUDB)** covers the **EU and EFTA** (incl. **Norway and Switzerland**, not the UK). Regional Azure services in an EU/EFTA region are in scope. Microsoft names **limited transfers** out of the boundary (security, support, remote access, network transit, directory data).
- For Azure OpenAI and other Foundry models, the **deployment type** decides where inference runs:
  - **Global**: any Azure region worldwide.
  - **Data Zone EU**: within the EUDB.
  - **Standard / Regional Provisioned**: within the resource's Azure geography.
- Data at rest stays in the resource's geography for **all** deployment types.
- Microsoft is US-owned. The **CLOUD Act** caveat applies, and Microsoft France has publicly confirmed it (see Notes).

## European Azure regions

From Microsoft's regions list (Europe tab). "Restricted" means access only on request, e.g. for disaster recovery.

| Region (programmatic name) | Location | Status | In EUDB? |
|---|---|---|---|
| North Europe (`northeurope`) | Ireland | EU | Yes |
| West Europe (`westeurope`) | Netherlands | EU | Yes |
| Germany West Central (`germanywestcentral`) | Frankfurt, Germany | EU | Yes |
| Germany North (`germanynorth`), restricted | Berlin, Germany | EU | Yes |
| France Central (`francecentral`) | Paris, France | EU | Yes |
| France South (`francesouth`), restricted | Marseille, France | EU | Yes |
| Sweden Central (`swedencentral`) | Gävle, Sweden | EU | Yes |
| Italy North (`italynorth`) | Milan, Italy | EU | Yes |
| Spain Central (`spaincentral`) | Madrid, Spain | EU | Yes |
| Poland Central (`polandcentral`) | Warsaw, Poland | EU | Yes |
| Austria East (`austriaeast`) | Vienna, Austria | EU | Yes |
| Belgium Central (`belgiumcentral`) | Brussels, Belgium | EU | Yes |
| Denmark East (`denmarkeast`) | Copenhagen, Denmark | EU | Yes |
| Norway East (`norwayeast`) / Norway West (`norwaywest`), restricted | Norway (no city given) | EEA (EFTA, non-EU) | Yes |
| Switzerland North (`switzerlandnorth`) | Zurich, Switzerland | CH (EFTA, non-EEA) | Yes |
| Switzerland West (`switzerlandwest`), restricted | Geneva, Switzerland | CH (EFTA, non-EEA) | Yes |
| UK South (`uksouth`) / UK West (`ukwest`) | London / Cardiff, UK | UK | **No** |

- North Europe and West Europe share one Azure geography ("Europe"). A Standard deployment in either can be processed in **Ireland or the Netherlands**.
- The EUDB page also lists data centers in **Finland and Greece** that are "announced or currently operating". Neither has a public region in the regions list yet.

## Azure OpenAI / Foundry deployment types

| Deployment type | Inference (processing) | Data at rest |
|---|---|---|
| Global (Standard, Provisioned, Batch) | **Any Azure region worldwide**, incl. the US | Resource's geography |
| Data Zone (Standard, Provisioned, Batch) in an EU resource | Within the **EU Data Boundary**. Microsoft can add regions to the zone without notice. | Resource's geography |
| Standard / Regional Provisioned | Within the resource's **Azure geography**, possibly across regions in it | Resource's geography |
| Developer (fine-tune evaluation only) | Any region, **no residency guarantee** | n/a |

**Does Data Zone EU include Switzerland or Norway? Microsoft's own docs disagree:**

- The deployment-types page says the EU Data Zone "follows the Azure EU Data Boundary, which can include … EFTA countries … such as **Norway and Switzerland**".
- The data-privacy page says processing stays in "that or any other **European Union Member Nation**".

Microsoft doesn't publish a fixed member list for the EU Data Zone.

**GDPR view, if Norway or Switzerland are included:**
- **Norway** is EEA. GDPR applies there via the EEA Agreement, so it is **not a third country**: no SCCs, adequacy decision or transfer impact assessment needed. It only matters where we or our customers have promised "EU only"; say "EEA" instead.
- **Switzerland** is a third country with an EU adequacy decision (Art. 45 GDPR). No SCCs or transfer impact assessment, but it must be recorded as a third-country transfer in the records of processing and the privacy notice.
- See `openai.md` (GDPR implications section) for the full explanation.

To name one country (e.g. for a DPIA), use a **Standard** deployment in a single-region geography, such as Sweden Central or Germany West Central. Check model availability first, since new models launch Global first and regional last.

## Retention, ZDR, caching and abuse monitoring (Azure OpenAI / "Models sold by Azure")

Azure has no product called "ZDR". The equivalent is **modified abuse monitoring**, plus not using the stateful features.

| Topic | Azure OpenAI / Foundry Models sold by Azure |
|---|---|
| Training on our data | No. Prompts, completions, embeddings and training data are **not available to OpenAI** or other model providers, and not used to train foundation models without our permission. |
| Abuse monitoring (default) | Classifiers and pattern detection run on all traffic. Flagged prompts and completions are first reviewed **automatically** (including by LLMs, which don't store them). If needed, **human review** by authorized Microsoft employees, using secure workstations with just-in-time approval. For EEA deployments, reviewers are **located in the EEA**. The data used for human review is stored in the **resource's geography**, also for Global and Data Zone deployments. |
| Abuse-monitoring retention period | The current Microsoft pages don't state a duration. Older versions said **up to 30 days**. **TODO: verify.** |
| Modified abuse monitoring | Apply via Microsoft's form; requires meeting "Limited Access" eligibility criteria. Some advanced models have stricter criteria. If approved, **no storage and no human review**. Automated review at request time still runs, and severe or recurring abuse can lead to access restrictions or a request to turn human review back on. |
| Verifying it's off | The resource's JSON view (portal) or `az cognitiveservices account show` shows `"ContentLogging": "false"`. The attribute only appears when storage is off. |
| Stored data (until we delete it) | Files API, vector stores, **Responses API** (message history), **Assistants Threads**, **Stored completions** (preview), fine-tuning data and models, Batch inputs. All stored in the resource's geography, encrypted with AES-256 (customer-managed keys optional). |
| Batch | Batch is a **Global** deployment type. Data at rest stays in the geography, but **processing can happen in any geography** where the model is deployed. |
| Preview features | May have different privacy practices, including abuse monitoring |

### Prompt caching

| Aspect | Detail |
|---|---|
| Default | **On** for supported models (GPT-4o and newer). Requires at least 1,024 tokens with an identical first 1,024 tokens. |
| In-memory retention | Typically cleared after 5–10 minutes of inactivity, **always removed within 1 hour** of last use. **Not shared between Azure subscriptions.** Compatible with all data residency options. |
| Extended retention (`prompt_cache_retention: "24h"`) | Up to **24 hours**, with key/value tensors offloaded to **GPU-local storage**. Available for gpt-4.1 and gpt-5.x models; **on by default for gpt-5.5**. Data stays within the data zone (Data Zone deployments) or region (Regional deployments). |
| GPT-5.6 and later | `prompt_cache_options.ttl` sets a **minimum** lifetime of 30 minutes; the service "might retain it longer". The older `prompt_cache_retention` setting is deprecated for these models. |
| Turning it off | Only on GPT-5.6+ Standard deployments: set `prompt_cache_options.mode` to `explicit` and add no breakpoints. **Earlier models and PTU-M deployments can't turn caching off.** |

### Steps for minimal retention

1. Use a **Data Zone EU** or **Standard/Regional** deployment, not Global (and avoid Batch, which is Global).
2. Apply for **modified abuse monitoring**, then check that `ContentLogging` is `false`.
3. Don't use stateful features for personal data: Responses API storage, Assistants, Stored completions, Files and vector stores. Or delete the data promptly.
4. Avoid extended (24 h) prompt caching where it matters. Note that gpt-5.5 has it on by default.
5. Don't enable **Anthropic (Claude) models** in Foundry. They are processed by Anthropic, under Anthropic's retention rules, not Microsoft's (see `anthropic.md`).

## Exceptions – data that may leave the EU Data Boundary

| Exception | Detail |
|---|---|
| Remote access (DevOps) | Microsoft staff **outside** the EUDB may access EUDB data remotely, just-in-time and via secure workstations (SAW/VDI), with no data kept outside. Under GDPR this counts as a transfer. Customer Lockbox can require our approval. |
| Security operations | Pseudonymized log data, support data and, rarely, limited Customer Data are transferred for security purposes, "stored primarily in the **United States**", and accessible from any Azure region. MSRC uses a follow-the-sun model. |
| Support (Professional Services Data) | Case titles, cases escalated to engineering and support voicemails may be stored outside the EUDB. Consulting data is stored in the **US**. |
| Directory data | Limited Microsoft Entra data (usernames, email addresses) may be replicated outside the EUDB. |
| Network transit | Traffic may occasionally be routed outside the EUDB. |
| Service quality and billing | Pseudonymized IDs are consolidated globally for metrics and licence checks. |
| Global deployments | Inference anywhere in the world (see above). |
| Anthropic (Claude) models | If enabled, Customer Data is **processed by Anthropic in the US**. Storage stays in the EUDB. |
| Preview / free-trial services | Not covered by the EUDB. |
| Customer-initiated | E.g. our own users outside the EU, or connectors to third-party services. |

## Contracting entity

| Agreement | Contracting / billing entity (EEA) |
|---|---|
| Microsoft Customer Agreement (MCA), direct | **Microsoft Ireland Operations Limited (MIOL)** for EMEA. In "Limited Risk Distributor" (LRD) countries (incl. Germany, Austria, France, Belgium, Denmark, Finland, Italy, Netherlands, Norway, Sweden, Switzerland, Spain, UK), the **local Microsoft subsidiary** is the selling entity, and MIOL is the licensor. |
| Enterprise Agreement (EA) | Typically MIOL in EMEA. **TODO: verify on our signed enrollment.** |
| Cloud Solution Provider (CSP) | We buy from, and are billed by, the partner. We still accept the MCA directly with Microsoft, so Microsoft's DPA applies between Microsoft and us. List Microsoft as the processor. Also list the partner if it has access to data. |

**Microsoft Ireland Operations Limited:**

- Address: One Microsoft Place, South County Business Park, Leopardstown, Dublin 18, Ireland (D18 P521 per the DPA)
- Registered office: 70 Sir John Rogerson's Quay, Dublin 2, Ireland
- Irish company registration no. 256796, VAT IE 8256796 U (per Microsoft's own Terms of Sale page)

The DPA names MIOL's privacy representative at the Leopardstown address. Check our invoice ("Sold to" / billing entity) to confirm the entity.

## Sub-processor list entry (draft)

| Field | Entry |
|---|---|
| Sub-processor | Microsoft Ireland Operations Limited. **TODO: verify from contract/invoice** (local LRD subsidiary or EA enrollment may differ) |
| Address | One Microsoft Place, South County Business Park, Leopardstown, Dublin 18, D18 P521, Ireland |
| Service | Microsoft Azure; Azure OpenAI / Microsoft Foundry Models |
| Purpose | Cloud hosting and AI model inference (processing of prompts and outputs) |
| Processing location | **TODO: pick one**, e.g. "Sweden (Sweden Central, Standard deployment)" or "EU Data Boundary (EU/EFTA, Data Zone EU deployment)". Don't use Global deployments if location matters. |
| Data categories | **TODO**: what we actually send, e.g. user content and prompts, account data |
| Third-country transfers | Possible: remote access by Microsoft Corporation (USA) and affiliate staff worldwide; security operations data stored mainly in the US; support data; Entra directory data. Safeguards: 2021 SCCs (processor-to-processor, MIOL → Microsoft Corporation) in the Microsoft Products and Services DPA; Microsoft Corporation is certified under the EU-US DPF, Swiss-US DPF and the UK Extension. |
| Further sub-processors | Microsoft Online Services Subprocessors List: https://aka.ms/subprocessor (Microsoft's own short link; it resolves to Service Trust Portal page 115f1cb5-7f7b-4a5e-838d-df6d03c84dda) |

## Notes

- **Sub-processor notice:** the current DPA (last updated 2026-05-22) gives **6 months' notice** before a new sub-processor gets access to Customer Data, and **30 days** for other personal data. **New in 2026:** sub-processors that support AI features get only **30 days' notice**. The customer can switch such a sub-processor off until 6 months after the notice. Subscribe via "My Library" on the Service Trust Portal.
- **Transfers:** the May 2026 DPA keeps the 2021 SCCs (MIOL → Microsoft Corporation), the EU-US DPF, the Swiss-US DPF and the UK Extension. It also mentions the UK IDTA.
- **Azure OpenAI and OpenAI:** for Azure OpenAI, OpenAI is **not** a sub-processor. Microsoft hosts the models, and prompts are not available to OpenAI or used for training. This differs from Microsoft 365 Copilot, where Microsoft lists OpenAI as a sub-processor.
- **CLOUD Act:**
  - The DPA's Additional Safeguards Addendum commits Microsoft to redirect, notify and challenge government orders.
  - It does **not** rule out disclosure.
  - On 10 June 2025, Microsoft France's legal counsel told the French Senate under oath he "cannot guarantee" that data won't be handed to US authorities (per press reports).
- List **MIOL** (or the entity on our contract), not Microsoft Corporation. Microsoft Corporation belongs under third-country transfers.

## Open items

- [ ] Confirm the contracting entity from our Azure invoice or EA/MCA/CSP agreement, including whether an LRD subsidiary (e.g. Microsoft Deutschland GmbH) appears.
- [ ] Decide on the deployment type and region. Confirm the models we use are available as Standard or Data Zone there.
- [ ] Ask Microsoft in writing whether the **EU Data Zone** includes Switzerland and Norway. The two Microsoft pages contradict each other.
- [ ] Download and review the **Subprocessors List** from the Service Trust Portal. It is rendered by script and couldn't be read during research; a third-party summary page returned 403. Microsoft's links (aka.ms/subprocessor, go.microsoft.com/fwlink/?linkid=2096306) lead to page 115f1cb5-…, not the ea6e2b66-… page used earlier. Confirm which one is current.
- [x] Check the **current DPA (May 2026)**. Checked on 2026-10-29: the SCC/DPF wording is unchanged. It adds the 30-day notice for AI sub-processors and the UK IDTA (see Notes).
- [ ] Make sure Anthropic models are **not enabled** in Foundry, or document the US transfer.
- [ ] Consider Customer Lockbox and modified abuse monitoring.
- [ ] Apply for modified abuse monitoring if needed; after approval, check `ContentLogging` is `false` on each resource.
- [ ] Verify the abuse-monitoring retention period (older docs: up to 30 days; current pages don't say).
- [ ] Check whether we use gpt-5.5 (extended 24 h caching on by default) or Batch (Global processing).
- [ ] Verify registration no. 256796 directly in the Irish CRO register (taken from Microsoft's Terms of Sale page).

## Key documents

_Links checked: 2026-10-29._

| Document | Link | Status |
|---|---|---|
| Sub-processor list | [Microsoft Online Services Subprocessors List](https://aka.ms/subprocessor) | Public, JS-rendered (no content when fetched). Microsoft's short link resolves to a different Service Trust Portal page than the one under "Sub-processor list entry". |
| Privacy policy | [Microsoft Privacy Statement](https://www.microsoft.com/en-us/privacy/privacystatement) | Public. Last updated September 2026. For enterprise services, it defers to the Product Terms and DPA. |
| Terms of service | [Microsoft Product Terms](https://www.microsoft.com/licensing/terms/) and [Microsoft Customer Agreement](https://www.microsoft.com/licensing/docs/customeragreement) | Public. Product Terms show no date; the MCA page says "Last Update: March 1, 2023" and shows the agreement after picking country and language. |
| Data processing agreement (DPA) | [Microsoft Products and Services DPA](https://www.microsoft.com/licensing/docs/view/Microsoft-Products-and-Services-Data-Protection-Addendum-DPA) | Public. Current version "Last updated May 22, 2026" (.docx download). |
| Transfer safeguards (SCCs / DPF) | [DPF list – Microsoft Corporation](https://www.dataprivacyframework.gov/participant/6474) | DPF entry is public, JS-rendered (no content when fetched). SCCs (2021, processor-to-processor, MIOL → Microsoft Corporation) and UK IDTA are included in the DPA, section "Data Transfers". |
| Security / trust center | [Microsoft Trust Center](https://www.microsoft.com/en-us/trust-center) and [Service Trust Portal](https://servicetrust.microsoft.com/) | Public. The Service Trust Portal is JS-rendered; some documents need sign-in with an Entra account and acceptance of an NDA. |
| Legal notice / imprint | [Microsoft Deutschland GmbH – Impressum](https://www.microsoft.com/de-de/rechtliche-hinweise/impressum_de) | Public. It names Microsoft Deutschland GmbH, which says it is not a provider of Microsoft products or services; no imprint page for MIOL found (its details are on the Terms of Sale page). |
| Sub-processor change notifications | [Service Trust Portal – My Library](https://learn.microsoft.com/en-us/compliance/assurance/stp-get-started) | Save the list to "My Library" and set "Notification Settings" for email alerts. The May 2026 DPA gives 6 months' notice for Customer Data, 30 days for AI subprocessors (which can be disabled until 6 months after notice), and 30 days for other personal data. |

## Sources

- [What is the EU Data Boundary? (Microsoft Learn)](https://learn.microsoft.com/en-us/privacy/eudb/eu-data-boundary-learn)
- [Continuing data transfers for all EU Data Boundary Services](https://learn.microsoft.com/en-us/privacy/eudb/eu-data-boundary-transfers-for-all-services)
- [List of Azure regions](https://learn.microsoft.com/en-us/azure/reliability/regions-list)
- [Foundry Models – Deployment types](https://learn.microsoft.com/en-us/azure/foundry/foundry-models/concepts/deployment-types)
- [Data, privacy, and security for Foundry Models sold by Azure](https://learn.microsoft.com/en-us/azure/foundry/responsible-ai/openai/data-privacy)
- [Microsoft Products and Services DPA](https://www.microsoft.com/licensing/docs/view/Microsoft-Products-and-Services-Data-Protection-Addendum-DPA) (current version last updated 2026-05-22; the April 2025 PDF was first reviewed via a [third-party copy](https://www.alton.gov.uk/wp-content/uploads/2026/02/MicrosoftProductandServicesDPAWWEnglishApril2025CR.pdf))
- [Understand your MCA invoice – billing entities and LRD model](https://learn.microsoft.com/en-us/azure/cost-management-billing/understand/mca-understand-your-invoice)
- [Microsoft Terms of Sale (MIOL registration details)](https://www.microsoft.com/en-cy/store/b/terms-of-sale)
- [Microsoft Online Services Subprocessors List (Service Trust Portal)](https://aka.ms/subprocessor) (earlier link used: [ea6e2b66 page](https://servicetrust.microsoft.com/DocumentPage/ea6e2b66-933b-4b11-aafb-2225f36bfd73))
- [Microsoft Trust Center – Data access](https://www.microsoft.com/en-us/trust-center/privacy/data-access)
- [CSP – confirm customer acceptance of the MCA](https://learn.microsoft.com/en-us/partner-center/customers/confirm-customer-agreement)
- [The Register – Microsoft "cannot guarantee" data sovereignty](https://www.theregister.com/off-prem/2025/07/25/microsoft-exec-admits-it-cannot-guarantee-data-sovereignty/458553)
- [Microsoft – Abuse monitoring](https://learn.microsoft.com/en-us/azure/foundry/openai/concepts/abuse-monitoring)
- [Microsoft – Prompt caching with Azure OpenAI](https://learn.microsoft.com/en-us/azure/foundry/openai/how-to/prompt-caching)
- [Microsoft – Online Services Subprocessors List (aka.ms/subprocessor)](https://aka.ms/subprocessor)
- [Microsoft – Privacy Statement](https://www.microsoft.com/en-us/privacy/privacystatement)
- [Microsoft – Product Terms](https://www.microsoft.com/licensing/terms/)
- [Microsoft – Microsoft Customer Agreement](https://www.microsoft.com/licensing/docs/customeragreement)
- [Data Privacy Framework – Microsoft Corporation](https://www.dataprivacyframework.gov/participant/6474)
- [Microsoft – Trust Center](https://www.microsoft.com/en-us/trust-center)
- [Microsoft – Service Trust Portal](https://servicetrust.microsoft.com/)
- [Microsoft – Impressum (Microsoft Deutschland GmbH)](https://www.microsoft.com/de-de/rechtliche-hinweise/impressum_de)
- [Microsoft – Get started with the Service Trust Portal (My Library notifications)](https://learn.microsoft.com/en-us/compliance/assurance/stp-get-started)
