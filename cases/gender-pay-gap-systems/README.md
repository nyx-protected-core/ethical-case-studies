# The Gender Pay Gap from the Systems Side: Pay Transparency, Pay-Setting Design and Auditing Pay Algorithms

Date: 2026-11-18

*Research case study. Research only: nothing here was built or run. The pseudocode is there to explain the method and is not a tested tool.*

---

## 1. Introduction and scope

The gender pay gap is the difference between what men and women earn on average. Many forces drive it. They include which jobs and sectors people work in, working hours, how jobs are valued, how pay is set and negotiated, how performance is judged, and plain discrimination.

**This case focuses on pay structures, incentives, negotiation and evaluation design.** These are the parts of the gap that employers and regulators can see and change directly through rules, data and algorithms. The case looks at four things:

1. the laws that make pay more visible;
2. the evidence on whether those laws narrow the gap;
3. how negotiation, bonus and performance-rating systems create or remove gaps;
4. how to audit pay-setting rules, job-evaluation schemes and pay algorithms for bias.

This is only part of the picture. The case does not claim that these factors explain the whole gap, and it reports unadjusted and adjusted figures the way each source states them. This case examines pay-setting systems, incentive structures and pay transparency only.

### 1.1 How the gap is measured

Most disagreements about "the" pay gap come from comparing different measures.

- **Unadjusted (raw) gap.** This is the difference between men's and women's average pay, given as a percentage of men's pay. It makes no correction for job, hours, age or anything else, so it captures the whole difference, whatever its cause.
- **Adjusted (or "unexplained") gap.** This is what is left after a statistical model accounts for measured differences such as occupation, sector, firm, experience and education. It is often treated as an upper bound on discrimination, but it is neither a clean measure of discrimination nor a lower bound. Missing variables can push it up. Controls that are themselves shaped by bias, such as job grade, can push it down (see Section 6). In long-run US data, standard human-capital variables now explain little of the gap, while differences in occupation and industry remain important [32].
- **Mean or median.** The mean is pulled up by very high earners, who are mostly men in most countries, so a mean gap is usually wider than a median gap. The median shows the typical worker. Good reporting gives both, which is why the UK and EU rules require both.
- **Hourly or annual pay, and who is included.** Hourly pay removes differences in hours worked. Including or leaving out part-time workers can change the result a great deal.

### 1.2 Current figures

| Measure | Figure | Source |
|---|---|---|
| EU unadjusted gap (mean gross hourly earnings, firms with 10+ employees), 2024 | **11.1%** (euro area 11.4%). The range runs from -0.8% in Luxembourg to 18.8% in Estonia. Figures are provisional. | Eurostat [1] |
| EU unadjusted and adjusted gap, 2022 (Structure of Earnings Survey, Oaxaca-Blinder decomposition) | Unadjusted **12.2%**; adjusted ("unexplained") **10.3%**. Measured characteristics explain only about 1.9 points. | Eurostat working paper [2] |
| EU private and public sector, 2024 | Every country with data except Slovenia had a larger gap in the private sector. Eurostat suggests this may be because public pay usually follows open wage grids. | Eurostat [1] |
| UK, full-time employees (median hourly pay excluding overtime), April 2026 | **6.8%**, down from 7.2% in 2025. It was 9.4% a decade earlier. Figures are provisional. | ONS [3] |
| UK, all employees, April 2026 | **12.3%** (13.0% in 2025). Part-time employees: -2.1%. | ONS [3] |
| OECD, median full-time earnings, 2024 | About **10%**. The OECD average has declined by about 9 percentage points since the 1990s. | OECD [4] |

The official EU unadjusted figure of 11.1% is for 2024, from Eurostat Statistics Explained [1]. The unadjusted 12.2% and adjusted 10.3% figures are from the 2022 Structure of Earnings Survey and should not be subtracted from the 2024 official figure [2]. Two points stand out. First, in the 2022 EU decomposition most of the gap is *not* explained by measured characteristics. Eurostat itself says the unexplained figure is best read as a "residual gap", not a measure of discrimination [2]. Second, the ONS warns that its figure "is not … of the difference in pay between men and women for doing the same job" [3]. Population statistics like these cannot tell you whether a given employer pays fairly. That question needs within-firm data and an audit (Section 5).

---

## 2. Pay-transparency laws

### 2.1 EU Pay Transparency Directive, Directive (EU) 2023/970

The Directive was adopted on 10 May 2023 [5]. All details below were checked against the Official Journal text.

