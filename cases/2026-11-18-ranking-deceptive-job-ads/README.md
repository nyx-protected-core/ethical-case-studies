# Algorithms Against Trafficking Recruitment: An Ethical Case Study

*Research-only case study. Nothing here has been built or run. The pseudocode is included only to make the method concrete.*

## 1. Introduction

Algorithms are already used to sort, rank and flag huge volumes of online content. This case study asks where that capability could do real good against modern slavery and human trafficking, and where it could do harm. We look at three candidate problems, pick the one where the evidence for impact is strongest, and work through it in depth: how big the problem is, what has already been tried, a proposed method, the ethical risks and how to reduce them, and what is still unknown.

One principle runs through the whole document. In this field an algorithm should help trained people decide where to look first. It should never decide on its own that a person is a trafficker, a victim or a criminal.

## 2. Three candidate problems

### (a) Deceptive online job ads used to recruit trafficking victims

**Problem.** Traffickers often recruit by "fishing": they post attractive job offers, often for well-paid work abroad, and wait for people to reply. These ads appear on ordinary job boards, classified sites and social media.

**Proposed solution.** A transparent risk-scoring system that reads job ads as they are posted and gives each one a score built from understandable warning signs. Examples are pay far above the market rate for the role, a vague or unverifiable employer, requests for upfront fees or passport copies, offers of travel and housing abroad, and contact only through private messaging apps. The system also links ads that share near-identical text or the same phone numbers, which can reveal one actor posting under many names. Its output is a ranked queue for trained human reviewers, such as a platform's trust-and-safety team or a specialist anti-trafficking organisation. Those reviewers decide what to do: warn job seekers, ask the poster to verify, or escalate. The system never takes automatic action against an individual.

### (b) Forced-labour risk scoring in supply chains

**Problem.** Forced labour is hidden in the lower tiers of global supply chains, such as raw materials, farms and subcontracted factories, where buyers have little visibility.

**Proposed solution.** A risk model that combines trade and shipping records, supplier registries, country and sector risk indicators, audit histories and worker grievance reports to score suppliers and sourcing routes. The scores help buyers and labour inspectors decide where to send audits and worker-voice surveys. Important safeguards would be needed so that a high-risk score triggers remediation and support for workers rather than an instant cut-off, which could push workers into worse jobs.

### (c) Fair, needs-based allocation of food aid

**Problem.** Food-aid budgets are limited, needs change quickly, and targeting errors leave some hungry households out while others are covered twice.

**Proposed solution.** An allocation model that estimates household or area need from survey data, market prices, weather and displacement data, then solves a constrained optimisation problem: get the most nutritional benefit within the budget, with explicit fairness limits, such as a minimum coverage level for every district and caps on how much any group can be under-served. The model's choices and the trade-offs behind them would be published so that communities can challenge them.

### Which idea is strongest?

We choose **(a), deceptive job-ad detection**, for four reasons.

1. **It acts upstream.** It aims to reach people at the recruitment stage, before they travel, hand over documents or take on debt. (b) usually finds harm after exploitation has started, and (c) is valuable but addresses a different goal.
2. **The signal is public and textual.** Job ads are published openly, so the main input data exists without collecting private information about people.
3. **There is a research base to build on and learn from.** There are public datasets for job-scam detection and a decade of work on trafficking-related ads, including well-documented failures and critiques.
4. **The risks are serious but can be designed around.** The main dangers, false accusations and surveillance creep, can be reduced by keeping a human in charge, measuring errors openly and limiting what data is used.

The evidence also shows a clear limit, covered below: no one can reliably identify trafficking from an ad alone. So the honest goal is to detect deceptive and high-risk recruitment, which is a narrower and more achievable goal than detecting trafficking.

## 3. Deep dive: risk-ranking deceptive online job ads

### 3.1 Scale of the problem

