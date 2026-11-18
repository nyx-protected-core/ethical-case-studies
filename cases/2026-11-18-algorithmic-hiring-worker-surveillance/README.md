# Algorithmic Hiring and Worker Surveillance: Fair Work, Bias Audits and Algorithmic Management

*Research case study. Research only: nothing here was built or run. The pseudocode is there to explain the method and is not a tested tool. Legal details are stated as of mid-November 2026 and are not legal advice.*

---

## 1. Introduction and scope

Software now takes part in decisions that used to belong only to managers. It decides who sees a job ad, whose application reaches a human, what a delivery rider is offered for a trip, how fast a warehouse picker must work, and whose account is switched off. This case looks at what that means for fair work.

It covers four things:

1. **Hiring tools.** These include resume screeners, ranking systems, assessments and language-model screening. The case looks at the evidence on bias and at the main laws: New York City's Local Law 144, the EU AI Act and several US state laws.
2. **Algorithmic management.** This covers platform work and warehouse work: how pay is set, deactivation, productivity scoring and surveillance. The main law here is the EU Platform Work Directive.
3. **Recruitment platforms misused by traffickers.** This part is kept short and covers only what platforms' own systems can do. A companion case study on deceptive online job ads covers the topic in detail.
4. **A practical audit checklist** with pseudocode, mapped to the laws.

The case uses neutral protected characteristics as its examples: sex, race and ethnicity, age and disability. It does not try to cover every form of workplace technology, such as robotics or general productivity software.

### 1.1 How widespread are these tools?

| Measure | Figure | Source |
|---|---|---|
| Firms using at least one algorithmic management tool (to instruct, monitor or evaluate workers), 2024 | **United States 90%**; average of four European countries surveyed (France, Germany, Italy, Spain) **79%**; Japan **40%**. Survey of over 6,000 mid-level managers in six countries, June–August 2024. | OECD [1] |
| US firms that monitor the content and tone of conversations, calls or emails | **55%**, compared with **6%** in Europe and **8%** in Japan | OECD [1] |
| Managers with at least one trustworthiness concern about the tools they use | **Nearly two-thirds.** The most common concerns were unclear accountability for a wrong decision (28%), being unable to follow the tool's logic (27%) and poor protection of workers' physical and mental health (27%). | OECD [1] |
| EU-27 establishments using machines, systems or computers that monitor workers' performance or behaviour, 2024 | **7%** on average, rising to **15%** in transport and storage. Survey of 41,458 establishments. | EU-OSHA, ESENER 2024 [2] |
| Organisations using AI in HR, 2025 | **43%**, with **51%** reported as using it for recruitment tasks such as resume screening and candidate communication | SHRM 2025 Talent Trends [3] |

The OECD and EU-OSHA figures look very different, but they measure different things. The OECD asked managers about 15 categories of tools, including simple scheduling and instruction software. EU-OSHA asked a much wider range of establishments, including small ones, about one narrow category: systems that monitor performance or behaviour. Both are useful. Algorithmic management in a broad sense is now normal in larger firms, and intensive monitoring is concentrated in certain sectors, such as transport and storage, and in certain countries.

---

## 2. Resume screening and hiring tools

### 2.1 A well-known failure

In 2018, Reuters reported that Amazon had worked since 2014 on a tool that rated job applicants' resumes [4]. The models were trained on resumes the company had received over a 10-year period, most of them from men. According to the report, the system "taught itself that male candidates were preferable." It penalised resumes containing the word "women's", as in "women's chess club captain", and downgraded graduates of two all-women's colleges. Amazon edited the programs to be neutral to those particular terms, but could not be sure the system would not find other ways to sort candidates unfairly, and the project was dropped. People familiar with the effort said recruiters looked at the tool's recommendations but never relied on them alone. Amazon declined to comment on the tool itself [4].

The case matters because of *how* the bias arose, not because of who built the tool. Nobody told the model to prefer men. It learned that pattern from past decisions, and deleting the obvious signals did not remove it. Barocas and Selbst describe the general mechanism: data mining can inherit earlier discrimination through the training labels, through how the "good employee" target is defined, and through features that act as proxies for protected groups [8].

### 2.2 Evidence on bias in automated hiring

- **Bias by default.** Bogen and Rieke's review of hiring technology for Upturn followed tools across every stage of hiring, from ad targeting to interview analysis. They concluded that "without active measures to mitigate them, bias will arise in predictive hiring tools by default" [6]. They also noted that hiring is "a cumulative series of small decisions," so an unfair filter at an early stage, such as who is shown an ad, may never appear in a later audit.
- **What vendors disclose.** Raghavan, Barocas, Kleinberg and Levy studied vendors of algorithmic pre-employment assessments and what they publicly said about how their tools were built, validated and checked for bias [5]. Disclosure varied widely. Vendors made consequential choices, such as the prediction target and the training data, that were rarely open to outside view. Some "de-biasing" methods also sit uneasily with US anti-discrimination law, which generally forbids making decisions on the basis of protected characteristics.
- **Language-model resume screening.** Wilson and Caliskan tested text-embedding models used to retrieve and rank resumes across nine occupations, with over 500 resumes and 500 job descriptions [9]. The models significantly favoured White-associated names in **85.1%** of tests and female-associated names in only **11.1%**. Resumes with Black male-associated names were disadvantaged in up to 100% of tests, which supports the view that bias has to be checked for groups that combine characteristics, not one characteristic at a time.
- **Language-model scoring.** Armstrong, Liu, MacNeil and Metaxa asked a widely used language model to score resumes that differed only in names linked to race and sex, across 10 occupations [10]. The scores reflected some stereotype-based bias. When the model was asked to *write* resumes, it gave women less experienced jobs and gave Asian and Hispanic candidates markers of immigrant status, such as non-native English.
- **The wider workplace.** Ajunwa's book-length study of workplace data tracks how hiring tests, personality assessments, wellness programmes and surveillance turn workers into data, and argues that the law has not kept pace [7].

