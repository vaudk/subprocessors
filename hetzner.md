# Hetzner – Data Residency & Sub-processor Notes

_Researched: 2026-10-28. Research notes, not legal advice; have DPO/legal review before use._

## Summary

- Hetzner Online GmbH is a German, privately owned company. It is the **contracting party and processor for every location**, including the US and Singapore ones.
- **The customer picks the location** for each server. Hetzner's own data center parks are in **Germany (Nuremberg, Falkenstein) and Finland (Helsinki)**, all inside the EU.
- **Ashburn, Hillsboro (USA) and Singapore** are cloud-only locations in **rented colocation space**. Hetzner subsidiaries and third-party colocation providers run them.
- Hetzner says that if you choose an EU location, server data is processed only in the EU. It also says support for **all** locations is provided from the EU, and customer master data (billing, contract) stays in the EU.
- Data only leaves the EU if we deploy servers in a US or Singapore location ourselves.

## Locations

| Location (Cloud code) | City / country | In the EU? | Data center | Run by |
|---|---|---|---|---|
| nbg1 | Nuremberg, Germany | Yes | Hetzner-owned data center park | Hetzner Online GmbH |
| fsn1 | Falkenstein (Vogtland), Germany | Yes | Hetzner-owned data center park | Hetzner Online GmbH |
| hel1 | "Helsinki", Finland (the park is in Tuusula, about 20 km north) | Yes | Hetzner-owned data center park | Hetzner Finland Oy (building rental, technical support) |
| ash | Ashburn, Virginia, USA | **No** | Colocation | Hetzner US LLC + colocation provider |
| hil | Hillsboro, Oregon, USA | **No** | Colocation | Hetzner US LLC + colocation provider |
| sin | Singapore | **No** | Colocation | Hetzner Singapore Pte. Ltd. + NTT Global Data Centers SG1 Pte Ltd |

- Hetzner Cloud groups these into network zones: `eu-central` (nbg1, fsn1, hel1), `us-east` (ash), `us-west` (hil) and `ap-southeast` (sin).
- **Non-cloud products** (dedicated servers, managed servers, webhosting, Storage Box and so on) are processed only in the EU, according to Hetzner's data-protection FAQ.
- Hetzner's own docs say: "Hetzner owns and operates three data center parks located in Nuremberg (Germany), Falkenstein (Germany) and Helsinki (Finland). We do not have our own data center parks in the USA or in Singapore."
- Certifications: ISO/IEC 27001:2022 for all data center parks and infrastructure. The DPA's TOMs annex also lists § 8a BSI-KritisV and a BSI C5 Type 2 attestation.

## Official sub-processor list (DPA Annex 3)

Source: "Anlage 3 zum AV-Vertrag – Genehmigte Subunternehmer", version 1.0, dated 06.08.2024. I read this PDF directly.

| Server location | Sub-processor | Address | Service |
|---|---|---|---|
| Finland | Hetzner Finland Oy | Huurrekuja 10, 04360 Tuusula, Finland | Building rental; technical support |
| USA | Hetzner US LLC | 1500 Broadway, 19th Fl, New York, NY 10036, USA | Server rental at the US locations |
| USA | NTT Global Data Centers Americas, Inc. | 1625 National Drive, Sacramento, CA 95834, USA | Colocation provider, US locations |
| USA | QTS Investment Properties Hillsboro, LLC | 12851 Foster Street, Overland Park, KS 66213, USA | Colocation provider, US locations |
| Singapore | Hetzner Singapore Pte. Ltd. | 1 Scotts Road, #21-10 Shaw Centre, Singapore 228208 | Server rental at the Singapore location |
| Singapore | NTT Global Data Centers SG1 Pte Ltd | 8 Kallang Avenue #15-01/09, Aperia, 339509 Singapore | Colocation provider, Singapore location |

- The annex does not say which US provider serves which site. QTS is presumably Hillsboro, going by its entity name, and NTT presumably Ashburn. This is **not verified**.
- The annex is organised by server location. It implies that the US and Singapore sub-processors are only involved if we use those locations.
- **Scope caveat:** DPA § 7(1) excludes "Nebenleistungen" from the sub-processor definition, for example telecommunications, postal and transport services, maintenance and user service. Tools such as a ticketing system or a payment provider may therefore be missing from the list.

## Third-country access and exceptions

