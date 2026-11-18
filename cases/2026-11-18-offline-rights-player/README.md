# Offline Rights Player: design study for a cheap audio device carrying workers' rights and hotline information (Thai, Burmese, Khmer, Lao)

Ethical case study (anti-trafficking). Research and design writing only: no firmware, no builds.
Author role: assembly / firmware engineer. All prices, stock figures and lifecycle flags were read from LCSC on 2026-11-18 (UTC+7) and will change.

---

## Summary

- **What it is:** a pocket-sized audio player with no radio of any kind. It plays short recorded messages about workers' rights and verified hotline numbers in Thai, Burmese, Khmer and Lao. It is for people who have no smartphone, or whose phone has been taken from them. It is loosely modelled on the Amplio Talking Book (the former Literacy Bridge device), but with the Talking Book's microphone, feedback recording and usage logging removed on purpose.
- **Recommended build (Variant B):** the cheapest active MCU I found on LCSC (Puya PY32F002AF15P6TU, C5292059, US$0.215 at qty 5) decodes 4-bit IMA ADPCM speech from a 32 Mbit SPI NOR flash (PUYA P25Q32SH, C2925186). It drives a 28 mm speaker through an NS8002 amplifier. The flash holds about 17 minutes of speech at 8 kHz, roughly 4 minutes per language.
- **Electronics cost:** about US$2.30 per unit at prototype quantities (verified LCSC prices plus estimated passives and PCB). With battery holder, cells and a simple case, the total is roughly **US$4.40–6.30 per unit** before assembly labour. The non-electronic lines are estimates, marked unverified.
- **Battery life (estimate):** 3×AA alkaline gives roughly 20–30 hours of playback. With a hard power switch, standby drain is zero. A rechargeable Li-ion option with a small solar panel or hand crank is described for off-grid use.
- **Privacy is the core requirement.** The device has no GPS, cellular, Wi-Fi, Bluetooth, NFC, microphone, clock, logs or play counters, and no per-unit serial number. Every unit is byte-identical. The MCU's factory unique ID is never read by firmware, and readout is locked. Content is changed only physically, by a trusted organisation. **Covert beacons and anything designed to be hidden on a person are ruled out completely.**
- **Hotlines:** only numbers I could read on an official or organisation-owned page are marked verified: 1300 (MSDHS), Issara Institute, and Labour Protection Network. Every other number is marked unverified. I found one discrepancy (LPN's Burmese line differs between LPN's own site and a third-party directory). That shows why numbers must be re-verified by a named organisation before every production batch.
- **An honest alternative check:** a laminated wallet card with the same numbers costs a few cents. The audio device is only justified where low literacy, language mix or visual impairment make a card useless. Field work must confirm this first (section 8).

---

## 1. Purpose and users

**Why audio, why offline, why these four languages**

- Thailand's 2025 Trafficking in Persons report describes forced labour among migrant workers from Burma, Cambodia and Laos. The listed sectors include fishing, seafood processing, agriculture and factories. It describes document confiscation, debt bondage, wage deductions, withheld bank cards and isolation at sea [S1].
- The same report says MSDHS and the Ministry of Labour run hotlines with operators fluent in 19 foreign languages. It also says the government gave rights information to trafficking victims in seven languages, mainly through a mobile app [S1]. App-based information does not reach someone who has no phone, whose phone is held by an employer, or who cannot read.
- Audio needs no literacy. That matters because written Burmese, Khmer and Lao are hard to access for people with little schooling, and many migrants speak a minority language (Shan, Karen, Mon) rather than the national one. MAP Foundation, for example, publishes materials in Burmese, Shan and Karen [S7].
- Offline matters for two reasons. Workers at sea often have no signal, and any networked device can be tracked. This device works with no network at all.

**Who carries it**

- Newly arrived migrant workers: handed out at a pre-departure briefing, a border crossing, a port, a clinic or an NGO drop-in centre.
- Fishers before a trip. The message should then explain what to do when back in port, because calling from sea is usually impossible.
- Domestic workers, who are often isolated in a private house.
- It is given to whole groups, not to selected individuals, so that carrying one marks nobody out (section 6).

**How content gets on and gets updated: physically only**

