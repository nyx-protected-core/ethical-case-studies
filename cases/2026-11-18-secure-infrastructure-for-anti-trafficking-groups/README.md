# Open Infrastructure for People at Risk: A Systems-Engineering Case Study

*A research note written from a Linux and systems-administration viewpoint. It proposes three problems where open infrastructure can help, then examines the strongest one in depth: secure, low-maintenance infrastructure for anti-trafficking organisations and human-rights defenders.*

*Status: research and design only. Nothing described here has been deployed or tested. Figures are quoted from the sources listed at the end; every link and DOI was checked to load or resolve on 18 November 2026.*

---

## Part 1: Three problems and short solutions

### 1. Offline servers for schools and clinics in low-connectivity regions

**Problem.** The ITU estimates that in 2024 about 2.6 billion people, one third of the world's population, were still offline. In low-income countries only 27 per cent of people use the Internet, and the average for Africa is 38 per cent [6].

**Solution.** A small Linux box (a Raspberry Pi or a recycled PC) that serves a local Wi-Fi network with offline copies of Wikipedia, maps, medical references and learning platforms. Internet-in-a-Box already does this and can bundle learning platforms such as Kolibri and Moodle [25]. The systems work that remains is mostly about operations: power-loss-safe filesystems, content updates delivered on USB drives, and hardware that a local teacher can replace.

### 2. Digital security for anti-trafficking organisations and human-rights defenders

**Problem.** The groups that document trafficking and abuse hold some of the most dangerous data there is: names, locations and testimony of survivors and witnesses. They are also targets themselves. In 2023, surveillance made up 9.8 per cent of the violations reported to one international protection organisation, and at least 300 defenders were killed [4]. Commercial spyware has been traced to operations in 45 countries [5].

**Solution.** A minimal, hardened and offline-first case-records server, paired with encrypted field-collection apps on phones. It should be designed to be safe when seized, cheap to run, and maintainable by one part-time, non-specialist person. This is the subject of Part 2.

### 3. Keeping NGO software alive after the grant ends

**Problem.** Open-source tools for civil society are often built on project grants, while maintenance needs steady funding over many years. A well-known report on open-source maintenance describes public code as infrastructure that is "built and maintained almost exclusively by unpaid volunteers" [16]. Martus, a pioneering encrypted human-rights documentation tool, stopped receiving updates after about 15 years. Its maintainer judged that the platform could no longer keep up with technical and security demands [17].

**Solution.** Choose boring, widely packaged components (a Debian stable base, distribution packages, standard backup tools) over bespoke stacks. Write the exit plan, covering data export in open formats and a migration path, before deployment. Fund maintenance as a line item rather than as a project.

### Choosing the strongest idea

All three are real. Idea 2 is chosen for three reasons:

- **Severity of harm.** A failure here can get a survivor or witness re-trafficked, threatened or killed. It is not just an inconvenience.
- **Fit with systems skills.** Most of the harm comes from operational failures: unpatched machines, unencrypted disks, missing or unencrypted backups, and logs full of personal data. These are exactly the failures that Linux administration practice addresses.
- **It absorbs the other two.** A good design for idea 2 has to work offline (idea 1) and has to outlive its funding (idea 3).

---

## Part 2: Secure, maintainable infrastructure for anti-trafficking and human-rights organisations

### 2.1 Problem scale

**Trafficking and modern slavery.**

- The ILO, Walk Free and IOM estimate that 50 million people were living in modern slavery in 2021: 28 million in forced labour and 22 million in forced marriage. That is 10 million more than the 2016 estimate [1]. The report's more precise figures are 49.6 million in modern slavery, of whom 27.6 million were in forced labour [1].
- UNODC's 2024 Global Report on Trafficking in Persons covers 156 countries [2]. It records a 25 per cent rise in detected victims in 2022 compared with 2019 [3]. Over the same period, detected victims of trafficking for forced labour rose 47 per cent and detected child victims rose 31 per cent. Children made up 38 per cent of detected victims, and women and girls 61 per cent [3].
- UNODC also reports that trafficking for forced criminality, including forced work in online scams, grew from 1 per cent of detected victims in 2016 to 8 per cent in 2022 [3]. Technology is now part of how trafficking itself operates.

