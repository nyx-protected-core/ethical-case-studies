# Protecting Children Online: Policy, Prevention and Platform Design Against Online Child Sexual Exploitation and Abuse

An ethics case study on online child sexual exploitation and abuse (OCSEA), including trafficking-linked livestreamed abuse. It is written strictly at the level of aggregate statistics, law, policy, system design and support services.

Sources were checked on 18 November 2026. Bracketed tags such as [S1] refer to entries in SOURCES.md.

**Scope note.** This document deliberately contains:

- no description of abuse or offender methods;
- no case narratives;
- no victim or accused names;
- no detail that could help anyone avoid detection.

Financial-intelligence material is described only as institutional practice.

---

## 1. Summary

OCSEA is measured mainly through reports to hotlines and clearinghouses. Those reports are large in number but are not counts of children.

- **NCMEC:** the CyberTipline received 21.3 million reports in 2025 [S1].
- **IWF:** confirmed 311,610 reports as criminal in 2025 [S3].
- **Philippines:** a household-survey study estimated that 471,416 children were trafficked to produce new sexual exploitation material in 2022, about 1 in 100 children [S7].

Policy is split over how to detect abuse in private and end-to-end encrypted (E2EE) communications.

- **For detection:** official report data show that provider reporting identifies children in imminent danger, and that hash-matching of confirmed images is used to help stop recirculation [S1, S4, S41]. This case study does not treat those findings as a settled argument for mandatory detection orders. A published position by a named child-safety organisation specifically on mandatory detection under the proposed EU regulation was not opened for this study.
- **Against mandatory scanning of private communications:** data-protection authorities and security researchers argue that scanning private communications, especially on the user's device, creates serious privacy and security risks and error rates [S20, S21, S22].
- **EU status (18 November 2026):** the proposed Child Sexual Abuse Regulation is still in trilogue. A replacement interim derogation, Regulation (EU) 2026/1881, applies until 3 April 2028 and excludes E2EE communications [S14–S19].

The case study proposes a layered approach:

- prevention-first platform design: safe defaults for minors, friction on contact from unknown adults, easy reporting;
- hash-matching of known material only where it is lawful and proportionate;
- financial-intelligence partnerships;
- survivor-centred justice: Barnahus-style services, compensation rights and limits on repeat interviews.

All of it is accountable through transparency reporting and independent oversight.

---

## 2. Scale: what official sources report

### 2.1 Headline figures

| Source | Period | Figure | What it counts |
| --- | --- | --- | --- |
| NCMEC CyberTipline [S1] | 2025 | 21.3 million reports (21,181,300 from electronic service providers; 170,193 from the public) | Reports received, not children or unique images |
| NCMEC CyberTipline [S1] | 2025 | 61.8 million files in provider reports | Files attached to reports, including duplicates |
| NCMEC CyberTipline [S1] | 2025 | More than 53,000 reports escalated as urgent or involving a child in imminent danger | Triage outcome |
| NCMEC CyberTipline [S1] | 2025 | More than 18.8 million reports referred to law enforcement; more than 4.5 million designated informational | Triage outcome |
| NCMEC CyberTipline [S1] | 2025 | Just over 300 companies reported; five providers accounted for more than 75% of reports | Concentration of reporting |
| NCMEC [S2] | 2025 | 1.4 million online enticement reports (up 158% on 2024); 113,530 child sex trafficking reports (up 323%) | Report categories |
| NCMEC CyberTipline [S1] | 2025 | More than 5,700 reports directly from victims, more than double 2024 | Self-reports |
| Internet Watch Foundation [S3, S4] | 2025 | 451,210 reports assessed; 311,610 confirmed criminal; 317,101 new images and videos added to its hash list | Assessed reports, mostly URLs; one URL can hold many images |
| WeProtect Global Threat Assessment 2025 [S11, S12] | 2024 | NCMEC received 20.5 million reports; around 100 financial sexual extortion reports a day | Secondary summary of NCMEC data |
| WeProtect Global Threat Assessment 2025 [S12] | 2023–2024 | 1,325% rise in reports linked to AI-generated abuse material to NCMEC | Report category trend |
| WeProtect Global Threat Assessment 2025 [S12] | — | 60% of the top 50 global content-sharing platforms publish no information on how they address child sexual exploitation | Transparency gap |

