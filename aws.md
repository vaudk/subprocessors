# Amazon Web Services (incl. Amazon Bedrock) – Data Residency & Sub-processor Notes

_Researched: 2026-10-29. Research notes, not legal advice; have DPO/legal review before use._

## Summary

- EU customers contract with **Amazon Web Services EMEA SARL** in Luxembourg. It is a wholly owned subsidiary of Amazon.com, Inc. (USA), so the **CLOUD Act caveat applies**.
- **We choose the region.** The DPA (§12.1) says that once we pick a region, AWS "will not transfer Customer Data from Customer's selected Region(s)" except as needed to provide the services we started, or to comply with the law or a binding government order.
- The **DPA is public and part of the Service Terms**, so no signature is needed. EU SCCs are published as separate attachments (controller-to-processor and processor-to-processor), plus a supplementary addendum and Swiss and UK addenda. Amazon.com, Inc. and Amazon Web Services, Inc. are also certified under the EU-US Data Privacy Framework.
- **Sub-processors** are listed publicly, with 30 days' notice and email alerts for subscribers. The list is mostly AWS entities running each region. "Service improvement" processing in the USA and other countries applies only to the services named on the list. **Amazon Bedrock is not among them.**
- **Bedrock:**
  - No training: per the [Bedrock FAQ](https://aws.amazon.com/bedrock/faqs/), AWS and third-party model providers don't use inputs or outputs to train models.
  - Model providers have no access: Bedrock runs each provider's model in AWS-owned "Model Deployment Accounts".
  - **Exception for abuse detection:** for OpenAI models (except gpt-oss) and **Claude Fable 5 / 5.1**, inputs and outputs are stored for up to 30 days (see below).
- **EU-only Bedrock:** call an EU source region and use the `eu.` geographic cross-region inference profiles. The `global.` profiles route worldwide. See `anthropic.md` for the EU destination regions behind the Claude `eu.` profiles.

## Regions and Bedrock routing

| Option | Where data is processed | Notes |
|---|---|---|
| Single EU region (e.g. `eu-central-1` Frankfurt) | That region | DPA §12.1 commitment. Not every Bedrock model is offered in every region. |
| Bedrock **geographic** cross-region inference (`eu.` profile) | EU regions listed in the profile | "Data residency: Within geographic boundaries (such as US, EU, and APAC)". The destinations depend on the source region and model. Check with `GetInferenceProfile`. |
| Bedrock **global** cross-region inference (`global.` profile) | "Any supported AWS commercial Region worldwide" | Don't use it for EU-resident data. |
| Other AWS services with Bedrock-powered AI features | May use cross-region inference | Service Terms §1.24.2: these services "may use cross-region inference, using the optimal AWS Region". Check each service's docs. |

**AWS European Sovereign Cloud:** a separate AWS partition run by German-law entities and EU-resident staff. Its first region is in Brandenburg (`eusc-de-east-1`), generally available since January 2026 according to AWS. It has its own sub-processor section (section 4 of the list). This would only matter if we needed protection beyond what the normal EU regions give. Not researched further.

## Sub-processors

From the [AWS sub-processor page](https://aws.amazon.com/compliance/sub-processors/), retrieved 2026-10-29:

| Section | Who | Processing location | Relevance for us |
|---|---|---|---|
| 1. Infrastructure entities | One AWS entity per region (e.g. Amazon Data Services France SAS for Paris; an AWS entity in Germany for Frankfurt) | The customer's selected region | Always applies, for the regions we use |
| 2a. Application, media and networking services | AMCS LLC (USA), AMCS SG (Singapore) | Selected region and end-user location | Only for Chime, Connect, SES, SNS, Pinpoint and similar |
| 2b. Service improvement | "AWS development entities" (USA, India, Ireland, Germany, Netherlands, UK, etc.) | Varies per service: often USA, also India, Ireland, Germany and others | Only for the listed services (e.g. Rekognition, Textract, Transcribe, Comprehend, Translate, Lex, AI features of CloudWatch), and only unless we opt out. **Bedrock is not listed.** |
| 2c. Customer-initiated support | AWS entities worldwide, e.g. Amazon Web Services EMEA SARL (France, Germany, Ireland, Italy), Amazon Web Services, Inc. (USA), and entities in India, South Africa and Costa Rica | Worldwide | Only if we share customer data in a support case |
| 3. Third-party providers | Twilio, Infobip, Sinch, Vonage, Meta (WhatsApp), Esri, Grab and others | Various, incl. USA | Only for messaging (SMS/WhatsApp) and Amazon Location Service |
| 4. AWS European Sovereign Cloud | Separate list | EU | Only if we use that partition |

- **Opt out of service improvement** with an AWS Organizations "AI services opt-out policy" if we use any service from section 2b. **TODO: check which services we use.**
- **Support cases:** don't attach customer data to support cases, or accept that worldwide support entities may see it.

## Retention and training (Bedrock)

| Topic | What AWS says |
|---|---|
| Training | Per the Bedrock FAQ, AWS and third-party model providers don't use Bedrock inputs or outputs to train models. |
| Model provider access | None: providers "don't have access to Amazon Bedrock logs or to customer prompts and completions" ([data protection](https://docs.aws.amazon.com/bedrock/latest/userguide/data-protection.html)). |
| Default storage | Bedrock uses a "zero operator access (ZOA) data security model". Model invocation logging is off unless we turn it on (it then writes to our own CloudWatch or S3). |
| **Abuse detection: OpenAI models** (except gpt-oss and GPT OSS Safeguard) | Classifier-flagged traffic is kept **up to 30 days** for automated offline abuse detection. "Eligible customers may request full ZDR through their AWS account team." |
| **Abuse detection: Claude Fable 5 / 5.1** | **All traffic** is kept **up to 30 days**. Flagged traffic may get **human review by AWS**. Customers in the Enterprise Frontier Safeguards program get ZDR through 2026-12-31 only. |
| Where retained data sits | "Retained inputs and outputs are stored and processed by AWS and are not shared with third-party model providers." With cross-region inference, they are stored in the destination region. |
| CSAM | Bedrock may store and review flagged image inputs or outputs, and report to NCMEC or an authority. |
| After termination | Customer data can be returned or deleted up to 90 days after termination (DPA §14). |

## Transfers

- **DPA §12.1:** no transfer out of our selected regions, except to provide services we started or to comply with law or a binding government order.
- **SCCs (DPA §12.2):** apply to any transfer of GDPR data to a third country, directly or onward. Controller-to-processor when we are controller; processor-to-processor when we act as processor for our customers. Both are published as PDFs, and a supplementary addendum adds further measures.
- **Alternative mechanism (§12.3):** the SCCs don't apply if AWS adopts processor BCRs or another recognised mechanism. Amazon.com, Inc. and Amazon Web Services, Inc. are DPF-certified (EU-US, UK Extension, Swiss-US).
- **Government requests (DPA):** AWS will try to redirect a government demand to the customer, and will give the customer reasonable notice if compelled to disclose, unless legally prohibited.

## Contracting entity

**Amazon Web Services EMEA SARL** (Luxembourg):
- Address: 38 Avenue John F. Kennedy, L-1855 Luxembourg
- Registration number: B186284; VAT ID: LU 26888617
- Privacy contact: aws-EU-privacy@amazon.com (per a third-party directory; **TODO: confirm in the privacy notice**)

AWS's [contracting-party page](https://aws.amazon.com/legal/aws-emea/) says AWS Europe has sold to EMEA accounts since 1 July 2018. The [marketing-entities page](https://aws.amazon.com/legal/marketingentities) gives the address and registration numbers. The Customer Agreement (§12) picks the entity from the account's country. Its EU-specific clause softens the liability limits when the AWS party is AWS EMEA SARL and the account is in the EU.

## Sub-processor list entry (draft)

| Field | Entry |
|---|---|
| Sub-processor | Amazon Web Services EMEA SARL |
| Address | 38 Avenue John F. Kennedy, L-1855 Luxembourg (RCS B186284) |
| Service | AWS cloud infrastructure; Amazon Bedrock (**TODO: list the services we actually use**) |
| Purpose | Hosting; AI model inference (processing of prompts and outputs) |
| Processing location | EU: our selected AWS regions (**TODO: name them, e.g. eu-central-1 Frankfurt**); Bedrock `eu.` cross-region profiles stay in EU regions |
| Data categories | **TODO** |
| Third-country transfers | None by design for the selected EU regions (DPA §12.1). Possible: customer-initiated support (worldwide support entities), service improvement for listed services unless we opt out, and messaging or location third parties if used. Safeguards: EU SCCs (DPA §12.2) and the EU-US DPF (Amazon.com, Inc. / Amazon Web Services, Inc.). US parent: CLOUD Act. |
| Further sub-processors | https://aws.amazon.com/compliance/sub-processors/ |

## Notes

- **CLOUD Act:** AWS EMEA SARL is owned by Amazon.com, Inc., so the CLOUD Act caveat applies as for Microsoft and Google. The DPA's redirect-and-notify commitment softens this, but doesn't remove it.
- **Claude on Bedrock:** for Claude Fable 5 / 5.1, the 30-day storage of all traffic (with possible human review) is a material difference from older Claude models. Record it if we use Fable models on Bedrock.
- **Data privacy FAQ:** the FAQ page printed with its sections collapsed, so its answers aren't in the saved PDF.

## Open items

- [ ] List the AWS services and regions we actually use, and check none of them are in the "service improvement" section. If any are, set the AI services opt-out policy in AWS Organizations.
- [ ] Use only `eu.` inference profiles (never `global.`) for Bedrock, called from an EU source region. Consider an SCP that blocks `global.` profiles.
- [ ] If we use OpenAI models on Bedrock, ask the AWS account team about ZDR. If we use Claude Fable 5 / 5.1, check eligibility for the Enterprise Frontier Safeguards program, or record the 30-day retention.
- [ ] Subscribe to sub-processor change emails on the AWS sub-processor page.
- [ ] Check whether model invocation logging is enabled. If it is, make sure the logs stay in the EU and have a retention period.
- [ ] Confirm the contracting entity from our AWS invoice (AWS EMEA SARL).

## Key documents

_Links checked: 2026-10-29._

| Document | Link | Status |
|---|---|---|
| Sub-processor list | [AWS Sub-processors](https://aws.amazon.com/compliance/sub-processors/) | Public. Four sections, including the European Sovereign Cloud. |
| Privacy policy | [AWS Privacy Notice](https://aws.amazon.com/privacy/) | Public. Last updated 2026-05-18. |
| Terms of service | [AWS Customer Agreement](https://aws.amazon.com/agreement/); [AWS Service Terms](https://aws.amazon.com/service-terms/) | Public. Customer Agreement last updated 2026-08-14; Service Terms last updated 2026-10-22. |
| Data processing agreement (DPA) | [AWS Data Processing Addendum (PDF)](https://d1.awsstatic.com/legal/aws-dpa/aws-dpa.pdf) | Public; part of the Service Terms. The PDF shows no date (file last modified 2024-03-19). Older copies are still online at other URLs; this is the one the Service Terms link to. |
| Transfer safeguards (SCCs / DPF) | [Controller-to-processor SCCs](https://d1.awsstatic.com/legal/aws-dpa/controller-to-processor-sccs.pdf); [Processor-to-processor SCCs](https://d1.awsstatic.com/legal/aws-dpa/processor-to-processor-sccs.pdf); [Supplementary addendum](https://d1.awsstatic.com/legal/aws-dpa/supplementary-addendum-to-the-aws-dpa.pdf); [DPF – Amazon.com, Inc.](https://www.dataprivacyframework.gov/participant/5776) | Public. Swiss and UK addenda also published. |
| Security / trust center | [AWS Compliance](https://aws.amazon.com/compliance/); [AWS Artifact](https://aws.amazon.com/artifact/) | Public. Audit reports (SOC, ISO) are downloaded via AWS Artifact in the console. |
| Legal notice / imprint | [AWS Marketing Entities](https://aws.amazon.com/legal/marketingentities); [AWS Europe contracting party](https://aws.amazon.com/legal/aws-emea/) | No imprint page (aws.amazon.com/de/impressum redirects to the homepage). The marketing-entities page gives the AWS EMEA SARL address, RCS and VAT ID. |
| Sub-processor change notifications | [AWS Sub-processors](https://aws.amazon.com/compliance/sub-processors/) | At least 30 days' notice (DPA §6.1); email notice if you subscribe on the page. Objection means termination, stopping the service, or moving to another region. |

## Sources

- [AWS – Sub-processors](https://aws.amazon.com/compliance/sub-processors/)
- [AWS – Privacy Notice](https://aws.amazon.com/privacy/)
- [AWS – Customer Agreement](https://aws.amazon.com/agreement/)
- [AWS – Service Terms](https://aws.amazon.com/service-terms/)
- [AWS – Data Processing Addendum (PDF)](https://d1.awsstatic.com/legal/aws-dpa/aws-dpa.pdf)
- [AWS – Controller-to-processor SCCs (PDF)](https://d1.awsstatic.com/legal/aws-dpa/controller-to-processor-sccs.pdf)
- [AWS – Processor-to-processor SCCs (PDF)](https://d1.awsstatic.com/legal/aws-dpa/processor-to-processor-sccs.pdf)
- [AWS – Supplementary addendum to the DPA (PDF)](https://d1.awsstatic.com/legal/aws-dpa/supplementary-addendum-to-the-aws-dpa.pdf)
- [AWS – Swiss addendum (PDF)](https://d1.awsstatic.com/legal/aws-dpa/swiss-addendum-to-AWS-DPA.pdf) and [UK GDPR addendum (PDF)](https://d1.awsstatic.com/legal/aws-dpa/aws-uk-gdpr-dpa.pdf)
- [AWS – GDPR Center](https://aws.amazon.com/compliance/gdpr-center/)
- [AWS – Data Privacy FAQ](https://aws.amazon.com/compliance/data-privacy-faq/)
- [AWS – Compliance](https://aws.amazon.com/compliance/) and [AWS Artifact](https://aws.amazon.com/artifact/)
- [AWS – AWS Europe (contracting party)](https://aws.amazon.com/legal/aws-emea/)
- [AWS – Marketing entities contact info](https://aws.amazon.com/legal/marketingentities)
- [DPF – Amazon.com, Inc.](https://www.dataprivacyframework.gov/participant/5776)
- [Amazon Bedrock – Data protection](https://docs.aws.amazon.com/bedrock/latest/userguide/data-protection.html)
- [Amazon Bedrock – Abuse detection](https://docs.aws.amazon.com/bedrock/latest/userguide/abuse-detection.html)
- [Amazon Bedrock – Cross-Region inference](https://docs.aws.amazon.com/bedrock/latest/userguide/cross-region-inference.html)
- [Amazon Bedrock – FAQs](https://aws.amazon.com/bedrock/faqs/)
- [AWS News Blog – Opening the AWS European Sovereign Cloud](https://aws.amazon.com/blogs/aws/opening-the-aws-european-sovereign-cloud) (not fetched)
