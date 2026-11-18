# Following the Money Fairly: Financial Data, Human Trafficking Detection, and the Ethics of Watching Payments

*Research case study. Research only: nothing here was built or run. The pseudocode describes governance and audit steps. It is not a detection tool and contains no transaction-scoring logic.*

---

## 1. Introduction: why financial data matters

Most human trafficking is a business, and businesses leave payment trails. The International Labour Organization (ILO) estimates that forced labour in the private economy generates **US$236 billion in illegal profits a year**, a rise of US$64 billion (37 per cent) since 2014 [1]. Forced commercial sexual exploitation accounts for **73 per cent** of those profits but only 27 per cent of the victims in privately imposed forced labour. The gap comes from very different profits per victim: about US$27,252 a year in forced commercial sexual exploitation, compared with US$3,687 in other forms of forced labour [1]. The ILO counts about 28 million people in forced labour on any given day in 2021 [1].

Money on that scale has to be paid, moved, spent or saved. Wages are withheld or skimmed, recruitment "debts" are collected, rent and transport for victims are paid, and advertising is bought. Some of this passes through banks, money transfer companies, card issuers and payment apps. For that reason, governments, financial intelligence units (FIUs) and banks have spent more than a decade trying to use financial data to find trafficking. They also use it to support prosecutions and to identify victims who might otherwise never come forward.

The same data is also among the most revealing information that exists about a person. A payment history shows where someone lives, who they support, what work they do, which clinic they visit and who they send money home to. Systems built to find traffickers look at everyone's payments, and they make mistakes. Those mistakes do not fall evenly. They tend to land on people who are already on the margins of the banking system: migrant workers, remittance senders and sex workers, the very groups that trafficking policy says it wants to protect.

This case asks how the two goals can be held together. It covers:

1. how banks and FIUs flag trafficking-linked payments, drawing only on published public guidance;
2. what is publicly known about whether this works, including false positives;
3. the privacy, data-protection and bias risks, especially "de-risking";
4. the legal rules on sharing financial data;
5. a fair-design checklist, with governance-level pseudocode;
6. the strongest counterarguments; and
7. recommendations ranked by the strength of the evidence behind them.

### 1.1 Scope and safety note

This case stays at the policy level. It summarises, at a high level and with citations, the *categories* of indicators that official bodies have already published. It does not add detail beyond those sources, and it proposes no new detection rules, thresholds or typologies. It does not describe how to avoid detection, how to structure payments, or how to defeat monitoring. The only pseudocode is for governance: human review, error-rate audits, appeals and data retention.

---

## 2. How banks and FIUs flag trafficking-linked payments: the public guidance

### 2.1 The basic machinery

In most countries the system has three parts:

- **Reporting entities**, such as banks, money transfer operators, casinos and payment firms, must know their customers and monitor transactions. They report to the national FIU when they have grounds to suspect that funds are linked to crime. In the United States these reports are called Suspicious Activity Reports (SARs). Elsewhere they are often called Suspicious Transaction Reports (STRs) or, in Australia, Suspicious Matter Reports (SMRs).
- **The FIU** analyses the reports, combines them with other data, and passes "disclosures" of financial intelligence to police and prosecutors.
- **Law enforcement** uses that intelligence to open or extend investigations, trace assets and support prosecutions.

Trafficking guidance published by FIUs tries to help the first step. It tells reporting entities what patterns have appeared in past cases, so that their staff and monitoring systems recognise them, and it asks them to label their reports so the FIU can route them.

### 2.2 United States: FinCEN advisories of 2014 and 2020

The US FIU, the Financial Crimes Enforcement Network (FinCEN), issued **Advisory FIN-2014-A008** on **11 September 2014**, titled *Guidance on Recognizing Activity that May be Associated with Human Smuggling and Human Trafficking — Financial Red Flags* [2]. It was developed with law enforcement, non-governmental organisations and the financial industry. It separates human smuggling from human trafficking, describes the stages at which traffickers touch the financial system (recruitment, transportation and exploitation), and lists "red flags" in two appendices [2].

At a high level, the trafficking red flags include:

- a business customer whose payroll costs are absent or very low for the size of its workforce;
- large deductions from employees' wages for items such as housing and food, so that workers receive only a small fraction of their pay;
- payroll cheques cashed in a way that leaves most of the money with the employer;
- several apparently unrelated customers sending money to the same beneficiary;
- spending inconsistent with a business's stated activity that appears to cover the living costs of a group of people, such as lodging, vehicle rental and bulk food;
- frequent payments for online escort or classified advertising; and
- behavioural signs, such as a customer accompanied by a third party who speaks or acts for them [2].

The advisory, as quoted in the first issue of this study, warns that **"no one transaction or red flag by itself is a clear indicator"** of trafficking, and that red flags should be weighed with the customer's profile and expected activity [2]. The 2014 PDF was not re-opened for this revision, so that exact wording is **UNVERIFIED** against the 2014 text. The 2020 advisory, which was opened, contains a near-identical line: "no single red flag is a clear indicator of human trafficking activity" [3].

On **15 October 2020** FinCEN issued **Advisory FIN-2020-A008**, the *Supplemental Advisory on Identifying and Reporting Human Trafficking and Related Activity* [3]. It added **20 new financial and behavioural indicators** and **four typologies** seen in bank reporting since 2014: front companies, exploitative employment practices (such as visa fraud, wage retention and fees disguised as recruitment costs), funnel accounts, and alternative payment methods such as prepaid cards, mobile payment apps and virtual currency [3]. It asks filers to use the key term "HUMAN TRAFFICKING FIN-2020-A008" and to tick the human-trafficking box on the SAR form [3].

The 2020 advisory also contains one of the most important safeguards in this whole field: **"A potential victim of human trafficking should not be reported as the subject of a SAR."** Information about the victim belongs in the narrative instead [3]. This recognises that in many cases the account holder making the deposits is the exploited person, not the exploiter.

### 2.3 FATF and APG: *Financial Flows from Human Trafficking* (2018)

In **July 2018** the Financial Action Task Force (FATF), the global standard-setter for anti-money-laundering rules, and the Asia/Pacific Group on Money Laundering (APG) published *Financial Flows from Human Trafficking* [6]. It updated an earlier FATF report from 2011. It separates trafficking into three types, following the UN trafficking protocol: forced labour, sexual exploitation and the removal of organs. It found that each type produces money differently and uses different laundering methods. It also analyses the financial behaviour of each role in a scheme, such as victim, perpetrator and launderer, and publishes a set of indicators in an annex [6].

The report is frank about the limits. It found that the challenges identified in 2011 were still present "seven years later" [6]. National risk assessments did not treat laundering from trafficking with the seriousness its estimated proceeds deserved. Information-sharing within countries was incomplete. Banks said they needed more precise indicators for their own national context, and FIUs reported difficulty prioritising trafficking-related reports because the reports often could not be told apart from other suspicious activity [6]. Identifying the proceeds of forced labour was described as especially hard, because the profit may be spread through an otherwise legitimate business [6]. Its two main good practices were to assess and share the risks properly, and to build partnerships between the public sector, the private sector, civil society and non-profit organisations [6].