### 2.2 Prevalence from surveys

- **Disrupting Harm (ECPAT, INTERPOL and UNICEF Innocenti):**
  - The first phase was completed in 2022 in 13 countries in Eastern and Southern Africa and Southeast Asia [S5].
  - Nationally representative household surveys covered about 1,000 internet-using children aged 12–17 per country.
  - Data from 11,912 children in 12 countries show that 1% to 20% experienced at least one form of clear online sexual exploitation and abuse in the past year [S6].
  - Country estimates include 20.2% in the Philippines and 12.2% in Kenya [S6].
  - A second phase extended the research to 12 more countries across four other regions [S5].
- **Scale of Harm (International Justice Mission and University of Nottingham Rights Lab, 2023):**
  - The study used 3,600 household surveys and the network scale-up method.
  - It estimated that 471,416 Filipino children were trafficked to produce new child sexual exploitation material in 2022, about 1 in 100 children.
  - It estimated 232,444 adult traffickers, about 3 in every 1,000 adults [S7].
  - The report names the method's known biases: barrier effects, transmission bias and recall bias [S7].

### 2.3 The Philippines: law and financial intelligence

- **RA 9775 (Anti-Child Pornography Act of 2009, approved 17 November 2009):**
  - It defined and penalised the offence.
  - It required internet service providers to notify the police or the National Bureau of Investigation within seven days of learning that the offence was being committed using their facilities.
  - It stated that nothing in that section requires an ISP to monitor any user, subscriber or customer [S9].
- **RA 11930 (Anti-OSAEC and Anti-CSAEM Act, lapsed into law 30 July 2022):**
  - It punishes online sexual abuse or exploitation of children.
  - It penalises the production, distribution, possession of and access to child sexual abuse or exploitation materials.
  - It amends the Anti-Money Laundering Act of 2001 and repeals RA 9775 [S8].
- **Anti-Money Laundering Council (AMLC) 2023 study of suspicious transaction reports (STRs):**
  - The study used 182,729 OSAEC-related STRs from July 2020 to December 2022, worth PhP 1.56 billion in total.
  - Annual STR volume rose from 22,315 in 2020 to 92,200 in 2022.
  - 73% were detected proactively by reporting institutions, and 27% were triggered by AMLC referrals.
  - 87.25% involved remittances, and the United States was the top source country by volume and value [S10].

### 2.4 Why report counts are not victim counts

1. **One child, many reports.** One child or one image can appear in many reports, files and URLs [S1, S3].
2. **Counts follow detection effort.**
   - Reporting depends on what providers choose or are required to detect. Five providers produced more than 75% of NCMEC reports in 2025 [S1].
   - WeProtect assesses that wider adoption of E2EE likely contributed to a drop of 7 million in incidents reported to NCMEC [S12]. A fall in reports can therefore mean less detection rather than less abuse.
3. **Reporting conventions change.** NCMEC introduced a feature for providers to bundle related reports about a single viral incident [S1], which changes year-on-year comparisons.
4. **Volume can exceed capacity.** A longitudinal measurement study using NCMEC data found growth to nearly 1 million detected events a month, "exceeding the capabilities of independent clearinghouses and law enforcement to take action" [S13].
5. **Surveys measure something different.** They estimate prevalence but rely on self-report or network-based estimates, each with known biases [S6, S7].

---

## 3. The encryption versus detection debate

### 3.1 The EU proposal and its status (as of 18 November 2026)

- **Proposal:** in May 2022 the Commission proposed permanent rules (COM(2022) 209) [S15]. They would oblige providers to assess and mitigate risks and allow detection orders, with a new EU Centre to support them.
- **Positions:**
  - The European Parliament has been ready to negotiate since November 2023.
  - The Council adopted its position in November 2025 [S18].
- **Trilogues:** these began on 9 December 2025, with further rounds through 2026 [S16].
- **Already provisionally agreed** [S16], including:
  - risk assessment and mitigation;
  - reporting;
  - removal, blocking and delisting orders;
  - most provisions on the EU Centre;
  - transparency reporting.
