# Black Forest Labs (FLUX API) – Data Residency & Sub-processor Notes

_Researched: 2026-10-29. Research notes, not legal advice; have DPO/legal review before use._

## Summary

- Black Forest Labs (BFL) makes the FLUX image and video models. EU customers contract with **BFL GmbH** in Freiburg, Germany, under the **EU Developer Terms** and **EU API Service Terms** (both revised 2026-08-26). The group also has a US entity, Black Forest Labs Inc. (San Francisco).
- There is a **region choice for inference**: `api.eu.bfl.ai` is "multi-cluster routing limited to EU regions". The default `api.bfl.ai` routes across all clusters worldwide, and `api.us.bfl.ai` stays in the US.
- **Training on our data, unless we opt out or sign an enterprise agreement.** The self-serve terms give BFL a perpetual licence to use inputs and outputs "to train and improve its artificial intelligence models" (EU API Service Terms §2b). The privacy policy says the same. Opting out means emailing privacy@blackforestlabs.ai with the subject "Training Opt Out".
- **No public DPA.** The self-serve terms contain no data processing clauses at all. The privacy policy treats BFL as **controller** for API inputs, and says it "does not apply to content that we process on behalf of customers of our business offerings pursuant to an enterprise contract". The enterprise page says: "Security documentation, DPAs, and vendor assessment materials are available on request."
- **Zero data retention is an enterprise promise.** The enterprise page advertises "Zero data retention" for the managed API. Nothing in the self-serve terms says so.
- **Hosting is Microsoft Azure.** The trust center lists Azure as the cloud provider, and both API hostnames resolve to Azure Front Door. Microsoft is US-owned, so the CLOUD Act caveat applies.

## API endpoints

| Endpoint | Routing (per docs) | Notes |
|---|---|---|
| `api.bfl.ai` (global) | "Routes requests across all available clusters globally" | No location commitment. Don't use for EU-resident data. |
| `api.eu.bfl.ai` | "Multi-cluster routing limited to EU regions", "GDPR compliant" | No countries or Azure regions named. Resolves to Azure Front Door (`*.azurefd.net`), whose global edge terminates TLS at the nearest point of presence. |
| `api.us.bfl.ai` | "Multi-cluster routing limited to US regions" | US only. |

- Clients must poll the `polling_url` returned by the API. Result URLs are temporary; the API terms (§5) say outputs are only available through "a time-limited retrieval URL" and may be deleted after it expires. Third-party guides give 10 minutes.
- **The legal documents never mention the EU endpoint.** The EU routing commitment exists only in the documentation. **TODO: get it written into the contract or DPA.**

## Sub-processors

There is no published list outside the trust center. Its sub-processor page (trust.bfl.ai/subprocessors) loads only in a browser and printed empty. The trust center overview shows these entries, which may not be the full list:

| Sub-processor | Purpose (per trust center) | Location |
|---|---|---|
| Microsoft Azure | Cloud provider | Not stated (EU regions for `api.eu`, per docs) |
| Google Workspace | Identity provider | Not stated |
| GitHub | Version control | Not stated |
| Vanta | Continuous security and compliance monitoring | Not stated |

Also named in the terms or privacy policy:
- **Stripe**: payment processing ("We may use a third-party payment processor (such as Stripe)").
- **Black Forest Labs Inc. (USA)**: group affiliate. The privacy policy allows disclosure to "Our affiliates or others within our corporate group".
- Unnamed vendors for "system administration, cloud storage, security, customer relationship management, marketing communications, web analytics, content moderation, payment networks, and payment processing".

**TODO: get the full list (trust center in a browser, or request it with the DPA).**

## Transfers

- The privacy policy says data "may be transferred to, and stored at/processed in countries outside the country they were collected from". Transfers out of the EEA/UK use the EU SCCs or the UK addendum, unless the destination has an adequacy decision.
- **No country is named**, and the privacy policy doesn't mention the US. The US parent and US vendors (Stripe, GitHub, Google, Microsoft) make US transfers likely, at least for account and billing data.
- With `api.eu.bfl.ai`, inference itself should stay in the EU. Whether logs, moderation or training copies also stay there is not documented.

## Retention and training

| Topic | What BFL says |
|---|---|
| Training (self-serve) | Inputs, Tasks and Outputs are used "to train and improve our AI models after implementing appropriate technical safeguards like data minimisation and deidentification techniques" (privacy policy). The API terms give BFL a "perpetual, irrevocable, worldwide" licence to Input and Output for this. |
| Training opt-out | Email privacy@blackforestlabs.ai, subject "Training Opt Out", with account details. No dashboard toggle documented. |
| Retention (self-serve) | "As long as is reasonably necessary to provide our Service" and for disputes, security and legal obligations. No period given. |
| Zero data retention | Advertised on the enterprise page for the managed API and for dedicated instances. Not in the self-serve terms. |
| Generated results | Available only through a time-limited URL; BFL may delete them after it expires. |

**Practical point:** for any customer data, use either an enterprise agreement (DPA, zero retention, no training) or at least the written training opt-out. Under the self-serve terms, BFL acts as an independent controller that trains on what we send.

## Contracting entity

**BFL GmbH**, a German GmbH:
- Address: Ingeborg-Krummer-Schroth-Straße 18, 79106 Freiburg im Breisgau, Germany
- Commercial register: Amtsgericht München, HRB 291820
- VAT ID: DE419968449
- Managing directors: Robin Rombach, Andreas Blattmann, Axel Sauer
- Contacts: privacy@blackforestlabs.ai, DPO dpo@blackforestlabs.ai, legal@blackforestlabs.ai

