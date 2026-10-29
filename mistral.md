# Mistral AI (Studio / La Plateforme API) – Data Residency & Sub-processor Notes

_Researched: 2026-10-28. Research notes, not legal advice; have DPO/legal review before use._

## Summary

- Mistral AI is a **French company** (SAS, Paris) with no US parent. Mistral's help center says: "By default, your data is hosted in the European Union." Data is hosted in the US only if you choose the US API endpoint.
- There is a **region choice for inference**:
  - `api.mistral.ai`: global endpoint, with no stated inference location.
  - `api.eu.mistral.ai`: "multiple data centers in EU and EFTA countries".
  - `api.us.mistral.ai`: US.
  - Regional endpoints cost **1.1×** list price.
- Regional inference does **not** cover control-plane data: account configuration, API keys, billing, access management, usage analytics and other operational metadata.
- **Features can send data outside the EU** "temporarily" to the sub-processors listed in the Trust Center, such as web search (Brave) and image generation (Black Forest Labs). Enterprise customers can switch off some of these features for the whole organization.
- Hosting and inference run on **US-owned clouds** (Microsoft, Google, CoreWeave) as well as Mistral's own data center in France. The CLOUD Act caveat applies to the US-owned providers, though not to Mistral itself.

## API endpoints

| Endpoint | Inference location (per docs) | Notes |
|---|---|---|
| `api.mistral.ai` (global) | Not committed. The help center says data is hosted in the EU by default. | No guarantee about where each request is processed |
| `api.eu.mistral.ai` | "Multiple data centers in EU and EFTA countries" | No individual countries named. EFTA means Norway can be included. Agents, Batch and the Files API are **not** supported; function calling is the only supported tool. Not every model is available. |
| `api.us.mistral.ai` | "Multiple data centers in the United States" | Don't use it for EU-resident data |

**Recommendation:** Use `api.eu.mistral.ai` if we want a documented EU/EFTA processing commitment. The global endpoint relies only on the general "hosted in the EU by default" statement.

## Hosting / inference sub-processors

The official list (trust.mistral.ai/subprocessors) is rendered with JavaScript, and no content came back when fetched during research. The rows below come from **search-result summaries and third-party pages**. One of them (FinanceGadget) says it checked the list on 2026-08-01 and found 25 entries.