- **Pay structures (Art. 4).** Employers must have pay structures that ensure equal pay for equal work or work of equal value. The value of work is to be assessed on objective, gender-neutral criteria that include *skills, effort, responsibility and working conditions*. The text adds that "relevant soft skills shall not be undervalued."
- **Before hiring (Art. 5).** Job applicants have a right to know the starting pay or its range. Employers "shall not ask applicants about their pay history." Job vacancy notices and job titles must be gender-neutral.
- **Pay-setting criteria (Art. 6).** Employers must make the criteria for pay, pay levels and pay progression easily accessible to workers. Member States may exempt employers with fewer than 50 workers from the pay-progression part.
- **Right to information (Art. 7).** Workers can request their own pay level and the average pay, by sex, for workers doing the same work or work of equal value. Employers must answer within two months. Contract terms that stop workers from disclosing their own pay must be prohibited.
- **Gender pay gap reporting (Art. 9).** Employers must report the mean and median gender pay gap, the gaps in variable or complementary pay, the share of each sex receiving variable pay, the share of each sex in each pay quartile, and the gap by category of workers. The schedule depends on size:
  - 250 or more workers: by **7 June 2027**, then every year;
  - 150–249 workers: by **7 June 2027**, then every three years;
  - 100–149 workers: by **7 June 2031**, then every three years;
  - Member States may let or require smaller employers to report.
- **Joint pay assessment, the 5% trigger (Art. 10).** An employer that reports must carry out a joint pay assessment with workers' representatives when *all three* of these conditions are met:
  1. the reporting shows a difference in average pay of **at least 5% in any category of workers**;
  2. the employer has not justified the difference on objective, gender-neutral criteria;
  3. the employer has not remedied it within **six months** of reporting.

  The assessment must find, fix and prevent unjustified differences. The fix must include a review of the employer's gender-neutral job-evaluation and classification systems, or setting such systems up.
- **Burden of proof (Art. 18).** Once a worker shows facts from which discrimination can be presumed, the employer must prove there was none. If the employer has not met its transparency duties under Articles 5, 6, 7, 9 and 10, the burden falls on the employer in any case. The exception is a breach that was "manifestly unintentional and of a minor character."
- **Remedies and penalties (Arts. 16, 23).** Workers are entitled to full compensation, including back pay and related bonuses, with no fixed upper limit set in advance. Member States must lay down penalties that include fines.
- **Transposition (Art. 34).** Member States had to transpose the Directive by **7 June 2026**. Under Article 35, the Commission must review the thresholds and the 5% trigger by 7 June 2033. Progress on transposition varies by country. The OECD notes, for example, that Poland transposed the pre-employment parts in 2025 [4]. Readers should check the national law that applies to them.

### 2.2 Denmark: gender-segregated wage statistics (2006)

Denmark adopted Act no. 562 on 9 June 2006. As the Act is described in [8], employers with at least 35 employees, and at least 10 employees of each gender in an occupation code, had to prepare gender-segregated wage statistics every year. They had to share these with employees through employee representatives, but they did not have to publish them. This is a narrow, inward-facing kind of transparency: firms tell their own staff about their own gaps.

### 2.3 United Kingdom: gender pay gap reporting (2017)

The Equality Act 2010 (Gender Pay Gap Information) Regulations 2017 came into force on 6 April 2017 [6]. They apply to private and voluntary sector employers with **250 or more employees** on the snapshot date of 5 April. Public bodies listed in Schedule 19 of the Equality Act are excluded from these Regulations. Each year, employers must publish:

- the mean and the median gap in hourly pay;
- the mean and the median gap in bonus pay;
- the share of men and of women who received a bonus;
- the share of men and of women in each pay quartile.

They must publish within 12 months of the snapshot date, with a written statement signed by a director or equivalent confirming that the figures are accurate. The UK rules require only reporting. There is no required action plan and no trigger for remedies.

### 2.4 Iceland: equal pay certification and the ÍST 85 standard (2018)

An amendment to Iceland's Gender Equality Act came into force on 1 January 2018. It requires companies and institutions with **25 or more workers** on an annual basis to obtain **equal pay certification** [7]. Accredited auditors check that the employer's pay management system meets the **ÍST 85** Equal Pay Standard. The certificate must be renewed every three years.

Certification was phased in by size:
- 250 or more workers: by the end of 2018;
- 150–249 workers: by the end of 2019;
- 90–149 workers: by the end of 2020;
- 25–89 workers: by the end of 2021.

Under the standard, the employer classifies jobs by their value, analyses pay against that classification, and writes down how pay decisions are made. The government says certification "does not involve any requirement to the effect that individuals should receive exactly the same wages." What it does require is that pay decisions rest only on relevant factors [7]. Workplaces that fail to comply can face daily fines.

Iceland's system is the clearest example of a law that audits the **process** rather than only reporting the **outcome**.

---

## 3. Evidence: do these laws close the gap?

The best studies use natural experiments: size thresholds, staggered rollouts or disclosure cut-offs. Read together, they show three things. Transparency usually narrows gaps a little. It often does so by **slowing men's pay growth rather than raising women's**. And the design details matter a great deal.

