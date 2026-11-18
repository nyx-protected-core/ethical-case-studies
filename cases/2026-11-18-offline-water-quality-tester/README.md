# Village Water Check: design study for a cheap, offline water-quality tester (turbidity + lead/arsenic test-strip reader)

Case study for the ethics track (global problems: unsafe drinking water in villages). Research and design writing only: no firmware, no builds.
Author role: assembly / firmware engineer. All LCSC prices, stock figures and lifecycle labels were read from LCSC's public product-data endpoint on 2026-11-18, about 17:55 (UTC+7). They will change.

---

## Summary

- **What it is:** a hand-held, battery-powered box with no radio of any kind. It does two things. (a) It measures how cloudy a water sample is (turbidity) with an infrared LED and a light sensor in a dark chamber. (b) It reads the colour of a **commercial** lead or arsenic test strip after the user has run that kit's own chemistry. It shows results as coloured lights, picture icons and beep patterns, so you need neither a smartphone nor reading skills.
- **It is a screening aid, not a lab assay.** A clear sample can still carry arsenic, lead or germs. Field arsenic kits have a documented history of wrong answers: up to 68% false negatives in one Bangladesh study [S9][S10]. So the strip channel **never shows green**. Its best possible result is amber ("strip did not detect it; get a lab test before relying on this well").
- **Recommended build:** Puya PY32F002AF15P6TU MCU (C5292059, US$0.215 @5, 12-bit ADC). A 850 nm IR LED (XL-502HIRC-850, C5349968) and three PT204-6C phototransistors (C98114) give a two-detector "ratio" turbidity cell (90° scatter + 180° straight-through) plus one strip-reading detector. A 5050 RGB LED (C51933352) lights the strip. Five 5 mm indicator LEDs, a passive buzzer (MLT-8530, C94599), a 3.3 V LDO (ME6211, C82942) and 3×AA cells complete it.
- **Cost:** electronics about **US$1.60 per unit** at prototype quantities (verified LCSC prices plus estimated passives and PCB). With 3×AA holder and cells, an enclosure with a dark optical chamber, a glass sample vial and a strip tray, the total is roughly **US$4.20–6.70 per unit** before assembly labour. The non-electronic lines are estimates, marked unverified. **Test strips are a recurring cost not included here, and they are what makes the chemistry channel work at all.**
- **Battery:** measure-on-demand with a hard power switch. One test uses roughly 0.04 mAh (estimate), so cell shelf life, not usage, limits the battery (section 5).
- **Ethics core:** false reassurance is the main danger. The design answers it with conservative thresholds, no green for chemistry, and a fixed "get a lab test" message. Calibration is held by a named maintaining organisation, there are no accounts or logs and no data leaves the box, and the device is positioned to support proper water programmes, never to replace them.

---

## 1. Purpose and users

**What decision it informs**

The user wants to know: "Can I drink this as it is, do I need to treat it, or should I stop and get it tested properly?" The device can honestly answer only parts of that:

| Question | Can a cheap device answer it? | What the device says |
|---|---|---|
| Is this water cloudy? | Yes, roughly (section 2). Cloudiness matters because particles shelter germs and use up chlorine. WHO says that above 1 NTU, higher disinfection doses or longer contact times are needed [S2][S3]. | Clarity lights: green / amber / red |
| Is it free of germs? | **No.** Clear water can still carry pathogens. | Never claimed. Icon card says "clear is not the same as safe". |
| Is there arsenic or lead? | **Only as a screen**, through a commercial strip whose chemistry the user runs first (section 3). | Strip lights: amber or red only, never green |

**How a non-literate, no-smartphone user reads it (tied to the electronics)**

- **Two rows of lights on the front panel**, each next to a printed icon. One row is a **drop with waves** for "clarity". The other is a **strip with a test tube** for "chemical strip".
  - Clarity row: green / amber / red 5 mm LEDs.
  - Strip row: amber / red only. There is deliberately **no green LED fitted** on this row, so the hardware cannot show "safe" for chemistry even if the firmware has a bug.
- **Electronics in plain words:** each LED is a diode that lights when the MCU pin pulls current through it via a resistor (roughly 2–5 mA is plenty indoors). The MCU drives one LED at a time, so the power draw stays tiny.
- **Buzzer patterns** for low-vision users and noisy places. The buzzer is a small magnet-and-diaphragm speaker (MLT-8530, 2.7 kHz resonance). The MCU toggles a transistor at that frequency, so it is loudest there:
  - one long tone = OK / clear
  - two tones = caution
  - rapid repeated beeps = stop / get a lab test
  - a rising "chirp" = device fault or calibration check failed
- **A laminated icon card** ships with every unit and is designed with the local programme in its languages. It shows each light combination as a picture with an action: boil/chlorinate, settle and filter, use another source, take a sample to the lab. Every card carries the same message: **"A red strip light or any doubt: use another source and get a lab test."**
- Two large buttons with embossed icons: **TEST CLARITY** (drop) and **READ STRIP** (strip). A slide power switch.

---

## 2. Turbidity sensing

**Method choice: nephelometric (90°) with a straight-through reference (ratio design)**

- Turbidity is measured by shining light through the sample and seeing how much is scattered sideways by suspended particles. Nephelometry measures the light scattered at 90° to the beam [S4][S5].
- Two standard flavours exist:
  - **NTU** (US EPA Method 180.1) uses a white tungsten lamp.
  - **FNU** (ISO 7027) uses near-infrared light, about 860 nm, with a 90° detector [S5][S6].
- The units are not directly interchangeable. USGS warns that turbidity readings from IR and white-light sources are not directly comparable [S5].
- **Why IR at 850 nm:** a near-IR LED is cheap, stable and efficient. Most natural water colour (humic browns) absorbs strongly in the blue but weakly in the near-IR, so sample colour fools an IR reading less. 850 nm sits inside the ISO 7027 near-IR band [S5][S6]. A 940 nm LED (e.g. IR333C, C5200795, active on LCSC) is cheaper per milliwatt, but it lies outside that band, so I rejected it for the turbidity path.
- **Why add a 180° (straight-through) detector:** Kelley et al.'s open-source turbidimeter used a single 90° detector. They found slight non-linearity at high turbidity, which they attributed to the single-beam design, compared with a commercial ratio-design meter [S7].
  - A ratio design divides the 90° scatter signal by the straight-through signal.
  - This cancels most LED brightness drift (temperature, ageing, battery sag) and extends the useful range.
  - Electronics in plain words: an LED's light output falls as it warms and as its current drops, and both detectors see the same drop. Dividing one by the other makes the drop cancel out.
