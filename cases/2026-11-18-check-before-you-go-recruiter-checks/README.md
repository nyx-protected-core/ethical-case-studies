# Check Before You Go: web technology against deceptive recruitment

An ethical case study from a full-stack web perspective.
Anchor value: ending human trafficking worldwide.
Status: research and design only. Nothing here has been built, deployed or tested with users.
Date of research: 18 November 2026.

Citations in square brackets, for example [S1] or [A3], point to entries in SOURCES.md.
Every number in this document comes from a cited source. Where a source is a press summary
of a longer report, that is said in SOURCES.md.

---

## Part 1. Three problems the web could help with

### Proposal A. A recruiter and job-offer checker for migrant jobseekers

**Problem.** Deceptive recruitment is one of the main doors into forced labour. Migrant workers
are more than three times as likely to be in forced labour as non-migrant adult workers [S1].
Many pay recruitment fees, even though the ILO's fair recruitment principles say workers should
not be charged any fees or related costs for their recruitment [S3]. Debt from those fees can turn
into debt bondage [S2].

**Proposed solution.** A free, multilingual, offline-first web app (a Progressive Web App) that a
jobseeker can use before they sign or pay anything. It would:

- look up a recruiter's licence in a copy of public government registries, where such registries exist;
- walk through a plain-language red-flag checklist (fees, passport handover, vague employer, job
  location that changes, pressure to travel on a tourist visa);
- show hotline and embassy contacts for the destination and origin country;
- work on a cheap phone, on a slow or missing connection, with no account and no tracking.

### Proposal B. Fighting deceptive job ads, including scam-compound recruitment

**Problem.** In Southeast Asia, people have been recruited through job ads on mainstream social
media into scam compounds, where they are held and forced to defraud others. The UN human rights
office cited credible sources estimating at least 120,000 people in Myanmar and at least 100,000 in
Cambodia may be held in such situations [S5]. UNODC reports that trafficking for forced criminality,
which includes online scams, grew from 1 per cent of detected victims in 2016 to 8 per cent in
2022 [S4].

**Proposed solution.** A "paste the ad" mode in the same app: the user pastes or photographs a job
ad and the app checks it, on the device, against patterns drawn from public reports (illustrative
examples: a high salary for vague "customer service" or "marketing" work in a border zone, the
employer paying all travel, contact moved to a chat app only). It returns "questions to ask before
you go", not a verdict. A separate, opt-in channel would let users forward an ad to a trusted
organisation, never automatically.

### Proposal C. Web accessibility as a human right

**Problem.** The WHO estimates 1.3 billion people, about 16 per cent of the world's population,
experience significant disability [S18]. Article 9 of the Convention on the Rights of Persons with
Disabilities requires states to ensure access to information and communications technologies on an
equal basis with others [S19]. Many public-service and safety websites still fail basic accessibility.

**Proposed solution.** Open, reusable accessible components and audit checklists aimed at
government and NGO safety sites (hotlines, migration services), built to WCAG 2.2 level AA [S13].

### Which one to take further

Proposal C is important, but it is a general good rather than a direct response to trafficking.
Proposals A and B share the same users, the same moment of risk (before departure, before
payment) and the same technical base. **The chosen idea is a merge of A and B: one offline-first
checker for both recruiters and job ads.** Accessibility (C) is built into it as a hard requirement
rather than treated as a separate project.

---

## Part 2. Deep dive: "Check Before You Go"

### 2.1 How big is the problem?

All figures below are estimates or counts of *detected* cases. Prevalence estimates for hidden
crimes have real limits, and some researchers argue that global prevalence numbers can distort
anti-trafficking responses [A8]. They are used here to show scale, not precision.

| Figure | Source |
|---|---|
| 49.6 million people in modern slavery on any given day in 2021: 27.6 million in forced labour and 22 million in forced marriage | ILO, Walk Free and IOM, Global Estimates of Modern Slavery (2022) [S1] |
| More than 3.3 million of those in forced labour are children | [S1] |
| Adult migrant workers are more than three times as likely to be in forced labour as adult non-migrant workers | [S1] |
| Forced labour in the private economy generates about US$236 billion in illegal profits a year, close to US$10,000 per victim | ILO, Profits and Poverty (2024) [S2] |
| Illegal profits from recruitment fees and related costs paid by international migrants in forced labour: about US$5.6 billion a year, 15 per cent of illegal profits from that group, and 26 per cent within forced labour exploitation | [S2] |
| On average, migrant workers need about 3.93 months of work to pay off recruitment fees and related costs (KNOMAD data used by the ILO), with wide variation between countries | [S2] |
| Detected trafficking victims rose 25 per cent in 2022 compared with 2019; victims detected for forced labour rose 47 per cent | UNODC, Global Report on Trafficking in Persons 2024 [S4] |
| In 2022, 42 per cent of detected victims were trafficked for forced labour and 36 per cent for sexual exploitation | [S4] |
| Forced criminality, including online scams, rose from 1 per cent of detected victims in 2016 to 8 per cent in 2022 | [S4] |
| At least 120,000 people in Myanmar and at least 100,000 in Cambodia may be held in situations where they are forced to carry out online scams | OHCHR briefing paper (2023) [S5] |

