# Field Moisture Light: design study for a sub-US$5 soil-moisture and rain indicator for smallholder farmers, readable with no smartphone

Ethical case study (food insecurity). Research and design writing only: no firmware, no builds.
Author role: assembly / firmware engineer. I would write the firmware in C and assembly later; this document contains **no code**.
All prices, stock figures and lifecycle flags were read from LCSC on 2026-11-18 (UTC+7) and will change.

---

## Summary

- **What it is:** a stake that a farmer pushes into the root zone. The top holds one button and three small lights. Press the button and the lights answer one question: *does this soil need water now?* Red means dry, yellow means getting dry, green means enough water. A second short pattern says whether rain has fallen on the device in the last day. There is no app, no radio, no account, and nothing leaves the device.
- **Sensing:** a capacitive probe made from the PCB's own copper, sealed under solder mask and epoxy. No bare metal touches the soil, so it does not corrode the way cheap two-prong resistive probes do [S1][S2]. A smaller capacitive pad on the top face senses rain the same way.
- **Recommended MCU:** Puya PY32F002BL15S7TU (LCSC C49424671), lifecycle flag `normal`, 3,135 in stock, US$0.1573 at qty 5 and $0.124 at qty 50. Its datasheet gives a typical 1.70 µA in Stop mode with the low-power timer running [S10], which is what makes a coin cell last for years.
- **Power:** one CR2032 coin cell (235 mAh typical [S12]). Estimated average draw is about 3.5 µA with 10 button presses a day. That gives a paper estimate of 7 years, but heat, cell ageing and leakage make **2–5 years** the honest planning figure (section 4). Two AA cells or solar plus supercapacitor are covered as alternatives.
- **Cost:** about **US$0.42** in LCSC-verified electronics at qty 50, about **$0.92** with the PCB/probe (PCB estimated), and roughly **US$1.70–3.20 per unit** with cell, enclosure and sealing before assembly labour. The non-LCSC lines are estimates, marked unverified.
- **Evidence is encouraging but uneven.** The rigorous meta-analyses of digital farm advice find small average gains: about +4% yield [S15], or +6% yield and +6% income [S16]. Sensor-specific studies report large gains (Malawi: yield up more than 80%, gross margins more than tripled [S17]), but those come from case studies and demonstrations, not randomised trials. For rice, mild alternate wetting and drying saves about 23% of water with no significant yield loss in most cases. Severe drying cuts yield by 22.6% [S19]. The device has to steer farmers away from the severe case.
- **The honest limit:** this tool only helps farmers who can choose when to irrigate. Many of the most food-insecure smallholders are rainfed. For them the useful question is narrower, such as "is there enough moisture to sow?" Field work must confirm that need first (section 8).
- **Ethics:** a standalone offline device means the farmer owns the reading. The risks arrive later, if a vendor adds connectivity, accounts or proprietary calibration. I set out design rules to keep it farmer-controlled (section 7).

---

## 1. Purpose and users

**Who:** smallholder farmers with plots small enough to water by hand, bucket, hose, small pump or canal turn. Extension workers and farmer groups are secondary users. They help set it up and teach the colour meanings. The VIA "Chameleon" programme shows the model works: in Malawi, farmers were given sensors that "showed whether the soil was wet, moist or dry using colours, making it easy for farmers to understand" [S17].

**What decision it informs:**
1. **Irrigate now, or wait?** This is the main one. Both overwatering and underwatering cost the farmer. A Tanzanian farmer quoted by VIA says the tools taught him "I was choking the crops with water", which washed away fertiliser [S18]. Overwatering also costs pump fuel or electricity and the farmer's labour.
2. **Did it rain enough here?** Rain is patchy. A rain flag combined with the soil reading tells the farmer whether last night's shower actually reached the roots, or only wet the surface.
3. **(Rainfed variant) Is there enough moisture to sow?** This needs field confirmation (section 8).

**What it does not do:** it **cannot forecast rain**. It has no radio and no weather data. It reports only what has already happened in the soil and on its own rain pad. The label and training must say this plainly, so nobody waits for a "rain light" that predicts nothing.

**How a farmer with no smartphone reads it (tied to the electronics):**

| Farmer action | What they see | What the electronics are doing |
|---|---|---|
| Press the button | Red, yellow or green lights for about 3 s | The button pulls a GPIO low and wakes the MCU from Stop mode through an external interrupt. The MCU takes a fresh capacitance reading and lights one LED through a current-limiting resistor. The LEDs are off the rest of the time, which is how a coin cell lasts years. |
| Keep watching after the colour | Yellow double-blink = rain detected on the pad in the last ~24 h | The MCU has checked the rain pad every few minutes in the background and keeps a single "rain seen" flag in RAM. It holds no history and no timestamps. |
| Walk past without pressing (optional mode) | A slow red blink every ~10 s means "dry: water me" | A brief, low-current LED pulse. It costs about 6 µA on average (section 4), so it is a configuration choice, not the default. |
| Visually impaired user (optional part) | 1, 2 or 3 beeps | A passive magnetic buzzer (MLT-8530) driven by a timer PWM output. It adds about $0.15. |
| Low battery | All three LEDs flash together on a button press | The MCU measures its own supply against an internal reference channel [S9]. Below about 2.6 V it warns the farmer before the green LED fades out (see LED note in section 3). |

The label is pictograms (dry cracked soil / damp / water drop / rain cloud) in the local language, so it does not depend on literacy. **Colour-blind users:** red and green are hard to tell apart for many people. The position of each LED (left, middle, right, like a traffic light) and the blink count carry the same meaning as the colour.

---

## 2. Sensing approach