- **Modern slavery overall.** The 2021 Global Estimates of Modern Slavery, published in 2022 by the ILO, Walk Free and IOM, found **49.6 million people in modern slavery** on any given day. Of these, **27.6 million were in forced labour** and 22 million in forced marriage. That was 2.7 million more people in forced labour than in 2016. [1]
- **Migrants are at higher risk.** The same estimates found that migrant workers are **more than three times as likely** to be in forced labour as non-migrant adult workers. The report links this to unfair and unethical recruitment. [1]
- **Money.** The ILO's 2024 report *Profits and Poverty* estimates **US$236 billion a year in illegal profits** from forced labour in the private economy. That is close to US$10,000 per victim, and US$64 billion (37%) more than in 2014. The ILO notes that this figure *excludes* illegal recruitment fees. [2]
- **Detected trafficking is rising.** The UNODC *Global Report on Trafficking in Persons 2024* recorded a **25% rise in detected victims in 2022 compared with 2019**. Detections of trafficking for forced labour rose by **47%** over the same period. [4]
- **Online recruitment is a common route.** In the UNODC's 2020 Global Report, its chapter on the internet reviewed 79 trafficking court cases with an internet element. **31 of the 79 involved online recruitment, affecting almost half of the victims in the dataset.** The report describes "fishing" through ads for high-paying or prestigious jobs. In one case, about 100 women were drawn in by an ad for modelling jobs overseas. [3]
- **A fast-growing variant: scam compounds.** The UN Human Rights Office reported in 2023 that, according to credible sources, **at least 120,000 people in Myanmar and around 100,000 in Cambodia** may be held in situations where they are forced to carry out online scams. [5] UNODC indicators for this form of trafficking say victims are "mainly lured by job advertisements". These are typically offers of high salaries, free travel and housing for IT, customer-service or marketing roles, found on social media and messaging platforms. [6]

All of these are estimates or counts of *detected* cases. The true numbers are unknown and almost certainly higher.

### 3.2 Existing approaches and their limits

| Approach | What it does | Limits |
|---|---|---|
| DARPA Memex program [7]; knowledge-graph search systems (Szekely et al. 2015 [8]; Kejriwal and Szekely 2017 [9]) | Domain-specific crawling, indexing and search over web content, including ads, to support investigators | Built mainly for law-enforcement investigation of sex-trade ads rather than prevention, and heavy to operate |
| Commercial investigative tools, e.g. Traffic Jam by Marinus Analytics [10] | Search and analysis of online ads for law enforcement | Mostly focused on commercial sex ads; methods and error rates are not fully public |
| Public-data pattern mining (Dubrawski et al. 2015 [11]) | Uses publicly available ad data to find patterns of trafficking activity | Labels are scarce and noisy; mostly escort ads |
| Multimodal deep classifiers (Tong et al. 2017, Trafficking-10k dataset of more than 10,000 annotated ads [12]) | Text-and-image neural model scoring escort ads for trafficking risk | Black-box model; labels come from expert judgement, not confirmed cases; centred on sex-trade ads |
| Near-duplicate / template clustering (InfoShield, Lee et al. 2021 [13]) | Finds clusters of ads written from shared templates, suggesting organised posting | Clusters show coordination, not crime. Legitimate agencies also reuse templates |
| Job-scam classifiers on EMSCAD (Vidros et al. 2017 [14]) | First public dataset of 17,880 real job ads (866 fraudulent) with baseline classifiers | Fraud labels mean "employment scam", not trafficking; English-language data from one platform, 2012–2014 |

The most important finding for this case study comes from empirical criticism:

- **Indicators are common in ordinary ads.** Volodko, Cockbain and Kleinberg (2019) examined 430 ads on a major Lithuanian site for people seeking work abroad. **98.4% contained at least one commonly used labour-trafficking indicator.** This suggests that many "indicators" are simply normal features of that labour market. [15]
- **Ads alone cannot identify trafficking.** Giommoni (2024) reviewed research that claims to identify trafficking from online ads. He argues that it rests on unvalidated indicators, subjective interpretation and the assumption that ads are truthful, and that it often confuses voluntary sex work with trafficking. His conclusion is that victims are unlikely to be identifiable from online ads alone. [16]

These findings shape the proposed method. It ranks ads for **deception and exploitation risk** and asks humans to check them. It does not claim to detect trafficking.

### 3.3 Proposed method in plain words

**Goal.** Each day, give a limited team of trained reviewers a short, ordered list of the job ads most worth checking, with a plain explanation of why each ad is on the list.

**Step 1: Collect only what is needed.** Take the ad text, title, stated pay, location, destination country, contact method and posting metadata such as posting time and account age. Do not collect applicants' data. Do not build profiles of people who read or reply to ads.

**Step 2: Extract understandable features.** Each feature should be one a reviewer would recognise:

- **Pay mismatch:** stated pay compared with typical pay for that role and destination, using public wage data. Very high pay for low-skill work is a classic lure.
- **Employer vagueness:** no company name, no verifiable registration, website or address, or a well-known company's name used with a contact that does not match.
- **Money and documents:** asks for upfront "processing" or "visa" fees, or for passport copies or identity documents before any interview. The ILO's profits report treats illegal recruitment fees as a separate source of illegal profit. [2]
- **Cross-border relocation package:** free flights, housing and food abroad, especially with no named workplace.
- **Contact channel:** contact only through private messaging apps, with no email or company domain.
- **Urgency and pressure:** "start immediately", "no experience needed", "limited places".
- **Network signals:** the same phone number, near-identical text or the same images across many ads posted under different employer names.

**Step 3: Link ads into networks.** Use near-duplicate detection to find ads that are slight rewrites of each other. MinHash estimates text similarity cheaply (Broder 1997 [17]), and locality-sensitive hashing (Indyk and Motwani 1998 [18]) groups similar ads without comparing every pair. Connect ads that share phone numbers, messaging handles or images, and treat the connected groups as candidate "posting networks". The size and spread of a network become extra features. For example, one phone number advertising jobs for 15 different "companies" in 4 countries is worth a look.

**Step 4: Score with an interpretable model.** Use a model whose reasoning a reviewer can read, such as a points-based scorecard or a short rule list. Rudin (2019) argues that interpretable models should be used in place of explained black boxes in high-stakes decisions [19], and certifiably optimal rule lists show that simple models can stay competitive [20]. Each flagged ad is shown with the specific reasons that raised its score.

**Step 5: Rank for a fixed review budget.** Reviewers can check only K ads per day, so the system's job is to put the K most worthwhile ads first. It should also send a small random sample of unflagged ads to reviewers, so that the system's blind spots can be measured.

**Step 6: Human review and proportionate action.** Reviewers choose from graded responses: no action; ask the poster to verify identity or licence; show applicants a safety notice ("Never pay a fee to get a job; never hand over your passport"); remove the ad as fraud under platform rules; or, in clear cases with evidence beyond the ad, refer to an anti-trafficking organisation or the authorities under a published policy. Every decision is logged with its reason.

**Step 7: Appeals and feedback.** Posters whose ads are restricted can appeal to a different human reviewer. Confirmed outcomes are fed back as labels to recalibrate the scorecard. Appeal results are included so that the system learns from its mistakes.

#### Pseudocode

```
# Runs daily. Produces a ranked review queue. Never acts on its own.

for ad in new_ads_today:
    f = {}
    f.pay_ratio        = ad.stated_pay / typical_pay(ad.role, ad.destination)
    f.no_verifiable_employer = not verify_employer(ad.employer_name, ad.website, ad.registration)
    f.asks_fee         = mentions_upfront_fee(ad.text)          # "processing fee", "visa fee"
    f.asks_documents   = mentions_passport_or_id_upfront(ad.text)
    f.relocation_pkg   = offers_travel_and_housing_abroad(ad.text, ad.destination)
    f.messaging_only   = contact_is_messaging_app_only(ad.contacts)
    f.urgency          = urgency_phrases(ad.text)
    ad.signature       = minhash(shingles(normalise(ad.text)))
    ad.features        = f

# Link ads into posting networks
buckets = lsh_index(signatures of recent_ads, similarity_threshold = T)
graph   = new Graph()
for pair in candidate_pairs(buckets) where jaccard_estimate(pair) >= T:
    graph.add_edge(pair.a, pair.b, reason = "near-duplicate text")
for group in ads_sharing(phone_number or messaging_handle or image_hash):
    graph.connect_all(group, reason = "shared contact or image")

for ad in new_ads_today:
    net = graph.component(ad)
    ad.features.network_size     = count(net)
    ad.features.distinct_employers = count_distinct(employer_name in net)
    ad.features.distinct_countries = count_distinct(destination in net)

# Interpretable scorecard: points and reasons
for ad in new_ads_today:
    points, reasons = 0, []
    if ad.features.pay_ratio > 2.0:          points += 3; reasons.add("pay far above role norm")
    if ad.features.asks_fee:                 points += 4; reasons.add("asks for upfront fee")
    if ad.features.asks_documents:           points += 3; reasons.add("asks for passport/ID before interview")
    if ad.features.no_verifiable_employer:   points += 2; reasons.add("employer cannot be verified")
    if ad.features.relocation_pkg and ad.features.messaging_only:
                                             points += 2; reasons.add("relocation offer via messaging app only")
    if ad.features.distinct_employers >= 3:  points += 3; reasons.add("same contact used by many 'employers'")
    ad.score   = calibrated_probability(points)   # mapped using reviewed outcomes
    ad.reasons = reasons

queue = top_K(new_ads_today, by = score)
queue += random_sample(rest_of_ads, size = small_audit_fraction)   # measures blind spots
send_to_human_reviewers(queue, show = [ad.text, ad.score, ad.reasons, network_view])

# Humans decide; system records
on reviewer_decision(ad, action, reason):
    log(ad.id, action, reason, reviewer_role)          # no applicant data stored
    if action in {restrict, remove}: open_appeal_window(ad.poster)
    add_label_for_recalibration(ad, outcome)
```