**How the recruitment works.** The UN human rights office describes scam-compound recruitment
using advertised professional roles (programmers, marketers, HR staff), often with several rounds
of interviews and tests, and ads seen on well-known social media platforms that promise high
salaries, bonuses, free food and housing [S5]. On arrival, passports are taken and victims are held
under a growing "debt" for travel, training and fines, which the report calls debt bondage [S5].
A survey-based study of survivors found that many were deceived by fraudulent job ads, others by
friends or relatives, and some were kidnapped, and it calls for better information-sharing about
recruitment practices [A7]. The OHCHR briefing also notes the challenge of identifying fake job ads
and monitoring platforms "without restricting people's rights" [S5].

**The fee problem in general.** The ILO definition says recruitment fees and related costs should
not be charged to workers, directly or through wage deductions [S3]. Public-health researchers
describe "usurious labor intermediaries" as one of the drivers that sustain exploitation and argue
for prevention at each stage of the migration cycle [A4].

**The access problem.** About 2.2 billion people were still offline in 2025. Only 23 per cent of
people in low-income countries use the Internet, compared with 94 per cent in high-income
countries; rural use (58 per cent) lags urban use (85 per cent); and mobile broadband remains
unaffordable in around 60 per cent of low- and middle-income countries [S17]. Any tool for
prospective migrants has to work on poor connections and cheap devices.

### 2.2 What already exists

Each of these was checked on 18 November 2026 (see SOURCES.md for details).

| Initiative | What it does | Gap this project could fill |
|---|---|---|
| ILO Fair Recruitment Initiative [S6] and the ILO fair recruitment principles and fee definition [S3] | Launched in 2014; sets the global norm that workers should not pay recruitment fees, and supports governments and social partners | Norms and policy, not a pocket tool for an individual jobseeker |
| IOM IRIS: Ethical Recruitment [S7] | Multi-stakeholder initiative promoting the Employer Pays Principle, voluntary certification of recruiters, and stronger regulation | Certification covers recruiters who opt in; it is not a lookup for every recruiter a worker meets |
| Recruitment Advisor [S8] | Worker review site for recruitment agencies, developed by a consortium of trade unions, with coordination teams in countries including the Philippines, Indonesia, Nepal, Sri Lanka, Bangladesh and Kenya | Needs a connection; reviews are user-generated rather than official licence data |
| Migrant-Rights.org [S9] | Research and advocacy site on migrant workers in the Gulf, with sections on recruitment and trafficking | Journalism and advocacy, not a checking tool. (Note: it is not the operator of Recruitment Advisor.) |
| Polaris and the US National Human Trafficking Hotline [S10] | US anti-trafficking organisation; its hotline page leads to a 24/7 confidential hotline by phone, text and chat | US-focused |
| Tech Against Trafficking [S11] | Coalition of companies working with civil society and experts to advance anti-trafficking technology and address misuse of platforms | A convenor and funder of tools, not a jobseeker tool itself |
| Philippines Department of Migrant Workers licensed agency search [S12] | Government page for checking whether a recruitment agency is licensed; a public guide describes fields for licence status and expiry date | Online only, one country; the page relies on scripts and could not be read directly in this research, so its contents are only partly verified |
| Apprise (research tool) [A5][A6] | Co-designed mobile app to support screening of possible victims of labour exploitation in Thailand | Used by inspectors and outreach workers during screening, not by jobseekers before departure |

**Conclusion.** The pieces exist (norms, licence registries, worker reviews, hotlines), but they are
scattered, mostly online-only, mostly single-language and single-country. Nothing found in this
research puts them together for one person, on one cheap phone, before they pay or travel. That
claim is limited to what was searched and should be checked again with practitioners.

### 2.3 The proposed solution

#### Principles

1. **Nothing leaves the phone unless the user sends it.** No accounts, no analytics, no
   third-party scripts, no fonts or images from other servers.
2. **Works offline.** After one visit, every core feature works with no connection.
3. **Advice, not verdicts.** The app never says a recruiter is "safe". It says what it found, how
   old the data is, and what questions to ask.
4. **Built with the people it serves.** Content and wording are co-designed with migrant worker
   organisations and survivors, following the value-sensitive design approach used by Apprise [A5].

#### Architecture

- **Static Progressive Web App.** Plain HTML, CSS and a small amount of JavaScript, served as static
  files. A Web App Manifest makes it installable [S15]. It also works as plain HTML with JavaScript
  turned off (checklists and hotlines still show; only search needs scripts).
- **Service worker cache.** A service worker [S14] caches the app shell, checklists and hotline data
  on first load. Updates download in the background and apply on the next start.
- **Signed public data packs.** Licence registries, hotline lists and red-flag rules are bundled into
  small per-country "data packs". Each pack is signed with an Ed25519 key [S16], and the app checks
  the signature before use, so a hijacked mirror or a tampered copy shared by phone cannot inject a
  fake "licensed" entry. Each pack carries the date its data was taken and the official source it
  came from.