### 2.1 Why not the cheap two-prong resistive probe

The cheapest probes are two bare metal prongs. They pass DC current through the soil and measure resistance. That fails in the field for three electrical reasons:
- **Electrolysis and corrosion.** DC through wet soil is an electrochemical cell. The anode prong dissolves and the surface chemistry changes, so readings drift until the probe is useless. A hobbyist reference notes they "start to rust, even if they're gold-plated", and the drift forces constant re-tuning [S1].
- **They measure salt as much as water.** Conductivity depends on dissolved ions, so fertiliser, salinity and soil type move the reading without any change in water content.
- **Contact dependence.** They "don't work that well in loose soil" [S1], because the reading depends on how well the soil touches the metal.

### 2.2 Capacitive sensing, from the PCB itself

A capacitive probe has **no exposed metal**. Two interleaved (interdigitated) copper electrodes on the PCB form a capacitor. The soil around the board is part of its dielectric. Water has a relative permittivity of about 80, dry soil about 2–6 and air 1 [S3][S4]. Adding water to soil therefore raises the capacitance a lot. That is the signal.

- **Construction:** draw the electrodes on both faces of the board and join each pair with vias. That maximises the fringe field into the soil. Then cover all copper with solder mask, which electrically insulates it and prevents corrosion and oxidation [S5]. A 2026 design study used a 1 mm finger gap on FR-4 for manufacturing margin and noted that 10 small PCBs cost about $5, i.e. about $0.50 per sensor board [S5]. Lastminuteengineers also recommends epoxy on the cut PCB edges, because bare FR-4 edges absorb moisture over time [S1].
- **Measurement with almost no extra parts:** charge the electrode through a known large resistor and time how long the voltage takes to cross a threshold. Wetter soil means higher capacitance, which means a longer RC time. The Southampton printed sensor used exactly this: a 1 MΩ series resistor, two comparator thresholds and a timer, repeated many cycles to average out noise [S3]. The PY32F002B has two comparators, a 12-bit ADC and 16-bit timers on chip [S9], so the "sensor electronics" are one resistor and copper. That replaces the 555 timer and regulator used in the common DFRobot-style module [S1][S6].
- **Electronics intuition:** with R = 1 MΩ, the Southampton team measured C ≈ 8.6 pF in air for the soil-facing capacitance. They note it multiplies by about 80 in water [S3]. τ = RC goes from about 9 µs (air) to hundreds of µs (wet). That is easy to time with a 24 MHz counter and gives good resolution without an analog front end. Their design added an op-amp buffer to cut lead parasitics [S3]. Here the electrodes sit on the same board as the MCU with millimetre traces, so I expect the buffer can be left out. This is unverified until a prototype is measured.

### 2.3 Calibration and drift (the real weak spot)

Capacitive probes avoid corrosion, but they do **not** give an absolute moisture percentage out of the box:
- A 2025 calibration study of the SEN0193 in loamy silt reached R² 0.85–0.87 and RMSE 4.5–4.9% after soil-specific calibration. Sensor-to-sensor variation was 10–16% CV above 30% moisture [S7].
- A 2026 study found the manufacturer's air/water calibration "unsuitable for soils". Calibration must be done dry and saturated in the actual medium, and salinity and fine texture degrade performance. The authors still concluded the sensors track moisture **trends** well enough for irrigation management [S8].
- A 2019 study obtained RMSE of 0.05 cm³/cm³ in the field-capacity range after soil-specific calibration [S6].
- The Southampton PCB sensor showed a flat, insensitive band between 15% and 25% VWC in clay loam, cause unknown [S3]. A homemade PCB probe geometry therefore has to be tested in real soil before anyone trusts its thresholds.

**Design consequence:** the device will not pretend to show a percentage. It shows **three bands relative to this field**, set by the farmer (section 7.3). Temperature also affects the reading [S8 reports texture/salinity; temperature effects are reported for SEN0193 in a thesis I only saw as a search result, **unverified**]. The firmware could average over the day or compare against the on-chip temperature sensor [S9], to be confirmed on a prototype.

### 2.4 Rain detection

- The common hobby rain module is an **exposed copper comb**. Water bridges the traces, so it is a resistive sensor. Even the tutorial warns that keeping it powered makes the surface "corrode faster", and the electronics are not waterproof [S2]. **Rejected** for a multi-year device.
- **Chosen approach:** a second interdigitated capacitive pad on the **top face** of the same PCB, under solder mask, tilted so water drains (the tutorial recommends a ~20° tilt for rain pads [S2]). Droplets raise its capacitance. It is read the same way as the soil probe and needs one resistor and one pin. Dew and splashing will give false "rain" flags. That is acceptable, because the flag only tells the farmer to check the soil colour, and the soil reading is the real decision signal. Dew sensitivity needs field testing.
- **What I did not choose:** a tipping-bucket rain gauge (moving parts, a magnet and reed switch, more cost) and any forecast feature (needs a radio, which breaks the privacy and offline design).

---

## 3. Parts: cheapest active LCSC parts

**How I checked lifecycle.** I read each part from LCSC's own product-detail data (`https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=<code>`) on 2026-11-18. That data includes LCSC's lifecycle field `productCycle`, live stock and the price ladder. I accept a part only if it shows `productCycle: "normal"` **and** non-zero stock. "Normal" is **the distributor's label, not a manufacturer lifecycle statement**. Before production, each maker should be checked for an EOL/NRND notice.

Two cross-checks showed why this matters:
- Everlight 19-217/GHC-YR1S2/3T green 0603 LED (C72043) shows `productCycle: "stop_product"` with 0 stock. **Rejected.**
- Everlight 19-213/Y2C yellow 0603 LED (C72038) shows `normal` but **0 stock**. **Rejected** under the stock rule.