| Policy and setting | Main finding | How the gap narrowed |
|---|---|---|
| Denmark, 2006 internal gender wage statistics [8] | The gap fell by about **2 percentage points, or 13%** of its level before the law. | Mainly **slower wage growth for men**. The wage bill fell but profitability did not change, because productivity also fell. |
| UK, 2017 public reporting for employers with 250+ employees [9] | Closed about **19% of the gap**. | **Lower pay growth for men.** The firms most exposed to public scrutiny cut their gaps the most. |
| Canada, public disclosure of university salaries above set thresholds [10] | Cut the gender gap among faculty by about **20–40%**. | The abstract does not say how the narrowing split between men's and women's pay. |
| Austria, 2011 internal pay-gap reports [12] | **No effect** on men's or women's wages. The estimates are precise and rule out a narrowing of more than 0.4 points. There was no wage compression either. | None. |
| UK universities, 2007 public release of average earnings by gender [13] | Female academics' earnings rose about **0.62 points** relative to men's, which stayed flat. The gap fell by **4.37%**. | **Women's pay rose.** The authors' evidence suggests the main reason was that senior women negotiated higher pay. |
| US universities, a pay-transparency shock among about 100,000 academics [14] | Pay became more equitable and more equal. | The link between pay and individually measured performance became much weaker. |
| US state laws protecting workers who discuss pay [11] | Average wages fell by about **2%**. | Employers bargain harder. They refuse to pay any one worker a high wage so they do not have to renegotiate with others. |
| Iceland, equal pay certification [15] | The gap fell by almost 8 points from 2012 to 2020, but trends were similar in certified and uncertified organisations. A **direct effect could not be confirmed**. | Unclear. The authors suggest an indirect effect through public debate. |

### What this means

1. **Transparency works mainly by changing how employers bargain, not by giving women more bargaining power.** The Danish and UK reporting laws narrowed gaps mostly by slowing men's pay [8][9]. Cullen and Pakzad-Hurson give the reason: once pay is visible, a high offer to one worker invites others to renegotiate, so employers hold all wages down [11]. Cullen's review describes the same trade-off. "Horizontal" transparency among coworkers narrows gaps but can lower average pay and lead to harmful comparisons. Transparency *across firms*, such as posted pay ranges, tends to help workers by sharpening competition between employers [16]. Since 2000, 71% of OECD countries have adopted some form of pay transparency [16].
2. **A narrower gap is not the same as better pay for women.** If a gap closes only because men's pay stops growing, women are not worse off, but they are not better off either. Policy goals should say whether the aim is a smaller gap, higher pay for women, or both.
3. **Design matters, and "public versus internal" does not explain the results on its own.** Denmark's statistics were internal (shared with employees through their representatives, not published), yet they narrowed the gap [8]. Austria's reports were also internal and had no measurable effect [12]. Public disclosure plus scrutiny (UK) also worked [9]. Features that differ between Denmark and Austria include how fine the comparison groups are (six-digit occupation codes with at least 10 of each gender in Denmark, collective-agreement pay groups in Austria), annual versus two-yearly reports, and Austria's legal duty on employees to keep the report confidential, backed by a fine [8][12][33]. Which of these matters most has not been tested directly. The OECD's review reaches a similar conclusion: reporting narrows gaps "where design and implementation are done effectively", with simple compliance, third-party involvement and public access as common features of success [4].
4. **Process certification is promising but not yet proven.** Iceland's standard improved how pay is set, but the first evaluation could not separate its effect from the national trend. Researchers also worry that it "lends legitimacy" to differences rooted in occupational segregation [15]. A qualitative study notes that job evaluation has long been criticised for reproducing gender inequality and that work done mostly by women is still undervalued under the standard [17].

---

## 4. Pay structures, incentives and negotiation

### 4.1 Negotiation and the "ask gap"

- **Ambiguity rewards those who push.** In a natural field experiment with nearly 2,500 job seekers, men were more likely to negotiate when job ads did not say pay was negotiable. When ads said explicitly that pay was negotiable, the gender difference "disappear[ed] completely". Men also preferred jobs where the rules for setting pay were ambiguous [18].
- **Asking carries a cost for women.** In four experiments, evaluators penalised women more than men for starting a pay negotiation. Male evaluators penalised women but not men, and female evaluators penalised every candidate who negotiated [19]. Telling women simply to "ask more" therefore misses the problem.
- **Forcing negotiation is not the fix.** In a lab setting, women already knew when to negotiate. They entered negotiations that paid off and avoided those that did not. Making everyone negotiate did not help, and the authors "caution against a greater push for women to negotiate" [20].
- **An anchor can close the ask gap.** On an online platform for engineering jobs, women asked for 2.9% less than comparable men. The gap in employers' bids was 2.2%, and in final offers 1.4%. Once the ask was taken into account, the gaps in bids and final pay disappeared. When the platform pre-filled the ask box with the median bid for similar candidates, the gaps in asks, bids and final offers **went to zero**, and women got no fewer offers [21]. This is a direct example of a system design choice, the default value in a form, removing a gap.
- **Bargaining inside firms.** Portuguese data show that women receive only about 90% of the firm-specific pay premiums that men receive. Sorting across firms and bargaining within them together explain about one-fifth of the gap in Portugal [22].

### 4.2 Salary-history bans

Prior pay is a channel through which past gaps carry into new jobs. Bans on asking about it are a test of that idea.

- California's ban and similar early state bans raised the female-to-male earnings ratio by about **1%**. The rise came mainly from workers over 35 and recent job changers [23].
- After bans, employers **posted wages more often** and paid job changers more, especially women (**+6.2%**) and non-white workers (+5.8%). There was no sign of adverse selection in the short run [24].
- US bans narrowed the gap by about **2 points**, almost entirely by raising **women's** earnings. They also weakened the link between past and future pay [25].

