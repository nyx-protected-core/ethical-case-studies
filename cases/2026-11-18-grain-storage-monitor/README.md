# Grain Bag Light: design study for a cheap, offline grain-storage monitor that sits inside a hermetic bag and answers with lights, not a phone

Ethical case study (food insecurity, post-harvest loss). Research and design writing only: no firmware, no builds.
Author role: assembly / firmware engineer. I would write the firmware in C and assembly later; this document contains **no code**.
All LCSC prices, stock and lifecycle labels were read from LCSC's product-detail data on **2026-11-18, about 18:05–18:15 (UTC+7)** and will change.

---

## Summary

- **What it is:** a small sealed capsule that a farmer places on top of the grain, inside the inner liner of a triple-layer hermetic bag (for example a PICS bag) or near the top of a small silo, before tying the bag. It wakes every hour or so, measures, and then sleeps. It answers one question: *is this grain staying dry and sealed, or is something going wrong?* **Red** means inspect now (dry, sell or use the grain). **Yellow** means watch: it is getting wetter, or the device is still settling. **Green** means dry enough now. A buzzer gives the same answer by sound. No radio, no app, no account, and nothing leaves the device.
- **The bag is never opened to read it.** You press the button through the plastic film, and the lights and buzzer answer through the liners. Every hole or re-opening weakens the hermetic seal [S13][S12], so the design never needs a cable through the bag. *Whether the LEDs are visible through two "thick and fairly opaque" liners [S12] is unverified* (section 5), which is why the buzzer is a primary channel, not an extra.
- **Two builds:**
  - **Build A, T/RH only (recommended for households):** Puya PY32F002BD15S6TU MCU plus an Aosong AHT20 sensor on a CR2032 coin cell. **About US$1.37 in LCSC-verified electronics** at about 50 units, and roughly **US$2.70–5.30 per unit** with PCB, capsule, vent membrane and cell (the non-LCSC lines are estimates, marked unverified).
  - **Build B, T/RH + CO₂ (cooperatives, warehouses, research):** adds a Sensirion SCD41 photoacoustic NDIR sensor on 3×AA cells. **About US$19.39 in electronics** (the SCD41 alone is about $17), roughly **US$22–26 per unit**. A thermal-conductivity STCC4 variant is about $11 in electronics but has open questions.
- **The CO₂ cost tension is real, and so is a deeper problem.** No battery-friendly CO₂ sensor I found is sold on LCSC's international site today. The SCD41, SCD40, STCC4 and SHTC3 all return "Page Not Found" on lcsc.com, although JLCPCB (LCSC's sister assembly service) stocks them. The CO₂ parts LCSC does list are a 5 V, 40 mA module and a $23 Infineon module with 5 units in stock. More importantly, a sealed hermetic bag *normally* holds **10–16% CO₂**: 12.6–14.0% one day after closing and 14.1–16.6% after seven months in a Benin trial [S7]. Affordable sensors top out at about 4% (SCD4x output range 0–40,000 ppm [S21]). So high CO₂ in a hermetic bag means the bag is doing its job, not that the grain is spoiling. CO₂ is therefore most useful as a **seal-integrity signal** (it rose, then fell back toward air, so the bag is leaking). I explain this in section 2.3.
- **Evidence for the problem:** APHLIS estimates Kenya lost 16.7% of its 2022 maize after harvest, but only about 2.5% of that loss was in household storage [S1]. FAO/World Bank put sub-Saharan grain losses at about US$4 billion a year, with 10–20% physical losses before processing [S2]. FAO estimates about 14% of the world's food is lost between harvest and retail [S3]. India's 2022 national study found much lower cereal losses: paddy 4.16% on farm plus 0.61% at market [S4]. IRRI cites 15–20% post-harvest loss for rice [S5]. **Estimates disagree widely**, and storage is only one slice.
- **Evidence for hermetic bags is strong:** a 2024 meta-analysis found grain in conventional bags had 42× more insects, 11× more damage and 23× more weight loss than grain in hermetic bags [S6]. Field results are good but not perfect: under 10% loss even after rodents holed one bag [S10], and a beetle species survived in some hermetic containers [S9]. A monitor can **support** this proven practice. It cannot replace drying grain before storage.
- **Ethics, in one line:** the device must fail toward red, never toward green. The reading belongs to the farmer, and the monitor must not cost more than the problem it catches. For a household, Build A costs more than the bag itself (a 100 kg PICS bag was US$1.64 in Malawi in 2018 [S18]). Simpler tools exist: a US$4 hygrometer method [S16] and the cobalt-chloride DryCard [S17]. The monitor earns its place only by watching the bag **continuously, without opening it**.

---

## 1. Purpose and users

**Who:**
- Smallholder households storing maize, rice, sorghum, cowpea, beans or wheat for 4–12 months, for food or for sale later when prices are higher.
- Farmer cooperatives and village stores holding many bags (the best fit for Build B).
- Extension workers, who would set the crop type and teach the light meanings.

PICS training had reached "millions of farmers in more than 37,000 villages", with more than 5 million bags sold by June 2015 [S12]. The user base already exists. The bag is the intervention, and this device is a helper for it.

**What decision it informs:**