- **Detector:** PT204-6C phototransistors (NPN, water-clear lens, 3 mm, Everlight; listed on LCSC as a 940 nm-peak NPN phototransistor [S20]). A phototransistor is a photodiode with built-in current gain, so a few microwatts of scattered light give a usable collector current. A load resistor turns that current into a voltage the MCU's 12-bit ADC can read.
  - Its sensitivity at 850 nm is lower than at its 940 nm peak. The silicon response is broad, and the sister part PT333-3C lists a 400–1100 nm bandwidth [S21]. That is adequate, but **the exact 850 nm response of PT204-6C is unverified.**
- **Ambient-light rejection:** the firmware takes one reading with the LED off and one with it on, and subtracts. This "synchronous" measurement removes leakage of room light or sunlight through the lid seal.

**Calibration and its limits**

- EPA Method 180.1 calls for calibration against formazin or an approved alternative. Formazin is hazardous, so Kelley et al. made 25 stable secondary standards from diluted hydrophilic cutting oil and checked them against a commercial meter [S7].
- Their open-source device read **0.93 NTU** on a 1 NTU standard and **9.68 NTU** on a 10 NTU standard, versus 0.98 and 8.90 on the commercial meter [S7]. They used a more sensitive light-to-frequency sensor (TSL230R) than my phototransistor + ADC. **Whether my cheaper detector resolves the 1 NTU region is unverified and must be bench-tested.**
- Kelley et al. also reported light leaking directly from LED to sensor, and LED drift with temperature, which they later corrected with a temperature sensor [S7]. Both lessons shape section 6: black baffles, and the ratio design instead of a temperature sensor.
- **Limits stated honestly:**
  - This is a **screening-grade** turbidity reading, reported as three bands, not a number.
  - Accuracy depends on clean, scratch-free sample vials, bubble-free samples, and a calibration that the maintaining organisation must re-check (section 8).
  - It does not claim compliance with ISO 7027 or EPA 180.1.

**How turbidity relates to safety (WHO)**

- WHO's 2017 technical brief *Water quality and health: Review of turbidity* treats turbidity mainly as an **operational indicator** of treatment and disinfection performance, not as a direct health measure [S2].
- A summary of that brief (from search results; I could not open the PDF itself: the WHO server and archive copies blocked or failed) says:
  - water should ideally be below 1 NTU for effective disinfection
  - small or low-resource supplies should keep it below 5 NTU [S2]
  - **These figures are unverified from the primary PDF.**
- YSI's turbidity guide (fetched) cites the same WHO brief: above 1 NTU, higher disinfection doses or longer contact times are needed [S3].
- Kelley et al. explain why: colloidal particles shelter pathogens, consume chlorine, and block UV disinfection [S7].
- **Proposed bands (conservative, to be field-validated):**
  - **Green:** at or below the lowest level the unit can reliably separate from clean water (target about 1 NTU-equivalent). Means "clear enough that boiling/chlorination works as normal". It does **not** mean "safe".
  - **Amber:** about 1–5. "Cloudy: let it settle and filter through cloth, then disinfect with longer contact."
  - **Red:** above about 5. "Very cloudy: settle and filter before any disinfection; don't rely on chlorine alone."
  - **Rule:** if the unit cannot confidently place a reading below the green threshold (noise, bubbles, low battery), it shows amber, not green.

---

## 3. Colorimetric test-strip reader

**What it reads**

- **Arsenic:** most field kits use variants of the century-old Gutzeit method. A reducing agent converts arsenic in the sample to arsine gas, which reacts with a paper strip impregnated with mercuric bromide to give a coloured spot. The user then compares the spot with a colour chart [S11]. Sulfur, selenium and tellurium compounds can interfere [S11].
- **Lead:** at-home lead kits come in several types (binary colour, binary strip, colorimetric vial, colour strip) [S12]. Exact strip chemistries vary by brand and are not assumed here.
- The device **does not run the chemistry**. The user follows the kit's own instructions, then slides the developed strip into a fixed tray in the reader's second dark chamber.

**How the reader works (electronics in plain words)**

- An RGB LED (three separate dies: red, green, blue) lights the strip pad at an angle. A PT204-6C phototransistor looks at the pad and measures **reflected** light.
- The firmware lights each colour in turn and measures, with an LED-off reading subtracted each time.
- A coloured spot absorbs some colours more than others:
  - a yellow-to-brown arsenic spot absorbs blue strongly
  - a pink/red spot absorbs green
- Each strip-pad reading is divided by a reading of a **printed white reference patch** in the same tray. This cancels LED brightness, battery voltage and ageing in the same way as the turbidity ratio.
- This "LED + single detector, time-sequenced colours" approach costs a few cents.
- An alternative is a true colour-sensor IC, Vishay VEML6040 (RGBW, 16-bit, I²C [S22]). It is active on LCSC but costs $1.28 and LCSC had only 21 in stock. The ams TCS34725FN is marked **stop_product** on LCSC [S23], so it fails the active-only rule.
- **Electronic readers are a recognised improvement.** US EPA's 2004 review notes that one group improving the Gutzeit kits after Bangladesh "introduced electronic devices to read the colored test strip, thereby eliminating errors from visual inspection" [S11]. A reader removes one error source (human colour judgement and lighting). It does **not** fix the chemistry's own errors.

**Honest accuracy limits**

- **Field arsenic kits have failed at scale:**
  - Rahman et al. (2002) tested 290 Bangladesh wells with field kits and with a laboratory method. False negatives were as high as 68% and false positives up to 35%. In 2,866 samples from previously labelled wells they found 44.9% mislabelling in the lower range (below 50 µg/L) [S9].
  - Scientific American's report on the study gives the causes: human error, and detection limits too close to the 50 µg/L standard [S10].
  - EPA's review says comparisons of three kits used in Bangladesh showed "up to 50% in some cases" false negatives and false positives [S11].
- **Newer kits are better but still miss the dangerous middle.** George et al. (2012) blind-tested the Arsenic Econo-Quick kit on 123 wells. It classified 89% correctly against the WHO 10 µg/L guideline and 92% against Bangladesh's 50 µg/L standard. Relative to the WHO guideline, **all misclassifications were underestimates for wells containing between >10 and 27 µg/L** [S13]. These are exactly the wells a "safe" light would wrongly clear.
- **Lead kits are worse for drinking water:**
  - In an evaluation of 16 brands, 12 had lead detection limits of 5–20 mg/L. That is hundreds of times the WHO provisional guideline of 10 µg/L [S12][S14], so they are not suitable for drinking water.
  - Binary strips did detect dissolved lead at drinking-water levels, but **failed to detect particulate lead** [S12].