These studies are audits of systems in test conditions, not measures of what happened to real applicants. They show that bias is easy to produce and easy to miss, not how much harm a particular product does.

### 2.3 New York City Local Law 144

Local Law 144 of 2021 regulates "automated employment decision tools" (AEDTs) used in hiring or promotion for jobs in New York City. The city's Department of Consumer and Worker Protection (DCWP) began enforcing it on **5 July 2023** [11].

- **Bias audit.** An employer may use an AEDT only if an independent auditor has audited it in the past year. The audit must report selection or scoring rates and **impact ratios** by sex, by race and ethnicity, and by **intersectional categories**. A category that makes up less than 2% of the audit data may be left out of the calculations [11].
- **Publication.** A summary of the most recent audit must be posted on the employer's website.
- **Notice.** Candidates must be told at least **10 business days** before the tool is used [11].
- **Penalties.** Civil penalties run from $500 to $1,500 per day [13].
- **No duty to act on results.** In DCWP's words, "the Law does not require any specific actions based on the results of a bias audit." Federal, state and city anti-discrimination law still applies [11].

**Evidence on compliance is weak.**

- Wright and colleagues sent 155 student investigators to check 391 employers [12]. They found only **18** posted audit reports and **13** posted transparency notices. Because the law lets employers decide for themselves whether their tool is in scope, the authors could not say that missing reports meant breaking the law. They called this situation "null compliance." Nearly all the audits that were published reported impact ratios above 0.8.
- The New York State Comptroller audited DCWP's enforcement for July 2023 to June 2025 and published the results in **December 2025** [13]. DCWP had received only **two** AEDT complaints in that period and had not tested whether its complaint intake worked. The auditors found that complaints made through the city's 311 line were not reliably routed to DCWP. DCWP reviewed 32 companies and found one issue of non-compliance. The Comptroller's auditors reviewed the same companies and found **at least 17** instances of potential non-compliance.

The lesson is that publishing audits does not protect applicants unless someone checks them and the law says what must happen when an audit shows a gap.

### 2.4 The EU AI Act

The AI Act is Regulation (EU) 2024/1689 [14].

- **Employment is high-risk.** Annex III, point 4, lists AI systems used (a) for recruitment or selection, "in particular to place targeted job advertisements, to analyse and filter job applications, and to evaluate candidates", and (b) to make decisions on terms of work, promotion or termination, to allocate tasks based on behaviour or personal traits, or to monitor and evaluate workers' performance and behaviour [14]. High-risk systems carry duties on risk management, data governance, documentation, transparency, human oversight, accuracy and robustness. Employers that deploy such a system must inform workers' representatives and the affected workers before using it at the workplace (Article 26(7)).
- **Emotion recognition at work is banned.** Article 5(1)(f) bans AI systems that infer the emotions of a person in the workplace or in education, except for medical or safety reasons. The bans have applied since **2 February 2025** [14].
- **Right to an explanation.** Article 86 gives a person affected by a decision based on a high-risk Annex III system's output, where the decision has legal or similarly significant effects, the right to a clear explanation of the AI system's role in it [14].
- **The high-risk dates were moved.** Under the original timetable, the Annex III high-risk rules would have applied from 2 August 2026. The "Digital Omnibus on AI", Regulation (EU) 2026/1744, changed this. Parliament approved it on 16 June 2026, the Council adopted it on 29 June 2026, and it was published in the Official Journal on 24 July 2026 [15]. **The high-risk obligations for Annex III systems, which include employment, now apply from 2 December 2027.** High-risk AI built into products under Annex I follows from 2 August 2028 [15]. The ban on emotion recognition at work was not delayed.

### 2.5 United States: federal guidance and state laws

- **Federal guidance was withdrawn, but the law was not.** The EEOC's technical assistance on assessing adverse impact from software, algorithms and AI under Title VII was removed from the agency's website in January 2025 [16]. Its address returned "not found" when checked for this case in November 2026. The guidance was never binding. Title VII's ban on disparate impact still applies, and so do the Uniform Guidelines on Employee Selection Procedures, which contain the four-fifths rule of thumb [17] (see Section 5).
- **Vendor liability is being tested in court.** In May 2025, a federal court in the Northern District of California granted preliminary certification of a nationwide collective of applicants aged 40 and over. They allege that Workday's AI screening led to their applications being rejected on grounds of age (No. 3:23-cv-00770-RFL) [18]. Preliminary certification is an early procedural step and is not a finding of discrimination.
- **Illinois, AI Video Interview Act (820 ILCS 42), in force since 1 January 2020.** An employer that uses AI to analyse recorded video interviews must, before the interview: tell applicants AI may be used, explain how it works and what general characteristics it evaluates, and obtain consent. It may not use AI to evaluate applicants who have not consented. Sharing of the videos is limited, and they must be deleted within 30 days of a request. Employers that rely *solely* on AI analysis to decide who gets an in-person interview must report the race and ethnicity of applicants who are and are not selected, and of those hired, to a state department every year [19].
- **Colorado.** The Colorado AI Act (SB 24-205) never took effect. It was due to start on 30 June 2026, but it was repealed and replaced by **SB 26-189**, signed on 14 May 2026 [20]. The new law covers "automated decision-making technology" used in consequential decisions, including employment. It requires documentation from developers, notice to consumers, a plain-language explanation within 30 days after an adverse decision, and a right to correct inaccurate personal data. It takes effect on **1 January 2027** [20]. A federal court stayed enforcement of the earlier law in April 2026 in a constitutional challenge, and a law-firm analysis reports that the stay also covers the replacement law. The new law has no private right of action [21].