| Light / sound | Meaning (what the farmer is told) | Action |
|---|---|---|
| **Green**, one short beep | "The air in the bag says the grain is dry enough now." | Leave the bag closed. Check again next week. |
| **Yellow**, two beeps | "Getting wetter, or still settling after sealing (first day)." | Check again in a few days. Look for wet patches or holes in the bag. |
| **Red**, long beeps | "Too damp for safe storage, or the seal may be broken (Build B)." | Open, inspect, re-dry or use/sell soon. Check the liners for holes. |
| **Red + yellow alternating** | "The monitor itself has a problem (sensor fault or low battery)." | Do not trust it. Inspect the grain by hand and replace the battery or monitor. |

**Why green has to be earned.** Storage fungi need air humidity of at least 65% (water activity 0.65), which corresponds to about 13% moisture in cereal grain [S15]. Purdue's work with maize used 65% RH as the "dry enough" line and advised drying to 60% RH if the grain is to be sold [S16]. The monitor measures the air between the kernels. In a sealed bag that air settles to the grain's *equilibrium relative humidity* (ERH), so it is an indirect reading of grain moisture (section 2.1).

**How a no-smartphone user reads it.** One button and three LEDs behind a clear window, plus a picture card: red bag with a hand opening it, yellow bag with an eye, green bag with a tick. The buzzer repeats the colour as a beep count, which helps users who cannot see the lights through the liners, have poor eyesight, or check bags in a dark store.

**The electronics in plain words:**
- The microcontroller sleeps almost all the time, drawing about **1.7 µA**: its 32 kHz internal clock keeps a timer running, and almost nothing else is powered [S28].
- About once an hour the timer wakes it. It switches the humidity sensor's supply on through a pin, waits a few milliseconds for the sensor to start, asks for a reading, and waits about 80 ms while the sensor charges and measures a tiny polymer capacitor whose value changes with absorbed water [S19].
- It compares the reading with stored thresholds, keeps a short trend history, and switches everything off again.
- When you press the button, it turns on one LED for a few seconds through a resistor that limits the current to a few milliamps, and pulses the buzzer coil through a small transistor.
- The coin cell supplies microamps for months and milliamps for seconds. That ratio sets the battery life (section 4).

---

## 2. Sensing

### 2.1 Temperature and relative humidity (both builds)

**Principle: headspace RH tracks grain moisture.** Air sealed among the kernels comes to equilibrium with the grain's moisture. This is the *sorption isotherm* (EMC/ERH relationship). Purdue's predicted ERH for maize, using the Chung-Pfost equation (ASAE D245.6) [S16]:

| Maize moisture (wet basis) | ERH at 20 °C | at 25 °C | at 30 °C |
|---|---|---|---|
| 11.9% | 53.1% | 56.2% | 59.0% |
| 14.0% | 67.6% | 70.1% | 72.2% |
| 15.2% | 74.3% | 76.3% | 78.1% |
| 16.3% | 79.6% | 81.2% | 82.7% |
| 17.8% | 85.3% | 86.6% | 87.6% |

Three facts that matter for the electronics:
1. **Temperature has to be measured as well.** At the same moisture, ERH rises with temperature. So the threshold is a (crop, temperature) table, not a single number.
2. **The isotherm depends on the grain.** The table above is maize only. Rice, sorghum, cowpea and beans each need their own curve, and it differs again between drying grain (desorption) and grain taking up moisture (adsorption) [S16]. Per-crop tables are an **open question** (section 8).
3. **Equilibrium takes time.** In a small sealed bag, hygrometers settled in about 30–45 minutes [S16]. The DryCard team notes that freshly dried grain reads falsely dry until moisture inside the kernels equilibrates, about 4 hours for rice after a heated dryer [S17]. In a 50–100 kg bag with the sensor in the top layer, settling will take longer (**unverified**). The firmware therefore shows "settling" (yellow) for the first 24 hours and never green during that time.

**Chosen sensor: Aosong AHT20 (LCSC C2757850).** From its datasheet [S19]:
- ±2% RH typical accuracy, ±0.3 °C, drift under 1% RH a year.
- Sleep current at most 0.2 µA, 570 µA while measuring, 80 ms per measurement.
- Supply 2.2–5.5 V, so it runs straight from a coin cell.
- Two datasheet warnings shape the design:
  - Long exposure above 80% RH can shift the reading by **+3% RH after 60 hours**. It recovers slowly afterwards. A wet bag therefore biases the reading upward, which pushes the device **toward red**: the safe direction.
  - Volatile solvents, tapes, adhesives and packaging materials can shift readings. The recommended neighbouring materials are metal, PTFE, PE, PP, POM and similar. This drives the enclosure choice (PP/PE capsule, PTFE membrane) in section 5.
- The datasheet also advises powering the sensor through a controllable supply with an RC filter, and keeping copper and heat sources away from it (a 1.5 mm slot in the PCB). The MCU pin power-gating in section 3 follows that advice.

A cheaper alternative is the **AHT30 (C45354925)**: $0.4448 @100 vs AHT20 $0.5357 @100, but ±3% RH typical [S20]. Near the 65% decision line, ±3% means a bag at 62% could read 65%. I chose the AHT20 because a 1% RH difference in accuracy is worth $0.09 when the cost of a wrong answer is a bag of grain.