- **Chemical safety of the kits themselves:**
  - Gutzeit tests generate highly toxic arsine gas, measured above the 0.05 ppmv threshold limit value.
  - The strips contain mercuric bromide, which creates mercury waste [S11].
  - The reader adds no chemistry, but its deployment must include kit-safety training (outdoor use, disposal), and choosing safer kits is a programme decision.
- **What a cheap reader adds to these limits:** strip-to-strip variation, tray position, pad wetness, and ageing of LEDs and the white reference. Each needs bench characterisation.
- **Decision rule:**
  - The strip channel reports **red** if the colour change exceeds a conservative threshold set **below** the kit's lowest chart step.
  - It reports **amber** otherwise ("not detected by this strip; get a lab test before relying on this source").
  - It never reports green. If the white reference reads wrong, or the tray is empty or misaligned, it gives the fault chirp and no result.
- **Statement for the icon card and any publication:** *This device is a screening aid. It is not a laboratory test. Only a laboratory test can tell you water is safe from arsenic or lead.*

---

## 4. Parts: cheapest active LCSC parts

**Rule:** only active-lifecycle parts. LCSC's product-data endpoint labels each part with a `productCycle` field. **"normal" is LCSC's distributor label for an active, in-production part. It is not a manufacturer lifecycle notice.** Before a production order, each part needs a manufacturer status check. Parts labelled `stop_product` or `sold_out`, or with zero stock, are rejected below.

**MCU candidates (I would write the firmware in C with small assembly where useful; no code is written here)**

| Candidate | LCSC | Label / stock | Price | Why |
|---|---|---|---|---|
| **PUYA PY32F002AF15P6TU (recommended)** | C5292059 | normal / 47,640 | $0.2150 @5, $0.1421 @140 | Cortex-M0+, 24 MHz, 20 KB flash, **12-bit ADC**, 18 I/O, 1.7–5.5 V [S15]. Enough pins for 1 IR LED, 3 RGB dies, 3 detectors, 5 indicator LEDs, buzzer and 2 buttons without multiplexing. |
| WCH CH32V003F4P6 (second source) | C5187096 | normal / 3,215 | $0.3079 @5, $0.2103 @150 | RISC-V, 48 MHz, 16 KB flash, **10-bit ADC**, 18 I/O [S16]. Coarser ADC (1024 steps vs 4096), which matters at the low-turbidity end. |
| PUYA PY32F002BL15S7TU (cheapest, reduced variant) | C49424671 | normal / 3,135 | $0.1573 @5, $0.1098 @200 | SOP-8, 12-bit ADC, 24 KB flash, **only 6 I/O** [S17]. Fits a turbidity-only unit, or needs charlieplexed LEDs. Datasheet: Stop mode about 1.5 µA, run at 24 MHz about 1.1 mA, ADC about 300 µA at 1 Msps [S18]. |
| Not chosen: CH32V003J4M6 | C5346354 | normal / 29,910 | $0.2702 @5 | SOP-8, 10-bit ADC, 8 I/O [S16]. Costs more than the PY32F002B for the same pin-starved package. |
| Not chosen: PY32F030F18P6TU | C3018715 | normal / **325** | $0.4196 @5 | 48 MHz, 64 KB flash. Overkill and low stock. |

**Privacy note on MCUs:** both Puya and WCH parts carry a factory unique ID (the PY32F002B datasheet lists a 128-byte UID area [S18]). The firmware must never read it or show it. The same no-ID rule as the earlier rights-player study applies.

**Firmware outline (prose only, no code)**

1. **Power-up.** Clocks start on the internal 24 MHz oscillator. The firmware checks battery voltage against the internal reference. If the battery is too low for reliable LED output, it plays the fault chirp, shows no result and sleeps.
2. **Wait.** It sleeps in Stop mode until a button press wakes it. In practice the hard power switch removes power entirely between sessions.
3. **TEST CLARITY.**
   - For each of N cycles (for example 64), it reads both detectors with the IR LED off, switches the LED on, waits a fixed settling time, reads again, and subtracts.
   - It averages the cycles (oversampling also gains effective ADC resolution) and rejects outliers caused by bubbles or movement.
   - It computes the ratio of 90° scatter to 180° transmitted light and compares it with two stored thresholds, written into flash by the maintaining organisation at calibration.
   - If the spread between cycles is too large (an unsteady sample), it reports amber, never green.
   - It drives one clarity LED and plays the matching beep pattern.
4. **READ STRIP.**
   - With the tray in place, it reads the white reference patch and the strip pad under red, green and blue light in turn, each with an off/on subtraction.
   - It forms the colour ratios (pad divided by reference).
   - It checks the reference against the stored window. If it is out of range (dirty, missing, faded), that is a fault.
   - It compares the pad's colour shift with the stored conservative threshold for the selected kit type.
   - Above threshold it shows red and plays rapid beeps. Otherwise it shows amber and plays two tones. There is no green path.
5. **Calibration-check mode.** A held two-button press during power-up runs the measurement against the organisation's sealed reference vial and reference strip card. It reports pass (long tone) or fail (chirp, and the unit then refuses to show green until it is re-calibrated on the bench).
6. **No memory of results.** Results live only in RAM and are gone at power-off. There are no logs, counters, clock or radio. Calibration constants are the only stored data, and they can be written only through the bench programming pads.

**Optical and indicator parts**