### 3.1 MCU candidates

| Candidate | LCSC code | LCSC lifecycle / stock | Price | Low-power figure | Verdict |
|---|---|---|---|---|---|
| **Puya PY32F002BL15S7TU** (SOP-8, Cortex-M0+, 24 KB flash, 3 KB SRAM, 1.7–5.5 V, −40…105 °C) | C49424671 | normal / 3,135 | $0.1573 @5, $0.124 @50, $0.1098 @200 | Stop mode with LPR regulator, LSI on, LPTIM running: **1.70 µA typ** (datasheet table 5-9; characterisation data, not production-tested) [S10]. Run at 24 MHz: about 1.1 mA [S10]. | **Recommended.** Cheapest, lowest sleep current found, and has on-chip comparators and ADC [S9]. |
| Puya PY32F002AL15S6TU (SOP-8, 20 KB flash, −40…85 °C) | C5292060 | normal / 11,805 | $0.1997 @5, $0.1555 @50, $0.132 @200 | Has Stop mode with low-power regulator and LPTIM wake-up [S11]. **I did not extract its Stop current figure: unverified.** | Second source with deeper stock. |
| WCH CH32V003J4M6 (SOP-8, RISC-V, 16 KB flash, 48 MHz) | C5346354 | normal / 29,910 | $0.2702 @5, $0.2142 @50, $0.1902 @150 | Standby **9.1 µA typ** at 3.3 V with LSI on, 7.6 µA with LSI off [S13] | Works and has good stock, but roughly 5× the sleep current, which costs years of coin-cell life (section 4). |
| (pin-count fallback) Puya PY32F002AF15P6TU (TSSOP-20) | C5292059 | normal / 47,640 | $0.215 @5, $0.1675 @50 | as PY32F002A | Use if the SOP-8 pin plan below proves too tight. |

**Pin plan on SOP-8 (6 usable I/O [S9]), a sketch to confirm against the pinout, unverified:** (1) common charge drive for both pads, (2) soil sense into a comparator, (3) rain sense, (4)+(5) LEDs, (6) button / wake. The SWD debug pins would be shared with LED or button pins. Fitting three LEDs plus an optional buzzer on two or three pins means charlieplexing or anti-parallel LED pairs. That is fine in firmware but must be checked against each pin's alternate functions. If it does not fit cleanly, move to the TSSOP-20 fallback for +$0.04.

**Firmware note:** I would write the firmware: Stop-mode sleep with LPTIM wake, the comparator-timer capacitance measurement, debounced button wake, LED/buzzer patterns, and the farmer calibration sequence. As agreed, **no code is written here**. The firmware must never read the MCU's factory unique ID (the PY32F002B datasheet lists a UID [S9]), and readout protection should be enabled so a vendor cannot quietly swap in a different build.

### 3.2 Indicators, input, power hardware

| Part | LCSC code | Lifecycle / stock | Price | Role / note |
|---|---|---|---|---|
| NATIONSTAR NCD0805R1 red LED, Vf 1.6–2.6 V | C84256 | normal / 1,946,850 | $0.0136 @50 | "Dry" light |
| Hubei KENTO KT-0805Y yellow LED, Vf 1.8–2.4 V | C2296 | normal / 242,800 | $0.0125 @50 | "Getting dry" + rain blink |
| Hubei KENTO KT-0805G green LED, Vf 2.6–3.1 V | C2297 | normal / 2,206,300 | $0.0166 @50 | "Enough water" |
| (cheaper red alt.) KT-0603R, Vf 2.4 V | C2286 | normal / 6,093,500 | $0.0077 @100 | Optional cost-down |
| XKB TS-1187A-B-A-B tactile switch, 5.1 mm SMD | C318884 | normal / 471,800 | $0.0216 @20, $0.017 @200 | Press-to-read button. Must sit under a sealed flexible membrane (section 5). |
| Huaneng MLT-8530 passive magnetic buzzer, 2.5–4.5 V, 2.7 kHz | C94599 | normal / 108,145 | $0.1987 @5, $0.1548 @50 | **Optional**, for visually impaired users. Drive current not checked: **unverified**. |
| MYOUNG BS-01-A4CJ004 CR2032 holder, through-hole | C964743 | normal / **330** | $0.2839 @5, $0.212 @50 | Coin-cell holder (low stock, see alternative) |
| MYOUNG BS-08-B2AA001 CR2032 holder, SMT | C964777 | normal / 3,500 | $0.3601 @5, $0.2852 @50 | Second source |
| UNI-ROYAL 0603 1 MΩ ±1% | C22935 | normal / 2,056,500 | $0.0018 @100 | Charge resistor, soil and rain pads (×2) |
| UNI-ROYAL 0603 330 Ω ±1% | C23138 | normal / 98,900 | $0.0017 @100 | LED current limit (×3) |
| UNI-ROYAL 0603 10 kΩ ±1% | C25804 | normal / 2,437,200 | $0.002 @100 | Button pull-up / reset |
| YAGEO 0603 100 nF X7R | C14663 | normal / 999,700 | $0.0056 @100 | Decoupling (×2) |
| MYOUNG BH-AA-B6AA034 2×AA holder | C51883822 | normal / **49** | $1.0521 @1 | Only for the AA variant. Expensive, and stock is thin. |