Detected victims are a small fraction of the estimated total, so the people and organisations holding records about identified victims hold rare and sensitive information.

**Attacks on defenders and civil society.**

- Front Line Defenders' Global Analysis 2023/24 documents the killing of at least 300 human-rights defenders in 28 countries in 2023, and nearly 3,000 over the past decade [4]. Its casework statistics, based on 1,538 reported violations in 105 countries, list arbitrary arrest or detention (15 per cent), legal action (13 per cent), death threats (10.2 per cent), surveillance (9.8 per cent) and physical attacks (8.5 per cent) as the leading violations [4].
- The Citizen Lab's 2018 "Hide and Seek" study found 1,091 IP addresses matching its fingerprint for NSO Group's Pegasus spyware. It clustered them into 36 distinct operators and identified 45 countries where operators may be conducting surveillance [5].

**What the research says about the people involved.**

- An interview study of victim service providers and trafficking survivors, which won a distinguished paper award at USENIX Security 2019, found a central tension: staff build trust by giving clients autonomy, while also trying to steer their technology use to reduce the risk of revictimisation [7].
- Studies of political activists during the Sudanese revolution [8] and of refugees in the United States [9] show that people's security practices are shaped by local threats, trust networks and intermediaries such as case workers, so generic security advice often does not fit.

**Summary.** The people most in need are survivors and the small organisations that serve them. They often have no dedicated IT staff and work under threat. Their adversaries include traffickers, abusive partners and, in some countries, the state.

### 2.2 What already exists

| Tool | What it does | Relevance and gaps |
|---|---|---|
| **Tella** [18] | An open-source mobile app that encrypts photos, video and audio at capture, hides them from the phone's gallery, works offline, and can submit to Uwazi, Open Data Kit, Nextcloud and other back ends. | A strong field-collection front end. It needs a trustworthy server to submit to. |
| **Uwazi** (HURIDOCS) [19] | An open-source documentation database used by more than 350 organisations, with over 550 databases and 3.4 million records. | A strong record store. As open-source software it can be self-hosted, which moves the operational burden onto the organisation. |
| **Martus** (Benetech) [17] | End-to-end encrypted documentation with backup to servers. It was used offline and, by one group, on Tails. | **Discontinued.** Its maintainer judged that a 15-year-old product could not keep up. Self-hosted servers keep running but get no security patches. The lesson: plan for the tool's death on day one. |
| **Signal** [26] | End-to-end encrypted messaging. Its protocols (X3DH/PQXDH, Double Ratchet) are publicly specified. | The default for staff communication. It does not manage cases. |
| **Tails** [20] | An amnesic Debian-based live system that routes all traffic through Tor, with optional encrypted persistent storage. | Useful for the highest-risk individual work. It is not a multi-user server. |
| **Security in a Box** [21] | Maintained, plain-language digital-security guides for defenders, covering Linux, backups and destroying sensitive information. | The training layer. Any deployment should link staff to it rather than write its own. |

**The gap.** The field-collection tools (Tella) and the record stores (Uwazi) are mature. What remains under-served is the layer between them: a small organisation's own server, run safely by one non-specialist for years. It should run without exposed services or silent backup failures, it should not write personal data to logs, and it should leave nothing readable on a seized disk.

### 2.3 Proposed solution: the "quiet box"

**Goal.** A single small server, such as a fanless mini-PC, in the organisation's office. It holds case records and receives submissions from staff phones. It is safe if stolen or seized, it patches itself, it backs itself up with encryption, and it tells a human when something is wrong. It is a reference design, not a product.

#### Design principles

1. **Collect less.** The safest record is one that was never created. The case schema stores only what the service actually needs, such as a pseudonymous case ID, needs and referrals. Identity documents and full testimony go into a separate, more restricted store or are not digitised at all. This follows the data-minimisation principle in the ICRC Handbook [14].
2. **Encrypted at rest, locked when off.** Use full-disk encryption with LUKS, managed by cryptsetup [24], unlocked by a passphrase that a person enters at boot. Do not use automatic unlock: if the box is taken while powered off, the disk is unreadable. This trades convenience (someone must be present after a power cut) for protection against seizure.
3. **Not on the public Internet.** The server listens only on the office LAN and on a private VPN for staff phones. It exposes no web admin panel and accepts no inbound connections from the Internet. That shrinks the attack surface to almost nothing.
4. **Boring, packaged components.** Use Debian stable and distribution packages wherever possible, so that security updates arrive through one channel and outlive any single project. This applies the lesson of Martus and of idea 3.
5. **Two people, one page.** Every routine task, such as checking backups, rotating the offline drive, or restoring a test file, fits on one printed page that a non-specialist can follow.