| Role | Part | LCSC | Label / stock | Price | Notes |
|---|---|---|---|---|---|
| Turbidity light source, 850 nm (recommended) | XINGLIGHT XL-502HIRC-850 | C5349968 | normal / 1,910 | $0.0683 @10 | 5 mm, 30°, 35 mA max [S19] |
| 850 nm alternative | EVERLIGHT HIR333C/H0 | C2936543 | normal / 215 | $0.1582 @5 | 100 mA, 30° [S19] |
| 850 nm alternative | NEWOPTO XYC-IR5B850AC-A2 | C6081336 | normal / 520 | $0.1041 @10 | 50 mA, 20° [S19] |
| Detectors ×3 (90°, 180°, strip) | EVERLIGHT PT204-6C | C98114 | normal / 2,780 | $0.0679 @10 | NPN phototransistor, 3 mm clear [S20] |
| Detector alternative (photodiode) | EVERLIGHT PD333-3C/H0/L2 | C95402 | normal / 17,745 | $0.1634 @5 | 400–1100 nm. Lower output, needs a transimpedance stage or a large load resistor [S20] |
| Strip illumination | Guiguang GL5050RGB01-B | C51933352 | normal / 9,040 | $0.0292 @20 | 5050 RGB, 6-pin (independent dies) [S24] |
| RGB alternative | XINGLIGHT XL-5050RGBC | C2843868 | normal / 40,150 | $0.0538 @10 | [S24] |
| Colour-sensor IC (optional upgrade) | VISHAY VEML6040A3OG | C78465 | normal / **21** | $1.2802 @1 | RGBW, 16-bit, I²C, 2.5–3.6 V [S22]. Low stock. |
| Indicator red | XINGLIGHT XL-502SURC | C2895484 | normal / 29,880 | $0.0312 @20 | 5 mm, 625 nm [S24] |
| Indicator amber/yellow | XINGLIGHT XL-502UYD | C2895494 | normal / 26,700 | $0.0379 @20 | 5 mm, 588 nm [S24] |
| Indicator green | TUOZHAN TZ-L2-05YYBYG1GDJ15-001 | C779442 | normal / 43,380 | $0.0271 @20 | 5 mm green [S24] |
| Buzzer | Huaneng MLT-8530 | C94599 | normal / 108,145 | $0.1987 @5 | Passive magnetic, 2.7 kHz, 80 dB, 2.5–4.5 V [S25] |
| 3.3 V regulator | MICRONE ME6211C33M5G-N | C82942 | normal / 261,680 | $0.0630 @10 | Keeps the buzzer and optics on a stable rail [S25] |
| Buttons ×2 | XKB TS-1187A-C-C-B | C318889 | normal / 107,180 | $0.0414 @20 | [S25] |

**Rejected for the rule (or other reasons)**

| Part | LCSC | Why rejected |
|---|---|---|
| ams TCS34725FN colour sensor | C2649487 | LCSC label **stop_product**, 45 in stock, $6.15 @1 [S23] |
| EVERLIGHT PT333-3C phototransistor | C264295 | Label normal but **0 stock** [S20] |
| EVERLIGHT PD204-6C photodiode | C264281 | Label normal but **0 stock** [S20] |
| EVERLIGHT PT19-21C/L41/TR8 (C20416370 listing) | C20416370 | Label **sold_out** [S20]. The C131276 listing is normal but has only 120 in stock. |
| EVERLIGHT IR333C 940 nm | C5200795 | Active (19,480 stock, $0.1096 @5), but 940 nm is outside the ISO 7027 near-IR band [S5][S6]. Acceptable for a non-standard design, not chosen. |
| MYOUNG BH-AA-A5BJ034 2×AA holder | C51883821 | Label normal but **0 stock**. No AA holder verified in stock, so holder cost is unverified. |

---

## 5. Power and battery life

**Recommended: 3×AA alkaline + slide switch + ME6211 3.3 V LDO**

- **Why 3×AA, not 2×AA:** blue and green LED dies need about 2.8–3.2 V forward voltage (typical for InGaN; **unverified for the GL5050RGB01-B**). Two AA cells sag to about 2.2 V or lower near end of life and could not light them.
- Three cells (about 4.5 V fresh) feed the LDO, which holds 3.3 V until the cells are well discharged. That keeps the buzzer inside its 2.5–4.5 V rating [S25] and the optics stable.
- Below the LDO's regulation point, the firmware's battery check refuses to give results.
- AA cells are sold in every village shop, need no charger, and are easy for users to replace.

**Measure-on-demand duty cycle (estimate; component currents from datasheets where cited, the rest assumed)**

| Phase | Current | Time | Charge |
|---|---|---|---|
| MCU awake at 24 MHz (about 1.1 mA [S18]) + ADC (about 0.3 mA [S18]) | about 1.4 mA | 10 s | 14 mC |
| IR LED pulsed, 20 mA at 50% duty during turbidity read (assumed) | 10 mA avg | 2 s | 20 mC |
| RGB dies, 10 mA each, sequenced (assumed) | 10 mA | 3 s | 30 mC |
| Result LED, 5 mA (assumed) | 5 mA | 10 s | 50 mC |
| Buzzer, about 30 mA average while sounding (assumed; MLT-8530 rated max 95 mA [S25]) | 30 mA | 0.5 s | 15 mC |
| **Total per test** | | | **≈ 130 mC ≈ 0.036 mAh** |

- With alkaline AA at, say, 1,500–2,000 mAh usable to the cut-off (cell capacity **unverified**), the arithmetic gives tens of thousands of tests.
- **The realistic limits are therefore cell shelf life and leakage** (years), not use. Because the hard switch removes power, standby drain is zero.
- **Advice:** remove the cells if the unit is stored for months, because alkaline leakage destroys boards in hot, humid climates.
- **Coin cell (CR2032):** rejected as the default. Its small capacity is not the problem; it cannot comfortably supply 20–30 mA LED and buzzer pulses, and the LEDs would dim mid-test. That would upset the ratio measurement's settling.
- **Solar:** not justified for a device used a few times a week. A small panel adds cost, a charge circuit and a sealed port. Revisit only if field users report battery cost as a barrier.

---

## 6. Enclosure, optics, waterproofing and BOM

**Optical design (the part that makes or breaks accuracy)**

- **Two separate dark chambers**, moulded or 3D-printed in **matte black** plastic:
  - **Turbidity chamber:** a vertical well for a standard round **glass** sample vial with a black cap. The IR LED is at one side. One detector sits at 90°, behind a narrow slit. The second detector sits directly opposite (180°) behind a neutral-density aperture, so it does not saturate.
    - Baffles block any straight path from LED to the 90° detector. This is the light leak Kelley et al. found in their prototype [S7].
    - The lid closes over the cap with a light-tight lip, so the off/on subtraction only has to remove residual leakage.
  - **Strip chamber:** a slide-in tray with a moulded stop so the strip pad always lands in the same spot. The white reference patch is printed on the tray beside the pad. The RGB LED is at about 45° and the detector looks straight down, so mirror-like glare from a wet pad does not reach it.
- **Vials, not an immersed probe, as the default.** A vial keeps all electronics dry and gives a fixed, known optical path. The user also never dips the device into a well. Vials must be kept clean and unscratched, and a spare vial ships with each unit.
- **Probe variant (if a programme wants in-tank readings):** the LED and detectors are potted behind a clear, flat acrylic or glass window in an IP68-style sealed housing, with the cable gland potted too.
  - Downsides: biofouling and scratches on the window shift readings, and sealing costs more.
  - **Not recommended** for first deployment.