- **Sixth trilogue (29 September 2026)** [S17]:
  - Provisional agreement on searches by the EU Centre in public content, covering known and new material.
  - Agreement in principle to limit detection orders in public content to known material "to avoid a general monitoring obligation".
  - No breakthrough on detection or searches in non-public (private) content.
  - All elements remain part of a package.
- **Official record:** the European Parliament's Legislative Observatory still lists the file as "Awaiting Parliament's position in 1st reading" [S14]. The Legislative Train page says the next trilogue is scheduled for November 2026 [S15].

### 3.2 The interim ePrivacy derogation

- **2021 regulation:** Regulation (EU) 2021/1232 let providers of certain messaging services carry out voluntary detection, despite ePrivacy confidentiality rules. Regulation (EU) 2024/1307 extended it to 3 April 2026 [S19].
- **Lapse:** in March 2026 Parliament rejected a further extension (228 for, 311 against, 92 abstentions). The regulation expired after 3 April 2026 [S18].
- **Replacement:** Regulation (EU) 2026/1881 of 24 July 2026 restored a temporary derogation until 3 April 2028 [S19]. It:
  - does not apply to scanning of audio communications;
  - does not apply to communications to which end-to-end encryption "is, has been or will be applied";
  - limits processing to what is strictly necessary [S19].

### 3.3 Arguments for detection

- **Detection finds children.** NCMEC escalated more than 53,000 urgent or imminent-danger reports in 2025, and most reports come from providers [S1].
- **Hash lists stop re-sharing.** Hashes of confirmed images are shared with internet companies to help prevent the imagery from being uploaded or redistributed [S41], which the IWF frames as helping protect victims and survivors [S4].
- **Encryption reduces visibility.** WeProtect links wider E2EE adoption to a drop in reported incidents and calls for safety-by-design in new technologies [S12].
- **International human-rights guidance supports it, with limits.** General comment No. 25 says that where encryption is considered appropriate, states "should consider appropriate measures enabling the detection and reporting" of child sexual exploitation and abuse. These measures "must be strictly limited according to the principles of legality, necessity and proportionality" [S27].

### 3.4 Arguments against mandatory scanning of private communications

- **EU data-protection bodies (EDPB and EDPS joint opinion, 2022)** [S21]:
  - The interference created by detecting unknown material and grooming in interpersonal communications "goes beyond what is necessary and proportionate". The reasons given are intrusiveness, probabilistic nature and error rates.
  - Generalised access to content to detect grooming is more likely to affect the essence of the rights in Articles 7 and 8 of the Charter.
  - Scanning of audio communications should be excluded.
  - Encryption "contribute[s] in a fundamental way" to privacy and confidentiality.
- **Abelson et al. (2024), "Bugs in our pockets"** [S20]:
  - Client-side scanning "by its nature creates serious security and privacy risks for all society", while its help to law enforcement "is at best problematic".
  - It "can fail, can be evaded, and can be abused". This case study does not elaborate on how.
- **Scientists' joint statement (4 July 2023)** [S22]:
  - The signatory researchers acknowledged the seriousness of the crime.
  - They said existing scanning technologies are "deeply flawed" and recommended that the proposal "be not taken forward".
- **Apple, 2021–2022** [S23]:
  - In August 2021 Apple announced on-device hash matching of photos being uploaded to its cloud service, alongside a messaging safety feature.
  - After public backlash it delayed the rollout. In December 2022 it abandoned the on-device photo-matching component and kept the messaging feature.
  - Legal commentary also noted the risk that hash collisions could mislabel innocent content [S23].

### 3.5 The UK Online Safety Act 2023

- **Section 121:** Ofcom may require a provider to "use accredited technology to identify CSEA content, whether communicated publicly or privately", and to take it down [S24].
- **Minimum standards:** the Secretary of State's minimum standards of accuracy came into force on 4 September 2026 [S25].
- **Accreditation:** Ofcom, or a body it appoints, must then set up an accreditation process. Only after that can Ofcom issue Technology Notices, and only where necessary and proportionate [S25, S26].
- **Current status:** the sources checked describe accreditation as the next step. They do not report any technology as accredited [S25, S26].