The Directive now bans pay-history questions across the EU (Art. 5(2)) [5]. For algorithm design, the lesson is that **prior salary is a proxy that carries past bias forward** (see Section 5.6).

### 4.3 Bonuses, merit pay and performance ratings

- **Performance-reward bias.** In personnel data from a large service organisation, women and minorities received smaller salary increases than white men with *the same* performance scores, even with job, work unit and supervisor held constant [26]. Merit systems "with limited transparency and accountability can actually increase ascriptive bias."
- **The paradox of meritocracy.** In three experiments with 445 people with management experience, managers gave larger bonuses to men than to equally performing women *when the organisation was described as meritocratic* [27].
- **Job evaluation and performance-related pay can make gaps look gender-neutral.** Research on these systems finds that the pay they produce reflects gendered cultural views of which jobs are worth more, while the system itself lends the result an air of neutrality [28].
- **Structured or discretionary pay.** Eurostat's 2024 finding that public-sector gaps are smaller where open wage grids are used [1] fits the micro evidence. Rules that are written down, visible and applied the same way to everyone leave less room for bias than discretionary decisions. The cost, as the US academic data show, is a weaker link between pay and individually measured performance [14].

### 4.4 Design principles that follow

1. State the pay range and whether pay is negotiable in every job ad [5][18].
2. Use anchors and defaults based on market data rather than the candidate's prior pay [21][24][25].
3. Turn performance ratings into pay through a written formula, and check that formula for gaps between people with equal ratings [26][27].
4. Keep the discretionary part of pay small, record a reason for every discretionary decision, and review those decisions.

---

## 5. Auditing pay algorithms and job-evaluation systems

### 5.1 How job evaluation works

Job evaluation ranks jobs by their value to the organisation so that pay can be tied to that value. The usual analytical method is **point-factor** evaluation:

1. Choose **factors**. The Directive's minimum is skills, effort, responsibility and working conditions [5].
2. Break each factor into **sub-factors** and **levels**. For example, responsibility for people, for money, or for the wellbeing of others.
3. Give each factor a **weight**, then score every job.
4. Sum the points, group jobs into **grades** or bands, and attach pay ranges to each grade.

The ILO's step-by-step guide sets out a gender-neutral process. It covers forming a pay-equity committee, choosing which jobs to compare (including jobs held mostly by women and those held mostly by men), collecting job data with neutral questionnaires, analysing results, setting the value of each job, and estimating and correcting pay gaps between jobs of equal value [29]. The guide stresses that bias can enter "at any stage" of designing or applying an evaluation method [29].

**Known sources of bias in factor choice and weighting** [28][29]:
- Factors that fit male-dominated jobs, such as managing budgets or heavy lifting, get high weights. Factors that fit female-dominated jobs, such as emotional effort, caring for others' wellbeing, coordination and dealing with interruptions, are left out or given little weight. The Directive's warning that soft skills must not be undervalued targets this [5].
- Job descriptions copied from older, gendered job titles.
- Scoring panels where one group dominates.
- Grade boundaries drawn so that jobs held mostly by women land just below a boundary.
- Market pricing used to "check" job-evaluation results, which can bring market-wide undervaluation back into the system.

### 5.2 What counts as a "pay algorithm"

Any rule that turns data into pay:
- salary bands and rules for where new hires start in a band;
- merit matrices (rating × position in range → percentage increase);
- bonus formulas and pools;
- promotion uplift rules;
- market-pricing models;
- machine-learning compensation recommenders that suggest offers or raises.

Each one can be audited the same way: what goes in, what rule applies, what comes out, and who is affected differently.

### 5.3 Step-by-step audit method in plain words

**Step 1: Data inventory.** List every pay component (base, allowances, bonus, equity, overtime) and every input to each pay decision. Record where each figure comes from and who can change it. Collect gender data only for the audit, keep it separate from decision systems, and protect access to it (see Section 6.3).

**Step 2: Job-family mapping and value grouping.** Map every role to a job family and a level. Next, use a gender-neutral job evaluation to group roles of *equal value* across families. The Directive's "categories of workers" and its 5% test apply at this level [5].

**Step 3: Descriptive gaps.** For each category, compute the mean and median gap for base pay and for total pay. Also compute the share of each gender receiving variable pay and the share in each pay quartile. These figures mirror Article 9 of the Directive and UK reporting [5][6].

**Step 4: Regression on legitimate factors.** Regress log pay on factors the employer can defend as objective and gender-neutral, such as job value, level, location and relevant experience, plus a gender indicator. Add interactions between gender and the main factors, because a single average can hide a gap that exists only at senior levels or in one job family.

**Step 5: Oaxaca-Blinder decomposition.** Split the raw gap into two parts [30][31]:
- the part *explained* by different average characteristics;
- the part *unexplained* by different returns to the same characteristics.

Eurostat applies this method to the 2022 Structure of Earnings Survey data [2]. Inside a firm, the unexplained part is the first thing to investigate. It is not proof of discrimination, and the explained part is not proof that pay is fair.