#### Components and why

| Layer | Choice | Why |
|---|---|---|
| Base OS | Debian stable, minimal install | A long support window, security updates through one channel, and widely documented. |
| Patching | `unattended-upgrades`, security origin only, scheduled by the systemd timers APT already ships [22] | Unpatched machines are the most common failure in small organisations. Security-only updates rarely change behaviour, while feature upgrades are left for a planned maintenance day. |
| Disk | LUKS2 full-disk encryption [24] | Protects against theft and seizure while the machine is off. |
| Services | Each application runs as a systemd service with sandboxing directives (no new privileges, read-only system directories, private temp) | Limits the damage if one application is compromised. Using systemd keeps it standard rather than bespoke. |
| Logs | journald with persistent storage, a short retention limit, and applications configured not to log request bodies | Logs are a classic place where survivor names leak. Keep enough to debug, for days rather than years. |
| Backups | restic [23]: encrypted, deduplicated snapshots to (a) a USB drive rotated off-site weekly and (b) an optional remote repository; run by a systemd timer, with `restic check` run on a schedule | Encryption happens before data leaves the box, so a lost backup drive is not a breach. Two destinations protect against a single disaster. A scheduled integrity check catches silent corruption. |
| Retention | A systemd timer that deletes or anonymises closed cases after an agreed period | Data that no longer exists cannot be seized or leaked. The rule is enforced by the machine rather than by memory. |
| Field intake | Tella on staff phones, submitting to the box over the VPN [18] | Data is encrypted at capture and the app works offline, so no tool has to be invented. |
| Record store | Uwazi or a similar open-source case store, self-hosted [19] | Mature, maintained by an organisation whose mission is human-rights documentation. |
| Alerts | A daily health summary (disk space, last good backup, pending reboots) sent over an end-to-end encrypted channel | A silent failure is the main risk when nobody is watching. |

#### Where a real server differs from a laptop or a container

- **Boot and unlock.** A laptop is unlocked by its user every day. A server may reboot unattended after a power cut. With passphrase-only LUKS it then waits, locked, for a person to come in. That is deliberate. The organisation must plan for it, for example with a small UPS and a named person who can unlock the box.
- **systemd as the supervisor.** In a container the platform restarts processes. On a real server, systemd units with restart policies, sandboxing directives and timers take on that role, and replace scattered cron jobs with something inspectable.
- **journald has to be tuned.** By default a server keeps logs for as long as disk space allows. Here retention is capped on purpose, and logs live on the encrypted disk.
- **Updates actually change the machine.** Unattended upgrades on a live box can restart services. Security-only origins plus a regular, announced reboot window keep this predictable.
- **Physical security is real.** A server sits in a room that someone can walk into. Its threat model includes theft, seizure and coercion of whoever holds the passphrase. A cloud VM's threat model does not.

#### What this deliberately does not do

It does not analyse data, predict "hotspots", match faces, or share data with authorities automatically. Research on anti-trafficking technology warns against "tech solutionist fixes" for problems that need political will [10]. This design only keeps an organisation's own records safe and available.

### 2.4 Ethical risks