### 3.6 Where the fair middle ground lies

Both sides accept that:

- the crime is grave;
- detecting known material in public or unencrypted spaces is less contested than scanning private or encrypted content for new material or grooming [S17, S21];
- any measure must be lawful, necessary and proportionate [S21, S27].

The remaining disagreement is about whether private and encrypted communications can be scanned safely, and who carries the cost of errors.


---

## 4. Age assurance: a child-protection tool and its trade-offs

Age assurance — an umbrella term covering age verification, age estimation and self-declaration [S42] — is now a central instrument in child-protection policy. It is treated here only at the level of law, guidance and trade-offs. This section does not describe how any method works, how it might be evaded, or how a service should implement it.

**What recent rules require.**

- **European Data Protection Board, Statement 1/2025 (adopted 11 February 2025).** The EDPB recognises age assurance as one way to improve children's well-being online, but states that it "poses specific risks to data protection with the potential to adversely impact" not only data-protection rights but also "the right to non-discrimination … and the right to free expression and information" [S42]. Age assurance "should always be implemented in a risk-based and proportionate manner". In many cases it poses a high risk to rights and freedoms and therefore requires a data-protection impact assessment before processing [S42]. The least intrusive effective method should be used, and "viable alternatives to prove their age should be provided to users who cannot or do not wish to use a specific method" [S42]. Age assurance should not give providers additional means to identify, locate, profile or track people [S42].
- **Ofcom, Protection of Children Codes of Practice (in force 25 July 2025).** From that date, services likely to be accessed by children in the United Kingdom must comply with the children's safety duties in the Codes, or take other effective measures [S43]. Ofcom's guidance on "highly effective age assurance", which those duties rely on, requires any method used to be technically accurate, robust, reliable and fair [S43]. Self-declaration of age is not treated as highly effective [S43]. Services that allow pornography, or certain other content harmful to children, are expected to use such methods so that children are not normally able to encounter that content [S43].
- **Australia, Social Media Minimum Age scheme (in effect 10 December 2025).** From that date, age-restricted social media platforms must take reasonable steps to prevent people under 16 from creating or keeping an account [S44]. The Office of the Australian Information Commissioner, which co-regulates the privacy provisions with the eSafety Commissioner, requires methods to be necessary and proportionate, personal and sensitive information to be minimised, and personal information collected for the scheme to be destroyed once its purposes are met [S44].

**Trade-offs that should be weighed with the child-protection aim.**

- **Privacy and data security.** Age checks can involve identity documents, biometric images or other personal data. The EDPB warns against using the process to identify, locate, profile or track users, and against repurposing the data [S42]. The OAIC likewise requires purpose limitation and destruction of data collected for the Australian scheme [S44]. A data breach or later reuse of that information would fall on users, including children and adults who are only proving they are old enough.
- **Exclusion of people without documents.** Methods that depend on official identity documents, payment cards or mobile-account data can shut out people who lack those credentials. The EDPB's call for viable alternatives is a response to that risk [S42]. An excluded adult may lose access to lawful services; an excluded adolescent may lose access to information, support or community.
- **Accuracy and bias of age estimation.** Estimation methods can misclassify people, and error rates may differ by age, sex or appearance. Ofcom's "fair" and "technically accurate" criteria are intended to constrain that risk [S43], but a residual error rate remains, with costs both for children who are treated as adults and for adults who are treated as children.
- **Children's access to information.** General comment No. 25 already cited in this study states that safety tools "should not be used to restrict children's access to information" and that restrictions must be lawful, necessary and proportionate [S27]. Age-gating an entire service, rather than a specific high-risk feature, can conflict with that guidance even when the child-protection aim is genuine.
- **Displacement to less regulated services.** When a widely used service introduces strict age checks, some users — including some children — may move to services with weaker safeguards. The risk can be stated in those general terms; this study does not discuss how such a move is made.

None of these trade-offs is an argument for abandoning age assurance where it is necessary and proportionate. They are an argument for treating it as a rights-sensitive measure, with data minimisation, alternatives, accuracy standards, independent oversight and a narrow purpose, rather than as a costless default.

---

## 5. Payment-flow detection by financial intelligence units