---

## 3. Platform work and algorithmic management

Algorithmic management means using software to direct, evaluate and discipline workers. Kellogg, Valentine and Christin's review sorts it into six mechanisms, the "6 Rs": algorithms *direct* workers by **restricting** and **recommending**, *evaluate* them by **recording** and **rating**, and *discipline* them by **replacing** and **rewarding** [22]. They also describe how workers push back, which they call "algoactivism."

### 3.1 Pay setting

- **Information asymmetry.** Rosenblat and Stark spent nine months studying ride-hail drivers. They found the company exercised "significant indirect control" through its app, and that the gap in information and power between platform and driver was "fundamental" to that control [23].
- **Algorithmic wage discrimination.** Dubal describes "a practice in which individual workers are paid different hourly wages—calculated with ever-changing formulas using granular data on location, individual behavior, demand, supply, or other factors—for broadly similar work" [24]. The result can be a labour market where people "doing the same work, with the same skill, for the same company, at the same time may receive different hourly pay." Because the formula is hidden and changes all the time, workers cannot easily see whether pay is fair, compare offers, or bargain over it.
- **Remote gig work.** Wood, Graham, Lehdonvirta and Hjorth interviewed 107 workers and surveyed 679 in Southeast Asia and Sub-Saharan Africa [25]. Algorithmic control gave workers "high levels of flexibility, autonomy, task variety and complexity." It could also bring "low pay, social isolation, working unsocial and irregular hours, overwork, sleep deprivation and exhaustion." Both findings matter for a fair assessment.

### 3.2 Deactivation without due process

On a platform, being "deactivated" works like being dismissed, but it usually happens without a hearing.

- **A California survey.** A 2023 survey of 810 current and former ride-hail drivers in California found that two-thirds had been deactivated at least once [26]. That included **69%** of drivers of colour and **57%** of drivers who identify as white. **30%** of deactivated drivers were given no explanation at all. The survey was spread through driver networks and online ads, and its authors say respondents with deactivation experiences may have been more likely to take part. It describes the drivers who responded, not all drivers.
- **A Dutch court ruling.** On 4 April 2023, the Amsterdam Court of Appeal ruled on drivers whose accounts Uber had deactivated for alleged fraud [27]. Under the GDPR, people have a right to information about decisions made solely by automated means. For three of the four drivers, the court held that Uber had not shown real human involvement in the deactivation, and it ordered Uber to provide the information they had asked for, with a penalty of €4,000 per day of delay. For the fourth driver, a personal conversation before deactivation was enough to show human involvement. The ruling makes a practical point: a person simply approving the system's output does not count as human review.

### 3.3 Surveillance and productivity scoring

The highest monitoring rates appear in warehouses, logistics and call centres (Section 1.1). Warehouse "rate" systems measure each picker's speed against a quota.

**California AB 701**, the Warehouse Quotas law, took effect on **1 January 2022** [28]. It applies to employers with 100 or more employees at one warehouse distribution centre, or 1,000 or more across the state. It requires:

- a written description of each quota and of any consequences for missing it;
- a ban on quotas that prevent workers from taking meal or rest breaks, using the bathroom (including reasonable travel time), or complying with health and safety standards;
- a right for current and former employees to get their quota and their last **90 days** of personal work-speed data within **21 days** of asking.

The law does not ban quotas. It makes them visible and puts a limit on them.

### 3.4 The EU Platform Work Directive, Directive (EU) 2024/2831

The Directive [33] entered into force on **1 December 2024**. Member States must transpose it by **2 December 2026** [29][30].

- **Presumption of employment.** If there are facts indicating that the platform directs and controls the work, the relationship is legally presumed to be employment. The platform can rebut the presumption, but the burden of proof is on the platform [29][30].
- **Limits on data processing.** Platforms may not use automated monitoring or decision-making systems to process personal data on a worker's **emotional or psychological state**, data on private conversations, data collected while the person is not working, data used to predict trade-union activity or the exercise of other fundamental rights, or data used to infer racial or ethnic origin, migration status, political opinions, religious beliefs, disability, health, sex life or sexual orientation. Biometric data is also excluded, except for authentication [29][30].
- **Transparency.** Workers must be told about the automated monitoring and decision-making systems in use, and platforms must give specified information to workers' representatives and public authorities [30][31].
- **Human oversight.** Platforms must monitor and evaluate the impact of automated decisions, including on working conditions and equal treatment. The people doing the oversight need "the competence, training and authority" to do it, including "the right to override automated decisions" [29].
- **Human decision and review.** Any decision to restrict, suspend or terminate an account or contractual relationship, or with a similarly serious effect, must be taken by a human. Workers have the right to an explanation, a review, and correction or compensation if a decision infringed their rights [31].
- **Who is covered.** Several of these data and automation safeguards in Articles 7–11 apply to everyone who performs platform work, including people classified as self-employed, and do not depend on the employment presumption [31].