**Step 6: Rule-by-rule disparate-impact tests.** Test each pay-setting rule on its own:
- *Bands:* are women more often placed low in their band at entry?
- *Merit matrix:* for the same rating and position in range, do women and men get the same increase? Are ratings themselves spread differently by gender within the same job and level [26]?
- *Bonus:* compare bonus rates and amounts with performance held constant. Check whether discretionary "modifiers" favour one group [27].
- *Machine-learning recommender:* compare recommended offers with what is actually paid, by gender, and test calibration within each group.

**Step 7: Proxy detection.** Look for inputs that are correlated with gender and may carry bias even though they look neutral:
- prior salary [23][24][25];
- negotiation history or the candidate's stated ask [21];
- employment gaps;
- total years of service rather than relevant experience;
- part-time history;
- some job titles, schools or keywords in free text.

Measure how well each feature predicts gender. Then ask two questions. Is the feature a legitimate measure of job value? Does removing it change the gender coefficient?

**Step 8: Counterfactual tests.** For rule-based systems, swap the recorded gender, or swap the proxy features to their values for the other gender's group, and check whether the output changes. For a machine-learning recommender, run matched pairs, profiles that are identical except for gender and its proxies, through the model.

**Step 9: Small groups and uncertainty.** Many categories have only a few people in them. Report confidence intervals, for example by bootstrap. Flag cells too small to support a conclusion instead of treating noise as a finding. Combine years or similar categories where that makes sense, and protect privacy (Section 6.3).

**Step 10: Remediation, budget and re-audit.** Fix unexplained gaps with individual adjustments that have their own budget, kept separate from the normal merit budget, so that the fixes are not quietly paid for by smaller rises elsewhere. Fix the rule that caused the gap, not only its results. Re-audit every year before the pay cycle, and run a check after every rule change and every model retraining.

### 5.4 Tying it to the Directive's 5% trigger

The 5% test in Article 10 applies to the difference in *average pay level* in any category of workers. A joint assessment is required when that difference is not justified by objective, gender-neutral criteria and has not been fixed within six months [5].

An internal audit can work as an early-warning system:
- flag any category where the raw gap is 5% or more;
- for each flagged category, record which objective, gender-neutral factors account for the gap and which do not;
- treat any unexplained part that cannot be justified as needing a fix before the six-month window closes.

The burden-of-proof rule in Article 18 makes the audit trail itself valuable [5].

### 5.5 Pseudocode (illustration only, not run)

```text
# PAY EQUITY AUDIT: illustrative pseudocode, not a tested implementation

INPUT  employees: id, gender, category, job_value_points, level, location,
                  relevant_experience, base_pay, variable_pay, hours,
                  rating, entry_band_position, model_recommendation (if any)
INPUT  rules: bands, merit_matrix, bonus_formula, recommender_model
PARAM  TRIGGER = 0.05        # Directive Art. 10 threshold
PARAM  MIN_CELL = 10         # suppress or flag smaller groups (policy choice)

# Step 3: descriptive gaps per category
for each category c:
    m = employees in c with gender = male
    f = employees in c with gender = female
    gap_mean[c]   = (mean(hourly_pay(m)) - mean(hourly_pay(f))) / mean(hourly_pay(m))
    gap_median[c] = (median(hourly_pay(m)) - median(hourly_pay(f))) / median(hourly_pay(m))
    gap_variable[c] = same calculation using variable_pay
    share_receiving_variable[c] = share of m and of f with variable_pay > 0
    ci[c] = bootstrap_confidence_interval(gap_mean[c], reps = 2000)
    if size(m) < MIN_CELL or size(f) < MIN_CELL:
        mark c as SMALL_CELL           # report as a range only, never as a point

# Step 4: regression with interactions
model = OLS( log(hourly_pay) ~ female
               + job_value_points + level + location + relevant_experience
               + female * level + female * job_family )
record coefficient on female, its interactions, and their confidence intervals

# Step 5: Oaxaca-Blinder decomposition
beta_m = OLS fitted on men only;   beta_f = OLS fitted on women only
X_m = mean covariates of men;      X_f = mean covariates of women
explained   = (X_m - X_f) * beta_m
unexplained = X_f * (beta_m - beta_f)
# also report the decomposition using a pooled reference, since results
# depend on which group's coefficients are used as the reference

# Step 6: rule-by-rule disparate impact
for each rule r in rules:
    for each (rating, band_position) cell:
        compare outcome(r) for women vs men; store the difference and its CI
    flag r if any difference is significant and above the materiality level

# Step 7: proxy detection
for each input feature x:
    proxy_strength[x] = how well x predicts gender (e.g. AUC)
    delta_coef[x] = change in the female coefficient when x is dropped
    if proxy_strength[x] is high and x is not a documented job-value factor:
        flag x for review (examples: prior_salary, stated_ask, employment_gap)

# Step 8: counterfactual test of the recommender or formula
for each employee e:
    e_swap = copy of e with gender and flagged proxies set to the
             other group's typical values
    diff[e] = output(e_swap) - output(e)
report the distribution of diff; flag if the mean is not ~0 or the tails are large

# Step 4-5 to trigger: Directive check
for each category c:
    if gap_mean[c] >= TRIGGER:
        justified = portion explained by documented objective, gender-neutral factors
        if gap_mean[c] - justified >= TRIGGER:
            status[c] = "REMEDY WITHIN 6 MONTHS OR JOINT PAY ASSESSMENT"

# Step 10: remediation plan
budget = sum over employees of the adjustment needed to close the unexplained gap
apply adjustments from a separate equity budget; change the flagged rules
schedule: full audit yearly; spot checks after any rule change or model retrain
```

