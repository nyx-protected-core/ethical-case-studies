# Open, repairable, low-cost solar charge controller for off-grid households and clinics

Ethics case study: research and design only. Nothing here has been built, simulated on hardware, bought or tested. Every number marked UNVERIFIED in the text appears in the numbered list in section 10. Sources are cited as [S#] and listed in SOURCES.md.

Date of research: 18 November 2026. Live LCSC data checked 18 November 2026.

---

## 0. Summary

**Why this matters.** In 2024, 655 million people still had no electricity, and 563 million of them lived in Sub-Saharan Africa [S1]. Close to 1 billion people in low- and lower-middle-income countries are served by health-care facilities that have no electricity or unreliable electricity [S3][S4]. Off-grid solar now serves about 449 million people at Tier 1 or Tier 2 [S1]. The weak point of many of those systems is the battery and the little controller that is supposed to protect it. Field studies report lead-acid batteries in cheap systems dying in about a year instead of three to five, mostly because of deep discharge and missing or poor charge control [S7][S8]. Closed, glued, tamper-proof designs, often built that way to protect pay-as-you-go (PAYG) lock-out hardware, make local repair hard, and the result is e-waste [S5][S6][S11].

**What is proposed.** The design is an open-hardware synchronous-buck MPPT charge controller for 12 V batteries and 100–400 W of panel:

- **Architecture.** MPPT, not PWM. Above about 100 W the cheapest panels per watt are 60- and 72-cell modules, and a PWM controller would waste more than half their power on a 12 V battery (section 3.1). The power stage is one phase up to about 200 W and two interleaved identical phases for 400 W. Tiers are chosen by which parts are fitted, not by a different board.
- **Parts.** Every core part is listed as "normal" (not discontinued) on LCSC, with stock and price read from the live listing on 18 November 2026. Two through-hole gate drivers I first wanted (IR2104PBF, IR2184PBF in DIP-8) are listed by LCSC as discontinued ("stop_product"), so the SOIC-8 versions are used instead.
- **Repairability.** The MCU is a socketed DIP-28 ATmega328P that can be reflashed through a 6-pin header or replaced with the chip from a common Arduino-style board. The power devices are TO-220 parts that unscrew from the heatsink. Fuses are automotive blade fuses, terminals are screw terminals, and no part is smaller than 0805 or SOT-23.
- **Efficiency.** The estimate is about 96–97 % at 100–200 W and about 96 % at 400 W with two phases, with the arithmetic shown. The largest single loss is high-side switching. Inductor core loss is not included because the vendor data needed to calculate it was not available (UNVERIFIED).
- **Night drain.** Estimated at about 2.3 mA (about 0.7 Wh/day).
- **Battery safety.** Separate, physically selected profiles for flooded, AGM and LiFePO4. Lead-acid gets temperature compensation of −5 mV/cell/°C. LiFePO4 is never charged below +5 °C by default, and never below 0 °C under any setting. Hardware comparator trips act independently of firmware, the controller tolerates a BMS disconnect, and it checks for a chemistry mismatch.
- **PAYG ethics.** The open controller should never contain lock-out logic. Any finance module plugs in through a documented connector and can only switch a "non-essential" output. A hard-wired essential/medical output, together with battery protection and charging, can never be disabled for non-payment. Unlock codes work offline, data collection is minimal, and terms are clear. This keeps the credit-access benefit that the evidence supports [S21] while removing the worst harms reported in the field [S22][S23][S24].
- **Licences.** CERN-OHL-S v2 is recommended for the hardware, Apache-2.0 or MIT for the firmware, Optiboot (GPLv2 with a bootloader exception) for the bootloader, and avr-libc (Modified BSD) as the C library. The main vendor-SDK risk is avoided by using a part that needs no vendor SDK.

**Cost.** The priced LCSC lines add up to $59.13 per board at qty 1 (including minimum-order overage), $32.80 per board at 100 boards and $29.90 per board at 1000 boards. The PCB, heatsink, enclosure, small passives, fuses, assembly and test are **not** priced (UNVERIFIED). Stock of several parts is too low for a 1000-board run today.

**Biggest open questions.** These all need bench work, not more desk research:
1. Real switching loss with these FETs and drivers.
2. Inductor core loss and temperature rise.
3. Whether the reverse-battery fuse coordination works with the parts chosen.
4. The 2025 edition of IEC TS 62257-9-8. I read only a 2020 summary of its requirements.

---

## 1. Why energy access belongs in a series about ending human trafficking

Energy is not a trafficking intervention by itself. It matters here for two reasons that the sources support.

- **Livelihood and health.** Lighting, phone charging, refrigeration and powered tools underpin income and health. A clinic without reliable power cannot run a vaccine fridge or a delivery-room light [S3][S4]. Households pushed into crisis by illness or lost income are more exposed to exploitative "help". The ILO's 2025 forced-labour indicators note that debt can start as "wage advances or loans to cover recruitment fees or medical expenses in the absence of social protection" [S48].
- **Debt.** The ILO lists *debt bondage* among its eleven indicators of forced labour. It describes debt "deliberately manipulated to ensure it cannot be repaid", for example through unclear repayment terms or very high interest [S48]. PAYG solar is a form of consumer debt secured by remote lock-out. It is not debt bondage. But its design choices about transparency, lock-out, fees and data decide whether it moves people toward financial resilience or toward hidden, unmanageable debt. Section 5 treats this fairly and on the evidence.

---

## 2. Scale of the problem

### 2.1 Electricity access (Tracking SDG7, 2026 edition, data year 2024)

The latest edition I could find is **Tracking SDG7: The Energy Progress Report 2026**. It was published by the World Bank with the IEA, IRENA, UN DESA Statistics Division and WHO, and its electricity data are for **2024** [S1]. Its launch press release is dated 24 June 2026 [S2].

| Figure (2024 unless stated) | Value | Source |
|---|---|---|
| People without electricity, world | 655 million | [S1] |
| Global access rate | 92 % ("stalled") | [S1] |
| People without electricity in Sub-Saharan Africa | 563 million of the 655 million | [S1] |
| Three largest deficits | Nigeria 87 million, DR Congo 85 million, Ethiopia 57 million (about one-third of the global deficit together) | [S1] |
| Lowest national access rates | South Sudan 5 %, Chad 13 %, Malawi 16 % | [S1] |
| Rural access, world, 2010 → 2024 | 73 % → 85 % | [S1] |
| Rural deficit in Sub-Saharan Africa, 2010 → 2024 | 376 million → 447 million (the only region where it grew) | [S1] |
| People served by Tier 1–2 off-grid solar | about 449 million in 2024, up from 385 million in 2023 | [S1] |
| Share of off-grid solar users for whom it is first-time access | about 65 % | [S1], citing 60 Decibels 2025 |
| Households without access that can afford the monthly PAYG fee for a Tier 1 solar home system | only 22 % | [S1] |
| Off-grid solar investment in 2024 | USD 300 million, down 30 % year on year | [S1], citing GOGLA 2025 |
| Pace needed for universal access by 2030 | 1.35 percentage points per year, more than triple the current pace | [S1] |

The report also makes a point that matters directly for this design. Quality-verified off-grid solar products "tend not to be modular or interoperable, which makes it harder for off-grid products to access a larger system, especially during a PAYG repayment period" [S1]. A controller with an open, documented interface and a growth path from 100 W to 400 W answers exactly that.

A note on method: the 449 million figure is built from GOGLA-affiliate sales, assumed to be about 29 % of the market. It assumes product life of 1.5× the warranty, typically three years for solar home systems, and applies a discount for PAYG default and repossession [S1]. That is a modelled estimate, not a count.

### 2.2 Health facilities

The 2026 SDG7 report has no new statistic on how many health facilities lack power. I searched its full text and it only mentions health-facility electrification in general terms. The most recent dedicated figures I found are in the joint WHO / World Bank / IRENA / SEforALL report *Energizing health: accelerating electricity access in health-care facilities*, published 14 January 2023. It uses national survey data from 27 low- and lower-middle-income countries for survey years 2015–2022 [S3][S4]:

- In low- and lower-middle-income countries, about **12 % of health-care facilities in South Asia and 15 % in Sub-Saharan Africa have no electricity at all**. The figure for Latin America and the Caribbean is 8 % [S3][S4].
- Only about **40 %** of facilities in those Sub-Saharan countries had reliable electricity, and only about half of hospitals [S3].
- At least **25,000** health-care facilities in Sub-Saharan Africa have no electricity, and **68,350** have only unreliable electricity [S3].
- Across the four regions with enough data, **433 million** people rely on facilities without any electricity, and **478 million** on facilities without reliable electricity. That is at least **912 million** in total, which the report rounds to "close to 1 billion" [S3].

These health figures are older than the SDG7 2024 data and come from a different survey base. They should not be combined arithmetically with the household numbers.

---

## 3. Why cheap solar kits fail early

### 3.1 Battery abuse

The battery is almost always the first part to die, and the controller decides how it is treated.

- **Malawi, peer-reviewed, 50 interviews around Lilongwe** [S7]. "Lead-acid batteries were reported as the single greatest source of failure." The average reported battery lifetime was about one year, often under six months, against the three to five years expected in the literature. The authors link this to "the lack of charge controllers, regular deep-discharging, the lack of expertise in SHS design and installation, and often insufficient solar panel cleaning". The battery waste flow feeds an informal lead-recycling trade with "severe health risks" [S7].
- **Zambia, peer-reviewed field test of 13 batteries after one year in a fee-for-service programme** [S8]. Average usable capacity fell from 61 Ah in new batteries to 34 Ah in used ones (55 %). The authors note that "operating a solar system with shallow cycles in combination with a low state of charge is harmful to the battery". That is the pattern produced by undersized panels and by controllers that never complete an absorption charge.
- **Rwanda, peer-reviewed study of PV in health centres and schools** [S9]. Without ongoing maintenance, battery deterioration left systems unable to deliver their intended service. The authors propose behaviour change and larger arrays to improve lead-acid battery lifetime. I read only the abstract.
- **1,027 field lead-acid batteries, 400–760 days each, 620 million data rows** [S10]. End of life could be predicted eight weeks ahead with 73 % accuracy from ordinary operating data. This matters for an open controller: honest logging of battery voltage, current and temperature is enough for useful health diagnosis without any extra hardware.
- **GOGLA** calls the battery "the weakest component in the core system". It puts the typical design life of a lithium solar-home-system battery at about 2,500 cycles, or five to seven years [S11]. GOGLA also notes that batteries are "usually the first component to reach its end of life" and pushes for modular, easily replaced batteries [S12].

**What a controller must do about it.** It must use correct charge voltages for the chemistry, compensate for temperature on lead-acid, refuse to charge LiFePO4 when cold, enforce a low-voltage disconnect (LVD) that cannot be bypassed casually, and give honest state-of-charge feedback. The quality framework requires exactly this: a deep-discharge threshold of at least 2.00 V/cell recommended (1.95 V/cell minimum) for lead-acid, and an overcharge threshold of 2.40 V/cell recommended for lead-acid and at most 3.65 V/cell for LiFePO4 [S15].

### 3.2 Closed designs that cannot be repaired

- **Kenya, 730 products followed 2014–2016, plus ethnography at a repair workshop** [S5]. Broken solar products are kept, repaired, cannibalised and repurposed. The authors argue that decentralised repair cultures, plus better product design, can reduce solar waste more than central recycling alone.
- **Nature Energy perspective** [S6]. Off-grid solar products commonly use "proprietary screws and tamper-proof design layouts that are intended to limit third-party access and repair" and parts that are hard to source locally. "A more important factor in the inflexible design of OGS products is the way in which these devices are integrated with pay-as-you-go (PAYG) technologies." Unaffiliated products, although sometimes lower quality, "tend to be easier to repair or upgrade in the informal repair markets on which energy-poor households tend to rely" [S6].
- **GOGLA's scorecard repairability factors** [S11]: whether the battery can be replaced; board construction ("soldered vs. surface mounted") and availability of replacement boards; non-destructive disassembly; no glue, welding or one-way screws; tools available to small independent technicians; time to disassemble; and access to repair information. GOGLA also recommends "open-source manuals in local languages" [S12].

### 3.3 Counterfeit and substandard products

- Lighting Global lab-tested 17 best-selling non-quality-verified pico-solar products from Ethiopia, Kenya, Myanmar, Nigeria and Tanzania. **None** met the Lighting Global standards. 94 % had at least one durability deficiency (including battery protection), 88 % advertised performance inaccurately, and 88 % had no consumer warranty [S13].
- The same note and the program description estimate that about 70–71 % of the pico-solar market is non-quality-verified. The note's wording covers no-name products and copycats; I did not find a separate figure for counterfeits [S13][S14].
- A 2015 Lighting Global note on Kenya records finding "two striking S2 counterfeits" of a popular quality-verified lantern during surveys [S49]. I read this only as a search-result excerpt (UNVERIFIED, item U5).
- **Component-level counterfeits.** I found no source in this research quantifying counterfeit power MOSFETs or controller ICs in off-grid solar. The design still buys from a distributor that names the brand, adds an incoming check of on-resistance for every power MOSFET (section 4.8), and gives a list of substitute parts so a technician is never forced onto an unknown part (UNVERIFIED, item U6).

### 3.4 E-waste

The Malawi study found the waste stream "dominated by lead-acid batteries" and described active informal lead recycling in densely populated areas [S7]. GOGLA notes that lithium batteries "can only be recycled in Europe or Asia" [S11]. The design response:

- Make the battery last (section 4.5).
- Make the controller repairable so it does not become waste (section 4.7).
- Log battery health so a battery is replaced on evidence, not on a guess [S10].

---

## 4. Controller design

### 4.0 Target and ratings

| Item | Target | Why |
|---|---|---|
| Battery | 12 V nominal: 6-cell lead-acid (flooded or AGM) or 4-cell LiFePO4 with its own BMS | The most common off-grid battery bus |
| Panel | 100–400 W. One 36-cell "12 V" panel, or one 60- or 72-cell panel. Voc must stay ≤ 55 V at the coldest site temperature | Larger modules are the cheapest per watt, but they need MPPT (3.1) |
| Charge current | Up to about 28 A (400 W tier) | 400 W ÷ 14 V ≈ 28.6 A before losses |
| Load output | 20 A fused, with low-voltage disconnect | Lights, phone charging, a DC fridge |
| Growth path | Tier A ≤ 100 W, Tier B ≤ 200 W, Tier C ≤ 400 W on the same PCB. A 24 V battery doubles power at the same current (4.9) | The SDG7 report flags non-modular kits as a barrier [S1] |

**Input voltage ceiling.** A 55 V limit on panel open-circuit voltage leaves margin under the LM5050-1's 75 V operating maximum and 100 V transient rating [S29], the 100 V MOSFETs [S25][S26] and the 100 V input capacitors. A typical 72-cell module's Voc of about 45–48 V at 25 °C is an illustrative figure from general knowledge, not from a specific panel datasheet (UNVERIFIED, item U7). In a cold place it can rise several percent, so the installer must check the actual panel's Voc temperature coefficient against the site's minimum temperature.

**Surge margin.** The PV TVS (SMCJ58A) clamps at 93.6 V at its full 16 A pulse current [S35]. That is close to the 100 V ratings of the FETs and the LM5050-1, so the margin is thin. A lower-standoff TVS (for example a 48 V standoff part) with a 45 V Voc limit would widen it, at the cost of excluding some 72-cell panels in cold climates (UNVERIFIED trade-off, item U38).

### 4.1 PWM versus MPPT, honestly

**PWM (series switch).** The panel is connected straight to the battery through a switch that is pulsed to regulate voltage. The panel therefore operates at battery voltage, not at its maximum-power voltage, so the power delivered is roughly battery voltage × panel current, and panel current near battery voltage is close to short-circuit current.

**MPPT (DC-DC converter).** A buck converter lets the panel sit at its maximum-power voltage (Vmp) and converts the excess voltage into extra battery current, less converter losses.

Worked example. The panel figures are illustrative 36-cell and 60-cell values, not from a specific datasheet (UNVERIFIED, item U7):

| Case | PWM delivers | MPPT delivers (at about 96 % efficiency, section 4.3) |
|---|---|---|
| 36-cell, 100 W panel (Vmp 18 V, Imp 5.56 A, Isc 5.9 A), battery at 13.5 V | at most 13.5 V × 5.9 A = **79.7 W (≈ 80 %)**, using Isc as an upper bound on current | ≈ **96 W** |
| Same panel, battery at 13.0 V | at most 76.7 W (≈ 77 %) | ≈ 96 W |
| 60-cell, 300 W panel (Vmp ≈ 32 V, Isc ≈ 9.8 A), battery at 13.5 V | at most 13.5 × 9.8 = **132 W (≈ 44 %)** | ≈ 288 W |

Caveats that favour PWM:
1. In hot sun, cell temperature lowers Vmp. With a 36-cell panel the PWM penalty then shrinks. The size of that effect depends on the panel's temperature coefficient, which I did not source (UNVERIFIED, item U8).
2. PWM is cheaper, simpler and easier to repair. It has one switch, no inductor and no high-speed driver.
3. Below about 50 W with a 36-cell panel, a PWM controller is a defensible choice.

**Decision.** For 100–400 W the controller is **MPPT, using a synchronous buck**. The panels that make 100–400 W affordable are 60- and 72-cell modules, and those are unusable with PWM on a 12 V battery (44 % in the example above). The same firmware can also run "PWM-like" (fixed high duty, no tracking) as a degraded fallback if the MPPT loop or its sensing fails. That keeps lights on while waiting for repair.

### 4.2 Architecture

```
 PV+ ──F1(blade)──┬─TVS D1(58 V)                      ┌── Phase A: Q1 (high-side) / Q3 (low-side) / L1 ──┐
                  └─Q5 + LM5050-1 (ideal diode: blocks ├── Phase B: Q2 / Q4 / L2 (fitted for 400 W only) ┼── BAT+ ──(external fuse at battery)── Battery
                    reverse polarity and night         │       drivers: IR2184S ×2 (fixed 400 ns dead time)│
                    back-feed)                         └── C bulk 100 V ×6 + 2.2 µF ×6                     ┘
                                                                                                           │
                                                                       Load switch Q6 (P-FET) ──F2(blade)── LOAD+
 Ground side (common negative): BAT− ──R1 (1 mΩ, net battery current)── GND ──R2 (1 mΩ, load current)── LOAD−
 Control: ATmega328P-PU (socketed) + REF3030 + INA181A2 ×2 + LM2903 ×2 (hardware trips) + NTC on heatsink and at battery
 Aux: PV/BAT diode-OR → LM5163 (100 V buck) → 12 V (driver supply, switched by Q9) → MCP1703 → 5 V logic
```

Plain-terms explanation of each block:

- **Synchronous buck.** Q1 chops the panel voltage. L1 smooths it into a current. Q3 replaces the freewheeling diode, because a FET at a few milliohms wastes far less than a diode's ~0.5 V drop at 14 A. The ratio of battery voltage to panel voltage is the duty cycle D = Vbat/Vpv. Changing D moves the panel's operating point, and MPPT is a slow search for the D that gives the most battery current.
- **Why two phases rather than one large phase.** The highest-saturation inductor I found in stock on LCSC that fits the job is 10 µH with Isat 32.1 A (C3037600), according to its LCSC parameter [S35]. One phase at 400 W would peak at about 34 A (section 4.3), which is above that saturation current. Two phases at about 14 A each peak at about 21 A, well inside it. Driving the second phase 180° out of step also cancels part of the ripple current in the capacitors. And a technician repairing one phase can compare it with an identical healthy phase.
- **Drivers with built-in dead time.** The IR2184S has a fixed 400 ns typical dead time (280–520 ns) between turning one FET off and the other on [S27]. A firmware bug therefore **cannot** turn both FETs on together and short the panel ("shoot-through"). That safety is worth the extra loss of the dead time (section 4.3). The lower-cost IR2104S has the same feature at 520 ns, but its gate current is only 0.13/0.27 A [S28]. That would roughly triple switching loss, so it is listed only as an emergency substitute (4.7).
- **Low-side current sensing.** The shunts sit in the negative return, so the current-sense amplifiers always see a common-mode voltage near 0 V. A high-side shunt would see −12.8 V if the battery were connected backwards, which is outside the INA181's −0.3 V absolute maximum [S31]. The battery shunt measures net battery current (charge minus load), and the load shunt measures load current, so charge current = battery + load. The INA181 is bidirectional when its REF pin is biased to mid-scale [S31][S35].
- **MCU choice.** The ATmega328P-PU in DIP-28 [S34][S35] was chosen for repairability over performance:
  - It is socketed.
  - It is the chip on widely available Arduino-Uno-style boards, so a spare can be pulled from one (UNVERIFIED that local markets stock them; item U9).
  - It can be reflashed with Optiboot over a USB-serial cable or with an ISP programmer [S40][S42].
  - It needs no vendor SDK.

  Its limits are a 10-bit ADC and no hardware dead-time generator, which is why dead time sits in the driver. PWM resolution is coarse. With Timer1 in phase-correct mode, TOP = 128 at 16 MHz gives 16 MHz ÷ (2 × 128) = **62.5 kHz** with 128 duty steps (0.78 % per step). At D = 0.39 and Vpv = 36 V, one step moves the panel voltage by about 36 × 0.0078 ÷ 0.39 ≈ 0.72 V. Firmware can average between neighbouring steps over successive cycles (dithering) to get a finer effective resolution. Whether that is good enough near the maximum-power point has to be checked on the bench (UNVERIFIED, item U10). Two-phase interleaving uses the phase-correct trick of one non-inverted and one inverted compare output (OC1A/OC1B), which puts the two pulses 180° apart. It needs bench confirmation (UNVERIFIED, item U11).
- **Upgrade MCU.** For higher resolution (HRTIM) the board can take a daughter-card with an STM32G431 (C529355, LCSC "normal", 6,381 in stock, $4.82 at qty 1 [S35]). This is what the open LibreSolar MPPT 2420 HC design uses [S36][S37]. The cost is that the chip is an LQFP-48 and cannot be socketed cheaply.

### 4.3 Efficiency and loss budget (arithmetic shown)

**Operating point.** Vpv = 36 V, Vbat = 14.0 V, so D = 14/36 = 0.389. Switching frequency is 62.5 kHz. Inductor L = 10 µH (C3037600, DCR 2.6 mΩ, Isat 32.1 A per its LCSC parameters [S35]).

**Ripple per phase.** ΔI = (Vpv − Vbat) × D ÷ (L × f) = 22 × 0.389 ÷ (10 µH × 62.5 kHz) = **13.7 A peak-to-peak**.
- With the inductor at −20 % tolerance (8 µH): 17.1 A peak-to-peak, peak current 22.3 A at 13.7 A DC.
- At Vpv = 45 V and 8 µH: 19.3 A peak-to-peak, peak 23.3 A.
- Both stay below Isat = 32.1 A.

**Datasheet values used:**
- High-side FET IRFB4410ZPBF: RDS(on) max 9.0 mΩ, Qg 83 nC, Qgs 19 nC, Qgd 27 nC, Coss(ER) 420 pF [S26].
- Low-side FET IRFB4110PBF: RDS(on) max 4.5 mΩ, Qg 150 nC, Qgd 43 nC, Coss(ER) 820 pF, Qrr 94 nC at 25 °C, VSD max 1.3 V [S25].
- Driver IR2184S: 1.9 A / 2.3 A typical short-circuit output current, dead time 400 ns typical [S27].

**Assumptions** (all UNVERIFIED, items U12–U16):
- Hot RDS(on) = 1.6 × the 25 °C maximum.
- Switching transition about 100 ns each way, worked out as (Qgd + Qgs/2) ÷ gate current with a 2.2 Ω external gate resistor. That gives a gate current of about 0.55–0.66 A at the Miller plateau, estimated from the driver's short-circuit current, not from a measured curve.
- Body-diode drop of 0.8 V during dead time.
- Inductor DCR × 1.2 when hot.
- 2 mΩ of PCB copper and terminals in the battery path.
- About 0.25 W of controller overhead while charging.
- **Inductor core loss is excluded**, because the vendor page gives no core-loss data (UNVERIFIED, item U17).

**Formulas, per phase:**
- I_rms = √(I² + ΔI²/12)
- High-side conduction = D · I_rms² · R_HS,hot
- Low-side conduction = (1 − D) · I_rms² · R_LS,hot
- High-side switching = ½ · Vpv · (I_valley · t_on + I_peak · t_off) · f
- Dead time = V_SD · (I_valley + I_peak) · t_dt · f
- Reverse recovery = Qrr · Vpv · f
- Coss = ½ · (C_HS + C_LS) · Vpv² · f
- Gate drive = (Qg,HS + Qg,LS) · V_drv · f
- DCR = I_rms² · DCR_hot

| Loss (W) | 100 W, 1 phase, 22 µH | 200 W, 1 phase, 10 µH | **400 W, 2 phases, 10 µH** | 400 W, 1 phase (not allowed) |
|---|---|---|---|---|
| Phase DC current / ripple / peak (A) | 6.9 / 6.2 / 10.0 | 13.7 / 13.7 / 20.6 | 13.7 / 13.7 / 20.5 per phase | 27.1 / 13.7 / **33.9 > Isat** |
| High-side conduction | 0.28 | 1.14 | 2.27 | 4.20 |
| Low-side conduction | 0.22 | 0.90 | 1.79 | 3.30 |
| High-side switching | 1.55 | 3.09 | **6.16** | 6.09 |
| Dead-time body diode | 0.28 | 0.55 | 1.09 | 1.08 |
| Low-side reverse recovery | 0.21 | 0.21 | 0.42 | 0.21 |
| Coss | 0.05 | 0.05 | 0.10 | 0.05 |
| Gate drive | 0.17 | 0.17 | 0.35 | 0.17 |
| Inductor DCR | 0.16 | 0.64 | 1.27 | 2.34 |
| PV ideal-diode FET (IRFB4110, I_pv² × 7.2 mΩ) | 0.06 | 0.22 | 0.89 | 0.89 |
| Battery shunt 1 mΩ | 0.05 | 0.19 | 0.75 | 0.73 |
| PCB and terminals (assumed 2 mΩ) | 0.09 | 0.38 | 1.50 | 1.47 |
| Controller overhead (estimate) | 0.25 | 0.25 | 0.25 | 0.25 |
| **Total** | **3.38** | **7.79** | **16.84** | 20.79 |
| **Efficiency (excluding core loss)** | **96.6 %** | **96.1 %** | **95.8 %** | 94.8 % |

What the table says, in plain terms:
- **High-side switching is the largest single loss** at every tier. If the bench shows the transitions are slower than 100 ns, efficiency falls about 1 % for every extra ~65 ns at 400 W. The cheapest improvement is a lower gate resistor, checked against ringing.
- **Dead time costs about 1 W at 400 W.** That is the price of the hardware shoot-through protection, and it is kept on purpose.
- At **100 W with the 10 µH inductor**, ripple (13.7 A peak-to-peak) is about twice the DC current, so the current reverses every cycle. The 22 µH part (C2962789, Isat 15 A [S35]) is therefore fitted for Tier A: peak 10.0 A, or 10.8 A at −20 % tolerance, both below 15 A. It is **not** allowed at 200 W, because 13.7 + 3.1 = 16.8 A exceeds its Isat.
- The **one-phase 400 W column** is shown only to show why it is forbidden: its 33.9 A peak exceeds the 32.1 A Isat.
- **Thermal.** At 400 W each high-side FET dissipates about 1.14 + 3.08 ≈ 4.2 W. With RθJC = 0.65 K/W [S26], an insulating pad and a shared heatsink, the heatsink must be sized so that Tj stays well below 175 °C. The pad and heatsink thermal resistances were not sourced (UNVERIFIED, item U18).

**Capacitor ripple check.**
- *Input, single phase at 200 W:* I_rms ≈ I · √(D(1 − D)) = 13.7 × √(0.389 × 0.611) ≈ **6.8 A**.
- *Input, two interleaved phases at 400 W* (D < 0.5, approximate formula ignoring inductor ripple): I_rms ≈ (I_total/2) · √(2D(1 − 2D)) = 13.7 × √(0.778 × 0.222) ≈ **5.7 A**.
- *Input capacitor capacity:* six 100 V 220 µF caps (C2835679) are rated 1.25 A each at 100 kHz, 7.5 A in total [S35]. The six 2.2 µF ceramics carry the fastest edges. **Note:** the 200 W single-phase case uses 6.8 A of the 7.5 A, so all six electrolytics are fitted at every tier above 100 W.
- *Output:* ripple current per phase is 13.7/√12 ≈ 3.95 A RMS. The four 35 V 1000 µF caps (C122661) are rated 2.23 A each at 100 kHz, 8.9 A in total [S35].
- The share split between ceramics and electrolytics, and capacitor life at enclosure temperature, need measurement (UNVERIFIED, item U19).

**Night drain (battery side, panel dark).** Datasheet or LCSC figures:

| Item | mA |
|---|---|
| ATmega328P: idle at 8 MHz, 5 V, 1.2 mA typ [S34], 10 % of the time; power-down with WDT the rest (4.2 µA at 3 V typ, scaled ×1.5 for 5 V as an assumption) | 0.126 |
| INA181A2 ×2, 195 µA typ each [S35] | 0.39 |
| LM2903P ×2, 800 µA each (LCSC parameter) [S35] | 1.6 |
| REF3030 (UNVERIFIED, item U20) | 0.05 |
| MCP1703, 2 µA [S33] | 0.002 |
| 1 MΩ battery divider | 0.013 |
| Load P-FET gate pull-up, 100 kΩ, with load on | 0.128 |
| LM5163 no-load quiescent, 10.5 µA [S30] | 0.011 |
| **Total** | **≈ 2.3 mA**, about 56 mAh/day or 0.71 Wh/day at 12.8 V |

The gate drivers (IR2184S quiescent 1.0 mA typ each [S27]) are unpowered at night through Q9. Most of the night drain is the comparators. Keeping them powered whenever the load is on is a deliberate safety choice.

### 4.4 Bill of materials and cost (live LCSC data, 18 November 2026)

**How the BOM was checked.** Each line was read from the LCSC product-detail data behind the live product page: model, maker, package, stock, LCSC's lifecycle field ("productCycle") and the price ladder [S35]. Part numbers were first found through the JLCPCB/LCSC parts search [S47].

"normal" is LCSC's own lifecycle status. **I did not verify manufacturer lifecycle status (active / NRND / EOL) on the manufacturers' sites.** Microchip's product page did not render for a scripted fetch. Treat manufacturer lifecycle as UNVERIFIED for every line (item U21).

**Rejected because LCSC lists them as "stop_product":**
- IR2104PBF DIP-8 (C2959)
- IR2184PBF DIP-8 (C2994)

**How to read the price columns.** "$/board @1" is what one board actually costs, including any minimum-order overage. For example, the DIP-28 socket has a minimum buy of 17 pieces. "@100" and "@1000" are per-board costs when buying for 100 or 1000 boards at the matching LCSC price break.

Configuration priced: **Tier C (400 W, two phases)**.

| Ref | Function | LCSC C# | MPN | Maker | Package | Qty/board | LCSC stock | LCSC lifecycle | Min buy | $/board @1 | $/board @100 | $/board @1000 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| U1 | MCU, socketed | C33901 | ATMEGA328P-PU | MICROCHIP | DIP-28-300mil | 1 | 538 | normal | 1 | 2.49 | 2.22 | 2.19 |
| U1-S | DIP-28 socket 7.62 mm | C5289491 | IC-28P | ZHOURI | DIP-28 | 1 | 901 | normal | 17 | 0.98 | 0.06 | 0.04 |
| Y1 | 16 MHz crystal HC-49S | C16212 | X49SD16MSD2SC | YXC Crystal Oscillators | HC-49S | 1 | 17205 | normal | 5 | 0.45 | 0.07 | 0.05 |
| U2,U3 | Half-bridge driver 400 ns dead time | C2995 | IR2184STRPBF | Infineon | SOIC-8 | 2 | 3657 | normal | 1 | 9.99 | 6.84 | 6.38 |
| Q1,Q2 | High-side buck FET 100 V | C2657 | IRFB4410ZPBF | Infineon | TO-220AB | 2 | 4569 | normal | 1 | 1.51 | 0.83 | 0.70 |
| Q3,Q4,Q5 | Low-side buck FET + PV ideal-diode FET 100 V | C2650 | IRFB4110PBF | Infineon | TO-220 | 3 | 14974 | normal | 1 | 2.99 | 1.78 | 1.63 |
| U4 | Ideal-diode controller (PV reverse/night) | C129323 | LM5050MK-1/NOPB | TI | TSOT-23-6 | 1 | 1991 | normal | 1 | 1.21 | 0.78 | 0.68 |
| L1,L2 | 10 uH power inductor (Isat 32.1 A) | C3037600 | FC-SE2918-100M-28A | FANGCHENG | SMD | 2 | 1239 | normal | 1 | 3.97 | 2.56 | 2.31 |
| C1-C6 | 100 V 220 uF electrolytic (PV bulk) | C2835679 | NXA100V220M12.5*25 LO | SAMYOUNG | Through Hole,D12.5xL25mm | 6 | 290 | normal | 1 | 3.28 | 1.72 | 1.63 |
| C7-C12 | 2.2 uF 100 V X7R 1210 (PV HF) | C55151 | CL32B225KCJSNNE | Samsung Electro-Mechanics | 1210 | 6 | 19690 | normal | 1 | 1.51 | 0.97 | 0.95 |
| C13-C16 | 35 V 1000 uF electrolytic (battery side) | C122661 | UHE1V102MHD | Nichicon | Through Hole,D12.5xL25mm | 4 | 1572 | normal | 1 | 1.86 | 1.06 | 0.92 |
| R1,R2 | 1 mOhm 2 W shunt (battery, load) | C149567 | WSL25121L000FEA18 | VISHAY | 2512 | 2 | 11595 | normal | 5 | 0.97 | 0.26 | 0.20 |
| U5,U6 | Current-sense amp gain 50 | C2058784 | INA181A2IDBVR | TI | SOT-23-6 | 2 | 51215 | normal | 5 | 1.85 | 0.53 | 0.45 |
| U7,U8 | Dual comparator DIP-8 (hardware trips) | C14770 | LM2903P | TI | PDIP-8 | 2 | 1330 | normal | 5 | 1.64 | 0.45 | 0.37 |
| U9 | 3.0 V ADC reference | C38423 | REF3030AIDBZR | TI | SOT-23 | 1 | 105411 | normal | 1 | 0.51 | 0.29 | 0.27 |
| U10 | 100 V buck for 12 V aux rail | C2873264 | LM5163DDAR | TI | ESOP-8 | 1 | 5773 | normal | 1 | 2.30 | 1.64 | 1.52 |
| U11 | 5 V LDO 16 V max in | C145389 | MCP1703-5002E/DB | MICROCHIP | SOT-223-3 | 1 | 77 | normal | 1 | 1.71 | 1.08 | 0.97 |
| D1 | TVS 58 V PV input | C135180 | SMCJ58A-13-F | DIODES | SMC(DO-214AB) | 1 | 2894 | normal | 1 | 0.70 | 0.49 | 0.46 |
| D2,D3 | TVS 18 V battery / load | C374030 | SMCJ18A | Littelfuse | DO-214AB | 2 | 610 | normal | 5 | 1.53 | 0.43 | 0.37 |
| D4 | Reverse-battery crowbar rectifier | C2461 | 6A10 | MDD(Microdiode Semiconductor) | R-6 | 1 | 48960 | normal | 5 | 0.62 | 0.10 | 0.07 |
| D5,D6 | Bootstrap diode | C84067 | UF4007 | Changzhou Starsea Elec | DO-41 | 2 | 2200 | normal | 20 | 0.59 | 0.05 | 0.04 |
| D7,D8 | Aux supply OR diodes | C2457 | 1N4007G | MDD(Microdiode Semiconductor) | DO-41 | 2 | 130720 | normal | 20 | 0.33 | 0.03 | 0.03 |
| Q6 | Load switch P-FET | C2564 | IRF4905PBF | Infineon | TO-220AB | 1 | 24358 | normal | 1 | 0.61 | 0.38 | 0.35 |
| Q7,Q8 | NPN level shifters | C20526 | MMBT3904(RANGE:100-300) | JSCJ | SOT-23 | 2 | 3825600 | normal | 50 | 0.52 | 0.02 | 0.02 |
| Q9 | Driver-supply switch P-FET | C15127 | AO3401A | AOS | SOT-23 | 1 | 891375 | normal | 5 | 0.41 | 0.06 | 0.05 |
| D9 | 15 V gate clamp zener | C2104 | BZT52C15 | JSCJ | SOD-123 | 1 | 11350 | normal | 50 | 1.06 | 0.02 | 0.02 |
| RT1,RT2 | 10k NTC B3950 (heatsink, battery) | C5439706 | MF52A103F3950 | SURGING | Through Hole,P=2mm | 2 | 31700 | normal | 10 | 0.46 | 0.08 | 0.06 |
| F-clips | Blade-fuse clips (2 per fuse x 2 fuses) | C2680614 | 3557 | Keystone | Through Hole,P=3.4mm | 4 | 3310 | normal | 5 | 1.28 | 0.75 | 0.59 |
| J1-J3 | 30 A 10.16 mm 2-pin terminal | C591854 | 1-353080-2 | TE Connectivity | Through Hole | 3 | 404 | normal | 1 | 10.13 | 6.93 | 6.34 |
| J4 | 6-pin 2.54 mm header (serial/ISP) | C56153599 | PZ254V-12-06P-T | XFCN | Through Hole,P=2.54mm | 1 | 980 | normal | 20 | 0.48 | 0.02 | 0.02 |
| U7-S,U8-S | DIP-8 sockets for comparators | C305904 | 1-2199298-2 | TE Connectivity | DIP-8 | 2 | 3560 | normal | 5 | 1.18 | 0.30 | 0.24 |
| | **Subtotal, priced LCSC lines** | | | | | | | | | **59.13** | **32.80** | **29.90** |

**Tier variants (not added to the subtotal above):**
- *Tier A (≤ 100 W):* omit Q2, Q4, U3, D6 and L2. Replace L1 with the 22 µH FC-SE2918-220M (C2962789: LCSC "normal", 371 in stock, $1.88 at qty 1, $1.10 at 1000 [S35]).
- *Tier B (≤ 200 W):* omit the phase B parts and keep the 10 µH L1.

**Stock warnings** (live stock, 18 November 2026):

| Part | Stock | Shortfall |
|---|---|---|
| ATmega328P-PU | 538 | Not enough for 1000 boards |
| MCP1703-5002E/DB | 77 | Not enough for 1000 boards |
| NXA 100 V 220 µF | 290 | 6,000 needed for 1000 boards |
| TE 1-353080-2 | 404 | 3,000 needed |
| SMCJ18A (Littelfuse) | 610 | 2,000 needed |

Volume builds need second sources: other makers' SMCJ18A, other 100 V 220 µF low-ESR caps and other 10.16 mm 30 A terminals. Each substitute must be re-checked for lifecycle and stock (UNVERIFIED, item U22).

**Not priced, so the subtotal is *not* a full product cost** (UNVERIFIED, item U23):
- PCB. 2 oz copper or heavier is recommended for 28 A, and the JLCPCB price was not quoted.
- Heatsink and insulating pads.
- Enclosure.
- Small resistors and ceramics.
- LM5163 inductor and feedback parts.
- Blade fuses.
- In-line battery fuse holder.
- Wire and lugs.
- PCB assembly, test labour, shipping and import duty.

**Terminal rating note.** The 30 A terminals are at their limit at about 28 A of battery current. For Tier C, use two terminals in parallel for the battery, or a bolted lug (UNVERIFIED, item U24).

### 4.5 Battery chemistry safety

**Physically selected profile.** A 3-position jumper block (FLOODED / AGM / LiFePO4) is read at boot. A change requires removing the jumper with the power off. The firmware refuses to start charging if the jumper is missing or in an invalid position. This makes chemistry a visible, physical choice that a repair technician can see, not a hidden menu setting.

| Setting (12 V) | Flooded lead-acid | AGM | LiFePO4 (4 cells, with BMS) |
|---|---|---|---|
| Bulk/absorption | 14.1–14.7 V (2.35–2.45 V/cell); default 14.4 V | 14.4 V bulk, 14.1–14.7 V absorption | ≤ 14.6 V (3.65 V/cell max); default 14.2 V |
| Float | 13.5 V (2.25 V/cell) | 13.5–13.8 V (2.25–2.3 V/cell) | None, or a hold below about 13.6 V (UNVERIFIED, item U25) |
| Equalize | 16.2 V (2.70 V/cell), flooded only, manual, load disconnected | Never | Never |
| Temperature compensation | −5 mV/cell/°C relative to 25 °C (−30 mV/°C for 12 V) | Same, and always required for AGM | None |
| Charge temperature window | No charging of a frozen battery; stop above 50 °C | Same | +5 °C to +50 °C (default); never below 0 °C even if reconfigured |
| Load LVD (default) | 12.0 V (2.00 V/cell recommended, about 50 % depth of discharge); hard floor 11.7 V (1.95 V/cell) | Same | 11.2 V (2.8 V/cell). The quality framework's minimum is 2.45 V/cell (9.8 V) |
| Overcharge-protection framework limits | 2.35–2.45 V/cell | Same | ≤ 3.70 V/cell |
| Sources | [S19][S15] | [S19][S15] | [S20][S15] |

Notes on the table:
- **Lead-acid charge values.** These are Trojan's published figures [S19]. Other makers' batteries differ, so the defaults can be adjusted within the framework limits [S15] using a resistor-coded setting on the board, again physical and visible.
- **LiFePO4 temperature window.** The +5 °C figure comes from a Victron LiFePO4 safety certificate [S20]. The brief's absolute rule (no LiFePO4 charging below 0 °C) is enforced as a floor that no setting can override. Cell makers differ, and some cells allow slow charging at 0–5 °C (UNVERIFIED, item U26).
- **Battery temperature sensor.** If the NTC (10 kΩ B3950, C5439706) is missing or shorted, the ADC reading sits near a rail. Then LiFePO4 charging **stops**, because without a temperature reading it cannot be shown safe, and lead-acid falls back to the uncompensated 25 °C setpoints with a fault light.
- **Load LVD under load.** Battery voltage sags under load, so LVD uses a 30-second average and a small current-based correction. Reconnection requires +0.6 V hysteresis. The correction factor needs field tuning (UNVERIFIED, item U27).
- **Charging from a deeply discharged battery.** The quality framework warns that recharging a lithium cell discharged below 0 % SOC "can be unsafe" [S15]. If a LiFePO4 pack reads below 10.0 V (2.5 V/cell), the controller does **not** charge it automatically and shows a "battery needs inspection" code. The pack's BMS normally prevents this state anyway.

**BMS interaction (LiFePO4).** A LiFePO4 pack's BMS can open its MOSFETs at any time, on cell over-voltage, over-temperature or low temperature. When that happens during charging:
1. The charger suddenly has no battery, and its output voltage rises fast.
2. The **hardware over-voltage comparator** (LM2903, threshold set by resistors) pulls the drivers' SD pins low within microseconds, independent of firmware.
3. The 1000 µF output capacitors and the 18 V TVS (standoff 18 V, clamping 29.2 V at rated pulse current [S35]) absorb the energy stored in the inductor.
4. Firmware then waits, retries at low current, and records the event.

Repeated disconnects within minutes are reported as a battery or BMS fault, not silently retried.

**Chemistry-mismatch detection.** All of these heuristics need field validation (UNVERIFIED, item U28):
- **Set to lead-acid, but the battery behaves like LiFePO4 with a BMS.** Signs are a resting voltage that stays flat around 13.2–13.4 V through most of the discharge, a near-vertical voltage knee at the end of charge, or BMS-style hard disconnects at about 14.6 V. The controller stops above 14.6 V, refuses equalize and flags the mismatch.
- **Set to LiFePO4, but the battery behaves like lead-acid.** Signs are a resting voltage that falls steadily with charge removed (Trojan's OCV table is 12.73 V at 100 % and 12.10 V at 50 % [S19]) and a gradual rise in voltage during absorption. The risk is chronic undercharge and sulphation, so the controller flags it.
- **Gross mismatch.** A battery that reads below 9 V or above 16.5 V at rest on connection is not a healthy 12 V battery of either type, so the controller does not charge it.
- **24 V battery on a 12 V setting**, or the reverse, is caught by the same voltage-window check at connection.

### 4.6 Protection

| Hazard | Protection | How it works |
|---|---|---|
| **Panel connected backwards** | LM5050-1 ideal-diode controller + Q5 (IRFB4110) | Q5's body diode is reverse-biased, so no current flows. TI's datasheet gives a reverse-input application for this part, which adds a diode in the GND pin and TVS clamps on IN/OUT [S29]. |
| **Reverse current at night** (battery draining into the dark panel) | Same LM5050-1 + Q5 | The LM5050-1 turns the FET off within about 50 ns when current reverses [S29]. A diode would do the same job but would waste about 0.5 V × 11 A ≈ 5.5 W at 400 W. |
| **Battery connected backwards** | External fuse at the battery, crowbar rectifier D4 (6A10), the low-side FETs' body diodes and the battery TVS | Reversed polarity forward-biases these paths, and the fuse at the battery blows. Diodes in the aux-supply path (1N4007 ×2) keep logic unpowered. The current-sense amplifiers are on the low side and so are not stressed. **Fuse–diode coordination (whether D4 and the FETs survive until the fuse clears) was not calculated, because the fuse's I²t and the diode's I²t were not sourced. Bench test is required** (UNVERIFIED, item U29). After such an event the repair guide says to replace D4, the TVS and the fuse, and to check the low-side FETs. |
| **Charge overcurrent** | Firmware current limit (fine) plus a hardware comparator on the battery-shunt amplifier output (coarse, about 120 % of the tier limit) | The comparator pulls the IR2184 SD pins low, and the latch is cleared only by the MCU. |
| **Output short or load short** | Blade fuse F2 (20 A), a hardware comparator on the load-shunt amplifier output that switches off Q6 through Q8, and a firmware retry with back-off | — |
| **Panel-side fault** | Blade fuse F1 sized to the panel's Isc rating (for example 15 A for a 300 W 60-cell panel; UNVERIFIED, item U30) | — |
| **Battery cable fault** | **External fuse or breaker as close to the battery as practical**, as the quality framework requires ("located as close to the battery as practical") [S15] | Sized about 125 % of the tier's maximum charge current, for example 40 A for Tier C (UNVERIFIED, item U31). |
| **Overtemperature** | NTC on the heatsink | Derates charge current linearly from 70 °C to 85 °C heatsink temperature and stops at 85 °C (thresholds UNVERIFIED, item U32). The LM5163 and drivers also have their own thermal shutdown or UVLO [S30][S27]. |
| **Surge or lightning** | PV: SMCJ58A-13-F (58 V standoff, breakdown listed as 71.2 V on LCSC, which may be the maximum rather than the minimum; 93.6 V clamping at 16 A; 1.5 kW pulse rating). Battery and load: SMCJ18A, Littelfuse (18 V standoff, 20 V breakdown as listed, 29.2 V clamping, 1.5 kW for a 10/1000 µs pulse) [S35] | TVS diodes only handle induced surges, not a direct strike. Long PV cable runs should also have a DC surge protective device at the array and a proper earth. That is outside this board (UNVERIFIED sizing, item U33). |
| **Over-voltage on the battery bus** | Hardware comparator, threshold set by resistors per chemistry jumper | 15.0 V for LiFePO4; 16.8 V for flooded, to allow for equalize plus cold-temperature compensation. |
| **Load low-voltage disconnect** | Q6 (IRF4905, 55 V, 20 mΩ P-FET [S35]) switched by the MCU, with the thresholds in 4.5 | Q6 is off when the MCU is in reset, so it fails safe. Loss in Q6 at 10 A ≈ 10² × 0.02 × 1.6 (hot) ≈ 3.2 W, so it needs a heatsink above about 10 A. |
| **PAYG "disabled" state** | **Not in this board.** See 5.4 | The quality framework requires battery protection to remain active, and charging to continue, in the disabled state [S15]. |

### 4.7 Local repairability

Design rules:
1. **No part smaller than 0805 or SOT-23. No QFN, BGA or fine-pitch packages** on the main board. The LM5163 ESOP-8 is the hardest to hand-solder because of its thermal pad. The MCP1703 is SOT-223, the drivers SOIC-8 and the amplifiers SOT-23-6.
2. **Socketed DIP:** MCU (DIP-28) and both comparators (DIP-8).
3. **TO-220 power parts** screwed to one heatsink with insulating pads, so a part can be swapped with a screwdriver and a 60 W iron.
4. **Standard consumables:**
   - automotive blade fuses, which are available wherever cars are sold;
   - 10.16 mm screw terminals;
   - no proprietary connectors on the power path.
5. **Printed schematic** inside the lid, plus silkscreened part values, test points (TP) and expected voltages: TP-12V, TP-5V, TP-REF 3.00 V, TP-ISENSE at 1.50 V with zero current.
6. **Reflashing:**
   - Optiboot over a 6-pin serial header with a common USB-serial cable at 115200 baud (UNVERIFIED that the header part is single-row; item U34).
   - ISP fallback with any AVR programmer and avrdude [S42].
   - A pre-flashed spare MCU can simply be swapped in.
7. **Status LEDs and a blink-code table** printed in the lid. Fault codes are deliberately simple: one code per protection in 4.6.
8. **Open repair guide** in plain language, with translations encouraged, as GOGLA recommends [S12]. It follows a fault tree, for example: no 12 V → check F1, D7/D8, LM5163; no charge with 12 V present → check SD latch, then IR2184 outputs, then FETs.
9. **Substitutes table** (all to be confirmed for lifecycle and stock before use):

| Part | Substitute | Trade-off |
|---|---|---|
| IRFB4410Z (high-side) | Any 100 V TO-220 N-FET with RDS(on) ≤ 10 mΩ and Qgd ≤ 30 nC | Higher Qgd means more switching loss |
| IRFB4110 (low-side, PV) | IRFB4410Z, or any 100 V TO-220 with RDS(on) ≤ 9 mΩ | Higher RDS(on) means more conduction loss |
| IR2184S | IRS2104S (C151525) or IR2104S. Same SO-8 pinout family (UNVERIFIED pin compatibility, item U35) | About 0.13–0.27 A gate current [S28] gives roughly 3× the switching loss, so derate to Tier A |
| LM2903P | LM393P | 0–70 °C rated only [S32]. Acceptable indoors |
| ATmega328P-PU | The DIP chip from an Arduino-Uno-style board | Needs a bootloader or a reflash |
| 1000 µF 35 V, 220 µF 100 V | Any low-ESR 105 °C part of the same or higher voltage and ripple rating | Check ripple current |
| NTC 10 kΩ B3950 | Any 10 kΩ NTC; set B value in firmware | — |
| Fuses | Any ATO/ATC blade fuse of the same rating | — |

**Assembly at JLCPCB.** The SMD parts are all in common packages, and the through-hole parts (TO-220, DIP sockets, electrolytics, terminals) can be hand-soldered locally after SMT assembly. Local hand assembly of the through-hole parts keeps value and skills local. Whether JLCPCB would assemble every part on this list was not checked (UNVERIFIED, item U36).

### 4.8 Incoming inspection against counterfeit parts

- **MOSFETs.** Measure RDS(on) of each TO-220 FET at 10 A with 10 V on the gate, using a bench supply and a 4-wire Kelvin connection. Reject any that are far above the datasheet maximum: 4.5 mΩ (IRFB4110) or 9.0 mΩ (IRFB4410Z) [S25][S26].
- **MCU.** Check that the MCU's signature bytes read back through ISP.
- **Batch traceability.** Keep the reel or lot label photographs for each production batch.

These are standard engineering practice, not sourced from a study (UNVERIFIED as a counterfeit countermeasure, item U6).

### 4.9 Growth path

| Tier | PV | Battery | Fitted parts | Notes |
|---|---|---|---|---|
| A | ≤ 100 W, 36-cell or small 60-cell panel | 12 V | Phase A with 22 µH inductor | Lowest cost |
| B | ≤ 200 W | 12 V | Phase A with 10 µH inductor | — |
| C | ≤ 400 W | 12 V | Phases A and B | Battery terminal doubled |
| D (future) | ≤ 800 W | 24 V | Phases A and B. Change D2/D3 to a 33 V standoff TVS, change the OV comparator resistors, check that the battery-side capacitors stay 35 V | Same current, twice the power. The PV Voc limit still applies. **Not designed in detail** (UNVERIFIED, item U37) |
| Upgrade | Any | Any | STM32G431 daughter-card in place of the DIP MCU | Needed for finer MPPT and for CAN, as in LibreSolar [S36][S37] |

---

## 5. Ethics of pay-as-you-go (PAYG) lock-out firmware

### 5.1 What PAYG lock-out is

A PAYG solar system is sold on credit. The buyer pays small amounts, usually by mobile money, and receives codes or remote commands that keep the system running. If payments stop, firmware disables it: "digital collateral" [S21]. The quality framework treats PAYG parts as part of the product and requires that "appropriate battery protection must remain active regardless of whether the system is in an enabled or disabled state", and that the panel can still charge the battery while disabled. There is an exception for some lithium designs that protect the battery in other ways [S15].

### 5.2 The case for: wider credit access (the evidence)

- **Randomised field experiment, Uganda.** School-fee loans were secured by the borrower's existing PAYG solar system. Securing the loan with lock-out "drastically reduces default rates (by 19 pp) and increases the lender's rate of return (by 38 pp)". Access to these loans "significantly increases school enrollment and school-related expenditures without detrimental effects to households' balance sheet" [S21]. About a third of the repayment effect came from screening and two-thirds from the borrower's incentive to keep the light on [S21]. This is an NBER working paper, revised in July 2023. The PDF says it was "not … peer-reviewed". I did not confirm whether a journal version exists (UNVERIFIED, item U1).
- **SDG7 2026** lists pay-as-you-go financing among "effective responses to demand-side problems" [S1].
- **CGAP (2018)** notes that "very few lenders have created a scalable solar financing operation without using lockout technology". Earlier microfinance solar loans "have never reached scale", and lock-out "is the key enabler of flexible repayment schedules" [S22].

### 5.3 The case against: harms reported in the field

- **Lock-out as shame and as an added cost.** In a CGAP account from Uganda, a customer's darkened house "communicated a message, and each of her neighbors understood it". Respondents said lock-outs "were counter-productive", because they forced households to buy kerosene, which made it harder to resume paying. Among 1,327 respondents to the L-IFT survey, interest in PAYG fell from 63 % to 40 % once lock-out was explained [S22].
- **Whether lock-out even works is contested.** A quoted industry risk analyst said: "I have not seen any data to indicate that lockouts have a significant impact on payment rates" [S22]. The randomised trial above points the other way for its specific loan product [S21]. The evidence is mixed and context-specific.
- **Repayment struggle is common** (GOGLA, *Keeping the Lights On*, 2025; Kenya and Uganda; 2,602 customers surveyed) [S23]:
  - 40 % of customers struggle to pay, and the share rises with time since purchase.
  - Only 33 % (Kenya) and 29 % (Uganda) of struggling customers have a stable monthly income.
  - 40 % have other debts.
  - Over 50 % report climate shocks hitting their income.
  - **More than 50 % in Uganda and a third in Kenya do not accurately know their remaining balance or how long they still have to pay.**
  - 49 % (Uganda) and 28 % (Kenya) report technical issues.
- **Data and power over the customer.** A Nature Energy perspective identifies six areas of concern. They include "data collection and use" and whether PAYG suits lower-income households [S24]. GOGLA's own recommendations include sharing customer data with credit bureaus and other providers [S23]. That can build credit histories, but it also creates new privacy and exclusion risks.
- **Repairability.** Protecting the lock-out is a main reason products are "black-boxed" and hard to repair [S6].
- **Income-generating assets.** CGAP warns that lock-out "may be inappropriate for income-generation loans, when a customer is depending on the device for revenue to service their loan" [S22].
- **Clinics.** No source I found reports a clinic load being disabled by PAYG lock-out. I treat it as a foreseeable harm to design out, not as a documented incident (UNVERIFIED, item U39).

### 5.4 Design options

The aim is to keep the credit benefit and remove the worst harms. These are design proposals, not legal advice.

1. **Keep finance out of the open controller.**
   - The controller firmware has **no** lock-out code.
   - Any finance module is a separate board on a documented connector (power, ground, a single "non-essential output enable" line and a UART).
   - The controller treats the module as untrusted. The module can only ask to switch the non-essential output. It cannot touch charging, battery protection, LVD settings or the essential output.
   - Under CERN-OHL-W such a module can be "External Material" (6.1). Under CERN-OHL-S it would have to be open if conveyed as part of the same product. Section 6 discusses which is preferable.
2. **Essential/medical output that cannot be disabled.**
   - A second, separately fused output feeds the essential load: one light and phone charging for a household, or the vaccine fridge or delivery light for a clinic.
   - Its switch is driven only by the battery-protection logic. There is no electrical path from the finance connector to it, and that is enforced in **hardware**, so firmware cannot get around it.
   - It goes beyond the framework's minimum [S15], which protects the battery but not the user's essential load.
3. **Charging and battery protection always run.** This is already required [S15] and is kept.
4. **Clear terms, shown on the device.**
   - A days-remaining and balance-remaining display (or LED/blink code) on the device itself, because many customers do not know their balance [S23].
   - The terms state the total price, the cash price, fees, grace period and what happens at default.
   - The terms are in writing before purchase, like the warranty-transparency rule [S15].
5. **Grace periods and no visible shaming.** Lock-out turns off only the non-essential port and never flashes a public "unpaid" signal. A grace period of several days is the default, with a free emergency extension once per term.
6. **Unlock codes that work offline.**
   - Codes can be typed on a keypad or given by voice or SMS, so mobile-money or data-network outages do not leave people in the dark.
   - The OpenPAYGO token scheme is an open-source example of offline tokens. Its original Python token repository is Apache-2.0 and now deprecated in favour of the MIT-licensed OpenPAYGO-python [S45]. I did not check an embedded (C) decoder or its licence (UNVERIFIED, item U40).
7. **Data minimisation.**
   - The finance module may hold only payment state and token counters. No GPS, no microphone, no usage profiling.
   - Any radio must be **declared on the label and in the terms**.
   - The open controller's own logs (battery health) stay on the device unless the owner exports them.
   - **Nothing covert or hidden on a person.** No tracking of where the owner goes, and no hidden module in the hardware.
8. **Permanent ownership on payoff.** A final token permanently disables the finance module's control of the non-essential port. The owner may then remove the module.
9. **No remote repossession pressure through the device.** Repossession, where it happens, follows consumer-protection rules outside the device. The device never threatens it.
10. **Mobile-money dependence.** Payments through cash agents, and codes delivered by voice, keep a path open for people without smartphones or reliable mobile data. The fees and outages of mobile money are a real cost that I did not quantify (UNVERIFIED, item U41).

**Honest trade-off.** These choices reduce a lender's leverage, and the randomised evidence says that leverage lowers default [S21]. A lender could price that risk into higher charges, or lend less. The study's position is that an essential circuit that can never be locked is a floor worth that cost for clinics and for a household's basic light and phone. How large the effect would be on lending volume is unknown (UNVERIFIED, item U42).

---

## 6. Standards

I have **not** read the full text of any paywalled IEC standard. The table below states only what the publisher's abstract, a public preview or a certifier's summary says.

| Standard | What I could actually see | Relevance |
|---|---|---|
| **IEC 62509:2010**, *Battery charge controllers for photovoltaic systems – Performance and functioning*. Edition 1.0, published 2010-12-16, stability date 2026, CHF 210 [S17] | Abstract only: "minimum requirements for the functioning and performance of battery charge controllers (BCC) used with **lead acid** batteries in terrestrial photovoltaic systems… to be read in conjunction with IEC 62093:2005" [S17] | Its scope, per the abstract, does not cover LiFePO4. Claiming LiFePO4 compliance with IEC 62509 would therefore be wrong. The stability date of 2026 suggests a revision may be pending (UNVERIFIED, item U43). Test clauses unknown (item U44) |
| **IEC TS 62257-9-5:2024**, *Renewable energy off-grid systems – Part 9-5: Laboratory evaluation of stand-alone renewable energy products for rural electrification*. Edition 5.0, published 2024-12-11, 417 pages [S18] | Abstract and preview cover: "written primarily for off-grid renewable energy products with batteries and PV modules with DC system voltages not exceeding 35 V and peak power ratings not exceeding 350 W" [S18] | This design's 400 W tier and its 55 V PV input are **outside** the primary scope. Tiers A–B are within it on power, but the PV Voc of a 60/72-cell module exceeds 35 V |
| **IEC TS 62257-9-8** (requirements; this is what VeraSol/Lighting Global certify against). The 2025 edition exists [S16]. I read VeraSol's summary of the **2020** edition [S15] | From the 2020 summary [S15]: battery protection thresholds (section 4.5); a minimum 2-year warranty on the main system with ≥ 80 % battery capacity retention at two years; replaceable fuses must be labelled with a spare included; short-circuit protection on the battery leads "as close to the battery as practical"; battery protection must stay active and charging must continue in the PAYG-disabled state. **Products with total PV > 240 W, Voc > 35 V or Isc > 8 A: "Charge controllers… shall meet IEC 62109 or UL 1741"** | The 2025 edition's changes, per VeraSol [S16]: certification valid three years, two-unit sample size, truth-in-advertising tolerance 10 %. The detailed 2025 requirements are **not verified** (item U45). This design **triggers the IEC 62109 / UL 1741 requirement**, which is a major certification cost |
| **IEC 62109-1** (safety of power converters in PV systems), **UL 1741** | Named in [S15] only; not read | Required by the framework above 240 W or 35 V Voc. Isolation, creepage and enclosure rules may change the PCB layout (item U46) |
| **IEC 61427-1** (secondary batteries for PV) | Named in [S15] as a route for LiFePO4 battery testing; not read | Applies to the battery, not the controller |
| **IEC 62093** (balance-of-system reliability) | Named in the IEC 62509 abstract [S17]; not read; current edition not checked | — |
| **VeraSol / Lighting Global quality standards** | Program description [S14] and summary [S15]; 2018 test report on non-verified products [S13]; transition notice [S16] | The quality framework that buyers and funders use |

---

## 7. Licence check

All licence texts below were read in the actual licence file or on the actual page on 18 November 2026. I am not a lawyer, and nothing here is legal advice.

| Item | Licence (as read) | Notes |
|---|---|---|
| **Proposed hardware licence: CERN-OHL-S v2** (strongly reciprocal) | Licence text [S38]. It defines "Available Component" (sections 1.7–1.9), and a recipient of a Product must be given the Complete Source or told where it is (section 4.1). **The -S text has no "External Material" clause**: I searched the text and the phrase does not appear | Anything combined into the Product must be open, except Available Components. This favours repair. It also means a PAYG module sold as part of the same product would have to be open source |
| **Alternative: CERN-OHL-W v2** (weakly reciprocal) | Licence text, as distributed in the LibreSolar repository [S36][S39]. It defines "External Material" (section 1.8) as something that "interfaces with the Covered Source using a documented interface which is described in the Covered Source". Section 4.2 states that Complete Source "need not include any Source for the External Material" | It would let a closed finance module plug into the documented connector of 5.4 |
| **Recommendation** | **CERN-OHL-S** for the controller | Protects repairability and keeps any finance hardware sold with it open to inspection, which supports the transparency and data-minimisation goals. If adoption by PAYG companies matters more, use -W, and rely on the hardware-enforced essential output (5.4, item 2), which does not depend on the licence |
| **Reference design: LibreSolar MPPT 2420 HC** (STM32G431, 100 V MOSFETs, 20 A) | **CERN-OHL-W v2** (LICENCE file) [S36] | Used here as a reference for the upgrade path only. No files copied |
| **LibreSolar charge-controller firmware** | **Apache-2.0** (LICENSE file; README says "released under the Apache-2.0 License"); built on Zephyr RTOS [S37] | Usable with the STM32 upgrade. Zephyr's own licence was not checked (item U47) |
| **Proposed firmware licence** | **Apache-2.0** (it includes a patent grant), or **MIT** | Either one lets anyone repair and redistribute modified firmware |
| **Bootloader: Optiboot** | **GPLv2 plus a "Bootloader Exception"** that gives "unlimited permission to link or embed compiled bootloader… into combinations with other programs". The maintainers state that it has "no effect on the application itself" [S40] | Fixes to Optiboot itself are expected to be fed back |
| **C library: avr-libc** | "Modified BSD License… OK to use in both free and proprietary applications" [S41] | — |
| **Programmer tool: avrdude** | **GPLv2** (COPYING) [S42] | A tool only; it does not end up in the firmware |
| **Compiler: avr-gcc** | Not checked. The GCC runtime library exception is relevant if you link libgcc | Item U48 |
| **Arduino core** (if used instead of bare avr-libc) | The ArduinoCore-avr repository had no LICENSE file at the paths I tried. MiniCore's LICENSE says the Arduino core and libraries are under the **GNU LGPL** and the IDE under the GPL [S46] | The recommendation is bare avr-libc, to avoid LGPL relinking obligations on a microcontroller (item U49) |
| **KiCad libraries** (symbols and footprints) | CC-BY-SA 4.0 with an exception: designs that use the libraries are not bound by it [S43] | Safe to use for a CERN-OHL design |
| **EasyEDA/LCSC symbols and footprints, JLCPCB part library** | **Not checked** | Item U50. Draw your own or use KiCad libraries |
| **Vendor SDK risk** | Main option: **none**, because the ATmega328P needs no vendor SDK. Upgrade option: the STM32G4 HAL is **BSD-3-Clause** (LICENSE.md) [S44]. Other ST packages (CubeMX, middleware) carry other terms, not checked | Item U51 |
| **TI parts** (LM5050-1, LM5163, INA181, LM2903, REF3030) | Datasheets only; no TI software or reference-design files used | TI reference-design file licences not checked (item U52) |
| **PAYG token library (optional, finance module only)** | OpenPAYGO-Token (Python): **Apache-2.0**, repository marked deprecated. OpenPAYGO-python: **MIT** [S45] | Embedded decoder licence not checked (item U40) |

---

## 8. Limitations

- Desk research only. **No circuit has been simulated, built or measured.** Every loss figure is an estimate built on stated assumptions.
- Inductor core loss, capacitor life at temperature, heatsink size and EMI are not quantified.
- The design is a block-level architecture, not a schematic. Component values for dividers, compensation, the LM5163 network and comparator thresholds are not given.
- Standards: only abstracts and summaries were read (section 6). Certification to IEC 62109 or UL 1741 would be needed above 240 W or 35 V Voc [S15], and that cost is not estimated.
- LCSC "normal" is a distributor field, not the manufacturer's lifecycle status. Stock changes daily.
- The field studies cited mostly cover lead-acid batteries and Kenya, Malawi, Zambia, Rwanda and Uganda. Results may not carry over to other regions or to LiFePO4.
- The PAYG evidence is limited and mixed. The strongest pro-lock-out result is a single randomised trial for one loan product in one country [S21].

## 9. Open questions

1. What are the real switching transition times, and the real efficiency, with IR2184S + IRFB4410Z at 62.5 kHz? (Bench test.)
2. What is the inductor core loss at 13.7 A ripple and 62.5 kHz? Would a lower frequency with a larger inductor be better?
3. Is the 7-bit duty resolution plus dithering good enough to sit within about 1 % of the maximum-power point?
4. Will D4 and the low-side FETs survive a reversed battery until the external fuse clears?
5. What does the 2025 edition of IEC TS 62257-9-8 actually require for PAYG-disabled states and for controllers above 240 W?
6. Can an essential output that can never be locked be made acceptable to PAYG lenders, perhaps with lower-cost insurance products of the kind CGAP mentions [S22]?
7. Which local markets actually stock DIP ATmega328P chips, blade fuses and TO-220 MOSFETs, and at what price?
8. Should the firmware export battery-health data in a standard format so that independent technicians can diagnose batteries [S10]?

---

## 10. UNVERIFIED list

Every claim, part or figure that I could not confirm from a source opened during this research:

1. **U1.** Whether the "Digital Collateral" NBER working paper [S21] has since been published in a peer-reviewed journal. The PDF I read says it was not peer-reviewed.
2. **U2.** The Rwanda study [S9]. I read only its abstract, so no specific numbers from it are used.
3. **U3.** Any arithmetic combination of the 2023 health-facility figures (survey years 2015–2022) [S3] with the 2024 household figures [S1]. They are not comparable, and no combined figure is given.
4. **U4.** The 449 million off-grid solar users figure [S1]. It is a model built on GOGLA-affiliate sales, an assumed 29 % market share and an assumed product life, not a count.
5. **U5.** The "two striking S2 counterfeits" quote [S49]. I read it only as a search-result excerpt, not in the full document.
6. **U6.** How common counterfeit power MOSFETs and ICs are in off-grid controllers. No source was found. The incoming-inspection steps (4.8) are general practice, not taken from a study.
7. **U7.** The illustrative panel figures: 36-cell 100 W (Vmp 18 V, Imp 5.56 A, Isc 5.9 A), 60-cell 300 W (Vmp ≈ 32 V, Isc ≈ 9.8 A), and a 72-cell Voc of about 45–48 V. These are not from a specific panel datasheet.
8. **U8.** The size of the hot-climate Vmp drop that narrows the gap between PWM and MPPT (no temperature-coefficient source).
9. **U9.** That DIP ATmega328P chips or Arduino-Uno-style boards are actually available in the target local markets.
10. **U10.** That 128-step PWM plus dithering tracks the maximum-power point within about 1 %.
11. **U11.** That the ATmega328P phase-correct OC1A non-inverted / OC1B inverted trick gives clean 180° interleaving across the full duty range.
12. **U12.** Hot RDS(on) factor of 1.6 × the 25 °C maximum (assumption, not read from the datasheet curve).
13. **U13.** Switching transition time of about 100 ns. This is estimated from Qgd + Qgs/2 and a gate current derived from the driver's short-circuit current, not measured.
14. **U14.** Body-diode voltage of 0.8 V during dead time. The datasheet gives only 1.3 V maximum at 75 A [S25].
15. **U15.** Inductor DCR × 1.2 when hot, and 2 mΩ of PCB and terminal resistance.
16. **U16.** Controller overhead of 0.25 W while charging.
17. **U17.** Inductor core loss. No vendor core-loss data was seen, so it is excluded from the efficiency figures, which are therefore optimistic.
18. **U18.** Heatsink and insulating-pad thermal resistance, and the resulting junction temperatures.
19. **U19.** How ripple current splits between ceramic and electrolytic capacitors, and electrolytic life at enclosure temperature.
20. **U20.** REF3030 quiescent current (about 0.05 mA was assumed; the datasheet was not read).
21. **U21.** Manufacturer lifecycle status (active / NRND / EOL) for every BOM line. Only LCSC's "productCycle" field was checked.
22. **U22.** Second-source parts for the lines with low stock (lifecycle, stock, equivalence).
23. **U23.** Cost of the PCB, heatsink, enclosure, small passives, LM5163 inductor and feedback parts, fuses, battery fuse holder, wiring, assembly, test, shipping and duties. None of these are priced.
24. **U24.** Whether 30 A terminals are adequate at about 28 A continuous, and the right way to double them.
25. **U25.** The best LiFePO4 "hold" voltage (about 13.6 V) and whether to float at all.
26. **U26.** LiFePO4 cell-specific low-temperature charge limits. +5 °C is from one maker's certificate [S20], and other makers differ.
27. **U27.** The LVD averaging window, the load-current correction and the 0.6 V reconnect hysteresis.
28. **U28.** All chemistry-mismatch heuristics in 4.5.
29. **U29.** Reverse-battery fuse coordination: the I²t of the fuse against D4 and the FET body diodes.
30. **U30.** PV fuse F1 rating, for example 15 A for a 300 W 60-cell panel.
31. **U31.** External battery fuse rating, for example 40 A for Tier C.
32. **U32.** Thermal derating thresholds of 70 °C and 85 °C.
33. **U33.** Sizing of the surge protective device and earthing for long PV runs.
34. **U34.** That PZ254V-12-06P-T (C56153599) is a single-row 1×6 header. LCSC showed no row parameter.
35. **U35.** Pin compatibility of the IRS2104S / IR2104S substitute with the IR2184S footprint, and of other substitutes in the table in 4.7.
36. **U36.** Whether JLCPCB assembly supports every SMD line as listed.
37. **U37.** The 24 V (Tier D) variant. It is not designed in detail.
38. **U38.** The trade-off of a lower-standoff PV TVS against a lower Voc limit.
39. **U39.** That PAYG lock-out has disabled clinic loads in practice. No documented case was found, so it is treated as a foreseeable risk.
40. **U40.** The existence and licence of an embedded (C) OpenPAYGO token decoder.
41. **U41.** The cost and outage frequency of mobile money for PAYG customers.
42. **U42.** The effect on lending volume of an essential output that can never be locked.
43. **U43.** Whether a revision of IEC 62509 is pending (inferred only from its 2026 stability date).
44. **U44.** IEC 62509 test clauses and pass criteria (full text not read).
45. **U45.** The detailed requirements of IEC TS 62257-9-8:2025 (only the 2020 summary and the 2025 transition notice were read).
46. **U46.** IEC 62109-1 / UL 1741 requirements and their effect on PCB layout and enclosure (not read).
47. **U47.** Zephyr RTOS licence terms (not checked).
48. **U48.** GCC and libgcc licence terms for avr-gcc (not checked).
49. **U49.** The ArduinoCore-avr licence from its own repository. It was inferred from the MiniCore LICENSE file [S46].
50. **U50.** Licence or terms of use for EasyEDA/LCSC symbols, footprints and the JLCPCB part library.
51. **U51.** Licences of ST's CubeMX and middleware beyond the STM32G4 HAL.
52. **U52.** Licences of TI reference-design files (none used).
53. **U53.** The ATmega328P's active current at 16 MHz. The datasheet table I read gives 8 MHz values only [S34]. The night estimate uses 8 MHz idle values.
54. **U54.** The LM2903P quiescent current. 800 µA was taken from the LCSC parameter, not read in the TI datasheet table.
55. **U55.** That the IC-28P DIP-28 socket (C5289491) holds a part reliably over years in a hot, humid enclosure. Socket contact quality is unknown.

**Total: 55 unverified items.**