**Transposition status.** An October 2026 comparative study of draft national laws in Austria, Czechia, Estonia, the Netherlands and Sweden found that none of them was a complete model. Several, however, keep the rule that a human must take serious decisions such as account suspension [31]. Whether every Member State meets the 2 December 2026 deadline was not known when this case was written.

---

## 4. Recruitment platforms as a trafficking channel

Traffickers use ordinary job boards and social-media recruitment to reach people looking for work, especially work abroad. A companion case study on deceptive online job ads covers the detection methods and the evidence. This section covers only what a recruitment platform's *own* hiring and matching systems can do.

The clearest standard comes from the ILO's *General Principles and Operational Guidelines for Fair Recruitment*. It says that "no recruitment fees or related costs should be charged to, or otherwise borne by, workers or jobseekers", and that employers, not workers, should bear the cost of recruitment [32]. A platform can turn this into checks it runs itself:

1. **Verify employers and recruiters** before they can post ads abroad, for example by checking a recruitment-agency licence where the law requires one.
2. **Flag ads or messages that ask candidates for money**, such as "placement fees", "visa fees" or "training deposits".
3. **Flag contact details, such as phone numbers, messaging handles or payment accounts, reused across unrelated employers.**
4. **Send flagged cases to trained reviewers, not automatic bans.** The fairness concerns in this case apply here too: a filter that over-flags ads from certain countries or languages can harm honest employers and the jobseekers who rely on them.

The matching algorithm itself matters as well. If ranking rewards "fast-response" or "high-pay" ads with no checks, it can push the most deceptive offers to the top.

---

## 5. Practical audit checklist and pseudocode

This checklist is meant for an independent auditor, a regulator, or an internal team that is kept separate from the people who built the system. It covers hiring tools, such as screening, ranking and assessment, and management tools, such as pay setting, task allocation, scoring and deactivation.

### 5.1 Checklist

| # | Item | What to check | Mapping |
|---|---|---|---|
| 1 | **Inventory and purpose** | List every automated tool that affects hiring, pay, task allocation, ratings, discipline or termination. For each one, record its purpose, the decision it supports, who owns it, the vendor, and whether a human can override it. | LL144 (is it an AEDT that "substantially assists" a decision?); AI Act Annex III point 4; PWD automated monitoring and decision systems |
| 2 | **Data provenance** | Where do the training data and labels come from? Do past human decisions serve as "ground truth"? Which groups are missing or under-represented? | AI Act Art. 10 (data governance) |
| 3 | **Legal basis and prohibited inputs** | Is there a lawful basis for each data category? Does the tool infer emotions or use any category the PWD bans? | AI Act Art. 5(1)(f); PWD data limits; GDPR |
| 4 | **Selection-rate impact ratios** | Selection or scoring rate for each group, divided by the rate for the most-selected group. Report results by sex, by race and ethnicity, and by intersectional categories, plus age and disability where the data exists lawfully. | LL144 rules (required); UGESP four-fifths rule; AI Act Art. 9–10 |
| 5 | **Statistical tests and small samples** | Use exact tests and confidence intervals, not only the 0.8 rule of thumb. Mark small groups as "not enough data" instead of passing them. Pool data over time only when the tool has not changed. | UGESP 1607.4(D) |
| 6 | **Validity and job-relatedness** | Does the score predict real job performance, measured by something other than past manager ratings alone? Is each feature related to the job? | UGESP validation; Title VII business necessity |
| 7 | **Proxy detection** | Can the protected characteristic be predicted from the model's inputs? Which features drive that? Examples include postcode, gaps in work history, college names and the "style" of the text. | AI Act Art. 10 (bias examination) |
| 8 | **Robustness and counterfactual tests** | Swap names, pronouns or group-linked terms on otherwise identical resumes or profiles. Score changes should be close to zero. | AI Act Art. 15 (accuracy, robustness) |
| 9 | **Explanation and notice** | Are candidates and workers told before use? Can a person get a plain-language reason for an outcome? | LL144 10-business-day notice; AI Act Art. 26(7), Art. 86; PWD transparency; Illinois AIVIA; Colorado SB 26-189 |
| 10 | **Human review and appeals** | Can reviewers override the tool, and do they? Count how often overrides happen. A near-zero override rate is a warning sign that review is a rubber stamp. Is there a working appeal route with time limits? | AI Act Art. 14, 26; PWD human oversight and review |
| 11 | **Deactivation due process** | Every suspension or termination is decided by a named human. The worker gets the reason and the evidence, a chance to respond, and reinstatement or compensation if the decision was wrong. Compare deactivation rates by group. | PWD (human decision for account restriction or termination); GDPR Art. 22 case law |
| 12 | **Surveillance minimisation** | Is each monitoring stream necessary and proportionate? No emotion inference, no tracking off shift, no monitoring of private conversations. Can workers see their own data? Quotas must leave room for breaks and safety. | AI Act Art. 5(1)(f); PWD data limits; California AB 701 |
| 13 | **Pay-setting transparency** | Can workers see how pay offers are calculated? Do people doing similar work at the same time receive different pay? Compare by group. | PWD transparency |
| 14 | **Monitoring and re-audit cadence** | Re-audit at least every year, and after any retraining, change of vendor model, change of threshold or change in who applies. Track drift in impact ratios between audits. | LL144 (one-year validity); AI Act post-market monitoring |
| 15 | **Auditor independence** | Is the auditor free of financial ties to the vendor or employer? Does the auditor get raw data, not just the vendor's summaries? | LL144 independent auditor |
| 16 | **Publication and action** | Publish a summary with results for every group, including those excluded as too small, and state what was changed. Decide in advance what a failing result triggers, such as a pause, a fix or retirement of the tool. | LL144 requires publication but not action; good practice goes further |

