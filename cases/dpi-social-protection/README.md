# Digital Public Infrastructure for Social Protection: Reach, Exclusion and a Privacy-First Design

An ethics case study on digital ID, payments and benefit-delivery rails in India, Brazil, Kenya and Togo.

Sources were checked on 18 November 2026. Bracketed tags such as [S1] refer to entries in SOURCES.md. Any figure that comes from a government portal or a partly verified source says so where it is used.

---

## 1. Summary

Digital public infrastructure (DPI) here means three shared layers that governments build once and then reuse across programmes:

- **Identity:** a digital ID.
- **Payments:** an instant or mobile payment rail.
- **Data and delivery:** a beneficiary registry plus the software that pays out benefits.

The evidence shows that DPI can cut leakage and speed up payments. It also shows that the same tools can lock out people who are entitled to support when they are rolled out quickly or without fallbacks.

- **Andhra Pradesh:** biometric smartcards made payments faster and less corrupt, without reducing access [S1].
- **Jharkhand:** stricter biometric checks in the food-ration programme reduced corruption. However, 1.5 to 2 million legitimate beneficiaries lost access at some point. The authors attribute the harm mainly to how the transition was managed [S2, S3].
- **Togo:** machine-learning targeting from phone data excluded fewer poor people than geographic targeting. It excluded more than a hypothetical full social registry would have [S13].

The design proposal in Section 6 treats exclusion as a first-class failure, measured as seriously as fraud. It makes non-biometric, offline and assisted paths mandatory. It also limits data collection and linkage using selective disclosure, tokenised identifiers and privacy-preserving audit logs.

---

## 2. Scope and method

- **Questions:**
  1. What does the evidence say about reach, leakage and exclusion errors?
  2. What privacy and surveillance risks follow?
  3. What would an accessible, privacy-first design look like?
  4. Which ethical risks remain, and how can they be mitigated?
- **Evidence base:**
  - peer-reviewed studies, each checked through doi.org and Crossref, with abstracts read;
  - one working paper, used to confirm the study setting;
  - court and statutory texts read in full or in relevant part;
  - multilateral reports;
  - official statistics portals;
  - two news reports, used only for one documented case.
- **Rules:**
  - No figure appears without a source.
  - Government-reported figures are labelled as such.
  - Where studies disagree, both sides are given.
  - No private individual is named. This includes the child whose death is discussed in Section 4.1.
- **Not covered:** cost-benefit modelling, country legal advice, or any primary data collection.

---

## 3. Background: what "DPI for social protection" means

| Layer | Purpose | Examples in this study |
| --- | --- | --- |
| Identity | Establish uniqueness and authenticate a claimant | Aadhaar (India); MOSIP as an open-source option [S29] |
| Payments | Move money to the claimant | UPI (India), Pix (Brazil), M-Pesa (Kenya); Mojaloop as an open-source option [S31] |
| Registry and delivery | Decide who is eligible and pay them | DBT (India), Cadastro Único and Bolsa Família (Brazil), Novissi (Togo); OpenG2P as an open-source option [S30] |

The global context:

- **Social protection:** the ILO reports that 52.4% of the world's population has at least one form of social protection coverage, up from 42.8% in 2015 [S16].
- **Identity:** the World Bank's ID4D Global Dataset 2025 estimates that about 800 million people lack proof of legal identity. It also estimates that 2.8 billion people do not have access to government-recognised digital ID systems [S14].
- **Accounts:** Global Findex 2025 reports that nearly 80% of adults worldwide have a financial account, while 1.3 billion adults still lack one. Three-quarters of adults who receive government payments receive them into an account [S15].

These figures matter for design: any system that assumes an ID, an account and a phone excludes some of the people social protection exists to reach.

Gelb and Metz describe the rapid spread of national ID programmes, which more than 60 developing countries had set up since 2000. They warn that, at worst, ID systems can exclude vulnerable groups, support discrimination and facilitate data exploitation [S17].

---

## 4. Evidence by country

### 4.1 India: Aadhaar, DBT, UPI and the PDS

**Scale (government-reported).**

- **DBT Bharat portal**, for financial year 2026-27 as displayed on 18 November 2026:
  - ₹3,13,421 crore transferred;
  - 495 crore transactions;
  - 319 schemes;
  - 56 ministries.