**Temperature as a spoilage signal is weak in small bags.** In PICS bags, grain temperature "correlated closely" with ambient temperature over eight months [S11]. In 60 m silo-bags, grain temperature also follows ambient air because the surface-to-volume ratio is high. That hides the heat of biological activity, and Bartosik et al. turned to CO₂ for exactly this reason [S13]. In a 100 kg bag, a mouldy hot spot away from the sensor will probably not show as a temperature rise at the sensor (**unverified**, but consistent with [S11][S13]). Build A therefore uses temperature mainly to pick the right ERH threshold, and only secondarily as a "too hot" warning.

### 2.2 Why CO₂ is the textbook early spoilage signal, and what it really means inside a hermetic bag

**The textbook case.** In a gas-tight store, respiration by grain, fungi and insects "increases CO₂ and reduces O₂ concentrations". The degree of change "could be related to the biological activity inside the silo-bag, and can be used as a monitoring tool to detect early spoilage problems" [S13]. INTA and Silcheck built a commercial service on this. It monitored more than 10,000 silo-bags, classifying each section as safe, ambiguous or risky from the CO₂ reading together with the grain moisture at bagging, location, storage time and the previous reading [S13]. Published threshold figures (for example, soybean below about 4% CO₂ safe and above about 12% unsafe) appeared in search extracts of Cardoso/Bartosik papers, but **I could not open the primary paper** [S14], so those numbers are **unverified** and are not used here.

**What is different in a PICS-type bag.** Hermetic bags work *because* respiration drives the oxygen down and the CO₂ up until insects stop feeding and die [S12][S9]. Measured values:
- **Benin (Baributsa et al. 2020), maize bought already infested:** O₂ in hermetic bags held at 0.5–1.0% for seven months, while CO₂ was 12.6–14.0% one day after closing and 14.1–16.6% at seven months. There was no weight loss in any hermetic bag, against 6.3% and 10.3% in the two non-hermetic bags [S7].
- **Burkina Faso (Waongo et al. 2019), sorghum:** over eight months, CO₂ rose by more than 6 points in PICS bags against 0.7 in polypropylene bags [S8].
- **Purdue lab (Williams et al. 2017), maize:** oxygen fell sharply only in **infested** PICS bags. Clean grain left the oxygen high. Briefly opening the bags for sampling let oxygen back in [S11].

**So for a hermetic bag:**
1. **High CO₂ is normal and protective** if the grain had insects or was respiring. It does not mean the bag is spoiling.
2. **Low CO₂ is ambiguous.** It can mean clean, dry, inactive grain (good) or a leak (bad). The device cannot tell which from one reading.
3. **The useful signal is the history.** CO₂ that **rose and then falls back toward air** means the seal has been broken, and insects can then recover. This is the event a monitor can catch that a farmer cannot see. A Zimbabwe field trial in which *T. castaneum* survived inside hermetic containers noted that gas monitoring or a pressure test "could have helped determine ... possible loss of hermeticity" [S9].
4. **CO₂ that keeps rising while RH is also high** suggests microbial respiration in damp grain. That fits Bartosik's silo-bag logic [S13], but in a hypoxic bag fungal growth is also suppressed. How CO₂ behaves when damp grain moulds inside a PICS bag is **unverified** for small bags.

**The sensor-range problem.** The SCD4x's output range is 0–40,000 ppm (0–4%) [S21]. The STCC4 covers 380–32,000 ppm [S22]. A working PICS bag at 10–16% [S7] will **pin these sensors at the top of their range**. The firmware has to treat a pinned reading as "hermetic atmosphere present", not as a number. How each sensor behaves (whether it returns a clean saturated value or a fault) at 10–16% CO₂ and 0.5–5% O₂ is **unverified** and must be bench-tested first. It is the single biggest technical risk in Build B.

**A cheaper insect-activity proxy, and why I did not pick it.** Acoustic detection of larval feeding (a piezo disc plus amplifier) or an oxygen-indicator dot behind a window are possible alternatives. I did not verify parts, costs or field evidence for either, so they stay as **unverified ideas** in section 8. Build A's honest substitute for CO₂ is a **moisture trend**: insects "generate moisture" in stored grain [S15], so a rising ERH in a sealed bag is a warning even without a gas sensor.

### 2.3 CO₂ options and their real price (verified 18 Nov 2026)