### 5.2 Pseudocode (illustration only, not run)

```text
# ---------- Inputs ----------
# decisions: one row per candidate or worker, with
#   group fields (sex, race_ethnicity, age_band, disability, where lawfully held),
#   outcome (selected / scored / deactivated), score, tool_version, date
# MIN_N: smallest group size to report a ratio (for example 30), set before the audit
# ALPHA: significance level, set before the audit

function impact_ratios(decisions, group_field):
    rates = {}
    for g in distinct(decisions[group_field]):
        rows = decisions where group_field == g
        if count(rows) < MIN_N:
            rates[g] = INSUFFICIENT_DATA        # report it, never silently drop it
        else:
            rates[g] = count(rows where outcome == SELECTED) / count(rows)
    reference = group with the highest rate among groups with enough data
    for g in rates:
        if rates[g] is not INSUFFICIENT_DATA:
            ratio = rates[g] / rates[reference]
            p = fisher_exact_test(g, reference)          # exact test suits small cells
            ci = confidence_interval_for_ratio(g, reference)
            flag = (ratio < 0.8) or (p < ALPHA and ratio < 1)
            emit(g, rates[g], ratio, ci, p, flag)
    return results

function intersectional_audit(decisions):
    # combine fields, e.g. sex x race_ethnicity, as LL144 rules require
    decisions.cell = concat(sex, race_ethnicity)
    return impact_ratios(decisions, "cell")

function proxy_check(features, protected_attribute):
    # if a simple model predicts the protected attribute from the tool's inputs
    # much better than chance, those inputs can stand in for it
    model = train_simple_classifier(features -> protected_attribute)
    auc = cross_validated_auc(model)
    top = features_ranked_by_importance(model)
    return auc, top            # investigate top features if auc is clearly above 0.5

function counterfactual_test(tool, profiles, swaps):
    # swaps: pairs of names or terms linked to different groups
    diffs = []
    for p in profiles:
        for (a, b) in swaps:
            diffs.append(tool.score(replace(p, a, b)) - tool.score(p))
    return mean(diffs), share_of(diffs where abs(d) > TOLERANCE)

function validity_check(scores, later_performance):
    # later_performance must not be just past ratings by the same managers
    return correlation(scores, later_performance), by_group(correlation)

function deactivation_due_process(events):
    for e in events where type in {RESTRICT, SUSPEND, TERMINATE}:
        require e.decided_by_human and e.reviewer_had_authority_to_override
        require e.reason_given and e.evidence_shared
        require e.appeal_offered and e.appeal_resolved_within_time_limit
    report share_failing_each_requirement
    report impact_ratios(events, group_field) using outcome == TERMINATE
    report override_rate = count(human decision differs from system) / count(events)

function audit(system):
    for each group_field in [sex, race_ethnicity, age_band, disability]:
        impact_ratios(system.decisions, group_field)
    intersectional_audit(system.decisions)
    proxy_check(system.features, each protected attribute held lawfully)
    counterfactual_test(system.tool, sample_profiles, name_and_term_swaps)
    validity_check(system.scores, system.later_performance)
    if system.makes_management_decisions:
        deactivation_due_process(system.events)
    publish(summary including INSUFFICIENT_DATA groups and actions taken)
    schedule_reaudit(min(12 months, next model or threshold change))
```

### 5.3 Notes on the method

- **The four-fifths rule is a rule of thumb, not a legal line.** The Uniform Guidelines say that a ratio below 0.8 "will generally be regarded" as evidence of adverse impact. They also say that smaller differences can still count as adverse impact when they are significant, and that larger ones may not when the numbers are small [17]. An audit that reports only "above 0.8, pass" says little, and nearly all published LL144 audits reported exactly that [12].
- **Small samples cut both ways.** A tiny group can show a large gap by chance, or hide a real one. Mark these groups clearly, use exact tests, and pool data over a longer period if the tool has not changed.
- **Leaving out small categories can hide the worst outcomes.** LL144 allows categories under 2% of the data to be dropped [11]. Intersectional groups are often the smallest, and Wilson and Caliskan found some of the largest disadvantages there [9].
- **Validity comes first.** A tool with equal selection rates that predicts nothing is still unfair to everyone it rejects. A recent re-analysis of personnel-selection research found that earlier validity estimates were overstated by about 0.10–0.20, with structured interviews coming out on top [34]. The validity of a vendor's score should be tested in the setting where it is used, not assumed.

---

## 6. Fair counterarguments

### 6.1 Efficiency and scale

Employers that receive thousands of applications for each opening cannot read them all with care. Screening tools make it possible to give every application *some* review instead of none. Platforms that match millions of trips or tasks a day cannot do it by hand. Many managers in the OECD survey said the tools improved their decisions (60%) and their own job satisfaction [1]. These are real benefits.