### 2.4 Australia: AUSTRAC and the Fintel Alliance

AUSTRAC, Australia's FIU and AML regulator, set up the **Fintel Alliance** in **2017** as a public-private partnership. Its members include major banks, remittance providers, gambling operators, law enforcement and government agencies [7]. Fintel Alliance publishes "financial crime guides" written jointly with industry partners.

Its **February 2022** guide, *Detecting and Stopping Forced Sexual Servitude in Australia*, notes that forced sexual servitude makes up around **30 per cent of slavery cases in Australia** [7]. It sorts indicators by role (coordinators and their "trusted allies", victims and customers). At the category level, they include payments to online merchants or classified sites, regular payments to hotels or short-term accommodation, many third-party cash deposits, and purchases that do not fit the customer's profile [7].

The guide contains two cautions that matter for fairness. First, "no single financial indicator will be a definitive way to identify" the crime, and businesses should combine indicators with their own knowledge [7]. Second, it states that similar payments "may be made to legitimate brothels and legal sex workers", so a range of indicators is needed to tell forced sexual servitude apart from lawful sex work [7]. In other words, the official guidance itself warns that the same signals can describe lawful work.

### 2.5 Canada: FINTRAC and Project Protect

**Project Protect** is a public-private partnership led by the Bank of Montreal and supported by Canadian law enforcement and FINTRAC, Canada's FIU. It was **launched in 2016** to target human trafficking for sexual exploitation by focusing on the money laundering involved [8]. FINTRAC published an operational alert on the topic in 2016 and updated it in **July 2021** (reference FINTRAC-2021-OA001) [8].

For the 2021 update, FINTRAC analysed a sample of about **100,000 transactions** from its trafficking-related disclosures between 2018 and 2020. It found that email money transfers and cash deposits were the main transaction types. It also identified three business models: short-stay locations such as hotels, illicit storefront businesses, and private residences [8]. The alert reminds reporting entities that a report needs "more than a 'gut feel' or 'hunch'". The legal test is *reasonable grounds to suspect*, based on the facts, the context and the indicators viewed together [8].

FINTRAC's own figures on results:

| Measure | Figure | Source |
|---|---|---|
| Change in suspicious transaction reporting on trafficking in the sex trade since Project Protect began | **750 per cent increase** | [8] |
| Disclosures of financial intelligence to police since launch (to July 2021) | **979** | [8] |
| Additional indicators in the 2021 alert | **58** | [9] |
| Project Protect disclosures in 2023–24 | **147**, of which 43% proactive | [9] |
| Investigations assisted in 2023–24 | **24** project-level investigations, 12 police forces | [9] |
| Scope change | Expanded in 2023–24 to include **labour trafficking** | [9] |

FINTRAC's 2023–24 annual report also notes that police credited its contribution to one operation that identified and supported **13 trafficking survivors** [9].

### 2.6 United Kingdom: JMLIT

The UK's **Joint Money Laundering Intelligence Taskforce (JMLIT)** was piloted in **2015** by the National Crime Agency, the Home Office, City of London Police, UK Finance and a small group of banks [10]. It has grown from 12 to over 200 partner organisations, and its scope now explicitly includes modern slavery and sexual exploitation as well as money laundering and fraud [10]. The National Crime Agency reports that since 2015 the partnership has supported **over 1,200 operations**, identified **over 10,000 accounts** used for criminal purposes, contributed intelligence leading directly to **over 419 arrests**, and helped seize or restrain **over £250 million** [10]. These figures cover all economic crime, not trafficking alone; the published sources do not break out a trafficking-specific figure. In July 2024 a "Data Fusion" pilot brought bank analysts and law enforcement officers together in a joint team that shared account and transaction data [10].

### 2.7 Other public guidance

The OSCE's 2014 report *Leveraging Anti-Money Laundering Regimes to Combat Trafficking in Human Beings* set out recommendations for governments, FIUs, police and banks. They include running parallel financial investigations alongside trafficking cases, and two-way feedback between FIUs and law enforcement [11]. The report also noted that no country had then been rated fully "compliant" with all of the FATF Recommendations [11].

### 2.8 Report volumes

Official totals show the scale of the reporting system:

- **United States.** FinCEN received about **4.7 million SARs in fiscal year 2024**, up from 4.3 million in 2022 and 4.6 million in 2023. The ten largest filers submitted about 45 per cent of them [5]. FinCEN does not publish an annual count of trafficking-flagged SARs in that summary. Its February 2024 trend analysis gives a partial view. Between January 2020 and December 2021 it received **2,311** reports linking virtual currency to online child sexual exploitation and human trafficking. These were **19 per cent** of all trafficking-related reports in that period, and they rose from **336 in 2020 to 1,975 in 2021** [4]. FinCEN noted that 95 per cent of these reports involved child sexual abuse material, so the two categories overlap heavily [4].
- **Canada.** See the Project Protect figures above [8][9].

---

## 3. What is publicly known about effectiveness

### 3.1 Evidence that financial intelligence contributes

There is real, if patchy, evidence of value.

- **Canada.** Reporting on trafficking in the sex trade rose 750 per cent after Project Protect began, and FINTRAC has produced hundreds of disclosures to police. In 2023–24, 43 per cent of those disclosures were proactive, meaning police may not have known about the people involved beforehand [8][9].
- **United States.** A Government Accountability Office (GAO) survey found that **more than 72 per cent** of personnel at six federal law enforcement agencies used Bank Secrecy Act (BSA) reports in investigations between 2015 and 2018, most often to find new subjects or to expand existing cases [18].
- **The Netherlands.** A peer-reviewed study of a Dutch anti-trafficking public-private partnership describes a pilot search strategy run on about 2 per cent of one bank's data. After follow-up checks, the bank reported 72 unusual transactions to the FIU. The FIU independently judged **50 of them (70 per cent)** suspicious, and they were linked to 50 potential victims of labour exploitation. The work led to two criminal investigations and one regulatory action in December 2019 [16].
- **United Kingdom.** A November 2025 university-led study of financial investigations in modern slavery cases found that financial evidence can corroborate other intelligence. It can reduce reliance on victim testimony and, in some cases, allow prosecution without victims giving evidence in court [17]. The same study found that suspicious activity reports and JMLIT were **underused** in these cases, alongside a shortage of accredited financial investigators [17].

### 3.2 Evidence on false positives and low conversion

The broader evidence on anti-money-laundering monitoring is much less encouraging.