| Option | Where listed / lifecycle | Price | Stock | Fit inside a bag on batteries |
|---|---|---|---|---|
| **Sensirion SCD41-D-R2** (photoacoustic NDIR, I²C, built-in SHT4x) | **Not available on lcsc.com today** (C3659294 product page returns "Page Not Found"; LCSC data endpoint returns null). JLCPCB lists C3659294. DigiKey lists the part as **Active** [S24]. | JLCPCB **$16.9758 @30**, $15.6033 @100 [S25]. DigiKey $17.73 @50 [S24]. | JLCPCB 1,173. DigiKey 10,482. | **Best fit.** Single-shot mode (SCD41 only): 0.45 mA average at 3.3 V with one measurement every 5 min, **175 mA peak** (205 mA max), 2.4–5.5 V supply that "must be kept constant", operating 0–95% RH non-condensing and −10 to 60 °C [S21]. A coin cell cannot deliver the peak current, so Build B uses AA cells. |
| Sensirion SCD40-D-R2 | Not on lcsc.com (C3659421 "Page Not Found"). JLCPCB stock 2,368. | Not priced | n/a | No single-shot mode [S21], so power-hungry. Rejected. |
| **Sensirion STCC4-D-R3** (thermal-conductivity CO₂) | Not on lcsc.com (C49191471 "Page Not Found"). JLCPCB stocks it. DigiKey lists it **Active** [S23]. | JLCPCB **$8.9489 @30**, $8.1956 @100 [S25]. DigiKey $8.52 @50 [S23]. | JLCPCB 2,655. DigiKey 1,620. | Half the price of the SCD41, 100 µA average in single-shot mode every 10 s. But ±(100 ppm + 10%), recommended 10–40 °C and **20–80% RH**, and it needs an SHT4x companion sensor for temperature and humidity compensation [S22]. Whether the AHT20 could compensate it in firmware instead is **unverified**, and the SHT4x is also not on lcsc.com today. Thermal-conductivity sensing in a low-O₂, high-CO₂ bag: **unverified**. |
| Saiya SY-CO2-28BMI (NDIR module, UART) | **LCSC C22466760, `normal`** | $12.7909 @50 | **32** | Runs on 5 V only (4.5–5.5 V), **40 mA average, 125 mA peak**, needs a supply able to source 150 mA, T90 under 120 s, 400–10,000 ppm range, 0–95% RH non-condensing, recalibration advised every 6 months, and its auto-baseline is "not suitable" for greenhouses or cold stores [S26]. Each reading costs roughly 40 mA × 120 s ≈ 4.8 C (my arithmetic), about 100× an SCD41 shot. **Rejected for battery use.** |
| Infineon XENSIV PAS CO2 5V (PASCO2V15AUMA1) | **LCSC C26655608, `normal`** | $23.2182 @30 | **5** | Needs a 5 V emitter rail plus 3.3 V logic [S27]. Too expensive, too little stock. **Rejected.** |

**Bottom line on CO₂:** the cheapest workable CO₂ sensor costs **about 6–12× the entire Build A electronics**, and none passes the design's "verified Active on LCSC" sourcing rule today. JLCPCB and DigiKey listings are not LCSC, and I mark them as such. So Build B is a design option I **cannot fully clear under the LCSC rule this turn**. If it goes ahead, either LCSC re-lists Sensirion parts, or the designer accepts a second distributor for that one line.

---

## 3. Cheapest active parts on LCSC, and firmware outline

**Lifecycle method (same rule as the soil-moisture study).**
- **How I checked:** for each part I read LCSC's own product-detail data (`wmsc.lcsc.com/ftps/wm/product/detail?productCode=C…`) and recorded the `productCycle` field, price breaks and stock on **2026-11-18 (UTC+7)**. `normal` is LCSC's label for a part in normal supply. It is the **distributor's** label, not the manufacturer's lifecycle statement.
- **Search route:** LCSC's own search returned "Access Denied" to me, so I found candidate part codes with JLCPCB's public parts search [S25] and then verified each code on LCSC.
- **Sensirion parts:** none are listed on lcsc.com today (section 2.3).

### 3.1 MCU candidates

| Part | LCSC code | Lifecycle (LCSC) | Price | Stock | Notes |
|---|---|---|---|---|---|
| **Puya PY32F002BD15S6TU** (Cortex-M0+, SOP-8 class) | **C7469097** | `normal` | **$0.1344 @50** | 1,750 | **Chosen.** Stop mode with the LPTIM running from the internal 32 kHz LSI: **1.70 µA** typical [S28]. Enough pins for I²C, 3 LEDs, buzzer, button and sensor power. |
| Alternative MCU 1 | C49424671 | not re-stated (**unverified**) | see note | see note | Recorded as a fallback during the search; I did not keep its sleep-current figure, so it is **unverified** for this design. |
| Alternative MCU 2 | C5292060 | not re-stated (**unverified**) | see note | see note | As above (**unverified** sleep current). |
| Alternative MCU 3 | C5346354 | not re-stated (**unverified**) | see note | see note | As above. PY32F002A-class Stop current and WCH CH32V003 standby current were **not re-fetched** this turn, so they stay **unverified**. |

### 3.2 Sensors and indicators (Build A)

