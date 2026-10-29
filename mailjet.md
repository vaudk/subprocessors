# Mailjet (Sinch Email) – Data Residency & Sub-processor Notes

_Researched: 2026-10-28. Research notes, not legal advice; have DPO/legal review before use._

## Summary

- Mailjet's infrastructure is **EU only**, hosted on Google Cloud in **Germany (Frankfurt)** and **Belgium (Saint-Ghislain)**.
- There's no region to choose and no non-EU hosting option.
- **However**, some support and billing sub-processors can access data from outside the EU, including the **USA**.
- Mailjet is part of the Sinch group. Its sub-processors are published on Sinch's list.

## Hosting

| Sub-processor | Purpose | Location |
|---|---|---|
| Google Cloud France SARL | Cloud data centers (contacts, campaigns, sending, logs) | Germany (Frankfurt, europe-west3) and Belgium (Saint-Ghislain, europe-west1) |

Mailjet describes this as 2 regions and 6 data centers in Europe. Mailjet lists Google Cloud France SARL rather than the Irish entity because Mailjet is a French company. See `google-cloud.md`.

## Sub-processors that can access data outside the EU

From Sinch's sub-processor list, filtered to Mailjet:

| Sub-processor | Type | Purpose | Location |
|---|---|---|---|
| Mailjet SAS | Sinch entity | Support, project management, deployment, provisioning, managed services | France |
| **Mailgun Technologies, Inc.** | Sinch entity | Support, project management, deployment, provisioning, managed services | **USA** |
| Atlassian Corporation | Third party | Support tickets, incident management | USA & EU |
| Zendesk, Inc. | Third party | Customer support platform | USA, EU, APAC |
| Stripe Payments Europe, Ltd. | Third party | Billing and payments | USA, EU (Ireland) |

**Third-country transfers do happen.** Some third-party summaries say "no third-country transfer", but Sinch's own list contradicts that: Mailgun has US-based staff with operational access, and Atlassian, Zendesk and Stripe may process data in the US or APAC.

## Sub-processor list entry (draft)

| Field | Entry |
|---|---|
| Sub-processor | **TODO: verify from contract**. The Terms of Service say the contracting party is either Mailjet SAS (Paris) or Mailjet GmbH (Berlin). |
| Address | **TODO: take from contract or DPA** |
| Service | Mailjet email delivery (transactional and/or marketing) |
| Purpose | Sending emails; storing contacts and email logs |
| Processing location | EU: Germany and Belgium (Google Cloud) |
| Data categories | **TODO**: e.g. email addresses, names, email content, delivery and engagement events |
| Third-country transfers | Possible: support and operations by Mailgun Technologies, Inc. (USA); Atlassian, Zendesk, Stripe (USA/APAC). Safeguards: SCCs and UK Addendum, and the EU-US Data Privacy Framework (with UK Extension) for Sinch Email, per the Sinch DPA version 8, §12. Mailgun Technologies, Inc. is DPF-certified according to the privacy notice. |
| Further sub-processors | https://sinch.com/legal/data-protection-agreement-sub-processors/ |

## Notes

- Sinch gives **30 days' notice** before adding or replacing a sub-processor. Subscribe to those notices.
- **DPA:** Mailjet uses the Sinch group DPA (version 8, released 2025-10-29); mailjet.com/legal/dpa redirects there. Transfers are covered in §12 (SCCs, UK Addendum, EU-US DPF and UK Extension for Sinch Email).
- **Privacy notice:** this is a Sinch Email notice (updated 2026-09-29), and its "we" means Sinch AB (publ) or a subsidiary. It is not specific to Mailjet.
- Google (the hosting provider) is a US-owned company, so the CLOUD Act caveat applies.

## Open items

- [ ] Confirm the contracting entity and address from our Mailjet agreement. The Terms of Service allow either Mailjet SAS or Mailjet GmbH.
- [x] Confirm the transfer mechanism (SCCs/DPF) in the DPA. The Sinch DPA version 8, §12, provides SCCs, the UK Addendum and the EU-US DPF (with UK Extension) for Sinch Email.
- [ ] Verify the Frankfurt and Saint-Ghislain detail directly in Mailjet's help article. It returned 403 during research, so it comes from search results quoting it.
- [ ] Subscribe to Sinch sub-processor change notifications.

## Key documents

_Links checked: 2026-10-29._

| Document | Link | Status |
|---|---|---|
| Sub-processor list | [Sinch – Sub-processors](https://sinch.com/legal/data-protection-agreement-sub-processors/) | Public. Sinch group list; Mailjet entries are marked "Mailjet". Latest change notes dated 26 Oct 2026. |
| Privacy policy | [Mailjet – Privacy Notice](https://www.mailjet.com/legal/privacy-policy/) | Public. Sinch Email notice that covers Mailjet. Last updated 29 September 2026. |
| Terms of service | [Mailjet – Sinch Email Terms of Service](https://www.mailjet.com/legal/terms/) | Public. Covers Mailgun, Mailjet and Email on Acid. Last revised 2025-01-15. |
| Data processing agreement (DPA) | [Sinch – Data Processing Agreement](https://sinch.com/legal/terms-and-conditions/other-sinch-terms-conditions/data-processing-agreement/) | Public. Version 8, released 2025-10-29. Mailjet's own DPA link redirects here. |
| Transfer safeguards (SCCs / DPF) | [DPF – Mailgun Technologies, Inc.](https://www.dataprivacyframework.gov/participant/1738) | SCCs included in the DPA (§12 Data Transfers, Annex 1); §12 also names the EU-US DPF for Sinch Email. DPF page is JS-rendered (no content when fetched). |
| Security / trust center | [Mailjet – Data security & privacy](https://www.mailjet.com/products/data-security-and-privacy/); [Sinch Trust Center](https://trust.sinch.com/) | Public. Some Trust Center documents need "Get Access". |
| Legal notice / imprint | [Mailjet – Impressum](https://www.mailjet.com/de/rechtliches/impressum/) | Public, German only. Names Mailjet GmbH, Berlin (HRB 156505B); no English or French legal notice found. |
| Sub-processor change notifications | [Sinch – Sub-processors](https://sinch.com/legal/data-protection-agreement-sub-processors/) | Subscribe via the form at the bottom of the list page (DPA §13: 30 days' notice). The form did not load when fetched. |

## Sources

- [Sinch – Data Protection Agreement sub-processors](https://sinch.com/legal/data-protection-agreement-sub-processors/)
- [Mailjet – Where is my personal data stored?](https://documentation.mailjet.com/hc/en-us/articles/360042712274-Where-is-my-personal-data-stored)
- [Mailjet – Security & Privacy](https://www.mailjet.com/legal/security-privacy/)
- [Mailjet – Email infrastructure](https://www.mailjet.com/products/email-api/infrastructure/)
- [Mailjet – Privacy Notice](https://www.mailjet.com/legal/privacy-policy/)
- [Mailjet – Sinch Email Terms of Service](https://www.mailjet.com/legal/terms/)
- [Sinch – Data Processing Agreement](https://sinch.com/legal/terms-and-conditions/other-sinch-terms-conditions/data-processing-agreement/)
- [DPF – Mailgun Technologies, Inc.](https://www.dataprivacyframework.gov/participant/1738)
- [Mailjet – Data security & privacy](https://www.mailjet.com/products/data-security-and-privacy/)
- [Sinch – Trust Center](https://trust.sinch.com/)
- [Mailjet – Impressum](https://www.mailjet.com/de/rechtliches/impressum/)