The EU Developer Terms name BFL GmbH as the party for developers "located in the European Union" or using the services "through an establishment located in the European Union". The imprint gives the register details. US entity: Black Forest Labs Inc., 2261 Market Street STE 22997, San Francisco, CA 94114.

## Sub-processor list entry (draft)

| Field | Entry |
|---|---|
| Sub-processor | BFL GmbH (Black Forest Labs) |
| Address | Ingeborg-Krummer-Schroth-Straße 18, 79106 Freiburg im Breisgau, Germany (HRB 291820, AG München) |
| Service | FLUX API, EU endpoint `api.eu.bfl.ai` (**TODO: confirm we use it**) |
| Purpose | AI image/video generation (processing of prompts, reference images and outputs) |
| Processing location | EU (Azure, EU regions per docs); no country named |
| Data categories | **TODO**: prompts, uploaded images (may show people), generated outputs |
| Third-country transfers | Possible: US group entity (Black Forest Labs Inc.) and US vendors (Stripe, GitHub, Google, Microsoft), at least for account and billing data. Safeguards: EU SCCs / UK addendum per the privacy policy. **TODO: confirm in DPA** |
| Further sub-processors | https://trust.bfl.ai/subprocessors (browser only) |

**Only valid with a DPA.** Under the self-serve terms BFL is not our processor (see Summary).

## Notes

- **Biometric data and minors:** the EU Developer Terms ban uploading images or personal data of people under 18. Using FLUX for facial recognition or surveillance is also banned. Anyone processing biometric data must handle consents and notices.
- **Content credentials:** BFL may embed provenance data (Content Credentials) in any output.
- **Certifications:** the trust center lists ISO 27001:2022, a SOC 2 Type 2 report (dated 12.17.25) and a "GDPR Compliance Audit Report". All are behind "Request access".
- **Two sets of terms:** non-EU developers fall under the global Developer Terms and FLUX API Service Terms, which may name the US entity. Make sure we accept the EU versions.

## Open items

- [ ] Decide between an enterprise agreement (DPA, zero retention, no training) and self-serve plus training opt-out. Don't send customer data under self-serve terms without the opt-out.
- [ ] Request the DPA ("available on request") and check it names BFL GmbH, the EU endpoint, the sub-processors and the transfer mechanism.
- [ ] If we stay on self-serve: send the "Training Opt Out" email to privacy@blackforestlabs.ai and keep the confirmation.
- [ ] Get the full sub-processor list. trust.bfl.ai/subprocessors loads only in a browser.
- [ ] Ask which Azure regions back `api.eu.bfl.ai`, and whether logs, moderation and any training copies stay in the EU.
- [ ] Confirm all traffic goes to `api.eu.bfl.ai`, not the global endpoint.
- [ ] Ask how sub-processor changes are announced (nothing documented).
- [ ] Request the SOC 2 Type 2 report and ISO certificate from the trust center.

## Key documents

_Links checked: 2026-10-29._

| Document | Link | Status |
|---|---|---|
| Sub-processor list | [BFL Trust Center – Subprocessors](https://trust.bfl.ai/subprocessors) | Public, browser only (printed empty). The trust center overview shows 4 entries. |
| Privacy policy | [Black Forest Labs Privacy Policy](https://bfl.ai/legal/privacy-policy) | Public. Last revised 2026-08-01. |
| Terms of service | [EU Developer Terms of Service](https://bfl.ai/legal/eu-developer-terms-of-service); [EU API Service Terms](https://bfl.ai/legal/eu-api-service-terms) | Public. Both last revised 2026-08-26. |
| Data processing agreement (DPA) | n/a | **No public link.** "Security documentation, DPAs, and vendor assessment materials are available on request" ([enterprise page](https://bfl.ai/enterprise)). |
| Transfer safeguards (SCCs / DPF) | [Privacy Policy](https://bfl.ai/legal/privacy-policy) | **No public link** to SCCs. The privacy policy says EU SCCs / UK addendum are used. No DPF listing found. |
| Security / trust center | [BFL Trust Center](https://trust.bfl.ai/) | Public. ISO 27001 certificate, SOC 2 Type 2 report and GDPR audit report behind "Request access". |
| Legal notice / imprint | [Imprint (Impressum)](https://bfl.ai/legal/imprint) | Public. BFL GmbH, HRB 291820. |
| Sub-processor change notifications | n/a | **Not documented.** The trust center may have a subscribe option; not confirmed. |

## Sources

- [BFL – EU Developer Terms of Service](https://bfl.ai/legal/eu-developer-terms-of-service)
- [BFL – EU API Service Terms](https://bfl.ai/legal/eu-api-service-terms)
- [BFL – Developer Terms of Service (non-EU)](https://bfl.ai/legal/developer-terms-of-service)
- [BFL – FLUX API Service Terms (non-EU)](https://bfl.ai/legal/flux-api-service-terms)
- [BFL – Website and FLUX Terms of Service](https://bfl.ai/legal/terms-of-service)
- [BFL – Privacy Policy](https://bfl.ai/legal/privacy-policy)
- [BFL – Usage Policy](https://bfl.ai/legal/usage-policy)
- [BFL – Imprint](https://bfl.ai/legal/imprint)
- [BFL – Enterprise](https://bfl.ai/enterprise)
- [BFL Trust Center](https://trust.bfl.ai/) and [Subprocessors](https://trust.bfl.ai/subprocessors)
- [BFL Docs – FLUX API Integration Guide (endpoints)](https://docs.bfl.ai/api_integration/integration_guidelines)