The point values and thresholds above are illustrative. Real values would be set from reviewed data and published.

#### Evaluation plan

- **Precision at fixed budget (precision@K):** of the K ads reviewers check each day, what share turn out to be deceptive or high-risk? This is the main measure of whether reviewers' time is well spent.
- **Recall at fixed budget:** of all confirmed deceptive ads, including those found through the random audit sample, user reports and appeals, what share appeared in the top K?
- **Calibration:** when the system says "30% likely", about 30% of those ads should be confirmed. This is checked with reliability curves on reviewed data and recalibrated regularly.
- **Per-group error audits:** false-positive and false-negative rates reported separately by language, region, destination country, job sector and size of employer. Chouldechova (2017) shows that different fairness measures cannot all be equal at once when base rates differ between groups [21], so the chosen measure, and the reason for choosing it, must be stated openly.
- **Baselines:** compare against simple keyword rules and against existing job-scam classifiers trained on EMSCAD [14], so that any claimed gain is real.
- **Outcome measures beyond accuracy:** time from posting to review, the share of applicants who saw a safety notice before replying, appeal rates and appeal reversal rates.
- **Documentation:** publish a model card describing intended use, out-of-scope uses, data, error rates by group and known limits (Mitchell et al. 2019 [22]).

### 3.4 Ethical risks and mitigations

| Risk | Why it matters | Mitigation |
|---|---|---|
| **False positives against legitimate employers and job seekers** | Legitimate international recruiters, small businesses and seasonal agriculture often offer housing and travel. The 98.4% indicator rate in one real labour market [15] shows how easily ordinary ads look "risky". Removing their ads cuts off safe jobs, and migrants may then turn to worse channels. | Rank, don't punish. Use graded responses, with "verify" before "remove". Require human review for any restriction. Offer fast appeals to a different reviewer. Publish false-positive rates. Allow licensed recruiters to verify once and be marked as verified. |
| **Bias against languages, regions or informal work** | Models trained mostly on English or one platform may score other languages and informal job markets as more suspicious. Typical-pay data is thin for informal work. | Per-language and per-region error audits; native-speaker reviewers; prefer features that work across languages (fees, document requests, shared contacts); flag low-confidence cases rather than scoring them high. |
| **Displacement** | Traffickers adapt. Stricter screening on one platform may push recruitment to private messaging apps or word of mouth, where it is harder to see and to warn people. | Treat displacement as an expected result and monitor for it. Pair detection with prevention, such as safety notices and links to fair-recruitment information. Share anonymised patterns with other platforms rather than only removing ads. |
| **Misuse for surveillance of sex workers or migrants** | Research shows that anti-trafficking measures aimed at online ads have harmed sex workers. After the 2018 US laws known as FOSTA-SESTA and the removal of Backpage, sex workers reported losing income and safety tools (Blunt and Wolf 2020 [23]; Musto et al. 2021 [24]). A job-ad tool could be repurposed to track migrants or enforce immigration rules. | A narrow written purpose: job ads only, not personal ads or sex-work ads. Contractual and technical bans on immigration-enforcement use and on scoring individuals. No tracking of applicants. Independent oversight board. Public statement of who may use the system and for what. |
| **Data privacy** | Ads contain phone numbers and handles that may belong to real, innocent people. Network linking could expose them. | Data minimisation: store hashed contact identifiers, not raw ones, where possible. Short retention periods. Run processing on the platform's own systems instead of sending data to third parties. No collection of applicant data. Access controls and logs. |
| **Automation bias** | Reviewers may simply trust the score. | Show the reasons, not just the number. Include random audit items whose scores are hidden. Train reviewers. Monitor how often reviewers agree with the score. |
| **Over-claiming** | Saying "we detect trafficking" invites misuse and false confidence [16]. | Describe the system honestly as ranking recruitment ads for deception risk for human review. Publish error rates and limits. |