| Topic | Detail |
|---|---|
| Customer-chosen non-EU location | Only "the data stored on your cloud server" is transferred to the USA or Singapore. Hetzner does not pass customer master data to Hetzner US LLC or Hetzner Singapore Pte. Ltd. |
| Support | "Support access is generally provided by our teams within the EU." Annex 3 says support for all server locations happens in the EU. |
| Authority requests | For EU locations, Hetzner accepts requests and court orders only from German authorities (Germany) or Finnish authorities (Helsinki). Hetzner says US and Singapore authorities have no direct access to server data in the EU. |
| Legal jurisdiction | Hetzner is German, not US-owned, so the CLOUD Act concern is much smaller than with US hyperscalers. Two caveats remain: data in Ashburn or Hillsboro is fully subject to US law, and Hetzner owns a US subsidiary. Whether US authorities could compel the German parent through it has not been assessed. |
| Shared responsibility | For cloud and dedicated servers, the customer is responsible for OS-level security, encryption at rest and backups (DPA Annex 2, TOMs). |

## Transfer mechanism

- DPA § 3(1): processing takes place "ausschließlich" (only) in an EU or EEA state.
- § 3(2)–(4): any third-country transfer needs the controller's prior consent and must meet Art. 44 ff. GDPR. Hetzner must state the country and the safeguard in advance. The DPA lists the possible safeguards (adequacy decision, BCRs, SCCs, codes of conduct, certification) but **does not say which one applies** to the US and Singapore locations.
- A third-party summary says Hetzner offers DPAs supplemented by EU SCCs for non-EU products. I could not confirm this on a Hetzner page.
- **Hetzner's own answer is consent, not SCCs.** Its [data protection FAQ](https://docs.hetzner.com/general/company-and-policy/data-protection-at-hetzner/) (last changed 2026-07-20) says: "The transfer of your server data from the EU to the third country is based on your express consent, which you gave when selecting the product location." This is the Art. 49(1)(a) GDPR derogation. It is meant for occasional transfers, not ongoing hosting, and it is weak where the data belongs to our customers. This is another reason to stay on EU locations.
- **If we use only EU locations, there is no third-country transfer to document.**

## Contracting entity

**Hetzner Online GmbH**, Industriestraße 25, 91710 Gunzenhausen, Germany.

- Handelsregister: Amtsgericht (Registriergericht) Ansbach, **HRB 6089**
- VAT ID: DE812871812
- Managing directors: Martin Hetzner, Stephan Konvickova, Günther Müller
- Data protection officer: data-protection@hetzner.com, +49 9831 505-216

Sources for these details: Hetzner's legal notice and the footer of the DPA template. Hetzner Online GmbH stays the contracting partner even for the US and Singapore locations.

The DPA (AV-Vertrag, template version 1.1, dated 10.02.2025) is concluded online at https://accounts.hetzner.com/account/dpa. Acceptance is by checkbox; there is no individual negotiation. It is governed by German law, with Gunzenhausen as the place of jurisdiction.

## Sub-processor list entry (draft)

| Field | Entry |
|---|---|
| Sub-processor | Hetzner Online GmbH |
| Address | Industriestraße 25, 91710 Gunzenhausen, Germany (AG Ansbach, HRB 6089) |
| Service | Hetzner Cloud / dedicated servers. **TODO: name the products we actually use** |
| Purpose | Infrastructure hosting (compute, storage, network) |
| Processing location | **TODO: pick our locations**, e.g. "Germany (Nuremberg nbg1 / Falkenstein fsn1)" and/or "Finland (Helsinki hel1, via Hetzner Finland Oy)". EU only if no ash, hil or sin resources exist. |
| Data categories | **TODO**: everything stored on or processed by our servers, e.g. user content, account data, logs |
| Third-country transfers | None if only EU locations are used (support and master data stay in the EU, per Hetzner). If US or Singapore locations are used: Hetzner US LLC with NTT and QTS (USA), or Hetzner Singapore Pte. Ltd. with NTT SG1 (Singapore). Hetzner's FAQ names the customer's consent when choosing the location (Art. 49 derogation), not SCCs. **TODO: if we ever use these locations, ask Hetzner for SCCs.** |
| Further sub-processors | https://www.hetzner.com/AV/subunternehmer.pdf |

## Notes

- If we use **only German locations**, Hetzner Finland Oy is presumably not involved either, because Annex 3 ties it to the Finland server location. This is worth confirming.
- Hetzner gives advance notice of new or replaced sub-processors. We have **14 days to object** on data-protection grounds, with a special termination right if no solution is found (DPA § 7(3)). Silence counts as consent.
- Hetzner provides the annual TOM audit results in the customer account under the DPA section (DPA § 8(3)).
- The current TOMs annex is version 1.2 (2026-02-17). It is newer than the DPA body (version 1.1, 2025-02-10).
- The sub-processor annex is dated 06.08.2024. The Singapore location is already listed, so it looks current. Check it again when signing.

## Open items