- The trusted content owner (section 6.4) produces one master audio image per release.
- In Variant B, the image is written to the on-board SPI flash by the content owner's bench programmer through test pads, before the case is closed. There is no user-accessible port.
- To update, units are returned to a distribution point. The trusted organisation re-flashes them, or simply swaps them for new units. Units are cheap enough that swapping is realistic.
- No over-the-air update, no USB mass-storage mode and no smartphone pairing. Each of those would need a radio or a data port, and that creates a tracking and tampering surface.
- Each release starts with a short spoken line in all four languages: "Information checked in [month year]". Listeners can then tell stale content, without any per-unit marking (section 6.3).

**What the content says (outline only; the content owner writes the actual script)**

1. "If you are in danger" plus the national 24-hour number (1300), repeated slowly, digit by digit, three times. The aim is for the listener to memorise it in case the device is taken.
2. Basic rights in plain words: keeping your own passport and documents, wages, days off, changing employer, the fact that recruitment fees and debt cannot be used to hold you, and what forced labour looks like.
3. A language-specific NGO line where one has been verified (section 7).
4. What to do at sea versus on shore.

Optionally, a few minutes of neutral content such as songs or health tips. That gives the device an everyday use and lowers the "troublemaker" signal (section 6).

---

## 2. MCU and audio IC options (lowest-cost active parts)

**How I checked lifecycle.** I read each part from LCSC's own product-detail data on 2026-11-18. That data includes a lifecycle field, `productCycle`, plus live stock and ladder pricing. Every part I recommend shows `productCycle: "normal"` and non-zero stock unless I say otherwise.

As a cross-check, the Waytronic WT588F02B-8S (C2917616), a popular flash voice chip, returns **no product record** from the same LCSC source. The jlc-smt.com mirror of LCSC labels it **停产 (discontinued)** with 0 stock [S10]. I have therefore **rejected** it, even though a third-party distributor still lists it as "Active" [S10]. That disagreement is exactly why I only accept LCSC's own lifecycle flag.

**Caveat:** "normal" is LCSC's lifecycle flag, not a manufacturer PCN or lifecycle statement. Before production, someone should also check each manufacturer for an EOL or NRND notice.

| Option | Part (LCSC code) | LCSC lifecycle / stock | Price (small qty) | What it does | Verdict |
|---|---|---|---|---|---|
| A. Flash voice IC, standalone | Waytronic WTN6040-8S-F-B (C55213606) | normal / **24 in stock** | $0.4364 @1, $0.4132 @100 | Re-flashable voice IC with 100 KB of storage (about 40 s), 224 segments, 12-bit PWM that drives an 8 Ω 0.5 W speaker directly, 1.8–5.5 V, standby under 10 µA, about 1.5 mA running (PWM off) [S11][S12] | Cheapest single-chip design, but **40 seconds total** only covers a "hotline-only" unit (about 10 s per language). Stock is very low. |
| B. Cheapest MCU + SPI flash + amp (**recommended**) | PUYA PY32F002AF15P6TU (C5292059) | normal / 47,640 | $0.2150 @5, $0.1421 @140 | Cortex-M0+, 24 MHz, 20 KB flash, 3 KB SRAM, 1.7–5.5 V [S13]. Fast enough to decode 4-bit IMA ADPCM at 8 kHz in software and output it through a timer PWM. | **Lowest active-part cost** that holds minutes of speech per language. |
| B-alt. MCU alternative | WCH CH32V003F4P6 (C5187096) | normal / 3,215 | $0.3079 @5, $0.1738 @500 | RISC-V2A, 48 MHz, 16 KB flash, 2 KB SRAM [S14][S15] | Good second source if Puya is short. It costs more at small quantities. |
| C. MP3 decoder SoC + microSD | Waytronic WT2003HP8-32N-A21 (C55223352) | normal / 4,971 | $0.9768 @1, $0.9239 @100 | 32-bit decoder with a built-in 0.5 W class-D amplifier. Plays MP3/WAV from 8 to 320 kbps. Reads SPI flash up to 128 Mbit, TF/microSD up to 32 GB, or USB disk. Controlled over UART. 2.2–5.5 V [S16] | Hours of content, but it needs a sequencing MCU (the PY32 above). A removable card brings tampering and unique-ID risks (section 6). |
| Rejected | Waytronic WT588F02B-8S (C2917616) | **no LCSC record; jlc-smt shows 停产 (discontinued), 0 stock** | n/a | 220 KB flash voice IC, about 170 s [S10] | Fails the active-lifecycle-parts rule. |

**Why B is recommended**