- **Most alerts are wrong.** The Bank for International Settlements' Financial Stability Institute notes that rule-based transaction monitoring has been shown to produce **false-positive alerts of around 90–95 per cent**, "leading to substantial resource implications" [13]. The same paper warns that measuring success only by cutting false positives can increase false negatives, which it calls "more damaging" [13].
- **Most reports are not used.** Europol's 2017 review of EU FIUs found that **only about 10 per cent** of suspicious transaction reports were further investigated after collection, a figure unchanged since 2006 [12]. Over 65 per cent of all reports were received by just two member states, the UK and the Netherlands [12]. Europol itself says this rate should be read with caution, because FIUs work differently. Some police FIUs check every report, so their rate is always 100 per cent [12].
- **The overall system has weak measured impact.** A peer-reviewed policy review estimated that anti-money-laundering rules have **less than 0.1 per cent impact on criminal finances**, and that compliance costs exceed recovered criminal funds **more than a hundred times over**. The author cautions that the data are poorly validated, so the findings "cannot be definitive" [14].
- **Over-reporting dilutes the signal.** Economic modelling published in a peer-reviewed law and economics journal shows how heavy penalties for missing a report can push banks into "crying wolf". They file more reports of less suspicious activity, which dilutes the information value of all reports [15]. The FATF has made the same point about "defensive" reporting [29].

None of these figures is specific to trafficking. They describe the system that trafficking detection sits inside.

### 3.3 Evidence gaps

The honest summary is that nobody publicly knows the false-positive rate, the false-negative rate, or the share of convictions that relied on financial intelligence for trafficking specifically. The specific gaps are:

- **No trafficking-specific error rates are published.** No FIU source reviewed here reports how many trafficking-flagged reports turned out to be unrelated to trafficking, or how many trafficking cases had no prior report.
- **Outcome data are fragmented.** In 2024 the GAO found that US agencies track the outcomes of illicit-finance investigations separately and that no comprehensive government-wide data exist. It also found that FinCEN's surveys of law enforcement satisfaction had **response rates of only 2 to 10 per cent**, which raises the risk of bias [19].
- **Success is counted in outputs, not outcomes.** Reports filed, disclosures made and accounts identified are easy to count. Victims identified and supported, exploiters convicted and assets returned to victims are counted rarely and inconsistently [6][17][19].
- **Harms are not counted at all.** No official source reviewed here counts accounts closed or payments blocked because of a trafficking alert, or how many of those customers were victims or lawful workers.

---

## 4. Privacy, data protection and bias

### 4.1 The European framework

**The GDPR.** The EU General Data Protection Regulation (Regulation (EU) 2016/679) requires personal data to be processed lawfully, fairly and transparently. Data must be collected for "specified, explicit and legitimate purposes" (purpose limitation), limited to what is necessary (data minimisation), accurate, and kept no longer than necessary (Article 5) [20]. Article 22 gives people the right not to be subject to a decision "based solely on automated processing, including profiling" that has legal or similarly significant effects. Where an exception applies, it guarantees at least the right to human intervention, to express a view and to contest the decision [20].

**The data-protection authorities' view.** On 19 May 2021, before the Commission's new legislation was proposed, the European Data Protection Board (EDPB) wrote to the Commission to follow up its statement of 15 December 2020. It asked that the new AML laws specify which personal data may be processed, about whom, for what purposes, for how long, and who may receive it. It also asked for data protection by design, using techniques such as data avoidance, separation, abstraction and encryption [21]. It stressed that "a fair balance has to be struck" between preventing money laundering and the rights to privacy and data protection [21].

The European Data Protection Supervisor (EDPS) gave its opinion on the Commission's July 2021 package; a summary was published in the Official Journal on 29 December 2021 [22]. The EDPS asked the legislator to list the categories of personal data that banks may process, rather than leaving that to technical standards. It also asked for strict limits on special categories of data and data on criminal offences, and stated that **processing data on sexual orientation or ethnic origin "should not be allowed"** [22]. It asked for a reassessment of FIUs' "extensive access powers", and it questioned whether general public access to beneficial-ownership registers was necessary and proportionate [22].

**The 2024 AML package.** The EU adopted its new rulebook on 31 May 2024. It was published on 19 June 2024 and consists of:

- **Regulation (EU) 2024/1624 (the AML Regulation, or AMLR).** It applies in general from **10 July 2027** [23].
- **Directive (EU) 2024/1640**, the "sixth" AML Directive, on national mechanisms such as FIUs and registers [23].
- **Regulation (EU) 2024/1620**, establishing the **Anti-Money Laundering Authority (AMLA)**. AMLA has existed legally since **26 June 2024**, has its seat in Frankfurt, and is due to begin direct supervision of selected high-risk financial firms during 2028 [23].

Several AMLR provisions matter directly for fairness:

- **Article 76** allows banks to process special categories of personal data only where "strictly necessary". It requires them to inform customers, use reliable and up-to-date data, and **"not take decisions that would lead to biased and discriminatory outcomes"** on the basis of that data [23].
- **Article 77** sets a **five-year retention period**, running from the end of the business relationship, the occasional transaction, or a refusal to enter one [23].
- **Article 75**, on partnerships for information sharing, is covered in Section 5 [23].

**The courts.** On **22 November 2022**, in joined cases **C-37/20 (Luxembourg Business Registers) and C-601/20 (Sovim)**, the Court of Justice of the EU struck down the provision of the AML Directive that made beneficial-ownership information accessible "in all cases to any member of the general public" [24]. The Court accepted that fighting money laundering is an objective of general interest that can justify even serious interferences with privacy. But it held that this measure was "neither limited to what is strictly necessary nor proportionate" [24]. The ruling concerned company ownership registers, not transaction monitoring. It still shows that AML transparency measures are subject to a real necessity and proportionality test under the EU Charter of Fundamental Rights.

### 4.2 The United States framework

US privacy protection for bank records is narrower:

- **The Bank Secrecy Act** declares its purpose to be requiring reports and records that are "highly useful" in criminal, tax and regulatory investigations, among other aims [25].
- **The Right to Financial Privacy Act of 1978** generally bars federal authorities from obtaining a customer's bank records without consent, a subpoena, a summons, a search warrant or a formal written request (12 U.S.C. § 3402). However, it does not authorise withholding information "required to be reported in accordance with any Federal statute or rule" (§ 3413(d)), which covers SAR reporting [25].
- **Banks that file SARs** are protected from liability to the customer for the disclosure and for not telling them about it (31 U.S.C. § 5318(g)(3)) [25].

In practice, a US customer has little legal ground to learn of, or challenge, a report made about them.

### 4.3 Bias and de-risking

**What de-risking is.** The FATF defines de-risking as financial institutions "terminating or restricting business relationships with clients or categories of clients to avoid, rather than manage, risk" [28]. Its October 2021 stocktake found that de-risking has reduced correspondent banking in some countries and narrowed access for money transfer operators and non-profit organisations [28]. It found that the effects fall hardest on smaller and more isolated economies, emerging markets and conflict zones. It also found that some transactions may move outside the regulated system as a result [28]. The stocktake concluded that **profitability is the primary driver**, sometimes worsened by compliance costs. AML rules are "not the main cause" but can be a related factor, especially when countries or firms fail to apply the risk-based approach [28]. It noted that about 1.7 billion adults still lack a basic transaction account [28].