| Risk | What can go wrong | Mitigation in this design | Residual risk |
|---|---|---|---|
| **Harm to survivors through data** | A leak or seizure exposes survivors to traffickers, abusers or deportation. | Minimise collection, encrypt at rest and in backups, enforce retention, keep logs free of personal data. | Coercion of staff can defeat any encryption. |
| **Surveillance and dual use** | The same tools could be used to monitor the people served, or a donor or state partner could demand access. | No analytics or tracking features. Data sharing is a human decision, recorded in policy. | A future administrator could add tracking; governance must prevent it. |
| **Seizure and coercion** | Police or criminals take the box and pressure staff for passphrases. | Off means locked. Keep only minimal data. Plan for raids, following existing guidance [21]. | Staff safety comes first. No design should ask a person to resist coercion. |
| **Consent** | Survivors in crisis cannot meaningfully consent to complex data processing. Individual consent alone is a weak basis [12]. | Collect only what the service needs, explain it plainly, and keep a default of not digitising sensitive testimony. | Power imbalance between service and client remains [7]. |
| **Experimentation on vulnerable people** | Piloting new technology on people with no alternative is itself a harm [11]. | Use mature, maintained tools. No novel cryptography, no pilots on survivors. | Even mature tools are new to each organisation. |
| **Dependence** | The organisation comes to depend on one outside volunteer or vendor. | Standard components, a one-page runbook, two trained staff, documented export. | Skilled help may still be scarce locally. |
| **Sustainability** | The tool dies (as Martus did [17]) or the volunteer leaves, leaving an unpatched box full of sensitive data. | Debian stable updates outlive any single project. An exit plan and an open export format exist from day one. | Hardware ageing and Debian release upgrades still need someone. |
| **Do no harm** | Building infrastructure that is worse than the paper files it replaces. | Use the Signal Code's rights, including privacy and security, data agency and rectification [15], and the ICRC Handbook [14], as review checklists before go-live. | Judgement calls remain with the organisation, not the technologist. |

The underlying principle comes from the humanitarian literature. The rights to protection, privacy and security apply to information activities, not only to physical aid [15]. Introducing a new risk is ethically different from managing an existing one [11].

### 2.5 Limitations and open questions

- **Not tested.** This is a design. It has not been built, audited or trialled with any organisation. A real deployment would need a security review and a pilot with a willing, informed organisation, on non-survivor test data first.
- **Context varies sharply.** In some countries the main adversary is the state, and holding records at all is dangerous. There, the right answer may be no server and no digital records. Local threat assessment comes before any technology.
- **Human factors dominate.** The research cited here [7][8][9] shows that shared phones, trust and intermediaries matter more than cipher choices. The design's success depends on training and on the organisation's own culture.
- **Passphrase at boot is a real cost.** After a power cut, nobody can use the system until someone unlocks it. Whether that trade-off is acceptable is an open question for each organisation.
- **Backups are both a risk and a requirement.** Off-site copies protect against loss but add another place to seize. The balance between the number of copies and exposure needs case-by-case judgement.
- **Funding.** Open-source sustainability research shows that continued project activity depends on factors beyond code quality, including a project's position in its dependency network and its organisational support [13]. Who pays the person who reads the daily health alert in year three?
- **Legal duties.** Data-protection law, mandatory reporting and court orders differ by country and may conflict with minimisation. This note does not give legal advice.

---

## References

All links and DOIs were checked to load or resolve on 18 November 2026. Peer-reviewed items are marked **[PR]**.

**Problem scale and context**

1. International Labour Organization, Walk Free and International Organization for Migration (2022). *Global Estimates of Modern Slavery: Forced Labour and Forced Marriage.* Geneva: ILO. https://www.ilo.org/publications/major-publications/global-estimates-modern-slavery-forced-labour-and-forced-marriage (summary figures also at https://www.walkfree.org/reports/global-estimates-of-modern-slavery-2022/)
2. United Nations Office on Drugs and Crime (2024). *Global Report on Trafficking in Persons 2024.* https://www.unodc.org/unodc/data-and-analysis/glotip.html
3. United Nations Office on Drugs and Crime (11 December 2024). "UNODC global human trafficking report: detected victims up 25 per cent as more children are exploited and forced labour cases spike." Press release. https://www.unodc.org/unodc/en/press/releases/2024/December/unodc-global-human-trafficking-report_-detected-victims-up-25-per-cent-as-more-children-are-exploited-and-forced-labour-cases-spike.html
4. Front Line Defenders (2024). *Global Analysis 2023/24.* https://www.frontlinedefenders.org/en/resource-publication/global-analysis-202324
5. Marczak, B., Scott-Railton, J., McKune, S., Abdul Razzak, B. and Deibert, R. (2018). "Hide and Seek: Tracking NSO Group's Pegasus Spyware to Operations in 45 Countries." Citizen Lab, University of Toronto. https://citizenlab.ca/2018/09/hide-and-seek-tracking-nso-groups-pegasus-spyware-to-operations-in-45-countries/
6. International Telecommunication Union (2024). *Measuring Digital Development: Facts and Figures 2024 – Internet use.* https://www.itu.int/itu-d/reports/statistics/2024/11/10/ff24-internet-use/