- **Cumulative "estimated gains":** the portal reports ₹5,14,201.92 crore [S25]. This is a government estimate. It has not been independently audited, and its method is not given on the page.
- **UPI:** NPCI reports 25,466.70 million transactions worth ₹31,52,929.46 crore in October 2026, across 758 banks live on UPI [S26]. UPI is a general payment rail, not a welfare scheme. Its reach shows what a benefit system could pay into.

**Leakage and payment-quality evidence.**

| Study | Setting and design | Main findings | Direction |
| --- | --- | --- | --- |
| Muralidharan, Niehaus and Sukhtankar 2016 [S1] | Andhra Pradesh; randomised rollout of biometric smartcards across 157 subdistricts and 19 million people; employment guarantee (NREGS) and pensions (SSP) | Faster, more predictable and less corrupt NREGS payments without hurting access to the programme; less leakage in both programmes; cost-effective; beneficiaries strongly preferred it | Positive |
| Muralidharan, Niehaus and Sukhtankar 2023 [S4] | Same reform, general-equilibrium effects | Beneficiary household earnings up 14%, poverty down 26%; 86% of income gains came from non-programme earnings | Positive |
| Banerjee, Duflo, Imbert, Mathew and Pande 2020 [S5] | Field experiment and nationwide scale-up of just-in-time e-payments in the workfare programme | Spending fell 24% while employment rose slightly; fewer fake households; officials' personal wealth fell 10%; payment delays increased; the scale-up gave a persistent 19% reduction in spending | Mixed: leakage down, delays up |
| Muralidharan, Niehaus and Sukhtankar 2025 [S2], working-paper version [S3] | Jharkhand food-ration programme (PDS); randomised and natural experiments on stricter biometric authentication | Corruption fell, but 1.5 to 2 million legitimate beneficiaries lost access at some point; harm was driven mainly by how the transition was managed | Mixed: leakage down, exclusion up |

**Exclusion evidence.**

- **Kerala and Karnataka (qualitative):** Masiero's nine years of research on the PDS in these two states argue that a biometric PDS "does not prevent the exclusion errors pervading the program" [S6].
- **Data injustice:** Masiero and Das describe multiple forms of data injustice that recipients experienced under Aadhaar-linked social protection [S7].
- **Field survey:** Nagaraj and Prakash surveyed 1,600 below-poverty-line respondents in two states. They found that Aadhaar-enabled PDS design ignored local social complexity, with harmful effects on food security for poorer households [S8].
- **UIDAI audit:** the Comptroller and Auditor General's 2021 performance audit of UIDAI reported 4,75,000 duplicate Aadhaar numbers cancelled. It also found that 73% of 3.04 crore biometric updates were voluntary updates, reported as linked to faulty biometrics. These figures come from press coverage of the report, and the report itself could not be retrieved, so treat them as partly verified [S22].

**A documented case, reported with care.** In September 2017, an 11-year-old girl in Simdega district, Jharkhand, died.

- **Before her death:** her family's ration card had been removed from the PDS list because it had not been linked ("seeded") to Aadhaar, and the family had stopped receiving rations months earlier [S23, S24].
- **Cause of death (disputed):** the family said she died of hunger, while district officials and a local medical practitioner said the cause was malaria [S23].
- **Why it matters:** whatever the medical cause, the case shows a delisting rule acting with no human check, at the cost of food access. This study does not name the child or her family.

**The courts.** In *Justice K. S. Puttaswamy (Retd.) v. Union of India* (26 September 2018), the Supreme Court upheld the Aadhaar Act's core welfare-authentication provision (section 7) by majority [S19].

- **On exclusion:** the majority held that the Act "cannot be crucified on the unproven plea of exclusion of some". It cited the authority's claim of 99.76% biometric accuracy.
- **Struck down or limited:**
  - use of Aadhaar authentication by private entities "pursuant to any contract to this effect";
  - section 33(2), as it stood;
  - mandatory Aadhaar for school admission.
- **Children:** no child may be denied a scheme's benefit for not producing an Aadhaar number.
- **The dissent:** it found that probabilistic biometrics had led to "serious instances of exclusion of eligible beneficiaries". It also held that "dignity and the rights of individuals cannot be made to depend on algorithms or probabilities" [S19].

### 4.2 Brazil: Cadastro Único, Bolsa Família and Pix