In February 2025 the FATF amended its Standards to promote financial inclusion and proportionate, risk-based measures [37]. After the February 2025 plenary it updated Recommendation 1 and related interpretive notes. The changes replace "commensurate" with "proportionate", require countries to allow and encourage simplified measures in lower-risk scenarios, and require supervisors to take account of firms' risk-mitigation measures "to avoid overcompliance" [37]. The official announcement also noted that about 1.4 billion people still had no bank account [37]. Those amendments go to the heart of the de-risking problem this section describes. This study found no public evaluation showing that bank behaviour has yet changed as a result. Whether de-risking has eased since February 2025 is therefore **UNVERIFIED**.

**Remittance corridors.** A 2015 World Bank survey for the G20 found that **54 per cent** of responding money transfer operators had a bank account closed in 2014, up from **29 per cent** in 2010 [30]. The share reporting no closures fell from 67 per cent to 42 per cent [30]. Money transfer operators are the main channel through which many migrant workers send wages home. When they lose bank accounts, costs rise and some flows move to informal channels. A 2025 peer-reviewed study of Somalia and Nigeria, based on fieldwork in 2023–24, argues that ambiguous international remittance rules and unrealistic know-your-customer requirements contribute to the financial exclusion of banked remitters [33].

**Account closures in the UK.** The Financial Conduct Authority (FCA) found in **September 2023** that, apart from dormancy, concerns about financial crime were by far the most common reason UK firms gave for declining, suspending or closing accounts [31]. Its **September 2024** follow-up found that firms' reasons included "non-specific financial crime concerns", including "a generalised concern about the financial crime risks posed by certain groups of customers" which led to those customers "often being denied account access" [31]. It also found that customers in vulnerable circumstances, such as those without standard identity documents, faced worse outcomes [31]. That is a regulator describing group-based exclusion in its own words.

**Sex workers.** The trafficking indicators for sexual exploitation overlap heavily with the ordinary finances of lawful sex work, as AUSTRAC's own guide acknowledges [7]. Peer-reviewed research documents the result:

- A community-led participatory study published in *Porn Studies* in 2026 found that **32 per cent** of respondents had been banned from a bank account, payment app or payment processor, and **23 per cent** reported having funds seized. It linked financial exclusion to housing instability, food insecurity and safety risks [34]. The sample was recruited through the community, so these are not population estimates.
- A 2022 article in the *International Journal of Gender, Sexuality and Law* argues that payment intermediaries' terms of service act as an "extra-legal regulation" of sex work. It finds that they often have a more immediate effect than national law, and that they apply regardless of whether the work is legal where it takes place [35].

**Why this matters for trafficking policy.** Closing an account rarely stops a trafficker, who can open others or use cash and nominees. It can, however, strand a victim whose wages are paid into that account, cut a migrant worker off from the corridor home, or push a lawful worker into cash and greater danger. FinCEN's instruction not to name potential victims as SAR subjects [3], and AUSTRAC's warning that similar payments can be lawful [7], show that regulators see this risk. But no official source reviewed here measures how often it happens.

**Algorithmic bias.** The FATF's 2021 stocktake on data pooling warns that bias "may be introduced to a system through the introduction (or exclusion) of certain data or in the programming of the algorithmic model". It calls for analytics that do not "reinforce human prejudices or discrimination" based on characteristics such as religion, race, gender, age, sexual orientation or ethnicity [29]. Because many trafficking indicators involve nationality, migration routes, occupation and remittance patterns, a model trained on past reports can easily learn to flag *groups* rather than *behaviour*.

**How to audit bias without collecting restricted data.** Checklist item 6 and recommendation 7 ask firms to compare alert, report and exit rates by nationality, occupation and corridor, and their proxies. That sits in tension with limits this study already cites. The EDPS took the view that processing data on ethnic origin "should not be allowed" [22]. AMLR Article 76 allows special-category data only where "strictly necessary" and forbids decisions that would lead to biased and discriminatory outcomes [23]. An audit that collected nationality or ethnicity in order to measure bias could itself breach those limits.

Three options reduce that tension, and none of them requires a bank to build a new file of sensitive attributes:

1. **Supervisor- or FIU-run audits on aggregated data.** The supervisor or FIU already receives reports and can compare rates across corridors or customer types without passing special-category data back to each firm.
2. **Audits that use non-sensitive proxies.** Corridor, account type, product, and whether the customer is a money-transfer operator or a small correspondent are not special-category data, and they are the channels through which nationality- and occupation-based exclusion often appears [28][31][33].
3. **A data-protection impact assessment for the audit itself.** If a more specific comparison is needed, the audit is a separate processing purpose. It should have its own legal basis, minimisation rules, retention limit and access list, and should be reviewed by the data-protection authority before it starts [20][21][22].

The checklist and the recommendations below should be read with those options in mind, not as a licence to collect ethnic-origin or nationality files for model training.

---

## 5. Data-sharing rules

A single bank sees only part of a scheme, so the case for sharing is strong. The FATF puts it this way: "a single financial institution has only a partial view of transactions and sees one small piece of what is often a large, complex puzzle" [27]. Sharing also multiplies the privacy risk. The main legal gateways are below.

### 5.1 United States: sections 314(a) and 314(b) of the USA PATRIOT Act

- **Section 314(a)** (31 CFR § 1010.520) lets federal, state, local and certain foreign law enforcement agencies, through FinCEN, ask financial institutions to search for accounts and transactions of named people. The agency must certify that each person "is engaged in, or is reasonably suspected based on credible evidence of engaging in" terrorist activity or money laundering [26].
- **Section 314(b)** (31 CFR § 1010.540) lets registered financial institutions share information with each other, under a safe harbour from liability, to identify and report possible money laundering or terrorist activity [26]. FinCEN's 2020 advisory points out that trafficking offences are predicates for money laundering, so they fall within the scope of 314(b) sharing [3]. In fiscal year 2024 more than **6,100** institutions were registered for 314(b), and **48,223** SARs referred to 314(b) sharing [5].

### 5.2 United Kingdom: Economic Crime and Corporate Transparency Act 2023

**Section 188** of the Economic Crime and Corporate Transparency Act 2023 protects "direct disclosures" between businesses in the regulated sector. Where its conditions are met, a disclosure about a customer or former customer does not breach any obligation of confidence or create civil liability [32]. The conditions are that the information relates to a customer, that either a "request condition" or a "warning condition" is met, and that the disclosing firm is satisfied the disclosure may help the other firm. **Section 189** covers indirect sharing through a third-party intermediary [32].

The Home Office guidance states that the measures came into force on **15 January 2024** [32]. It adds that sharing through an intermediary may rely only on the warning condition, which in practice means a firm has already decided to take "safeguarding action" against the customer. It also states that firms still need a lawful basis under the UK GDPR and must maintain effective complaint processes [32].

### 5.3 European Union: AMLR Article 75 partnerships