### 5.6 Special issues with machine-learning compensation tools

A model trained on past pay learns past pay patterns, gaps included. Leaving the gender column out does not help when prior salary, stated ask, employment gaps or particular job titles stand in for it [21][23][24][25]. Practical safeguards:

- Exclude prior salary and stated ask as inputs.
- Anchor recommendations to the band for the role's evaluated value.
- Test every model version with counterfactual pairs before release.
- Log every case where a human overrides the model, and audit the overrides by gender as well, because people's discretion can bring the bias back [26][27].

---

## 6. Ethical risks and limits

### 6.1 Proxies
Removing gender from a model does not remove gender from the data. Features that predict gender can rebuild the gap. Proxy detection (Step 7) has to be done on purpose and repeated, because proxies change as the workforce changes.

### 6.2 Over-adjustment: "explaining away" the gap
Each control added to the regression shrinks the "unexplained" gap. But if a control is itself shaped by bias, the audit hides the problem instead of finding it. Examples are job grade when grading is biased [28][29], performance ratings when ratings are biased [26], and starting salary when it was based on prior pay [24]. A small adjusted gap can therefore mean the system is fair, or it can mean the audit has adjusted for the bias. Good practice:
- report results with and without each disputed control;
- justify every control as a legitimate measure of job value;
- treat "explained" as "accounted for", not as "fair".

### 6.3 Privacy of small cells
Gap figures for a category with three women can reveal individual salaries. The Danish law's rule of at least 10 employees of each gender in an occupation was partly there to protect anonymity [8]. Audits should suppress or combine small cells, report ranges instead of point estimates, limit who can see raw gender data, and keep audit data apart from decision systems. Under the Directive, workers can be required to use information they receive only to pursue equal pay (Art. 7(6)) [5].

### 6.4 Gaming
Any threshold invites behaviour aimed at the threshold rather than the goal. Examples: redrawing worker categories so that no single category reaches 5%, shifting pay from reported to unreported components, outsourcing low-paid roles, or meeting a process standard on paper only. Iceland's experience shows this risk [15][17]. Countermeasures include public reporting of how categories are defined, reporting total pay, outside audits, and watching how categories change over time.

### 6.5 Wage compression and lower average pay
Transparency between coworkers can narrow gaps by holding down everyone's pay, especially men's [8][9][11]. It can also weaken rewards for individual performance [14]. Whether that is acceptable is a values question. Policy makers should say openly which they want: a smaller gap, higher pay for women, or both. They should also consider transparency across firms, such as posted pay ranges, which tends to raise pay by sharpening competition between employers [16].

### 6.6 Legal limits and fairness definitions
- Statistical fairness tests do not always agree with one another, and an audit cannot meet every definition at once. The audit should state which standard it uses, for example equal pay for equal job value with legitimate factors held constant.
- Fixing a gap must not create new unjustified differences. Adjustments should be tied to individual cases of equal value, not to group membership alone.
- National law decides which factors count as justifications and how audit data may be processed. Data-protection rules limit how gender data is handled.
- Collecting data on grounds other than sex is not required by the Directive [5]. Intersectional analysis may be useful, but it needs a legal basis and strong privacy protection.

### 6.7 What this case leaves out
These pay-system levers can narrow the part of the gap that comes from how employers set and review pay. They do not address every cause, so even a well-audited firm can show a raw gap, as population figures show [1][2][3]. This case examines pay-setting systems, incentive structures and pay transparency only.

---

## 7. Recommendations, ranked by strength of evidence