- **Registry:** Cadastro Único is the federal register used to identify and characterise low-income families across the country, and it feeds many social programmes [S27].
- **Bolsa Família outcomes:** a nationwide ecological study covered 2,853 municipalities with adequate vital statistics (2004-09) [S9].
  - Higher Bolsa Família coverage was associated with lower under-5 mortality.
  - The rate ratio was 0.83 (95% CI 0.79 to 0.88) for consolidated coverage.
  - Effects were larger for deaths from malnutrition (RR 0.35) and diarrhoea (RR 0.47).
  - The design is ecological and observational, so these are associations adjusted for covariates, not experimental effects.
- **Pix:** the central bank's 2023-2025 management report records almost 80 billion Pix transactions in 2025, moving over R$35 trillion. It also reports 148 million individuals who made or received a Pix in December 2025 [S28]. Pix is a general-purpose instant payment rail. This study did not verify how widely it is used to pay social benefits, so it is discussed only as delivery infrastructure.
- **Data protection:** the LGPD (Law 13.709/2018) sets purpose limitation ("finalidade") and necessity ("necessidade": processing limited to the minimum necessary) as principles. It allows public administration to process and share data needed to carry out public policies, subject to the public-sector chapter [S21].

### 4.3 Kenya: M-Pesa and GiveDirectly evaluations

| Study | Design | Main findings |
| --- | --- | --- |
| Suri and Jack 2016 [S10] | Long-run panel on access to M-Pesa | M-Pesa access lifted about 194,000 households (2% of Kenyan households) out of poverty, with larger effects for female-headed households |
| Haushofer and Shapiro 2016 [S11] | RCT of GiveDirectly unconditional transfers | Monthly household consumption rose from $158 to $193 PPP nine months after transfers began; large gains in psychological well-being; no overall effect on cortisol; monthly transfers more likely to improve food security, lump sums more likely spent on durables |
| Egger, Haushofer, Miguel, Niehaus and Walker 2022 [S12] | Village-level RCT, about USD 1,000 each to over 10,500 households in 653 villages | Fiscal shock over 15% of local GDP; large positive spillovers on non-recipients; minimal price inflation; local multiplier of 2.5 |

Mobile money made this kind of transfer cheap to deliver. The evidence is about cash plus a rail. It is not evidence that any particular ID or registry design is safe.

### 4.4 Togo: Novissi and machine-learning targeting

During COVID-19, Togo's Novissi emergency cash programme used machine-learning models trained on survey data to rank mobile subscribers by predicted poverty from their phone-network data [S13].

- **Compared with the geographic targeting options the government considered:** the approach cut exclusion errors by 4% to 21%.
- **Compared with methods that need a comprehensive social registry:** it increased exclusion errors by 9% to 35%. Togo has no such registry, so this comparison is hypothetical.
- **What it means:** algorithmic targeting can be the best available option in a crisis and still leave many eligible people out. It also cannot reach people without a SIM.

The official Novissi site could not be retrieved, so this study describes the programme only through [S13].

---

## 5. Contested and null findings, and what they mean

1. **The same technology, opposite results.** Andhra Pradesh [S1] and Jharkhand [S2] both used biometric authentication.
   - Access held up in one and fell for 1.5 to 2 million people in the other.
   - The authors point to transition management, not the technology alone [S2]. Fallbacks, timelines and who carries the cost of failure are design choices.
2. **Leakage savings and beneficiary costs fall on different people.**
   - Banerjee et al. found spending fell 24% but payment delays rose [S5].
   - A government's "savings" or "gains" figure (for example [S25]) can include money that was legitimately owed but never paid. The cost of exclusion lands on the poorest people, while the savings appear on the government's books.
3. **Evidence about exclusion is lopsided.** Leakage is measured routinely, but exclusion is only measured when researchers design for it ([S2], [S8]). The CAG finding that UIDAI had no system to analyse authentication errors could not be fully verified [S22]. If confirmed, it points to a structural blind spot.
4. **The court record shows the same tension.** The majority treated exclusion as "unproven"; the dissent treated it as documented [S19]. Later experimental evidence [S2] quantified exclusion during a stricter regime.
5. **Algorithmic targeting is better than a weak alternative and worse than a strong one** [S13].

---

## 6. Privacy and surveillance risks