**Article 75** of the AML Regulation allows "partnerships for information sharing", with a set of conditions that work as a ready-made fairness checklist [23]:

- Sharing must be **"strictly necessary"** and in line with fundamental rights.
- Participants must **notify their supervisors in advance**. Supervisors, consulting data-protection authorities where relevant, verify that compliance mechanisms are in place and that a **data protection impact assessment** has been carried out before any sharing begins.
- Firms must **record every instance of sharing** and must **not rely solely** on information received from the partnership.
- Firms may not take decisions affecting a customer relationship on the basis of partnership information **without assessing it themselves**. Any refusal or termination that used such information must record that it came from a partnership.
- **Information generated by AI, machine learning or algorithms may be shared only where those processes "were subject to adequate human oversight"**.
- Pseudonymisation and security measures are required.
- Information about suspicious transaction reports may be shared only with the **agreement of the FIU** that received the report.
- Supervisors may require an **independent audit** of the partnership.

### 5.4 FATF guidance on partnerships and privacy-enhancing technologies

The FATF's **July 2021** *Stocktake on Data Pooling, Collaborative Analytics and Data Protection* surveyed privacy-enhancing technologies. These include homomorphic encryption, secure multiparty computation, zero-knowledge proofs and private set intersection, which can let institutions cross-match data without revealing the underlying personal information [29]. It also identified risks [29]:

- data protection law still applies, and pseudonymised data can be re-identified;
- explainability matters;
- confidentiality of suspicious transaction reports and "tipping off" rules must be respected;
- centralised data pools create security risks;
- bias in analytics must be prevented; and
- pooling suspicion can make **defensive reporting** and de-risking worse.

On the last point, the stocktake warns that a bank might treat a customer as suspicious "based solely on third party information, which may be inaccurate or the grounds for suspicion was ultimately rejected by the financial intelligence unit". It calls this an "unintended and unethical impact" of "denying a legitimate customer's access to the financial system" [29].

The FATF's **July 2022** report *Partnering in the Fight Against Financial Crime* states that AML and data protection "are not in opposition nor inherently mutually exclusive" [27]. It says sharing initiatives should be "necessary, reasonable and proportionate", that their risks "need to be outweighed by the public benefits", and that the public sector should promote regular dialogue between AML and data-protection authorities [27]. It is a non-binding report [27].

---

## 6. A fair-design checklist

The checklist below applies to any trafficking-related use of financial data: monitoring rules, analytics models, partnerships and data pools. Each item is tied to an existing legal or official source, so none of it is a new obligation invented here.

| # | Principle | What it means in practice | Anchor |
|---|---|---|---|
| 1 | **Purpose limitation** | Data gathered to detect trafficking and laundering is not reused for unrelated purposes, such as marketing, credit scoring or immigration enforcement, without a separate legal basis. | GDPR Art. 5(1)(b) [20]; EDPB [21] |
| 2 | **Clear legal basis** | Each processing activity and each sharing gateway is named in law, including what data, about whom, for what purpose and for how long. | EDPB [21]; EDPS [22]; AMLR Art. 75 [23] |
| 3 | **Necessity and proportionality** | A documented test shows that the data and sharing are strictly necessary and that the benefits outweigh the intrusion, reviewed when evidence changes. | CJEU C-37/20 [24]; FATF 2022 [27] |
| 4 | **Human review before any account action** | No account is closed, frozen or refused, and no customer is reported, on an automated score alone. A trained person reviews the facts, context and indicators together. | GDPR Art. 22 [20]; AMLR Art. 75(4) [23]; FINTRAC "more than a gut feel" [8] |
| 5 | **Error-rate audits** | Alert volumes, the share that become reports, FIU feedback on reports, and known missed cases are measured regularly, along with false-positive and false-negative estimates where possible. | BIS FSI [13]; GAO 2024 [19] |
| 6 | **Disparate-impact audits** | Alert, report and exit rates are compared across groups, using supervisor- or FIU-run aggregated analyses or non-sensitive proxies such as corridor and account type, rather than by collecting special-category data. Unexplained gaps are investigated and fixed. The audit itself is covered by a DPIA where needed (section 4.3). | AMLR Art. 76 [23]; EDPS [22]; FATF 2021 [29]; FCA 2024 [31] |
| 7 | **Survivor-centred handling** | Possible victims are not reported as SAR subjects. A suspected victim's account is not closed as a reflex, and safe referral routes to support services are ready. | FinCEN 2020 [3]; AUSTRAC [7] |
| 8 | **Appeals and redress** | Customers who are refused or exited get the reason where the law allows, a complaint route, and access to an ombudsman or other dispute body. Basic-account rights are respected. | Payment Accounts Directive Arts. 16 and 19 [36]; UK guidance [32] |
| 9 | **Data minimisation and retention** | Only data needed for the purpose is collected or shared. It is retained for the legal period and then deleted, and data that only reached partnership level is clearly labelled. | GDPR Art. 5(1)(c),(e) [20]; AMLR Art. 77 [23] |
| 10 | **Independent oversight** | Supervisors and data-protection authorities both oversee the system. Partnerships are audited independently, and a DPIA is completed before launch. | AMLR Art. 75(2),(4)(h),(7) [23]; FATF 2022 [27] |
| 11 | **Transparency reporting** | Aggregate figures are published at least yearly: reports, disclosures, outcomes, exits, appeals and audit findings. | EDPS [22]; GAO 2024 [19] |

**Basic-account rights.** The EU Payment Accounts Directive (2014/92/EU) gives consumers legally resident in the EU, including people with no fixed address and asylum seekers, the right to a basic payment account [36]. A bank must refuse an application where opening it would breach AML rules. In that case, and unless disclosure would conflict with AML objectives, it must give the specific reason in writing and explain how to complain [36]. A bank may end a basic account only on listed grounds, such as deliberate use for illegal purposes. For some other grounds it must give at least two months' written notice [36]. These rules show that the law already treats a bank account as close to a right, not a privilege.

### 6.1 Governance-level pseudocode

The pseudocode describes *who decides what, and how errors are caught*. It deliberately contains no indicators, scores or thresholds.