| Function | Part | LCSC code | Lifecycle | Price | Stock |
|---|---|---|---|---|---|
| T/RH sensor | **Aosong AHT20** | **C2757850** | `normal` | **$0.6051 @30** ($0.5357 @100) | 28,096 |
| T/RH alternative | Aosong AHT30 (±3% RH) | C45354925 | `normal` | $0.4448 @100 | in stock |
| T/RH alternative | GXHT30C (SHT30-class clone) | C2913569 | listed | not used | specs **unverified** |
| T/RH rejected | CHT8305C | C5121990 | listed | n/a | **0 stock** |
| T/RH rejected | TI HDC1080 | C82227 | listed | $1.37 | 88 |
| Red / yellow / green LEDs | 0603 LEDs | C84256 / C2296 / C2297 | `normal` | **$0.0427 combined** | in stock |
| Buzzer | MLT-8530 magnetic transducer | C94599 | `normal` | $0.1548 | in stock |
| Buzzer / LED driver | AO3400A N-MOSFET | C20917 | `normal` | $0.0627 | in stock |
| Button | TS-1187A tact switch | C318884 | `normal` | $0.0216 @20 | in stock |
| Battery holder | CR2032 holder | C964777 | `normal` | $0.2852 | in stock |
| Passives | 330 Ω ×3 (C23138), 4.7 kΩ ×2 (C23162), 10 kΩ (C25804), 100 nF ×3 (C14663) | — | `normal` | $0.0295 combined | in stock |
| Bulk capacitor | 10 µF 0805 | C15850 | `normal` | $0.0314 @20 | in stock |

### 3.3 Additional parts (Build B)

| Function | Part | Code / source | Lifecycle | Price | Stock |
|---|---|---|---|---|---|
| CO₂ (+T/RH) | Sensirion SCD41-D-R2 | C3659294 on **JLCPCB, not LCSC** | DigiKey: Active | $16.9758 @30 (JLCPCB) | 1,173 (JLCPCB) |
| 3.3 V regulator | Holtek HT7333-A LDO | C21583 | `normal` | $0.2015 | in stock |
| Regulator alternative | Torex XC6206 3.3 V | C5446 | `normal` | not used | in stock |
| Sensor power switch | AO3401A P-MOSFET | C15127 | `normal` | $0.0647 | in stock |
| Battery holder | BH-AA-A1AJ020 (1×AA) ×3 | C5290181 | `normal` | $0.3318 @30 each | in stock |
| Extra decoupling | 10 µF ×2 (C15850), 100 nF ×1 (C14663) | LCSC | `normal` | about $0.068 | in stock |

The HT7333's quiescent current and dropout were not re-read from its datasheet this turn: **unverified**. The LDO keeps the SCD41's supply constant, as its datasheet requires [S21].

### 3.4 Firmware outline (prose only, no code)

1. **Reset and self-test:**
   - Check the battery voltage against the internal reference.
   - Read the sensor once. If the sensor does not answer, or the battery is low, go to the fault pattern (red and yellow alternating).
2. **Commissioning:**
   - A long button press selects the crop (blinks 1–4 for maize, rice, sorghum, beans). An extension worker does this.
   - The device stores the crop choice and starts a 24-hour "settling" period, during which it never shows green.
3. **Periodic wake (about hourly, from the low-power timer):**
   - Power the sensor from a GPIO, wait for start-up, take one measurement, and power the sensor off.
   - Look up the ERH threshold for that crop at that temperature (a small table built from the sorption isotherm, with a safety margin of a few % RH).
   - Store a compressed reading in a small ring buffer in RAM or flash (e.g. 6-hour averages for the season).
4. **Classification. Green only if *all* of these pass:**
   - Settling period over.
   - RH below the crop threshold minus margin.
   - No rising RH trend over the last few days.
   - Sensor answered with a valid checksum.
   - Battery above the reserve level.
   - (Build B) the CO₂ history shows no "rose, then fell toward air" pattern.

   Any single failure gives yellow or red. Any doubt gives yellow, never green.
5. **Build B CO₂ handling:**
   - Automatic self-calibration (ASC) **off**. ASC assumes the sensor regularly sees fresh air, which is never true inside a sealed bag [S21].
   - Power the SCD41 on, **discard the first single-shot reading** after power-up [S21], then take the reading.
   - Treat a value at the range ceiling as "hermetic atmosphere present", not as a concentration.
   - Flag a leak when CO₂ falls from saturated to near air levels over several readings.
6. **Button press:** show the stored classification for about 5 seconds on one LED, with the matching beep pattern. Debounce, and limit how often it can repeat so a stuck button cannot drain the cell.
7. **End of life:** when the battery reaches the reserve level, beep the fault pattern once a day for a few weeks, then stop.

Sensirion's embedded I²C driver for the SCD4x is BSD-3-Clause [S32]. I used no code from it or from any reference design.

---

## 4. Power and condensation protection

### 4.1 Build A (CR2032), for one season and beyond

My arithmetic, using datasheet figures:
- **Sleep:** MCU 1.70 µA [S28] + AHT20 ≤0.2 µA [S19] (less when it is power-gated off).
- **Hourly measurement:** about 570 µA × 80 ms for the sensor [S19], plus a few ms of MCU run time (MCU awake time **unverified**, estimated).
- **Button presses:** a few mA for about 5 s, a few times a week.
- **Average:** about **2.1 µA**. That is about 18.4 mAh a year against a CR2032's nominal capacity [S29]. On paper that is **about 12.8 years**.
- **Realistic plan:** **2–4 years** of service, allowing for self-discharge, high storage temperatures, pulse-current losses and button use. One season (6–10 months) is comfortably covered.

### 4.2 Build B (3×AA + LDO)