**LED electronics note (coin cell):** a CR2032 starts near 3.0 V and is specified to 2.0 V [S12]. The green LED needs up to 3.1 V forward voltage, so it will dim and then fail as the cell ages. Red (1.6–2.6 V) and yellow (1.8–2.4 V) keep working much longer. That is why the **"needs water" signals are red and yellow**, the ones that survive longest. The low-battery warning (section 1) fires before green fails. A coin cell also has high internal resistance, so a big LED current pulse makes the voltage sag. At about 3 mA the 0805 LEDs are visible in shade. Visibility in **direct tropical sun is unverified**, and a small hood or recessed window may be needed.

---

## 4. Power

### 4.1 Coin cell (recommended baseline)

Energizer CR2032: 235 mAh typical to 2.0 V (15 kΩ load, 21 °C), self-discharge about 1%/year, **operating temperature −30 to 60 °C** [S12].

Estimated average current, PY32F002B (my arithmetic from datasheet typicals [S10]):

| Activity | Assumption | Average current |
|---|---|---|
| Stop mode with LPTIM wake timer | 1.70 µA typ [S10] | 1.70 µA |
| Soil + rain measurement | wake every 10 min (144×/day), about 10 ms at about 1.1 mA [S10] each | about 0.02 µA |
| LED display on button press | 10 presses/day × 3 s × 5 mA (LED + MCU awake) | about 1.74 µA |
| **Total (default)** | | **about 3.5 µA** |
| Optional "dry beacon" | 3 mA × 20 ms every 10 s while dry | +6 µA while active |

- Paper life: 235 mAh ÷ 3.5 µA ≈ 67,000 h ≈ **7.7 years**.
- **Planning figure: 2–5 years**, because: (a) the 1.70 µA is a typical room-temperature figure, not production-tested [S10], and leakage rises sharply with heat; (b) capacity falls at temperature extremes and with age; (c) PCB leakage in humid air and any extra button presses add up. With the beacon always on (about 9.5 µA) the paper life drops to about 2.8 years.
- **Comparison:** with the CH32V003 at 9.1 µA standby [S13] plus the same display load (about 10.9 µA), paper life is about 2.5 years. MCU sleep current is the single largest lever.
- **Heat is the real threat.** A dark sealed box in tropical sun can exceed the cell's 60 °C limit [S12]. (I have not measured enclosure temperatures; **unverified**, but it should be expected.) Use a white or light enclosure, shade from the stake head and vents above the electronics compartment.
- **Child safety:** the Energizer datasheet warns that a swallowed coin cell can cause serious injury or death in as little as 2 hours, and recommends compartments that need a tool or two independent actions to open [S12]. Farm households have children, so the cell compartment **must be screw-closed**.

### 4.2 Two AA cells

Roughly 10× the capacity, so the electronics would outlast the cells' shelf life, and AA cells can be bought in village shops. Downsides: the verified 2×AA holder is $1.05 with only 49 in stock (C51883822), the unit is bigger, and alkaline cells leak when old or hot. A good choice where coin cells are hard to buy locally. Holder sourcing needs a second option.

### 4.3 Small solar + supercapacitor

Daily charge at 3.5 µA ≈ 0.30 coulomb. A 1 F supercap discharged from 3.3 V to 2.0 V delivers about 1.3 C, which is about 4 days of darkness. Pros: no battery to buy or dispose of. Cons: extra charge-control parts, a panel exposed to dirt and impact, supercap temperature ratings (the 1 F coin types I saw list −25 to 70 °C [S14]), and leakage current that may exceed the load. **I did not verify a supercap or solar cell on LCSC**, so this variant's cost is unverified. Recommended only as a second-phase variant once the coin-cell version is proven.

---

## 5. Enclosure, waterproofing and BOM

**Mechanical concept:**
- **One PCB** (about 20 × 100 mm). The lower 60–70 mm is the interdigitated probe, both faces under solder mask. The upper part carries the MCU, LEDs, button, cell holder and top-face rain pad. One board keeps the cost low and removes the cable, a classic failure point.
- **Sealing:** probe edges and the soil line coated in epoxy or conformal coating [S1]. The head sits in a small light-coloured box or a short PVC pipe section with an end cap. The LEDs show through a clear window. The button works through a flexible silicone membrane or an overmoulded dome. The rain pad faces up through a sealed opening and is tilted for drainage [S2]. Target roughly IP65 equivalent for the head; the probe is fully sealed. This is not tested, so the IP rating is **unverified**.
- **Field realities:** insertion depth changes readings [S1], so a moulded depth-stop collar marks "push to here". Tools, livestock and machinery will break stakes, so they must be cheap enough to replace and simple enough for a local repairer (section 7.4).

**BOM (per unit, qty-50 prices unless noted):**

| Line | Source | Qty | Unit price | Line cost | Verified? |
|---|---|---|---|---|---|
| MCU PY32F002BL15S7TU | LCSC C49424671 | 1 | $0.124 | $0.124 | LCSC data |
| LED red NCD0805R1 | LCSC C84256 | 1 | $0.0136 | $0.0136 | LCSC data |
| LED yellow KT-0805Y | LCSC C2296 | 1 | $0.0125 | $0.0125 | LCSC data |
| LED green KT-0805G | LCSC C2297 | 1 | $0.0166 | $0.0166 | LCSC data |
| Tactile switch TS-1187A | LCSC C318884 | 1 | $0.0216 (@20) | $0.0216 | LCSC data |
| CR2032 holder BS-01-A4CJ004 | LCSC C964743 | 1 | $0.212 | $0.212 | LCSC data |
| Resistors 1 MΩ ×2, 330 Ω ×3, 10 kΩ ×1 | LCSC C22935 / C23138 / C25804 | 6 | @100 ladder | $0.0107 | LCSC data |
| Capacitors 100 nF ×2 | LCSC C14663 | 2 | $0.0056 (@100) | $0.0112 | LCSC data |
| **Subtotal, LCSC-verified electronics** | | | | **≈ $0.42** | |
| (optional) Buzzer MLT-8530 | LCSC C94599 | 1 | $0.1548 | +$0.155 | LCSC data |
| PCB incl. probe, 2-layer, solder mask both sides | PCB fab | 1 | ~$0.50 | $0.50 | **estimate** from [S5] ($5 for 10 boards <100×100 mm), unverified for this board |
| CR2032 cell | local / bulk | 1 | $0.20–0.50 | $0.20–0.50 | **unverified estimate** |
| Enclosure head (box or PVC + cap), window, membrane | local | 1 | $0.50–1.50 | $0.50–1.50 | **unverified estimate** |
| Epoxy / conformal coat | local | – | ~$0.10 | $0.10 | **unverified estimate** |
| **Total per unit (no buzzer), before assembly labour** | | | | **≈ $1.72–3.02** | |
| **Total with buzzer** | | | | **≈ $1.88–3.18** | |

