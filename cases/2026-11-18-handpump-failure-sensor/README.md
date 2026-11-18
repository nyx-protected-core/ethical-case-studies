# A handpump-failure sensor that texts the maintainer: a low-cost, repairable design study

Research and design only. Nothing here was built, flashed, measured or bought. Every number is either cited as [S#] (see SOURCES.md), shown with its arithmetic, or marked UNVERIFIED and listed in section 10. Parts data were read from live LCSC product pages on 18 November 2026 [S28].

This is part of an ethics case-study series whose anchor value is ending human trafficking. Water is part of that story. When the village pump breaks, someone, usually a woman or a girl, walks further for water, more often and at riskier hours. A household with no water close by is also easier to push into debt and distress migration. A pump that stays working is a small, real protection.

## Summary

- **The choice.** Of the three options asked about, this study designs the **handpump-failure sensor**. It is the cheapest per waterpoint, it fits on the roughly one-in-four handpumps in sub-Saharan Africa that are broken at any time [S1], and it is the only option with field trials in Africa that measured shorter repair times [S4][S5].
- **The evidence it rests on.** In Kyuso, Kenya, sensors plus a paid, responsive mechanic cut mean downtime from 27 days to under 3 [S4]. In Rwanda, sensor-dispatched repairs took a median of about 21 days, against about 152 under the existing system [S5]. The Rwanda trial also had sensors on the pumps whose operators never saw the data, and those pumps did not get fixed faster [S5]. **The sensor helps only when someone acts on it.**
- **The design.** A sealed box on the pump handle with a socketed ATmega328P-PU, an LIS2DH12 accelerometer that counts strokes, and a SIMCom A7672E LTE Cat-1 modem with GSM fallback. It sends **one SMS a day**: stroke counts in six 4-hour blocks plus a status code. An alert goes out early if a normally busy pump falls silent or the stroke signature changes.
- **Power, honestly.** It runs on three D-size alkaline cells. Sleep draw works out to about 5 µA. The daily radio session dominates: about 1.2 mAh a day with good signal, 8.9 mAh with poor signal, and 17 mAh if every session runs to its 3-minute timeout. On a deliberately derated 10 Ah pack, that is about 3 years with poor signal and 1.6 years in the worst case. The 23 years the arithmetic gives with good signal will not happen, because shelf life and heat limit the cells first. **Replace the cells every two years at a routine service**, as the Kyuso trial did [S4].
- **Connectivity.** 2G is going away: 11 African countries are on a GSMA roadmap to switch off 2G/3G by 2030 [S16]. Only two NB-IoT networks and no LTE-M network are listed for all of Africa [S18]. So the design uses LTE Cat-1, which is mainstream 4G, with GSM fallback, and has a footprint option for an NB-IoT/LTE-M modem. A plain SMS is kept as the payload so any phone can read it.
- **Cost.** Electronics from LCSC come to about **US$44 per board at qty 1, US$25.9 at qty 100 and US$25.0 at qty 1000**, before PCB, enclosure, battery holder and cells, which are UNVERIFIED. The modem (US$12.45) and the antenna (US$5.70) are about 70% of the qty-100 figure.
- **Ethics.** It counts handle strokes on a public pump: no camera, microphone, GPS fix, Bluetooth or Wi-Fi scanning, and no identifiers of people. Usage is aggregated to 4-hour blocks so it cannot show when one woman pumps alone. The data belong to the community water committee and are shared with the maintainer under a written agreement. Functionality status, not usage, goes to open registries. Who pays for SMS, cells and repairs has to be settled *before* installation. Never install it where there is no one funded to respond.
- **Count of unverified items:** 40 (section 10).

## 1. The problem, with dated evidence

**How many pumps are broken.** A 2019 review of 47 countries estimated that about one in four handpumps in sub-Saharan Africa is non-functional at any point in time. That was 175,000 of an estimated 680,000 in 2015 (26%), with medians of 27–28% across estimates and every country above 10% [S1]. Local surveys are worse. Of 571 Afridev sites mapped in Kwale County, Kenya, in 2013, 41.7% were not producing water [S2]. The same 30-year cohort study found failure risk rose with salinity, deeper static water levels, sand aquifers, and **distance to spare-part suppliers** [S2]. Distance to spare parts is a logistics problem that information can help with.

**How long repairs take.**
- In Kyuso, Kenya, before the new service, the mean downtime was 27 days [S4].
- A 2013 survey of 440 Afridev pumps in Kwale estimated 37 days of downtime per breakdown at pumps that were working again, and 85 days at those that were not [S4].
- In rural Rwanda, under the existing ("nominal") maintenance, the median time to successful repair was about 152 days [S5].

**Why speed matters for health.** In Kenya, a study linked repair data to household surveys. Only repairs within 24 hours were associated with lower reported diarrhoea: a 60% reduction for households whose pumps were consistently fixed within a day. Slower repairs showed no reduction [S7]. This is observational and self-reported, but it suggests that "fixed eventually" is not the same as "safe".

## 2. Three options, compared briefly

| | Handpump-failure sensor (texts the maintainer) | Solar pump retrofit on an existing borehole | Inline chlorine doser |
|---|---|---|---|
| What it fixes | Slow discovery and dispatch of repairs | Labour of pumping, and volume | Microbial contamination of water that is flowing |
| Evidence in African handpump settings | Kenya: downtime 27 → <3 days with sensors plus a paid mechanic [S4]. Rwanda: median repair ~152 → ~21 days, functionality ~68% → ~91% [S5] | Benin feasibility project; retrofit cost is an **estimate**: about USD 1,000 for the device and USD 2,000–5,000 for a full solarisation, against USD 8,000–20,000 for conventional solarised systems with a tank [S10] | Strong health trial, but on **shared taps on piped supply** in Bangladesh: child diarrhoea 10.0% vs 7.5%, PR 0.77 [S11]. For handpumps the proven model is a manual dispenser *beside* the pump, not inline [S12] |
| Unit cost (this study) | ~US$25 of electronics at qty 100–1000 plus enclosure and cells (section 4.10) | USD 2,000–5,000 (estimate) [S10] | Dispenser programmes report < US$1.70 per person per year (self-reported) [S12] |
| Recurring cost | One SMS a day, cells every ~2 years, and above all a funded repair service | Pump and controller repair, tank, theft risk of panels | Chlorine supply and refills by circuit riders [S12] |
| Fits on a handpump? | Yes, as a retrofit on the handle, with no change to the water path [S3] | Replaces or bypasses the hand mechanism | Inline dosing needs a pipe and a flow. A handpump spout gives neither, so a manual dispenser is the realistic form |
| Biggest risk | Data with no responder (see the nominal arm of [S5]) | Capital cost; a more complex machine to keep running | Taste rejection, dosing errors, supply chain |

**The pick: the failure sensor.** It addresses the most common failure, a broken pump that nobody fixes quickly. It costs one to two orders of magnitude less per waterpoint than a solar retrofit [S10]. It has the most direct African field evidence of all three options [S4][S5]. The chlorine idea is worthwhile, but for handpumps it is already solved by a non-electronic dispenser with its own delivery network [S12]. An electronic inline doser on a handpump would be an answer looking for a pipe.

The sensor's weakness is also its ethical centre: **it does nothing on its own.** In the Rwanda trial every arm had sensors, but only the "ambulance" arm's operator saw the data and dispatched technicians [S5]. The design and the ethics below are built around that fact.

## 3. What earlier sensor projects teach

- **Oxford Smith School "Smart Handpump" (Zambia, then Kenya).**
  - The 2011 proof of concept put an accelerometer, a microprocessor and a GSM modem on the handle. It estimated volume to within about 10% on India Mk II pumps in Lusaka [S3].
  - Its battery lasted "of the order of a day", and the authors named power consumption as the biggest remaining technical challenge [S3].
  - By the 2013 Kyuso trial, the transmitter sat inside the handle and sent data by SMS. Batteries were changed every two years, and "information costs", meaning messages plus a replacement battery, were 27% of recurring maintenance costs [S4].
  - The trial report says one daily message carrying six four-hour blocks would be enough for operations [S4]. This design adopts that.
  - The report also found that mobile signal was often present in the village but absent at handle height in the depressions where pumps sit [S4]. Antenna placement matters.
- **Maintenance enterprise.** The service continued as FundiFix. In February–June 2015 it made 56 repairs to 18 pumps with a mean downtime of 1.1 days [S8]. A 2015 university news release put the share of pumps out of action at any one time at "up to one third" [S9].
- **Rwanda sensors.** These were built into Afridev and India Mk II pumps and combined an accelerometer with a water-pressure transducer [S5]. The headline result is about service models, not hardware: the three arms cost roughly the same per functional pump-year, but only the arm that acted on the data delivered fast repairs [S5].
- **Fault prediction.** Later Oxford work used a higher-rate accelerometer and an on-board novelty filter with cloud models to predict failures [S6]. I saw only a search-engine summary of that paper, so this study does not depend on its numbers (UNVERIFIED).

## 4. Design

### 4.1 What it measures, and what it deliberately does not

- **It measures** handle motion: a 3-axis accelerometer at 10 samples per second. Firmware turns this into a stroke count and a simple stroke "signature": swing amplitude and stroke period. It also measures battery voltage, board temperature (from the accelerometer's internal sensor), and cellular signal quality at report time.
- **It sends** one SMS a day containing:
  - the waterpoint ID;
  - the date;
  - stroke counts in six 4-hour blocks;
  - a status code (OK / SILENT / SIGNATURE-CHANGE / TAMPER / LOW-BATT);
  - battery voltage and signal quality.

  That fits in one 160-character SMS. An unscheduled alert is sent the same day for SILENT, SIGNATURE-CHANGE or TAMPER.
- **It never collects** location fixes (the pump's position is recorded once at installation, on paper and in the registry), audio, images, Bluetooth or Wi-Fi scans, phone identifiers of passers-by, or anything that identifies a person. There is no hardware for any of these on the board.

**Failure logic (simple enough for an 8-bit MCU).**
- *SILENT*: zero strokes for a configurable window (default 24 h), at a pump whose 14-day median is above a threshold. Rain-driven drops in use are real [S7], so SILENT means "please check", not "broken".
- *SIGNATURE-CHANGE*: many strokes, but with a shorter period and larger swing than the pump's own baseline. That is the pattern you would expect if the handle swings freely, for example from a disconnected rod or a failed foot valve. Whether a simple threshold catches this reliably is UNVERIFIED. Earlier work used logistic regression and cloud models [S6].
- Volume is *not* claimed. Stroke-to-volume calibration worked within ~10% on one pump type in one city [S3], but this design reports strokes only, unless a local calibration is done.

### 4.2 Block diagram (in words)

```
 3 x D alkaline (4.5 V fresh) --F1 2 A 5x20 fuse--Q1 P-FET (reverse polarity)--+-----------------------------+
                                                                               |                             |
                                                     U5 TPS7A0230 3.0 V LDO (25 nA)    Q4 P-FET load switch (MCU-controlled, soft-start)
                                                                               |                             |
                                              always-on 3.0 V rail             |            6 x 470 uF polymer reservoir (2.82 mF)
                         +-------------------+-------------+-------------------+                             |
                   U1 ATmega328P-PU     U2 LIS2DH12    S1 reed switch                     U4 TPS63802 buck-boost -> 3.8 V
                   (DIP socket, 32 kHz  (INT1 -> MCU)  (magnet wake)                                         |
                    crystal for RTC)                                                     2 x 470 uF polymer at VBAT
                         | UART (3.0 V)                                                                      |
                   U6 TXS0104E level shifter <------- 1.8 V VDD_EXT ----------- U3 SIMCom A7672E (LTE Cat-1 + GSM)
                                                                                                             |
                                                                         nano-SIM (ESD-protected)    u.FL -> FPC antenna
```

### 4.3 The parts, and why each one (plain electronics)

- **MCU: ATmega328P-PU in a DIP socket (C33901) [S22][S28].**
  - It is the chip inside the classic Arduino Uno, so in many towns someone can program one, and a dead chip is pulled and replaced by hand.
  - Datasheet power-save current is 0.75 µA with the 32 kHz RTC running (quoted at 1.8 V) [S22]. That suits a device that sleeps 99.9% of the time.
  - The 32.768 kHz watch crystal (C93229, a through-hole "DT-26" can) runs Timer2 as a real-time clock, so the MCU knows when to send the daily SMS without waking.
  - It clocks from the internal 8 MHz oscillator divided down to 1 MHz while counting strokes. Datasheet active current is 0.2 mA at 1 MHz, 1.8 V [S22]. The 3.0 V figure used below is an assumption (UNVERIFIED).
- **Accelerometer: ST LIS2DH12 (C110926) [S21].**
  - In low-power mode it draws 3 µA at 10 Hz [S21]. That is enough to see a handpump stroke, which takes on the order of a second (the stroke rate itself is UNVERIFIED).
  - It has a FIFO buffer and a motion interrupt. The MCU sleeps until the handle moves, then reads 32 samples at a time (3.2 s at 10 Hz) instead of waking for every sample.
  - Its 2 × 2 mm LGA package is not hand-solderable in a village. Repair at board level is the plan (section 4.9).
  - The ADXL362 (C59641) and BMA400 (C437655) are lower-power alternatives but cost US$8–10 at qty 100 [S28]. They are not worth it when the radio dominates the budget.
- **Modem: SIMCom A7672E-FASE (C5370391), LTE Cat-1 with GSM 900/1800 fallback [S19][S28].**
  - Why Cat-1 and not 2G-only: 2G is on its way out (section 4.6). Why not NB-IoT/LTE-M: these are barely deployed in Africa [S18].
  - Cat-1 is ordinary 4G, so it works wherever a 4G phone works, and the GSM fallback keeps it working where only 2G reaches.
  - Its supply window is 3.4–4.2 V, and it can draw **2 A peaks** in GSM transmit [S19]. That one fact drives the power design in 4.5.
  - Its UART runs at 1.8 V [S19], hence the level shifter U6 (TXS0104E, C44955).
  - Its *power-off* leakage is 20 µA [S19]. That alone would be four times the whole sleep budget, so **the modem is never merely "off": its whole power domain is disconnected.**
  - A cheaper A7670E-LASC (C46467016, US$7.08 at qty 100) shares the same band table [S19] but had only 19 in stock [S28].
- **Modem supply: TI TPS63802 buck-boost (C2845237) with a 0.47 µH, 7 A-saturation inductor (C329408) [S23][S28].**
  - A buck-boost converter can make a steady 3.8 V whether the battery is above it (fresh, 4.5–4.8 V) or below it (aged, ~3.3 V). A plain regulator cannot.
  - It is rated for 2 A out at 3.3 V for inputs of 2.3 V and up [S23]. 2 A at 3.8 V out from a sagging input is UNVERIFIED and must be bench-tested.
  - Shutdown current is 45 nA typical [S23].
- **Always-on rail: TI TPS7A0230 3.0 V LDO (C3747031), 25 nA quiescent [S24].** It powers the MCU and accelerometer, which need under 3.6 V. At micro-amp loads an LDO wastes almost nothing, and its own 25 nA is negligible.
- **Reservoir capacitors: 8 × 470 µF / 6.3 V polymer (C2943987), 16 mΩ ESR each [S25].** Six sit on the battery side of the converter and two at the modem's VBAT. SIMCom asks for at least 300 µF, or 600 µF if the supply cannot itself provide 2 A [S19]. The arithmetic in 4.5 shows why this design needs more.
  - **Catch found in the datasheet:** polymer capacitors of this size are specified at up to 0.2CV, which is **592 µA each** [S25]. Eight of them left on the battery could, in the worst case, leak 4.7 mA, about a thousand times the sleep budget.
  - That is why they sit *inside* the switched modem domain, behind load switch Q4 (DMG2305UX, C150470). Q4 gets an RC gate ramp to limit inrush into 3.8 mF. The RC values are not computed here (UNVERIFIED).
- **Protection.**
  - Q1, a second DMG2305UX P-FET, guards against reverse polarity. Fitted "backwards", it conducts with the cells the right way round and blocks if they are reversed, without the 0.3–0.7 V loss of a series diode.
  - F1 is a standard 5 × 20 mm 2 A time-lag glass fuse (C142702) in a clip holder (C41371862). Any electrical shop can match the size, and a spare rides in the box.
  - Four TPD1E10B06 ESD diodes (C48260) protect the SIM lines.
- **Technician interface.**
  - A sealed reed switch (C150532) wakes the unit when a magnet is held to a marked spot on the lid, so the box needs **no hole for a button**.
  - The LED then blinks signal strength and status.
  - A 2 × 3 ISP header and a 1 × 6 serial header (C65114, C42431790) allow reflashing with a cheap USB programmer.

### 4.4 Power and battery-life arithmetic

Assumptions are labelled. Every datasheet figure is cited above. The script output is reproduced exactly:

```
  ATmega328P power-save + 32 kHz RTC (0.75 uA @1.8 V; 1.0 assumed @3.0 V)      1.000 uA
  LIS2DH12 low-power mode, 10 Hz ODR                                           3.000 uA
  TPS7A0230 quiescent                                                          0.025 uA
  TPS63802 shutdown (typ)                                                      0.045 uA
  TXS0104E with modem side unpowered (assumed)                                 1.000 uA
  Modem domain (load switch off)                                               0.000 uA
  TOTAL sleep                                                                  5.070 uA  -> 0.122 mAh/day
  Pumping-time processing: 6750 wakes x 10 ms x 0.5 mA = 0.0094 mAh/day
  Battery-current factor for modem rail: 3.8/(4.0x0.90) = 1.056
  typical (30 s at 120 mA)             session   1.06 mAh/day, total   1.19 mAh/day ( 0.43 Ah/yr) | 3 x D alkaline, derated 10 Ah:  23.1 yr | 3 x AA alkaline, derated 1.2 Ah:   2.8 yr
  poor signal (120 s at 250 mA)        session   8.80 mAh/day, total   8.93 mAh/day ( 3.26 Ah/yr) | 3 x D alkaline, derated 10 Ah:   3.1 yr | 3 x AA alkaline, derated 1.2 Ah:   0.4 yr
  worst, timeout (180 s at 320 mA)     session  16.89 mAh/day, total  17.02 mAh/day ( 6.21 Ah/yr) | 3 x D alkaline, derated 10 Ah:   1.6 yr | 3 x AA alkaline, derated 1.2 Ah:   0.2 yr
  GSM burst: input peak 2.71 A, input mean 0.43 A; reservoir 2.82 mF; droop 0.47 V over 577 us
  Pack IR 0.45 ohm: drop at mean 0.43 A = 0.20 V; if battery had to give the full peak: 1.22 V
  Pack IR 0.9 ohm: drop at mean 0.43 A = 0.39 V; if battery had to give the full peak: 2.44 V
  Polymer cap leakage spec (0.2CV): 592 uA max each, x8 = 4.74 mA if left on the battery
```

How to read this:
- **Sleep (~5 µA → 0.12 mAh/day)** is small. 3 µA of it is the accelerometer watching the handle. The 1 µA assumed for the level shifter with its modem side unpowered is a guess (UNVERIFIED). If it is worse, the shifter can also be switched.
- **Stroke processing is negligible** (0.009 mAh/day for 6 hours of pumping). Waking 6,750 times for 10 ms at about 0.5 mA is under one hundredth of a milliamp-hour.
- **The daily radio session is everything.** The datasheet does not give the energy of "power up, register, send one SMS, power down"; its sleep and idle figures are literally "TBD" [S19]. So three cases are modelled:
  - **Typical:** 30 s at 120 mA.
  - **Poor signal:** 120 s at 250 mA.
  - **Firmware hard timeout:** 180 s at 320 mA, where 320 mA is the datasheet's GSM talk current [S19].

  All three are assumptions (UNVERIFIED) and the first thing to measure on a prototype.
- **Cells.** The Energizer E95 D-cell datasheet gives a capacity chart (axis to 24,000 mAh at 21 °C), a fresh internal resistance of 150–300 mΩ, an operating range of −18 °C to 55 °C, and a 10-year shelf life at 21 °C [S27]. Exact chart values could not be read from the text (UNVERIFIED).
  - I derate to **10 Ah usable**. That allows for stopping at about 1.0 V per cell, pulse loads, heat, self-discharge and the real chance of buying low-grade or counterfeit cells. The derating is a judgement, not a datasheet number (UNVERIFIED).
  - The 3 × AA variant uses 1.2 Ah derated (UNVERIFIED, no AA datasheet was read).
- **Result.**
  - On D cells: about **3 years with poor signal, 1.6 years worst case**.
  - On AA cells: only months unless the signal is good.
  - The "23 years" for good signal is arithmetic, not a promise. Shelf life is quoted at 21 °C [S27], and a box on a sunlit handle will run much hotter (UNVERIFIED).
  - **Recommendation: change the cells every two years at a scheduled service**, which is what the Kyuso trial did [S4]. Add a LOW-BATT flag when the loaded pack voltage falls below 3.6 V.

**Why not solar plus a rechargeable cell?** It would remove the cell budget. But a panel on a handpump is the most stealable, breakable and shaded part of the system, and a rechargeable cell needs a charger and a BMS: more parts to fail. The arithmetic says three D cells last a service interval even with poor signal, so the simpler choice wins. A solar variant stays open as a growth path for sites that need more frequent reports.

### 4.5 The GSM burst problem, and the reservoir

In GSM mode the modem draws up to 2 A for 577 µs in each 4.6 ms frame [S19]. At 3.8 V out and a 3.3 V aged pack with 85% converter efficiency (assumed), the converter would pull **2.71 A** from its input during the burst. The mean over a frame is closer to 0.43 A, scaled from the 320 mA talk current [S19].

- If the cells had to supply the peak: 2.71 A × 0.45 Ω (three fresh cells at 150 mΩ) = **1.2 V drop**; at 300 mΩ per cell it is **2.4 V**. The modem would brown out and reset.
- With the 2.82 mF battery-side reservoir supplying the difference: ΔV = (2.71 − 0.43) A × 577 µs / 2.82 mF ≈ **0.47 V** per burst. The cells only supply the 0.43 A mean, a drop of 0.2–0.4 V.
- So a fresh-to-mid-life pack copes. An aged pack under 3.6 V loaded should be restricted to LTE, where the datasheet's highest listed transmit current is 600 mA [S19]. That is a firmware rule: allow GSM fallback only while the pack is healthy.
- In LTE-only operation the reservoir is comfortably oversized.

### 4.6 Connectivity: the 2G/SMS sunset and the alternatives

- **The risk is real and dated.**
  - A GSMA roadmap reported on 25 September 2026 lists 11 African countries to phase out 2G and 3G by 2030: Kenya, Uganda, DRC, Egypt, Ethiopia, Ghana, Niger, Nigeria, Rwanda, Senegal and South Africa [S16].
  - In South Africa, MTN plans 3G off at the end of 2026 and 2G off by the end of 2029 [S17].
  - A 2G-only modem such as the SIM800C (C2981431, still listed as active and in stock [S28]) is cheap but could be stranded within the battery life of a few units. It is rejected for new builds.
- **The low-power cellular options are not there yet.** LTE-M and NB-IoT were designed for sensors, but as of November 2025 the deployment list shows **two NB-IoT networks and zero LTE-M networks in all of Africa** [S18].
  - The footprint option is a SIMCom SIM7080G-M (C18548266, Cat-M/NB-IoT, 3.2 µA PSM, 2.7–4.8 V supply) [S20][S28]. It suits Kenya or South Africa if the operator confirms SMS or a data path for it.
- **Chosen path: LTE Cat-1 with GSM fallback.** It works on the networks that ordinary 4G phones use, and it keeps working on 2G until 2G is gone.
- **SMS stays the payload.** It is the most universal, auditable and cheap-to-read channel: a committee member's basic phone can receive the same message as the maintainer's server.
  - Whether SMS is supported over LTE on each target network is an operator question (SMS over SGs or IMS) and is UNVERIFIED per network.
- **Fallbacks if cellular fails at a site.**
  - (a) Move the antenna up. Kyuso found signal at standing height but not at handle height in a hollow [S4]. A 1–2 m lead to a small antenna on a post is cheap. This uses an SMA bulkhead (C496549, in large stock [S28]) instead of the internal FPC antenna.
  - (b) A community LoRa gateway serving many pumps (needs backhaul; not costed here).
  - (c) Satellite IoT (not costed; UNVERIFIED).
  - (d) No radio at all: the same box shows a "check pump" LED for the committee, which then phones the mechanic. That still beats silence.

### 4.7 Enclosure, mounting, tamper and theft

- **Mounting.**
  - Earlier work put the transmitter on or inside the handle [S3][S4]. This design uses a small steel or thick-ABS box clamped under the handle with two U-bolts and security fasteners (pin-Torx or one-way heads). That avoids drilling the handle, which would void pump warranties and weaken it.
  - Clamp dimensions for Afridev and India Mk II handles are not checked (UNVERIFIED), and neither is the effect of the extra mass on pumping.
- **Sealing.**
  - A gasketed lid gives an IP65/IP67 target (no specific enclosure part was costed; UNVERIFIED).
  - The board is conformal-coated, **not potted**. Potting improves water resistance but makes repair impossible, which contradicts section 4.9.
  - A desiccant sachet goes in at each two-yearly cell change.
- **Heat.**
  - White paint or a separate sunshade over the box.
  - The cell pack sits in the shaded lower half.
  - Alkaline cells are rated only to 55 °C [S27]; the real box temperature is UNVERIFIED.
  - If field logs show regular overheating, move the cells into the pump pedestal on a short cable.
- **Steel box and the antenna.** A steel box blocks radio, so with a steel box the FPC antenna must sit under a plastic window, or the external SMA antenna is used. The W3907's 55%/49% efficiency figures are for its datasheet test conditions [S26], not inside a box on a steel handle (UNVERIFIED).
- **Theft.**
  - Nothing inside is worth much on resale. There is no solar panel, and the cells are worth more out of the box than the electronics are.
  - The best defence is social: the committee knows what it is, knows it is theirs, and knows it brings the mechanic faster. A clear label in the local language helps: "Water committee property. Sends pump status only. No camera, no microphone, no tracking."
- **Tamper detection.** The accelerometer knows the box's orientation. If the box is removed or rotated far from its installed angle, the unit sends TAMPER once and keeps working. False alarms from rough use are UNVERIFIED.

### 4.8 Firmware behaviour (summary)

1. Sleep in power-save with the RTC running and the accelerometer interrupt armed.
2. On motion: read the FIFO, count strokes by zero-crossings of the filtered tilt angle, update the stroke-signature statistics, and sleep again.
3. Once a day at a randomised minute, to spread network load: close the load switch, wait for the reservoir to charge, power up the modem, register (LTE first; GSM only if the pack is healthy), send one SMS, power down cleanly (AT+CPOF, then open the switch). This is the shutdown order the hardware guide recommends [S19].
4. Hard timeout per session (default 180 s). On failure, retry next day and keep up to 14 days of 4-hour blocks in EEPROM, so nothing is lost while signal is down.
5. Configuration (report time, thresholds, maintainer number) by SMS **only from numbers on the committee's allow-list**, and always echoed by SMS to the committee's number, so no setting changes silently.

### 4.9 Local repairability

- **Repair tiers.**
  - *Committee level:* change cells (D alkaline, sold everywhere), change the 5 × 20 mm fuse (a standard size), dry and re-gasket the box, move the antenna.
  - *Local technician:* swap the socketed ATmega328P-PU (C33901; also sold as an Uno spare), reflash over the ISP header with a cheap USB programmer, replace the screw-terminal battery lead, the SMA lead or the reed switch, all through-hole.
  - *Board swap:* the modem, the accelerometer and the buck-boost converter are fine-pitch SMD. When those fail, the whole board is swapped. At ~US$25 of parts that is cheaper than a mechanic's motorbike trip in Kyuso, where a repair cost USD 62 on average [S4].
- **Printed documentation in the box:**
  - a one-page schematic;
  - the SMS format;
  - the LED codes;
  - the committee's own phone numbers.

  A short repair guide in the local language covers cells, fuse, antenna, reflashing and board swap.
- **Common substitutes.**
  - MCU: ATmega328P-AU (C14877; 0 in stock at LCSC when read, but common) on an adapter.
  - Modem: A7670E variants (same band table [S19]).
  - Accelerometer: LIS3DH-class parts (not checked).
  - Fuse: any 5 × 20 mm 2 A time-lag.
  - Polymer capacitors: any low-ESR 470 µF, 6.3 V or higher.
  - The SIM is an ordinary nano-SIM from any operator.

### 4.10 Bill of materials and cost

Read from LCSC on 18 November 2026 [S28]. All lines are listed by LCSC as "normal" lifecycle (active). A distributor label is not a manufacturer lifecycle statement: confirm each part as Active (not NRND, EOL or obsolete) on the manufacturer's own product page before ordering. Per-board cost respects LCSC minimum-buy quantities and price breaks, so the qty-1 column includes buying full minimum lots.

| Ref | Function | LCSC | MPN | Maker | Qty | LCSC stock | Lifecycle | Min buy | $/board @1 | @100 | @1000 |
|---|---|---|---|---|---|---|---|---|---|---|---|
| U1 | MCU, socketed DIP | C33901 | ATMEGA328P-PU | MICROCHIP | 1 | 538 (stock < 1000-board need) | normal | 1 | 2.49 | 2.222 | 2.189 |
| XU1 | 28-pin DIP socket | C5289491 | IC-28P | ZHOURI | 1 | 901 (stock < 1000-board need) | normal | 17 | 0.98 | 0.057 | 0.038 |
| U2 | Accelerometer | C110926 | LIS2DH12TR | ST | 1 | 9025 | normal | 1 | 0.90 | 0.541 | 0.495 |
| U3 | LTE Cat-1 + GSM modem | C5370391 | A7672E-FASE | SIMCom Wireless Solutions | 1 | 831 (stock < 1000-board need) | normal | 1 | 12.74 | 12.450 | 12.450 |
| U4 | Buck-boost 3.8 V modem rail | C2845237 | TPS63802DLAR | TI | 1 | 17608 | normal | 1 | 0.93 | 0.598 | 0.555 |
| L1 | 0.47 uH inductor for U4 | C329408 | MHCI04012-R47M-R8AU | Chilisin | 1 | 1607 | normal | 1 | 0.26 | 0.195 | 0.181 |
| U5 | 3.0 V always-on LDO | C3747031 | TPS7A0230PDBVR | TI | 1 | 27020 | normal | 1 | 0.32 | 0.225 | 0.210 |
| U6 | 1.8 V/3.0 V UART level shifter | C44955 | TXS0104EPWR | TI | 1 | 23955 | normal | 5 | 1.92 | 0.307 | 0.218 |
| Q1,Q4 | Reverse-polarity P-FET; modem-domain load switch | C150470 | DMG2305UX-7 | DIODES | 2 | 26975 | normal | 1 | 1.02 | 0.565 | 0.519 |
| Q2,Q3 | PWRKEY driver, divider switch | C8545 | 2N7002 | JSCJ | 2 | 5499900 | normal | 50 | 0.80 | 0.032 | 0.025 |
| Y1 | 32.768 kHz watch crystal | C93229 | DT-26 32.768KHz | KDS | 1 | 30980 | normal | 5 | 0.58 | 0.094 | 0.075 |
| S1 | Reed switch (magnet wake) | C150532 | S1A050000 | Cosmo | 1 | 2427 | normal | 1 | 1.69 | 0.939 | 0.832 |
| F1h | 5x20 mm fuse holder | C41371862 | BLX-A-B | XFCN | 1 | 4835 | normal | 5 | 0.83 | 0.131 | 0.098 |
| F1 | 2 A time-lag 5x20 fuse (+1 spare) | C142702 | 0218002.MXP | Littelfuse | 2 | 3165 | normal | 5 | 0.72 | 0.194 | 0.170 |
| J1 | Nano-SIM holder | C7529385 | NANO SIM TL6P H1.35 | SHOU HAN | 1 | 18435 | normal | 5 | 1.01 | 0.160 | 0.119 |
| D1-D4 | ESD diodes (SIM lines) | C48260 | TPD1E10B06DPYR | TI | 4 | 299540 | normal | 20 | 0.90 | 0.147 | 0.117 |
| C1-C8 | 470 uF 6.3 V polymer bulk | C2943987 | KNE2470UF6.3V149EC0008 | KNSCHA | 8 | 1870 (stock < 1000-board need) | normal | 10 | 0.86 | 0.509 | 0.373 |
| J2 | u.FL antenna jack | C53433123 | U.FL-IPEX-1 | XUNPU | 1 | 12960 | normal | 10 | 0.61 | 0.048 | 0.037 |
| ANT1 | LTE FPC antenna 698-3600 MHz | C6750605 | W3907B0100 | PULSE | 1 | 19 (stock < 100-board need) | normal | 1 | 8.11 | 5.701 | 5.702 |
| J3 | Battery screw terminal 5.08 mm | C474952 | KF128-5.08-2P-AA | Cixi Kefa Elec | 1 | 52945 | normal | 5 | 0.97 | 0.152 | 0.110 |
| J4 | ISP header 2x3 | C65114 | 2.54-2*3P | BOOMELE(Boom Precision Elec) | 1 | 36220 | normal | 10 | 0.46 | 0.036 | 0.026 |
| J5 | Serial header 1x6 | C42431790 | PZ2.54-1X6P-H25 | JXTCONN | 1 | 5900 | normal | 20 | 0.67 | 0.033 | 0.022 |
| C9-C16 | 100 nF 0805 | C49678 | CC0805KRX7R9BB104 | YAGEO | 8 | 8865340 | normal | 20 | 0.28 | 0.056 | 0.035 |
| C17-C20 | 22 uF 0805 (U4 in/out) | C45783 | CL21A226MAQNNNE | Samsung Electro-Mechanics | 4 | 96560 | normal | 20 | 2.19 | 0.352 | 0.273 |
| C21-C23 | 10 uF 0805 | C15850 | CL21A106KAYNNNE | Samsung Electro-Mechanics | 3 | 1740080 | normal | 20 | 0.63 | 0.071 | 0.053 |
| R1-R8 | 10 k 0805 | C17414 | 0805W8F1002T5E | UNI-ROYAL | 8 | 34445900 | normal | 100 | 0.34 | 0.027 | 0.015 |
| R9-R12 | 1 k 0805 | C17513 | 0805W8F1001T5E | UNI-ROYAL | 4 | 31941700 | normal | 100 | 0.35 | 0.014 | 0.011 |
| LED1 | Red LED 0805 (technician status) | C84256 | NCD0805R1 | NATIONSTAR | 1 | 1946600 | normal | 50 | 0.68 | 0.014 | 0.011 |
| | **LCSC electronics subtotal** | | | | | | | | **44.22** | **25.87** | **24.96** |

**Not on LCSC, costs UNVERIFIED:**
- PCB fabrication and assembly (2-layer, about 60 × 50 mm, JLCPCB);
- enclosure, gasket, U-bolts and security screws;
- a 3 × D battery holder (none found on LCSC);
- three D alkaline cells;
- the SIM and its airtime;
- an optional external SMA antenna with a 1–2 m lead.

**Stock warnings for volume [S28]:**
- the W3907B0100 antenna had only 19 in stock (choose an SMA whip or another FPC antenna for runs of 100+);
- the A7672E-FASE (831), ATmega328P-PU (538), DIP socket (901) and polymer capacitors (1,870, at 8 per board) cannot cover 1,000 boards from current stock.

**Where the money goes:** at qty 100 the modem is US$12.45 and the antenna US$5.70, 70% of the US$25.87. Everything else together is about US$7.7.

## 5. Ethics

### 5.1 The central risk: a sensor that replaces a maintenance system instead of supporting it

Evidence first. In Rwanda, pumps in all three arms carried sensors. Where the operator did not use the data, median repair stayed at about 152 days, and only 22% of those pumps were working at the end of the study [S5]. In Kyuso, the gains came from a *bundle*: data, a dedicated local mechanic, spare parts and a payment model contingent on service [S4]. The sensor is a **dispatch aid, not a maintenance system.**

How this can go wrong, and the design rules against each failure:

- **Displacement.** A donor funds sensors instead of mechanics, spare parts or the community fund, because sensors photograph well.
  - *Rule:* no installation without a named, funded responder and a written response-time commitment.
  - The study recommends funders budget the sensor at no more than a small fraction of the per-pump maintenance cost. Kyuso's recurring repair cost was about USD 127 per handpump per year [S4].
- **Undermining the committee.** Alerts go straight to an NGO's server, and the water committee, the area mechanic and the caretaker are bypassed and stop doing their jobs.
  - *Rule:* every SMS goes **first to the committee's own number** and in parallel to the maintainer. The device supports the caretaker's routine; it does not replace the weekly check.
- **Upward accountability only.** The Kyuso report itself lists "remote surveillance of maintenance service delivery" as a benefit [S4]. Watching the *service provider* is legitimate, but it can slide into watching the *community*, for example who pays and who uses how much.
  - *Rule:* the data are about the pump and the service, never about individuals (5.3).
- **Abandonment when funding ends.** Hundreds of silent boxes on handles, telling no one anything.
  - *Rule:* the exit plan (who removes or adopts the units) is in the installation agreement, and the box works without a server (SMS to the committee's phone).

### 5.2 Who pays for SMS, cells and repairs

- **The costs are known in kind, not in local price.** One SMS a day is 365 SMS a year per pump; local bulk SMS prices are UNVERIFIED. Add cells every ~2 years and, much larger, the repairs themselves. In Kyuso, "information costs" were 27% of recurring costs, transport about 26% and spare parts 28% [S4].
- **The models seen in the evidence:**
  - *Community pre-payment for a guaranteed service.* In Kyuso, households were willing to pre-pay. Common existing fees were around USD 0.56 a month or USD 1.1 per m³ [S4]. A later case study reports communities stating willingness to pay about USD 10 a month collectively (secondary, UNVERIFIED) [S8].
  - *Results-based funding*, where a funder pays the service provider per waterpoint that meets an uptime target. The Uptime working paper used USD 50 per quarter per waterpoint above 96% uptime, plus a volume payment, plus a 1:1 match of local revenue [S13]. Sensors are one way to *verify* uptime [S13]. That creates an incentive to game the sensor, so the committee's own SMS copy is the check.
  - *Public budget* (district water office).
- **Recommendation.**
  - Airtime and cells belong in the **maintenance contract, paid by the service provider**, not charged per message to the community.
  - The community's payment buys *repairs*, with a public response-time promise.
  - Nobody should lose water access because a fee is unpaid: the pump is never locked, and the sensor has no ability to lock anything. This design deliberately has no actuator.
  - Where people cannot pay, the results-based or public model should cover them rather than leaving their pump unmonitored.

### 5.3 No surveillance of individual users

- **No personal data by design.** No camera, microphone, GPS fix, Bluetooth/Wi-Fi scanning or phone-identifier capture (4.1). The device cannot tell who pumps.
- **Timing is still sensitive.** Hourly stroke data at a remote pump can reveal *when someone is usually alone there*. Women and girls collect much of the water. For a series anchored on trafficking and violence, that is the risk to design out.
  - The design aggregates to **4-hour blocks** (as recommended for operations in [S4]) and sends once a day, so the data are always at least hours old.
  - Higher-resolution data stay on the device for diagnosis only and are overwritten after 14 days.
- **Purpose limitation and minimisation.** Kenya's Data Protection Act s.25 requires purpose limitation, data limited to what is necessary, limited retention, and safeguards on transfers abroad [S15]. Stroke counts at a public pump are probably not "personal data" on their own. Committee phone numbers and payment records are, so they belong in the maintainer's system under that law, not in the sensor.
- **No data trade.** Usage data must not be sold or shared for marketing, credit scoring, insurance pricing or political targeting. That goes in the agreement.

### 5.4 Community ownership of the data

- **A written data agreement, read aloud and signed at installation**, says:
  - (a) the water committee owns the pump's data;
  - (b) the maintainer and the district may use it to fix pumps and plan;
  - (c) what goes into open registries;
  - (d) how long data are kept;
  - (e) how the committee can see everything, get a copy, or have the unit removed.
- **Open data, the right slice.** WPdx accepts provider data under CC BY 4.0 and requires the provider to hold the rights [S14]. The committee's consent is therefore what allows publication.
  - The study recommends publishing only **functionality status and dates**, which is what registries need, and not stroke time series.
- **The committee gets the same data as everyone else**, in the same SMS, on its own phone. That symmetry is the practical form of ownership.

### 5.5 False reassurance: when "OK" is wrong

A daily "OK" can be trusted more than it deserves.
- **The pump works but the water is unsafe.** The sensor counts strokes; it says nothing about contamination, a cracked apron, or a rising nitrate or arsenic level. *Rule:* "OK" means "the handle is being used normally", and the SMS and training say so. Water-quality testing stays on its own schedule.
- **Strokes without water.** A worn cylinder seal can let people pump for longer to get less water before the pattern is clear enough for SIGNATURE-CHANGE (section 4.1). *Rule:* the caretaker's weekly check continues (section 5.1).
- **A dead or out-of-coverage box looks like a quiet pump.** If no SMS arrives, the maintainer cannot tell a broken pump from a broken sensor or a network gap. *Rule:* a missing daily SMS is itself an alert to the committee, never a sign that all is well.
- **Alerts that nobody answers.** If alerts go unanswered, the committee may stop reporting by voice because "the box will tell them". *Rule:* the named responder and response-time commitment in section 5.1 are a condition of installation.

## 6. Licence check

| Item | Licence (as read in the file or on the page) | Consequence for this design |
|---|---|---|
| This hardware design (schematic, PCB, enclosure drawings) | Recommend **CERN-OHL-S v2** [S29] | Anyone may build and modify it, but must share changes. A closed copy cannot be locked down. The modem, MCU and other bought parts fall under "Available Component" (1.7) if they are obtainable as physical parts with enough information to use them [S29] |
| Firmware written for this design | Recommend **Apache-2.0** [S34] | Permissive, with an explicit patent grant (section 3). Keeps reuse easy for local firms |
| ST `lis2dh12-pid` accelerometer driver | **BSD 3-Clause** [S30] | Compatible. Keep the copyright notice |
| AVR-LibC (C runtime for avr-gcc) | **Modified BSD** [S32] | Compatible |
| Optiboot bootloader (if a serial bootloader is used) | **GPL v2 with a "Bootloader Exception"** plus a maintainers' statement that applications are not affected [S31] | Usable. Changes to Optiboot itself must be shared. ISP-only programming avoids it entirely |
| KiCad symbol/footprint libraries | **CC BY-SA 4.0 with an exception** for designs that use them [S33] | Using the libraries does not force a licence on the board design |
| SIMCom modem firmware and documents | Closed. Hardware guide obtained from a third-party mirror [S19]. EULA and redistribution terms not read (UNVERIFIED) | The modem is a black box. Its firmware cannot be audited or fixed, which is a further reason to keep it powered off except for the daily send, and to use only standard AT commands so the modem can be swapped |
| Arduino AVR core and common GSM libraries | Not used. Their licence files were not checked (UNVERIFIED) | The firmware plan uses avr-gcc, AVR-LibC and plain AT commands to avoid LGPL linking questions on a microcontroller |

## 7. Limitations

- Nothing was built or measured. The radio-session energy is the dominant term and is entirely assumed (4.4).
- The stroke-signature fault detection is untested. The literature that worked used more data and more computation [S6].
- The trial evidence comes from Kenya and Rwanda, a few hundred pumps, a few years, and non-random arm assignment in Kyuso [S4] [S5]. It may not transfer to other institutions or pump types.
- The health link to fast repair is observational [S7].
- Network-sunset facts come from news reports of a roadmap, not the roadmap itself [S16][S17].
- Costs for the enclosure, PCB, cells, airtime and, above all, the maintenance service are not costed.
- The solar and chlorine options were compared from a small number of sources. The solar retrofit cost is a project estimate [S10], and the in-line chlorination trial was on piped taps, not handpumps [S11].

## 8. Open questions

1. What does one power-up, register and SMS session actually cost in mAh on the A7672E, on each target network, at handle height?
2. Can a simple threshold on swing amplitude and period distinguish a disconnected rod or failed foot valve from normal use on Afridev and India Mk II pumps?
3. What is the internal temperature of a white box under a handle at midday in the dry season, and does it shorten alkaline cell life below two years?
4. Will operators in each country deliver SMS from LTE-only devices once 2G is off?
5. Who will be the named, funded responder in each district, and what response time will they commit to in writing?
6. Will committees prefer the SMS on their own phone, a paper log, or both?

## 9. Growth path

- **Lower tier:** no radio, LED only, for sites with no signal. The committee calls the mechanic.
- **Higher tier:**
  - a water-presence sensor at the spout, as in the Rwanda design [S5], to confirm "strokes but no water";
  - a small solar panel and rechargeable cell for sites wanting several reports a day;
  - the NB-IoT/LTE-M modem footprint where networks support it [S18].

## 10. UNVERIFIED list

Every claim, part or figure below could not be confirmed this turn.

1. ATmega328P power-save current at 3.0 V. 1.0 µA is assumed; the datasheet headline is 0.75 µA at 1.8 V [S22].
2. ATmega328P active current at 1 MHz and 3.0 V. 0.5 mA is assumed; the datasheet headline is 0.2 mA at 1.8 V [S22].
3. TXS0104E supply leakage with its 1.8 V side unpowered (1 µA assumed).
4. A7672E energy per daily session (30 s at 120 mA, 120 s at 250 mA, 180 s at 320 mA are assumptions); the datasheet sleep and idle figures are "TBD" [S19].
5. TPS63802 efficiency at 3.8 V output (90% assumed for battery-life arithmetic, 85% for the burst case).
6. TPS63802 ability to deliver 2 A at 3.8 V output from a 2.5–3.3 V input (datasheet rating is 2 A at 3.3 V out for VIN ≥ 2.3 V [S23]).
7. Usable capacity of a D alkaline cell to 1.0 V under this load profile. The 10 Ah derating is a judgement, and chart values in [S27] could not be read.
8. AA alkaline usable capacity (1.2 Ah derated; no AA datasheet read).
9. Internal temperature of a sunlit enclosure on a handpump handle, and its effect on alkaline cell life and leakage.
10. Typical handpump stroke rate (used only to justify the 10 Hz sample rate).
11. That the stroke counting by tilt-angle zero-crossings described here works reliably on Afridev and India Mk II handles.
12. That a simple swing/period threshold detects disconnected rods or failed foot valves (the SIGNATURE-CHANGE alert).
13. Stroke-to-volume accuracy on pump types other than the India Mk II tested in Lusaka [S3].
14. The fault-prediction results of the later Oxford work (seen only as a search summary) [S6].
15. Per-message SMS prices in any target country.
16. SMS support over LTE (SMS over SGs/IMS) for an LTE-only device on each target network.
17. The meaning of the A7672E "FASE" versus "LASE"/"LASC" suffixes (firmware or voice variants).
18. That the third-party mirror of the A7672X/A7670X hardware guide [S19] matches SIMCom's current official release.
19. SIMCom firmware licence, EULA and documentation-redistribution terms, and therefore whether the module meets CERN-OHL-S "Available Component" in practice.
20. Antenna performance of the W3907B0100 inside or on a steel handle in a depression. The datasheet efficiency figures are for its own test setup [S26].
21. That a 1–2 m lead to an antenna on a post restores signal at sites where handle-height signal is absent.
22. Enclosure part, IP rating and cost (none chosen from a live page).
23. PCB fabrication and assembly cost at JLCPCB (not quoted).
24. A 3 × D battery holder part and cost (none found on LCSC).
25. Local retail price and quality, including counterfeit prevalence, of D alkaline cells in target markets.
26. Clamp dimensions for Afridev and India Mk II handles, and whether the added mass changes pumping effort.
27. Soft-start RC values for load switch Q4 charging 3.8 mF, and the resulting inrush current.
28. Typical (not maximum) leakage of the polymer capacitors. The maximum is 592 µA each [S25], which is why they are switched.
29. False-alarm rate of the orientation-based TAMPER alert.
30. LIS2DH12 motion-interrupt threshold and duration settings suitable for handle motion.
31. Volume supply: ATmega328P-PU (538 in stock), A7672E-FASE (831), DIP sockets (901), polymer capacitors (1,870) and W3907B0100 (19) cannot cover 1,000 boards from current LCSC stock [S28]; other distributors not checked.
32. Price and stock of an external SMA whip antenna and lead (not checked).
33. Country 2G/3G switch-off dates. The 2030 roadmap is from news reports [S16][S17], not from the GSMA document.
34. Completeness of the NB-IoT/LTE-M deployment list for Africa (as of November 2025) [S18].
35. Communities' stated willingness to pay about USD 10 a month (secondary source citing a study not read) [S8].
36. Current Uptime payment values (the 2020 working-paper values were read [S13]; later figures were not).
37. Evidence Action's cost and reach figures (self-reported) [S12].
38. The solar retrofit cost of USD 2,000–5,000 (a project estimate, not field data) [S10].
39. The size and causality of the 24-hour-repair health effect (observational, self-reported) [S7].
40. The licence of the Arduino AVR core and common GSM libraries (licence files not checked, and these are not used in the plan).