| Risk | How it arises in DPI | Evidence or legal anchor |
| --- | --- | --- |
| Function creep | An ID built for welfare becomes a key for private services, policing or unrelated checks | Puttaswamy limited private contract-based Aadhaar use [S19]; UN Special Rapporteur warns of a "digital welfare dystopia" [S18] |
| Centralised databases and breaches | One central store of biometrics and transaction history is a high-value target and a single point of failure | Gelb and Metz warn of data exploitation [S17]; LGPD necessity principle [S21] |
| Linking across datasets | A common identifier lets agencies join welfare, payment, telecom and tax data into a profile | Masiero and Das on datafication [S7]; LGPD purpose limitation [S21] |
| Weak consent | Enrolment that is formally voluntary becomes compulsory when benefits depend on it | The Puttaswamy majority itself noted that enrolment "assumes the character of compulsory enrolment" for those seeking section 7 benefits [S19] |
| Broad state exemptions | Data law lets the state process benefit data without fresh consent | DPDP Act 2023 section 7(b) allows processing for subsidies and benefits where consent was given earlier or the data sits in a notified state database; section 17 exempts some processing [S20] |
| Profiling and algorithmic targeting | Phone or behavioural data used to rank people for aid; errors and biases are hard to contest | Togo exclusion trade-offs [S13]; UN Special Rapporteur says big tech operates in "an almost human rights-free zone" [S18] |

The UN Special Rapporteur's 2019 report (A/74/493) describes a world "stumbling, zombie-like, into a digital welfare dystopia" [S18]. The study uses this as a warning about direction rather than a finding about any one country.

---

## 7. Design proposal: an accessible, privacy-first benefit rail

### 7.1 Principles

1. **No one loses a benefit because a machine said no.** Every automated denial gets a human review path, and benefits continue while the review is pending.
2. **Collect the minimum.** This follows the LGPD necessity principle [S21] and the DPDP purpose framing [S20].
3. **Use the most inclusive channel by default.** Smartphone apps and biometrics are optional upgrades, never the only way in.
4. **Measure exclusion as seriously as fraud.**
5. **Use open standards so a country can switch vendors and audit the code.**

### 7.2 Architecture

| Component | Proposal | Standard or reference |
| --- | --- | --- |
| Identity | Federated ID: national ID, civil-registry record, community attestation or a programme-issued credential are all accepted. Use sector-specific tokenised identifiers rather than one master number, so databases cannot be joined trivially | MOSIP as one open-source national ID platform [S29] |
| Credentials | Eligibility issued as a verifiable credential; the claimant discloses only "eligible for scheme X this month", not income, caste or health data | W3C Verifiable Credentials Data Model 2.0 [S32]; SD-JWT selective disclosure, RFC 9901 [S33] |
| Registry and payout | Programme registry separate from the ID system; eligibility rules published as versioned code | OpenG2P [S30] |
| Payments | Interoperable rail, so a recipient can choose a bank, mobile wallet or agent cash-out | Mojaloop [S31]; national rails such as UPI, Pix, M-Pesa |
| Channels | USSD and SMS for feature phones; offline-capable agent devices that sync later; voice (IVR) menus; paper vouchers as a last resort | Global Findex: 86% of adults worldwide own a mobile phone, so a feature-phone path reaches far more people than a smartphone-only one [S15] |
| Authentication fallbacks | Ranked list: OTP to a registered phone, card plus PIN, nominee or proxy collection, officer-witnessed manual override with a logged reason. Biometric failure never ends the attempt | The court required alternative verification for children [S19]; transition-management lesson from [S2] |
| Accessibility | WCAG 2.2 AA for all web and app surfaces; all major local languages; plain-language notices; voice and assisted modes | WCAG 2.2 [S34] |
| Grievance and redress | Built into every channel. Each denial produces a reference number and plain-language reason; time-bound escalation; interim payment while a case is open; published resolution statistics | Lesson from [S2], [S8], [S23] |
| Audit logs | Append-only, tamper-evident transparency log of rule changes, bulk delistings and access to records. Entries hold hashed or tokenised identifiers, not raw personal data. Claimants can see who accessed their record | Pattern from Certificate Transparency, RFC 9162 [S35] |

### 7.3 Data-minimisation rules

