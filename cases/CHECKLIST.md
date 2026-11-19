# Publication checklist

Every case study must pass every item below before it is added to `INDEX.md`. A case that fails an item is held, fixed and checked again. Older cases are being brought into line with this list; until a case has been checked against it, treat its gaps as known issues, not as exceptions.

## 1. Sources

- [ ] Every factual claim is cited, and every source is public and cited by a working link (DOI, publisher page, official page or open copy).
- [ ] Load-bearing figures were checked in the source itself, not in a search summary, a press story or another case. Where only an abstract or summary was read, the case says so next to the claim.
- [ ] No unpublished, internal or private material is cited as a source. If an unpublished review shaped the work, its points are restated as the case's own reasoning and marked as not publicly sourced.
- [ ] When a working paper and a published version differ, the case names the version used (for example "NBER Working Paper 26669, November 2021 revision, which the 2024 journal article follows") and does not mix figures from different versions.
- [ ] A figure from a regional, national or sectoral study is described at the level the source measured it. A multi-country rate is not presented as one country's rate.
- [ ] Figures are dated (survey year or edition), and the latest edition of a regularly updated series is used, or the case says why not. When companion cases quote the same series, they use the same edition or label the difference.
- [ ] A legal description (what a law requires, who must publish what) is checked against the law's text or a source that quotes it, and the case says which.

## 2. Required sections

- [ ] **Unverified or uncertain items.** A section that lists every claim not checked in the source, every estimate, and every open question. If there are none, it says "None" and states what was checked.
- [ ] **What this does not replace.** A plain statement of what the case or design must not be used instead of (for example legal advice, a maintenance system, a water-quality test, a human caseworker, field calibration).
- [ ] **Fair counterarguments.** The strongest evidence and arguments against the case's conclusion, stated fairly and with sources, and an explanation of how the conclusion handles them. Where careful studies disagree, the case reports both and says the question is open.

## 3. Hardware and design cases (in addition)

- [ ] **Active-lifecycle parts.** Every part is confirmed Active on the **manufacturer's** own product or lifecycle page. A distributor label (for example LCSC "normal") is necessary but not enough. Parts not yet confirmed are listed in the unverified section.
- [ ] **Licence check.** A table lists the licence of the design itself and of every reused piece of code, library, reference layout, artwork, recording or text, as read in the licence file or on the page. Anything not yet read is marked UNVERIFIED and must not be copied until it is.
- [ ] **False reassurance.** A section on how the device or tool could give a confident wrong answer, or silence that looks like "fine", and the design rule against each.
- [ ] **Worst-case electronics.** Power, voltage and timing claims hold at datasheet worst case, not only typical values (for example an LED's maximum forward voltage against the battery's real voltage).
- [ ] **Radio and data.** Any design that transmits says why, what it sends, who controls the data, and why the offline rule does not apply.

## 4. Privacy and hygiene

- [ ] No private person's name, no health details about any individual, no secrets, keys or credentials.
- [ ] No local file paths, machine names, usernames, internal tool names, or names of the assistants or team that produced the case.
- [ ] No location-revealing timestamps: give dates only (for example "checked 18 November 2026"). No clock times, city time-zone names or local UTC offsets. A source's own published time (for example a UN situation report stamped 15:00) may be quoted as part of the citation.
- [ ] No internal process language (review round numbers, "must-fix" labels, brief numbers) in the published text; describe changes in a neutral, dated changelog.
- [ ] Scan every file before publishing for author, team and tool names, local paths, personal email addresses, and time-zone strings (for example `rg -in '/home|@gmail|\bbots?\b|[a-z]+/[a-z_]+ time|utc[+-][0-9]'`, plus the maintainers' private list of names). Known false positives: `/home/` inside some publisher URLs, and the phrase "bot-challenge".

## 5. Gender cases (in addition)

- [ ] Gender cases never mention motherhood, pregnancy, childcare, maternity or parental status, and carry no disclaimer about leaving them out. Scan:
  `rg -in 'mother|pregnan|childcare|maternity|parental'`
- [ ] Gender equality is argued on equal merit, and gaps that disadvantage men or boys are reported with the same care as gaps that disadvantage women or girls.

## 6. Before adding to the index

- [ ] The case lives at `cases/<category>/<slug>/`, is listed in that category's `README.md`, and has a line under that category in `INDEX.md`. The index and category lines match the case's own title and framing.
- [ ] The files' checksums are recorded with the publish request.