- **Housing:** off-the-shelf ABS hand-held box with a gasketed lid, aiming for splash resistance (IP54-class, **unverified** cost and rating). Tool-needed screws, and a tamper-evident seal over the programming pads, which protects the calibration.
  - Plain neutral colour and no serial-number label.
  - The pictogram card attaches to the lid.

**BOM (per unit, prototype quantities)**

| # | Item | Part / LCSC | Qty | Unit price (small qty) | Line | Verified? |
|---|---|---|---|---|---|---|
| 1 | MCU | PY32F002AF15P6TU / C5292059 | 1 | $0.2150 @5 | $0.215 | Yes |
| 2 | 850 nm IR LED | XL-502HIRC-850 / C5349968 | 1 | $0.0683 @10 | $0.068 | Yes |
| 3 | Phototransistor | PT204-6C / C98114 | 3 | $0.0679 @10 | $0.204 | Yes |
| 4 | RGB LED (strip illumination) | GL5050RGB01-B / C51933352 | 1 | $0.0292 @20 | $0.029 | Yes |
| 5 | Indicator LEDs (clarity G/A/R + strip A/R) | C779442 ×1, C2895494 ×2, C2895484 ×2 | 5 | $0.0271 / $0.0379 / $0.0312 @20 | $0.165 | Yes |
| 6 | Passive buzzer | MLT-8530 / C94599 | 1 | $0.1987 @5 | $0.199 | Yes |
| 7 | 3.3 V LDO | ME6211C33M5G-N / C82942 | 1 | $0.0630 @10 | $0.063 | Yes |
| 8 | Tactile buttons | TS-1187A-C-C-B / C318889 | 2 | $0.0414 @20 | $0.083 | Yes |
| 9 | Passives + 2 small NPN drivers (IR LED, buzzer) | generic, not sourced | about 20 | n/a | $0.10 *est.* | **Unverified** |
| 10 | Slide power switch | not sourced | 1 | n/a | $0.05 *est.* | **Unverified** |
| 11 | 2-layer PCB | JLCPCB | 1 | n/a | $0.40 *est.* | **Unverified** |
| | **Electronics subtotal** | | | | **≈ $1.58** | |
| 12 | 3×AA holder | none verified in stock | 1 | n/a | $0.50–1.00 *est.* | **Unverified** |
| 13 | 3×AA alkaline cells | local retail | 3 | n/a | $0.60–1.00 *est.* | **Unverified** |
| 14 | Enclosure with two black optical chambers, lid gasket, strip tray, printed white reference, screws, seal | generic / 3D-printed or moulded | 1 | n/a | $1.20–2.50 *est.* | **Unverified** |
| 15 | Glass sample vials with black caps | generic | 2 | n/a | $0.20–0.40 *est.* | **Unverified** |
| 16 | Laminated pictogram card | local print | 1 | n/a | $0.10–0.20 *est.* | **Unverified** |
| | **Indicative total per unit (before assembly labour)** | | | | **≈ $4.20–6.70** | |
| — | Consumables: arsenic or lead test kits/strips, per test | commercial | per test | **not priced** | **excluded** | **Unverified** |

- **Optional colour-sensor variant:** add VEML6040A3OG (C78465, $1.28 @1, only 21 in stock) in place of the RGB-sequenced phototransistor for the strip channel. That is about +$1.25. I do not recommend it until stock is reliable and bench tests show a clear accuracy gain.
- **Turbidity-only variant:** use PY32F002BL15S7TU (C49424671, $0.157 @5) and drop the RGB LED, strip chamber, one detector and two indicator LEDs. That saves roughly $0.20 in electronics plus the strip-tray cost, for programmes that only need a clarity check.

---

## 7. Evidence: the health burden

**Arsenic**

- WHO calls inorganic arsenic a confirmed carcinogen and "the most significant chemical contaminant in drinking-water globally" [S1].
  - Long-term exposure causes skin lesions and skin cancer, and cancers of the bladder and lungs.
  - It is associated with cardiovascular disease, diabetes, adverse pregnancy outcomes, and impaired cognitive development [S1].