### 6.2 Humans are biased too

The baseline is not a fair human process.

- In Bertrand and Mullainathan's field experiment, identical resumes with White-sounding names got **50% more callbacks** than those with African-American-sounding names [35].
- Quillian, Pager, Hexel and Midtbøen's meta-analysis of field experiments found that since 1989, White applicants received on average **36% more callbacks** than African Americans and **24% more** than Latinos. It found **no change** in hiring discrimination against African Americans over 25 years [36].

Any judgement of algorithmic hiring has to ask: compared with what?

### 6.3 Algorithms can beat human judgement

- Hoffman, Kahn and Li studied job testing at 15 firms. Managers who hired against the test's recommendations ended up with worse hires on average, which suggests they overruled the test because of bias or error, not because they knew something better [37].
- Li, Raymond and Bergman built a resume screener that rewards "exploration", meaning it gives candidates from under-represented groups a chance so that it can learn about them [38]. It raised both the quality of candidates selected for interview and their demographic diversity. A standard supervised-learning screener raised quality but selected far fewer Black and Hispanic applicants. How the algorithm is designed matters more than whether one is used.
- Cowgill's model shows when an algorithm trained on biased human decisions can *reduce* that bias. This happens when the human decisions were noisy enough to give it useful variation to learn from. When they were not, the algorithm will "codify or exacerbate existing biases" [39].
- Kleinberg, Ludwig, Mullainathan and Sunstein argue that, with the right record-keeping, algorithms make discrimination *easier to detect* than human decisions do, because their inputs and trade-offs can be inspected [40].

### 6.4 Flexibility that workers value

Chen, Chevalier, Rossi and Oehlsen estimate that ride-hail drivers earn more than twice the surplus they would get in less flexible arrangements, because they can choose when to work [41]. Wood and colleagues also found real autonomy in remote gig work [25]. Rules that force platform work into rigid schedules could take away something many workers want. The Platform Work Directive's presumption of employment is rebuttable, and its data and automation safeguards apply whatever a worker's status. Both points partly answer this concern [29][31].

### 6.5 Compliance costs for small firms

Audits, documentation and appeal processes cost money, and the cost falls hardest on small employers and small vendors. The EU itself delayed the AI Act's high-risk rules because standards and guidance were late and implementation costs risked rising sharply [15]. An audit regime that only large firms can afford may push small firms towards opaque off-the-shelf tools whose vendors do not share data. A lighter, standardised approach for small users, with the burden of proof placed on vendors, is one way to answer this.

### 6.6 How these arguments balance

These counterarguments are strong against *banning* algorithmic tools. They are weak against *auditing* them. Section 6.3 shows that the good results came from tools that were designed carefully and measured. That is what this case recommends, not a return to unexamined human judgement.

---

## 7. Recommendations, ranked by strength of evidence

**Strong evidence**

1. **Audit before use and every year, with results for intersectional groups and real statistical tests, not only the 0.8 rule.** Bias appears by default [6][9], and pass/fail ratios hide it [12][17].
2. **Require action, not just publication.** LL144 shows that publishing alone, with weak enforcement, gives little protection [12][13]. Set in advance what a failing audit triggers.
3. **Make human review real.** Reviewers need the authority, time and information to override the tool, and override rates should be measured. Courts and the Platform Work Directive already treat human sign-off with no real review as automated decision-making [27][29][31].
4. **Give a reason, a review and a remedy for deactivation and other serious decisions** [26][27][31].

**Moderate evidence**

5. **Test validity in the setting where the tool is used.** Treat vendor claims as unproven until shown [5][34].
6. **Design for exploration and fairness, not just for imitating past hiring** [38][39].
7. **Keep surveillance to what is necessary: no emotion inference, no tracking off shift, and quotas that leave room for breaks and safety** [14][28][29].
8. **Make pay formulas visible to workers and compare pay between groups doing similar work** [23][24].

**Emerging evidence, or based on principle**

9. **Recruitment platforms should verify employers, flag requests for fees, and flag reused contacts, with human review of flags** [32].
10. **Give small users standard audit templates and put the main audit burden on vendors** [15].
11. **Track enforcement, not just laws.** Several of the laws described here have changed dates or been replaced since they were passed [15][20][21].

---

## 8. References

Each DOI was resolved through the Crossref API, and its title, authors and year were checked. Each web link was opened, and the figures or legal details cited were checked against the page, except where a note says otherwise.