```text
# Governance pipeline for any trafficking-related alert.
# No step below decides suspicion by itself.

ON alert_raised(alert):
    record(alert, source, time, model_or_rule_version)      # audit trail
    assign_to_trained_reviewer(alert)                       # human in the loop

ON reviewer_assessment(alert):
    gather context the institution already lawfully holds
    IF reviewer sees signs the customer may be a VICTIM:
        do NOT name the customer as the subject of the report
        follow the safe-referral protocol (support services, not exit)
        flag account for "protective handling" (no automatic closure)
    IF reasonable grounds to suspect, under the legal test:
        file report to FIU, following official filing instructions
        do NOT tip off the customer
    ELSE:
        close alert with written reason
    log decision, reviewer, and reasons

ON proposed_account_action(customer, action):               # exit, freeze, refusal
    REQUIRE second-person review
    REQUIRE check: is the decision based on own assessment,
                   not solely on partnership or third-party data?
    REQUIRE check: does basic-account law allow this action?
    IF allowed:
        notify customer of reason and complaint route,
              unless the law forbids disclosure
    log action and legal ground

EVERY quarter:
    audit = compute_error_metrics()          # alert-to-report rate,
                                             # FIU feedback rate,
                                             # known missed cases
    fairness = compare_rates_across_groups(  # nationality, occupation,
                 alerts, reports, exits)     # corridor, and proxies
    IF unexplained disparity OR error drift:
        open remediation ticket, owner, deadline
        pause or retrain the rule or model under change control
    report audit and fairness results to board and supervisor

ON appeal_received(customer):
    route to staff independent of the original decision
    decide within a set time; record outcome
    IF original decision wrong:
        restore service, correct records, notify any partner
        that received the original information

ON retention_review(record):
    IF legal retention period has expired AND no legal hold:
        delete record and log the deletion

EVERY year:
    publish aggregate transparency report
    commission independent audit of any information-sharing partnership
    refresh the data protection impact assessment
```

---

## 7. Counterarguments

### 7.1 "Financial intelligence works, and victims depend on it"

This argument is strong. Trafficking is hard to prove from victim testimony alone. Victims may not see themselves as victims, may fear traffickers or the authorities, or may be traumatised [6][17]. Financial evidence can corroborate other evidence and sometimes allow a case to go ahead without victims testifying [17]. The Dutch partnership found real potential victims of labour exploitation who were not otherwise known [16]. Canadian police credited FINTRAC's contribution to an operation that identified 13 survivors [9]. FinCEN's advisory notes that for some victims, the only outside contact may happen at a bank branch [3].

**Response.** The checklist in Section 6 does not ask anyone to stop following the money. It asks that the money be followed with human review, measured accuracy and protection for victims. These safeguards are what make intelligence credible in court and keep victims safe.

### 7.2 "False negatives cost lives; false positives cost paperwork"

Missing a trafficking network leaves people in exploitation, and the BIS paper calls false negatives "more damaging" than false positives [13]. Critics of strict safeguards argue that some false alerts are the price of catching real cases.

**Response.** A false positive becomes more than paperwork when it leads to a closed account, frozen wages or a lost remittance channel [28][31][34]. The trade-off has to be measured before it can be weighed, and at present neither side of it is measured for trafficking (Section 3.3). Over-reporting also hides real cases among weak ones [15][29]. Better precision can therefore improve detection rather than reduce it.

### 7.3 "The law already protects privacy"

The EU now has GDPR, AMLR Articles 75 to 77, court review and an EU authority [20][23][24]. The US has the Right to Financial Privacy Act and FinCEN's own filing safeguards [3][25]. On this view, more rules add cost without adding protection.

**Response.** Partly true, especially in the EU, where Article 75 already contains much of the checklist. But the AMLR does not apply until 10 July 2027 [23]. US protections largely give way to reporting duties [25]. And none of these laws requires the disparate-impact measurement or transparency reporting that would show whether the protections work [19][31].

### 7.4 "Small banks cannot afford all this"

GAO found that BSA compliance costs are proportionally heavier for small banks: about 2 per cent of operating expenses for the three smallest banks in its sample, against under 1 per cent for the three largest [18]. Customer identification and suspicious-activity reporting were the costliest parts [18]. Adding audits and appeals could push small institutions to drop risky-seeming customers altogether, which is exactly the de-risking this case warns against [28].

**Response.** This is a fair concern. Proportionate rules can scale audits to size and risk. Shared public guidance and FIU feedback can reduce each bank's burden, and partnerships can let small institutions benefit from larger ones' analysis under Article 75-style safeguards [23][27]. The FATF itself points to under-used simplified measures for lower-risk situations [28].

### 7.5 "Partnerships and data pools are the future, so privacy should not slow them down"

The FATF and national FIUs strongly promote public-private partnerships [6][10][27].

**Response.** The FATF's own data-pooling stocktake lists the risks: re-identification, security, defensive reporting and bias [29]. EU law now makes human oversight of algorithmic outputs and a prior DPIA conditions for sharing [23]. Building those controls in from the start is cheaper than repairing a scandal later.

---

## 8. Recommendations, ranked by strength of evidence

**Strong evidence: binding law or consistent official findings**

1. **Never treat an automated alert as a decision.** Require trained human review before any report, freeze, refusal or exit. This is grounded in GDPR Article 22, AMLR Article 75(4) and FINTRAC's legal test [8][20][23].
2. **Protect victims inside the process.** Adopt FinCEN's rule that potential victims are not SAR subjects, and pair it with a protective-handling route instead of automatic exit [3][7].
3. **Treat de-risking as a failure, not a safe default.** Apply the risk-based approach case by case. Honour basic-account rights, and give reasons and complaint routes when refusing or exiting [28][30][31][36].
4. **Make sharing lawful, logged and limited.** Use the AMLR Article 75 conditions as a model even outside the EU: prior supervisory check, DPIA, records, independent assessment, human oversight of algorithmic outputs, and FIU consent for sharing report-related information [23][27].
5. **Minimise and delete.** Collect and share only what is needed, and delete it when the legal retention period ends [20][23].

**Moderate evidence: documented by official studies, but not yet measured for trafficking**

6. **Measure error rates and publish them.** Alert-to-report rates, FIU feedback and known missed cases should be reported for trafficking specifically. General AML evidence shows very high false-positive rates and low use of reports, and trafficking-specific figures do not exist [12][13][19].
7. **Run disparate-impact audits, within the data limits the law already sets.** Compare alert, report and exit rates using supervisor- or FIU-run aggregated data, or non-sensitive proxies such as corridor and account type, rather than by collecting nationality or ethnic-origin files. Give the audit its own DPIA where a more specific comparison is needed. Regulators already describe group-based exclusion, and EU law both forbids biased outcomes from sensitive data and restricts the processing of that data [22][23][29][31].
8. **Close the feedback loop.** FIUs should tell reporting entities which reports were useful. Better feedback addresses the indicator-quality problem the FATF identified and reduces defensive reporting [6][11][15].
9. **Invest in financial investigators.** Financial evidence is valuable but underused in modern slavery cases for lack of trained investigators [17].

**Emerging evidence: promising, but pilots and models only**

10. **Pilot privacy-enhancing technologies under regulatory oversight**, with data-protection authorities involved from the start. They may reduce the data each party sees, but standards, explainability and bias controls are still immature [27][29].
11. **Evaluate partnerships against outcomes for people**, not just intelligence outputs. Count victims identified and supported, convictions, assets returned to victims, and harms such as wrongful exits [9][10][16][19].
12. **Involve affected communities.** Migrant worker organisations, remittance providers and sex worker groups should help design the audits. Community-led research shows harms that institutional data do not record [33][34][35].

---

## 9. References

Each reference was opened and checked for the figures, dates or legal text cited in this case. DOIs were resolved through the Crossref API.