1. **Mandatory, comparable reporting that employees or the public can see, plus a duty to act (strong evidence of a modest effect).** Public reporting narrowed gaps in the UK [9]. In Denmark, statistics shared only with employees and their representatives, at a fine occupation level, also narrowed gaps [8]. Austria's coarser internal reports, which employees had to keep confidential, did not [12][33]. The lesson is not simply "publish": make figures visible to the people who can act on them, comparable at the level of the job, simple and verified, and where possible computed from administrative data, as the OECD recommends [4]. Public reporting adds reputational pressure on top of this [9].
2. **Bans on pay-history questions (good quasi-experimental evidence) and pay ranges in job ads (limited evidence so far).** Salary-history bans have quasi-experimental evidence of raising women's pay rather than holding men's down [23][24][25], and the ban is now law across the EU [5]. For pay ranges in job advertisements, the OECD's 2026 review finds that evidence on efficacy remains limited and that further evaluation is needed to determine what works [4]. Tell candidates explicitly that pay is negotiable [18].
3. **Neutral anchors in pay-setting tools (strong evidence from one setting).** Defaults based on market data removed the ask gap completely on one platform [21]. Any offer system can copy this design.
4. **Rule-based conversion of ratings into pay, with audits (good observational and experimental evidence).** Performance-reward bias and the paradox of meritocracy show that discretion and a culture that calls itself meritocratic do not ensure fairness [26][27]. Turn ratings into pay with written formulas, record reasons for discretion, and test outcomes for people with equal ratings.
5. **Gender-neutral job evaluation that is itself audited (accepted practice; evidence on effect still emerging).** Follow the ILO process [29]. Review factor weights for undervalued work, as the Directive requires [5]. Do not treat a certificate as proof of fairness [15][17][28].
6. **Annual audits tied to the 5% trigger, with a separate remediation budget (legally required in the EU; strong practical logic).** Use the method in Section 5. Report uncertainty, protect small cells, and show results with and without disputed controls.
7. **Prefer transparency that raises pay to transparency that only compresses it (theory and emerging evidence).** Combine within-firm reporting with cross-firm pay information so that gaps close by raising women's pay, not by lowering everyone's [11][16].

---

## 8. References

All references below were checked. Each DOI was resolved through the Crossref API, and its title, authors and year were confirmed. Each figure used in this case was confirmed on the page linked or in the official abstract or full text.

**Statistics and official sources**

1. Eurostat (2025, updated). *Gender pay gap statistics*. Statistics Explained. https://ec.europa.eu/eurostat/statistics-explained/index.php?title=Gender_pay_gap_statistics
2. Leythienne, D. & Pérez-Julián, M. (2025). *Gender pay gaps in the European Union – A statistical analysis based on Structure of Earnings Survey 2022 data* (2025 edition, revised version). Eurostat Statistical Working Papers. https://doi.org/10.2785/9475790. Page: https://ec.europa.eu/eurostat/web/products-statistical-working-papers/w/ks-01-26-017
3. Office for National Statistics (2026). *Gender pay gap in the UK: 2026*. Released 22 October 2026. https://www.ons.gov.uk/employmentandlabourmarket/peopleinwork/earningsandworkinghours/bulletins/genderpaygapintheuk/latest
4. OECD (2026). *Pay Transparency in Progress*. Gender Equality at Work. OECD Publishing. https://doi.org/10.1787/121f268d-en
5. Directive (EU) 2023/970 of the European Parliament and of the Council of 10 May 2023 to strengthen the application of the principle of equal pay for equal work or work of equal value between men and women through pay transparency and enforcement mechanisms. OJ L 132, 17.5.2023, p. 21. https://eur-lex.europa.eu/eli/dir/2023/970/oj
6. The Equality Act 2010 (Gender Pay Gap Information) Regulations 2017, SI 2017/172. https://www.legislation.gov.uk/uksi/2017/172/made
7. Government of Iceland (2018). *New Icelandic law on Equal Pay Certification entered into force on January 1, 2018*. https://www.government.is/news/article/2018/01/04/New-Icelandic-law-on-Equal-Pay-Certification-entered-into-force-on-January-1-2018/

**Evidence on pay transparency**

8. Bennedsen, M., Simintzi, E., Tsoutsoura, M. & Wolfenzon, D. (2022). Do firms respond to gender pay gap transparency? *Journal of Finance*, 77(4), 2051–2091. https://doi.org/10.1111/jofi.13136
9. Blundell, J., Duchini, E., Simion, S. & Turrell, A. (2025). Pay transparency and gender equality. *American Economic Journal: Economic Policy*, 17, 418–445. https://doi.org/10.1257/pol.20220766
10. Baker, M., Halberstam, Y., Kroft, K., Mas, A. & Messacar, D. (2023). Pay transparency and the gender gap. *American Economic Journal: Applied Economics*, 15(2), 157–183. https://doi.org/10.1257/app.20210141
11. Cullen, Z. & Pakzad-Hurson, B. (2023). Equilibrium effects of pay transparency. *Econometrica*, 91(3), 765–802. https://doi.org/10.3982/ECTA19788
12. Gulyas, A., Seitz, S. & Sinha, S. (2023). Does pay transparency affect the gender wage gap? Evidence from Austria. *American Economic Journal: Economic Policy*, 15, 236–255. https://doi.org/10.1257/pol.20210128
13. Gamage, D. K., Kavetsos, G., Mallick, S. & Sevilla, A. (2024). Pay transparency intervention and the gender pay gap: Evidence from research-intensive universities in the UK. *British Journal of Industrial Relations*, 62, 293–318 (online 2023). https://doi.org/10.1111/bjir.12778
14. Obloj, T. & Zenger, T. (2022). The influence of pay transparency on (gender) inequity, inequality and the performance basis of pay. *Nature Human Behaviour*, 6(5), 646–655. https://doi.org/10.1038/s41562-022-01288-9
15. Haraldsdóttir, R. K., Rafnsdóttir, G. L. & Jónsdóttir, G. A. (2022). Tailoring the tool to reality or the reality to the tool? About the equal pay standard and the elimination of the gender pay gap. *Stjórnmál og stjórnsýsla / Icelandic Review of Politics & Administration*, 18(2), 235–260. https://doi.org/10.13177/irpa.a.2022.18.2.4
16. Cullen, Z. (2024). Is pay transparency good? *Journal of Economic Perspectives*, 38(1), 153–180. https://doi.org/10.1257/jep.38.1.153
17. Wagner, I. (2022). Equal pay for work of equal value? Iceland and the Equal Pay Standard. *Social Politics: International Studies in Gender, State & Society*, 29, 477–496 (online 2020). https://doi.org/10.1093/sp/jxaa032