- **Search runs on the device.** Recruiter lookups and ad checks run locally against the cached
  pack. No search term is ever sent to a server.
- **Low bandwidth.** Target under 150 KB for the first load of the app shell, and packs split by
  country and language so a user only downloads what they need. Packs can be shared phone to phone
  as files, because the signature makes them safe to pass along.
- **Accessibility.** WCAG 2.2 level AA [S13] as a release requirement: large touch targets, readable
  at 200 per cent zoom, full screen-reader support, and no time limits.
- **Multilingual.** Every string in translation files, right-to-left layout support, audio versions of
  the checklist for users who do not read easily, and translations reviewed by native speakers from
  the worker community.
- **Quick exit.** A visible button that clears the screen to a neutral page, and an option to clear
  all local data.

#### Threat model

| Who or what | What they might do | Design response |
|---|---|---|
| A trafficker or controlling employer holding the phone | Look at history to see the user checked them | No server logs to subpoena or leak; no saved search history by default; quick exit and "clear everything" |
| A network observer (employer Wi-Fi, a state, an ISP) | See that the user visited an anti-trafficking site | Static hosting over HTTPS; once installed, the app makes no requests for searches. The first download is still visible, which is a residual risk to document |
| A recruiter or trafficker studying the red flags | Reword ads to avoid the checklist | Checklist is framed around rights the worker can test ("you should never pay a fee", "keep your own passport"), which are hard to fake around; rules updated from new reports |
| An attacker tampering with data | Insert a fake licensed agency | Signed data packs, signature checked on device, key held offline by a small, named governance group |
| A rival or angry recruiter | Claim defamation | Only official registry data is shown as fact, always with its source and date; no user ratings or accusations published by the app |
| The project itself drifting | Adding analytics or tracking "to measure impact" | A written, public no-tracking policy; impact measured through partner organisations' own consented surveys instead |

#### How it avoids harming its users

- It never asks for a name, phone number or location.
- It never reports anyone to anyone. Contacting a hotline is always the user's own action.
- It does not tell people not to migrate. Labour migration is an economic and social mobility strategy
  that benefits millions of people [A4]; the aim
  is safer recruitment, not less movement.
- It points to worker-led and union resources [S8] as well as official hotlines, because some users
  have good reason not to contact authorities.

### 2.4 Ethical risks and mitigations

| Risk | Why it matters | Mitigation |
|---|---|---|
| **Privacy and surveillance of vulnerable users** | Anti-trafficking technology has repeatedly expanded surveillance of the people it claims to protect [A1][A3]. Researchers on Apprise note users will "(mis)use" tools for their own purposes and raise privacy risks for vulnerable workers [A6]. | No accounts, no analytics, on-device search, no history by default, quick exit, independent privacy review before launch. |
| **Traffickers reverse-engineering the red flags** | A public checklist is also a guide to evasion. | Focus on rights-based checks that cannot be satisfied by rewording (fees, passport retention, written contract in the worker's language). Update rules from new public reports. Accept that some patterns will be gamed and say so. |
| **False reassurance from a "legit" result** | A licensed agency can still exploit workers, and a registry hit may be a cloned name. | Never display "safe" or a green tick. Show "Found in the official register as of [date]" plus "Licensed agencies can still break the rules. Check these points too." Always show the checklist after a lookup. |
| **Stale data** | A licence may have been suspended since the pack was made. | Every result shows the data date. Packs older than a set age show a warning and a link to the official registry. |
| **Defamation of recruiters** | Wrongly calling a business a trafficker causes real harm and legal risk. | Show only official registry facts. The ad checker comments on the *ad text*, not on any named company. No public lists of "bad" recruiters. |
| **Digital divide** | 2.2 billion people are offline and costs are high in poorer countries [S17], so the people most at risk may not be reached. | Offline-first, tiny downloads, phone-to-phone sharing, printable one-page versions of the checklist for community groups, and audio. Be honest that a web app is one channel among many. |
| **Paternalism and "saviour" framing** | Critics warn that tech "solutionism" can distract from root causes and political will [A2][A3], and that framing migrants as passive victims removes their agency. | Co-design with worker organisations and survivors [A5][A6]; plain, respectful language; frame the tool as information the worker has a right to. State openly that the root causes (fees, weak labour enforcement, poverty) need policy action, not apps. |
| **Misplaced trust in data** | Counts and estimates are uncertain [A8]. | Present figures as estimates with sources; avoid alarmist messaging. |

### 2.5 Open questions

- Which countries publish recruiter licence data in a form that can legally be republished?
  This needs checking country by country.
- Who holds the signing key, and how is that group accountable?
- How can impact be measured without tracking users? Partner-run, consented surveys are the
  current proposal.
- Would platform companies accept an ad pattern feed, as the OHCHR briefing recommends platforms
  address fraudulent job ads [S5], without it becoming a tool to block legitimate job seekers?

### 2.6 Limits of this study

This is desk research. No migrant workers, survivors or practitioners were consulted, and that
consultation must come before any build. The "nothing like this exists" finding is based on a
limited search. Figures come from the cited reports and carry their methods' uncertainty.