- It costs less than A per minute of speech by a wide margin.
- All of its storage is soldered inside the case, so nobody can swap in fake hotline numbers on a card.
- The firmware stays tiny (button scan, ADPCM decode, PWM output), and I can write it by hand in assembly/C. I have deliberately written **no code** here.
- Option C is the upgrade path if field work shows people want long-form content such as legal explainers or radio-style dramas.

---

## 3. Audio storage: how much speech fits where

Bitrates:
- 4-bit IMA ADPCM at 8 kHz is 32 kbit/s, or about 4.0 KB/s (ADPCM reference: [S17]).
- At 6 kHz it is 24 kbit/s (estimate; quality should be listened to).
- MP3 on the WT2003HP8 can go as low as 8 kbit/s [S16].
- Opus can go down to 6 kbit/s [S18], but it is too heavy for a 24 MHz M0+ without an FPU (my engineering judgement, unverified).

| Storage | Part / cost | Format | Minutes per MB (approx.) | Total speech | Per language (÷4) |
|---|---|---|---|---|---|
| On-chip, voice IC | WTN6040 internal 100 KB (in the $0.44 IC) | Vendor format | n/a | **about 40 s** (vendor figure) [S12] | about 10 s |
| On-chip, MCU | PY32F002A 20 KB, mostly used by firmware | ADPCM | about 4.4 | a few seconds | not usable |
| External SPI NOR, 32 Mbit (4 MB) | P25Q32SH-SUH-IT (C2925186), $0.6643 @1, $0.3503 @90, **only 62 in stock**; alt W25Q32JVSSIQ (C179173), $1.5226 @1, 102 in stock | IMA ADPCM 8 kHz (32 kbps) | about 4.4 | **about 17 min** | about 4 min |
| Same 4 MB | same | ADPCM 6 kHz (24 kbps) | about 5.8 | about 23 min | about 5.5 min |
| External SPI NOR, 128 Mbit (16 MB) | W25Q128JVSIQ (C97521), normal but **0 stock**, $2.2812 @1 | ADPCM 8 kHz | about 4.4 | about 70 min | about 17 min |
| microSD (Variant C) | TF-01A socket (C91145), $0.1937 @5; card itself unverified | MP3 16 kbps / 8 kbps | about 8.7 / about 17.5 | 1 GB holds well over 100 hours | effectively unlimited |

Rule of thumb: about 4 minutes of clear speech per language fits in a $0.66 flash chip. That is enough for the outline in section 1.

---

## 4. Power and battery life

**Load estimate (Variant B, while playing)**

- NS8002 amplifier: 6 mA quiescent (LCSC data) [S19], plus speaker power.
- Speech at normal room volume into an 8 Ω, 0.5 W speaker averages perhaps 50–150 mW of audio. A class-AB stage at around 40–50% efficiency then draws roughly **50–100 mA** from about 4 V. This is my estimate and should be measured on a prototype.
- MCU plus flash reads add a few mA (estimate; manufacturer current tables not checked here).
- **Planning figure: about 80 mA while playing, and 0 mA when the hard slide switch is off.**

**Option 1: 3×AA alkaline (default)**

- Energizer E91 is rated 2,850 mAh at a continuous 25 mA drain [S20]. At about 80 mA, expect perhaps 2,000 mAh usable (estimate; capacity falls as drain rises).
- Runtime ≈ 2,000 / 80 ≈ **25 hours of playback**. At 10 minutes a day, that is more than 5 months.
- Why 3 cells and not 2: the NS8002 needs at least 3.0 V [S19], and two AA cells sag below that well before they are empty. The P25Q32SH flash needs 2.3–3.6 V [S21], so an ME6211 3.3 V LDO (C82942; 2–6 V in, 60 µA Iq) [S22] supplies the logic.
- I verified a 2×AA holder on LCSC (MYOUNG BH-AA-B6AA034, C51883822, $1.05, 49 in stock). That fits Variants A and C, which run down to 1.8 V and 2.2 V. **I have not verified a 3×AA holder part.**
- AA cells are sold everywhere in Thailand, Myanmar, Cambodia and Laos, and need no charger. Users can buy their own without contacting anyone. Disposal of alkaline cells is an e-waste concern to raise with the content owner.

**Option 2: Li-ion + small solar panel or hand crank (off-grid)**