- **Scale:** an estimated **140 million people in at least 70 countries** have drunk water above the WHO provisional guideline of 10 µg/L. Statistical modelling suggests **94–220 million** are at risk from groundwater [S1].
  - Countries named by WHO include Bangladesh, Cambodia, China, India, Pakistan and Viet Nam, as well as countries in the Americas [S1].
  - **Caveat (WHO's own):** there is no universal case definition and no way to tell arsenic-caused cancers apart, so "there is no reliable estimate of the magnitude of the problem worldwide" [S1].
- **Bangladesh** (Flanagan, Johnston & Zheng, WHO Bulletin 2012) [S8]:
  - Survey data from 2000–2010 put chronic exposure at **35–77 million people**, described as "the largest mass poisoning in history". 97% of the rural population relies on tube wells.
  - The 2009 national survey estimated **22 million** people drinking water above 50 µg/L and **5.6 million** above 200 µg/L.
  - A modelled **nearly 43,000 deaths a year (about 5.6% of all deaths)** were attributable to chronic arsenic exposure at then-current levels.
  - **Caveats:** these are model estimates built on hazard ratios from specific cohorts and on 2009 exposure data. Exposure has changed since then, with new deep wells and new untested shallow wells.
- **Why testing matters, and why bad testing hurts:** Rahman et al. note that more than 1.3 million tube wells in Bangladesh had been field-kit tested [S9]. Scientific American reports the authors' warning that mislabelling half of the truly safe wells "has a major socioeconomic impact", because villagers abandon wells they could use [S10]. False positives cost too: they push people towards surface water carrying germs.

**Lead**

- WHO and IHME:
  - **more than 3.5 million deaths** were attributed to lead exposure globally in 2023, mainly through cardiovascular effects
  - **more than 71.5 million DALYs** were lost
  - there is **no known safe blood lead level**; even 3.5 µg/dL may be associated with decreased intelligence in children
  - children can absorb 4–5 times as much lead as adults from the same dose [S14a]
- WHO's drinking-water guideline for lead is **10 µg/L, and it is provisional.** It is set by treatment performance and analytical achievability, "no longer a health-based guideline value", so concentrations should be kept as low as reasonably practical [S14].
- **Caveat that shapes the design:** WHO lists drinking water (via lead pipes, solder and fittings) as one route among many. Others are lead-acid battery recycling, mining, paint, cosmetics, spices and ceramics [S14a]. The global death figure is **not** a drinking-water figure.
  - In villages on groundwater with no lead plumbing, lead in water may be a smaller risk than arsenic. It matters near battery recycling, mining, or old piped systems.
  - Combined with the poor drinking-water performance of most consumer lead kits [S12], this argues for **arsenic-first** strip support, with lead added only where a programme has a validated strip and a known lead source.

---

## 8. Ethics

**False reassurance is the central danger**

A cheap tester that lights green on contaminated water is worse than no tester, because it turns doubt into confidence. Design responses:

| Risk | Design response |
|---|---|
| Strip channel says "safe" for arsenic or lead | **No green LED is fitted on the strip row.** The best result is amber: "not detected by strip — get a lab test". The threshold is set below the kit's lowest chart step, because known kit errors are underestimates in the 10–27 µg/L range [S13]. |
| Clarity green is read as "safe to drink" | The icon card shows clear water with a germ icon and a question mark: "clear is not safe". Green means only "clear enough to boil/chlorinate normally". |
| Unit drifts out of calibration and starts reading low | Ratio optics and a white reference cancel much of the drift. A calibration-check mode uses a sealed reference vial and card. On failure, green is locked out until bench re-calibration. A low-battery lockout prevents dim-LED errors. |
| User misreads or skips the kit chemistry | Training by the programme, with the kit's own instructions. The reader faults if the tray is empty or the reference is wrong. |
| Kit chemistry harms the user (arsine, mercury waste) [S11] | Deployment only alongside kit-safety training and a waste-return route. Prefer safer kit designs where the programme can source them. |
| Device displaces proper testing | Positioned as a **triage** tool that tells people when to seek a lab test. Every red or uncertain result points to the lab. Distribution only through programmes that offer a lab route. |
| Mislabelling safe wells causes needless abandonment [S10] | Red is a prompt for a lab test, not an instruction to abandon a well. The icon card says "use another source **until tested**". |

**Who maintains calibration**

- A **named maintaining organisation** (for example a district public-health laboratory, a university water lab, or an established WASH NGO) owns:
  - the bench calibration of thresholds against proper standards (formazin or an approved alternative for turbidity [S7]; laboratory-measured water samples for strip thresholds)
  - the sealed reference vials and cards, and their replacement schedule
  - re-calibration when check mode fails, and withdrawal of failing units
- Each strip threshold is set **per kit brand and lot**. A change of kit supplier requires re-validation before units are re-programmed.
- **Field question:** what calibration interval the maintaining organisation can actually sustain. If it cannot, the programme should not deploy the strip channel at all.

**Data ownership and privacy**

- **Offline by construction:** no radio, no app, no account, no cloud, no logs, no stored results, no clock. Nothing is collected, so there is nothing to sell, leak or subpoena.
- The firmware never reads the MCU's factory unique ID, and units carry no serial label.
- If a programme wants well-level records (which can be valuable for public health), it keeps them **on paper or in its own system, with community consent**, entered by people, not harvested by the device. That keeps the community in control of what is known about their water, and who knows it.
- **Lock-in risk:** later "connected" versions could turn villagers into data sources for a vendor. Any such change should be a separate, consented design, never a firmware update to this device.

**Not displacing proper water programmes**

- WHO is explicit: "the most important action in affected communities is the prevention of further exposure to arsenic by provision of a safe water supply" [S1]. A tester does not provide safe water.
- The device should be distributed only as part of programmes that also offer lab testing and safe-water options (deep wells, treatment, piped supply). It is not a stand-alone product sold as a solution.
- **Honest alternative check:** where a programme already has periodic lab testing of every well, this device adds little. Its value is in the gap between lab visits, and for new, untested wells: George et al. note that since the blanket kit-testing campaign "millions of new wells have been installed but not tested" in Bangladesh [S13].

---

## 9. Licence and citation check

| Item | Built on? | Licence | Notes |
|---|---|---|---|
| Kelley et al., *An Affordable Open-Source Turbidimeter*, Sensors 2014 [S7] | **Concepts only:** 90° IR geometry, cutting-oil secondary standards, the light-leak and thermal-drift lessons. No schematics, code or CAD copied. | Article: **CC BY** (Europe PMC licence field, fetched) [S7b] | Their online build files and Arduino firmware (hosted separately, "[32]" in the paper) have their **own licence, which I have not checked (unverified)**. If a future version reuses those files, their licence must be read first. |
| Vishay *Designing the VEML6040 into an Application* note (sample C code) | Not used | Vishay document terms (not checked) | Only relevant if the VEML6040 variant is built. Cite, don't copy, until terms are checked. |
| Puya / WCH vendor SDKs and HAL libraries | Not used yet. No firmware written. | **Unverified** (SDK licence files not read) | Must be checked before firmware work begins. The plan is to write register-level C/assembly from the reference manual, so no SDK code is needed. |
| Arsenic/lead kit colour charts | Thresholds will be derived per kit by lab comparison, not copied from charts | Kit manufacturers' documentation | Kit brand names in any publication need fair-use care. |
| Reference designs from component vendors | None used | n/a | The circuit is a textbook LED/phototransistor/MCU arrangement. |

**No open-source firmware or hardware design files are incorporated in this study.**

---

## 10. Open questions and field confirmation needs

1. **Low-end turbidity resolution:** can PT204-6C + 12-bit ADC + oversampling reliably separate 1 NTU from clean water in this geometry? Bench test against standards. This is unverified, and it sets whether green can exist at all.
2. **PT204-6C response at 850 nm**, and whether a phototransistor or a photodiode with a transimpedance amplifier gives better low-end noise. Unverified.
3. **WHO turbidity figures** (ideally <1 NTU for disinfection, <5 NTU for small supplies) need confirming from the primary WHO PDF, which I could not open. The 1 NTU disinfection statement is confirmed via YSI's citation [S3].
4. **Which arsenic kit(s)** the programme can source reliably and safely. The reader must be validated per kit against laboratory results (e.g. AAS/ICP-MS) on local well samples, including the 10–50 µg/L range, before any deployment.
5. **Whether lead testing belongs at all** in a given area: is there a known lead source (battery recycling, mining, lead plumbing)? Is there a strip validated at drinking-water levels that also catches particulate lead [S12]?
6. **Who the maintaining organisation is**, what calibration interval it can sustain, and who pays for strips and reference vials. Unverified.
7. **Pictogram and beep comprehension** testing with non-literate users in the target communities.
8. **Enclosure cost and splash rating** at real volumes; AA holder sourcing (none verified in stock on LCSC). Unverified.
9. **Blue/green LED forward voltage** of GL5050RGB01-B at 3.3 V. Unverified.
10. **Manufacturer lifecycle confirmation** for each part. LCSC's "normal" is a distributor label.
11. **Does the community want it?** Field work should confirm that a screening tool improves decisions and does not cause needless abandonment of good wells [S10].

---

## Parts table (verified on LCSC, 2026-11-18 about 17:55 UTC+7)

"Active?" means LCSC's `productCycle` label read "normal" (LCSC's active/in-production label; distributor data, not a manufacturer notice).

