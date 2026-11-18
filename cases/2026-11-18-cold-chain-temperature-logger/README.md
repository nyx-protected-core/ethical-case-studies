# Cold Box Watch: design study for a cheap, offline temperature logger for the last mile of the vaccine cold chain

Ethical case study (essential-medicines access). Research and design writing only: no firmware, no builds.
Author role: assembly / firmware engineer. I would write the firmware in C and assembly later; this document contains **no code**.
All LCSC prices, stock and lifecycle labels were read from LCSC's product-detail data on **2026-11-18** and will change.

> **This is not a WHO PQS-prequalified device, and it must never be used instead of one.** It is a teaching and auxiliary design. A district or clinic that has PQS electronic freeze indicators, 30-day temperature recorders (30 DTRs) and vaccine vial monitors (VVMs) must keep using them and follow their SOPs. Section 7b explains why this matters.

---

## Summary

- **What it is:** a palm-sized sealed logger that a health worker places with the most freeze-sensitive vaccine in a cold box or vaccine carrier when vaccines leave the district store for a clinic or an outreach session. It wakes every 5 minutes, measures, logs and goes back to sleep. On arrival, one button press answers one question: *did this load stay safe, or was there a freeze or heat excursion that means quarantine and report?* It has no radio, no app, no account and no network.
- **How it answers without a smartphone:** a green LED with a tick and a red LED with a cross, in different positions so colour-blind users can read them, plus a buzzer pattern. A small segment LCD shows the tick or cross, the minimum and maximum temperatures, cumulative minutes outside +2 to +8 °C, the elapsed trip time and a per-day history. The thresholds copy the WHO PQS factory alarms: **freeze at ≤ −0.5 °C for 60 minutes** and **heat at ≥ +8 °C for 10 hours** (30 DTR) [S1][S3], plus the shipping-indicator transport alarms of **≥ +30 °C cumulative for 10 hours** and **≥ +45 °C for 1 hour** [S2].
- **Recommended build (A):** ST **STM32L011F4P6TR** MCU with its own RTC on a 32.768 kHz crystal, a TI **TMP117** sensor (**±0.1 °C max from −20 to +50 °C** [S13]), a 32 KB I²C EEPROM log, a Holtek **HT1621B** segment-LCD driver, a 25 nA LDO, and **2× AA lithium-iron-disulfide cells** that are rated to −40 °C [S26]. **About US$4.39 in LCSC-verified electronics** at roughly 30–50 units. My rough per-unit total with LCD glass, PCB, enclosure and cells is **US$9–16**, but the non-LCSC lines are estimates and are marked unverified.
- **Cheapest build (B):** a Puya **PY32F002B** MCU plus a **BM8563** external RTC. It has no LDO because every part tolerates the raw cell voltage. **About US$3.27 in LCSC electronics**, but it has a tight 12-pin I/O budget and about 3× the sleep current.
- **Sensor finding:** the accurate sensor turns out to be cheap. The TMP117 costs **$1.17 @30** on LCSC (`normal`, 2,280 in stock) and, at the sensor die, before enclosure and placement error, beats the PQS ±0.5 °C requirement by 5×. The cheaper TI parts **fail at exactly the point that matters most, the freeze threshold**. TMP112A is ±0.5 °C only from 0 to +65 °C and ±1.0 °C below that [S15]. TMP1075 is ±1 °C max [S16]. TMP102 is ±2 °C max [S17]. The MCP9808 meets ±0.5 °C max but needs at least 2.7 V [S14], which a cold cell can drop below. The Sensirion STS40 and STS30 product pages **return null on lcsc.com today**, although JLCPCB lists them [S31].
- **Display finding:** no healthy, low-power LCSC display fits. The only 0.91" OLED on LCSC (HS91L02W2C01) needs **3.5–4.2 V and 23 mA** for its internal DC/DC [S22]. The low-power answer, a segment LCD like those on certified 30 DTRs, needs **custom or semi-custom glass that LCSC does not sell**. The driver chip (HT1621B) is on LCSC. The glass is **unverified** and has to come from an LCD glass maker.
- **Power:** the typical average is about **1.3 µA** (my arithmetic in section 5, using ST's 0.67 µA Stop-mode figure), or about **0.95 mAh per 30 days**. Energy is not the constraint; **cold is**. Coin cells keep their energy in the cold but struggle to deliver pulse current, and a brown-out reset during an alarm would be the worst possible failure. So the design uses AA lithium cells, and any loss of power forces a **NOT OK** state.
- **Evidence:** a UNICEF-authored review found exposure below the recommended range during **33% (wealthier) / 37.1% (lower-income)** of storage evaluations and in shipments in **38% / 19.3%** of studies [S7]. An earlier PATH review found **14–35%** of refrigerators or shipments exposed vaccine to freezing, and **75–100%** of shipments when the whole chain was followed [S8]. WHO says VVMs **do not record freezing** [S5] and that without a monitoring device "it is impossible to detect freeze damage" in transport [S5].
- **Ethics core:** the main risk is **false reassurance**, an OK on a load that froze. The design treats every uncertain state (sensor fault, power loss, clock loss, log error, low battery) as **NOT OK**, never as OK. The data belongs to the clinic, stays on the device and leaves only by cable when the clinic chooses.

---

## 1. Purpose, users and the decision it informs

**Users.** District cold-chain officers and health workers move vaccines from the district (lowest delivery-level) store to health facilities and outreach sites in passive containers, cold boxes and vaccine carriers, packed with conditioned ice-packs or cool-water packs [S5][S6]. The receiving health worker has to decide on arrival whether the load can be used.

**The decision.** *Is this batch still within its allowed temperature exposure, or did a freeze or heat excursion happen that means quarantine and report?* WHO's transport guidance is explicit. With freeze-sensitive vaccine packed with conditioned ice-packs, an electronic freeze indicator must travel with the load, and its status is checked and recorded at the receiving facility. "If an alarm is triggered, carry out a shake test on each type of freeze-sensitive vaccine in the load". If only a few vials are in the load, "the freeze-sensitive vaccines in the load should be quarantined and the shipment should be replaced" [S6]. This device feeds that existing decision. It does not invent a new one.

**Why heat and freeze are handled differently.** The VVM on each vial records cumulative heat exposure by a colour change, but "VVMs DO NOT record exposure to freezing temperatures" [S5]. Freezing is the gap. Frozen ice-packs drive a container "to well below 0°C" for "up to several hours", and conditioning packs correctly "is a challenge" in practice [S6]. So the **freeze function is the safety-critical core**. The heat functions are a secondary record that sits alongside the VVM and never replaces it.

**How a low-resource user reads it (no smartphone):**
- **Press the button once.** This wakes the display for about 15 seconds.
- **Green LED (left, under a printed tick) lit steadily for 2 s, plus one short beep** = no alarm since the trip started.
- **Red LED (right, under a printed cross) plus three long beeps** = at least one alarm fired. The LCD shows which one (snowflake for freeze, sun for heat) and on which day.
- **Amber LED (middle, under a "?")** = the device **cannot vouch** for the load: sensor fault, power interruption, clock loss, low battery, log fault, or a trip that was never started. **Amber is never a pass.** The printed instruction says to treat it like an alarm and follow the SOP.
- **LCD:** tick or cross, the current temperature, trip minimum and maximum, **cumulative minutes outside +2 to +8 °C** (shown even when no alarm fired, so nothing is hidden), elapsed trip time, and a history mode that steps through each day's min/max. The tick or cross stays on every screen, the way PQS requires for shipping indicators [S2].

**Why both LEDs and an LCD.** LEDs are readable in a dark store and need no literacy. Under PQS, an LED-only freeze indicator must still show activation, battery health and above/below 0 °C, and its symbols must be readable "by users with all forms of colour blindness" [S3]. That is why the LEDs differ by **position and printed symbol**, not by colour alone. The LCD adds the numbers a supervisor needs for the report.

**The electronics in plain words.** The microcontroller spends more than 99.9% of its life in Stop mode, with only its real-time clock ticking from a 32.768 kHz quartz crystal. That crystal is the same tuning-fork part found in a watch. Every 5 minutes the RTC raises an interrupt. The MCU wakes in microseconds, powers the sensor over I²C, asks for one averaged conversion, writes the reading to EEPROM, updates its alarm counters and goes back to sleep. The LCD driver holds the display segments with a small AC waveform. A DC voltage would slowly damage the liquid crystal, which is why a dedicated driver chip is used. That chip is switched off entirely between button presses.

---

## 2. What WHO PQS actually requires (and why this device is not it)

WHO's PQS category **E006, Temperature monitoring devices** covers 30-day electronic refrigerator loggers, electronic shipping indicators and freeze indicators [S4]. Three specifications bear directly on this design. I quote the requirements I used:

| Requirement | E006/TR06.4, 30-day refrigerator logger (Type 1, +2 to +8 °C) [S1] | E006/TR07.3, electronic shipping indicator (Type 1) [S2] | E06/IN03.1, irreversible freeze indicator [S3] |
|---|---|---|---|
| Accuracy | **±0.5 °C or better, −20 to +20 °C** (12 months after calibration); ±1.0 °C after 12 months without re-calibration | **±0.5 °C, −5 to +25 °C**; ±1 °C from −20 to −5 and +25 to +55 °C; **time ±10 s/day** | **±0.5 °C at 0 °C** |
| Resolution | ±0.2 °C (−10 to +20 °C) | ±0.2 °C (−20 to +55 °C) | — |
| Measuring / logging | measure ≤ every 10 min; log ≤ hourly; rolling 30 days; daily min/max "history mode" | measure ≤ every 10 min; log at least the first violation of each alarm type | — |
| Response time | T90 ≤ 20 min (EN 12830) | T90 ≤ 10 min (EN 12830) | — |
| **Alarms** | **Low: ≤ −0.5 °C for 60 min. High: ≥ +8 °C for 10 hours** | **High ≥ +45 °C single event 1 h; Medium ≥ +30 °C cumulative 10 h; Low ≤ −0.5 °C single event 1 h** | **−0.5 °C ±0.5 °C for 60 ± 5 min**, not user-resettable |
| Operating range | logger −20 to +50 °C (Type 1, internal sensor) | −30 to +55 °C | −20 °C without failure; upper +43 °C |
| Power | non-replaceable battery, 2 years of operation after up to 1 year of shelf life | non-replaceable; 18-month storage life before start | non-replaceable or none; 3-year battery life |
| Protection | IP64; 5 drops from 1 m when cold | IP64 | IP64 (electronic) |
| Calibration | Certificate of Traceability and Calibration (ISO/IEC 17025 or NIST-traceable) | same | same |
| Display rules | low-battery warning; user must not be able to delete or modify logged data or alarm settings | OK/Alarm always visible; **no flashing** (must photocopy); language-independent symbols | colour-blind-safe; activation and battery status shown |

**Vaccine temperature range.** "With a few exceptions, vaccines should be stored at +2°C to +8°C" [S5]. Freeze-sensitive products include the aluminium-adjuvanted vaccines that the shake test checks [S5]. The WHO 30 DTR description matches the alarm table: alarms trigger "if the temperature in the refrigerator drops to −0.5°C or below for 60 minutes or if it exceeds +8°C for a continuous period of 10 hours" [S5].

**Which specification fits the last mile?** None exactly. TR06 is for refrigerators. TR07 is for international shipments arriving at the primary store. IN03 is a pure freeze indicator. WHO's transport table for cold boxes and vaccine carriers names the **electronic freeze indicator plus VVMs** as best practice whenever freeze-sensitive vaccine travels with conditioned ice-packs [S5]. A footnote in the same module states that "WHO PQS does not have specifications" for one of the in-vehicle monitoring categories [S5]. So I designed to the **strictest accuracy figure among the three (±0.5 °C around 0 °C)**, the **IN03/TR06 freeze alarm**, the **TR07 heat alarms** for transport, and the **TR06 daily min/max history**.

**Honesty statement.** A DIY device built from this study is **not PQS-prequalified** and cannot be. Prequalification needs a legal manufacturer, a product dossier, laboratory verification, and a calibration certificate traceable to an ISO/IEC 17025 lab for **each unit** [S1][S2]. This design also deliberately breaks one PQS rule: it uses **replaceable** batteries (section 5), where PQS demands non-replaceable ones [S1][S2]. That trade makes it cheaper to keep alive, but it opens a path to tampering and to resets, which the firmware has to handle (section 4.6).

---

## 3. Sensing: which sensor, and the honest cost/accuracy trade

The decision line sits at **−0.5 °C**, and the device has to tell −0.4 °C from −0.6 °C reliably over an hour. A sensor that is ±1 °C at 0 °C could read +0.4 °C while the vial sits at −0.6 °C. That is a missed freeze, which is exactly the false reassurance this device must not produce. So I judged each candidate on its **guaranteed maximum error around 0 °C**, not its typical error.

| Sensor | Guaranteed accuracy (datasheet) | Supply | Power | LCSC (read 2026-11-18) | Verdict |
|---|---|---|---|---|---|
| **TI TMP117AIDRVR** | **±0.1 °C max, −20 to +50 °C**; ±0.15 °C max −40 to +70 °C [S13] | 1.7–5.5 V (−55 to +70 °C) [S13] | 135 µA during conversion, 0.15 µA typ shutdown; 8-average conversion takes 124 ms [S13] | **C699536, `normal`, $1.1717 @30, 2,280 in stock** | **Chosen.** 5× better than the PQS ±0.5 °C. Room for enclosure and placement error. TI lists NIST traceability of its production test and ASTM E1112 / ISO 80601 medical-grade compliance [S13]. |
| TI TMP117AIYBGR (WCSP) | same family [S13] | same | same | C2871893, `normal`, $1.989 @30, 1,927 | Same die in a chip-scale package; harder to hand-assemble. Not needed. |
| Microchip MCP9808 | ±0.25 °C typ, **±0.5 °C max −20 to +100 °C** [S14] | **2.7–5.5 V** [S14] | 200 µA typ operating, 0.1 µA shutdown; 250 ms at 0.0625 °C resolution [S14] | C129490 `normal` $1.7177 @30, 481 stock; C94847 `normal` $1.6863 @30, 161 stock | Meets PQS with **zero margin**. Costs more than the TMP117. Its 2.7 V floor is risky on cold cells. **Rejected.** |
| TI TMP112AIDRLR | **±0.5 °C max only 0 to +65 °C**; ±1.0 °C max −40 to +125 °C [S15] | 1.4–3.6 V [S15] | 3.2 µA typical quiescent [S15] | C28927, `normal`, $0.4084 @30, 4,851 | **Fails at the freeze threshold.** Below 0 °C its guarantee is ±1 °C. **Rejected.** |
| TI TMP1075 | ±0.25 °C typ, **±1 °C max** −40 to +110 °C [S16] | — | 2.7 µA average, 0.37 µA shutdown [S16] | C2864807 `normal` $0.281 @30, 21,443; C3663690 `normal` $0.4179 @30 | Cheap and plentiful, but ±1 °C max. **Rejected.** |
| TI TMP102 | ±0.5 °C typ, **±2 °C max** −25 to +85 °C [S17] | — | — | C99269, `normal`, $0.4481 @30, 34,056 | **Rejected.** |
| Sensirion STS40 / STS30 | accuracy **unverified** (datasheet not opened this turn) | — | — | **Not on lcsc.com**: C3663715 (STS40-AD1B-R3) and C129802 (STS30-DIS) return null. JLCPCB lists STS40-AD1B-R3 with 10,299 stock and STS30-DIS-2.5KS with 1,141 [S31] | Fails the LCSC-only rule. The route is an authorised distributor (DigiKey, Mouser) or JLCPCB assembly. Not needed, because the TMP117 is better documented and on LCSC. |
| TI HDC-series humidity/temperature | not evaluated | — | — | — | Humidity is not needed here. Skipped. |

**The trade, stated honestly.** The ±0.1 °C TMP117 costs about **$0.76 more** per unit than the ±1 °C TMP1075 ($1.1717 vs $0.281 @30, which is under a dollar). The vaccines in one cold box are worth hundreds of dollars (section 7b). Spending the extra $0.76 is the clearest call in this study. The TMP117 is also **cheaper** than the MCP9808 at LCSC today, so the "accurate sensors are expensive" intuition does not hold here.

**What sensor accuracy does not cover.** The ±0.1 °C applies at the sensor die. **System error** is larger: the die sits in a plastic case, the case sits next to vials, and the vial nearest an ice-pack is colder than the one in the middle. PQS asks for T90 ≤ 10 min for shipping indicators [S2]. A thick, sealed case can lag by far more than that. So the sensor needs a **thermal path to the outside**: a thin PCB tongue pressed against the case wall, little air around it, and low thermal mass. The real T90 of the enclosure is **unverified** until it is bench-tested against a calibrated reference (section 8).

**Calibration.** Each unit gets a two-point check at a stirred ice bath (0 °C) and a reference near +5 °C, compared against a reference thermometer. The result is stored as a per-unit offset. The TMP117 includes a programmable offset register and EEPROM for this [S13]. That check is a **workshop practice, not an ISO/IEC 17025 certificate**, and the device must not claim otherwise.

---

## 4. Parts: cheapest active LCSC parts, and where LCSC has no healthy source

**How I checked.** For each part I read LCSC's own product-detail data (`wmsc.lcsc.com/ftps/wm/product/detail?productCode=C…`) on **2026-11-18**, and recorded the `productCycle` field, price breaks and stock. `normal` is LCSC's label for a part in normal supply. It is the **distributor's** label, not the manufacturer's lifecycle statement, and it is not the same as "In Production" wording on a listing page. A `null` result means the product page does not exist on lcsc.com today. To find candidate C-codes I used JLCPCB's public parts search [S31], then confirmed every code on LCSC itself. None of the parts below showed an obsolete, EOL or NRND label on LCSC. I did **not** check each manufacturer's own lifecycle page, so manufacturer lifecycle status is **unverified**.

### 4.1 MCU candidates

The firmware needs I²C (sensor, EEPROM, RTC in build B), about 3 GPIOs for the LCD driver, 3 LEDs, a buzzer, a button, and peripheral power. It also needs an accurate timebase. Log size: 30 days × 288 samples/day (one every 5 min) = **8,640 samples**. At 2 bytes each that is **17,280 bytes**, plus a header and event records, which fits a 32 KB EEPROM with room to spare (my arithmetic). An MCU's own flash is the wrong place for a rolling log, because erasing a flash page every few hours wears it and risks losing data on brown-out. A byte-writable EEPROM rated for 1,000,000 cycles and 40-year retention [S20] is the right tool.

| MCU | LCSC | Lifecycle (LCSC) | Price | Stock | Why / why not |
|---|---|---|---|---|---|
| **ST STM32L011F4P6TR** (Cortex-M0+, TSSOP-20) | **C503083** | `normal` | **$0.8151 @30** | 19,970 | **Chosen for build A.** 16 KB flash, 2 KB RAM, 512 B data EEPROM, built-in **RTC with 32 kHz crystal oscillator and calibration**. **0.54 µA in Stop + RTC + 2 KB RAM retention** in the datasheet used for the first budget [S18]. ST's product page gives **0.67 µA** for Stop mode + RTC + 2 KB RAM retention. This revision uses 0.67 µA in the power budget. Supply 1.65–3.6 V, −40 to +125 °C [S18]. The RTC removes a separate clock chip and gives watch-crystal accuracy. |
| ST STM32L011F4P6 (tube packing) | C87903 | `normal` | $1.1325 @30 | 2,494 | Same die, dearer packing. Use C503083. |
| **Puya PY32F002BD15S6TU** (Cortex-M0+, SOP-14) | **C7469097** | `normal` | **$0.1344 @50** | 1,750 | **Build B (cheapest).** 24 KB flash, 1.7–5.5 V, −40 to +85 °C, 12 I/O (LCSC parameters). Stop current **1.70 µA** with LPTIM on its internal 32 kHz RC, a figure carried from our grain study [S28] and not re-fetched this turn. **No crystal RTC**, so it needs the BM8563 below. The 12 I/O are fully used, which leaves no spare pins (section 4.6). |
| ST STM32L031F6P7 (TSSOP-20) | C1337403 | `normal` | $1.5156 @30 | 1,576 | Larger-flash sibling for a richer UI. Its flash/RAM size and Stop+RTC current were **not fetched this turn (unverified)**. |
| Rejected: STM32L053C8T6 (has an on-chip LCD controller) | C458472 | `normal` | $5.0263 @30 | **96** | Would remove the HT1621B, but it costs 6× more and LCSC stock is thin. Rejected. |
| Rejected for now: WCH CH32V003F4P6 | C5187096 | `normal` | $0.2396 @50 | 3,215 | Cheap, but it has no crystal RTC and its standby current is **unverified** for this design. |

### 4.2 Timebase

- **Build A:** the STM32L011 RTC runs from an **Epson FC-135 32.768 kHz crystal (C32346, `normal`, $0.1177 @50, 235,995 in stock)**. Its specs: ±20 ppm at +25 °C, turnover 20–30 °C, parabolic coefficient up to −0.04 ppm/°C², CL 12.5 pF, −40 to +85 °C [S24]. At +5 °C, 20 °C below turnover, the parabola adds up to −0.04 × 20² = **−16 ppm**. Worst case is about −36 ppm ≈ **−3.1 s/day** (my arithmetic). That is inside PQS TR07's ±10 s/day [S2], and the RTC's smooth calibration can trim it further [S18]. Two load capacitors, nominally 22 pF 0603 (C1653, `normal`, $0.009 @50), are a starting value to tune on the bench (**unverified** for this board's stray capacitance).
- **Build B:** **BM8563ESA RTC (C269877, `normal`, $0.1848 @30, 51,806 in stock)**. Clock runs from 1.2 to 5.5 V, typical backup current 0.5 µA at 3.0 V, I²C [S19]. Its datasheet characterises a crystal with CL = 8 pF [S19], so the FC-135 (12.5 pF) is a **mismatch**. Build B needs a 7–9 pF 32.768 kHz crystal, which I did **not** look up on LCSC (**unverified**).

### 4.3 Log storage

| Part | LCSC | Lifecycle | Price | Stock | Notes |
|---|---|---|---|---|---|
| **Microchip AT24C256C-SSHL-T** (32 KB I²C EEPROM) | **C6482** | `normal` | **$0.248 @30** | 67,079 | **Chosen.** 1.7–5.5 V, 5 ms max write, 1,000,000 cycles, 40-year retention; standby up to 1 µA at 1.7 V and 6 µA at 5 V [S20]. It is **power-gated** from a GPIO between writes, so it adds nothing to sleep current. |
| Microchip 24LC256T-I/SN | C5458 | `normal` | $0.3756 @30 | 44,744 | Alternative. Datasheet not opened (**unverified** specs). |
| Infineon FM24CL16B-G (2 KB F-RAM) | C2953561 | `normal` | $1.1883 @30 | 483 | Effectively unlimited writes, but only 2 KB, which is too small for 30 days at 5 min. Rejected. |
| Winbond W25Q16JVSSIQ (SPI NOR flash) | C131025 | `normal` | $1.066 @50 | 235 | Too much capacity, page-erase wear, thin stock. Rejected. |

### 4.4 Display and indicators

| Part | LCSC | Lifecycle | Price | Stock | Notes |
|---|---|---|---|---|---|
| **Holtek HT1621B** segment-LCD driver (32×4, SSOP-48) | **C7873** | `normal` | **$0.3635 @30** | 78,789 | **Chosen.** 2.4–5.2 V, 60 µA typ with crystal or 150 µA typ with its RC oscillator (LCD on, 3 V), **0.1 µA typ standby** (5 µA max) [S21]. −40 to +85 °C [S21]. A second listing, C7532, is also `normal` ($0.4807 @30, 11,581 stock). |
| **Segment LCD glass** (TN, wide-temperature, custom or semi-custom icons: tick, cross, snowflake, sun, "?", 4 digits, °C, battery) | **no healthy LCSC source** | — | **unverified** | — | LCSC search showed only zero-stock or un-coded "segment LCD" listings. The route is an LCD glass maker (custom tooling) or an authorised distributor's catalogue glass; **price and cold-temperature response time are unverified**. TN fluid slows down near 0 °C, so a wide-temperature fluid has to be specified. |
| HS91L02W2C01 0.91" 128×32 OLED (SSD1306) | C5248081 | `normal` | $1.7606 @30 | 2,358 | **Rejected for the field build.** Needs **VBAT 3.5–4.2 V for its internal DC/DC, 23 mA typ / 29 mA max** with the whole display lit [S22], which 2× AA lithium cannot supply without a boost converter. Emissive, not photocopy-able. Fine for a **bench prototype** on USB power only. |
| Red / amber / green LEDs (0805) | C84256 / C2296 / C2297 | `normal` | $0.0136 / $0.0125 / $0.0166 @50 (**$0.0427 combined**) | 1.9 M / 243 k / 2.2 M | Position + printed symbol make the meaning colour-blind-safe [S3]. |
| Buzzer: MLT-8530 magnetic transducer | C94599 | `normal` | $0.1548 @50 | 108,145 | Driven by a MOSFET. Its rated current is **unverified** (datasheet not re-opened). This pulse is one reason for AA cells over a coin cell. |
| Buzzer driver AO3400A N-MOSFET | C20917 | `normal` | $0.0627 @50 | 580,515 | — |
| Tact switch TS-1187A | C318884 | `normal` | $0.0216 @20 | 471,700 | One button: short press = show status; long press with a paper-clip-recessed second contact = start trip (design detail **unverified**). |

### 4.5 Power parts

| Part | LCSC | Lifecycle | Price | Stock | Notes |
|---|---|---|---|---|---|
| **TI TPS7A0233DBVR** 3.3 V LDO (build A) | **C5142805** | `normal` | **$0.6098 @30** | 7,494 | **25 nA Iq typ, even in dropout**; input 1.5–6.0 V [S23]. Keeps fresh lithium cells (up to 2 × 1.83 V OCV ≈ 3.66 V [S26]) below the STM32L011's 3.6 V limit [S18], and adds almost nothing to sleep current. |
| Torex XC6206P332MR-G (rejected) | C5446 | `normal` | $0.1127 @50 | 85,165 | Cheaper, but its Iq (≈1 µA class, **unverified**, not re-read) would be larger than the whole MCU sleep budget. |
| 1×AA holder BH-AA-A1AJ020 (×2) | C5290181 | `normal` | $0.3318 @30 each | 2,732 | Two single holders in series. A 2×AA LCSC holder was not found in this search. |
| AA lithium-iron-disulfide cells (e.g. Energizer L91) ×2 | **not an LCSC part** | — | **unverified** (retail) | — | Rated −40 to +60 °C; "approximately full rated capacity at −40°C" at 25 mA [S26]. |
| Passives: 330 Ω ×3 (C23138), 4.7 kΩ ×2 I²C pull-ups (C23162), 10 kΩ ×2 (C25804), 100 nF ×4 (C14663), 10 µF ×2 (C15850) | as listed | all `normal` | **≈ $0.0999 combined** (100-pc tier for resistors/100 nF; 20-pc tier for 10 µF) | all > 98,000 | — |

### 4.6 Consolidated parts table (build A unless marked)

"LCSC label" here means LCSC `productCycle = normal` on 2026-11-18. That is the **distributor's** label, not a manufacturer lifecycle notice. Manufacturer or DigiKey status was read on 18 November 2026. Lifecycle is stated as confirmed only where that status was read. All other rows are marked **verify lifecycle with manufacturer**.

| Part | LCSC code | LCSC label | Manufacturer / DigiKey status (18 Nov 2026) | Price (small qty) | Stock | Role |
|---|---|---|---|---|---|---|
| STM32L011F4P6TR | C503083 | `normal` | **Active** on the ST product page; 10-year longevity from 2026-01-01 [S18] | $0.8151 @30 | 19,970 | MCU + RTC (A) |
| PY32F002BD15S6TU | C7469097 | `normal` | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | $0.1344 @50 | 1,750 | MCU (B) |
| BM8563ESA | C269877 | `normal` | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | $0.1848 @30 | 51,806 | RTC (B) |
| TMP117AIDRVR | C699536 | `normal` | **ACTIVE** on the TI product page [S13] | $1.1717 @30 | 2,280 | Temperature sensor |
| AT24C256C-SSHL-T | C6482 | `normal` | DigiKey **Part Status Active** [S20] | $0.248 @30 | 67,079 | 30-day log |
| HT1621B | C7873 | `normal` | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | $0.3635 @30 | 78,789 | LCD driver |
| FC-135 32.768 kHz | C32346 | `normal` | **Verify lifecycle with manufacturer** (**UNVERIFIED**; not found on DigiKey under the searched number) | $0.1177 @50 | 235,995 | RTC crystal (A) |
| 22 pF 0603 ×2 | C1653 | `normal` | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | $0.009 @50 each | 963,350 | Crystal load (A) |
| TPS7A0233DBVR | C5142805 | `normal` | **ACTIVE** on the TI TPS7A02 product page [S23] | $0.6098 @30 | 7,494 | 3.3 V LDO (A) |
| LEDs R/Y/G | C84256 / C2296 / C2297 | `normal` | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | $0.0427 set @50 | ≥ 242,800 | OK / ? / ALARM |
| MLT-8530 | C94599 | `normal` | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | $0.1548 @50 | 108,145 | Buzzer |
| AO3400A | C20917 | `normal` | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | $0.0627 @50 | 580,515 | Buzzer driver |
| TS-1187A | C318884 | `normal` | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | $0.0216 @20 | 471,700 | Button |
| BH-AA-A1AJ020 ×2 | C5290181 | `normal` | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | $0.3318 @30 each | 2,732 | Cell holders |
| Passives (see 4.5) | C23138, C23162, C25804, C14663, C15850 | `normal` | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | ≈ $0.0999 total | large | Support |
| *Rejected / not on LCSC* | | | | | | |
| MCP9808T-E/MS | C129490 | `normal` | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | $1.7177 @30 | 481 | Sensor alternative (rejected) |
| TMP112AIDRLR | C28927 | `normal` | **ACTIVE** on the TI product page [S15] | $0.4084 @30 | 4,851 | Rejected (±1 °C below 0 °C) |
| TMP1075DGKR | C2864807 | `normal` | **ACTIVE** on the TI product page [S16] | $0.281 @30 | 21,443 | Rejected (±1 °C) |
| TMP102AIDRLR | C99269 | `normal` | **ACTIVE** on the TI product page [S17] | $0.4481 @30 | 34,056 | Rejected (±2 °C) |
| STS40-AD1B-R3 / STS30-DIS-2.5KS | C3663715 / C129802 | **not on lcsc.com (null)** | Not used; **verify lifecycle with manufacturer** if ever sourced | JLCPCB only | JLCPCB 10,299 / 1,141 | Not used; authorised-distributor route |
| HS91L02W2C01 OLED | C5248081 | `normal` | **Verify lifecycle with manufacturer** (**UNVERIFIED**) | $1.7606 @30 | 2,358 | Bench only (3.5–4.2 V, 23 mA) |
| Segment LCD glass | — | **no LCSC source** | Not applicable | unverified | — | Must come from an LCD glass maker |
| AA Li-FeS2 cells | — | not LCSC | Not applicable | unverified | — | Power |

### 4.7 Firmware outline (prose only, no code)

1. **Factory/workshop setup.** Load the per-unit calibration offset (from the two-point check in section 3) into the sensor's offset register. Write the unit ID and the alarm table to a write-once area of the MCU's data EEPROM. The alarm table is fixed in firmware: freeze ≤ −0.5 °C for 60 min, heat ≥ +8 °C for 10 h, transport ≥ +30 °C cumulative for 10 h, ≥ +45 °C for 1 h [S1][S2][S3]. There is no menu to change thresholds, because PQS forbids user changes to logged data and alarm settings [S1].
2. **Start a trip.** A deliberate long press on the recessed contact starts a trip. The firmware writes a trip header (start time, unit ID, battery voltage) and then waits a short **settling delay** before alarms count, the way PQS allows up to 60 min for shipping indicators [S2]. Unlike PQS, readings during the delay are **logged and shown**, and any reading ≤ −0.5 °C during the delay still counts toward the freeze alarm. A freeze that starts while ice-packs are mis-conditioned must not be hidden by a start delay.
3. **Periodic wake (every 5 minutes, RTC alarm interrupt).** Power the sensor and EEPROM from GPIO. Request one TMP117 one-shot conversion with 8× averaging (≈124 ms [S13]), sleeping in a low-power mode while it converts. Read the result, apply sanity checks (I²C acknowledge, value within −55..+150 °C, change since last sample physically plausible), timestamp it from the RTC, and append it to the EEPROM ring. The ring overwrites the oldest record after 30 days, matching PQS behaviour [S1][S4].
4. **Alarm logic (time-at-temperature).** Keep a **continuous-run counter** for freeze (resets when the temperature rises above −0.5 °C) and for the +8 °C and +45 °C single events. Keep a **cumulative counter** for ≥ +30 °C. When a counter crosses its duration, set a **latched alarm flag** in EEPROM with the day and time of the first violation. Latched flags are never cleared by the user, matching the IN03 rule that "it must not be possible for the end user to re-set the device after a freezing event" [S3]. Also accumulate **minutes outside +2..+8 °C** and the trip min/max for display, even when no alarm fires.
5. **Fail-safe states (never OK).** Any sensor read failure, out-of-range value, EEPROM write or verify failure, RTC invalid flag, brown-out reset, watchdog reset, low battery, or a **power-up without a clean shutdown record** latches the **"?" state**. The device then cannot show the tick until the trip is closed and a new one is started, and the reason is logged. A battery swap during a trip is therefore always visible.
6. **Status update.** After each sample, recompute the overall state as ALARM (any latched alarm), "?" (any fault latch, with no alarm), or OK. Do not light anything automatically except a rare (every 30 s) 5 ms red blink while in ALARM, so a passer-by can see it without pressing. A shipping-indicator design would avoid this flashing for photocopy reasons [S2], so I treat it as an open question (section 8).
7. **Button press.** Debounce. Power the HT1621B and show the state icon, current temperature, min/max, minutes out of range and elapsed time. Light the matching LED for 2 s and sound the matching buzzer pattern. Further presses step through the daily min/max history, as in the TR06 history mode [S1]. Apply a **pause period** so the warm hand and air of a reading do not count as an excursion, as TR06 requires [S1]. Pause only the alarm counters, never the logging or the clock, and cap the pause at a few minutes. After 15 s, power down the LCD driver.
8. **Offline readout (optional).** A UART on test pads streams the log as plain text (CSV-like) to a cheap USB-serial adapter on a computer the clinic or district controls. The port is **read-only**: it has no command that erases or edits records.
9. **Deep sleep.** Re-arm the RTC alarm, gate all peripherals off, and enter Stop mode (0.67 µA with RTC on the STM32L011, from ST's product page [S18]). The independent watchdog runs on the internal low-speed oscillator, so a firmware hang becomes a logged reset ("?") rather than a silent stall.

**Build B differences:** the BM8563's interrupt output wakes the PY32 instead of an internal RTC. With 12 I/O there are just enough pins: I²C ×2, RTC interrupt, HT1621 CS/WR/DATA ×3, LEDs ×3, buzzer, button, peripheral power. That leaves **no spare pin** for the UART readout unless I²C or the SWD debug pins are shared. **Unverified** until a pin map is drawn.

---

## 5. Power: 30 days unattended, and the cold

### 5.1 Budget (build A, typical; my arithmetic from the cited datasheet figures)

| Item | Basis | Average current |
|---|---|---|
| MCU Stop + RTC + RAM retention | 0.67 µA on ST's product page for Stop + RTC + 2 KB RAM retention [S18] (0.54 µA was used in the first datasheet budget) | 0.67 µA |
| TMP117 shutdown | 0.15 µA typ (0.5 µA max) [S13] | 0.15 µA |
| LDO quiescent | 25 nA typ [S23] | 0.03 µA |
| EEPROM, HT1621B | power-gated from GPIO | ≈ 0 |
| Independent watchdog + its oscillator | **unverified**, not read from the datasheet | (not included) |
| Sample every 5 min | TMP117 124 ms × 135 µA ≈ 16.7 µC [S13]; EEPROM write ≤ 5 ms × ≤ 3 mA ≈ 15 µC [S20]; MCU awake ≈ 150 ms at an **assumed** 0.3 mA ≈ 45 µC (**unverified** estimate). Total ≈ 77 µC per 300 s | ≈ 0.26 µA |
| Display reads | **assumed** 10 presses/day × 15 s × HT1621B 150 µA (RC oscillator, typ [S21]) ≈ 22.5 mC/day | ≈ 0.26 µA |
| **Total (typical)** | | **≈ 1.3 µA** |

- **30 days** at 1.3 µA × 720 h ≈ **0.95 mAh**. Even at a pessimistic 5× (max datasheet currents, more presses, a long ALARM-blink period) that is about **4.7 mAh per 30 days**. The change from the earlier 1.2 µA / 0.9 mAh figures is the ST Stop-mode current, and it is not material to the cell choice. The ALARM blink every 30 s at an assumed 2 mA × 5 ms adds about 0.33 µA (my arithmetic).
- **Build B** sleep is about 1.70 µA (PY32 Stop [S28]) + 0.5 µA (BM8563 typ [S19]) + 0.15 µA ≈ **2.4 µA**, so about **3× build A**. That is still only about 1.9 mAh per 30 days.
- **Conclusion:** energy is not the constraint. Any common primary cell holds 50–1,000× a 30-day budget. The real constraints are **cold, pulse current and voltage window**.

### 5.2 Battery choice and cold derating (honestly)

- **CR2032 coin cell.** Energizer rates it **−30 to +60 °C**, 235 mAh at a 15 kΩ load at 21 °C, ~1% per year self-discharge [S25]. **The datasheet gives no capacity or internal-resistance figures at 0 °C or below**, so its cold performance is **unverified** from my sources. The general behaviour is that internal resistance rises sharply in the cold, so a short high-current pulse (buzzer, LED, EEPROM write) can sag the voltage. If that sag trips the MCU's brown-out reset in the middle of an alarm, the alarm could be lost, or at best the unit falls back to "?". For a **freeze** logger that is unacceptable. **Rejected** for the field build. Acceptable for a bench demo.
- **2× AA lithium-iron-disulfide (e.g. Energizer L91). Chosen.** Rated −40 to +60 °C. "LiFeS2 batteries can deliver approximately full rated capacity at −40°C if they are discharged at 25 mA", and "battery capacity is not lost due to cold temperature use" [S26]. Our pulses are a few mA, well under 25 mA. Fresh OCV is 1.79–1.83 V per cell [S26], so up to about 3.66 V for two cells, which is why build A has the TPS7A02. The energy margin is so large that one set of cells covers many trips. That brings its own risk: cells that sit in the unit for years. The firmware shows "?" below a battery threshold, set to leave at least 30 days of margin, as PQS asks [S1].
- **2× AA alkaline. Not recommended.** The Energizer lithium manual says LiFeS2 "operates at temperatures where alkaline batteries do not run at all" [S26]. Alkaline also leaks over long storage. That leak risk is general knowledge, **unverified** from my sources.
- **Rechargeable (Li-ion, NiMH). Not recommended.** It adds a charger and charging infrastructure, and its self-discharge and charging limits near 0 °C were **unverified** this turn. A logger that is flat when the cold box leaves the store is worse than no logger, because people may assume it is working.
- **PQS tension.** PQS asks for non-replaceable batteries [S1][S2]. Replaceable AA cells make the device cheaper to keep in service and easier to repair locally. The price is that battery removal becomes a reset path, which section 4.7 step 5 turns into a visible "?" state instead of a silent one.


**Safety and regulatory points (several **UNVERIFIED** until sourced).** The battery discussion above covers cold performance and leakage. It does not yet settle the following, which should be checked before any field trial:

- Equipment that contains lithium cells is subject to transport rules. Units shipped to districts would need UN 38.3 test summaries for the cells, and would have to travel under the applicable air- and road-transport conditions (**UNVERIFIED** as to the exact packing instruction and whether a sealed logger is treated as equipment containing cells or as loose cells).
- Lithium and alkaline cells, and old and new cells, must not be mixed in the same device.
- End-of-life lithium cells have to be disposed of safely. Many districts have no battery recycling route (**UNVERIFIED** as to local practice). A programme would need a take-back path, not only a "do not bin" label.
- The logger rides among vials. Cleaning and disinfection agents, and how often the case can be wiped, have not been specified (**UNVERIFIED**).
- Whether a supplementary, non-PQS device used with vaccines falls under any national medical-device or health-programme rules is **UNVERIFIED** and should be asked of the national EPI programme before a trial (see also open question 9).


---

## 6. Enclosure, placement and readout

- **Where it rides.** With the **most freeze-sensitive vaccine**, as WHO says for freeze indicators ("Pack freeze indicator with most freeze-sensitive vaccine") [S5], and in the **coldest position** that vaccine occupies, usually against or between the coolant-packs. In a vaccine carrier the vial in the centre is the reference point PQS uses for container tests [S6]. The logger should sit where the vials are, not in the lid or in a pocket.
- **Thermal coupling.** The sensor sits on a narrow PCB tongue pressed against a thin section of the case wall, with a thermal pad and a small air gap elsewhere. The case should have low thermal mass so its T90 approaches PQS TR07's ≤ 10 min [S2]. **This T90 is unverified until bench-tested.**
- **Condensation.** Taking a cold logger out of a cold box into warm humid air will condense water on it, and inside it if it is not sealed. The design uses a gasketed case aiming at the PQS **IP64** level [S1][S2][S3], a conformal coat on the PCB, a small desiccant sachet, and no open vents. The cells sit in a separate sealed compartment so that changing them does not expose the electronics. **IP rating and enclosure part are unverified**, and no LCSC enclosure was checked.
- **Mechanics.** It must survive the TR06 style "5 drops from 1 m" test when cold [S1] (**unverified** for this design). A lanyard eye lets it be tied to the basket or carrier, as WHO suggests for 30 DTRs [S5].
- **Labels.** Printed, language-independent pictograms beside each LED (tick / ? / cross). "NOT A WHO PQS DEVICE – SUPPLEMENTARY ONLY – ALWAYS CHECK VVM AND CERTIFIED FREEZE INDICATOR" is printed in the local language on the front.
- **Readout without a network.** (1) On the device: tick/cross/"?", min/max, minutes out of range, and per-day history on the LCD. A photograph or a hand-copied line on the delivery form is the record, matching WHO's "check and record freeze indicator and VVM status at point of delivery" practice [S5]. (2) Optionally, a read-only UART on pads to a USB-serial adapter and any offline computer at the district store, giving a plain-text log that opens in a spreadsheet. No app, cloud, account or radio is involved.

---

## 6b. Bill of materials (per unit, low volume ≈ 30–50 units)

### Build A (recommended): STM32L011 + TMP117 + HT1621B, 2× AA lithium

| Line | Source | US$ / unit | Status |
|---|---|---|---|
| STM32L011F4P6TR | LCSC C503083 | 0.8151 | verified 2026-11-18 |
| TMP117AIDRVR | LCSC C699536 | 1.1717 | verified |
| AT24C256C-SSHL-T | LCSC C6482 | 0.2480 | verified |
| HT1621B | LCSC C7873 | 0.3635 | verified |
| TPS7A0233DBVR | LCSC C5142805 | 0.6098 | verified |
| FC-135 crystal | LCSC C32346 | 0.1177 | verified |
| 22 pF ×2 | LCSC C1653 | 0.0180 | verified (value to tune) |
| LEDs R/Y/G | LCSC C84256 / C2296 / C2297 | 0.0427 | verified |
| MLT-8530 buzzer | LCSC C94599 | 0.1548 | verified |
| AO3400A | LCSC C20917 | 0.0627 | verified |
| TS-1187A | LCSC C318884 | 0.0216 | verified |
| 2× AA holder | LCSC C5290181 ×2 | 0.6636 | verified |
| Passives | LCSC (section 4.5) | 0.0999 | verified |
| **LCSC electronics subtotal** | | **≈ 4.39** | my sum of the verified lines |
| Segment LCD glass | LCD glass maker | **unverified** (rough guess 1–3) | unverified |
| PCB (2-layer, small) | any fab | **unverified** (rough guess 0.5–2) | unverified |
| Gasketed enclosure + desiccant + label | not LCSC | **unverified** (rough guess 1–4) | unverified |
| 2× AA Li-FeS2 cells | retail | **unverified** (rough guess 2–6) | unverified |
| Assembly, calibration check | labour | not costed | — |
| **Rough per-unit total** | | **≈ 9–16** | **unverified** beyond the LCSC subtotal |

### Build B (cheapest): PY32F002B + BM8563 RTC, no LDO

Remove the STM32L011 (−0.8151), TPS7A02 (−0.6098) and 22 pF pair (−0.0180). Add the PY32F002BD15S6TU (+0.1344, C7469097) and BM8563ESA (+0.1848, C269877). **LCSC electronics subtotal ≈ US$3.27** (my arithmetic). The 7–9 pF crystal it needs is **unverified** on LCSC. The non-LCSC lines are the same as build A, so the **rough total is ≈ US$8–15 (unverified)**. B saves about US$1.12 but triples the sleep current and leaves no spare I/O. **I recommend A.** The saving is small next to the value of one load (section 7b), and A's on-chip RTC removes one more part that could fail.

**The cost point that matters:** the LCSC-verified electronics come to about **$3–4.40**. The costs that are hard to pin down are the **LCD glass, the enclosure and the cells**, all outside LCSC, and none of them could be verified this turn.

---

## 7. Evidence: scale of the problem, and its limits

- **Freezing is common, not rare.** A 2007 PATH systematic review found that "between 14% and 35% of refrigerators or transport shipments were found to have exposed vaccine to freezing temperatures". In studies that examined all segments of distribution, "between 75% and 100% of the vaccine shipments were exposed", and "more rigorous study designs were associated with higher levels of freeze exposure" [S8].
- **It was still common a decade later.** A 2017 UNICEF-authored update reviewing 45 studies found exposure below recommended ranges in **33% (wealthier) and 37.1% (lower-income)** of storage evaluations. For shipments, it occurred in **38%** of studies from higher-income countries and **19.3%** from lower-income countries. The review also noted "the lack of knowledge of health workers regarding freeze damage" [S7]. *Limit:* these figures pool heterogeneous studies, and a search excerpt of the full text (not opened by me) says most studies defined "too cold" as ≤ 2 °C, not ≤ −0.5 °C (**unverified**). So they measure **exposure below the range**, not proven loss of potency.
- **Wastage.** WHO's *Monitoring vaccine wastage at country level* (WHO/V&B/03.18 Rev.1) states that WHO "reports over 50% vaccine wastage around the world" [S9]. *Limit:* that figure **includes open-vial wastage** (multi-dose vials discarded after sessions), not only cold-chain failure. WHO's later concept note separates closed-vial wastage, "due primarily to ineffective temperature control, temperature monitoring and stock management during storage and transportation", and sets an EVM limit of **1% per storage facility** [S10]. The cold-chain share of total wastage is therefore smaller than 50%, and I did not find a single verified global number for it (**unverified**).
- **Equipment and management gaps.** A WHO/UNICEF joint statement reported that in 2014 **only 14%** of low- and lower-middle-income countries met the WHO criteria for temperature control in the cold chain. **20%** of health facilities in those countries had no cold chain equipment, and of the equipped facilities **only 2%** had a functional cold chain using optimal technologies [S11]. *Limit:* these are 2014 figures. Gavi's cold-chain equipment platform has changed things since then, and I did not fetch a newer figure (**unverified**).
- **Why the last mile specifically.** WHO's own transport guidance says that without temperature monitoring devices "it is impossible to detect freeze damage" in transport, and that VVMs do not record freezing [S5]. Frozen ice-packs drive containers below 0 °C for hours, and correct conditioning is "a challenge" in practice [S6]. The last mile is exactly where packing is done by hand, often in a hurry, by staff with little training on freeze damage [S7].
- **The honest limit of a device.** A logger detects. It does not prevent. The 2017 review concludes that it is "important to address this issue by educating vaccinators and cold chain staff" [S7]. Without an SOP for what to do with an ALARM, this device only produces worry.

---

## 7b. Ethics

- **False reassurance is the main harm.** A wrong "OK" on a frozen load means children get a vaccine that may not protect them, and nobody knows. A wrong "ALARM" wastes good vaccine and teaches staff to ignore the device. Both matter, but the first is worse and invisible. The design leans against false OK in four ways: a sensor with 5× margin over PQS accuracy at the sensor die, before enclosure and placement error (section 3); freeze counting that is never paused by a start delay (4.7 step 2); every fault, reset, battery swap or unknown state latched as "?", never OK (4.7 step 5); and alarms that cannot be cleared by the user, as PQS requires [S3]. It still uses **the same thresholds as the certified devices**, so its ALARM means the same thing the SOP already describes. A home-made, stricter threshold would make the two devices disagree on the same load and confuse the person deciding.
- **Placement can still fool it.** A perfect sensor placed in the warm lid reports OK while vials against an unconditioned ice-pack freeze. Training on placement is part of the device.
- **The danger of a cheap logger being trusted over certified equipment.** A cheap device with a friendly display can become *the* monitor because it is the one people understand, while the PQS freeze indicator in the same box is ignored. WHO already warns that 30 DTRs and freeze indicators "should NEVER be used to replace" a proper monitoring system in larger stores [S5]. The same humility applies here, one level down. This device must **never overrule** a triggered certified freeze indicator, a VVM past its discard point, or a shake-test result. When it disagrees with them, the conservative reading wins. Its printed label says so.
- **What it does NOT replace.** It is **not a PQS-prequalified monitor** and has no traceable calibration certificate. It **does not replace** the VVM on each vial, certified electronic freeze indicators, 30 DTRs, PQS cold boxes and vaccine carriers, correct ice-pack conditioning, the shake test, or national SOPs and supervision [S5][S6]. It does not make vaccines safe. It only records whether they may not be.
- **Data ownership.** The log belongs to the **clinic** that holds the device. There is no radio, account, cloud, telemetry or vendor key, and nothing is uploaded. Data leaves only by a cable the clinic plugs in, to a computer the clinic chooses. There is a real tension here: national programmes **need** excursion reports. WHO KPIs include "the number of freeze indicators triggered by low-temperature exposure in cold boxes and/or vaccine carriers" [S5]. The design supports that reporting through the existing paper forms without forcing it. Logs could expose individual staff to blame, so the reporting culture matters as much as the hardware. That is a programme question, not a firmware one.
- **Affordability against what is protected.** UNICEF's PCV price data for AMC/Gavi-eligible countries lists PCV13 at about **US$3.30 per dose** in recent years [S12]. I read that from a price table whose column layout is hard to parse, so treat the exact year mapping as approximate. PCV is an aluminium-adjuvanted vaccine, the class the shake test covers [S5]. As an illustration (my arithmetic), a carrier holding 100 doses of a ~$3 vaccine protects about $300 of vaccine, before counting the cost of a child left unprotected. A device costing $9–16 that is reused across many trips is cheap by that measure. It is still not free for a district that has to buy hundreds, and cost should never become the reason to drop a certified indicator in favour of this one.
- **Repairability and e-waste.** Replaceable cells and a single-sided, hand-solderable layout (TSSOP and SOIC parts, apart from the SSOP-48 LCD driver) let a district technician repair it. This is one reason I accepted the PQS non-replaceable-battery deviation (section 5.2).

---

## 8. Licence and citation check, and open questions

**Licences.** No reference design, schematic or open-source firmware project was used or copied in this study. The circuit is a plain combination of datasheet application circuits, and the firmware would be written from scratch. If vendor support files are used later: ST's **STM32L0 HAL driver is BSD-3-Clause** [S29] and ST's **CMSIS device headers for L0 are Apache-2.0** [S30]. Both are permissive and fine for an open design, provided their notices are kept. I would use only the register-definition headers. Puya's PY32 SDK licence is **unverified** (not checked this turn). The WHO PQS documents are cited, not reproduced. The pictograms on the label must be drawn fresh, not copied from PQS figures or commercial devices.

**Open questions and field-confirmation needs:**
1. **Enclosure T90 and placement error.** Bench-test against a calibrated reference in a real PQS vaccine carrier with conditioned and unconditioned ice-packs. Does the logger see the freeze the vials see, and within 10–20 min [S1][S2]?
2. **LCD glass.** Supplier, cost, minimum order quantity and the response time of a wide-temperature TN fluid near 0 °C are all unverified. If there is no affordable glass, the LED + buzzer + UART version must stand on its own.
3. **Should it flash at all?** PQS shipping indicators ban flashing so the display can be photocopied [S2]. My 30-second ALARM blink helps in a dark store but conflicts with that rule. This needs field input.
4. **Pause period length** (TR06 requires one [S1]). Too long hides a real excursion; too short counts the reader's hand. This needs testing.
5. **CR2032 at 0 °C and below.** The cold internal resistance and capacity were unverified because the Energizer datasheet has no cold data [S25]. This matters if anyone wants a coin-cell variant.
6. **Build B pin map and crystal** (7–9 pF part on LCSC) are unverified.
7. **Manufacturer lifecycle status.** Confirmed Active or ACTIVE on the manufacturer page or DigiKey for STM32L011F4P6TR, TMP117, TPS7A02, AT24C256C-SSHL-T, and the rejected TMP112, TMP1075 and TMP102. All other named parts remain marked **verify lifecycle with manufacturer**.
8. **Health-worker testing.** Do users read tick / ? / cross correctly, including colour-blind users and people with low literacy? Does "?" get treated as an alarm in practice, or ignored?
9. **Programme fit.** Would a national EPI programme accept a non-PQS supplementary device at all, or would its presence complicate audits? This needs to be asked, not assumed.
10. **Cold-chain share of wastage.** I found no verified global figure separating closed-vial (cold-chain) wastage from total wastage.

---

## Sources

All accessed 2026-11-18. PDFs were fetched and read as text. [S7] and [S8] were read as PubMed abstracts through NCBI E-utilities.

- [S1] WHO PQS E006/TR06.4, *30-day electronic refrigerator temperature logger* (rev. Jan 2022). https://extranet.who.int/prequal/sites/default/files/document_files/PQS_E006_TR06.4.pdf
- [S2] WHO PQS E006/TR07.3, *Electronic shipping indicators* (16 Oct 2014). https://extranet.who.int/prequal/sites/default/files/document_files/WHO-PQS-E006-TR07%203-final_0.pdf
- [S3] WHO PQS E06/IN03.1, *Irreversible freeze indicator*. https://extranet.who.int/prequal/sites/default/files/document_files/PQS_E006_IN03.1.pdf
- [S4] WHO Prequalification, *E006: Temperature monitoring devices* category page. https://extranet.who.int/prequal/immunization-devices/e006-temperature-monitoring-devices
- [S5] WHO Vaccine Management Handbook, Module VMH-E2, *How to monitor temperatures in the vaccine supply chain* (WHO/IVB/15.04). https://iris.who.int/server/api/core/bitstreams/87e73054-8634-4c32-b7a4-783ef105be19/content
- [S6] WHO Vaccine Management Handbook, Module VMH-E7-02.1, *How to use passive containers and coolant-packs for vaccine transport and outreach operations* (WHO/IVB/15.03, July 2015). https://iris.who.int/server/api/core/bitstreams/ddf20565-7a3f-4f43-877e-87151fc9a73b/content
- [S7] Hanson CM et al., "Is freezing in the vaccine cold chain an ongoing issue? A literature review", *Vaccine* 2017;35(17):2127–2133, doi:10.1016/j.vaccine.2016.09.070, PMID 28364920. https://pubmed.ncbi.nlm.nih.gov/28364920/
- [S8] Matthias DM et al., "Freezing temperatures in the vaccine cold chain: a systematic literature review", *Vaccine* 2007;25(20):3980–6, doi:10.1016/j.vaccine.2007.02.052, PMID 17382434. https://pubmed.ncbi.nlm.nih.gov/17382434/
- [S9] WHO, *Monitoring vaccine wastage at country level* (WHO/V&B/03.18 Rev.1). https://iris.who.int/server/api/core/bitstreams/bf88e08d-273b-48df-93b7-a1d7cb9ac02a/content
- [S10] WHO, *WHO initiative for better planning and forecasting of vaccine supply needs* (wastage-concept revision note). https://www.who.int/docs/default-source/immunization/tools/revising-wastage-concept-note.pdf
- [S11] WHO/UNICEF joint statement, *Achieving immunization targets with the comprehensive effective vaccine management (EVM) framework*. https://iris.who.int/server/api/core/bitstreams/ea5bb0ba-573a-44ce-be18-f6edb3dad743/content
- [S12] UNICEF Supply Division, *PCV vaccine prices* (17 Jun 2026). https://www.unicef.org/supply/media/25651/file/PCV-vaccine-prices-17062026.pdf
- [S13] Texas Instruments, *TMP117 high-accuracy, low-power digital temperature sensor* datasheet. https://www.ti.com/lit/ds/symlink/tmp117.pdf
- [S14] Microchip, *MCP9808 ±0.5°C maximum accuracy digital temperature sensor* datasheet (DS25095A). https://ww1.microchip.com/downloads/en/DeviceDoc/25095A.pdf
- [S15] Texas Instruments, *TMP112x* datasheet. https://www.ti.com/lit/ds/symlink/tmp112.pdf
- [S16] Texas Instruments, *TMP1075* datasheet. https://www.ti.com/lit/ds/symlink/tmp1075.pdf
- [S17] Texas Instruments, *TMP102* datasheet. https://www.ti.com/lit/ds/symlink/tmp102.pdf
- [S18] STMicroelectronics, *STM32L011x3/x4* datasheet (DocID027973 Rev 5). https://www.st.com/resource/en/datasheet/stm32l011f4.pdf
- [S19] BM8563 RTC datasheet (LCSC copy for C269877). https://datasheet.lcsc.com/datasheet/pdf/17751eb9d33a03cc13269b3bcdbd2a70.pdf?productCode=C269877
- [S20] Microchip AT24C256C datasheet (LCSC copy for C6482). https://datasheet.lcsc.com/datasheet/pdf/d17b1e5acedd417cab5266434df57260.pdf?productCode=C6482
- [S21] Holtek, *HT1621 RAM mapping 32×4 LCD controller* datasheet v3.40. https://www.holtek.com.cn/webapi/116706/HT1621_21Gv340.pdf
- [S22] HS (Hansheng) HS91L02W2C01 0.91" OLED datasheet (LCSC copy for C5248081). https://datasheet.lcsc.com/datasheet/pdf/8953314a04a56b0774ba6ab8fe698503.pdf?productCode=C5248081
- [S23] Texas Instruments, *TPS7A02 nanopower IQ 25-nA LDO* datasheet. https://www.ti.com/lit/ds/symlink/tps7a02.pdf
- [S24] Epson FC-135 32.768 kHz crystal datasheet (LCSC copy for C32346). https://datasheet.lcsc.com/datasheet/pdf/d4d1bd2e87b2e72a8e8e7fe7d590f4c0.pdf?productCode=C32346
- [S25] Energizer, *CR2032* product datasheet (Form 2032NA0618). https://energizer.com/wp-content/uploads/2025/01/cr2032.pdf
- [S26] Energizer, *Cylindrical Primary Lithium (L91/L92) handbook and application manual*. https://energizer.com/wp-content/uploads/2024/05/lithiuml91l92_appman.pdf
- [S27] (not used: Energizer L91 datasheet seen only in search results, not opened)
- [S28] Puya PY32F002B Stop-mode current (1.70 µA) carried from our earlier study `ethics-grain-storage-monitor.md` [its S28]; not re-fetched this turn. Supply, flash and I/O figures from LCSC product-detail parameters for C7469097.
- [S29] STMicroelectronics `stm32l0xx-hal-driver` LICENSE.md (BSD-3-Clause). https://github.com/STMicroelectronics/stm32l0xx-hal-driver/blob/master/LICENSE.md
- [S30] STMicroelectronics `cmsis-device-l0` LICENSE.md (Apache-2.0). https://github.com/STMicroelectronics/cmsis-device-l0
- [S31] JLCPCB public parts search (`selectSmtComponentList`), used to find candidate C-codes and the STS40/STS30 listings.
- LCSC product-detail data for every `C…` code quoted in section 4 (`wmsc.lcsc.com/ftps/wm/product/detail?productCode=…`), read 2026-11-18. Lifecycle labels are the distributor's.

## Revisions (18 November 2026, round 7/8 audit)

- Renamed the consolidated "Active?" column to "LCSC label" and added a manufacturer or DigiKey status column. Lifecycle is stated as confirmed only for STM32L011F4P6TR, TMP117, TPS7A02, AT24C256C-SSHL-T, TMP112, TMP1075 and TMP102. All other named parts are marked "verify lifecycle with manufacturer".
- Added a safety and regulatory paragraph on lithium-cell transport, mixing of cells, end-of-life disposal, cleaning among vials, and whether a supplementary device falls under medical-device or health-programme rules, all marked unverified until sourced.
- Qualified the "5× better than PQS" wording so that it applies at the sensor die, before enclosure and placement error.
- Updated the Stop-mode current from 0.54 µA to ST's published 0.67 µA for Stop + RTC + 2 KB RAM retention. The typical average is now about 1.3 µA, or about 0.95 mAh per 30 days.
- The Summary already listed all three heat alarms (the 30 DTR +8 °C / 10 hour alarm and the TR07 +30 °C and +45 °C transport alarms), so that item needed no change.