- One 18650 or pouch Li-ion cell (cell not sourced; unverified), charged by a TP4056-42-ESOP8 (C16581; $0.1869 @5; 4–8 V in, 4.2 V CC/CV, up to 1 A) [S23]. The input can be a 5–6 V mini solar panel or a hand-crank dynamo (both unverified for part and price).
- The NS8002 runs directly from 3.0–4.2 V. The LDO keeps the flash inside its 3.6 V maximum.
- Rough check: a 2,000 mAh cell at 80 mA gives about 25 h, the same as 3×AA.
- Trade-offs: higher cost, cell shipping restrictions, swelling or heat risk on boats and in hot rooms, and a charge port that must be **power-only** (no data lines connected).

**Off-grid recommendation:** AA as the default, solar/crank as a variant for remote farm or fishing communities after field input. The hard power switch matters for privacy too: when it is off, nothing runs at all.

---

## 5. Enclosure and BOM cost

**Enclosure**

- Off-the-shelf ABS or PC handheld box, or a simple two-part moulding at higher volume. Cost unverified (estimated $1–2 at low volume).
- Use a splash-resistant gasket for boats and wet factories (field question: what IP rating is needed).
- Close the case with screws that need a tool, and add a tamper-evident seal over the programming pads.
- Plain neutral colour. No "anti-trafficking" branding and no serial labels (section 6).
- Buttons: large, embossed icons with a tactile dot for low-vision users, following the Talking Book's practice [S2]:
  - power slide
  - play/pause
  - next message
  - language (cycles Thai → Burmese → Khmer → Lao, with each name spoken)
  - volume
- An optional 3.5 mm earphone jack allows discreet listening. Part not verified.

**BOM, Variant B (recommended), prototype quantities.** LCSC prices are as read on 2026-11-18. Rows marked *est.* are unverified estimates.

| # | Item | Part / LCSC | Qty | Unit price (small qty) | Line | Verified? |
|---|---|---|---|---|---|---|
| 1 | MCU | PY32F002AF15P6TU / C5292059 | 1 | $0.2150 @5 | $0.215 | Yes |
| 2 | 4 MB SPI flash | P25Q32SH-SUH-IT / C2925186 | 1 | $0.6643 @1 | $0.664 | Yes (low stock) |
| 3 | Audio amplifier | NS8002 / C189960 | 1 | $0.0431 @10 | $0.043 | Yes |
| 4 | 3.3 V LDO | ME6211C33M5G-N / C82942 | 1 | $0.0630 @10 | $0.063 | Yes |
| 5 | Speaker 28 mm 8 Ω 0.5 W | GSPK2805TN-8R0.5W / C530550 | 1 | $0.5999 @1 | $0.600 | Yes |
| 6 | Tactile switches | TS-1187A-C-C-B / C318889 | 4 | $0.0414 @20 | $0.166 | Yes |
| 7 | Passives (decoupling, RC filter, resistors) | generic | about 15 | n/a | $0.10 *est.* | **Unverified** |
| 8 | Power slide switch | not sourced | 1 | n/a | $0.05 *est.* | **Unverified** |
| 9 | 2-layer PCB | JLCPCB | 1 | LCSC page banner shows "PCB from $2/5 pcs" (promo) | $0.40 *est.* | **Unverified for production** |
| | **Electronics subtotal** | | | | **≈ $2.30** | |
| 10 | 3×AA holder | not sourced | 1 | n/a | $0.50–1.00 *est.* | **Unverified** |
| 11 | 3×AA alkaline cells | local retail | 3 | n/a | $0.60–1.00 *est.* | **Unverified** |
| 12 | Enclosure, screws, seal, lanyard | generic | 1 | n/a | $1.00–2.00 *est.* | **Unverified** |
| | **Indicative total per unit (before assembly labour)** | | | | **≈ $4.40–6.30** | |

Variant deltas (small qty):
- **A (hotline-only):** WTN6040 $0.4364 drives the speaker directly, with no MCU, flash or amp. Electronics come to about $1.50 *est.*, and it can use the verified 2×AA holder.
- **C (long-form):** add WT2003HP8 $0.9768 and TF-01A socket $0.1937, plus a microSD card (unverified). The NS8002 and flash can be dropped because the WT2003HP8 has a built-in amplifier. Electronics come to about $3.5–4.5 *est.*
- **Solar/rechargeable:** add TP4056 $0.1869, plus a cell and panel (unverified).

For comparison, Amplio does not publish a Talking Book price ("contact supplier for quote") [S2]. Its device also includes recording and usage-data features that this design deliberately leaves out.

---

## 6. Privacy and safety ethics (the critical section)

### 6.1 Absolute exclusions