**Security and privacy research on affected people**

7. Chen, C., Dell, N. and Roesner, F. (2019). "Computer Security and Privacy in the Interactions Between Victim Service Providers and Human Trafficking Survivors." *28th USENIX Security Symposium*, pp. 89–104. **[PR]** (no DOI; open access) https://www.usenix.org/conference/usenixsecurity19/presentation/chen
8. Daffalla, A., Simko, L., Kohno, T. and Bardas, A. G. (2021). "Defensive Technology Use by Political Activists During the Sudanese Revolution." *2021 IEEE Symposium on Security and Privacy*, pp. 372–390. **[PR]** https://doi.org/10.1109/SP40001.2021.00055
9. Simko, L., Lerner, A., Ibtasam, S., Roesner, F. and Kohno, T. (2018). "Computer Security and Privacy for Refugees in the United States." *2018 IEEE Symposium on Security and Privacy*, pp. 409–423. **[PR]** https://doi.org/10.1109/SP.2018.00023

**Ethics, technology and humanitarian data**

10. Musto, J., Thakor, M. and Gerasimov, B. (2020). "Editorial: Between Hope and Hype: Critical evaluations of technology's role in anti-trafficking." *Anti-Trafficking Review*, 14. **[PR]** https://doi.org/10.14197/atr.201220141
11. Sandvik, K. B., Jacobsen, K. L. and McDonald, S. M. (2017). "Do no harm: A taxonomy of the challenges of humanitarian experimentation." *International Review of the Red Cross*, 99(904), pp. 319–344. **[PR]** https://doi.org/10.1017/S181638311700042X
12. Raymond, N. A. (2017). "Beyond 'Do No Harm' and Individual Consent: Reckoning with the Emerging Ethical Challenges of Civil Society's Use of Data." In *Group Privacy*, pp. 67–82. Cham: Springer. **[PR, edited volume]** https://doi.org/10.1007/978-3-319-46608-8_4
13. Valiev, M., Vasilescu, B. and Herbsleb, J. (2018). "Ecosystem-level determinants of sustained activity in open-source projects: a case study of the PyPI ecosystem." *Proceedings of ESEC/FSE 2018*, pp. 644–655. ACM. **[PR]** https://doi.org/10.1145/3236024.3236062
14. International Committee of the Red Cross. *Handbook on Data Protection in Humanitarian Action* (3rd ed.). https://www.icrc.org/en/data-protection-humanitarian-action-handbook
15. Greenwood, F., Howarth, C., Escudero Poole, D., Raymond, N. A. and Scarnecchia, D. P. (2017). *The Signal Code: A Human Rights Approach to Information During Crisis.* Harvard Humanitarian Initiative. https://hhi.harvard.edu/publications/signal-code-human-rights-approach-information-during-crisis
16. Eghbal, N. (2016). *Roads and Bridges: The Unseen Labor Behind Our Digital Infrastructure.* Ford Foundation. https://www.fordfoundation.org/learning/library/research-reports/roads-and-bridges-the-unseen-labor-behind-our-digital-infrastructure/

**Software and technical documentation**

17. Benetech. *Martus FAQs* (including the discontinuation notice). https://martus.org/resources/faqs.html and https://martus.org/
18. *Tella.* https://tella-app.org/
19. HURIDOCS. *Uwazi.* https://uwazi.io/ and https://huridocs.org/
20. Tails Project. *How Tails works.* https://tails.net/about/index.en.html
21. *Security in a Box: Digital security tools and tactics.* https://securityinabox.org/en/
22. Debian Wiki. *UnattendedUpgrades (PeriodicUpdates).* https://wiki.debian.org/UnattendedUpgrades
23. *Restic Documentation.* https://restic.readthedocs.io/en/stable/
24. *cryptsetup and LUKS – open-source disk encryption.* https://gitlab.com/cryptsetup/cryptsetup
25. *Internet-in-a-Box.* https://internet-in-a-box.org/
26. Signal. *Technical information: specifications.* https://signal.org/docs/