That leaves margin under US$5 for assembly, freight and duty, none of which are estimated here.

---

## 6. Evidence review: does better irrigation timing raise yields and income?

**Short answer:** probably yes for farmers who control their water, when the tool comes with training. But the strongest causal evidence is for *digital advice* in general, and its effects are modest. The sensor-specific evidence shows large effects, but mostly from uncontrolled designs.

| Evidence | Design | Finding | Caveats |
|---|---|---|---|
| Fabregas, Kremer & Schilbach 2019, *Science* [S15] | Review + meta-analyses of experiments on digital agricultural information | Yields **+4%**. Odds of adopting recommended inputs **+22%**. Benefits likely exceed transmission cost "by an order of magnitude". | About advice by phone, not sensors. Modest average effect. The authors stress that ministry messages are often hard for farmers to use. |
| Beach et al. 2025, *Global Food Security* [S16] | Meta-analysis, 20 studies (15 SSA, 4 India, 1 Cambodia) | Fertiliser adoption **+23%** (95% CI +6 to +40%). Yield **+6%** (+2 to +9%). Income **+6%** (+2 to +9%). Improved-seed adoption not significant. | "Considerable variability" across studies. Too few studies to tell which design features matter. Excludes post-2021 studies. |
| Chikankheni et al. 2024, Malawi, *Irrigation and Drainage* [S17] | Case study, 4 irrigation schemes, colour-coded soil-water sensors plus "people-centred learning", 2017–2018 seasons | Yield **up more than 80%**; gross margins **more than tripled** across all schemes | Case study, not a randomised trial. Effects bundle training with tools. A co-author (R. Stirzaker) is, I believe, linked to VIA, the tool's developer; **unverified**, but a possible conflict of interest to check. |
| VIA (developer) impact page [S18] | Developer testimonials | "Not unusual for yields to increase by 30–50%" while using less water | Promotional source; treat as anecdote. |
| Chilundo et al. 2020, Mozambique [S20] | 4-year research-for-development project | Production and income rose with tools **plus** canal repair and market-linkage platforms | Cannot separate the sensor effect from the infrastructure and market changes. |
| Vatta et al. 2018, Indian Punjab, tensiometers in paddy [S21] | Economic assessment versus continuous flooding | Water and power use **−13%**, variable cost **−7%**, **no yield penalty** | The benefit is lower cost, not higher yield. The gain to the farmer depends on paying for power. |
| Mahapatra et al. 2025, Eastern India [S22] | Household survey (1,627) + farmer field **demonstrations** | Soil-moisture-sensor irrigation among interventions with **8–69% water savings** and **2–220% yield increases** | Wide ranges, demonstration plots, not randomised. |
| Carrijo, Lundy & Linquist 2017, AWD meta-analysis [S19] | 56 studies, 528 paired comparisons in rice | AWD overall: yield **−5.4%**. Mild AWD (soil ≥ −20 kPa or water ≥15 cm below surface): no significant yield loss in most cases and **−23.4% water**. Severe AWD: yield **−22.6%**. | Directly relevant for rice-growing Thailand/SE Asia. Losses were worst on soils with pH ≥ 7 or carbon <1%. A sensor that lets fields dry too far does harm. |

**What this means for the design:**
1. Gains come from **tools plus learning**. The Malawi and VIA results rest on group learning [S17][S18]. Shipping devices without training is unlikely to repeat them.
2. For many farmers the main benefit is **saving water, fuel, power and labour** [S21], rather than a yield jump. That is still worth having for food-insecure households, but should not be sold as "doubles your harvest".
3. For **rice**, the "dry" threshold must sit on the safe side of mild AWD [S19], and in practice a field water tube may beat a capacitive stake in flooded paddy. Whether this device suits paddy at all is an open question (section 8).
4. Average effects in rigorous studies are **single-digit percentages** [S15][S16]. Any pilot should run a proper comparison (ideally randomised by farmer group) before scale-up claims are made.

---

## 7. Ethics: data ownership and who profits

### 7.1 The baseline is good: offline means farmer-owned

The device has **no radio, no storage of history, no unique ID read by firmware, no account and no app**. The only "data" is a colour shown to the person standing next to it, and a single rain flag in RAM that is lost when the cell is removed. Nobody can extract, sell or subpoena what does not exist. That is the strongest privacy and ownership guarantee available, and it is cheaper than building a connected device.

### 7.2 The risk: a later "upgrade" that adds connectivity