The device has **none** of the following:
- GPS or GNSS
- cellular, Wi-Fi, Bluetooth, LoRa, NFC/RFID, or any RF transmitter or receiver
- IR transmitter
- microphone or any recording path
- real-time clock
- logging of any kind (play counts, timestamps, button history)
- user data storage
- USB data lines (charging, where present, is power-only)
- camera or sensors

**Covert beacons are ruled out completely.** That includes any "panic button" that transmits, any hidden tracker "for the victim's safety", and any feature that is meant to be hidden on a person's body. This study treats it as a hard rule: if a trafficker finds a hidden transmitter, the person carrying it is in danger. A transmitter can also be turned around and used to track the carrier.

### 6.2 Contrast with the Talking Book

The Amplio Talking Book records user feedback and collects usage data so programmes can be monitored [S2]. That is sensible for agricultural or health outreach. **Here it would be dangerous.** A confiscated device with recordings or a play log could show an employer that someone listened to the "if you are trapped" message, or contain the worker's own voice. This design keeps the Talking Book's good ideas (audio, icon buttons, tactile dots, rugged and offline) and removes all of its data collection.

### 6.3 No unique identity anywhere

- **Identical units.** Every unit gets the same firmware and audio image, byte for byte. There is no serial number on the label, PCB silkscreen or packaging, and no QR codes. Lot codes stay on the shipping carton only, never on the unit.
- **The MCU's factory unique ID is a real risk.** The CH32V003 reference manual says each chip has a 96-bit unique ID burned at the factory, readable over the debug interface or by application code, and intended for "product serial numbers… identity information" [S24][S15]. The Puya PY32 family is assumed to have a similar UID (not checked; unverified). Mitigations:
  - firmware never reads the UID registers
  - debug/SWD is disabled and flash readout protection is enabled after programming
  - no serial or debug header reaches the outside of the case
  - the distributor keeps **no record linking any unit to a recipient**

  Without that record, even a forensic read of the UID cannot identify a person.
- **microSD cards carry a unique 32-bit product serial number (PSN) in their CID register** [S25]. That is one reason Variant C is not the default. If C is used, cards are sealed inside the case, firmware never reads or stores the CID, and distribution keeps no card-to-person records.
- **The voice IC's version readback.** The WTN6040's "F9" command reads back a firmware version and project ID [S12]. That identifies the content release, not the unit, so it is acceptable and even useful for spotting stale content. It must never be personalised per unit.

### 6.4 Who controls content

- One **named content owner** is accountable for scripts, translations and numbers. For example, a coalition led by an established anti-trafficking organisation, with sign-off from the MSDHS 1300 operator for the national number. The study's authors are not the content owner.
- Native-speaker translation and review for each language, including dialect checks, plus legal review of the rights statements.
- A written re-verification of every number before each production run, and a spoken "checked in [month year]" line.
- The master image is held only by the content owner and programmed only on their bench.

### 6.5 How an employer or trafficker could misuse it, and how the design answers

| Misuse | Design response |
|---|---|
| Find the device and punish the holder as a "troublemaker" | Give units to whole groups, not selected people. Neutral look and branding. Optional everyday content (songs, health tips) so the device has an ordinary use. The spoken number is repeated so it can be memorised and the device thrown away. Units are cheap, so loss is acceptable. *Open ethical question: how far to "disguise" it; see section 8.* |
| Read who listened to what, or when | No logs, counters, clock or recording. Nothing exists to read. |
| Track the carrier | No radio of any kind. Nothing to track. |
| Hand out fake units carrying false "hotline" numbers that reach accomplices | Storage is soldered and sealed with no removable card in the default build. Tamper-evident seal. The main message gives the short national number 1300, which is easy to cross-check against posters, embassies and NGOs. The content owner publishes what genuine content says. |
| Re-flash a confiscated unit to spread misinformation | Readout and debug locks. Programming pads sit under a seal. The firmware accepts content only through the bench jig. Field question: is an integrity check of the audio image (hash compare) worth the cost and complexity? |
| Use the device as an "approved" tool to watch workers | Impossible by construction: it has no sensors or transmitters. |
| Someone overhears playback | Volume control. An optional earphone jack (unverified part) for discreet listening. |

### 6.6 Residual risks to state honestly

- Simply possessing a rights device may provoke retaliation in some workplaces, whatever the design. Distribution must be led by organisations that know local conditions.
- Audio cannot replace a person. The device only works if the numbers lead to real, safe help.
- Unofficial "verification" by the device's maker could create false trust. The device must name the organisation behind the content, not the hardware team.