### 3.5 Limitations and open questions

1. **Ground truth is weak.** Confirmed trafficking outcomes linked to specific ads are rare and mostly sealed in court or police records. Labels will come mostly from scam reports and reviewer judgement, which carry their own biases [14][16].
2. **Ads are not the whole story.** Much recruitment happens through acquaintances, agents and direct messages [3][6]. Public-ad screening will miss these routes by design.
3. **Base rates are unknown.** Without knowing how many ads are actually deceptive, precision on a review queue is hard to interpret. The random audit sample helps but cannot fully solve this.
4. **Typical-pay data is uneven.** Pay comparisons are reliable only where good wage data exists, which is often not the case in the corridors where risk is highest.
5. **Measuring real-world harm reduction is hard.** Fewer deceptive ads on one site does not prove fewer people were trafficked. Ideally the system would be evaluated together with anti-trafficking organisations using outcome data that respects privacy.
6. **Governance is unresolved.** Who owns the system, who audits it, and how affected workers and recruiters get a say are open questions. Technical design cannot settle them.
7. **Text generation tools change the picture.** Fraudsters can now produce many different-looking ads quickly, which may weaken near-duplicate detection. Signals such as fees, document requests and shared contacts may hold up better than wording.

## 4. References

Each DOI below was resolved through the Crossref API and its title, authors and year matched. Each web link was checked and returns a working page.