At a high level, financial institutions and financial intelligence units (FIUs) contribute in three ways:

1. **Monitoring guidance:** they publish indicator guidance so reporting institutions can tune transaction monitoring.
2. **Suspicious-activity reports:** institutions file reports with the FIU.
3. **Intelligence to law enforcement:** FIUs analyse the reports and pass intelligence to law enforcement, often through public–private partnerships.

**Key publications:**

| Body | Publication | Key points (institutional practice only) |
| --- | --- | --- |
| AUSTRAC (Australia) | *Financial crime guide: Combating the sexual exploitation of children for financial gain* [S28] | Helps institutions review profiling and transaction monitoring and report suspicious activity; "No single financial indicator will reveal if an account is being used for child sexual exploitation purposes"; stresses partnership with law enforcement |
| FATF | *Detecting, Disrupting and Investigating Online Child Sexual Exploitation* (March 2025) [S29, S30] | Covers live-streamed sexual abuse of children for financial gain and financial sexual extortion of children; says financial intelligence plays a critical role; recommends ways to improve detection and investigation |
| FINTRAC (Canada) | Operational alert on laundering the proceeds of online child sexual exploitation (updated 27 November 2025) [S31, S32] | Indicators "should not be treated in isolation" and must be assessed with what institutions know about their clients; built with financial institutions, child-protection bodies and police |
| AMLC (Philippines) | 2023 analysis of OSAEC-related STRs [S10] | 73% of STRs were detected proactively by reporting institutions; shows that cross-border remittance monitoring matters |

**Ethical note.** Financial monitoring raises its own risks: de-risking poor or migrant customers, profiling by nationality or sex, and false suspicion. Section 7 sets out mitigations.

This case study deliberately does not reproduce any specific indicator.

---

## 6. Survivor-centred support

- **Trauma-informed approach.** A trauma-informed system:
  - realises the widespread impact of trauma;
  - recognises its signs;
  - responds by building trauma knowledge into policies and practice;
  - seeks to "actively resist retraumatization".

  Its principles include safety, peer support, trustworthiness and transparency, collaboration and mutuality, and empowerment, voice and choice [S37].