---

## 7. Hotlines: verification status

**These numbers must be re-verified by the named content owner directly with each organisation immediately before any real deployment.** Numbers change, and directories disagree. Status below is as read on 2026-11-18 (UTC+7).

| Organisation / line | Number(s) | Languages (as stated) | Source read | Status |
|---|---|---|---|---|
| MSDHS Social Assistance Centre (ศูนย์ช่วยเหลือสังคม) | **1300** | Thai, with interpreters available. The TIP report says MSDHS and MOL hotlines have operators in 19 foreign languages | Thai government portal page for "แจ้งปัญหาสังคม 1300", owner MSDHS [S3]; US TIP 2025 [S1]; third-party directory [S4] | **Verified** (government page plus TIP report) |
| 1300 from abroad | +66 99 130 1300 (call/SMS) | — | Third-party directory only [S4] | **Unverified** (primary source not read) |
| Royal Thai Police anti-trafficking | 1191 | Thai, ask for an interpreter | Third-party directory citing IOM [S4] | **Unverified** |
| Dept of Employment migrant line | 1694 | English, Burmese, Cambodian | Third-party directory [S4] | **Unverified** |
| Ministry of Labour | 1506 | Thai | Third-party directory [S4] | **Unverified** |
| Issara Institute, 24/7 free worker voice channels | Myanmar **1-800-010-180**; Khmer **1-800-010-181**; Lao & Thai **1-800-010-182** | Myanmar, Khmer, Lao, Thai (also Nepali and Bengali lines) | Issara's own homepage [S5] | **Verified on the organisation's own site.** Ethics note: Issara's model shares "safeguarded" worker feedback with business partners [S5]. The content owner should decide whether and how to explain this to callers. |
| Labour Protection Network (LPN) | Thai **+66 84 121 1609**; Khmer **+66 85 534 1595**; **+66 92 321 1516** (unlabelled on LPN's site; the directory says Lao); Burmese **096 381 2069** on LPN's site | Thai, Khmer, Lao, Burmese | LPN homepage [S6]; directory [S4] | Thai and Khmer **verified**. Lao label **unverified**. **Burmese line DISCREPANCY:** the LPN site shows 096 381 2069 but the directory shows +66 34 434 726. Must be confirmed with LPN. |
| Migrant Workers Rights Network (MWRN), Samut Sakhon | +66 86 755 5337 | — | Only a search-result snippet of a 2015-era ILO project contact PDF. I did not open the PDF, and the MWRN page I fetched was empty. | **Unverified** |
| HomeNet Thailand, MAP Foundation, HRDF | (see directory) | Burmese/Shan/Karen (MAP) | Third-party directory [S4] | **Unverified** (not checked on their own sites) |
| ILO (TRIANGLE in ASEAN) | No worker hotline found; it works through partner NGOs and Migrant Worker Resource Centres | — | Directory [S4] | n/a. Use as a partner for content and distribution, not as a number. |

**Recommendation:** put **1300** in every language as the primary number, plus **one** verified NGO line per language (Issara and/or LPN). Fewer numbers are easier to memorise and easier to keep current.

---

## 8. Open questions needing field confirmation

1. **Is the target group real, and how big is it?** ILO's 2019 endline research on Thai fishers and seafood workers is reported in search results as 66% smartphone ownership, 29% basic phone and 1% none. I saw this only in a search snippet; the PDF was not opened, so it is **unverified**. If that holds, the main users are people whose phones are **confiscated or controlled**, new arrivals, people with low literacy, and people at sea without signal, rather than people who "never had a phone". This must be confirmed with partner NGOs.
2. **Card versus device:** would a laminated multilingual card, a sticker inside a payslip envelope, or posters at ports reach the same people for a fraction of the cost? Only build the device where audio clearly adds value.
3. **Possession risk:** in which workplaces would carrying the device provoke retaliation? Is "neutral everyday content" honest and helpful, or deceptive? The ethics lead and partner organisations should decide.
4. **Languages and dialects:** is standard Burmese enough, or are Shan, Karen and Mon needed? What about regional Khmer and Lao varieties? Each extra language costs storage time (about 4 min each at 4 MB).
5. **Content owner:** which organisation will own, sign off and re-verify content, and run the programming bench? Is MSDHS willing to endorse a 1300 recording?
6. **Distribution channel:** pre-departure training in the origin country, port-in/port-out checkpoints, migrant resource centres, clinics? Each channel has its own risk of officials collecting recipient lists. **No recipient lists should exist.**
7. **Environment:** heat, humidity, salt spray and drops on boats, and the IP rating needed.
8. **Power:** are AA cells affordable and available where users are? Would solar or crank be used, or would it be a cost burden? Plan for disposal and e-waste.
9. **Audio quality:** is 8 kHz ADPCM intelligible enough for tonal languages (Thai, Lao, and partly Burmese)? Test with native listeners. 6 kHz may be too low for tones (unverified concern).
10. **Supply:** the P25Q32SH has only 62 in stock and the WTN6040 only 24. Qualify a second-source 32 Mbit flash (for example W25Q32JVSSIQ C179173, or others LCSC lists as active) before committing.
11. **Lifecycle:** confirm with each manufacturer that no EOL or NRND notice exists. LCSC's "normal" flag is necessary but not sufficient.
12. **Power measurements:** measure real playback current and battery life on a prototype. Section 4 numbers are estimates.

---

## Parts table (verified on LCSC, 2026-11-18 UTC+7)

| Part | LCSC code | LCSC lifecycle flag | Stock | Price (smallest ladder) | ~100-qty price | Role |
|---|---|---|---|---|---|---|
| PUYA PY32F002AF15P6TU | C5292059 | normal | 47,640 | $0.2150 @5 | $0.1421 @140 | Main MCU, Variant B (cheapest) |
| WCH CH32V003F4P6 | C5187096 | normal | 3,215 | $0.3079 @5 | $0.2103 @150 | Alternate MCU |
| Waytronic WTN6040-8S-F-B | C55213606 | normal | **24** | $0.4364 @1 | $0.4132 @100 | Variant A standalone 40 s voice IC |
| Waytronic WT2003HP8-32N-A21 | C55223352 | normal | 4,971 | $0.9768 @1 | $0.9239 @100 | Variant C MP3 decoder + 0.5 W amplifier |
| PUYA P25Q32SH-SUH-IT (32 Mbit) | C2925186 | normal | **62** | $0.6643 @1 | $0.3503 @90 | Audio storage, Variant B |
| Winbond W25Q32JVSSIQ (32 Mbit) | C179173 | normal | 102 | $1.5226 @1 | $1.0415 @100 | Second-source flash |
| Winbond W25Q128JVSIQ (128 Mbit) | C97521 | normal | **0** | $2.2812 @1 | $1.5495 @100 | Bigger storage (out of stock; not used) |
| Nsiway NS8002 | C189960 | normal | 11,290 | $0.0431 @10 | $0.0339 @100 | Class-AB amplifier (3–5.25 V, 6 mA Iq) |
| MICRONE ME6211C33M5G-N | C82942 | normal | 261,690 | $0.0630 @10 | $0.0511 @100 | 3.3 V LDO |
| TOP POWER TP4056-42-ESOP8 | C16581 | normal | 935 | $0.1869 @5 | $0.1290 @150 | Li-ion charger (solar/crank variant) |
| INGHAi GSPK2805TN-8R0.5W | C530550 | normal | 280 | $0.5999 @1 | $0.3671 @100 | 28 mm 8 Ω speaker |
| INGHAi GSPK3605TN-8R0.5W | C530558 | normal | **0** | $0.3917 @1 | $0.3703 @100 | 36 mm speaker (louder; out of stock) |
| XKB TS-1187A-C-C-B | C318889 | normal | 107,180 | $0.0414 @20 | $0.0327 @200 | Buttons |
| Korean Hroparts TF-01A | C91145 | normal | 125,080 | $0.1937 @5 | $0.1349 @150 | microSD socket (Variant C) |
| MYOUNG BH-AA-B6AA034 (2×AA) | C51883822 | normal | 49 | $1.0521 @1 | $0.9959 @90 | Battery holder for Variants A and C |
| ~~Waytronic WT588F02B-8S~~ | C2917616 | **no LCSC record; jlc-smt shows 停产 (discontinued), 0 stock** | 0 | — | — | **Rejected** |

Lifecycle and stock came from LCSC's product-detail data endpoint `https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=<code>`, field `productCycle`. Product pages are at `https://www.lcsc.com/product-detail/<code>.html`.

---

## Sources (pages actually fetched on 2026-11-18 UTC+7 unless marked)

- [S1] US Department of State, *2025 Trafficking in Persons Report: Thailand*. https://www.state.gov/reports/2025-trafficking-in-persons-report/thailand
- [S2] Technology Exchange Lab, *Amplio Talking Book* (features, usage-data collection, dimensions, price "contact supplier"). https://techxlab.org/solutions/amplio-talking-book/ ; Amplio homepage: https://www.amplio.org/
- [S3] Thai government portal ทางรัฐ (DGA), *แจ้งปัญหาสังคม 1300*, service owner MSDHS. https://www.thangrath.go.th/feature/thangrat-hotline-1300/
- [S4] maidthailand.com, *Migrant worker NGO & hotline directory* (third-party, last updated 26 June 2026). https://maidthailand.com/en/resources/ngo-hotline-directory
- [S5] Issara Institute homepage (24/7 worker voice channels). https://www.issarainstitute.org/
- [S6] Labour Protection Network (LPN) Foundation homepage (Get Help numbers). https://www.lpnfoundation.org
- [S7] MAP Foundation languages, as reported in [S4] (MAP's own site not fetched).
- [S8] (not opened) ILO, *Endline research findings on fishers and seafood workers in Thailand*, PDF seen only in search results: https://www.ilo.org/media/401741/download. **Unverified.**
- [S9] (not opened) ILO project contacts PDF listing MWRN, seen only in search results: https://www.ilo.org/media/302706/download. **Unverified.**
- [S10] WT588F02B-8S: jlc-smt.com LCSC mirror showing 停产 (discontinued) and 0 stock (via search result), https://www.jlc-smt.com/lcsc/detail/C2917616.html ; LCSC data endpoint returned a null record: https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=C2917616 ; Ampheo listing "Active" (search result only), https://www.ampheo.com/product/wt588f02b-8s-4157 ; vendor pages (search results), http://www.waytronic.com/ProductsStd_372.html, https://www.wtsoundic.com/product/wt588f02b-8s/
- [S11] LCSC data, WTN6040-8S-F-B. https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=C55213606
- [S12] Waytronic, *WTN6040F-8S Programmable Sound Chip* (40 s, 100 KB, <10 µA standby, about 1.5 mA, F9 readback). https://gzwaytronic.com/products/wtn6040f-8s/
- [S13] LCSC data, PY32F002AF15P6TU. https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=C5292059 ; JLCPCB part page (search result): https://jlcpcb.com/partdetail/PY32F002AF15P6TU/C5292059
- [S14] LCSC data, CH32V003F4P6. https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=C5187096 ; product page https://www.lcsc.com/product-detail/C5187096.html
- [S15] WCH, *CH32V003 product page* (features incl. 96-bit unique ID). https://www.wch-ic.com/products/CH32V003.html
- [S16] Waytronic, *WT2003HP8-32N* product page. https://www.wtsoundic.com/product/wt2003hp8-32n/ ; LCSC data for WT2003HP8-32N-A21: https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=C55223352
- [S17] Wikipedia, *Adaptive differential pulse-code modulation* (4-bit ADPCM, G.726, IMA ADPCM). https://en.wikipedia.org/wiki/Adaptive_differential_pulse-code_modulation
- [S18] Xiph.Org, *Opus codec* (6–510 kbit/s). https://opus-codec.org/
- [S19] LCSC data, NS8002 (3–5.25 V, 6 mA Iq, 1.8 W @4 Ω). https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=C189960
- [S20] Energizer E91 datasheet, 2,850 mAh at 25 mA (search result only; PDF not opened): https://static.datasheets.com/doc/26018948-energizer-e91-ds.pdf. **Treat as unverified.**
- [S21] LCSC data, P25Q32SH-SUH-IT (2.3–3.6 V, 32 Mbit). https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=C2925186 ; W25Q32JVSSIQ: https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=C179173 ; W25Q128JVSIQ: https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=C97521
- [S22] LCSC data, ME6211C33M5G-N. https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=C82942
- [S23] LCSC data, TP4056-42-ESOP8. https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=C16581
- [S24] WCH CH32V003 Reference Manual, ch. 15 Electronic Signature (96-bit UID, readable by SWD or application code), via ManualsLib. https://www.manualslib.com/manual/2933885/Wch-Ch32v003-Series.html?page=165
- [S25] GBATEK, *SD/MMC CID register* (every card has a unique CID; 32-bit PSN). https://www.problemkaputt.de/gbatek-dsi-sd-mmc-protocol-cid-register-128bit-card-identification.htm
- Other LCSC data pages fetched: speakers C530550 and C530558; switch C318889; microSD socket C91145; 2×AA holder C51883822 (all at `https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=<code>`).