1. International Labour Organization (2024). *Profits and Poverty: The Economics of Forced Labour.* Geneva: ILO, 19 March 2024. https://doi.org/10.54394/JJJE9035 ; publication page: https://www.ilo.org/publications/major-publications/profits-and-poverty-economics-forced-labour ; press release: https://www.ilo.org/resource/news/annual-profits-forced-labour-amount-us-236-billion-ilo-report-finds
2. Financial Crimes Enforcement Network (2014). *Guidance on Recognizing Activity that May be Associated with Human Smuggling and Human Trafficking — Financial Red Flags.* Advisory FIN-2014-A008, 11 September 2014. https://www.fincen.gov/resources/advisories/fincen-advisory-fin-2014-a008
3. Financial Crimes Enforcement Network (2020). *Supplemental Advisory on Identifying and Reporting Human Trafficking and Related Activity.* Advisory FIN-2020-A008, 15 October 2020. https://www.fincen.gov/resources/advisories/fincen-advisory-fin-2020-a008 ; PDF: https://www.fincen.gov/system/files/advisory/2020-10-15/Advisory%20Human%20Trafficking%20508%20FINAL_0.pdf
4. Financial Crimes Enforcement Network (2024). *Use of Convertible Virtual Currency for Suspected Online Child Sexual Exploitation and Human Trafficking: Threat Pattern & Trend Information, January 2020 to December 2021.* Financial Trend Analysis, 13 February 2024. https://www.fincen.gov/system/files/shared/FTA_Human_Trafficking_FINAL508.pdf
5. Financial Crimes Enforcement Network (2025). *FinCEN Year in Review for FY 2024.* https://www.fincen.gov/system/files/shared/FinCEN-Infographic-Public-2025-508.pdf
6. FATF and Asia/Pacific Group on Money Laundering (2018). *Financial Flows from Human Trafficking.* Paris: FATF, July 2018. https://www.fatf-gafi.org/content/dam/fatf-gafi/reports/Human-Trafficking-2018.pdf
7. AUSTRAC / Fintel Alliance (2022). *Financial Crime Guide: Detecting and Stopping Forced Sexual Servitude in Australia.* February 2022. https://www.austrac.gov.au/sites/default/files/2022-02/AUSTRAC_FCG_DetectingAndStoppingForcedSexualServitude_web.pdf ; guide page: https://www.austrac.gov.au/industry-and-business/education-and-resources/publications-and-resources/detecting-and-stopping-forced-sexual-servitude-australia ; Fintel Alliance page: https://www.austrac.gov.au/government-and-partners/fintel-alliance
8. FINTRAC (2021). *Updated Indicators: Laundering of Proceeds from Human Trafficking for Sexual Exploitation.* Operational Alert FINTRAC-2021-OA001, July 2021. https://fintrac-canafe.canada.ca/intel/operation/oai-hts-2021-eng ; accompanying news release, 30 July 2021: https://fintrac-canafe.canada.ca/new-neuf/nr/2021-07-30-eng
9. FINTRAC (2024). *FINTRAC Annual Report 2023–24* (Project Protect section). https://fintrac-canafe.canada.ca/publications/ar/2024/1-eng
10. National Crime Agency (2025). "10 Year Anniversary of the UK's Public Private Partnerships" (17 July 2025). https://www.nationalcrimeagency.gov.uk/news/10-year-anniversary-of-the-uks-public-private-partnerships
11. Organization for Security and Co-operation in Europe (2014). *Leveraging Anti-Money Laundering Regimes to Combat Trafficking in Human Beings.* Vienna: OSCE. https://www.osce.org/secretariat/121125 ; PDF: https://cdn.osce.org/sites/default/files/f/documents/5/8/121125.pdf
12. Europol Financial Intelligence Group (2017). *From Suspicion to Action: Converting Financial Intelligence into Greater Operational Impact.* The Hague: Europol. https://www.europol.europa.eu/publications-events/publications/suspicion-to-action-converting-financial-intelligence-greater-operational-impact ; PDF: https://www.europol.europa.eu/sites/default/files/documents/ql-01-17-932-en-c_pf_final.pdf
13. Coelho, R., De Simoni, M., and Prenio, J. (2019). *Suptech Applications for Anti-Money Laundering.* FSI Insights on Policy Implementation No 18. Basel: Bank for International Settlements, August 2019. https://www.bis.org/publications/fsi-insight-18-suptech-applications-anti-money-laundering.pdf
14. Pol, R. F. (2020). Anti-money laundering: The world's least effective policy experiment? Together, we can fix it. *Policy Design and Practice*, 3(1), 73–94. https://doi.org/10.1080/25741292.2020.1725366
15. Takáts, E. (2011). A theory of "crying wolf": The economics of money laundering enforcement. *Journal of Law, Economics, and Organization*, 27(1), 32–78 (published online 2009). https://doi.org/10.1093/jleo/ewp018
16. Coster van Voorhout, J. E. B. (2020). Combatting human trafficking holistically through proactive financial investigations. *Journal of International Criminal Justice*, 18(1), 87–106. https://doi.org/10.1093/jicj/mqaa013
17. Modern Slavery and Human Rights Policy and Evidence Centre (2025). *Financial Investigations of Modern Slavery Offences.* Research led by the Wilberforce Institute, University of Hull, November 2025. https://files.modernslaverypec.org/production/assets/downloads/MSPEC_FinancialInv_Report_PROOF2.pdf
18. US Government Accountability Office (2020). *Anti-Money Laundering: Opportunities Exist to Increase Law Enforcement Use of Bank Secrecy Act Reports, and Banks' Costs to Comply with the Act Varied.* GAO-20-574, 22 September 2020. https://www.gao.gov/products/gao-20-574
19. US Government Accountability Office (2024). *Anti-Money Laundering: Better Information Needed on Effectiveness of Federal Efforts.* GAO-24-106301, published 8 February 2024, reissued with revisions 13 June 2024. https://www.gao.gov/products/gao-24-106301
20. European Parliament and Council (2016). Regulation (EU) 2016/679 (General Data Protection Regulation), Articles 5 and 22. Official text via the EU Publications Office: https://publications.europa.eu/resource/celex/32016R0679 ; EUR-Lex: https://eur-lex.europa.eu/eli/reg/2016/679/oj
21. European Data Protection Board (2021). Letter to the European Commission on the protection of personal data in the AML-CFT legislative proposals, 19 May 2021 (following the EDPB Statement of 15 December 2020). https://www.edpb.europa.eu/system/files/documents/2021-05/letter_to_ec_on_proposals_on_aml-cft_en.pdf ; statement: https://www.edpb.europa.eu/documents/statement/statement-on-the-protection-of-personal-data-processed-in-relation-with-the_en
22. European Data Protection Supervisor (2021). Summary of the Opinion of the EDPS on the AML/CFT package of legislative proposals. *Official Journal of the European Union*, C 524, 29 December 2021, pp. 10–12 (2021/C 524/03). https://publications.europa.eu/resource/celex/52021XX1229(01)
23. EU 2024 AML package: Regulation (EU) 2024/1624 of 31 May 2024 (AMLR), Articles 75–77 and 90, OJ L 2024/1624, 19 June 2024, https://publications.europa.eu/resource/celex/32024R1624 (EUR-Lex: https://eur-lex.europa.eu/eli/reg/2024/1624/oj); Directive (EU) 2024/1640 of 31 May 2024, https://publications.europa.eu/resource/celex/32024L1640 ; Regulation (EU) 2024/1620 establishing AMLA, https://eur-lex.europa.eu/eli/reg/2024/1620/oj ; AMLA, "About AMLA": https://www.amla.europa.eu/about-amla_en
24. Court of Justice of the European Union (2022). Judgment in Joined Cases C-37/20 *Luxembourg Business Registers* and C-601/20 *Sovim*, 22 November 2022. Press Release No 188/22. https://curia.europa.eu/jcms/upload/docs/application/pdf/2022-11/cp220188en.pdf
25. United States Code (2023 edition, US Government Publishing Office): Right to Financial Privacy Act, 12 U.S.C. § 3402, https://www.govinfo.gov/content/pkg/USCODE-2023-title12/html/USCODE-2023-title12-chap35-sec3402.htm , and § 3413, https://www.govinfo.gov/content/pkg/USCODE-2023-title12/html/USCODE-2023-title12-chap35-sec3413.htm ; Bank Secrecy Act, 31 U.S.C. § 5311, https://www.govinfo.gov/content/pkg/USCODE-2023-title31/html/USCODE-2023-title31-subtitleIV-chap53-subchapII-sec5311.htm , and § 5318(g)(3), https://www.govinfo.gov/content/pkg/USCODE-2023-title31/html/USCODE-2023-title31-subtitleIV-chap53-subchapII-sec5318.htm
26. Code of Federal Regulations, 31 CFR § 1010.520 (information sharing between government agencies and financial institutions, "314(a)") and § 1010.540 (voluntary information sharing among financial institutions, "314(b)"). https://www.ecfr.gov/current/title-31/subtitle-B/chapter-X/part-1010/subpart-E/section-1010.520 ; https://www.ecfr.gov/current/title-31/subtitle-B/chapter-X/part-1010/subpart-E/section-1010.540
27. FATF (2022). *Partnering in the Fight Against Financial Crime: Data Protection, Technology and Private Sector Information Sharing.* Paris: FATF, July 2022. https://www.fatf-gafi.org/en/publications/Digitaltransformation/Partnering-in-the-fight-against-financial-crime.html ; PDF: https://www.fatf-gafi.org/content/dam/fatf-gafi/guidance/Partnering-int-the-fight-against-financial-crime.pdf
28. FATF (2021). *High-Level Synopsis of the Stocktake of the Unintended Consequences of the FATF Standards.* 27 October 2021. https://www.fatf-gafi.org/content/dam/fatf-gafi/reports/Unintended-Consequences.pdf
29. FATF (2021). *Stocktake on Data Pooling, Collaborative Analytics and Data Protection.* Paris: FATF, July 2021. https://www.fatf-gafi.org/content/dam/fatf-gafi/guidance/Stocktake-Datapooling-Collaborative-Analytics.pdf
30. World Bank Group, Finance and Markets Global Practice (2015). *Report on the G20 Survey on De-risking Activities in the Remittance Market.* October 2015. https://documents1.worldbank.org/curated/en/679881467993185572/pdf/101071-WP-PUBLIC-GPFI-DWG-Remittances-De-risking-Report-2015-Final-2.pdf
31. Financial Conduct Authority (2023, 2024). *UK Payment Accounts: Access and Closures* (September 2023), https://www.fca.org.uk/publication/corporate/uk-payment-accounts-access-and-closures.pdf ; and *UK Payment Accounts Access and Closures: Update* (September 2024), https://www.fca.org.uk/publication/corporate/uk-payment-accounts-access-closures-update.pdf
32. Economic Crime and Corporate Transparency Act 2023, sections 188–189. https://www.legislation.gov.uk/ukpga/2023/56/section/188 ; Home Office, *Guidance on the Information Sharing Measures in the Economic Crime and Corporate Transparency Act 2023*: https://www.gov.uk/government/publications/information-sharing-measures-in-the-economic-crime-and-corporate-transparency-act/guidance-on-the-information-sharing-measures-in-the-economic-crime-and-corporate-transparency-act-2023
33. Muse, M. A. (2025). The ambiguity, opaqueness and consequences of FATF's remittance regulatory strategies: The case studies of Somalia and Nigeria. *Journal of Economic Criminology*, 8, 100156. https://doi.org/10.1016/j.jeconc.2025.100156
34. Collins, D., Blunt, D., Lee, L., Wolf, A., Johnson-Dash, J., and Garcia, G. (2026). Closed without warning: Material marginalization of people in the sex trades. *Porn Studies*, published online 6 August 2026, 1–29. https://doi.org/10.1080/23268743.2026.2692426
35. Beebe, B. (2022). "Shut up and take my money!": Revenue chokepoints, platform governance, and sex workers' financial exclusion. *International Journal of Gender, Sexuality and Law*, 2(1), 140–170. https://doi.org/10.19164/ijgsl.v2i1.1258
36. European Parliament and Council (2014). Directive 2014/92/EU of 23 July 2014 on the comparability of fees related to payment accounts, payment account switching and access to payment accounts with basic features, Articles 16 and 19. OJ L 257, 28 August 2014. https://publications.europa.eu/resource/celex/32014L0092
37. FATF (25 February 2025). "FATF updates Standards and consults on guidance to better promote financial inclusion." Official announcement of the February 2025 amendments to Recommendation 1 and related interpretive notes, including proportionate measures, simplified measures in lower-risk scenarios, and supervisor review to avoid overcompliance. https://www.fatf-gafi.org/en/publications/Fatfrecommendations/update-standards-promote-financial-conclusion-feb-2025.html (the page returned 403 to a direct automated request; the text was read through a second fetch on 18 November 2026). See also the plenary outcomes: https://www.fatf-gafi.org/en/publications/Fatfgeneral/outcomes-fatf-plenary-february-2025.html

## Revisions (18 November 2026, round 7/8 audit)

- Added a paragraph on the tension between nationality- and occupation-based bias audits and the data limits in AMLR Article 76 and the EDPS opinion, and set out three options: supervisor- or FIU-run aggregated audits, non-sensitive proxies such as corridor or account type, and a DPIA for the audit itself. Checklist item 6 and recommendation 7 were aligned with those options.
- Added the FATF's February 2025 amendments promoting financial inclusion and proportionate, risk-based measures, and noted that no source reviewed here shows that bank behaviour has yet changed.
- Recorded that the exact 2014 FinCEN wording was not re-opened, and cited the near-identical 2020 line, "no single red flag is a clear indicator of human trafficking activity".