| Sub-processor (as reported) | Purpose | Location (as reported) |
|---|---|---|
| Mistral Compute (Mistral's own infrastructure) | Cloud infrastructure, all products | France. Press reports put it in Bruyères-le-Châtel, Essonne, operated by Eclairion. |
| Microsoft (entity name not verified) | Cloud infrastructure (Azure) | Sweden, Norway |
| Google (Google LLC, as reported) | Cloud infrastructure | Netherlands, Belgium; **United States for the US API endpoint** |
| CoreWeave Inc. | Inference provider, all SaaS products | EEA. A third-party analysis maps this to CoreWeave sites in Sweden, Norway, Denmark and Spain (not confirmed by Mistral). |

**Candidate countries for EU-hosted data:** France, Sweden, Norway (EEA, not EU), Netherlands, Belgium, and possibly Denmark and Spain via CoreWeave.

**GDPR view of "EU and EFTA":**
- **Norway, Iceland and Liechtenstein** are EEA. GDPR applies there via the EEA Agreement, so they are **not third countries**: no SCCs, adequacy decision or transfer impact assessment needed. They only matter where we or our customers have promised "EU only"; say "EEA" instead.
- **Switzerland** is also EFTA but **not EEA**. It is a third country with an EU adequacy decision (Art. 45 GDPR). Mistral doesn't say whether Swiss sites are used. If they are, Switzerland must be recorded as a third-country transfer in the records of processing and the privacy notice. **TODO: ask Mistral whether `api.eu` uses Swiss sites.**
- See `openai.md` (GDPR implications section) for the full explanation.

## Sub-processors / features with third-country access

| Sub-processor (as reported) | Purpose | Location |
|---|---|---|
| Brave | Web search feature | USA (at least partly) |
| Black Forest Labs | Image generation | USA (at least partly) |
| Stripe | Billing and payments | USA |
| Google (US) | US API endpoint only | USA |
| Others | The list reportedly has about 25 entries in total | **TODO: verify full list** |

Mistral's help center says transfers outside the EU use GDPR Art. 46 safeguards: SCCs wherever there is no adequacy decision. It also says some non-EU sub-processors use additional measures such as zero data retention or encryption. A plain chat-completions API call to the EU endpoint, with no web search or image tools, should not involve these feature sub-processors. **TODO: confirm with Mistral.**

## Retention and zero data retention (ZDR)

- **Default (Studio/API):** inputs and outputs are kept for **30 rolling days** for abuse monitoring, then deleted. This is a third-party summary of Mistral's policy; **TODO: verify in the privacy policy**.
- **ZDR:** available on **pay-as-you-go only**, for stateless endpoints: chat completions, FIM, embeddings, moderation/classification, OCR, and audio (speech generation and transcription). It does not cover Agents, Batch, Conversations, Libraries, the Files API, Vibe Work or Chat. You **request** it through support and must give a legitimate reason; Mistral approves or denies at its discretion. Once approved, the status shows under Admin → Privacy.
- **Training:**
  - Free mode in Studio may be used for training.
  - Pay-as-you-go customers "retain full control" and can opt out by turning off the "Anonymous improvement data" toggle (Admin → Privacy).
  - ZDR and the training opt-out are separate controls.
- **Abuse monitoring:** the 30-day retention was introduced in May 2024 "to monitor abuse" (per Open Terms Archive's record of the terms change). ZDR is the only way to turn it off.
- **Prompt caching:** **opt-in**, by setting the same `prompt_cache_key` on requests that share a prefix. Cached tokens are billed at 10% of the input price, in 64-token blocks; a cache hit isn't guaranteed. Mistral **doesn't document** how long cached data is kept, where it's stored, whether it's isolated per organization, or whether it's compatible with ZDR. **TODO: ask Mistral in writing.**
- **Retention even with ZDR:** not documented.

### Steps to get ZDR

1. Use pay-as-you-go.
2. Request ZDR through support (state a legitimate reason).
3. Turn off the training toggle separately.
4. Use only stateless endpoints: chat, embeddings, OCR, audio.
5. Don't use `prompt_cache_key` until Mistral confirms how caching works under ZDR.

## Contracting entity

**Mistral AI**, a French SAS (société par actions simplifiée):
- Address: 15 rue des Halles, 75001 Paris, France
- RCS Paris 952 418 325 (SIREN 952418325); active, created 2023-04-28
- Privacy contact: privacy@mistral.ai

The DPA (effective 2026-07-27) names this entity as the party, and the French company register (recherche-entreprises.api.gouv.fr) confirms the address.

**Name difference:** the official [legal notice](https://mistral.ai/legal/) names the company "Mistral", a SAS with share capital of EUR 15,000. It gives the same RCS number and address. The DPA says "Mistral AI". It is the same legal entity. In our records, use the name on our signed agreement.

## Sub-processor list entry (draft)

| Field | Entry |
|---|---|
| Sub-processor | Mistral AI SAS |
| Address | 15 rue des Halles, 75001 Paris, France (RCS Paris 952 418 325) |
| Service | Mistral AI Studio / La Plateforme API (**TODO: confirm we use `api.eu.mistral.ai`**) |
| Purpose | AI model inference (processing of prompts and outputs) |
| Processing location | EU/EFTA (Mistral EU endpoint). Reported hosting sites: France, Sweden, Norway, Netherlands, Belgium, and other EEA countries (CoreWeave). No specific country is committed. Norway is EEA, not a third country. If Swiss sites are used, Switzerland is a third country with an EU adequacy decision (Art. 45 GDPR). |
| Data categories | **TODO**: what we actually send, e.g. user content and prompts |
| Third-country transfers | None by design for plain inference on the EU endpoint. Possible: feature-dependent sub-processors in the USA (e.g. Brave for web search, Black Forest Labs for image generation) and Stripe for billing; control-plane data is not covered by regional inference. Infrastructure providers Microsoft, Google and CoreWeave are US-owned (CLOUD Act). Safeguards: Art. 46 GDPR / EU SCCs per Mistral's help center. **TODO: confirm in the DPA** |
| Further sub-processors | https://trust.mistral.ai/subprocessors |

## Notes

- **DPA transfer clause:** Section 8.2 applies SCC **Module 4 (processor-to-controller)** when the *customer* is in a restricted country. It does not set out the mechanism for onward transfers to sub-processors; the help center fills that gap (Art. 46 / SCCs).
- **Sub-processor changes:** Mistral gives "reasonable notice" before engaging a new sub-processor, and the customer can object in writing within **10 days**. You can subscribe to change notices with the bell icon on any Trust Center page. The email notices (DPA §7.1) cover only the "addition or replacement" of a sub-processor. According to Open Terms Archive, this replaced the earlier "any changes" wording. A new location for an existing sub-processor may therefore not trigger an email, so check the list itself from time to time.
- **After termination:** data becomes inaccessible 30 days after termination (DPA §10.1).
- **Le Chat:** renamed **Vibe** on 2026-08-12. Enterprise plans are opted out of training by default; Standard plans are opted in unless the user opts out. ZDR does not cover Vibe or Chat. If Vibe is used, assess it as a separate entry.

## Open items

- [ ] Get the full sub-processor list directly from trust.mistral.ai/subprocessors (it is JavaScript-rendered and returned no content during research), including exact entity names (e.g. which Microsoft entity) and countries.
- [ ] Confirm all production traffic goes to `api.eu.mistral.ai` and that the models we use are available there.
- [ ] Ask Mistral in writing which sub-processors take part in a plain chat-completions call on the EU endpoint, and where control-plane data is stored.
- [ ] Verify the 30-day default retention in the privacy policy (the page was truncated during research), and decide whether to request ZDR.
- [ ] Check that the "Anonymous improvement data" training toggle is off in Admin → Privacy.
- [ ] Confirm the transfer mechanism for onward transfers to sub-processors in the signed DPA.
- [ ] Subscribe to Trust Center sub-processor change notifications.
- [ ] Ask Mistral in writing how long and where prompt-cache data is kept, and whether caching is compatible with ZDR.
- [ ] Ask Mistral whether `api.eu.mistral.ai` uses sites in Switzerland (third country with adequacy decision; would need recording as a transfer).

## Key documents

_Links checked: 2026-10-29._

| Document | Link | Status |
|---|---|---|
| Sub-processor list | [Mistral AI Trust Center – Subprocessors](https://trust.mistral.ai/subprocessors) | Public, JS-rendered (no content when fetched); the DPA links here as the sub-processor list. |
| Privacy policy | [Mistral AI – Privacy Policy](https://legal.mistral.ai/terms/privacy-policy) | Public. Effective 2026-09-03. |
| Terms of service | [Mistral AI – Commercial Terms of Service](https://legal.mistral.ai/terms/commercial-terms-of-service) | Public. Effective 2026-09-25. These cover business use of Studio/API; consumer terms don't apply to us. |
| Data processing agreement (DPA) | [Mistral AI – Data Processing Addendum](https://legal.mistral.ai/terms/data-processing-addendum/) | Public. Effective 2026-07-27. |
| Transfer safeguards (SCCs / DPF) | [Mistral AI – Data Processing Addendum](https://legal.mistral.ai/terms/data-processing-addendum/) | Included in the DPA (§8: SCCs; §8.2 Module 4). No separate SCCs published; DPF does not apply (EU company). |
| Security / trust center | [Mistral AI Trust Center](https://trust.mistral.ai/) | Public, JS-rendered (Vanta; no content when fetched); the DPA (Exhibit 2) points here for the TOMs. |
| Legal notice / imprint | [Mistral – Legal notice](https://mistral.ai/legal/) | Public. Names "Mistral" SAS, RCS Paris 952 418 325, 15 rue des Halles, 75001 Paris. |
| Sub-processor change notifications | [Mistral Help – Who are your subprocessors?](https://help.mistral.ai/en/articles/455208-who-are-your-subprocessors-and-how-can-i-stay-updated-on-changes) | Bell icon on any Trust Center page; email notice of additions or replacements (DPA §7.1), 10 days to object (§7.2). |

## Sources

- [Mistral Docs – Regional inference](https://docs.mistral.ai/inference/regional-inference)
- [Mistral Docs – Zero data retention](https://docs.mistral.ai/admin/monitor-comply/zero-data-retention)
- [Mistral Help – Where do you store my data?](https://help.mistral.ai/en/articles/347629-where-do-you-store-my-data-or-my-organization-s-data)
- [Mistral Help – Who are your subprocessors?](https://help.mistral.ai/en/articles/455208-who-are-your-subprocessors-and-how-can-i-stay-updated-on-changes)
- [Mistral Help – Can I activate ZDR?](https://help.mistral.ai/en/articles/347612-can-i-activate-zero-data-retention-zdr)
- [Mistral Help – Do you use my data for training?](https://help.mistral.ai/en/articles/347617-do-you-use-my-user-data-to-train-your-artificial-intelligence-models)
- [Mistral Help – Le Chat is now Vibe](https://help.mistral.ai/en/articles/682992-le-chat-is-now-vibe)
- [Mistral AI – Data Processing Addendum](https://legal.mistral.ai/terms/data-processing-addendum/)
- [Mistral AI Trust Center – Subprocessors (official)](https://trust.mistral.ai/subprocessors)
- [Mistral AI Trust Center](https://trust.mistral.ai/)
- [Mistral AI – Privacy Policy](https://legal.mistral.ai/terms/privacy-policy)
- [Mistral AI – Commercial Terms of Service](https://legal.mistral.ai/terms/commercial-terms-of-service)
- [Mistral – Legal notice](https://mistral.ai/legal/)
- [Recherche d'entreprises (French government register API) – SIREN 952418325](https://recherche-entreprises.api.gouv.fr/search?q=952418325)
- [FinanceGadget – Mistral AI data governance (third party)](https://financegadget.com/providers/mistral/)
- [GitHub – ai-emissions-map PR #31 (third party, CoreWeave site mapping)](https://github.com/lspdrz/ai-emissions-map/pull/31)
- [Anarlog – Mistral data retention policy (third party)](https://anarlog.so/blog/mistral-data-retention-policy/)
- [Copilot at Work – Mistral sub-processors (third party)](https://copilotatwork.substack.com/p/mistral-reopens-the-side-door-anthropic)
- [L'Usine Digitale – Mistral AI data center with Eclairion](https://www.usine-digitale.fr/editorial/mistral-ai-va-ouvrir-son-premier-data-center-en-france.N2227191)
- [Mistral – Prompt caching](https://docs.mistral.ai/studio/conversations/advanced/prompt-caching)
- [Open Terms Archive – Mistral retains API prompts and outputs for 30 days](https://opentermsarchive.org/en/memos/mistral-ai-retains-api-prompts-and-outputs-for-30-days/)