| Part | LCSC code | Active? (LCSC label) | Price (small qty) | Stock | Role |
|---|---|---|---|---|---|
| PUYA PY32F002AF15P6TU | C5292059 | Yes (normal) | $0.2150 @5 | 47,640 | MCU (recommended) |
| WCH CH32V003F4P6 | C5187096 | Yes (normal) | $0.3079 @5 | 3,215 | MCU second source |
| PUYA PY32F002BL15S7TU | C49424671 | Yes (normal) | $0.1573 @5 | 3,135 | MCU, turbidity-only variant |
| WCH CH32V003J4M6 | C5346354 | Yes (normal) | $0.2702 @5 | 29,910 | MCU, not chosen |
| PUYA PY32F030F18P6TU | C3018715 | Yes (normal) | $0.4196 @5 | 325 | MCU, not chosen |
| XINGLIGHT XL-502HIRC-850 | C5349968 | Yes (normal) | $0.0683 @10 | 1,910 | 850 nm IR LED (recommended) |
| EVERLIGHT HIR333C/H0 | C2936543 | Yes (normal) | $0.1582 @5 | 215 | 850 nm IR LED alt |
| NEWOPTO XYC-IR5B850AC-A2 | C6081336 | Yes (normal) | $0.1041 @10 | 520 | 850 nm IR LED alt |
| TONYU DY-HIR333C-H34-SN | C7470713 | Yes (normal) | $0.1452 @5 | 100 | 850 nm IR LED alt |
| EVERLIGHT IR333C | C5200795 | Yes (normal) | $0.1096 @5 | 19,480 | 940 nm, rejected for band |
| EVERLIGHT IR333C/H0/L10 | C72053 | Yes (normal) | $0.0770 @5 | 85 | 940 nm, rejected for band |
| EVERLIGHT PT204-6C | C98114 | Yes (normal) | $0.0679 @10 | 2,780 | Phototransistor ×3 |
| EVERLIGHT PD333-3C/H0/L2 | C95402 | Yes (normal) | $0.1634 @5 | 17,745 | Photodiode alt |
| EVERLIGHT PT19-21C/L41/TR8 | C131276 | Yes (normal) | $0.0367 @20 | 120 | SMD phototransistor, low stock |
| EVERLIGHT PT333-3C | C264295 | normal, **0 stock** | $0.0613 @10 | 0 | Rejected |
| EVERLIGHT PD204-6C | C264281 | normal, **0 stock** | $0.0591 @10 | 0 | Rejected |
| EVERLIGHT PT19-21C/L41/TR8 | C20416370 | **No (sold_out)** | $0.0363 @20 | 80 | Rejected |
| EVERLIGHT PT26-21C/TR8 | C73930 | **No (sold_out)** | $0.0937 @5 | 10,765 | Rejected |
| Guiguang GL5050RGB01-B | C51933352 | Yes (normal) | $0.0292 @20 | 9,040 | Strip illumination |
| XINGLIGHT XL-5050RGBC | C2843868 | Yes (normal) | $0.0538 @10 | 40,150 | RGB alt |
| VISHAY VEML6040A3OG | C78465 | Yes (normal) | $1.2802 @1 | 21 | Colour-sensor option |
| ams TCS34725FN | C2649487 | **No (stop_product)** | $6.1476 @1 | 45 | Rejected |
| XINGLIGHT XL-502SURC | C2895484 | Yes (normal) | $0.0312 @20 | 29,880 | Red indicator |
| XINGLIGHT XL-502UYD | C2895494 | Yes (normal) | $0.0379 @20 | 26,700 | Amber indicator |
| TUOZHAN TZ-L2-05YYBYG1GDJ15-001 | C779442 | Yes (normal) | $0.0271 @20 | 43,380 | Green indicator |
| XINGLIGHT XL-502SYGD | C2895550 | Yes (normal) | $0.0320 @20 | 8,920 | Yellow-green alt |
| Huaneng MLT-8530 | C94599 | Yes (normal) | $0.1987 @5 | 108,145 | Buzzer |
| MICRONE ME6211C33M5G-N | C82942 | Yes (normal) | $0.0630 @10 | 261,680 | 3.3 V LDO |
| XKB TS-1187A-C-C-B | C318889 | Yes (normal) | $0.0414 @20 | 107,180 | Buttons |
| MYOUNG BH-AA-A5BJ034 | C51883821 | normal, **0 stock** | $0.9168 @1 | 0 | 2×AA holder, rejected |
| MYOUNG MY-AA-5355-J | C5371785 | Yes (normal) | $0.0873 @5 | 100 | AA contact springs (possible DIY holder; not chosen) |

---

## Sources (pages actually fetched on 2026-11-18 UTC+7 unless marked)

- [S1] WHO, *Arsenic* fact sheet (key facts; 140 million in ≥70 countries; 94–220 million modelled; countries; no reliable global burden estimate; safe-water provision as key action). https://www.who.int/news-room/fact-sheets/detail/arsenic
- [S2] WHO, *Water quality and health: Review of turbidity* (WHO/FWC/WSH/17.01, 2017). PDF https://iris.who.int/bitstream/handle/10665/254631/WHO-FWC-WSH-17.01-eng.pdf. **Not opened** (server timed out or blocked; archive copies failed). Figures (<1 NTU ideal for disinfection, <5 NTU small supplies) are from a search-result summary only: **unverified**. Landing page fetched: https://wsportal.org/resource/water-quality-and-health-review-of-turbidity/
- [S3] YSI (Xylem), *Turbidity measurement and monitoring in water quality analysis* (NTU vs FNU; cites the WHO brief: above 1 NTU, higher disinfection doses or contact times are needed). https://www.ysi.com/parameters/turbidity
- [S4] Kelley et al., Sensors 2014, section 1 (nephelometry = 90° scatter; NTU/FNU) — see [S7].
- [S5] USGS Oregon Water Science Center, *Turbidity — Units of Measurement* (FNU = ISO 7027 IR; NTU = EPA 180.1 white light; IR typical 780–900 nm; units not directly comparable). https://or.water.usgs.gov/grapher/fnu.html
- [S6] ISO 7027 860 nm figure, as stated by YSI [S3] (fetched) and by USGS [S5]. The ISO standard itself was not opened (paywalled).
- [S7] Kelley C.D. et al., *An Affordable Open-Source Turbidimeter*, Sensors 2014, 14(4), 7142–7155, doi:10.3390/s140407142 (860 nm LED + TSL230R at 90°; cutting-oil standards; Table 1 accuracy; light-leak and thermal-drift notes; <$25 parts). https://www.mdpi.com/1424-8220/14/4/7142
  - [S7b] Europe PMC record for the same article, showing licence "cc by": https://www.ebi.ac.uk/europepmc/webservices/rest/search?query=DOI:%2210.3390/s140407142%22&resultType=core&format=json
