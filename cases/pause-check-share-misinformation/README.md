# Slowing the spread, not silencing the speaker: platform design and digital literacy against misinformation and polarisation

An ethical case study from a web and platform engineering perspective.
Status: research and design only. Nothing here has been built, deployed or tested with users.
Date of research: 18 November 2026.

Citations in square brackets point to SOURCES.md: [A#] peer-reviewed work, [P#] preprints and working
papers (not peer-reviewed), [S#] reports, platform statements and standards. Every number below comes
from a cited source. Effect sizes are reported as the papers report them (d, r, percentages), and they
are not directly comparable across studies that use different outcomes.

---

## 1. Scale and harms: what we know, and what is contested

### 1.1 People are worried

- In the Reuters Institute Digital News Report 2026, 62 per cent of respondents across markets said
  they were concerned about what is real and what is fake online, up 4 percentage points on the year
  before. Trust in news fell to 37 per cent, the lowest level the report has recorded since it started
  measuring trust in 2015 [S1].
- The WHO describes an "infodemic" as too much information, including false or misleading information,
  during a disease outbreak. It says an infodemic causes confusion and risk-taking that can harm health,
  and undermines trust in health authorities [S2].

### 1.2 But exposure is concentrated, not universal

The best behavioural data point the same way: most people see little false content, and a small group
sees and shares most of it.

- On Twitter in the 2016 US election, 1 per cent of individuals accounted for 80 per cent of exposures
  to fake news sources, and 0.1 per cent accounted for nearly 80 per cent of shares [A2].
- On Facebook in 2016, sharing fake news domains was rare. Users over 65 shared nearly seven times as
  many articles from fake news domains as the youngest age group [A1].
- Across mobile, desktop and television use in the US, fake news made up 0.15 per cent of the daily
  media diet; news of any kind made up at most 14.2 per cent [A3].
- A 2024 review in *Nature* describes "a pattern of low exposure to false and inflammatory content
  that is concentrated among a narrow fringe with strong motivations to seek out such information",
  and recommends focusing on the tails of the distribution, where harm risk is greatest [A4].
- Other researchers argue that alarmist narratives overstate prevalence and impact: engagement is not
  belief, and people are more often uninformed than misinformed [A5].

Most of this evidence comes from the US and Western Europe. The *Nature* review notes that research
and data are scarce elsewhere and that harms there may be more severe [A4].

### 1.3 Feed tweaks moved behaviour, not attitudes (the 2023 Meta studies)

In 2023, four studies with Meta's cooperation tested feed changes on consenting US users during the
2020 election:

- Switching to a reverse-chronological feed for three months cut time on the platforms and *increased*
  exposure to political and untrustworthy content, but did not significantly change issue
  polarisation, affective polarisation, political knowledge or other key attitudes [A6].
- Removing reshared content cut exposure to political news, including from untrustworthy sources, and
  reduced news knowledge, but did not significantly affect polarisation or political attitudes [A7].
- Reducing exposure to like-minded sources by about one-third for 23,377 users increased cross-cutting
  exposure but had no measurable effect on eight preregistered attitude measures, including affective
  polarisation and belief in false claims [A8].
- Across 208 million US users, ideological segregation in news exposure was high and asymmetric. Most
  content flagged by Meta's fact-checking programme sat in a homogeneously conservative corner [A9].

**Honest reading.** Short-term, single-lever feed changes during a high-salience election did not
shift attitudes. That does not show that platforms have no effect over years, or outside the US. A
2025 independent field experiment found that reranking X feeds to reduce or increase posts expressing
anti-democratic attitudes and partisan animosity shifted out-party animosity by more than 2 points on a
100-point scale over 10 days (n = 1,256) [A31]. The evidence is mixed, and this study's design treats
it that way.

### 1.4 Why correcting is hard

Misinformation keeps influencing reasoning even after it has been corrected, which is called the
continued influence effect [A10]. A meta-analysis of debunking found large effects for presenting
misinformation (d = 2.41–3.08), strong debunking effects (d = 1.14–1.33), and substantial persistence
of misinformation after debunking (d = 0.75–1.06) [A35]. A second meta-analysis found corrections have
a moderate effect on belief (r = .35), weaker in politics (r = .15) than in health (r = .27) [A34].

---

## 2. What works: evidence on interventions

A 2024 expert review mapped evidence from 81 papers into nine intervention types: accuracy prompts,
debunking, friction, inoculation, lateral reading, media-literacy tips, social norms, source-credibility
labels, and warning labels [A11]. The ones most relevant to web and platform engineers are below.

### 2.1 Prebunking and inoculation

| Study | What was tested | Result |
|---|---|---|
| Roozenbeek and van der Linden 2019 [A12] | *Bad News* browser game [S7], where players produce fake news using six techniques; N = 15,000, pre-post design | Ability to spot and resist misinformation improved after play, across education, age, ideology and cognitive style |
| Basol et al. 2020 [A13] | *Bad News* with a gamified control group (N = 196) | Improved spotting of misinformation techniques compared with control, and raised confidence among those who updated correctly |
| Basol et al. 2021 [A14] | *Go Viral!*, a five-minute game, in English, French and German (n = 1,771 and 1,777) | Raised perceived manipulativeness of COVID-19 misinformation and confidence, reduced willingness to share; first two effects lasted at least a week |
| Roozenbeek et al. 2022 [A15] | Five short videos about manipulation techniques; six RCTs (n = 6,464) and a YouTube field study (n = 22,632) | Better technique recognition, better discernment of trustworthy versus untrustworthy content, better sharing decisions, robust across the political spectrum |
| **Null finding:** Modirrousta-Galian and Higham 2023 [A16] | Reanalysis of five *Bad News* and *Go Viral!* studies using signal-detection (ROC) analysis | The games did **not** improve discrimination between true and fake news; they made people more likely to call *all* news false. The authors say the games may even be counterproductive |

**Engineering takeaway.** Prebunking scales well as short video or interactive content. Evaluations
must measure discrimination separately from general scepticism, otherwise a tool that makes people
distrust everything looks like a success.

### 2.2 Accuracy prompts

| Study | Result |
|---|---|
| Pennycook et al. 2020 [A17] | Over 1,700 US adults: one accuracy reminder nearly tripled truth discernment in COVID-19 sharing intentions |
| Pennycook et al. 2021, *Nature* [A18] | Survey experiments and a Twitter field experiment: subtly shifting attention to accuracy increased the quality of news people later shared. Most participants said sharing only accurate news is important |
| **Replication:** Roozenbeek, Freeman and van der Linden 2021 [A19] | Direct replication of the 2020 study. First stage (n = 701) failed (p = .67). With pooled N = 1,583, a small but significant effect was found (uncorrected p = .017; treatment d = 0.14, control d = 0.10). The paper also notes an unreported change in hypothesis in the original preregistration |
| Pennycook and Rand 2022 [A20] | Internal meta-analysis of 20 experiments (N = 26,863) by the original group: prompts improved sharing discernment, mainly by cutting intentions to share false headlines by 10 per cent relative to control; effects larger for older, more reflective and more attentive participants |

**Honest reading.** The effect is real but small, and most of the evidence comes from one research
group and from sharing *intentions* in surveys rather than real sharing.

### 2.3 Lateral reading and civic online reasoning

- Middle school, high school and college students in 12 US states struggled to evaluate online claims,
  sources and evidence [A21].
- In a district-wide field study, six 50-minute lessons on lateral reading (leaving a site to check
  what others say about it) led treatment students (n = 271) to grow significantly more than matched
  peers (n = 228) in judging the credibility of digital content [A22].
- College students in an asynchronous online course improved from pretest to posttest and used lateral
  reading more often afterwards [A23].

**Engineering takeaway.** Lateral reading is a habit that interfaces can support: make "who is behind
this?" one tap away.

### 2.4 Friction and forwarding limits

- Participants who paused to explain why a headline was true or false were less likely to say they
  would share false headlines, while their intention to share true headlines did not change [A24].
- WhatsApp says an earlier limit on forwarding was followed by a 25 per cent drop in total forwards
  globally, and in April 2020 limited "forwarded many times" messages to one chat at a time [S3]. A
  WhatsApp spokesperson later said highly forwarded messages fell by 70 per cent [S4]. These are company
  claims, not independent measurements, and they count messages, not misinformation.
- A modelling study using WhatsApp data from Brazil, India and Indonesia found forwarding limits delay
  spread, but are ineffective at stopping highly viral misinformation through public groups [A25].

**Engineering takeaway.** Content-neutral friction (it applies to everything highly forwarded, true or
false) avoids anyone deciding what is true, but it slows rather than stops spread.

### 2.5 Community Notes and crowd fact-checking

- A small, politically balanced crowd of laypeople can show high agreement with professional fact
  checkers when rating article accuracy [A26].
- But on Birdwatch (the early name for Community Notes), users mostly flagged posts from the other
  party and rated counter-partisans' notes as unhelpful; partisanship predicted judgments better than
  content did [A27].
- A study of the roll-out found **no evidence** that Community Notes reduced engagement with misleading
  posts overall, and suggested notes may arrive too late, after the most viral early phase [A28].
- A study of 40,078 posts (March–June 2023) found that, once attached, notes reduced reposts by 46.1
  per cent and views by 13.5 per cent; over a post's whole life, reductions were 11.6 per cent of
  reposts and 5.5 per cent of views. The authors could not test coverage or implied truth effects
  [A29]. A January 2026 correction added competing-interest disclosures (collaborations with people
  employed by Meta and X); it concerns disclosures only [A29].

**Honest reading.** Notes work when they arrive. Speed and coverage are the bottleneck.

### 2.6 Bridging-based ranking

"Bridging" means ranking or showing content that is rated well by people who usually disagree.

- Community Notes' bridging approach selects notes that appeal across different groups. In a survey
  experiment and on Twitter, people who saw notes selected this way were significantly less likely to
  reshare posts [P1]. This is a preprint and has not been peer reviewed.
- A working paper sets out bridging systems as a research area across ranking, recommenders and
  deliberation [P2].
- Downranking or removing posts scored for anti-democratic attitudes reduced partisan animosity
  (d = .20 for removal, d = .25 for downranking) without hurting user experience or engagement [A30].
- Independent feed reranking during the 2024 US campaign shifted out-party animosity by more than 2
  points on a 100-point scale in either direction [A31].

### 2.7 Media-literacy meta-analyses

- Across 51 media-literacy interventions (not limited to misinformation), the average effect was
  d = .37; interventions with more sessions were more effective, while those with more components were less effective [A32].
- A 2024 meta-analysis of 49 experiments (N = 81,155) on misinformation found an overall effect of
  d = 0.60: belief in misinformation d = 0.27, discernment d = 0.76, sharing d = 1.04. Effects were
  stronger with multiple sessions and among college students than among online crowd workers [A33].

### 2.8 Null, backfire and side-effect findings

| Finding | Source |
|---|---|
| Across 5 experiments, more than 10,100 subjects and 52 issues, **no** correction triggered the "backfire effect"; people generally accept factual corrections | Wood and Porter [A36] |
| Paying US Twitter users to follow a bot posting opposing views made Republicans substantially *more* conservative; Democrats' shift was not significant | Bail et al. 2018 [A37] |
| Warning labels on some false headlines made *unlabelled* false headlines seem more accurate (the "implied truth effect"); attaching verifications to some true headlines eliminated it | Pennycook et al. 2020 [A38] |
| Fact-checks, media-literacy tips and media coverage of misinformation all reduced belief in false information but also lowered the credibility of *true* information (n = 6,127; US, Poland, Hong Kong) | Hoes et al. 2024 [A39] |
| A media-literacy "tips" intervention improved discernment by 26.5 per cent in the US and 17.5 per cent in an educated Indian online sample, while also lowering the perceived accuracy of mainstream headlines, and had **no** effect in a largely rural Indian sample | Guess et al. 2020 [A40] |
| Gamified prebunking increased "false" responses to all news rather than discrimination | Modirrousta-Galian and Higham [A16] |
| Feed changes did not move attitudes in the 2020 US election studies | [A6][A7][A8] |

---

## 3. Design proposal: "Pause, Check, Share"

A set of web patterns that a large platform or a small community site could ship. They favour
**content-neutral** tools (which apply to everything) and **user-chosen** tools (which help people
check for themselves) over tools that require a central authority to decide what is true.

### 3.1 Principles

1. **Slow the spread, don't silence the speaker.** Prefer friction and context over removal.
2. **No persuasion profiles.** No per-user tracking of who "falls for" what. Measure in aggregate.
3. **Accessible by default.** WCAG 2.2 level AA [S6]; every prompt reachable by keyboard and screen
   reader; no time limits; respect reduced-motion settings; plain language.
4. **Every language, not just English.** Ship features only in languages that have reviewed content and
   active raters.
5. **Measure discernment, not just doubt.** Success means people share relatively less false content
   *without* trusting true content less [A16][A39].

### 3.2 The patterns

| Pattern | What the user sees | Evidence | Build notes |
|---|---|---|---|
| **1. Prebunking card** | An optional 60–120 second video or interactive card that explains one manipulation technique (for example, scapegoating or false dichotomies), shown at sign-up or before major events, always skippable | [A14][A15][A33] | Static HTML with captions and a text transcript; video self-hosted; technique-based, never about a party or topic; available in every supported language |
| **2. Occasional accuracy prompt** | Now and then, before sharing a news link, a one-tap question: "How accurate do you think this headline is?" The answer is not stored against the user | [A18][A20], small effect [A19] | Random sampling (for example, a small share of share actions) to avoid fatigue; answers logged only as anonymous aggregates; never blocks the share |
| **3. Read-before-share** | Sharing a link the user hasn't opened shows "You haven't opened this. Open it first?" with Share anyway as an equal choice | Friction evidence [A24] | Client-side check only; no record of what someone reads is sent to the server beyond what the site already logs |
| **4. Forwarding limits for viral chains** | Content forwarded many times can be passed on to fewer people at once and is labelled "Forwarded many times" | [S3][S4][A25] | Content-neutral; counts hops, not meaning; works with end-to-end encryption because it uses forward counters, not content |
| **5. "Who is behind this?" panel** | A button next to any link that opens a short panel: domain age, publisher's own "about" page, and a one-tap search of what others say about the source | Lateral reading [A22][A23] | Opens search in a new tab; the panel does not rate the source; no third-party tracking scripts |
| **6. Content credentials** | Images and video that carry C2PA content credentials show a small "Content credentials" badge that opens who made the file and how it was edited | C2PA standard [S5] | The badge says "credentials found" or nothing at all. It never says "no credentials, may be fake", because most real media has none and that would repeat the implied truth effect [A38]. Strip location data by default |
| **7. Bridged context notes** | Community-written notes appear only when rated helpful by raters who usually disagree with each other | [P1][A29][A27] | For small sites: a simple version that requires helpful ratings from at least two rater clusters; publish the algorithm and data; aim for speed, since late notes do little [A28] |

### 3.3 Evaluation plan

1. **Preregister** hypotheses, metrics and stopping rules before launch.
2. **Randomised rollout with a long-term holdout** (for example, a small share of users who never see a
   pattern), so effects can be measured over months, not days.
3. **Primary outcome: sharing discernment.** The share rate for content later rated false by
   professional fact-checkers and bridged notes, compared with the share rate for content rated true.
   Use signal-detection measures (sensitivity and bias separately) [A16].
4. **Guardrail outcomes:** sharing and trust of *true* news [A39], reported trust in the platform,
   abandonment of prompts, complaints, and engagement, so that a "win" is not just a quieter site.
5. **Equity cuts:** results reported per language, country, age group and assistive-technology use.
   Do not ship a pattern in a language where it has not been tested [A40].
6. **Accessibility testing:** automated checks plus manual testing with screen-reader and keyboard
   users before each release.
7. **Independent access:** give outside researchers privacy-protected data access, as the 2024
   *Nature* review recommends [A4]. Publish null results.
8. **Kill switch:** stop a pattern if guardrails are breached, for example if trust in true news falls.

---

## 4. Ethical risks and mitigations

| Risk | Why it matters | Mitigation |
|---|---|---|
| **Paternalism and censorship** | Platforms deciding what people may say or see concentrates power and can suppress legitimate speech. | Prefer content-neutral friction and optional tools over removal. Every prompt has an equal "continue" option. Removal stays limited to clearly illegal content under published rules. |
| **Who defines "misinformation"?** | Labels reflect the labeller. Fact-check flags have been politically asymmetric [A9], and crowd raters are partisan [A27]. | Use bridging so notes need agreement across divides [P1]; keep prebunking technique-based, not topic-based; publish rating data and algorithms; offer an appeal route. |
| **Chilling effects** | People may stop posting honest but uncertain views if they expect labels. | No labels on opinions; notes only add context; no account penalties for being noted; transparency reports. |
| **Manipulation through nudges** | The same nudge machinery could be used to push views or engagement. | Nudges are about accuracy and pacing only, never about which side to take; no individual targeting; public nudge register; independent audit. |
| **Language and regional inequity** | Most evidence is US and Western European [A4]; effects can vanish in other settings [A40]. Unequal moderation across languages can leave some users less protected or more policed. | Launch per language only with local reviewers and raters; report per-language results; fund research outside the US and Western Europe. |
| **Literacy turning into cynicism** | Interventions can lower trust in true news [A39][A40] and make people call everything false [A16]. With trust in news already at 37 per cent [S1], blanket doubt is a real harm. | Teach verification ("check what others say"), not suspicion; measure trust in true news as a guardrail; balance "be careful" messaging with pointers to reliable sources. |
| **Privacy of measurement** | Tracking who believes what creates sensitive profiles. | Aggregate-only metrics, minimum group sizes before reporting, no third-party analytics, short retention. |
| **False confidence from provenance badges** | Content credentials show history, not truth, and missing credentials are normal. | Neutral wording; never mark content without credentials as suspicious [S5][A38]. |

---

## 5. Limits of this study

This is desk research. No platform data were analysed and no users were consulted. Much of the
evidence comes from US samples, short time frames and survey measures of sharing intention rather than
real behaviour. Several key results come from small groups of researchers, some of whom have worked
with the platforms they study (disclosed in the papers). The proposal should be piloted, measured and
revised, and it may fail. Reporting that failure honestly is part of the design.