- No storage of raw biometrics outside the ID provider. Payment and programme systems receive only a yes or no result plus a token.
- Transaction metadata is kept only as long as audits require, then deleted or aggregated.
- Cross-programme linking needs a published legal basis, a privacy impact assessment and an entry in the transparency log.
- Phone-data targeting, as in [S13], is allowed only as an emergency measure with a sunset date, an opt-out and a manual route to apply.

### 7.4 Threat model

| Threat actor | Goal | Example attack | Control |
| --- | --- | --- | --- |
| Corrupt intermediary | Divert benefits | Ghost beneficiaries, skimming at cash-out | Direct-to-account payment, recipient SMS receipts, random audits, transparency log |
| Outside attacker | Steal data or money | Breach of the central database, SIM-swap to capture OTPs | No central biometric copies in programme systems, tokenisation, SIM-swap checks, rate limits |
| Over-reaching insider or agency | Surveil or profile | Joining welfare data with policing or telecom data | Tokenised sector IDs, purpose-bound access, logged and visible access |
| Vendor | Lock-in, data reuse | Proprietary formats, reuse of data for analytics | Open standards and source, data-processing contracts, exit plans |
| System itself | Exclude without intent | Biometric mismatch, bulk delisting, model error | Fallbacks, human review before delisting, exclusion metrics, interim payments |
| Political actor | Weaponise lists | Targeting of opponents, conditional access | Legal limits, independent oversight, published rules |

### 7.5 Evaluation plan that measures exclusion

| Metric | Definition | Method |
| --- | --- | --- |
| Exclusion error rate | Share of eligible people who did not receive the benefit in a period | Independent household survey on a sampling frame not drawn from the programme registry, in the spirit of [S2], [S8] |
| Inclusion error rate | Share of recipients who are ineligible | Same survey plus administrative checks |
| Authentication failure rate | Failed first attempts and final failures, broken down by age, disability, occupation and region | Privacy-preserving aggregates from the transparency log |
| Fallback success | Share of failed authentications resolved by a fallback the same day | Log aggregates and mystery-shopper visits |
| Grievance outcomes | Time to resolution; share upheld; share receiving interim payment | Grievance system data, published monthly |
| Leakage | Funds not reaching intended recipients | Recipient-confirmed receipts versus disbursement records, as in [S1], [S5] |
| Payment timeliness | Days from due date to receipt | Payment-rail timestamps |
| Accessibility | WCAG 2.2 AA conformance; task success with assistive technology and in each language | Expert audit plus user testing with disabled and low-literacy participants |

**Rollout.**

- Phase any stricter authentication in by randomised or staggered rollout, so exclusion can be measured against a comparison group, as in [S1] and [S2].
- Pre-register a stopping rule: if the exclusion rate in treated areas exceeds the comparison rate by a set margin, the rollout pauses.

---

## 8. Ethical risks and mitigations

| Risk | Who bears it | Mitigation |
| --- | --- | --- |
| Wrongful exclusion of eligible people | The poorest, elderly, manual workers, disabled people, people without phones | Mandatory fallbacks, interim payments, exclusion metrics with a stopping rule, no automated delisting |
| Treating leakage savings as gains | Excluded beneficiaries | Report savings and exclusion together; independent audit of "gains" claims |
| Surveillance and function creep | All residents, especially minorities and dissidents | Tokenised identifiers, legal limits on linkage, transparency log, sunset clauses |
| Consent that is not free | Anyone who needs benefits | Several ID options; benefits not conditional on any single ID |
| Algorithmic bias in targeting | People with few or shared phones, women and rural residents if under-represented in training data | Fairness testing by group, publication of error rates, manual application route |
| Gender and digital divides | Women and older adults | Assisted channels, female agents where culturally needed, feature-phone support; Findex reports account ownership of 77% for women and 81% for men globally [S15] |
| Vendor lock-in | Taxpayers, future governments | Open source and open standards [S29, S30, S31] |
| Over-claiming from evidence | Policymakers, the public | Report contested and null findings; label government-reported figures |

---

## 9. Limits of this study

- It is a desk review. No primary data were collected.
- Country coverage is uneven: India has the deepest experimental evidence on exclusion, while Togo has a single study.
- Some official figures come from live portals ([S25], [S26]) and will change.
- The CAG audit figures are partly verified [S22]. The Novissi site was not reachable.
- The design proposal has not been piloted. Its costs, and how USSD and offline paths would perform in practice, still need to be tested.