- [ ] Check that our Hetzner projects have no resources in ash, hil or sin. Consider enforcing EU-only locations (e.g. in Terraform or by policy).
- [ ] Conclude or confirm the DPA in the Hetzner account (accounts.hetzner.com/account/dpa) and archive the signed version, including Annex 1 (data categories and data subjects).
- [ ] Ask Hetzner (data-protection@hetzner.com) for SCCs for the US and Singapore locations. Its FAQ relies on consent given when choosing the location, which is not enough for ongoing hosting. Only needed if we use those locations.
- [ ] Confirm which colocation provider (NTT or QTS) serves Ashburn and which serves Hillsboro.
- [ ] Confirm whether the § 7(1) "Nebenleistungen" exclusion means some ancillary service providers (support tooling, payment) are not listed. Check whether any of them access customer data outside the EU.
- [ ] Optionally verify HRB 6089 in the official Handelsregister (handelsregister.de). It is currently taken from Hetzner's own legal notice and DPA footer.

## Key documents

_Links checked: 2026-10-29._

| Document | Link | Status |
|---|---|---|
| Sub-processor list | [Appendix 3 of the DPA: Approved Subcontractors (PDF)](https://www.hetzner.com/AV/subcontractors.pdf) | Public. Version 1.0, dated August 08, 2024; German version at https://www.hetzner.com/AV/subunternehmer.pdf. |
| Privacy policy | [Privacy policy](https://www.hetzner.com/legal/privacy-policy/) | Public. Status: September 14, 2026. |
| Terms of service | [Terms and Conditions](https://www.hetzner.com/legal/terms-and-conditions/) | Public. Version 2.0.0, last updated 27 October 2021; apply to all business relationships with Hetzner Online GmbH. |
| Data processing agreement (DPA) | [Data Processing Agreement template (PDF)](https://www.hetzner.com/AV/DPA_en.pdf) | Public template, version 1.1, February 10, 2025; concluded in the customer account at accounts.hetzner.com/account/dpa. |
| Transfer safeguards (SCCs / DPF) | [DPA § 3 (PDF)](https://www.hetzner.com/AV/DPA_en.pdf); [Data protection FAQ](https://docs.hetzner.com/general/company-and-policy/data-protection-at-hetzner/) | No separate SCCs or DPF listing found; the FAQ says US/Singapore transfers rest on the customer's "express consent, which you gave when selecting the product location". Not applicable if we use only EU locations. |
| Security / trust center | [Information security at Hetzner](https://docs.hetzner.com/general/company-and-policy/information-security-at-hetzner/) | Public, no dedicated trust center. Links the ISO 27001 certificate, BSI C5 Type 2 attestation and TOMs; last change 2025-04-07. |
| Legal notice / imprint | [Legal notice](https://www.hetzner.com/legal/legal-notice/) | Public. |
| Sub-processor change notifications | [DPA § 7(3) (PDF)](https://www.hetzner.com/AV/DPA_en.pdf) | No subscription found. DPA § 7(3): Hetzner will "inform the Client in good time and in an appropriate way" before engaging or replacing a subcontractor. |

## Sources

- [Hetzner – Anlage 3 zum AV-Vertrag: Genehmigte Subunternehmer (PDF)](https://www.hetzner.com/AV/subunternehmer.pdf)
- [Hetzner – AV-Vertrag template incl. TOMs annex (PDF)](https://www.hetzner.com/AV/DPA_de.pdf)
- [Hetzner – Appendix 3 of the DPA: Approved Subcontractors (PDF, EN)](https://www.hetzner.com/AV/subcontractors.pdf)
- [Hetzner – Data Processing Agreement template (PDF, EN)](https://www.hetzner.com/AV/DPA_en.pdf)
- [Hetzner – Privacy policy](https://www.hetzner.com/legal/privacy-policy/)
- [Hetzner – Terms and Conditions](https://www.hetzner.com/legal/terms-and-conditions/)
- [Hetzner Docs – Information security at Hetzner](https://docs.hetzner.com/general/company-and-policy/information-security-at-hetzner/)
- [Hetzner Docs – Data Protection at Hetzner (FAQ)](https://docs.hetzner.com/general/company-and-policy/data-protection-at-hetzner/)
- [Hetzner Docs – Datenschutz bei Hetzner (DE)](https://docs.hetzner.com/de/general/company-and-policy/data-protection-at-hetzner/)
- [Hetzner Docs – Cloud locations](https://docs.hetzner.com/cloud/general/locations/)
- [Hetzner Docs – Data centers and connection](https://docs.hetzner.com/general/infrastructure-and-availability/data-centers-and-connection/)
- [Hetzner – Datacenter](https://www.hetzner.com/unternehmen/rechenzentrum/)
- [Hetzner – Legal notice](https://www.hetzner.com/legal/legal-notice/)
- [DCD – Hetzner completes new data center at Finnish campus (third party)](https://www.datacenterdynamics.com/en/news/hetzner-completes-new-data-center-at-finnish-campus-begins-work-on-two-more/)
