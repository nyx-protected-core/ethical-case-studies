# Design study: an offline, tamper-evident wage and hours logbook owned by the worker

Case study. Revision 2, 18 November 2026 (ICT, UTC+7); revision 1 was issued earlier the same day. Research and
design only: nothing has been built or tested. Sources are numbered [S#] and listed in SOURCES.md.
**UNVERIFIED** marks a claim or figure I could not confirm from a primary or official source. "LCSC
lifecycle" means the label on LCSC's product page (`normal`, `stop_product`, `sold_out`). That is the
distributor's label, not a manufacturer lifecycle notice.

> **What changed in this revision.** Two reviews changed the Tier B handheld: a parts, firmware and licences
> peer review [S63], and a security review [S64]. Twelve must-fixes are adopted, five security and seven
> hardware. Section 8 has the details.
>
> - **The device now protects the worker from the device.**
>   - Every record is encrypted in flash by default.
>   - The on-device history view is gone. The unlocked screen shows only today's entries and a count.
>   - The decoy view is removed from the pilot.
>   - Export goes only to an authenticated NGO reader. Each export is bound to a fresh challenge and encrypted to
>     the NGO's key.
>   - The NGO keeps its own signed receipt of each visit, so erased or rewritten records show up as removed.
>   - The signing key is rotated at each visit, and the secure element's counter is bound into signatures where
>     the chip allows.
> - **Plain limit.** A well-funded laboratory could extract the encryption key, and anything derived from a PIN
>   or unlock pattern, from the secure element. No extraction of its P-256 signing key has been published.
> - **Hardware.**
>   - The default power source is now 3×AAA. A coin cell is kept only for a variant whose screen is blank when
>     idle.
>   - The MCU moves to the STM32L031F6P6 (32 KB flash, 8 KB RAM).
>   - The secure element moves to the UDFN package.
>   - The IR receiver is a black-lens part behind an IR-pass window, powered only during export.
>   - IR can never wake the device, and the factory bootloader is closed.
>   - The display is sourced from an authorised distributor (LCSC has none in stock), or replaced by custom
>     segment glass.
> - **Licences.** TinyCrypt is dropped, and the verifier is kept free of Microchip's library.

---

## 0. Summary

- **Who it is for.** Migrant fishers and farm workers in Thailand, mostly from Myanmar, Cambodia and Laos.
  Wage theft and debt bondage are common and well documented [S1][S3][S4][S5]:
  - 87% of 1,087 fishers surveyed were in debt for document "fees" and advances [S1];
  - 20% of migrant fishers were in forced labour [S3];
  - 58.7% of migrant farm workers working full time were paid below the provincial minimum wage [S4][S5].
- **The gap is evidence the worker controls.** In fishing, employers often hold the workers' bank books and ATM
  cards. They transfer the contract wage, which creates a paper trail for inspections, then withdraw it and pay
  less in cash [S1][S2]. Payment records then show the employer's version, and the worker usually has no
  receipt [S1].
- **Proposal: two tiers.**
  - **Tier A, a tamper-evident paper logbook,** stamped by an NGO at each visit. This is the first pilot.
  - **Tier B, a small offline handheld.** It has buttons, a reflective low-power display, a low-power MCU and a
    secure element. The secure element signs each record and holds the record-encryption keys. The log is
    encrypted, append-only and hash-chained. There is no radio, no location and no sensors. Export is by
    infrared, only to an authenticated NGO reader, and only with the worker present and pressing to consent.
  - **Rejected: a passive NFC card written by an employer-side reader** (section 2.4).
- **Cost.**
  - Verified LCSC core parts for Tier B cost USD **4.41 at qty 100 and 3.80 at 1000** without the display.
  - The display, from an authorised distributor, adds about USD **12.39 / 7.97 / 7.74** at qty 1 / 100 / 500.
    DigiKey showed **31,403** in stock on 18 November 2026 (UTC+7); that stock figure is confirmed. One
    aggregator listed the same part as "Obsolete", so manufacturer lifecycle is still noted in the parts table.
  - A whole unit is roughly **USD 40–62 / 22–30 / 17–22** at 1 / 100 / 1000, using unverified estimates for
    PCB, assembly and enclosure (section 2.7).
- **Honest limits.**
  - **What a log can prove.** A self-kept electronic log (Tier B) may not be refused as evidence solely because
    it is electronic. ET Act section 11 covers data messages; its weight depends on how the record was generated,
    stored and kept intact, and how its originator is identified [S15]. Tamper evidence shows records were not
    changed after entry; it cannot show they are true. The admissibility and weight of a *paper* log (Tier A)
    under Labour Court procedure is for the legal-aid lawyers to confirm (**UNVERIFIED**). A captain who holds
    the worker can force entries, an unlock or an erase (section 7). The design aims to reveal little when
    forced, to survive the device's loss, and never to deceive the employer.
  - **What a lab can do.** A well-funded laboratory could extract the encryption key, and anything derived from
    a PIN or pattern.
- **Go/no-go.** No hardware until worker groups, legal-aid lawyers and an inspectorate contact confirm need,
  safety and evidential weight (sections 1.5 and 6). The paper-logbook pilot has a further go/no-go: co-design
  must first decide whether a book that can be discovered by the employer is acceptably safe (section 2.1).

---

## 1. Problem and evidence

### 1.1 Scale of wage theft and debt bondage

| Finding | Figure | Source |
|---|---|---|
| Thai fishers in debt to their boat owner for document "fees" and cash "advances" (survey of 1,087 fishers, Jan 2024) | 87.0%; most (68.6%) owe THB 10,000–30,000 | [S1] |
| Fishers not paid monthly by bank transfer as Thai law requires | 99.4% | [S1] |
| Fishers not in control of their own bank books and ATM cards | 98.3% | [S1] |
| Fishers whose documents are held by the captain or owner | 83.6% | [S1] |
| Migrant fishers in forced labour, Thailand (ILO regional survey, 2026) | 20% (15% across fishing and seafood processing) | [S3] |
| Migrant farm workers working full time but paid below the provincial minimum wage (sugarcane, rubber, oil palm, maize; survey of 528 workers) | 58.7%; women 65.7%; Tak province 95.4% | [S4][S5] |
| Migration costs for Cambodian, Lao and Myanmar workers going to Thailand | about 2 months of Thai earnings; recruitment fees about 80% of total costs | [S6] |
| Thailand labour migration profile: borrowing to migrate, Myanmar workers in debt | 70% borrowed; 34% of Myanmar workers in debt, average about USD 800 | [S8] |

Qualitative evidence:
- **HRW, *Hidden Chains* (2018).** Wage withholding, document retention and debt-linked recruitment in the
  fishing fleet. Reforms and inspections had not protected workers [S10].
- **US TIP Report 2025 (Thailand).** Gaps in victim identification and corruption [S9]. My earlier reading of
  the report also found employers withholding bank cards and PINs, and ineffective inspections. That detail
  is not re-quoted from the page this round (**UNVERIFIED wording**).
- **FRN and HRDF case studies.** Inspectors (PIPO) arrange "informal settlements" face to face with the
  abuser. Claimed debts are erased in exchange for dropping abuse charges. Owners rarely give receipts for
  payments, advances or "fees" [S1][S2].

### 1.2 What the law requires of employers, and why that helps the worker

- **Labour Protection Act B.E. 2541, sections 112–115.** Employers of 10 or more employees must:
  - keep employee records;
  - keep wage documents showing working time, piece-work and amounts paid;
  - have employees sign evidence of payment, though a bank-transfer record counts as evidence;
  - keep the records for at least 2 years, and longer while a complaint or lawsuit is pending [S11].
- **Sea fishing.** The ministerial regulation of B.E. 2565 (2022) requires:
  - rest records available to inspectors;
  - payment at least monthly;
  - wages paid into the fisher's bank account, with the transfer record deemed evidence of payment [S12][S2].

  The FRN survey shows why that deemed-evidence rule is weak when the employer holds the ATM card [S1].
- **The HRDF cases (2022–23).** These are the strongest real-world evidence for this project [S2].
  - When the employer could not show clear proof of full payment, the labour inspector found for the workers
    "who could produce the payment records".
  - In one case the workers said they had recorded each cash payment as they received it.
  - The inspector ordered the outstanding wages paid with 15% annual interest.
- **Farm work.** The Ministerial Regulation on Agricultural Work B.E. 2557 (2014) removes minimum wage,
  normal-hours and overtime protections for seasonal or occasional farm work [S5]. A log still matters for
  agreed pay, unpaid days, deductions and debt, but there is less statutory ground to stand on.

### 1.3 Where a worker's log goes, and how it is weighed

- **DLPW complaint (Form คร.7).** Filed with the labour inspector at the local Labour Protection and Welfare
  office. Bring ID, proof of employment, payslips, attendance records and evidence of the amount owed. The
  inspector must issue an order within 60 days, extendable by 30. Hotlines are 1546 (DLPW) and 1506 (Ministry
  of Labour) [S17][S18]. I did not read the Ministry's own complaint manual (**UNVERIFIED** detail).
- **Labour Court.** Its procedure is inquisitorial: the court may call witnesses and evidence on its own
  initiative [S14]. In practice a worker's log can prompt the court or the inspector to demand the employer's
  statutory records (section 1.2). Contradictions between the two then become the issue.
- **Electronic records.** ET Act section 11 [S15][S16]:
  - A data message may not be refused as evidence solely because it is electronic.
  - Its weight depends on how it was generated, stored or communicated; how its integrity was maintained; and
    how its originator was identified.

  Tier B is designed to answer each of those three factors (section 3).
- **NGOs and brand grievance channels.** Issara's Golden Dreams app and helplines, and Ulula-type multi-channel
  grievance systems, take worker reports as the start of remediation, not as court evidence [S20][S21][S23].
  US DOL ILAB notes that smartphone feedback tools are not grievance mechanisms in themselves: they provide no
  remedy [S22].
- **A precedent abroad.** The US DOL has offered a worker timesheet app since 2011. It says a worker's own
  records can be invaluable when employer records are missing or inaccurate [S25]. That is US practice, not
  Thai law.

**Honest assessment.**
- A worker's self-recorded *electronic* log is admissible under ET Act section 11 and can be persuasive,
  especially with inspectors when the employer's records are absent or plainly fictitious [S2][S15]. The
  admissibility and weight of a paper log under Labour Court procedure is **UNVERIFIED** and should be
  confirmed by legal-aid lawyers before the Tier A pilot relies on it as evidence.
- On its own it does not prove the truth of what is written.
- What adds credibility, in rough order of value:
  1. Entries made at the time, with a visible gap between the entry time and the event time.
  2. Independent time anchors (an NGO stamp or signature at intervals).
  3. Corroboration: co-workers' parallel logs, photos of payslips or cash envelopes, bank statements, and
     employer records obtained by the inspector.
  4. Witness countersignatures.
  5. A documented chain of custody from the worker to the NGO.
- No Thai ruling on a hash-chained device log was found (**UNVERIFIED**). A lawyer's opinion is a
  precondition (section 6).

### 1.4 Existing tools

| Tool | What it does | Limits for this need |
|---|---|---|
| Issara Golden Dreams app and helplines (Myanmar 1-800-010-180, Khmer -181, Lao/Thai -182) | Employer and recruiter reviews, rights information, reporting; 489,726 worker-reported violations remediated 2021–2025, per MIT Solve | Needs a smartphone and data; reporting tool, not a record the worker holds [S20][S21] |
| Ulula | Anonymous grievance and survey by SMS, voice, app and WhatsApp, with case management | Brand or supplier-run channels; data held by the operator [S23] |
| UNU Apprise | Inspector-side screening app used at PIPO centres, with audio questions in workers' languages | Inspector-held, point-in-time screening [S24] |
| Digital wage payment (bank transfer mandate in fishing) | Creates a bank trail | The trail can be fictitious when the employer holds the card [S1][S2] |
| Paper notebooks kept by workers | Personal record of days and cash | Easy to dismiss as written later; easy to confiscate or destroy [S2] (practice inferred from the HRDF case) |
| US DOL Timesheet app | Self-recorded hours and pay | US, smartphone-only, no integrity protection described [S25] |

### 1.5 Would a device without a smartphone help? Arguments both ways

**For:**
- Long-haul fishers can be at sea for months without network, so phones are often useless there.
- Phones are shared, confiscated or monitored, and run out of battery.
- A phone note or spreadsheet can be edited at any time and carries no integrity protection.
- A low-literacy, icon-based single-purpose device may be easier than an app.
- The ALDI/Issara worker-voice material I read earlier noted low phone ownership among Burmese women and
  fear of reprisal. That was not re-checked this round, so the source is not listed (**UNVERIFIED**).

**Against:**
- It is one more object to hide, and it may be confiscated.
- Paper is cheaper and needs no batteries.
- Many workers already have smartphones: Golden Dreams' scale shows that [S21]. A tamper-evident app plus
  paper might reach more people for less.
- A device that labour advocates hand out could mark its holder as a complainant.

**Conclusion.** Build Tier A first. Build Tier B only for a co-designed subgroup, probably long-voyage fishers,
where partners confirm phones fail.

---

## 2. Hardware options

### 2.1 Option (a): tamper-evident paper logbook (recommended first pilot)

- **Format.**
  - A5 or smaller, sewn binding, so a removed page is visible.
  - Every page pre-numbered, with the book's serial number on each page.
  - Icon columns for: date, start, end, rest, days worked, pieces or kilograms, cash received, deduction
    (with icons for food, document fee, advance, debt, other), document taken or returned, and sick day.
  - Headings in Burmese, Khmer, Lao and Thai.
- **Paper anchor.** At each NGO visit the worker presents the book. The NGO worker draws a line under the last
  entry, then stamps, dates and signs across it. With consent, the NGO also photographs the pages. Entries
  before the anchor cannot later be added without visibly breaking the sequence.
- **Optional carbon duplicate.** A copy page the worker can leave with a trusted person ashore.
- **Waterproofing.** Synthetic (polymer) paper and a zip pouch (**UNVERIFIED**: no supplier or price checked).
- **Cost.** Probably under USD 2 per book at 1000 from a Thai printer (**UNVERIFIED estimate**; no quote).
- **Weaknesses.**
  - The whole book can be rewritten.
  - It can be confiscated or destroyed.
  - It is conspicuous, and it needs some literacy for numbers.

**Threat analysis for the paper book (Tier A).** Section 4.2 analyses threats to the handheld. The paper
book is the recommended first pilot, and it has a different residual risk. It records deductions, debts and
confiscated documents in plain view. A captain who finds it learns that the worker is building a record of
underpayment, and an NGO stamp or logo shows who is helping. The case's own principle is that a device
handed out by labour advocates could mark its holder as a complainant; the same is true of a stamped book.

| Threat | What the employer learns | Residual harm | Mitigation to test in co-design |
|---|---|---|---|
| Discovers or confiscates the book | That the worker is keeping a record; the pattern of hours, cash, deductions and debts | Retaliation, dismissal, violence, or destruction of the only copy | Neutral cover that does not name labour rights or an NGO; a small format that can be kept with other personal papers; a carbon copy left ashore with a person the worker chooses |
| Forces the worker to read or hand over the book | The wording of complaints, the size of debts, and which documents were taken | The employer can prepare a false counter-record or punish named grievances | Icon and number columns rather than narrative words; codes for deduction types instead of phrases such as "document fee" or "debt"; no worker or employer names in the book (the serial number is the identifier) |
| Reads the NGO stamp, signature or logo | Which organisation is helping the worker | The stamp identifies a helper and can be used to intimidate the worker or the NGO | A date-and-initials mark without an organisational name or logo, if co-design and the lawyers agree it still anchors the page; otherwise accept that a visible stamp is a reason not to run the pilot on that site |
| Finds or demands the carbon copy | A second readable record of the same complaints | Loss of the ashore backup | Keep the copy off the workplace, with a trusted person, not in a shared locker or bag |
| NGO photographs of the pages are later seized, leaked or demanded | A durable copy of the same sensitive record, held by the NGO | The photographs can outlive the book and may be requested by an employer, a court, or a hostile official | Written retention rules: store photographs encrypted, limit who can open them, set a deletion date, and obtain case-specific consent before any further use; do not keep them on personal phones |

These mitigations reduce exposure; they cannot remove it. Whether a paper book should be piloted at all
is a **go/no-go question for co-design** with worker groups and legal-aid lawyers, on a site-by-site basis.
If they judge that discovery is too likely or too dangerous in that workplace, Tier A should not proceed
there, even though it is cheaper than the handheld.

### 2.2 Option (b): offline handheld with a signed, encrypted, hash-chained log (Tier B)

- **Block diagram (revision 2).**
  - **Power:** 3×AAA cells into a TPS7A0230 3.0 V LDO (default). A coin cell is kept for a blank-screen variant
    only (section 2.5).
  - **MCU:** STM32L031F6P6, whose RTC runs from a 32.768 kHz crystal.
  - **Secure element:** ATECC608B in the UDFN package, on I²C. It generates and holds per-device P-256 keys and
    does ECDSA, ECDH, SHA-256, AES-128 and random numbers. It has two monotonic counters [S29].
  - **Log storage:** 16 Mbit SPI NOR flash holding the encrypted, append-only log. It is power-gated.
  - **Display:** Sharp 1.28" 128×128 memory-in-pixel LCD from an authorised distributor, or custom segment
    glass.
  - **Input:** 4 tactile buttons behind a silicone keypad.
  - **Export:** an IR LED and a black-lens phototransistor behind a recessed IR-pass window. The receiver is
    powered from a GPIO only during export.
  - **Power gates:** two AO3401A P-MOSFETs, one for the flash and one for the IR receiver.

- **MCU: STM32L031F6P6 (peer-review must-fix 4).**
  - Datasheet: 32 KB flash, 8 KB SRAM, 1 KB EEPROM; TSSOP-20. The LCSC-linked datasheet gives 0.6 µA in
    Stop mode with RTC and full 8 KB RAM retention [S45]. ST's product page gives **0.68 µA** for that
    state [S68]. This budget uses 0.68 µA. The annual total barely changes.
  - LCSC: `normal`, 31 in stock, which is enough for a 20–30-unit pilot [S27].
  - Why not the STM32L011F4P6 (16 KB flash, 2 KB SRAM [S26])? Once an IR bootloader and update path are
    included, the peer review estimates about 11–20 KB of code, leaving no room for a fallback image [S63].
    And 2 KB of RAM equals one full 128×128 1-bit frame, so the screen would have to be drawn line by line.
  - On the L031 the firmware still draws line by line, because the panel accepts per-line writes. That keeps
    RAM free for the encryption buffers.
  - Both parts come in TSSOP-20, but I have not checked them pin for pin (**UNVERIFIED**). Shrinking back to a
    16 KB part is a later decision.
  - The datasheet confirms a dedicated BOOT0 pin and a pre-programmed bootloader on SPI1 (PA4–PA7) and USART2
    (PA2/PA3 or PA9/PA10) [S45]. Section 5.6 explains why that matters.

- **Secure element: ATECC608B-MAHDA-S, UDFN 2×3 mm (peer-review must-fix 5).**
  - LCSC C1518773: `normal`, 187 in stock [S29].
  - Its pads are much harder to clip than SOIC-8 legs. Its I²C traces run on an inner layer or under the chip,
    with no test points.
  - **What it does and does not protect.** It stops the key being *read*, not being *used*. Anyone who drives
    its two I²C wires gets valid signatures on any digest [S63][S64]. That is the "signing oracle" problem;
    sections 3 and 7 explain the protocol fixes.
  - **Lab attacks exist** (section 7.1). Laser fault injection has read secret data slots out of the
    ATECC508A, the ATECC608A and the ATECC608B [S65][S66].
  - The full datasheet is under NDA. Only the summary is public [S29].

- **Display (peer-review must-fix 3).**
  - **No suitable reflective display is in stock on LCSC.** The LS013B7DH03 (C17302968), LS013B7DH05
    (C17500193), LS012B7DD01 (C17272853) and LS027B7DH01 (C17492463) are all at 0. So are both Waveshare 1.54"
    e-paper panels (C5374685, C359939), and the Pervasive E2154CS0C1 (C17236499) is `stop_product` [S31][S47].
  - **Option D1, an authorised distributor.** DigiKey 425-2903-ND for the LS013B7DH03 showed "Part Status
    Active" when I fetched it, with 31,403 in stock, USD 12.39 (1) / 7.97 (100) / 7.74 (500), and a 28-week
    manufacturer lead time [S46]. An aggregator listing showed Mouser at 0 and carried a description reading
    "Obsolete" [S46]. Stock for purchase and the manufacturer lifecycle are therefore **UNVERIFIED**. Buy the
    pilot quantity early.
  - **Option D2, custom segment glass.** Fixed icons and digits suit an icon-first UI and draw microwatts.
    Glass would be driven by an MCU with an LCD controller, such as the STM32L053C8T6 (LCSC C458472,
    `normal`, 96 in stock, USD 6.50 at 1 and 4.56 at 100 [S27]). That part is LQFP-48, so it means a different
    board. Glass tooling cost and minimum order are **UNVERIFIED**. Text labels in four scripts would have to
    be printed beside the icons or dropped.
  - **Rejected again: OLED.** It needs 3.5–4.2 V for its charge pump, draws 23 mA with the full display lit,
    and glows at night [S32].
  - **Drive.** The memory LCD needs an EXTCOMIN signal of 54–65 Hz [S31]. It holds its image at 12 µW typical
    and 50 µW maximum. The MCU's low-power timer can produce 64 Hz from the 32.768 kHz crystal in Stop mode
    (32,768 ÷ 512) without waking the CPU [S63]. Whether that timer's output reaches a free pin on the 20-pin
    part is **UNVERIFIED**.

- **Clock.** The MCU's RTC with the Epson ±20 ppm crystal [S28]. The RTC has digital calibration, so the
  offset measured at each NGO visit can be trimmed in as well as logged [S63]. The 20-pin part has no VBAT pin
  (**UNVERIFIED** against the pin table), so a battery swap loses the clock. Swaps happen only at NGO visits,
  and the clock is anchored immediately after.

- **Flash.** W25Q16JVSSIQ: 2.7–3.6 V, power-down 1 µA typical, page program 20–25 mA [S30]. Maximum page
  program time is 3 ms per the peer review [S63]. It is power-gated in sleep. When the gate is off, its SPI
  pins are set to analogue or low, so the chip is not back-powered through its protection diodes.

- **IR export hardware (peer-review must-fix 6).**
  - The receiver changes from the PT17-21C/L41 (clear lens, sensitive from 400 to 1100 nm, so it sees
    daylight [S35]) to the **PT17-21B/L41/TR8** (black lens, 730–1100 nm) [S48]. LCSC C146237: `normal`, 280
    in stock.
  - A black lens removes visible light, but sunlight is also strong at 940 nm. So the receiver also sits
    behind a recessed IR-pass window that the NGO reader docks against. The firmware discriminates by the
    short IrDA-style pulse shape. Range and daylight rejection are **UNVERIFIED** until bench-tested.
  - The receiver is powered from a GPIO through an AO3401A only in EXPORT, ANCHOR and WITNESS. In every other
    state it is dead.

### 2.3 Single parts table (revision 2)

Prices are unit prices in USD at qty 1 (or the smallest tier, shown in brackets) / 100 / 1000. LCSC rows were
re-read from each product page on 18 Nov 2026 (ICT). "Verified" = Y means the page was fetched and matched the
part, brand, ladder, stock and LCSC lifecycle label. "Buyable today" means in stock at the named source on that
date. **LCSC `normal` is the distributor's label, not a manufacturer lifecycle notice.** Manufacturer or DigiKey
status was read on 18 November 2026 (UTC+7). Lifecycle is stated as confirmed only where that manufacturer or
DigiKey status was read. All other rows are marked **verify lifecycle with manufacturer**.

| # | Part | Function | Source # | Qty/unit | USD @1 / @100 / @1000 | Stock (18 Nov) | LCSC label | Manufacturer / DigiKey status (18 Nov 2026) | Buyable today | Verified |
|---|---|---|---|---|---|---|---|---|---|---|
| 1 | ST STM32L031F6P6 (TSSOP-20) | MCU: RTC, UI, log control, IR | LCSC C115208 | 1 | 2.3407 / 1.5676 / 1.4355 | 31 | normal | **Active** on the ST product page; 10-year longevity commitment from 2026-01-01 [S68] | Y (pilot qty only) | Y |
| 2 | Epson Q13FC13500004 32.768 kHz ±20 ppm | RTC crystal | LCSC C32346 | 1 | 0.1539 (5) / 0.1177 / 0.0843 | 236,000 | normal | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | Y | Y |
| 3 | Microchip ATECC608B-MAHDA-S (UDFN-8 2×3) | Secure element: keys, ECDSA, ECDH, AES, RNG, counters | LCSC C1518773 | 1 | 1.7324 / 1.1242 / 1.0122 | 187 | normal | DigiKey **Part Status Active** [S69] | Y | Y |
| 4 | Winbond W25Q16JVSSIQ 16 Mbit | Encrypted log storage | LCSC C131025 | 1 | 1.2114 (5) / 1.0660 / 0.8280 | 235 | normal | Listed on DigiKey; no status shown. **Verify lifecycle with manufacturer** (**UNVERIFIED**) | Y | Y |
| 5 | XKB TS-1187A-B-A-B | Tactile button | LCSC C318884 | 4 | 0.0216 (20) / 0.0216 / 0.0149 | 471,700 | normal | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | Y | Y |
| 6 | TI TPS7A0230PDBVR 3.0 V | LDO for 3×AAA | LCSC C3747031 | 1 | 0.3219 / 0.2245 / 0.2097 | 27,040 | normal | **ACTIVE** on the TI product page [S70] | Y | Y |
| 7 | Everlight IR17-21C/TR8 940 nm | IR export transmitter | LCSC C131250 | 1 | 0.0591 (10) / 0.0463 / 0.0351 | 26,380 | normal | Listed on DigiKey; no status shown. **Verify lifecycle with manufacturer** (**UNVERIFIED**) | Y | Y |
| 8 | Everlight PT17-21B/L41/TR8 (black lens) | IR export receiver | LCSC C146237 | 1 | 0.0608 (10) / 0.0481 / 0.0370 | 280 | normal | DigiKey **Part Status Active** [S71] | Y | Y |
| 9 | AOS AO3401A | Power gates for flash and IR receiver | LCSC C15127 | 2 | 0.0821 (5) / 0.0647 / 0.0483 | 891,435 | normal | Listed on DigiKey; no status shown. **Verify lifecycle with manufacturer** (**UNVERIFIED**) | Y | Y |
| — | **Verified LCSC core, rows 1–9, without display** | | | | **6.13 / 4.41 / 3.80** | | | | | Y |
| 10a | Sharp LS013B7DH03 1.28" memory LCD | Display, option D1 | DigiKey 425-2903-ND | 1 | 12.39 / 7.97 / 7.74 (500+) | 31,403 at DigiKey (confirmed); LCSC C17302968: 0 | LCSC: normal | DigiKey **Part Status Active**; one aggregator listed the part as "Obsolete" [S72] | Y at DigiKey | Page read (DigiKey); LCSC Y |
| — | **Core with display D1** | | | | **18.52 / 12.38 / 11.54** | | | | | Partly |
| 10b | Custom segment LCD glass with STM32L053C8T6 (LCSC C458472) | Display, option D2 | — | 1 | MCU 6.4980 / 4.5589; glass **UNVERIFIED** | MCU: 96 | normal | MCU and glass: **verify lifecycle with manufacturer** (**UNVERIFIED**) | MCU only | MCU Y; glass N |
| A1 | ST STM32L011F4P6 | Revision 1 MCU, now a later shrink option | LCSC C87903 | (1) | 1.5454 / 0.9112 / 0.8047 | 2,494 | normal | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | Y | Y |
| A2 | Microchip ATECC608B-SSHDA-B (SOIC-8) | Revision 1 secure element, replaced (easy to clip) | LCSC C5444784 | (1) | 1.1150 / 0.7113 / 0.6369 | 109 | normal | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | Y | Y |
| A3 | Everlight PT17-21C/L41/TR8 (clear lens) | Revision 1 IR receiver, replaced (sees daylight) | LCSC C100090 | (1) | 0.0608 (10) / 0.0490 / 0.0388 | 28,320 | normal | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | Y | Y |
| A4 | TE BAT-HLD-001 CR2032 holder | Coin-cell variant only | LCSC C7501066 | (1) | 0.9722 / 0.6134 / 0.5324 | 1,283 | normal | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | Y | Y |
| A5 | STM32L051K8T6 (64 KB, LQFP-32) | Larger MCU if needed | LCSC C915970 | (1) | 3.7578 / 2.5795 (250) / 2.3632 | 348 | normal | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | Y | Y |
| A6 | Macronix MX25R1635FM1IL0 (1.65–3.6 V) | Wide-voltage flash, only if the display changes | LCSC C2802815 | (1) | 1.0687 / 1.0406 / — | 8 | normal | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | Low | Y |
| A7 | HS HS91L02W2C01 0.91" OLED | Rejected display | LCSC C5248081 | (1) | 2.3668 / 1.5790 / 1.4617 | 2,358 | normal | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | Y | Y |
| A8 | RV-3028-C7; BM8563ESA; STM32U031F8P6; GD25Q16CSIG | Alternatives from revision 1, not needed | C3019759; C269877; C22808553; C79074 | — | see revision 1 values [S37][S27][S30] | 0; 51,806; 0; 0 | normal | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | mixed | Y |
| N1 | NXP NTAG 424 DNA (C1136942) and NTAG216 (C1574631) | Option (c), rejected | LCSC | — | 0.3141 / 0.1844 / 0.1592; 0.5248 / 0.5248 / 0.1935 | 0; 0 | normal | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | N | Y |
| X1 | 3×AAA holder contacts or a moulded battery bay | Power | — | 1 | ~0.3–1 | — | — | — | N (**UNVERIFIED**) |
| X2 | Passives, including a reservoir capacitor | — | — | ~15 | ~0.10–0.30 total | — | — | — | N (**UNVERIFIED**) |
| X3 | 2-layer PCB and assembly (4-layer if I²C traces go on an inner layer) | — | — | 1 | ~15–30 / ~3–6 / ~1.5–3 | — | — | — | N (**UNVERIFIED estimate**) |
| X4 | Sealed enclosure with IR-pass window, recessed dock, silicone keypad, O-ring battery door | — | — | 1 | ~5–10 / ~3–5 / ~1.5–2.5 plus tooling | — | — | — | N (**UNVERIFIED estimate**) |
| X5 | IR-pass window material (filter acrylic or polycarbonate) | Daylight rejection | — | 1 | **UNVERIFIED** | — | — | — | N |
| X6 | 3×AAA alkaline cells | Power | — | 3 | ~0.5–1.5 total | — | — | — | N (**UNVERIFIED**) |

Notes:
- **Qty-1 column.** It mixes in minimum-order tiers (5 crystals, 5 flash chips, 20 buttons, 10 of each IR
  part, 5 MOSFETs), so a one-off build pays for those minimums [S63].
- **Manufacturer lifecycle.** Confirmed from the manufacturer or from a DigiKey "Part Status Active" field on
  18 November 2026 (UTC+7): STM32L031F6P6 (ST, Active, 10-year longevity from 2026-01-01), ATECC608B-MAHDA-S
  (DigiKey Active), TPS7A0230PDBVR (TI ACTIVE), PT17-21B/L41/TR8 (DigiKey Active), and LS013B7DH03 (DigiKey
  Active; one aggregator listed it as "Obsolete"). All other named parts, including the STM32L053C8T6 and
  alternates A1–A8, are marked **verify lifecycle with manufacturer**. An LCSC `normal` label is not treated
  as manufacturer confirmation.
- **Discarded C-numbers.** Revision 1 noted two C-numbers that now open unrelated parts (C91756 and C70377).
  They are not used.

### 2.4 Option (c): passive NFC card written by a reader (rejected as specified)

- **The idea.** An NTAG card (NTAG216, or NTAG 424 DNA with AES and "SUN" authenticated messages [S38]) is
  carried by the worker. A reader at the pay point writes each payment event.
- **Why it fails the brief.**
  - The reader is the author. If the employer runs it, the employer decides what is written and when. The
    card then holds the employer's version, which is the problem we started with.
  - The keys that make NTAG 424 DNA records trustworthy are held by whoever provisions the card, not the
    worker.
  - Any phone can read an unprotected NTAG21x. Read-protection options exist, but their exact behaviour was
    not checked (**UNVERIFIED**). So a confiscated card could reveal the log.
  - The worker cannot add their own entries, such as hours, deductions they disputed, or documents taken.
- **Salvage.** If the reader were held only by an NGO, the card would just be a portable anchor token. Tier B
  already provides that.

Verdict: **reject** as an employer-side scheme. Flag it as a risk: an employer or brand may offer such cards
as "worker empowerment" when they are really employer records.


### 2.5 Power (corrected in revision 2; peer-review must-fix 2)

**The revision 1 mistake.** The idle state was described as "a plain clock face", but the budget switched the
LCD off in sleep. A memory LCD that is switched off shows nothing. Keeping the clock face up means the panel
draws its hold power, 12 µW typical and 50 µW maximum, at all times. It also needs the 54–65 Hz EXTCOMIN
signal [S31][S63].

Budget at 3.0 V. Typical figures are from datasheets; the margins are mine.

| State | Contributors | Current |
|---|---|---|
| Sleep, clock face shown (default) | MCU Stop + RTC + 8 KB RAM: 0.68 µA on ST's product page [S68] (0.6 µA in the LCSC-linked datasheet [S45]); LCD hold: 12–50 µW, about 4–17 µA [S31]; secure element sleep: 0.03–0.15 µA [S29]; LDO quiescent current about 25 nA [S36]; low-power timer producing EXTCOMIN, plus one wake a minute to redraw the minutes (**UNVERIFIED**, assumed under 1 µA) | **about 5–18 µA** [S63] |
| Sleep, blank screen (coin-cell variant) | As above, with the LCD power-gated | about 0.7–2 µA |
| Active UI, per entry (~20 s) | MCU plus LCD updates, about 1 mA (**UNVERIFIED**) | ~20 mA·s |
| Encrypt, sign and write one record | AES and ECDSA in the secure element: 3 mA max at clock divider 0xD [S29]; command times are in the NDA datasheet (**UNVERIFIED**, about 0.2–0.5 s assumed); flash program 20–25 mA for ≤3 ms | ~1–2 mA·s |

**Annual consumption at 6 entries a day.**
- Active use: about 15 mAh a year.
- Sleep with the clock face shown: 44–158 mAh a year.
- Total: about **60–175 mAh a year**.
- Padding records (section 5.2) add a few writes a day, which is negligible.

**Default: 3×AAA alkaline plus the TPS7A0230.**
- The LDO holds the rail at 3.0 V, inside the LCD's 2.7–3.3 V window [S31][S36].
- An Energizer E92 AAA is rated about 1,200 mAh at 25 mA and about 1,500 mAh at 10 mA, measured down to
  0.8 V [S49].
- With a 3.0 V LDO the cells are spent at about 1.05 V each, so usable capacity is lower: guess 70–85%
  (**UNVERIFIED**).
- That is still several years in arithmetic. Plan to **swap cells every 12–18 months** at an NGO visit, with
  an anchor straight after, because heat and leakage will dominate.
- Energizer gives an alkaline operating range of −18 to 55 °C [S49]. A sealed case in tropical sun may exceed
  that (**UNVERIFIED**), so the case should be carried, not left on deck.
- The bulk of three cells is the cost.

**Coin cell: kept only for the blank-screen variant.**
- A CR2032 is rated 235 mAh typical to 2.0 V [S39]. Above the 2.7 V floor set by the LCD and flash it holds
  less (**UNVERIFIED** how much).
- At 60–175 mAh a year, the clock-face design would need a new cell every one to three years at typical LCD
  current. At the LCD's maximum in heat, a cell would not last a year.
- **The numbers support a coin cell only if the screen is blank when idle**, using about 20–30 mAh a year, so a
  yearly swap is comfortable.
- That variant gives up the "looks like a plain clock" cover, because a blank screen looks like a dead gadget.
  Whether that matters is a co-design question.
- Late-life pulse sag from the coin cell still needs:
  - the secure element at clock divider 0xD;
  - a brown-out threshold above 2.7 V (which of the selectable levels is **UNVERIFIED**);
  - a rule that no record is committed below a measured threshold [S63].

### 2.6 Waterproofing and field use

- **Target: IP67.** Dust-tight and short immersion. The IEC 60529 text was not read; the definitions are
  **UNVERIFIED** detail.
- **Construction.**
  - No connectors at all.
  - Silicone membrane keypad.
  - One clear window for the LCD and IR pair.
  - O-ring battery door that opens only with a coin, so it is not opened at sea.
  - Desiccant inside; conformal coating on the PCB (**UNVERIFIED** need).
  - Bright lanyard loop so it can be recovered.
- **Sea.** Salt spray, condensation and drops onto deck. The LCD is rated for operation from −20 to +70 °C
  [S31]. Sealed enclosures in tropical sun can exceed 60 °C (**UNVERIFIED**), so it should be carried, not
  left on deck.
- **Field.** Dust, pesticide residue (wipe-clean surfaces) and heat.

### 2.7 Cost per unit (Tier B, revision 2)

| Qty | Verified LCSC core (no display) | Display D1 (distributor) | Unverified (PCB, assembly, enclosure, window, passives, cells, holder) | Rough total |
|---|---|---|---|---|
| 1 | 6.13 | 12.39 | ~22–43 | **~40–62 USD** |
| 100 | 4.41 | 7.97 | ~10–17 | **~22–30 USD** |
| 1000 | 3.80 | 7.74 (500+ tier) | ~6–10, plus tooling | **~17–22 USD** |

- **Increase over revision 1.** The new MCU and secure element add about USD 1.0–1.4 [S63]. Most of the rest
  comes from buying the display at distributor prices.
- **Custom segment glass (D2)** could bring the display cost down at volume, but needs the larger MCU and
  tooling (**UNVERIFIED** cost).
- **Tier A paper book:** under USD 2 at 1000 (**UNVERIFIED**).

---

## 3. Tamper evidence and integrity (revised)

### 3.1 Mechanisms

1. **Append-only log, as a firmware rule.**
   - Records are only ever written into erased flash, and there is no edit or delete function. A mistake is
     fixed with a CORRECTION record that points back to the original, and the original stays.
   - A torn write is detected at boot, and a RECOVERY record is appended.
   - **Limit.** Append-only is a firmware promise, not a physical one. Anyone with a clip can erase the flash
     chip [S64]. Erasure is made *evident* by mechanisms 4 and 5; it cannot be prevented.
2. **Encryption of every record by default (security must-fix 1; peer-review must-fix 1).** The record body
   (type, times, amounts, codes, flags) is encrypted before it reaches the flash. A flash clip shows only
   ciphertext and padding. The keys live in the secure element (section 5.3).
3. **Hash chain and signature over the ciphertext.**
   - Each record's signature covers: a domain tag, the record header, the ciphertext, the hash of the previous
     full record, and the secure element's counter value.
   - Anyone holding an export can check the chain without being able to read it. Only the NGO can decrypt.
4. **Counter bound into every signature, and keys rotated at NGO visits (security must-fix 4).**
   - **The attack.** The secure element signs whatever digest reaches its bus. Between NGO visits, a captain
     with a clip could:
     1. erase the recent tail;
     2. drive the chip directly;
     3. sign a new, back-dated tail of "shift 8 h, paid in full" records that chains perfectly [S64].
   - **Fix A, the counter.** Every record carries the value of the secure element's monotonic counter, which
     can only go up. If the chip binds that value into the signature itself, a rewritten tail must show a jump
     in counter values, because the old values are used up [S64]. A counter value that the MCU merely copies
     into the digest is *not* enough, because the attacker chooses the digest.
     - Whether the ATECC608B can bind its counter into an external-message signature in one step is in the NDA
       datasheet (**UNVERIFIED**).
     - The fallback is a MAC/GenDig-style construction, also **UNVERIFIED**.
   - **Fix B, key rotation at each NGO visit, required either way.**
     1. At each visit the device generates a new signing key inside the chip.
     2. The old key signs the new public key.
     3. The NGO countersigns that hand-over.
     4. The old key is destroyed.

     After that, nobody, even with full control of the chip, can re-sign anything from an earlier epoch
     [S60][S61][S64].
   - **Optional strengthening.** Rotate the key daily, so a rewrite is limited to today. This costs one
     key-slot write a day. Slot write endurance and field-overwrite behaviour are **UNVERIFIED** (NDA). Left to
     the security follow-up.
5. **NGO-held visit receipts (security must-fix 3).** At every visit the NGO keeps its own signed receipt:
   - the device's current public-key fingerprint;
   - the last sequence number and counter value;
   - the head hash;
   - the NGO time;
   - the NGO's signature.

   No names and no record contents. The worker may also keep a short code on a card, but that is no longer the
   protection, because the employer can take the card. The verifier checks every new export against the last
   receipt (section 3.5).
6. **Entry time vs event time.** Each record still carries both, so a batch of late entries shows as late.
7. **Never derive secrets from chip identifiers.** No key, seed, password or pseudonym comes from the STM32
   unique ID or the ATECC608B serial number. All randomness comes from the secure element's RNG, because the
   MCU has no hardware RNG (absence inferred from its feature list [S64], **UNVERIFIED**).
8. **Enrolment at the NGO, in front of the worker.**
   - Keys are generated on the device, then the chip's configuration is locked.
   - The public key goes into an NGO register, so a look-alike device swapped in by an employer is caught at the
     next visit [S64].
   - The exact slot settings are in the NDA datasheet (**UNVERIFIED**).

### 3.2 Countersignatures

| Type | How | Value | Risk |
|---|---|---|---|
| NGO visit (required) | Mutually authenticated reader (section 4.4) reads the head *before* writing anything. The NGO signs and stores its receipt, writes an ANCHOR record, sets the clock, and runs the key hand-over | Bounds back-dating and truncation; corrects the clock; survives loss of the device | NGO key custody; NGO-held receipts are personal data (section 4.6) |
| Co-worker witness | A second device signs a **commitment**: a hash of the other device's head and a random value, not the other device's key or identity | Corroboration that does not expose the crew if one device is captured [S64] | Reveals that both carry devices |
| Employer acknowledgement | Not offered | — | Shows the device to the adversary |
| Public commitment (should-fix) | The NGO publishes a periodic hash over all its visit receipts, with no personal data | Shows a court that the NGO did not invent anchors later [S64] | Usefulness in a Thai forum **UNVERIFIED** |

### 3.3 Trusted time without a network

- **Drift.** About ±52 s per 30 days at 25 °C from the crystal [S28]. The RTC's digital calibration absorbs the
  static offset at each visit [S63]. Temperature and ageing drift are **UNVERIFIED**.
- **Who sets the clock.**
  - Only a verified NGO ANCHOR can set it. The firmware refuses any other IR time-set [S63].
  - The worker cannot set it.
  - Every calibration change is logged.
- **Clock loss.** A pulled battery stops the RTC and logs CLOCK_FAULT. Times until the next visit are marked
  unanchored, and repeated faults lower their value [S64].
- **Case opening (should-fix).** Wire one of the MCU's RTC tamper inputs to a switch inside the case, so that
  "case opened" becomes a signed, timestamped record. A clip-on attack would then leave a mark [S64]. Whether a
  tamper pin is free on this package is **UNVERIFIED**.
- **What times mean.** Between two anchors, times are the device's claim, bracketed by two NGO times. They are
  strong if the case was never opened and there were no clock faults; weak otherwise. The verifier says which.

### 3.4 What tamper evidence can and cannot prove

**A verifier can conclude, if the checks pass** [S64]:
- the records came from a device whose key is in the NGO register;
- no record before the NGO's last *held* receipt was changed, removed or reordered;
- each record existed no later than the next NGO visit;
- whether records after the last receipt were removed, if the counter is chip-bound or keys are rotated;
- how long after each event the entry was made.

**It cannot conclude:**
- that any entry is true;
- that the worker made the entry, or made it freely, rather than someone holding the device or the worker;
- that times between anchors are right, if the device was ever opened;
- anything about a lost or destroyed device beyond what the NGO's receipts and earlier exports hold;
- that a missing record means a missing event.

**Erasure and destruction remain possible.** A captain can erase the flash, smash the device or throw it
overboard. The design makes that *evident*, not impossible:
- the NGO's receipts prove a log existed up to the last visit;
- the counter and the key chain show records were later removed;
- the worker's report that the device was taken goes into the NGO's case notes [S64].

### 3.5 Checks the verifier runs at each visit

1. The last NGO receipt's head hash must appear in the export at its sequence number. If it is missing, the
   verifier reports: "history before the last visit was replaced."
2. The device's current key must chain, through signed hand-overs, back to the key named in the receipt. If it
   does not, the report says: "the device or its key was replaced."
3. The live counter must be at least the receipt's counter plus the records since. If the counter is higher
   than the records explain, the report says: "records made since the last visit were removed."
4. A short or empty log on a device whose counter is high reports: "the log was erased." The counter survives a
   flash wipe, because it lives in the secure element.
5. Corrections are listed beside their originals with entry times, so a cluster of corrections on one day
   stands out [S64].

Each case is reported in plain words, because the removal itself is evidence about who held the device.

---

## 4. Privacy and safety ethics (revised)

### 4.1 Principles

- **The worker holds the only readable copy in the field. In revision 2, even the device cannot show old
  records.** Nothing is uploaded. History is read only at the NGO, with the worker present.
- **Records only what the worker enters.** No location, microphone, camera, accelerometer, radio or beacon. It
  is not a tracking device, and it must never be adapted to track people, covertly or otherwise.
- **Discreet, not deceptive.**
  - When idle it looks like a plain clock. Identical clocks should be handed out widely, many of them never
    enrolled.
  - A locked, enrolled device must look and behave exactly like an unenrolled one. That is the most important
    safety measure: a clock that half the crew carries is not evidence of anything [S64].
  - Revision 2 removes the one deceptive feature, the decoy view (4.3).
- **Minimal content.**
  - No names, employer names or vessel names on the device.
  - Health (SICK_DAY) and any union-related entries are sensitive data under PDPA section 26 [S19]. They stay
    optional and are off by default.

### 4.2 Threats from carrying the device, after revision 2

| What the employer does | What he gets now | Residual risk |
|---|---|---|
| Forces an unlock | Today's entries and a count; nothing older (4.3) | He learns the worker keeps a record |
| Clips onto the flash chip | Ciphertext and padding | Little, unless a lab extracts the key (7.1) |
| Clips onto the secure element and signs | Can't decrypt past days (5.3); can't re-sign earlier epochs; a rewritten tail shows at the next visit (3.5) | Today's epoch can be faked until the next rotation |
| Builds his own IR reader | Nothing: the device exports only to authenticated NGO readers, encrypted to the NGO's key (4.4) | A stolen NGO reader key, until it is revoked |
| Brute-forces the button pattern | Lockout counted in a secure-element monotonic counter, so pulling the battery does not reset it [S64]; and still only today's view | A coercer simply asks the worker |
| Forces false "paid in full" entries | Signed records in his favour | Can't be detected cryptographically; integrity is not truth (3.4) |
| Erases, smashes or discards the device | Evidence since the last visit is lost | NGO receipts show it existed and was removed (3.5) |
| Swaps in a look-alike | Caught at the next visit by the NGO key register | — |
| Forces the worker to the NGO and demands an export | NGO practice: see the worker alone first; refuse exports with the employer present [S64] | People, not technology |

### 4.3 Duress options: decoy removed, honest minimal view instead (security must-fix 2)

**The decoy view is removed from the pilot.** The security review explains why decoys fail here [S64]:
- **The coercer is persistent.** A captain is not a one-off checkpoint. With duress codes, a persistent coercer
  simply demands the second code and compares [S56].
- **The firmware will be public.** So he knows a decoy exists.
- **Decoys leak from the environment.** Entry counts that do not match what he has seen give it away. So do
  flash usage, timing, and word of mouth once one worker on one boat is caught [S57].
- **Being caught is worse than having no decoy.** A discovered decoy tells him the worker lied *and* was
  trained to deceive him. In a setting with documented violence [S10], that is worse than his merely learning
  a record exists.
- **The evidence on decoys is thin.** Decoys helped against one-shot checkpoints [S59]. Research on abusers who
  force unlocks found hidden tools hard for victims to use under extreme pressure [S58].
- **It breaks this study's own principle,** "discreet, not deceptive."

**Replacement: an honest minimal view.**
- The unlocked menu shows only three things: "new entry", today's entries, and a count of records.
- Past days cannot be shown because the device cannot decrypt them (5.3).
- Under a forced unlock the worker can say, truthfully: "it doesn't show old entries; only the office can read
  it."
- **The trade-off.** The worker can no longer check their own history in the field and depends more on the NGO
  [S64]. That trade-off is put to workers in co-design. One option is a printed summary given to the worker at
  each NGO visit.

| Option | Pilot status | Note |
|---|---|---|
| Quick-hide to the clock face (any long press; auto-lock after 20 s) | On | Protects against a glance only |
| Honest minimal view | On (default) | Replaces REVIEW |
| Decoy view | **Removed** | Kept only as a later co-design question, if workers still want it after hearing the analysis |
| Covert duress marker | Not in pilot | A different button confirms an entry and sets a hidden "under pressure" flag inside the encrypted record. Screen, timing and size must be identical [S56][S64]. Co-design first; usability under stress is **UNVERIFIED** |
| Wipe | Off by default | Section 4.3.1 |

#### 4.3.1 Wipe

- **The employer can force the worker to run it** and so destroy their own evidence. That is the strongest
  reason it stays off [S64].
- **"No trace" is not achievable.** An empty device that was used yesterday is noticed [S59][S64].
- **If workers still want a wipe after co-design:**
  - it must return the device to the exact state of a never-enrolled clock;
  - NGO receipts must already hold history up to the last visit;
  - workers must be told plainly that it can be forced.
- **Open technical questions:** whether the secure element's key slots can be destroyed on demand, and how
  long a full 2 MB flash erase takes (**UNVERIFIED**).
- **Open legal question:** whether a self-wipe could be held against the worker as destroying evidence. That
  is for the legal-aid lawyers (**UNVERIFIED**).

### 4.4 Export only to an authenticated NGO reader, with the worker present (security must-fix 5)

A single long press is easy to coerce, and the revision 1 protocol would answer any reader that sent a
challenge. Revision 2 requires the following.

1. **Reader authentication.**
   - At enrolment, the device stores one or more NGO root public keys in the secure element.
   - A reader must present a reader key certified by an NGO root, and sign the device's fresh random challenge.
     The device checks it with the chip's Verify command [S29][S64].
   - Reader keys are kept per field team, revocable at the next visit, and never on a phone that leaves the
     office.
2. **A fresh challenge per export.**
   - The device makes its own nonce with the secure element's RNG, and the reader adds its challenge.
   - Both go into the signed EXPORT record, and the verifier rejects any export without a challenge it issued.
   - This defeats replay [S64].
3. **Encrypted export.**
   - The export is encrypted to the NGO's public key, using ECDH with an ephemeral key and AES-GCM, in the shape
     of HPKE [S62]. The secure element has ECDH and AES/GCM support [S29].
   - A fake or stolen reader gets only ciphertext.
   - Code size and speed are **UNVERIFIED**.
4. **Physical consent remains, but is not relied on.**
   - The long press is read as a GPIO level held for N seconds while the summary is on screen, never as an IR
     message.
   - Without reader authentication it does nothing.
5. **Default scope is only since the last NGO stamp.** At 9,600 baud a full 2 MB log would take about 36
   minutes [S63]. A full export is a rare, separately confirmed option.
6. **What an export reveals, and its protection.** An export reveals every visit date, every witness, and the
   worker's sensitive entries [S64]. It is therefore encrypted, and witnesses are stored as commitments.
7. **NGO practice (should-fix).** See the worker alone before any export, refuse any export with an employer
   present, and record a receipt (3.1 item 5). Authentication and encryption do not stop an employer dragging
   the worker to a real NGO.

### 4.5 Multilingual, icon-first UI for low literacy

- **Icons.** Co-designed with workers: sun or moon for a shift, fish or crop, banknote, hand-out for a
  deduction, document, sick.
- **Numbers.** Western digits, with optional Burmese, Khmer, Lao or Thai digits.
- **Text.**
  - Short labels in Burmese, Khmer, Lao and Thai are pre-rendered as bitmaps at build time and stored in a
    reserved region of external flash, separate from the log.
  - Burmese and Khmer shaping is done on the build machine with HarfBuzz ("Old MIT" licence [S53]) from Noto
    fonts (SIL Open Font License 1.1 [S52]).
  - Under the OFL, converted bitmaps count as modified versions. Ship the OFL text with the firmware source.
  - Glyph coverage and the number of labels are **UNVERIFIED**.
- **No audio by default.** A speaker would be indiscreet.
- **Practice mode.** Entries are marked TRAINING and excluded from reports.

### 4.6 PDPA and governance

- **On the device.** The worker holds their own data. PDPA obligations attach to controllers, such as the NGO
  once it receives an export [S19].
- **For the NGO:**
  - consent and purpose;
  - explicit consent for the sensitive categories in section 26 (including health and trade-union
    information) [S19];
  - retention limits and security;
  - restricting any cross-border transfer, such as a regional NGO's servers. The section number for transfer
    rules is not re-checked (**UNVERIFIED**).
- **Never to employers or brands.** No data goes to an employer or a brand's audit system without the
  worker's specific consent for that case. The logbook must not become employer monitoring or compliance
  theatre.
- **Co-design.** With worker organisations: FRN, MWRN, migrant women's groups, and farm-worker networks in Tak
  and other border provinces. Workers decide the icons, the presentation, whether a wipe or a covert duress
  marker is ever offered, and the trade-off of losing the field history view.
- **NGO-held receipts (revision 2).** Each visit receipt is pseudonymous personal data: a key fingerprint,
  counters, a hash and a time. It needs consent, a stated purpose and retention rules. It is the minimum the
  NGO must keep for erasure to be detectable (3.1 item 5).

---

## 5. Firmware plan (design level, revised)

### 5.1 State machine

```
 wake sources: BUTTON (EXTI) and RTC only. IR is never a wake source.

 [SLEEP / CLOCK FACE] --pattern ok--> [MENU: New entry | Today]
   ^   ^      |                          |         |
   |   |   pattern fail -> SE counter #2 |         +--> [TODAY: today's entries + count, nothing older]
   |   |   (lockout grows)               +--> [ENTRY: type -> value -> event time -> confirm]
   |   |                                              |
   |   +--------- 20 s idle / any long press ---------+--> [COMMIT: encrypt, sign with counter, write, verify]
   |
   |   [RTC midnight] --> [DAY_ROLL: pad block, destroy day key, new ephemeral key, DAY_OPEN record]
   |
   |   [NGO DOCK] (menu item + reader present) --> receiver powered --> [AUTH: reader cert + signed challenge]
   |        --ok--> [summary on screen + physical long press] --> [EXPORT since last stamp]
   |        --> [ANCHOR: NGO receipt, clock set, KEY HAND-OVER] --> receiver off
   +-------------------------------------------------------------------------------------
 No REVIEW and no decoy. WIPE only if armed at enrolment after co-design (off by default).
 LOW_BATT, CLOCK_FAULT, CASE_OPEN (if a tamper pin is wired) and RECOVERY are logged, then CLOCK FACE.
```

- **Boot: find the log end by binary search.** The log fills strictly in order, so a binary search for the
  first erased slot finds the head in about 14 reads, instead of scanning 2 MB [S63]. A coarse checkpoint in
  the MCU's EEPROM is a cross-check, and any disagreement is logged.
- **Commit is atomic.** Write into the next erased slot, read back, then accept. Nothing is committed below a
  measured battery threshold (2.5).

### 5.2 Record format, version 2 (128 bytes)

| Offset | Size | Field | Visible in flash? |
|---|---|---|---|
| 0 | 1 | format version (2) | yes |
| 1 | 1 | key epoch (low byte) | yes |
| 2 | 2 | day index since enrolment | yes |
| 4 | 4 | secure-element counter value | yes |
| 8 | 56 | AES-GCM ciphertext (40 B) + tag (16 B). Plaintext: type, flags (real or padding, training, correction-of), sequence, entry time, event time, value 1, value 2, icon code, days since anchor, battery code, random fill | **no** |
| 64 | 64 | ECDSA P-256 signature (raw r‖s, not DER [S63]) over SHA-256("WLOG2" ‖ bytes 0–63 ‖ SHA-256 of previous record), with the counter bound as in 3.1 item 4 | yes |

- **Types.** As in revision 1: SHIFT_START, SHIFT_END, REST, WORK_DAY, PIECES, PAY_CASH, PAY_BANK_SEEN,
  DEDUCTION, ADVANCE, DEBT_CLAIMED, DOC_TAKEN, DOC_RETURNED, CONTRACT_SEEN, SICK_DAY (optional), CORRECTION,
  TRAINING. New in revision 2:
  - system: DAY_OPEN, PADDING, KEY_HANDOVER, ANCHOR, WITNESS_COMMIT, EXPORT, FW_UPDATE;
  - faults: LOW_BATT, CLOCK_FAULT, CASE_OPEN, RECOVERY.

  The type is inside the ciphertext, so padding and real records look the same.
- **Padding.** Each day uses one 8-slot block (1 KB). At DAY_ROLL, unused slots are filled with encrypted
  PADDING records. So flash use reveals days elapsed, not entries made [S64]. A day with more than 8 entries
  opens a second block, which leaks "busy day" coarsely. Block size is a co-design and testing question.
- **Capacity.** About 1.6 MB of the 2 MB flash is for the log; the rest is fonts and icons plus update staging
  (sizes **UNVERIFIED**). That holds about 1,600 days, roughly 4.4 years.
- **Counter budget.** The ATECC608B has two monotonic counters [S29]. Counter 1 is records, including padding,
  at 8 or more a day. Counter 2 counts failed unlocks.
  - The counters' ceiling (about 2 million) comes from library documentation, per the peer review [S63]
    (**UNVERIFIED** against the NDA datasheet). At about 3,000 a year it is not a limit.
  - **Open question carried from the security review:** if the counter can be read without authorisation, it
    reveals roughly how much the device has been used. Padding limits this to days elapsed plus busy days.
    Random increments would blur it further. Workers must be told that a determined employer can learn the
    device has been used [S64].

### 5.3 Encryption keys (security must-fix 1)

- **Default: daily keys encrypted to the NGO.**
  1. At each DAY_ROLL the secure element makes an ephemeral key pair.
  2. It derives the day's AES key by ECDH with the pinned NGO public key, and writes the ephemeral public key
     into the DAY_OPEN record.
  3. At the next roll, the day key and the ephemeral private key are destroyed.

  So the device can read only today. A clip on the flash yields ciphertext. A clip on the secure element cannot
  decrypt earlier days, because their keys no longer exist. Only the NGO, holding its private key, can
  re-derive past day keys.
- **Feasibility is UNVERIFIED** (NDA datasheet). Open points: whether the ATECC608B can generate an ephemeral
  key into a slot, run ECDH and a key derivation into an AES slot, and overwrite those slots daily; and slot
  write endurance.
- **Fallback.** If that cannot be done, use a long-term AES key in a data slot, with decryption gated by a
  worker-derived authorisation value (5.4). The history view still stays out of the firmware.
- **Plain limit.** A well-funded laboratory could extract the encryption key, and anything derived from a PIN
  or unlock pattern. Laser fault injection has read ATECC608B secret data slots [S65][S66]. Under the default
  scheme only the current day's key is exposed. Under the fallback, the whole log is.

### 5.4 Authorisation, lockout and debug

- **Worker-derived authorisation (should-fix).** Require a value derived from the unlock pattern, never stored,
  for signing (and for decryption in the fallback) [S63][S64].
  - It protects a device taken *without* the worker, not one taken *with* the worker.
  - The pattern's length, and so its entropy, is still unspecified. The security review's estimate assumes 6
    presses on 4 buttons (4,096 patterns) (**UNVERIFIED**).
- **Lockout.** Failed attempts are counted in counter 2, so pulling the battery does not reset them [S64].
  Whether a pulled cell could still reset a firmware lockout timer depends on firmware not yet written
  (**UNVERIFIED**).
- **No hidden paths (adopted).** Production firmware has no factory, test or debug menu, no service button
  combination, and no IR command that skips consent. No secret sits in MCU flash that alone unlocks signing or
  decryption [S64].
- **Readout protection.**
  - Pilot units run RDP Level 1, with SWD pads under the battery, and go only to volunteers who know they are
    test devices. They are never used as evidence in a real case [S63][S64]. RDP Level 1 has been bypassed by
    voltage glitching on the related STM32L051 [S55]. Whether the L031 behaves the same is **UNVERIFIED**.
  - Production units use RDP Level 2.

### 5.5 Export format

- **Contents:**
  - a header (format version, public keys of the current and previous epochs, hand-over chain, firmware hash);
  - all records since the last NGO receipt;
  - a signed EXPORT trailer with the head hash, the counter value, the reader's challenge and the device's
    nonce.

  The whole export is encrypted to the NGO export key (4.4).
- **Verifier.** Open source, running on an offline NGO machine.
  - Uses a permissive crypto library (OpenSSL, Apache-2.0 [S54]), **not** CryptoAuthLib.
  - Accepts raw r‖s signatures.
  - Runs the checks in 3.5.
  - Prints a bilingual report that states, every time, what the log cannot prove (3.4).

### 5.6 IR consent hardware rules (peer-review must-fix 7)

1. **IR never wakes the device.** Only button interrupts and the RTC are Stop-mode wake sources. The L031's
   USART and LPUART can wake it from Stop [S45]; that is left disabled.
2. **The receiver is unpowered except in EXPORT, ANCHOR and WITNESS.** It is fed through an AO3401A from a
   GPIO (2.2).
3. **One code path drives the IR LED.** The LED's FET gate is controlled by a dedicated GPIO, which exactly one
   function enables, inside EXPORT, ANCHOR or WITNESS. Code review checks this.
4. **The consent press is physical.** A GPIO level held for N seconds while the summary is on screen. No IR
   message can stand in for it.
5. **The factory bootloader is closed.**
   - BOOT0 is tied low with no test pad.
   - The IR link avoids the pins the ROM bootloader listens on (USART2 on PA2/PA3 and PA9/PA10, SPI1 on
     PA4–PA7 [S45]) if the pin budget allows. Whether a free LPUART pin mapping exists on the 20-pin part is
     **UNVERIFIED**.
   - Production RDP Level 2 also disables the bootloader and debug.

### 5.7 Update and signing policy

- **Updates go through the same gate as export:** an authenticated NGO reader plus the physical press.
- **Release signing.**
  - Images are signed by a 2-of-3 offline release key held by independent custodians. No employer, brand or
    vendor holds it.
  - Images are verified with the secure element's Verify command, or with micro-ecc (BSD-2-Clause [S41]).
- **Staging.** Images are staged in an external-flash region away from the log, so an update never touches log
  sectors [S63].
- **Anti-rollback.** Both monotonic counters are allocated (5.2). So anti-rollback uses a version floor taken
  from the last signed FW_UPDATE record in the log (design idea, **UNVERIFIED**).
- An update can never read out or rewrite the log, and it appends FW_UPDATE with the image hash.

### 5.8 Licences of candidate stacks (re-checked 18 Nov 2026 ICT)

| Stack | Licence | Status | Use |
|---|---|---|---|
| Microchip CryptoAuthLib | Microchip licence: use "exclusively with Microchip products"; redistribution with the terms. **Not open source** [S43] | Active | Device only: a minimal ATECC command layer. Files carrying Microchip's terms are kept in a separate directory and labelled. **The verifier must not need it.** The full ATECC608B datasheet is under NDA [S29], so slot configuration can't be checked against a public primary spec |
| ST STM32L0 HAL/LL | BSD-3-Clause (`LICENSE.md` read) [S51] | Active | Prefer register-level code with CMSIS device headers. CMSIS licence (Apache-2.0) not re-read (**UNVERIFIED**) |
| micro-ecc | BSD-2-Clause [S41] | Active | Optional bootloader or verifier P-256 check |
| Monocypher | BSD-2-Clause or CC0 [S40] | Release 4.0.3 | No P-256, so not needed in revision 2 |
| TinyCrypt | BSD-style | **Dropped.** Archived since 5 Mar 2024. Zephyr's 4.3 migration guide: "The TinyCrypt library was removed as the upstream version is no longer maintained" [S42][S50] | Not used |
| OpenSSL | Apache-2.0 (`LICENSE.txt` read) [S54] | Active | Verifier: P-256, ECDH, AES-GCM |
| Noto Sans Myanmar, Khmer, Lao, Thai | SIL OFL 1.1 (`OFL.txt` read in each repository) [S52] | Active | Build-time bitmap labels |
| HarfBuzz | "Old MIT" (`COPYING` read) [S53] | Active | Build-time shaping only; nothing ships on the device |

Recommendation: everything except the CryptoAuthLib-derived layer is released under Apache-2.0 or BSD.

---

## 6. Risks, limits, open questions, pilot plan, partners

### 6.1 Risks and limits

1. **Evidential weight is untested** for this kind of device in Thai forums (**UNVERIFIED**). The
   inspectorate route [S2] is likelier to help than court.
2. **Worker safety.** No technical design protects a worker who is under the physical control of someone
   willing to hurt them [S64]. Co-design, a paper tier for those who opt out, wide distribution of identical
   unenrolled clocks, and NGO practice are the real protections.
3. **Coerced entries need no tools.** The device signs whatever is entered. Integrity is not truth (3.4).
4. **Laboratory extraction.** A well-funded lab could extract the encryption key and anything derived from a
   PIN or pattern [S65][S66]. That is beyond almost every employer in this sector, but it is not impossible
   (threat-level judgement, **UNVERIFIED**).
5. **NDA dependence.** Key-slot configuration, counter binding, daily key destruction and command timing all
   depend on an NDA datasheet. An independent expert cannot check them against a public spec.
6. **Supply.** The display is not on LCSC. The MCU (31) and secure element (187) are low on LCSC.
7. **Usability.** An icon UI for complex pay structures may fail. In the FRN survey, share-of-catch was 0.2%
   and per-trip pay 4.4% of payment structures [S1]. Losing the on-device history view makes the worker more
   dependent on the NGO.
8. **Clock and battery problems in heat.** The alkaline operating limit is 55 °C [S49].
9. **Mission creep and false confidence.** As in revision 1 (governance in 4.6; limits in 3.4).

### 6.2 Open questions

- Would DLPW inspectors and Labour Court judges weigh a signed, anchored log above a notebook? Could a
  self-wipe be held against the worker?
- Can the ATECC608B do what the design needs?
  - bind its counter into signatures;
  - run daily ephemeral ECDH keys and destroy them;
  - overwrite a P-256 slot at each visit;
  - require authorisation to read its counters.

  All of this is under NDA. Is the newer ATECC608C, which the lab's slides say had not been studied [S64],
  worth evaluating?
- Will workers accept losing the field history view? Is a printed summary at each visit enough?
- Will NGOs commit to visits often enough that confiscation costs weeks, not months?
- Which pay structures must the record types cover for farm work?

### 6.3 Pilot plan

| Phase | Duration | Content | Exit criterion |
|---|---|---|---|
| 0. Need and safety | 2–3 months | Partner consultations; legal opinion on evidential weight; ethics board approval; safety protocol | Partners and at least one lawyer confirm need and safety; otherwise stop |
| 1. Paper tier | 3 months | 30–50 workers (fishing port and a farm area); NGO anchor visits; mock complaint files reviewed by lawyers | Completion rate, safety incidents (target zero), lawyers' view of usefulness |
| 2. Device prototype | 4–6 months | 20–30 Tier B units with fishers who chose them; waterproofing and battery tests; usability sessions in four languages | No safety incidents; most entries contemporaneous; usable without help after training |
| 3. Evidence test | Parallel | Anonymised or mock cases put to inspectors and legal-aid lawyers | Documented acceptance, or rejection with reasons |
| 4. Decision | — | Scale paper, scale device, or stop | Written go/no-go |

### 6.4 Partners who must confirm the need

These are candidates. None has been contacted, and their interest is **UNVERIFIED**.
- **Fishing:** Fishers' Rights Network (FRN, with ITF) [S1]; Stella Maris seafarers' centres.
- **Legal aid:** Human Rights and Development Foundation (HRDF) [S2].
- **Migrant workers' and women's groups:** Migrant Workers Rights Network (MWRN); MAP Foundation.
- **Worker voice:** Issara Institute (Golden Dreams) [S20].
- **Inspectorate and research:** ILO Ship to Shore Rights; DLPW officials, consulted for evidence practice
  only [S3]; legal-aid lawyers with Labour Court experience.

---

## 7. Reviews

### 7.1 Security review

A separate security review of Tier B covered crypto and coercion [S64]. This revision fills the subsection that
was to have been left pending, and adopts its findings. Its framing: the cryptography is sound as far as it
goes. The danger is that the adversary holds the worker, the device and unlimited time. He is also persistent,
mostly uses the device's own screens after forcing an unlock, and knows how the open-source device works.

**Corrected claim on key extraction.**
- Laboratory work has read **secret data slots** out of each ATECC generation by laser fault injection:
  - the ATECC508A, with a single fault;
  - the ATECC608A, with a double fault [S66];
  - the ATECC608B, with a three-step chain through its EEPROM masking keys and the CheckMac and GenDig
    commands [S65], later reproduced on a low-cost bench [S64].
- So **a well-funded laboratory could extract the encryption key and anything derived from a PIN or unlock
  pattern.** That covers the AES key, any pairing secret and any authorisation value. Encryption at rest
  protects against a clip, not against a laboratory.
- **No extraction of the P-256 signing key has been published.** The lab reported P-256 key storage as not
  vulnerable to its attack, while not excluding later work [S64]. Whether the 608B chain reaches a private-key
  slot is **UNVERIFIED**.
- **Timing.** Timing leaks during ECDSA have extracted keys from certified chips [S67]. That has not been shown
  for this chip, but it is a reason not to let an attacker request unlimited timed signatures.

**Must-fixes adopted.** Lead editor's ruling: the five security must-fixes rank equal with the seven hardware
must-fixes.

| # | Finding | Resolution in this revision |
|---|---|---|
| Sec-1 | A forced unlock or a flash clip shows the whole history | Every record encrypted by default, with keys in the secure element and padding (3.1, 5.2, 5.3). REVIEW is replaced by an honest minimal view: today's entries and a count (4.3) |
| Sec-2 | A decoy fails against an informed, repeat coercer and reads as deliberate deception | Decoy removed from the pilot (4.3) |
| Sec-3 | Confiscation erases the anchors along with the device | NGO keeps its own signed receipt of each visit; every export is checked against it; removal is reported in plain words (3.1, 3.5) |
| Sec-4 | The secure element signs whatever reaches its bus, so a captain can erase recent entries and sign a back-dated "paid in full" tail | Counter bound into every signature (hardware feasibility **UNVERIFIED**), plus key rotation at every NGO visit, with optional daily rotation (3.1 item 4) |
| Sec-5 | One long press is easy to coerce, and any reader is answered | Export only to authenticated NGO readers, with a fresh challenge per export, encrypted to the NGO's key; only records since the last stamp by default (4.4) |

**The security review's other must-fixes, also adopted:**
- **M6, no hidden paths.** No hidden debug or IR paths; RDP Level 2 in production; no standalone secret in MCU
  flash (5.4).
- **M7, enrolment.** Keys generated at the NGO in front of the worker; configuration locked; public key
  registered; no MCU randomness (3.1 item 8).
- **M8, lockout.** The failed-unlock counter lives in the secure element (5.4).

**Should-fixes:**

| # | Recommendation | Status |
|---|---|---|
| S1 | Rotate the signing key at each visit | Adopted, as part of Sec-4 |
| S2 | Publish a hash over all receipts | Adopted, as optional (3.2) |
| S3 | Store witnesses as commitments | Adopted (3.2) |
| S4 | A wipe must leave a never-enrolled state, stay off by default, and workers must be told it can be forced | Adopted (4.3.1) |
| S5 | Log case openings via an RTC tamper pin | Adopted, pending pin check (3.3) |
| S6 | Covert duress marker | Deferred to co-design (4.3) |
| S7 | Pilot units only for volunteers, never as evidence | Adopted (5.4) |
| S8 | Verifier states what it cannot prove; flags clusters | Adopted (3.4, 3.5) |
| S9 | NGO practice: see the worker alone, refuse exports with an employer present | Adopted (4.4) |
| S10 | Worker-derived authorisation value | Adopted as a should-fix, with its limit (5.4) |

**Open questions carried from the security review:**
- **Counter leakage.** If reading the counter needs no authorisation, the counter reveals usage. It is mitigated
  by padding and possibly random increments (5.2).
- **Stronger chip.** Whether to evaluate the ATECC608C.
- **Forced exports.** How NGOs handle an employer bringing the worker in for an export.
- **Duress-feature usability.** Whether workers can use any duress feature under stress.

### 7.2 Peer review (parts, firmware and licences)

The peer review [S63] confirmed revision 1's LCSC data and cost arithmetic and raised seven must-fixes. Section
8 has the one-line resolutions.

---

## 8. Revision history and review response

**Revision 2, 18 November 2026 (ICT).** Responds to the peer review [S63] and the security review [S64]. Five
security must-fixes and seven hardware must-fixes are adopted at equal rank.

**Security review (security review [S64]):**

| # | Item | Resolution |
|---|---|---|
| Sec-1 | Full-record encryption and minimal view | Every record encrypted by default with secure-element keys (daily keys encrypted to the NGO; fallback: an authorised data-slot key) and padded; on-device history replaced by today's entries and a count (3.1, 4.3, 5.2, 5.3) |
| Sec-2 | Decoy | Removed from the pilot. Decoys fail against an informed, repeat coercer and look like deliberate deception (4.3) |
| Sec-3 | Seizure | The NGO keeps its own signed receipt of each visit; every new export is checked against it, so erased or rewritten records show up as removed (3.1 item 5, 3.5) |
| Sec-4 | Signing oracle | Monotonic counter bound into every signature (hardware support **UNVERIFIED**), plus key rotation at each NGO visit, with the old key destroyed (3.1 item 4) |
| Sec-5 | Coercible export | Export only to authenticated NGO readers; fresh challenge per export; export encrypted to the NGO's key; the physical press is kept but not relied on (4.4) |
| Sec-corr | Key-extraction claim | Corrected: a well-funded lab could extract the encryption key and any PIN- or pattern-derived secret. No published extraction of the P-256 signing key (7.1) |

**Peer review (peer review [S63]):**

| # | Item | Resolution |
|---|---|---|
| 1 | Log encryption required | Required, using the secure element's AES. Erasure or destruction is still possible; it is made evident by NGO receipts, the counter, and the key chain (3.1, 3.4, 3.5) |
| 2 | Power budget | Clock-face sleep recomputed at about 5–18 µA (60–175 mAh a year). Default is now 3×AAA plus the TPS7A0230. A coin cell is kept only for a blank-screen variant at about 20–30 mAh a year (2.5) |
| 3 | No display on LCSC | All Sharp memory LCDs and 1.54" e-paper panels on LCSC are at 0. Options: DigiKey 425-2903-ND (Active, 31,403 shown; stock and lifecycle **UNVERIFIED**) or custom segment glass with the STM32L053C8T6 (2.2, 2.3) |
| 4 | Flash and RAM | Prototype on the STM32L031F6P6 (32 KB / 8 KB, TSSOP-20; LCSC C115208, 31 in stock, USD 2.34 / 1.57 / 1.44); line-by-line drawing kept (2.2) |
| 5 | Secure element usable by anyone on its bus | UDFN ATECC608B-MAHDA-S (LCSC C1518773, 187 in stock, USD 1.73 / 1.12 / 1.01); buried traces; oracle limit and lab laser attacks stated (2.2, 7.1) |
| 6 | IR receiver sees sunlight | Black-lens PT17-21B/L41/TR8 (C146237; 730–1100 nm), recessed IR-pass window, and power only during export (2.2) |
| 7 | IR consent hardware | IR is never a wake source; a single code path drives the LED; the long press is read as a physical GPIO level; BOOT0 tied low; bootloader pins avoided; RDP Level 2 in production (5.6) |
| — | Other points | TinyCrypt dropped (Zephyr 4.3 removed it; verified). Export defaults to records since the last stamp. Boot finds the log end by binary search. Licences recorded: CryptoAuthLib is Microchip-only and kept out of the verifier; the full 608 datasheet is under NDA; ST L0 drivers are BSD-3; Noto is OFL 1.1; HarfBuzz is Old MIT; OpenSSL is Apache-2.0. A "buyable today" column was added. Raw r‖s signatures. Flash program time is 3 ms max (5.1, 5.5, 5.8) |

**Revision 1, 18 November 2026.** First issue.

---

## Unverified items (summary, revision 2)

**A. Carried from revision 1 (status updated):**
1. No Thai case law found on signed device logs; evidential weight untested.
2. ALDI/Issara note on low phone ownership among Burmese women (not re-checked).
3. TIP 2025 wording on withheld bank cards and PINs (not re-quoted).
4. Ministry of Labour complaint manual (not read).
5. Paper logbook cost and synthetic-paper supplier.
6. PCB, assembly, enclosure, passives, cells and holder costs (estimates).
7. Manufacturer lifecycle. Partly resolved: per the peer review, ST lists the STM32L011F4P6 as Active.
   Still open:
   - the LS013B7DH03 (DigiKey says Active; one aggregator listing says "Obsolete");
   - all other parts.
8. Code size on the L031. The peer review estimates 11–20 KB; not compiled.
9. Coin-cell capacity above 2.7 V, pulse sag, MOSFET leakage, and secure-element command times (NDA). Flash
   program time is now 3 ms max per the peer review; the typical figure is still open.
10. IP67 definitions (IEC 60529 not read); enclosure temperature in sun.
11. Crystal temperature and ageing drift.
12. NTAG21x read protection. Resolved per the peer review (password and PROT bit); I did not re-read it. The
    option stays rejected.
13. Usefulness of a published receipt hash in Thai forums.
14. Feasibility and cost of encryption on the secure element (NDA). The decoy question is moot now that the
    decoy is removed.
15. Glyph coverage and label counts for Burmese, Khmer and Lao. Licences are resolved.
16. PDPA cross-border transfer section number.
17. Reproducible builds; the CMSIS licence. The HAL licence and TinyCrypt status are resolved.
18. Partner interest (no one contacted).
19. IR range and daylight rejection (bench test needed).

**B. New in revision 2 (hardware and firmware):**
20. Distributor stock for the LS013B7DH03 at purchase time (DigiKey page showed 31,403); Sharp's formal
    lifecycle.
21. Custom segment glass: tooling cost and minimum order.
22. Pin compatibility between the STM32L011F4P6 and STM32L031F6P6 in TSSOP-20.
23. Whether the low-power timer output for EXTCOMIN reaches a free pin on the 20-pin part.
24. Whether the 20-pin part has a VBAT pin (believed not).
25. A free LPUART or USART pin mapping that avoids the ROM bootloader pins.
26. Which brown-out threshold to use.
27. Sleep overhead of EXTCOMIN generation and the once-a-minute redraw (assumed under 1 µA).
28. Usable AAA capacity at a cutoff of about 1.05 V per cell; leakage and heat above 55 °C.
29. Daily ephemeral-ECDH key scheme on the ATECC608B: key generation into a slot, key derivation into an AES
    slot, daily destruction, and slot write endurance.
30. Monotonic counter ceiling (about 2 million, per library documentation).
31. Anti-rollback using a version floor taken from the log.
32. How much the 8-slot padding block leaks about busy days.
33. IR-pass window material and supplier.
34. Sizes of the font and update-staging regions in flash.
35. Code size and speed of HPKE-style export encryption on the MCU and secure element.

**C. Carried in full from the security review's section 11 [S64]:**
36. The ATECC608B full datasheet is under NDA. Exact slot-configuration rules (private, non-readable,
    non-writable, authorised-host-only) were not read.
37. Whether the ATECC608B can bind a monotonic counter value into an ECDSA signature over an external message.
38. Whether an ATECC608B private-key slot can be overwritten in the field, for key epochs or a crypto-wipe;
    how many times; and at what time and energy cost.
39. The exact behaviour of the ATECC608B's I/O protection and "volatile key permitting" against a bus-level
    oracle.
40. No validation certificate for the ATECC608B's RNG was found or checked; only the maker's design claim.
41. Whether the published ATECC608B laser exploit chain, which reads secret data slots, can reach a P-256
    private-key slot.
42. The STM32L0 reference manual (RM0377) could not be downloaded; RDP behaviour comes from the datasheet and
    a third-party advisory.
43. Whether the flash-erase-suppression attack shown on the STM32L051 works on the STM32L011 (and, for this
    revision, the STM32L031).
44. Whether any RDP Level 2 bypass exists for the STM32L0 family.
45. That the STM32L011 has no hardware RNG is inferred from its absence in the datasheet feature list.
46. The pattern length, and so its entropy, is not specified in the design. The brute-force estimate assumes 6
    presses on 4 buttons.
47. Whether pulling the coin cell resets the 5-minute lockout depends on firmware not yet written.
48. Whether 940 nm export light is visible on common phone cameras with these parts, and the real IR range
    including reflections.
49. Code size and speed of encrypted, authenticated export on the 16 KB MCU (for the firmware review). This
    revision moves to a 32 KB MCU; still not measured.
50. Whether a duress marker and an honest minimal view can be made indistinguishable in timing, power and
    display.
51. Whether workers can operate duress features reliably under stress.
52. Whether the RTC tamper pins are free in this package and layout.
53. How long a full erase of the 2 MB flash takes, for the wipe; and whether reading an ATECC608B monotonic
    counter needs authorisation (if not, the counter leaks the record count).
54. Whether Thai labour inspectors or courts give weight to signed device logs, and whether a self-wipe could
    be treated as destroying evidence.
55. The likely hardware-attack capacity of employers in this sector (a threat-level judgement). Also, two
    sources were read only in part: the flash-erase-suppression journal paper (blocked by an access wall; known
    only through the advisory that cites it) and the "Shaping the Glitch" paper (read in a search excerpt of
    its PDF).

## Revisions (18 November 2026, round 7/8 audit)

- Stated part lifecycle as confirmed only where a manufacturer page or a DigiKey "Part Status Active" field was read: STM32L031F6P6, ATECC608B-MAHDA-S, TPS7A0230PDBVR, PT17-21B/L41/TR8 and LS013B7DH03. All other named parts are marked "verify lifecycle with manufacturer". An LCSC `normal` label is no longer treated as manufacturer confirmation.
- Added a threat analysis for the paper logbook (Tier A), covering discovery, forced reading, retaliation, the NGO stamp as an identifier, and photographs held by the NGO, with mitigations and a site-by-site go/no-go for co-design.
- Clarified that ET Act section 11 covers the Tier B electronic record, and that the admissibility of a paper log under Labour Court procedure is unverified.
- Used ST's published 0.68 µA Stop-mode figure in the power budget (the annual total is unchanged at about 5–18 µA).
- Recorded the confirmed DigiKey stock of 31,403 for the display, and replaced two dead NXP datasheet links in the sources list with current product-page URLs.