- **SCD41 single-shot:** 0.45 mA average at one shot every 5 minutes [S21], i.e. about 135 mC per shot. Hourly, with one discarded warm-up shot, that is about 270 mC an hour, or **about 75 µA average** (my arithmetic). Over an 8-month season (about 5,840 h) that is **about 440 mAh**.
- **Supply:** AA alkaline capacity [S30] (the exact Energizer E91 figure is **unverified**) gives several seasons on paper.
- **Peak current:** 175 mA typical, 205 mA maximum [S21]. AA cells handle this; a coin cell cannot.
- **Power gating:** the SCD41 is gated off with the AO3401A between readings.

### 4.3 Condensation and dust

Daily temperature swings can condense moisture at the top of a bag. Liquid water would destroy both a reading and the electronics. Protection:
- **Vent membrane:** ePTFE (Gore-type) over the sensor port, which passes water vapour and gas but not liquid water or dust [S31]. The AHT20 datasheet lists PTFE among acceptable neighbouring materials [S19].
- **Conformal coating** on the board, with the sensor opening masked off.
- The sensor sits away from the capsule wall. The firmware flags a sudden jump to about 100% RH as "condensation or wet grain: inspect" (red).
- The AHT20's long-term high-humidity drift is upward [S19], which is the safe direction.

---

## 5. Enclosure, placement and per-unit BOM

### 5.1 Food-safe enclosure

- **Capsule:** a small screw-top or snap-fit PP or HDPE capsule, roughly pill-bottle size, with a clear window over the LEDs. PP and PE are on the AHT20 datasheet's list of compatible neighbouring materials [S19]. The exact resin's **food-contact certification is unverified** and has to be confirmed with the supplier.
- **Inside the capsule:** no glues, tapes or solvent-based paints near the sensor [S19].
- **Openings:** one sensor port sealed with the ePTFE vent [S31]. The battery door has an O-ring.
- **Shape:** smooth and round, with nothing that can puncture a liner. A bright colour and a tether cord, so it is found when the grain is emptied and does not end up in a mill or a cooking pot.

### 5.2 Placement

- **Hermetic bag:** inside the innermost liner, on top of the grain, just under where the liner is twisted and tied. The top layer is where moisture migrates and condenses, and where the user can reach the button by pressing through the film without opening the bag.
- **Small silo:** hung near the top of the grain mass, under the lid.
- **Never** let the device's cord or a cable pass through the liners. Every opening or puncture costs hermeticity [S11][S13].
- **One monitor per bag** is the honest default. One monitor per stack of bags assumes they are all alike, which they are not.
- **Visibility:** reading by pressing through the film relies on light passing through the bag. PICS liners are described as "thick and fairly opaque" [S12]. LED visibility through two liners plus the woven outer bag is **unverified**, which is why the buzzer is a primary channel.

### 5.3 BOM: Build A (T/RH only), per unit at about 50 units

| Line | Source | Cost (USD) | Status |
|---|---|---|---|
| MCU PY32F002BD15S6TU | LCSC C7469097 | 0.1344 | verified 2026-11-18 |
| AHT20 | LCSC C2757850 | 0.6051 | verified |
| 3 LEDs | LCSC C84256 / C2296 / C2297 | 0.0427 | verified |
| Buzzer MLT-8530 | LCSC C94599 | 0.1548 | verified |
| AO3400A | LCSC C20917 | 0.0627 | verified |
| Tact switch | LCSC C318884 | 0.0216 | verified |
| CR2032 holder | LCSC C964777 | 0.2852 | verified |
| Resistors and 100 nF capacitors | LCSC (see 3.2) | 0.0295 | verified |
| 10 µF | LCSC C15850 | 0.0314 | verified |
| **Electronics subtotal** | | **1.3674** | **verified** |
| PCB (small 2-layer, share of a panel) | not LCSC | ~0.20–0.60 | **unverified estimate** |
| Capsule (PP/HDPE) with window and O-ring | not LCSC | ~0.40–1.50 | **unverified estimate** |
| ePTFE vent | not LCSC | ~0.20–0.80 | **unverified estimate** |
| CR2032 cell | not LCSC | ~0.20–0.50 | **unverified estimate** |
| Conformal coating and assembly share | not LCSC | ~0.30–0.50 | **unverified estimate** |
| **Per-unit total** | | **about 2.70–5.30** | partly unverified |

### 5.4 BOM: Build B (T/RH + CO₂), per unit at about 50 units

Same as Build A, minus the CR2032 holder, plus the lines below. The SCD41's own built-in humidity sensor could replace the AHT20; I kept the AHT20 so the moisture logic is identical in both builds.

| Line | Source | Cost (USD) | Status |
|---|---|---|---|
| Build A electronics minus CR2032 holder | LCSC | 1.0822 | verified |
| SCD41-D-R2 | **JLCPCB** C3659294 (not on LCSC) | 16.9758 | price verified at JLCPCB; **fails LCSC rule** |
| HT7333-A LDO | LCSC C21583 | 0.2015 | verified |
| AO3401A | LCSC C15127 | 0.0647 | verified |
| 3× AA holder BH-AA-A1AJ020 | LCSC C5290181 | 0.9954 | verified |
| Extra 2× 10 µF + 1× 100 nF | LCSC | ~0.068 | verified |
| **Electronics subtotal** | | **about 19.388** (of which ~2.41 LCSC-verified) | |
| PCB, larger capsule, vent, 3× AA cells, coating, assembly | not LCSC | ~2.50–6.50 | **unverified estimate** |
| **Per-unit total** | | **about 22–26** | partly unverified |