**Negotiation, salary history and pay-setting**

18. Leibbrandt, A. & List, J. A. (2015). Do women avoid salary negotiations? Evidence from a large-scale natural field experiment. *Management Science*, 61(9), 2016–2024. https://doi.org/10.1287/mnsc.2014.1994
19. Bowles, H. R., Babcock, L. & Lai, L. (2007). Social incentives for gender differences in the propensity to initiate negotiations: Sometimes it does hurt to ask. *Organizational Behavior and Human Decision Processes*, 103(1), 84–103. https://doi.org/10.1016/j.obhdp.2006.09.001
20. Exley, C. L., Niederle, M. & Vesterlund, L. (2020). Knowing when to ask: The cost of leaning in. *Journal of Political Economy*, 128(3), 816–854. https://doi.org/10.1086/704616
21. Roussille, N. (2024). The role of the ask gap in gender pay inequality. *Quarterly Journal of Economics*, 139(3), 1557–1610. https://doi.org/10.1093/qje/qjae004
22. Card, D., Cardoso, A. R. & Kline, P. (2016). Bargaining, sorting, and the gender wage gap: Quantifying the impact of firms on the relative pay of women. *Quarterly Journal of Economics*, 131(2), 633–686. https://doi.org/10.1093/qje/qjv038
23. Hansen, B. & McNichols, D. (2020). Information and the persistence of the gender wage gap: Early evidence from California's salary history ban. NBER Working Paper 27054. https://doi.org/10.3386/w27054
24. Bessen, J., Denk, E. & Meng, C. (2024). Perpetuating wage inequality: Evidence from salary history bans. *Journal of Economic Inequality*, 22, 709–733. https://doi.org/10.1007/s10888-023-09610-9
25. Sinha, S. (2022). Salary history bans: Strategic disclosure by job applicants and the gender pay gap. SSRN working paper. https://doi.org/10.2139/ssrn.4025580

**Performance pay, job evaluation and decomposition methods**

26. Castilla, E. J. (2008). Gender, race, and meritocracy in organizational careers. *American Journal of Sociology*, 113(6), 1479–1526. https://doi.org/10.1086/588738
27. Castilla, E. J. & Benard, S. (2010). The paradox of meritocracy in organizations. *Administrative Science Quarterly*, 55(4). https://doi.org/10.2189/asqu.2010.55.4.543
28. Koskinen Sandberg, P. (2017). Intertwining gender inequalities and gender-neutral legitimacy in job evaluation and performance-related pay. *Gender, Work & Organization*, 24, 156–170 (online 2016). https://doi.org/10.1111/gwao.12156
29. Chicha, M.-T. (2008). *Promoting equity: Gender-neutral job evaluation for equal pay: A step-by-step guide*. Geneva: International Labour Office. ISBN 978-92-2-121539-4. https://www.ilo.org/publications/promoting-equity-gender-neutral-job-evaluation-equal-pay-step-step-guide
30. Oaxaca, R. (1973). Male-female wage differentials in urban labor markets. *International Economic Review*, 14(3), 693–709. https://doi.org/10.2307/2525981
31. Blinder, A. S. (1973). Wage discrimination: Reduced form and structural estimates. *Journal of Human Resources*, 8(4), 436–455. https://doi.org/10.2307/144855
32. Blau, F. D. & Kahn, L. M. (2017). The gender wage gap: Extent, trends, and explanations. *Journal of Economic Literature*, 55(3), 789–865. https://doi.org/10.1257/jel.20160995
33. Austria, Gleichbehandlungsgesetz (Equal Treatment Act), § 11a (income reports: sent to the works council, employees bound to confidentiality, fine up to EUR 360). https://ris.bka.gv.at/GeltendeFassung.wxe?Abfrage=Bundesnormen&Gesetzesnummer=20003395
34. Denmark, Ligelønsloven (Equal Pay Act), consolidated text, § 5 a (gender-segregated pay statistics for information and consultation of employees). https://www.retsinformation.dk/eli/lta/2019/156

---

*Notes on verification.* The EU Directive was checked against the English Official Journal text obtained from the EU Publications Office, because the EUR-Lex website blocked automated access. The Canadian disclosure study [10] and the job-evaluation studies [17][28] are cited only for what their published abstracts state. The Danish law's thresholds and its employee-facing (not public) disclosure are given as described in [8], section II, and were checked against the consolidated Equal Pay Act, § 5 a, on retsinformation.dk [34]. The Austrian confidentiality duty and fine were checked against § 11a of the Equal Treatment Act [33].