The documented pattern in digital agriculture is extraction, not empowerment:
- In Ghana, researchers found that "digital platform operators have gained more from these technologies than the farmers they aim to support". Donor projects, accelerators and startups collect extensive farmer data through GPS, drones, surveys and apps, which "risk creating new forms of extractivism and surveillance" [S23].
- The OECD's November 2026 survey found most countries reporting **lock-in** concerns in agriculture, mainly from both technical and data-ownership issues. Only 7 of 33 respondents (21.2%) reported none. **Thailand** reported fragmented standards, proprietary technologies, unclear mandates on data ownership and no formal data-sharing agreements [S24]. The OECD also notes that firms can have incentives to keep systems incompatible in order to strengthen lock-in [S24].

If a vendor or aggregator later adds a radio and an account, farm-level moisture and location data could reveal **which plots are irrigated, yields and wealth**. That is valuable to input sellers, lenders, land buyers and tax or water authorities, and none of it pays the farmer.

### 7.3 Calibration that favours selling more

Whoever sets the thresholds decides when the light turns red. A vendor that sells water, pump electricity, diesel or fertiliser has an incentive to set "dry" early, which means more irrigation and more leaching, so more fertiliser. VIA's own farmer testimony shows overwatering washing fertiliser away [S18]. Mitigations:
- **Farmer-set thresholds.** A button sequence, done with an extension worker the first time, records "this is just-watered" and "this is when plants start to wilt" for this field. The firmware divides the range into bands. No vendor cloud is involved.
- **Published method.** Thresholds and calibration procedure are documented openly, so an NGO or farmer group can audit them.
- **Rice-safe default.** Where used in rice, the default must respect the mild-AWD limit [S19].

### 7.4 Repair, parts and lock-in