1. International Labour Organization, Walk Free and International Organization for Migration (2022). *Global Estimates of Modern Slavery: Forced Labour and Forced Marriage.* Geneva: ILO. https://www.ilo.org/publications/major-publications/global-estimates-modern-slavery-forced-labour-and-forced-marriage ; executive summary: https://www.ilo.org/media/370821/download
2. International Labour Organization (2024). *Profits and Poverty: The Economics of Forced Labour* (2nd ed.). Geneva: ILO. https://www.ilo.org/publications/major-publications/profits-and-poverty-economics-forced-labour ; news release: https://www.ilo.org/resource/news/annual-profits-forced-labour-amount-us-236-billion-ilo-report-finds
3. United Nations Office on Drugs and Crime (2021). *Global Report on Trafficking in Persons 2020*, Chapter V: Traffickers' use of the internet. Vienna: UNODC. https://www.unodc.org/documents/data-and-analysis/tip/2021/GLOTiP_2020_Chapter5.pdf
4. United Nations Office on Drugs and Crime (2024). "UNODC global human trafficking report: detected victims up 25 per cent as more children are exploited and forced labour cases spike" (press release for the *Global Report on Trafficking in Persons 2024*). https://www.unodc.org/unodc/en/press/releases/2024/December/unodc-global-human-trafficking-report_-detected-victims-up-25-per-cent-as-more-children-are-exploited-and-forced-labour-cases-spike.html ; report series: https://www.unodc.org/unodc/en/data-and-analysis/glotip.html
5. Office of the UN High Commissioner for Human Rights (2023). *Online Scam Operations and Trafficking into Forced Criminality in Southeast Asia: Recommendations for a Human Rights Response.* Bangkok: OHCHR. https://bangkok.ohchr.org/sites/default/files/wp_files/2023/08/ONLINE-SCAM-OPERATIONS-2582023.pdf ; news coverage: https://news.un.org/en/story/2023/08/1140187
6. United Nations Office on Drugs and Crime, Regional Office for Southeast Asia and the Pacific (2023). *Key Indicators of Trafficking in Persons for Forced Criminality to Commit Cyber Enabled Crimes.* https://www.unodc.org/roseap/uploads/documents/Publications/2023/UNODC_Key_Indicators_of_TIP_for_Forced_Criminality_FINAL_September_2023.pdf
7. Defense Advanced Research Projects Agency. *Memex* program page. https://www.darpa.mil/research/programs/memex
8. Szekely, P., Knoblock, C. A., Slepicka, J., Philpot, A., Singh, A., Yin, C., et al. (2015). Building and using a knowledge graph to combat human trafficking. In *The Semantic Web – ISWC 2015*, Lecture Notes in Computer Science. https://doi.org/10.1007/978-3-319-25010-6_12
9. Kejriwal, M., and Szekely, P. (2017). An investigative search engine for the human trafficking domain. In *The Semantic Web – ISWC 2017*, Lecture Notes in Computer Science. https://doi.org/10.1007/978-3-319-68204-4_25
10. Marinus Analytics. *Traffic Jam* product page. https://www.marinusanalytics.com/traffic-jam
11. Dubrawski, A., Miller, K., Barnes, M., Boecking, B., and Kennedy, E. (2015). Leveraging publicly available data to discern patterns of human-trafficking activity. *Journal of Human Trafficking*, 1(1). https://doi.org/10.1080/23322705.2015.1015342
12. Tong, E., Zadeh, A., Jones, C., and Morency, L.-P. (2017). Combating human trafficking with multimodal deep models. In *Proceedings of the 55th Annual Meeting of the Association for Computational Linguistics (Volume 1: Long Papers)*. https://doi.org/10.18653/v1/P17-1142
13. Lee, M.-C., Vajiac, C., Kulshrestha, A., Levy, S., Park, N., Jones, C., Rabbany, R., and Faloutsos, C. (2021). InfoShield: Generalizable information-theoretic human-trafficking detection. In *2021 IEEE 37th International Conference on Data Engineering (ICDE)*, 1116–1127. https://doi.org/10.1109/ICDE51399.2021.00101
14. Vidros, S., Kolias, C., Kambourakis, G., and Akoglu, L. (2017). Automatic detection of online recruitment frauds: Characteristics, methods, and a public dataset. *Future Internet*, 9(1), 6. https://doi.org/10.3390/fi9010006
15. Volodko, A., Cockbain, E., and Kleinberg, B. (2020; online 2019). "Spotting the signs" of trafficking recruitment online: Exploring the characteristics of advertisements targeted at migrant job-seekers. *Trends in Organized Crime*, 23, 7–35. https://doi.org/10.1007/s12117-019-09376-5
16. Giommoni, L. (2024). Why we cannot identify human trafficking from online advertisements. *Journal of Human Trafficking*. https://doi.org/10.1080/23322705.2024.2435198
17. Broder, A. Z. (1997). On the resemblance and containment of documents. In *Proceedings. Compression and Complexity of SEQUENCES 1997*. https://doi.org/10.1109/SEQUEN.1997.666900
18. Indyk, P., and Motwani, R. (1998). Approximate nearest neighbors: Towards removing the curse of dimensionality. In *Proceedings of the 30th Annual ACM Symposium on Theory of Computing (STOC '98)*. https://doi.org/10.1145/276698.276876
19. Rudin, C. (2019). Stop explaining black box machine learning models for high stakes decisions and use interpretable models instead. *Nature Machine Intelligence*, 1, 206–215. https://doi.org/10.1038/s42256-019-0048-x
20. Angelino, E., Larus-Stone, N., Alabi, D., Seltzer, M., and Rudin, C. (2017). Learning certifiably optimal rule lists. In *Proceedings of the 23rd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining*, 35–44. https://doi.org/10.1145/3097983.3098047
21. Chouldechova, A. (2017). Fair prediction with disparate impact: A study of bias in recidivism prediction instruments. *Big Data*, 5(2). https://doi.org/10.1089/big.2016.0047
22. Mitchell, M., Wu, S., Zaldivar, A., Barnes, P., Vasserman, L., Hutchinson, B., et al. (2019). Model cards for model reporting. In *Proceedings of the Conference on Fairness, Accountability, and Transparency (FAT\* '19)*. https://doi.org/10.1145/3287560.3287596
23. Blunt, D., and Wolf, A. (2020). Erased: The impact of FOSTA-SESTA and the removal of Backpage on sex workers. *Anti-Trafficking Review*, 14. https://doi.org/10.14197/atr.201220148
24. Musto, J., Fehrenbacher, A. E., Hoefinger, H., Mai, N., Macioti, P. G., Bennachie, C., et al. (2021). Anti-trafficking in the time of FOSTA/SESTA: Networked moral gentrification and sexual humanitarian creep. *Social Sciences*, 10(2), 58. https://doi.org/10.3390/socsci10020058