1. Milanez, A., Lemmens, A. & Ruggiu, C. (2025). *Algorithmic management in the workplace: New evidence from an OECD employer survey.* OECD Artificial Intelligence Papers. https://doi.org/10.1787/287c13c4-en
2. European Agency for Safety and Health at Work (2025). *European Survey of Enterprises on New and Emerging Risks (ESENER 2024): First findings.* https://osha.europa.eu/sites/default/files/documents/first-findings-esener-2024_EN.pdf
3. SHRM (2025). "Candidate 'ghosting' and employer competition are fueling talent shortages, new SHRM research finds" (press release for *2025 Talent Trends*). https://www.shrm.org/about/press-room/candidate--ghosting--and-employer-competition-are-fueling-talent
4. Dastin, J. (2018, 10 October). Amazon scraps secret AI recruiting tool that showed bias against women. Reuters. https://www.reuters.com/article/us-amazon-com-jobs-automation-insight/amazon-scraps-secret-ai-recruiting-tool-that-showed-bias-against-women-idUSKCN1MK08G/ (Reuters blocked automated access. The text was checked in the full Reuters copy published by *The Guardian*: https://www.theguardian.com/technology/2018/oct/10/amazon-hiring-ai-gender-bias-recruiting-engine)
5. Raghavan, M., Barocas, S., Kleinberg, J. & Levy, K. (2020). Mitigating bias in algorithmic hiring: Evaluating claims and practices. In *Proceedings of the 2020 Conference on Fairness, Accountability, and Transparency (FAT\*)*, 469–481. https://doi.org/10.1145/3351095.3372828
6. Bogen, M. & Rieke, A. (2018). *Help Wanted: An Examination of Hiring Algorithms, Equity, and Bias.* Upturn. https://www.upturn.org/work/help-wanted/
7. Ajunwa, I. (2023). *The Quantified Worker: Law and Technology in the Modern Workplace.* Cambridge University Press. https://doi.org/10.1017/9781316888681
8. Barocas, S. & Selbst, A. D. (2016). Big data's disparate impact. *California Law Review*, 104(3), 671–732. SSRN version: https://doi.org/10.2139/ssrn.2477899
9. Wilson, K. & Caliskan, A. (2024). Gender, race, and intersectional bias in resume screening via language model retrieval. In *Proceedings of the AAAI/ACM Conference on AI, Ethics, and Society*, 7(1), 1578–1590. https://doi.org/10.1609/aies.v7i1.31748
10. Armstrong, L., Liu, A., MacNeil, S. & Metaxa, D. (2024). The silicon ceiling: Auditing GPT's race and gender biases in hiring. In *Proceedings of the 4th ACM Conference on Equity and Access in Algorithms, Mechanisms, and Optimization (EAAMO '24)*, 1–18. https://doi.org/10.1145/3689904.3694699
11. New York City Department of Consumer and Worker Protection. *Automated Employment Decision Tools (AEDT)* page and *Frequently Asked Questions*. https://www.nyc.gov/site/dca/about/automated-employment-decision-tools.page and https://www.nyc.gov/site/dca/businesses/faq-automated-employment-decision-tools.page
12. Wright, L., Muenster, R. M., Vecchione, B., Qu, T., Cai, P., Smith, A., Comm 2450 Student Investigators, Metcalf, J. & Matias, J. N. (2024). Null compliance: NYC Local Law 144 and the challenges of algorithm accountability. In *Proceedings of the 2024 ACM Conference on Fairness, Accountability, and Transparency (FAccT '24)*, 1701–1713. https://doi.org/10.1145/3630106.3658998
13. Office of the New York State Comptroller (2025, December). *Department of Consumer and Worker Protection: Enforcement of Local Law 144 – Automated Employment Decision Tools.* Report 2024-N-6. https://www.osc.ny.gov/files/state-agencies/audits/pdf/sga-2026-24n6.pdf
14. Regulation (EU) 2024/1689 (Artificial Intelligence Act), Article 5, Article 26, Article 86, Article 113 and Annex III. Official text: https://eur-lex.europa.eu/eli/reg/2024/1689/oj/eng (EUR-Lex blocked automated access. The provisions were checked in the consolidated article-by-article text at https://artificialintelligenceact.eu/annex/3/ and https://artificialintelligenceact.eu/article/5/, which shows the 2026 amendments to Article 113.)
15. Regulation (EU) 2026/1744 (Digital Omnibus on AI). Official text: https://eur-lex.europa.eu/eli/reg/2026/1744/oj/eng (EUR-Lex blocked automated access. The dates and adoption steps were checked on the European Parliament Legislative Train page, https://www.europarl.europa.eu/legislative-train/package-digital-package/file-digital-omnibus-on-ai, and in the Council press release of 29 June 2026, https://www.consilium.europa.eu/en/press/press-releases/2026/06/29/artificial-intelligence-council-gives-final-green-light-to-simplify-and-streamline-rules/, which was read through a search index because the site blocked direct fetching.)
16. Paul, C. B. (2026, 31 March). The federal government removed its hiring guidance. *National Law Review.* https://natlawreview.com/article/federal-government-quietly-removed-its-ai-hiring-guidance-four-states-are-writing (The removal was also confirmed directly: the former EEOC guidance address returned HTTP 404 in November 2026.)
17. Uniform Guidelines on Employee Selection Procedures, 29 C.F.R. § 1607.4(D) (adverse impact and the "four-fifths rule"). https://www.ecfr.gov/current/title-29/subtitle-B/chapter-XIV/part-1607/section-1607.4
18. U.S. District Court for the Northern District of California (2025, 16 May). Order granting preliminary collective certification, No. 3:23-cv-00770-RFL (case against Workday, Inc.). https://www.govinfo.gov/content/pkg/USCOURTS-cand-3_23-cv-00770/pdf/USCOURTS-cand-3_23-cv-00770-1.pdf
19. Illinois Artificial Intelligence Video Interview Act, 820 ILCS 42 (P.A. 101-260, effective 1 January 2020; Section 20 added by P.A. 102-47). https://www.ilga.gov/legislation/ilcs/ilcs3.asp?ActID=4015&ChapterID=68
20. Colorado Attorney General. *Colorado Automated Decision-Making Technology rulemaking* (SB 24-205 and SB 26-189). https://coag.gov/ai/ ; bill page: https://leg.colorado.gov/bills/sb26-189 (The legislature's page timed out on direct fetch. Its summary and signing date of 14 May 2026 were read through a search index and match the Attorney General's page.)
21. Eckert Seamans (2026, 12 June). Colorado repeals and replaces its landmark AI statute: What businesses need to know. https://www.eckertseamans.com/legal-updates/colorado-repeals-and-replaces-its-landmark-ai-statute-what-businesses-need-to-know (A secondary legal analysis, used only for the federal stay and for remedies.)
22. Kellogg, K. C., Valentine, M. A. & Christin, A. (2020). Algorithms at work: The new contested terrain of control. *Academy of Management Annals*, 14(1), 366–410. https://doi.org/10.5465/annals.2018.0174
23. Rosenblat, A. & Stark, L. (2016). Algorithmic labor and information asymmetries: A case study of Uber's drivers. *International Journal of Communication*, 10, 3758–3784. https://ijoc.org/index.php/ijoc/article/view/4892
24. Dubal, V. (2023). On algorithmic wage discrimination. *Columbia Law Review*, 123(7). https://columbialawreview.org/content/on-algorithmic-wage-discrimination/ ; SSRN: https://doi.org/10.2139/ssrn.4331080
25. Wood, A. J., Graham, M., Lehdonvirta, V. & Hjorth, I. (2019; online 2018). Good gig, bad gig: Autonomy and algorithmic control in the global gig economy. *Work, Employment and Society*, 33(1), 56–75. https://doi.org/10.1177/0950017018785616
26. Asian Americans Advancing Justice – Asian Law Caucus & Rideshare Drivers United (2023, February). *Fired by an App: The Toll of Secret Algorithms and Unchecked Discrimination on California Rideshare Drivers.* https://www.asianlawcaucus.org/news-resources/guides-reports/fired-by-an-app-report
27. Gerechtshof Amsterdam (Amsterdam Court of Appeal) (2023, 4 April). ECLI:NL:GHAMS:2023:793, case 200.295.742/01. https://uitspraken.rechtspraak.nl/details?id=ECLI:NL:GHAMS:2023:793
28. California Labor Commissioner's Office. *Frequently Asked Questions on Warehouse Quotas (Assembly Bill 701).* https://dir.ca.gov/dlse/FAQ_warehousequotas.htm
29. European Agency for Safety and Health at Work. *Directive 2024/2831/EU – platform work* (legislation summary). https://osha.europa.eu/en/legislation/directive/directive-20242831eu-platform-work
30. European Parliament Legislative Observatory. Document summary of the final act, Directive (EU) 2024/2831. https://oeil.europarl.europa.eu/oeil/en/document-summary?id=1794723
31. Guinea, O., Pandya, D. & Sisto, E. (2026, October). *Transposing the Platform Work Directive: Comparative Assessment and Good Practices.* ECIPE Policy Brief. https://ecipe.org/publications/transposing-platform-work-directive/
32. International Labour Organization (2019). *General Principles and Operational Guidelines for Fair Recruitment and Definition of Recruitment Fees and Related Costs.* Geneva: ILO. https://www.ilo.org/sites/default/files/wcmsp5/groups/public/@ed_protect/@protrav/@migrant/documents/publication/wcms_536263.pdf
33. Directive (EU) 2024/2831 on improving working conditions in platform work. Official text: https://eur-lex.europa.eu/eli/dir/2024/2831/oj/eng (EUR-Lex blocked automated access. The content was checked through official EU summaries [29][30].)
34. Sackett, P. R., Zhang, C., Berry, C. M. & Lievens, F. (2022; online 2021). Revisiting meta-analytic estimates of validity in personnel selection: Addressing systematic overcorrection for restriction of range. *Journal of Applied Psychology*, 107(11), 2040–2068. https://doi.org/10.1037/apl0000994
35. Bertrand, M. & Mullainathan, S. (2004). Are Emily and Greg more employable than Lakisha and Jamal? A field experiment on labor market discrimination. *American Economic Review*, 94(4), 991–1013. https://doi.org/10.1257/0002828042002561
36. Quillian, L., Pager, D., Hexel, O. & Midtbøen, A. H. (2017). Meta-analysis of field experiments shows no change in racial discrimination in hiring over time. *Proceedings of the National Academy of Sciences*, 114(41), 10870–10875. https://doi.org/10.1073/pnas.1706255114
37. Hoffman, M., Kahn, L. B. & Li, D. (2018; online 2017). Discretion in hiring. *Quarterly Journal of Economics*, 133(2), 765–800. https://doi.org/10.1093/qje/qjx042
38. Li, D., Raymond, L. R. & Bergman, P. (2020). Hiring as exploration. NBER Working Paper 27736. https://doi.org/10.3386/w27736
39. Cowgill, B. (2019). Bias and productivity in humans and machines. Upjohn Institute Working Paper 19-309. https://doi.org/10.17848/wp19-309
40. Kleinberg, J., Ludwig, J., Mullainathan, S. & Sunstein, C. R. (2018). Discrimination in the age of algorithms. *Journal of Legal Analysis*, 10, 113–174. https://doi.org/10.1093/jla/laz001
41. Chen, M. K., Chevalier, J. A., Rossi, P. E. & Oehlsen, E. (2019). The value of flexible work: Evidence from Uber drivers. *Journal of Political Economy*, 127(6), 2735–2794. https://doi.org/10.1086/702171