**STCC4 variant:** swapping the SCD41 for the STCC4 ($8.9489 @30 at JLCPCB) brings the electronics to **about $11.36**. It still needs an SHT4x companion (also not on lcsc.com), and how it performs inside a bag is **unverified**.

---

## 6. Loss and hermetic-storage evidence, with honest limits

| Claim | Figure | Source | Limit |
|---|---|---|---|
| Kenya maize post-harvest loss, 2022 | 16.7% total; household storage about 2.54% | APHLIS [S1] | Model estimate. Storage is a small share, so drying and harvest matter more. |
| Sub-Saharan Africa grain losses | about US$4 bn/yr; 10–20% physical loss | FAO / World Bank / NRI "Missing Food", via UN News [S2] | I read the news summary; the full PDF timed out (**unverified** detail). |
| Global food loss, harvest to retail | about 14% | FAO SOFA 2019 [S3] | Regional breakdown not read (PDF timed out): **unverified**. |
| India cereal losses, 2022 | paddy 4.16% on farm + 0.61% at market | NABCONS via PIB [S4] | National average; much lower than older estimates. |
| Rice post-harvest losses | 15–20% | IRRI Rice Today [S5] | Popular article, broad range. |
| Hermetic vs conventional bags | 42× insects, 11× damage, 23× weight loss, 3.6× better seed viability | Ngoma et al. 2024 meta-analysis [S6] | Read via the PubMed abstract only. |
| PICS gas levels, infested maize | O₂ 0.5–1.0%; CO₂ 12.6–14.0% (day 1), 14.1–16.6% (7 months); loss 0% vs 6.3–10.3% | Baributsa et al., Benin [S7] | One trial; grain was infested at the start. |
| Sorghum in PICS | CO₂ rise of more than 6 points over 8 months | Waongo et al., Burkina Faso [S8] | Single site. |
| Hermetic containers in Zimbabwe | under 3% loss vs 3.7–14.2%; *T. castaneum* survived | Mubayiwa et al., Sci Rep [S9] | No gas monitoring in that trial. |
| Field PICS performance | under 10% loss despite rodent holes; up to 25% damage, and under 5% vs 15–35% loss in comparisons | Mlambo et al. 2017 [S10] | Rodent damage breaks the seal. |
| O₂ falls only in infested bags | qualitative | Williams et al. 2017 [S11] | Lab study. |
| Mould threshold | water activity 0.65, about 13% grain moisture | FAO [S15]; Tubbs et al. [S16] | Varies by crop and temperature. |

**Honest limits:**
- No study I found tests an electronic monitor inside a PICS bag. Every claim about Build B's usefulness is a design argument, not evidence.
- If storage loss is only about 2.5% of the crop in good years [S1], a monitor's realistic gain is small. Its value is in catching the rare bad bag (wet grain, broken seal) before it is lost.

---

## 7. Ethics

1. **False reassurance is the main harm.**
   - **Where it comes from:** a green light on a bag that is spoiling in a corner the sensor cannot reach is worse than having no device.
   - **Mitigations:**
     - Green has to be earned on every check (section 3.4) and never shows during settling.
     - Faults and a low battery show red and yellow, never green.
     - The picture card says plainly: "Green means the air near the monitor is dry. Still look at your grain when you open the bag."
     - Training materials should say the monitor only senses the top of the bag.
2. **Ownership.** The farmer owns the device and the readings. There is no radio, no logging that leaves the device, and no account. If a co-op or NGO lends units, the readings are still the farmer's. The device should never become a tool for a buyer or lender to judge grain quality without the farmer's consent.
3. **Affordability.**
   - **The cost comparison:** Build A at about $2.70–5.30 costs more than the bag it protects (about $1.64 [S18]). Build B costs more than the grain in a small bag; the exact maize-price comparison is **unverified**.
   - **Cheaper tools come first:** a $4 hygrometer used in a sealed jar before storage [S16] and the reusable DryCard [S17] (DryCard price **unverified**) answer "is it dry enough to seal?" for less money.
   - **Where the monitor fits:** watching *during* storage, without opening the bag. One reasonable model is co-op-owned units lent out each season.
4. **Repairability.**
   - One through-hole-friendly board and a standard coin cell or AA cells.
   - A screw-top capsule; no glued-shut housing.
   - Published schematic and light codes.
   - A local repairer should be able to replace the battery, sensor or buzzer.
5. **It must not displace drying.** Drying to safe moisture before sealing is the proven intervention [S15][S16][S12]. The monitor cannot dry grain. Training should never present it as a reason to seal grain that is not dry. During the settling period, the first-day reading doubles as a "should not have been sealed yet" warning.
6. **Never require opening the bag.**
   - Reading works by pressing through the film, with a buzzer for sound.
   - No cable through the liners, and no sampling port.
   - Battery life covers the full season, so the bag never has to be opened to change cells.
7. **E-waste.** Coin and AA cells left in rural fields are a hazard. A battery take-back scheme through the same co-op or extension network is part of the design, not an afterthought.