The FTC, with the Minnesota and Illinois attorneys general, sued Deere in January 2025, alleging it restricted farmers' and independent shops' ability to repair equipment and drove up repair costs [S25]. (I did not check the case's current status.) This design avoids that pattern:
- **Open hardware** (schematic, PCB files, BOM, firmware source) under a licence that allows local manufacture and repair.
- **Standard parts:** a CR2032 or AA cells sold in village shops, through-hole holder, no proprietary connectors or programming locks beyond readout protection. The firmware image is published so any repairer can re-flash a genuine build.
- **Second sources** for every active part (PY32F002A, CH32V003; two cell holders).

### 7.5 Who profits, and who is left out

- **Benefits go to farmers who control water timing.** Rainfed households, often the most food-insecure, gain little from an irrigation cue. Without care, distribution could favour already better-off irrigators. Programmes should target groups deliberately and test the "moisture to sow" use for rainfed farmers.
- **Rebound risk.** Water saved per hectare can be used to irrigate more land, so total extraction from a shared aquifer may not fall. That is a general concern I have **not** sourced here (**unverified**). It needs local water-governance input where groundwater is shared.
- **Gender and access.** Who in the household receives the device and training affects who benefits. Programmes should record and design for this. This is a design suggestion, not a sourced finding.

### 7.6 Design rules (non-negotiable for this case study)

1. No radio, no GPS, no history log, no unique ID read, no account, no app, in any version distributed to farmers under this design.
2. Thresholds are set by the farmer or their group, and the method is published.
3. Open hardware and firmware, with local repair possible.
4. Any future connected variant is a **different product** that needs fresh, explicit ethical review and farmer-controlled data governance (for example a cooperative), and opt-in only.
5. No claims of yield gains beyond what the evidence supports (section 6).

---

## 8. Open questions and field confirmation needed

1. **Need first:** do target farmers (which country, which crops, irrigated or rainfed) actually lack a timing cue? Would a simpler tool (finger test, field water tube in rice, VIA-style card) do as well? This needs a needs assessment with an agricultural extension partner.
2. **Soil types:** the homemade PCB probe must be calibrated against gravimetric samples in each major local soil. Clay, sand, saline and high-organic soils behave differently [S7][S8], and the Southampton probe showed an insensitive band in clay loam [S3].
3. **Per-field calibration UX:** can farmers reliably set the "just watered" and "wilting" points with a button sequence after one training session? Test with real users, including low-literacy and colour-blind users.
4. **Local crops and thresholds:** rooting depth sets probe length. Rice needs the mild-AWD limit [S19]. Vegetables and maize need crop-specific bands.
5. **Paddy suitability:** in flooded fields the soil probe sees saturation most of the time. A water-level indicator may be the right tool instead.
6. **Heat and sunlight:** measure enclosure temperature in tropical sun against the cell's 60 °C limit [S12], and LED visibility in direct sun.
7. **Rain pad false positives:** dew, splash and bird droppings. Is the rain flag useful or confusing in practice?
8. **Prototype measurements:** real Stop current on the bench, capacitance range of the chosen electrode geometry, and whether an op-amp buffer is needed [S3].
9. **Pin plan:** confirm the SOP-8 pin assignment against the PY32F002B pinout, or move to TSSOP-20.
10. **Supply:** the through-hole CR2032 holder has only 330 in stock and the 2×AA holder 49. Qualify second sources. Confirm with each manufacturer that no EOL/NRND notice exists, since LCSC's "normal" flag is necessary but not sufficient.
11. **Conflict of interest:** confirm the developer affiliation of authors in the Malawi study [S17].
12. **Evaluation design:** pilot with a comparison group (ideally randomised by farmer group), measuring water, labour, yield and income, before any scale-up.

---

## Parts table (consolidated)

| Part | LCSC code | Active? (LCSC `productCycle`) | Price (smallest ladder / ~50) | Stock | Role |
|---|---|---|---|---|---|
| PY32F002BL15S7TU | C49424671 | normal | $0.1573 @5 / $0.124 @50 | 3,135 | **MCU (recommended)** |
| PY32F002AL15S6TU | C5292060 | normal | $0.1997 @5 / $0.1555 @50 | 11,805 | MCU second source |
| CH32V003J4M6 | C5346354 | normal | $0.2702 @5 / $0.2142 @50 | 29,910 | MCU alternative (higher sleep current) |
| PY32F002AF15P6TU | C5292059 | normal | $0.215 @5 / $0.1675 @50 | 47,640 | MCU, more pins |
| NCD0805R1 (red LED) | C84256 | normal | $0.0136 @50 | 1,946,850 | Dry indicator |
| KT-0805Y (yellow LED) | C2296 | normal | $0.0125 @50 | 242,800 | Getting dry / rain blink |
| KT-0805G (green LED) | C2297 | normal | $0.0166 @50 | 2,206,300 | Enough water |
| KT-0603R (red LED) | C2286 | normal | $0.0077 @100 | 6,093,500 | Cost-down alt. |
| TS-1187A-B-A-B | C318884 | normal | $0.0216 @20 | 471,800 | Button |
| MLT-8530 | C94599 | normal | $0.1987 @5 / $0.1548 @50 | 108,145 | Optional buzzer |
| BS-01-A4CJ004 | C964743 | normal | $0.2839 @5 / $0.212 @50 | 330 | CR2032 holder (THT) |
| BS-08-B2AA001 | C964777 | normal | $0.3601 @5 / $0.2852 @50 | 3,500 | CR2032 holder (SMT) |
| BH-AA-B6AA034 | C51883822 | normal | $1.0521 @1 | 49 | 2×AA holder (variant) |
| 0603WAF1004T5E 1 MΩ | C22935 | normal | $0.0018 @100 | 2,056,500 | Charge resistor |
| 0603WAF3300T5E 330 Ω | C23138 | normal | $0.0017 @100 | 98,900 | LED resistor |
| 0603WAF1002T5E 10 kΩ | C25804 | normal | $0.002 @100 | 2,437,200 | Pull-up |
| CC0603KRX7R9BB104 100 nF | C14663 | normal | $0.0056 @100 | 999,700 | Decoupling |
| *Rejected:* 19-217/GHC-YR1S2/3T green LED | C72043 | **stop_product** | – | 0 | Fails active rule |
| *Rejected:* 19-213/Y2C-CQ2R2L/3T yellow LED | C72038 | normal | – | **0** | Fails stock rule |

Lifecycle and stock came from LCSC's product-detail data endpoint `https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=<code>`, field `productCycle`. Product pages are at `https://www.lcsc.com/product-detail/<code>.html`. "normal" is LCSC's distributor label, not a manufacturer statement.

---

## Sources

- [S1] Lastminuteengineers, *Interfacing Capacitive Soil Moisture Sensor with Arduino* (resistive probes rust; 555-based capacitive module; epoxy on PCB edges; insertion depth affects reading). https://lastminuteengineers.com/capacitive-soil-moisture-sensor-arduino/
- [S2] Lastminuteengineers, *How Rain Sensor Works* (exposed copper comb, resistive; corrosion when powered; ~20° tilt; electronics not waterproof). https://lastminuteengineers.com/rain-sensor-arduino-tutorial/
- [S3] Harris & Stonard, *A Printed Capacitance Sensor for Soil Moisture Measurement*, Proceedings 2(13):705, Eurosensors 2018 (charge/discharge timing, 1 MΩ, comparator thresholds, C_air ≈ 8.6 pF, flat band 15–25% VWC in clay loam). https://www.mdpi.com/2504-3900/2/13/705
- [S4] PMC12528689, *Designing and field calibration of low-cost microcontroller-based soil moisture sensor for subsurface drip-irrigation system* (dielectric constants: dry soil 2–6, water 80, air 1). Seen via search-result extract only; page not opened. **Treat the citation as unverified.** https://pmc.ncbi.nlm.nih.gov/articles/PMC12528689/
- [S5] Papini et al. 2026, *Design, 3D Modeling and Characterization of a Custom Low-Cost Interdigitated Capacitive Sensor for Soil Water Content* (solder mask prevents corrosion; 1 mm gap; $5 for 10 PCBs <100×100 mm). https://strathprints.strath.ac.uk/97436/1/Papini-etal-2026-Design-3D-modeling-and-characterization-of-a-custom-low-cost-interdigitated.pdf
- [S6] Nagahage, Nagahage & Fujino 2019, *Calibration and Validation of a Low-Cost Capacitive Moisture Sensor…*, Agriculture 9(7):141 (abstract read via OpenAlex). https://www.mdpi.com/2077-0472/9/7/141 ; https://api.openalex.org/works/doi:10.3390/agriculture9070141
- [S7] Abdelmoneim et al. 2025, *Calibration of Low-Cost Capacitive Soil Moisture Sensors for Irrigation Management Applications*, Sensors 25(2):343 (R² 0.85–0.87, RMSE 4.5–4.9%, CV 10–16% above 30%; abstract via OpenAlex). https://www.mdpi.com/1424-8220/25/2/343 ; https://api.openalex.org/works/doi:10.3390/s25020343
- [S8] Thelaidjia et al. 2026, *Performance of Two Low-Cost Capacitive Soil Moisture Sensors Under Contrasting Texture and Salinity Conditions*, Water 18(12):1431 (air/water calibration unsuitable; salinity and texture effects; abstract via OpenAlex). https://doi.org/10.3390/w18121431 ; https://api.openalex.org/works/doi:10.3390/w18121431
- [S9] LCSC product page, PY32F002BL15S7TU (6 I/O on SOP-8, 12-bit ADC, two comparators, 1.7–5.5 V, −40…105 °C) https://www.lcsc.com/product-detail/C49424671.html (via search-result extract) ; Seechips listing (UID, internal reference channels; search-result extract only, **treat as unverified**) https://www.seechips.com/product/detail/store/8141343/py32f002bl15s7tu.html
- [S10] PY32F002B datasheet (Chinese), LCSC-hosted, table 5-7 run current (24 MHz about 1.10 mA) and table 5-9 Stop-mode current (LPR + LSI + LPTIM 1.70 µA typ; characterisation data, not production-tested). https://datasheet.lcsc.com/datasheet/pdf/2e27a47167843b6c53e90436073b6c5c.pdf?productCode=C49424671
- [S11] PY32F002A datasheet, LCSC-hosted (Stop mode, LPR, LPTIM wake-up). https://datasheet.lcsc.com/datasheet/pdf/2f52960731e735af83e5463168090872.pdf?productCode=C5292060
- [S12] Energizer CR2032 product datasheet (235 mAh to 2.0 V at 15 kΩ, 21 °C; ~1%/yr self-discharge; −30 to 60 °C; ingestion warning and compartment guidance). https://energizer.com/wp-content/uploads/2025/01/cr2032.pdf
- [S13] WCH CH32V003 datasheet (table 3-8: Standby 9.1 µA LSI on / 7.6 µA LSI off at 3.3 V). https://ch32-riscv-ug.github.io/CH32V003/datasheet_en/CH32V003DS0.PDF
- [S14] Newark, Abracon ADCC-S05R5SA105RB 1 F 5.5 V coin supercapacitor (−25 to 70 °C, 15 Ω ESR); search-result extract only, **treat as unverified**. https://www.newark.com/abracon/adcc-s05r5sa105rb/supercap-1f-5-5v-radial/dp/82AK2577
- [S15] Fabregas, Kremer & Schilbach 2019, *Realizing the potential of digital development: The case of agricultural advice*, Science, doi:10.1126/science.aay3038 (abstract read via Europe PMC). https://www.ebi.ac.uk/europepmc/webservices/rest/search?query=EXT_ID:31831641%20AND%20SRC:MED&resultType=core&format=json ; https://doi.org/10.1126/science.aay3038
- [S16] Beach, Milliken, Franzen & Lapidus 2025, *Meta-analysis of the impacts of digital information interventions on agricultural development*, Global Food Security, doi:10.1016/j.gfs.2025.100866. https://pmc.ncbi.nlm.nih.gov/articles/PMC12167173/
- [S17] Chikankheni, Stirzaker, Strong, Fandika & Chipula 2024, *Simple soil water monitoring tools increase yield and income of smallholder farmers in Malawi: A case study of four irrigation schemes*, Irrigation and Drainage, doi:10.1002/ird.3013 (abstract via OpenAlex). https://doi.org/10.1002/ird.3013 ; https://api.openalex.org/works/doi:10.1002/ird.3013
- [S18] VIA, *Impact on farmers* (developer page; testimonials; "30–50%" claim). https://www.via.farm/stories_impact_on_farmers/
- [S19] Carrijo, Lundy & Linquist 2017, *Rice yields and water use under alternate wetting and drying irrigation: A meta-analysis*, Field Crops Research 203:173–180. https://linquist.ucdavis.edu/sites/g/files/dgvnsk6581/files/inline-files/2017-Carrijo-et-al-AWD-meta-analysis-FCR.pdf
- [S20] Chilundo et al. 2020, *Do agricultural innovation platforms and soil moisture and nutrient monitoring tools improve the production and livelihood of smallholder irrigators in Mozambique?*, Int. J. Water Resources Development, doi:10.1080/07900627.2020.1760799 (abstract via OpenAlex). https://api.openalex.org/works/doi:10.1080/07900627.2020.1760799
- [S21] Vatta, Sidhu, Lall, Birthal & Taneja 2018, *Assessing the economic impact of a low-cost water-saving irrigation technology in Indian Punjab: the tensiometer*, Water International, doi:10.1080/02508060.2017.1416443 (abstract via OpenAlex). https://api.openalex.org/works/doi:10.1080/02508060.2017.1416443
- [S22] Mahapatra et al. 2025, *Boosting the Water Productivity of Dry-Season Crops in Eastern India*, Irrigation and Drainage, doi:10.1002/ird.3116 (abstract via OpenAlex). https://api.openalex.org/works/doi:10.1002/ird.3116
- [S23] Sarku & Ayamga 2025, *Is the right going wrong? Analysing digital platformization, data extractivism and surveillance practices in smallholder farming in Ghana*, Information Technology for Development, doi:10.1080/02681102.2024.2447596 (abstract via OpenAlex). https://api.openalex.org/works/doi:10.1080/02681102.2024.2447596
- [S24] OECD, *Digital governance in agriculture*, ch. 5 "Lock-in issues" (2 Nov 2026), including Thailand's interoperability constraints. https://www.oecd.org/en/publications/digital-governance-in-agriculture_619edd1f-en/full-report/component-10.html
- [S25] Minnesota Attorney General press release, 15 Jan 2025, lawsuit with Illinois AG and FTC against Deere over repair restrictions. https://www.ag.state.mn.us/Office/Communications/2025/01/15_Deere.asp
- LCSC data pages fetched (all at `https://wmsc.lcsc.com/ftps/wm/product/detail?productCode=<code>`): C49424671, C5292060, C5346354, C5292059, C84256, C2296, C2297, C2286, C2293, C318884, C94599, C964743, C964777, C964753, C51883822, C22935, C23138, C25804, C21190, C14663, C72043, C72038.
