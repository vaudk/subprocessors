# OpenAI – Data Residency & Sub-processor Notes

_Researched: 2026-10-28. Research notes, not legal advice; have DPO/legal review before use._

## Summary

- EU data residency ("Europe" region) covers **EEA + Switzerland**. OpenAI does **not** publish which specific country or data center serves a Europe-region project.
- Requests must go to `eu.api.openai.com`, in a project created with the Europe region (existing projects can't be switched).
- Requires approval from OpenAI sales, abuse-monitoring controls, and a Modified Retention amendment.

## What is covered

- **Regional storage** (data at rest) and **regional processing** (inference) for supported endpoints, including chat completions and embeddings.
- TLS termination and HTTPS decryption for `eu.api.openai.com` happen inside the region (Cloudflare Regional Services).

## Exceptions – data that may leave the EEA/CH

| Exception | Detail |
|---|---|
| System data | Account and operational data is not covered by data residency. It may be processed and stored outside the region. |
| Extended prompt caching | On endpoints without regional processing, content may be processed and temporarily stored outside the region. |
| Third-party services | Data sent to connected tools or MCP servers follows those services' own policies. |
| Customer/end-user location | Transfers caused by your own infrastructure or users being outside the region are not covered. |
| Legal jurisdiction | OpenAI is a US company, so data on EU servers can still be subject to US legal process (e.g. the CLOUD Act). |

## Possible processing countries (from the sub-processor list)

OpenAI's sub-processor list names the countries where each infrastructure provider **may** process data across **all** OpenAI regions worldwide. A listed country is a candidate, not a confirmed location for Europe-region projects.

| Provider | European countries listed | Eligible for the Europe region (EEA + CH) |
|---|---|---|
| Microsoft Azure (main provider) | France, Germany, Ireland, Italy, Netherlands, Norway, Poland, Spain, Sweden, Switzerland, UK (one mirror also lists Belgium, Denmark) | All except the UK |
| CoreWeave | Norway, Spain, Sweden, UK | Norway, Spain, Sweden |
| Oracle Cloud | Netherlands, UK | Netherlands |
| Google Cloud | Finland, Netherlands, Norway, UK | Finland, Netherlands, Norway |
| AWS, Cerebras | US / Canada only | None |
| Snowflake (data warehousing) | US only | None; probably handles system data |
| Cloudflare (CDN) | Data center closest to the end user | Regional Services keeps TLS in the EU for `eu.api.openai.com` |

**Candidate countries for EU-region data:** France, Germany, Ireland, Italy, Netherlands, Norway, Poland, Spain, Sweden, Switzerland, Finland, and possibly Belgium and Denmark. Microsoft Azure regions are the most likely, since Azure is OpenAI's main provider.

- The UK is **not** part of the Europe region. It has its own region (`gb.api.openai.com`), which offers storage only, not regional processing.
- One mirror of the list shows it was last updated on 2026-10-14.

## Retention, ZDR, caching and abuse monitoring

| Topic | OpenAI API |
|---|---|
| Training on our data | No by default (only if we opt in), since 1 March 2023 |
| Default abuse-monitoring retention | Prompts, outputs and derived metadata kept **up to 30 days**, for all endpoints |
| Turning abuse logging off | **Modified Abuse Monitoring (MAM)**: keeps content out of abuse logs (except image/file inputs in rare cases). **Zero Data Retention (ZDR)**: same, plus `store` is always treated as `false` for `/v1/responses` and `/v1/chat/completions`. Both need **prior approval** via sales. Configured per org/project under Settings → Organization → Data controls. |
| Not covered by ZDR (data stored until deleted) | Conversations, Assistants (kept 30 days after deletion), Threads, vector stores, Files, fine-tuning, Batch, Agents |
| Short-lived storage even with ZDR | Responses background mode (~10 min), Chat Completions audio output (1 h), Code Interpreter containers (until they expire), MCP/hosted container session state |
| Retention even with ZDR | Images/files flagged as possible CSAM are kept for manual review. OpenAI can withdraw ZDR/MAM and keep and review flagged content in "severe risk" cases. |
| Prompt caching | Automatic. **Extended caching** stores encrypted key/value tensors on GPU-local storage for **up to 24 h**. This counts as stored application state. In regions without regional processing it may be processed and stored **outside the selected region**. |
| Web search | Live web search is not HIPAA-eligible; offline/cache-only mode is |

### Steps to get ZDR

1. Apply for ZDR (or MAM) through sales.
2. Use only ZDR-eligible endpoints: Responses with `store=false`, or Chat Completions.
3. Don't use Assistants, Files, vector stores, Batch or fine-tuning for personal data.
4. Check how extended caching interacts with EU data residency for the endpoints we use.

## GDPR implications of "EEA + Switzerland" (instead of EU only)

Short version: legally almost the same as EU-only. EEA countries are GDPR territory and Switzerland has an adequacy decision. Switzerland still needs **documentation**, and the scope can conflict with "EU only" promises we make to **our own customers**.

| Group | Countries | GDPR status | What we need to do |
|---|---|---|---|
| EU | 27 member states | GDPR applies directly | Nothing extra |
| EEA, non-EU | Norway, Iceland, Liechtenstein | GDPR applies via the EEA Agreement. **Not third countries.** | Nothing extra; legally the same as the EU |
| Switzerland | Switzerland | **Third country** with an EU **adequacy decision** (2000/518/EC, confirmed in the Commission's January 2024 review) | Allowed under Art. 45 GDPR without SCCs or a transfer impact assessment, but must be documented |

### What Switzerland means in practice

1. **Documentation.** A transfer to Switzerland is a third-country transfer and must appear in:
   - **records of processing** (Art. 30(1)(e)): third country, with the adequacy decision as the legal basis
   - **privacy notice** (Art. 13(1)(f) / 14(1)(f)): transfers to third countries and that an adequacy decision covers them
   - **sub-processor list**: "EEA and Switzerland. Switzerland: third country with an EU adequacy decision (Art. 45 GDPR)."
2. **No SCCs or transfer impact assessment needed** for Switzerland. The adequacy decision covers the transfer. The US is different: it needs the Data Privacy Framework, or SCCs plus a transfer impact assessment.
3. **Revocation risk is low.** Adequacy decisions can be withdrawn, as Safe Harbor and Privacy Shield were. Switzerland's has stood since 2000, was reconfirmed in 2024, and Switzerland's revised data protection law (revDSG, in force since 2023) moved it closer to GDPR. If it were withdrawn, OpenAI would have to put SCCs in place.
4. **Onward transfers.** Data in Switzerland is under Swiss law; onward transfers to the US would fall under Swiss rules, such as the Swiss-US Data Privacy Framework. Of little practical relevance, because OpenAI's US access must be assessed directly anyway.

### The Swiss adequacy decision

An adequacy decision is a formal legal act of the European Commission, published in the EU's Official Journal. We don't sign or receive anything, and OpenAI doesn't have to supply anything. It is a general decision about a country that applies automatically, and we cite it in our documentation.

**The decision for Switzerland:** Commission Decision **2000/518/EC** of 26 July 2000, on the adequate protection of personal data provided in Switzerland. Its legal basis was the old Data Protection Directive 95/46/EC.
- Short (about three pages). The recitals give the reasoning: Swiss data protection law, constitution, supervisory authority, and ratification of the Council of Europe data protection convention.
- **Article 1:** Switzerland is considered to provide an adequate level of protection for personal data transferred from the EU. Further articles cover scope, suspension of transfers by national supervisory authorities, Commission monitoring, and addressees.
- **Still in force** under Art. 45(9) GDPR until it is amended or repealed.
- **Confirmed on 15 January 2024**, when the Commission reviewed 11 older adequacy decisions and kept all of them (report COM(2024) 7).
- (EUR-Lex didn't load during research; the structure is described from background knowledge.)

**Modern adequacy decisions under GDPR** (e.g. Japan, UK, South Korea, US Data Privacy Framework) are much longer:

| Part | Content |
|---|---|
| Title | "Commission Implementing Decision (EU) 20XX/NNN … pursuant to Regulation (EU) 2016/679 on the adequate level of protection of personal data by [country]" |
| Recitals (often 100–200+) | Detailed assessment of the country's law against EU standards: scope, principles, data subjects' rights, onward transfers, supervision, redress, and **government access** for law enforcement and national security |
| Article 1 | The adequacy finding, sometimes limited (US: only DPF-certified companies; Canada: only commercial organisations) |
| Further articles | Monitoring, suspension, review (**at least every 4 years**, Art. 45(3) GDPR) |
| Annexes | e.g. the country's commitments, government letters, additional safeguards |

Process: Commission draft → European Data Protection Board opinion → approval by a committee of member-state representatives → adoption → publication in the Official Journal. The EU Court of Justice can annul a decision (it did so twice for the US: Schrems I and II).

**Countries with an adequacy decision:** Andorra, Argentina, Canada (commercial organisations), Faroe Islands, Guernsey, Isle of Man, Israel, Japan, Jersey, New Zealand, South Korea, **Switzerland**, United Kingdom, Uruguay, **USA** (DPF-certified companies only). There may be recent additions (e.g. Brazil was in progress), so check the Commission's list.

**Wording for our documentation:**

- **Records of processing (Art. 30(1)(e)):**
  > Transfer to third country: Switzerland. Safeguard: adequacy decision of the European Commission (Decision 2000/518/EC; maintained following review, COM(2024) 7), Art. 45 GDPR.
- **Privacy notice (Art. 13(1)(f)):**
  > Some of our service providers may process personal data in Switzerland. The European Commission has determined that Switzerland ensures an adequate level of data protection (Art. 45 GDPR).
- **Sub-processor list:** "Switzerland: third country with an EU adequacy decision (Art. 45 GDPR)."

No SCCs and no transfer impact assessment are needed for Switzerland.

### Is Switzerland actually used, and for what?

| Question | Answer | Source |
|---|---|---|
| Does OpenAI say what it uses Switzerland for? | **No.** The docs only define the Europe region as "EEA + Switzerland", with no breakdown by country or purpose. | OpenAI API docs – Your data |
| Can Chat Completions be processed in Switzerland? | **Yes, it's allowed.** Chat Completions supports regional processing in the Europe region, which includes Switzerland. Nothing in the docs restricts any endpoint to the EEA only. | Same |
| Does OpenAI have infrastructure there? | Not its own. One copy of the sub-processor list names Switzerland as a processing country for **Microsoft (Azure)**; another copy doesn't show it. Azure has an AI region near Zurich (Switzerland North). | Registora mirror; official list not readable (403) |
| Is OpenAI otherwise present in Switzerland? | An **office in Zurich**, but it's a research team (multimodal models). No sign of support or operations work, or a data center. | heise, Greater Zurich Area |

**Our reading (inference, not confirmed):** including Switzerland mainly gives OpenAI flexibility to use Azure capacity there without breaking its residency commitment. It's also attractive for Swiss customers. Whether EU customers' requests actually end up there is not publicly known; it may be rarely or never. But OpenAI has **reserved the right**, and that's what counts for our documentation.

**What we do:**
1. **Treat Switzerland as possible** for everything in the Europe region, storage and processing, Chat Completions included. Document it as a third country with an EU adequacy decision.
2. **If we've promised a customer "EU only" or "EEA only"**, get written confirmation from OpenAI of which countries actually serve our project. Without it, we can't make that promise for OpenAI. Google Vertex AI `eu` explicitly excludes Switzerland.
3. Include the question in the open item for the OpenAI account rep.

### What Norway (and Iceland, Liechtenstein) means in practice

Norway is EEA, so it is **legally treated like an EU member state** for data protection. That puts it in a different position from Switzerland, which is a third country covered by an adequacy decision.

| Point | Norway |
|---|---|
| Does GDPR apply? | Yes. GDPR was taken into the EEA Agreement in July 2018, and Norway applies it through its 2018 Personal Data Act. |
| Third country? | **No.** Transfers to Norway are intra-EEA, so GDPR Chapter V doesn't apply. |
| SCCs, adequacy decision or transfer impact assessment? | **None needed** |
| Privacy notice (Art. 13(1)(f)) | No third-country transfer to disclose |
| Records of processing (Art. 30(1)(e)) | Not a third-country transfer. Still list the location, since the sub-processor list names countries. |
| Supervisory authority | Datatilsynet. It takes part in the EDPB (without a vote); the one-stop-shop and cooperation procedures apply. |
| Courts | EFTA Court, not the EU Court of Justice. In practice it follows the EU Court's case law. |

Where the EU/EEA difference can still matter:

1. **"EU only" commitments:** as with Switzerland, Norway technically breaks a promise worded "EU". Use "EEA".
2. **Other EU laws:** only GDPR applies automatically through the EEA. The AI Act, NIS2, the Data Act and DORA must each be taken into the EEA Agreement separately, which can lag behind the EU. Relevant if a customer requires compliance with a specific EU law or "EU sovereignty". Not relevant for plain data protection.
3. **Sector or public-sector requirements:** most say "EU/EEA", but some say "EU". Check the wording.

Norway is a realistic location for OpenAI's Europe region. Microsoft, CoreWeave and Google all list Norway on OpenAI's sub-processor list. OpenAI also announced its own "Stargate Norway" data center near Narvik in 2025 (not verified in this research). Whether it serves the Europe region isn't documented.

### Where it actually matters: our own commitments

- **Customer contracts and data processing agreements:** if we promise "data stays in the EU", Switzerland and even Norway technically break that promise. Check the wording; prefer "EEA and countries with an EU adequacy decision", or "EEA + Switzerland" specifically.
- **Tenders, the public sector, sector rules:** some customers or rules (public authorities, healthcare, some works councils) require EU only. Pure-EU alternatives: Google Vertex AI `eu`, Scaleway, Hetzner (Germany/Finland), Mailjet. Mistral `api.eu` is "EU and EFTA", so it may also include Switzerland or Norway.
- **Marketing claims:** don't say "EU-hosted"; say "hosted in Europe (EEA/CH)".

### Context: the bigger risk is the US

Compared with Switzerland, the significant issue with OpenAI is its US connection:
- **US parent company and the CLOUD Act:** US authorities can demand data even when it's stored in Europe.
- **System data:** not covered by data residency.
- **Support access and US sub-processors.**

Switzerland is a documentation task; the US needs a real assessment (Data Privacy Framework, or SCCs plus a transfer impact assessment).

### Scope compared with other providers

| Provider | Scope | Switzerland in scope? |
|---|---|---|
| OpenAI (Europe region) | EEA + Switzerland | Yes |
| Azure (EU Data Boundary) | EU + EFTA, including Norway and Switzerland | Yes; unclear whether the EU Data Zone includes Switzerland (see `azure.md`) |
| Google Vertex AI `eu` | EU member states only | **No**, explicitly excluded |
| Mistral `api.eu` | "EU and EFTA" | Possibly |

## Sub-processor list entry (draft)

| Field | Entry |
|---|---|
| Sub-processor | OpenAI Ireland Ltd (named for EEA and Swiss customers in the DPA and in Services Agreement §16.5) |
| Address | 1st Floor, The Liffey Trust Centre, 117-126 Sheriff Street Upper, Dublin 1, D01 YC43, Ireland |
| Service | OpenAI API (Europe data residency region) |
| Purpose | AI model inference (processing of prompts and outputs) |
| Processing location | European Economic Area (EU member states and Norway, Iceland, Liechtenstein) and Switzerland (OpenAI "Europe" region); no specific country disclosed. Norway, Iceland and Liechtenstein are EEA, not third countries. Switzerland: third country with an EU adequacy decision (Art. 45 GDPR). |
| Data categories | **TODO**: what we actually send, e.g. user content and prompts |
| Third-country transfers | Possible: system data (incl. US), support and operations by the US parent (OpenAI, L.L.C.) and US sub-processors. Safeguards: for EEA and Swiss data, DPA §4.1 says OpenAI Ireland Ltd makes onward transfers "on the basis of agreements containing SCCs ... or an adequacy decision". These are OpenAI's own agreements with its affiliates and sub-processors, not SCCs signed with us. Only UK data gets SCCs plus the UK Addendum incorporated into the DPA (§4.2). |
| Further sub-processors | https://platform.openai.com/subprocessors (the address the DPA uses; also at https://openai.com/policies/sub-processor-list/) |

## Open items

- [ ] Ask the OpenAI account rep in writing which provider and country host our Europe-region project (GDPR Art. 28), including whether Switzerland is used for storage or processing (e.g. Chat Completions) and for what.
- [x] Confirm the contracting entity and address. The DPA and Services Agreement (v.010126, §16.5) name OpenAI Ireland Ltd, Dublin, for EEA and Swiss customers.
- [ ] Verify the sub-processor list directly on openai.com. It returned 403 during research, so the country table above comes from third-party mirrors, which disagree on Belgium and Denmark.
- [ ] Subscribe to OpenAI sub-processor change notifications.
- [ ] Add Switzerland (third country, adequacy decision) to the records of processing and the privacy notice. Wording templates are in "The Swiss adequacy decision".
- [ ] Verify the Decision 2000/518/EC article structure on EUR-Lex (it didn't load during research).
- [ ] Check our customer contracts and DPAs for "EU only" wording that EEA + Switzerland would breach.
- [ ] Decide whether we need ZDR or MAM, and apply through OpenAI sales.
- [ ] Check that we don't send personal data to non-ZDR endpoints (Assistants, Files, vector stores, Batch, fine-tuning).

## Key documents

_Links checked: 2026-10-29._

| Document | Link | Status |
|---|---|---|
| Sub-processor list | [OpenAI – Sub-processor list](https://openai.com/policies/sub-processor-list/) | Public, but returns 403 to automated fetches; search results show last updated 2026-10-14, and the DPA points to [platform.openai.com/subprocessors](https://platform.openai.com/subprocessors) (also 403). |
| Privacy policy | [OpenAI – EU Privacy Policy](https://openai.com/policies/eu-privacy-policy/) | Public, but returns 403 to automated fetches; applies to EEA and Swiss users (OpenAI Ireland Ltd). |
| Terms of service | [OpenAI – Services Agreement](https://openai.com/policies/services-agreement/) ([PDF](https://cdn.openai.com/osa/openai-services-agreement.pdf)) | Public, v.010126, effective 2026-01-01, covers the API; the web page returns 403 to automated fetches, the PDF loads. |
| Data processing agreement (DPA) | [OpenAI – Data Processing Addendum](https://openai.com/policies/data-processing-addendum/) ([PDF](https://cdn.openai.com/pdf/openai-data-processing-addendum.pdf)) | Public, v.010126, effective 2026-01-01; the web page returns 403 to automated fetches, the PDF loads. |
| Transfer safeguards (SCCs / DPF) | Included in the DPA ([PDF](https://cdn.openai.com/pdf/openai-data-processing-addendum.pdf)), Section 4 | No separate SCC publication and no DPF listing found for OpenAI; Section 4.1 relies on SCCs or adequacy decisions for EEA/Swiss onward transfers, Section 4.2 incorporates SCCs + UK Addendum for UK data. |
| Security / trust center | [OpenAI – Trust Portal](https://trust.openai.com/) | Public (SafeBase); SOC 2 report and some documents need a request: "Request access to our SOC 2 Report". |
| Legal notice / imprint | [OpenAI – Services Agreement (PDF)](https://cdn.openai.com/osa/openai-services-agreement.pdf), Section 16.5 | No imprint page found; Section 16.5 names OpenAI Ireland Ltd, 1st Floor, The Liffey Trust Centre, 117-126 Sheriff Street Upper, Dublin 1, D01 YC43, Ireland. |
| Sub-processor change notifications | [OpenAI – Subscribe to New Subprocessors](https://openai.com/form/subscribe-to-new-sub-processors/) | Public form, returns 403 to automated fetches; DPA Section 2.9: notice "via email if Customer subscribes to email notifications on the Sub-Processor List site", 30 days to object. |

## Sources

- [OpenAI API docs – Your data](https://developers.openai.com/api/docs/guides/your-data)
- [Data residency for the OpenAI API (Help Center)](https://help.openai.com/en/articles/10503543-data-residency-for-the-openai-api)
- [Data residency and inference residency for ChatGPT](https://help.openai.com/en/articles/9903489-data-residency-and-inference-residency-for-chatgpt)
- [OpenAI Sub-processor list (official)](https://openai.com/policies/sub-processor-list/)
- [OpenAI – Sub-processor list (DPA address)](https://platform.openai.com/subprocessors)
- [OpenAI – Subscribe to New Subprocessors](https://openai.com/form/subscribe-to-new-sub-processors/)
- [OpenAI – EU Privacy Policy](https://openai.com/policies/eu-privacy-policy/)
- [OpenAI – Services Agreement](https://openai.com/policies/services-agreement/)
- [OpenAI – Services Agreement (PDF, v.010126)](https://cdn.openai.com/osa/openai-services-agreement.pdf)
- [OpenAI – Data Processing Addendum](https://openai.com/policies/data-processing-addendum/)
- [OpenAI – Data Processing Addendum (PDF, v.010126)](https://cdn.openai.com/pdf/openai-data-processing-addendum.pdf)
- [OpenAI – Trust Portal](https://trust.openai.com/)
- [Registora – OpenAI subprocessors (mirror)](https://registora.com/providers/openai)
- [Sub-processors.com – OpenAI (mirror)](https://sub-processors.com/company/openai.com)
- [CompanyScope – OpenAI](https://companyscope.io/vendors/openai)
- [Sovergate – OpenAI and GDPR compliance](https://sovergate.com/blog/openai-gdpr-compliance)
- [heise – OpenAI opens office in Switzerland](https://www.heise.de/en/news/OpenAI-opens-office-in-Switzerland-including-new-employees-10187690.html)
- [Greater Zurich Area – OpenAI sets up office in Zurich](https://www.greaterzuricharea.com/en/news/openai-sets-office-zurich)
- [EUR-Lex – Commission report on the first review of 11 adequacy decisions, COM(2024) 7](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:52024DC0007)
- [EUR-Lex – Commission Decision 2000/518/EC (Switzerland)](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32000D0518)
- [European Commission – Adequacy decisions (current list)](https://commission.europa.eu/law/law-topic/data-protection/international-dimension-data-protection/adequacy-decisions_en)
- [Swiss Federal Office of Justice – Adequacy of Switzerland by the EU](https://www.bj.admin.ch/en/adequacy-of-switzerland-by-the-eu)
- [IAPP – European Commission upholds 11 adequacy decisions](https://iapp.org/news/a/european-commission-upholds-11-adequacy-decisions)