---

## 8. Licence and citation check, and open questions

**Licences:**
- **No code, schematics or reference designs** were copied from anyone.
- Sensirion's embedded I²C driver for the SCD4x is **BSD-3-Clause** [S32], so it could be used later with attribution.
- **Unverified:** the Puya and WCH SDK licences.
- **Article licences:** the PLOS One article's CC BY licence line was not re-checked (**unverified**). Europe PMC articles were read as open-access full text. All figures are cited, not reproduced at length.

**Open questions:**
1. How the SCD41 and STCC4 behave at 10–16% CO₂ and 0.5–5% O₂: pinned value, error, or damage? (bench test first)
2. Will LCSC re-list Sensirion parts, or will the design accept JLCPCB or DigiKey for one line?
3. Per-crop sorption isotherms and thresholds for rice, sorghum, cowpea, beans and wheat, in both drying and wetting directions.
4. How long ERH takes to settle at the top of a 50–100 kg bag.
5. LED visibility through two PICS liners and the woven outer bag.
6. Store temperatures in target regions, and their effect on cell life and on the thresholds.
7. Food-contact certification of the capsule resin.
8. Cheaper insect-activity proxies (acoustic, O₂-indicator dot): parts, cost and evidence.
9. Bartosik and Cardoso's silo-bag CO₂ thresholds (primary paper not opened).
10. Whether co-op lending or household purchase is the realistic distribution model.

### Unverified items (collected)

- Bartosik / Cardoso CO₂ thresholds [S14].
- LED visibility through liners.
- CO₂ sensor behaviour at 10–16% CO₂ and low O₂.
- Non-LCSC BOM lines: PCB, capsule, ePTFE vent, cells, coating and assembly.
- HT7333 quiescent current and dropout.
- Energizer E91 capacity figure.
- MCU awake time per wake.
- DryCard price.
- Maize price comparison.
- PY32F002A Stop current, CH32V003 standby current, and the alternative MCUs' figures.
- GXHT30C specifications.
- PLOS licence line.
- Puya and WCH SDK licences.
- Per-crop isotherms and thresholds.
- Capsule food-contact resin.
- Store temperatures.
- FAO SOFA regional figures.
- "Missing Food" full-report details.
- ERH settling time in a full bag.
- Temperature hot-spot detectability at the sensor.

---

## Sources

All were read on 2026-11-18 (UTC+7). Distributor pages carry live data that will change.

- [S1] APHLIS, Kenya maize post-harvest loss estimates, 2022.
- [S2] UN News, report on "Missing Food: The Case of Postharvest Grain Losses in Sub-Saharan Africa" (FAO / World Bank / NRI).
- [S3] FAO, *The State of Food and Agriculture 2019*, newsroom release.
- [S4] Press Information Bureau (India), NABCONS post-harvest loss study, 2022.
- [S5] IRRI, *Rice Today*, article on post-harvest losses.
- [S6] Ngoma et al. 2024, meta-analysis of hermetic storage (PubMed abstract).
- [S7] Baributsa et al., hermetic bags with infested maize in Benin, Europe PMC PMC7469259.
- [S8] Waongo et al. 2019, sorghum hermetic storage in Burkina Faso, PMC6737907.
- [S9] Mubayiwa et al., *Scientific Reports*, hermetic containers in Zimbabwe, PMC7881032.
- [S10] Mlambo et al. 2017, hermetic bag field performance (University of Greenwich repository).
- [S11] Williams et al. 2017, *PLOS One*, oxygen and grain temperature in PICS bags.
- [S12] Purdue Extension E-265, PICS bags.
- [S13] Bartosik et al., CIGR 2013, CO₂ monitoring of silo-bags.
- [S14] Cardoso / Bartosik et al., silo-bag CO₂ thresholds (Academia.edu listing; primary not opened).
- [S15] FAO, manual on storage fungi and grain moisture.
- [S16] Tubbs et al. 2017, *AIMS Agriculture and Food*, hygrometer method and maize ERH table.
- [S17] UC Davis, DryCard.
- [S18] Farm Radio Malawi, PICS bag price.
- [S19] Aosong AHT20 datasheet.
- [S20] Aosong AHT30 datasheet.
- [S21] Sensirion SCD4x datasheet.
- [S22] SOS electronic, Sensirion STCC4 product page and datasheet data.
- [S23] DigiKey, STCC4-D-R3 listing.
- [S24] DigiKey, SCD41-D-R2 listing.
- [S25] JLCPCB public parts search (selectSmtComponentList), C3659294 / C49191471.
- [S26] Saiya SY-CO2-28BMI datasheet.
- [S27] Infineon XENSIV PAS CO2 product brief.
- [S28] Puya PY32F002B datasheet.
- [S29] Energizer CR2032 datasheet.
- [S30] Energizer E91 AA datasheet.
- [S31] Gore protective vents product information.
- [S32] Sensirion `embedded-i2c-scd4x` repository licence (BSD-3-Clause).
- LCSC product-detail data for every `C…` code quoted in sections 3 and 5 (`wmsc.lcsc.com/ftps/wm/product/detail`), read 2026-11-18.