- [S8] Flanagan S.V., Johnston R.B., Zheng Y., *Arsenic in tube well water in Bangladesh: health and economic impacts and implications for arsenic mitigation*, Bull WHO 2012;90:839–846 (35–77 million exposed; 22 M >50 µg/L, 5.6 M >200 µg/L; about 43,000 deaths/yr, about 5.6% of deaths). Archived WHO page: https://web.archive.org/web/20140327071506/www.who.int/bulletin/volumes/90/11/11-101253/en
- [S9] Rahman M.M. et al., *Effectiveness and reliability of arsenic field testing kits: are the million dollar screening projects effective or not?*, Environ Sci Technol 2002;36(24):5385–5394 (abstract: false negatives up to 68%, false positives up to 35%, 44.9% mislabelling <50 µg/L, >1.3 million wells tested). https://researchportalplus.anu.edu.au/en/publications/effectiveness-and-reliability-of-arsenic-field-testing-kits-are-t/
- [S10] Scientific American, *Well Water Arsenic Tests Go Awry in Bangladesh*, 20 Nov 2002. https://www.scientificamerican.com/article/well-water-arsenic-tests/
- [S11] US EPA, *Monitoring Arsenic in the Environment: A Review of Science and Technologies for Field Measurements and Sensors*, EPA 542/R-04/002, April 2004 (Gutzeit method; interferences; up to 50% false readings; arsine above 0.05 ppmv TLV; mercuric bromide waste; electronic strip readers). https://www.epa.gov/sites/default/files/2015-08/documents/arsenic_paper.pdf
- [S12] *Challenges of Detecting Lead in Drinking Water Using at-Home Test Kits*, Environ Sci Technol 2021 (PMID 33428401) (16 brands, 12 with detection limits 5–20 mg/L; binary strips detect dissolved but not particulate lead). Abstract via Europe PMC: https://www.ebi.ac.uk/europepmc/webservices/rest/search?query=DOI:%2210.1021/acs.est.0c07614%22&resultType=core&format=json ; publisher page (timed out): https://pubs.acs.org/doi/full/10.1021/acs.est.0c07614
- [S13] George C.M. et al., *Evaluation of an arsenic test kit for rapid well screening in Bangladesh*, Environ Sci Technol 2012 (PMID 22866936, PMC3653972) (EQ kit 89%/92% correct; all WHO-guideline misclassifications underestimates for >10–27 µg/L). Abstract via Europe PMC: https://www.ebi.ac.uk/europepmc/webservices/rest/search?query=PMCID:PMC3653972&resultType=core&format=json
- [S14] WHO, *Chemical fact sheet: Lead* (GDWQ) (provisional guideline value 0.01 mg/L, not health-based, keep as low as reasonably practical). https://www.who.int/docs/default-source/wash-documents/wash-chemicals/lead-chemical-fact-sheet.pdf
  - [S14a] WHO, *Lead poisoning* fact sheet (IHME: >3.5 million deaths and >71.5 million DALYs in 2023; no safe level; 3.5 µg/dL; children absorb 4–5×; sources incl. plumbing). https://www.who.int/news-room/fact-sheets/detail/lead-poisoning-and-health
- [S15] LCSC data, PY32F002AF15P6TU. https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=C5292059
- [S16] LCSC data, CH32V003F4P6 and CH32V003J4M6. https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=C5187096 ; https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=C5346354
- [S17] LCSC data, PY32F002BL15S7TU (and product page via search result, https://www.lcsc.com/product-detail/C49424671.html). https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=C49424671 ; PY32F030F18P6TU: https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=C3018715
- [S18] Puya, *PY32F002B datasheet* V0.9 (Chinese): run 1.1 mA @24 MHz, Stop 1.5 µA, ADC 300 µA @1 Msps, 128-byte UID area. https://www.puyasemi.com/download_path/%E6%95%B0%E6%8D%AE%E6%89%8B%E5%86%8C/MCU%20%E5%BE%AE%E5%A4%84%E7%90%86%E5%99%A8/PY32F002B_Datasheet_V0.9.pdf (PY32F002A figures assumed similar: **unverified**)
- [S19] LCSC data, IR LEDs: C5349968, C2936543, C6081336, C7470713, C5200795, C72053, via https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=<code>. Everlight IR333C datasheet (940 nm): https://en.everlight.com/wp-content/plugins/ItemRelationship/product_files/pdf/IR333C.pdf (seen via search result)
- [S20] LCSC data, detectors: C98114, C95402, C131276, C264295, C264281, C20416370, C73930, via the same endpoint.
- [S21] Everlight PT333-3C datasheet (400–1100 nm bandwidth, 940 nm peak), via TME, seen in search results only: https://www.tme.eu/Document/563a7740fe3c16050d3f8d274884710b/PT333-3C.PDF. **Treat as unverified.**
- [S22] LCSC data, VEML6040A3OG (C78465); Vishay VEML6040 datasheet https://www.vishay.com/docs/84276/veml6040.pdf (seen via search result).
- [S23] LCSC data, TCS34725FN (C2649487): productCycle "stop_product".
- [S24] LCSC data, LEDs: C51933352, C2843868, C2895484, C2895494, C779442, C2895550.
- [S25] LCSC data, MLT-8530 (C94599), ME6211C33M5G-N (C82942), TS-1187A-C-C-B (C318889), BH-AA-A5BJ034 (C51883821), MY-AA-5355-J (C5371785). MLT-8530 spec (2.5–4.5 V, 2.7 kHz, 95 mA max) also from LCSC/JLCPCB search results: https://www.lcsc.com/product-detail/C94599.html