- **Barnahus (Children's House).**
  - It is a child-friendly, multidisciplinary and interagency model. It coordinates criminal and child-welfare investigations, bringing justice, health and social support services "under one roof" to prevent secondary victimisation [S33].
  - It started in Iceland in 1998, based on the US Children's Advocacy Center model.
  - The Council of Europe's Lanzarote Committee recognised it as a promising practice in 2015 [S33].
- **Non-revictimisation in proceedings.**
  - The Lanzarote Convention (Article 35) requires that interviews with the child [S34]:
    - take place without unjustified delay;
    - happen in suitable premises;
    - are conducted by trained professionals, if possible the same persons each time;
    - are as few as possible.
  - EU Directive 2011/93/EU repeats these protections [S35]. It also allows hearings without the public, lets the child be heard without being present in the courtroom, and requires prompt access to legal counselling and representation, "including for the purpose of claiming compensation".
- **Compensation.** EU Directive 2012/29/EU (Article 16) entitles victims to a decision on compensation by the offender within a reasonable time during criminal proceedings [S36].
- **Reporting without revictimisation.**
  - Child-led removal services let young people report their own images for assessment and hashing, so the images can be blocked from re-upload [S41]. IWF received 1,894 such reports in 2025 [S41].
  - Hash-sharing of confirmed images helps prevent survivors' images from being uploaded or redistributed [S41].

---

## 7. Safe-by-design platform features

| Feature | Basis | Design notes |
| --- | --- | --- |
| Safety by Design principles | eSafety Commissioner: service provider responsibility; user empowerment and autonomy; transparency and accountability [S38] | "The burden of safety should never fall solely upon the user"; assess risks before launch |
| High-privacy defaults for minors | ICO Age Appropriate Design Code, Standard 7: settings must be "high privacy" by default unless there is a compelling reason [S39, S40] | Also covers geolocation off by default, profiling limits, nudge techniques and parental controls [S39] |
| Friction on contact from unknown adults | Safety-by-design and privacy-by-design approach in General comment No. 25 [S27]; Safety by Design responsibility principle [S38] | Restrict discoverability and unsolicited messages to minors by default; add warnings and slow-downs. This reduces risk without reading content |
| Easy, in-context reporting | Safety by Design requires accessible mechanisms to flag concerns "at the point they occur" and clear escalation and law-enforcement protocols [S38] | One-tap reporting; child-friendly language; feedback to the reporter; links to support services |
| Hash-matching of known material | IWF hash list [S4, S41]; EU trilogue line limiting public-content detection orders to known material [S17] | Less intrusive than classifiers for new material or grooming [S21]. Trade-offs: false matches from hash collisions [S23], scope creep if the list is expanded beyond confirmed material [S20], and no coverage of E2EE content [S19] |
| Transparency reporting | Safety by Design transparency principle [S38]; EU text provisionally agreed on transparency reporting [S16]; UK categorised services to publish first reports in 2027 [S26] | Report detection volumes, error rates, appeals and reinstatements, and time to action; the 60% gap found by WeProtect shows the need [S12] |
| Age-appropriate design for children's rights | General comment No. 25 [S27] | Safety tools "should not be used to restrict children's access to information"; restrictions must be lawful, necessary and proportionate [S27] |

---

## 8. Ethical risks, trade-offs and mitigations

| Risk | Who bears it | Mitigation |
| --- | --- | --- |
| Under-protection of children in private or encrypted spaces | Children at risk | Prevention by design (defaults, contact friction, reporting), user-initiated reporting into E2EE services, financial-intelligence routes, investment in victim identification |
| Mass surveillance and weakened security through scanning private communications | All users, including children, journalists and at-risk groups | Limit mandated detection to known material in non-E2EE or public contexts, as in [S17] and [S19]; independent accreditation and accuracy standards [S25]; sunset and review clauses |
| False positives and wrongful suspicion | Users wrongly flagged, including teenagers sharing images among themselves | Human review before any report, published error rates, appeals, data minimisation [S21] |
| Scope creep (hash lists or classifiers repurposed) | Society at large | Legal purpose limitation, auditable hash-list governance, independent oversight [S20, S21] |
| Misreading report counts | Policymakers, public | Publish denominators, deduplicate, pair report counts with prevalence surveys [S6, S7, S13] |
| Financial de-risking and profiling | Poor, migrant and remittance-dependent families | Risk-based, combined indicators [S28, S31]; no single-factor exclusion; review for bias |
| Revictimisation through repeated interviews or exposure of images | Survivors | Barnahus model, limits on interviews, hash-based takedown, closed hearings [S33–S35] |
| Blocking children's legitimate access to information | Children | Proportionality tests; transparency to children in age-appropriate language [S27] |
| Age-assurance harms: privacy and security risks, exclusion of people without documents, misclassification, and movement to less regulated services | Adults and children who are checked, and children who lose access to information or safer services | Risk-based, proportionate methods; data minimisation and deletion; alternatives to a single credential; accuracy and fairness standards; narrow purpose limitation [S27, S42, S43, S44] |

---

## 9. Limits of this study

- It relies on official, intergovernmental, NGO and academic sources, and many figures are report counts that depend on detection effort.
- Prevalence studies cover specific countries and years.
- The EU legislation is still being negotiated, and its status may change after 18 November 2026.
- Some official pages could not be fetched, including the Council press releases, EUR-Lex and the Ofcom codes PDF. Their content was taken from other official copies or left out (see SOURCES.md). The age-assurance rules in section 4 were taken from official HTML pages and the EDPB statement PDF, not from the blocked Ofcom codes PDF.

---

### Revisions (18 November 2026)

This study was revised on 18 November 2026 after an independent balance review. The revisions add a policy-level section on age assurance, covering the EDPB's 11 February 2025 statement, Ofcom's Protection of Children Codes (in force 25 July 2025) and Australia's Social Media Minimum Age scheme (in effect 10 December 2025), and set out the privacy, exclusion, accuracy, access-to-information and displacement trade-offs. They also reword the uncited claim about "mandatory detection" so that it refers to detection supported by official report data, and note that a named organisation's published position on mandatory detection orders was not opened.
