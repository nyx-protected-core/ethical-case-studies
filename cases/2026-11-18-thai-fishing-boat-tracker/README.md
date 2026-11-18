# Low-cost position tracker for small Thai fishing vessels: a design study

Status: research and design only. Nothing here has been built, flashed or tested.
Date of research: 18 November 2026. Prices were read on that date. **Revision 2** (same day) responds to a peer review; see section 7.
Source tags like [S12] point to entries in `SOURCES.md`. **UNVERIFIED** marks anything I could not confirm from a primary page.

> **What changed in this revision**
> - **Headline change: SOS traffic shape.** Revision 1 encrypted the SOS but made it stand out on the air: an immediate send, repeats every 2 minutes, confirmed uplinks and a shore acknowledgement. Anyone with a cheap LoRa receiver on board could have spotted that pattern without decrypting anything. In revision 2 an SOS is indistinguishable from routine traffic:
>   - same FPort and the same padded frame length as every other message;
>   - always unconfirmed;
>   - sent only in normal report slots, with constant-rate cover traffic from every unit;
>   - acknowledged only through the fixed daily downlink schedule.
>
>   This has to be **prototyped and sniffer-tested before any other firmware work** (sections 3.3 and 4).
> - **Power safety:** a 3.3 V nanopower LDO (TPS7A0233) now feeds the module and the GNSS. A hardware shunt clamp, independent of the MCU, protects the system rail if the charger fails short to the panel (section 2.1).
> - **Identifiers:** the identifier table now says plainly what leaks (section 4.4). The DevEUI is a stable vessel pseudonym. NB-IoT gives the operator a location trail. Iridium computes position on its own. Any transmitter can be direction-found.
> - **Sourcing:** the module is now RAK3172-T-9-SM-I (TCXO, −40…+85 °C, 428 in stock). NB-IoT is opt-in, sourced from an authorised distributor, and not in the first pilot. The two disputed C-numbers are explained.
> - **Firmware:** written in C on the STM32WLE5 with a BSD-licensed LoRaWAN stack. The payload is 30 B on air (453 ms at SF10), so SF9 is planned if the 400 ms dwell limit applies. Flash budget added. No permanent RDP level 2.

---

## 0. Summary

- **The gap is real but narrow, and it has to be confirmed.** Thai law requires VMS only on commercial vessels of 30 GT and over [S4][S6]. In June 2023 the Department of Fisheries (DoF) counted 58,674 fishing vessels. That includes 4,621 commercial vessels under 30 GT and 48,776 artisanal boats, almost all under 10 GT [S1]. Neither group carries VMS. The project only makes sense if DoF and the labour authorities agree that a gap exists, and agree on which segment it is in (section 1.5).
- **Recommended comms:** a private LoRaWAN network in the AS923 band (920–925 MHz), received by shore gateways near PIPO ports, as the default. NB-IoT is opt-in only and not in the first pilot. For boats far offshore, an Iridium SBD variant on a **fixed schedule** carries SOS inside its normal slots. Swarm closed in March 2025 [S25]. Astrocast's operating company went bankrupt in March 2026 [S26]. Myriota's HyperPulse coverage list does not include Thailand [S27].
- **Unit cost (LoRa variant):** the LCSC-verified electronics cost about **USD 13.8 / 11.4 / 11.0** per unit at quantities of 1 / 100 / 1000. With antennas, battery, solar panel, enclosure, PCB and the clamp's power parts at **UNVERIFIED** estimates, the whole unit comes to roughly **USD 96 / 58 / 44**. Airtime is close to zero on a private LoRa network. NB-IoT is about USD 10 per SIM per year, based on a 2018 price [S34]. Iridium on a 6-hourly schedule is about USD 15.75 per month [S28].
- **Ethics core:**
  - The device sits on the vessel, never on a person. It is visible and labelled, with no microphone, no camera and no phone or Bluetooth scanning.
  - It sends a minimal message and carries no crew identifiers.
  - An independent data steward holds the data under a governance board that includes fisher worker organisations, with fixed retention and logged access.
  - A crew SOS has two functions: a safety distress call and a labour-help call that goes to an independent responder. Its radio traffic looks the same as routine reports.
  - The whole system is co-designed with fishers in Thai, Burmese, Khmer and Lao.
  - It contains **no covert beacons and nothing hidden on a person.**

---

## 1. Problem and gap

### 1.1 Forced labour at sea in Thailand: what the evidence says
- **ILO, February 2026 (Ship to Shore Rights regional survey):** an estimated 20% of migrant fishers in the surveyed countries, Thailand included, were in forced labour, against 0.4% of seafood processing workers [S16]. The report singles out distant-water vessels, but its figures cover the whole region [S16].
- **ILO 2020 endline survey in Thailand:** 14% of fishers surveyed in 11 coastal provinces were estimated to be in forced labour. The common forms were wage withholding, document retention, deception and coercion [S17].
- **US TIP report 2025, Thailand (Tier 2):** [S19]
  - Port inspections "continued to be inconsistent and ineffective" at finding forced labour.
  - Captains were present during interviews.
  - Some owners got advance warning of inspections.
  - There was no standard procedure for fishers who went missing at sea.
  - 9,348 vessels were inspected at PIPO centres with no victims identified.
  - Some corrupt officials reportedly protect vessel owners.
  - That last point matters directly for the ethics of who controls location data (section 3).
- **HRW "Hidden Chains" (2018):** forced labour and abuse of Burmese and Cambodian migrant fishers, debt bondage, and documents and wages withheld [S18].
- **Unions and NGOs (ITF and others):** surveys found that almost no fishers are paid monthly by bank transfer, as the law requires [S20].

### 1.2 Thailand's existing rules
| Measure | What it does | Source |
|---|---|---|
| Royal Ordinance on Fisheries B.E. 2558 (2015), amended B.E. 2560 (2017) | Modern fisheries law. DoF's own statistics use the 2015 ordinance "and amendments 2017" as their legal basis. | [S1][S4] |
| VMS for commercial vessels of 30 GT and over | Reports hourly from port-out to port-in. Model 1 and Model 2 units may use satellite or GSM. The unit is sealed, and PIPO must be told within 24 h of installation. | [S4][S6][S23] |
| Fisheries Monitoring Centre (FMC), set up in 2017 | Runs 24/7 and feeds VMS data to the PIPO centres | [S5] |
| PIPO (Port-In Port-Out) centres | 30 centres covering about 1,500 fishing ports. Small (<30 GT) commercial boats are inspected for some gear types but carry no VMS. | [S8][S2] |
| EU IUU "yellow card" | Issued April 2015, lifted 8 January 2019 | [S14][S9] |
| ILO C188 (Work in Fishing) | Ratified 30 January 2019, the first in Asia. In force for Thailand from 30 January 2020. | [S15] |
| Satellite comms for crew | A 2022 ministerial regulation makes employers on vessels fishing outside Thai waters provide crew satellite communication | [S49][S50] |
| AIS (Marine Department) | Class B or better required on specified vessels of 60–300 GT. Vessels already fitted with VMS are exempt. | [S24] **UNVERIFIED** (read from a search summary of the regulation, not the PDF itself) |

### 1.3 What small boats lack
- **DoF fleet census, 30 June 2023** [S1]:
  - 58,674 vessels in total.
  - 48,776 artisanal: 43,555 under 5 GT, 5,108 from 5 to under 10 GT, and 113 from 10 to under 15 GT.
  - 9,898 commercial: 4,621 under 30 GT, 2,795 from 30 to under 60 GT, 2,351 from 60 to under 150 GT, and 131 of 150 GT and over.
- **EJF (2023):** VMS covered 6,125 vessels, 61.3% of the commercial fleet, which left 3,862 commercial vessels without it [S2]. The difference from DoF's census is probably down to dates and definitions (**UNVERIFIED**).
- **Past attempts that stalled** [S2]:
  - VMS trials for vessels under 30 GT in 2020 were suspended because of funding and COVID.
  - A 2022 plan to fit AIS on about 4,865 "green oil" vessels was halted by 2023 over budget and regulatory problems.
- **Plans that never happened:** EJF (2019) described plans to extend VMS to 20 GT and then 10 GT. Progress was slow [S3].
- **The public picture is blind too:** globally, 72–76% of industrial fishing vessels are not publicly tracked [S21].
- **Cost puts small owners off:** a 2017 Ranong study found VMS cost about THB 20,000 to install, plus THB 900–1,000 a month for satellite or over THB 200 a month for GSM. Owners also complained that they cannot tell when the signal drops, and that they get called back to port when it does [S22]. A design for small boats has to beat these costs and show a local "link OK" indicator.

### 1.4 Changes in 2025–2026
- **Fisheries Act amendment bill:** [S9][S10][S11][S12][S13]
  - Revises 71 sections.
  - Passed the Senate on 16 September 2025 and the House on 17 September 2025.
  - DoF said on 29 September 2025 that it was going through the steps to publication in the Royal Gazette.
  - Its contents include:
    - Repeals section 34, which kept artisanal boats inside coastal waters. Small boats may then range further offshore, which matters for radio range.
    - Removes the fisheries regulator's labour stop-order powers over processing plants (sections 10/1, 11 and 11/1).
    - Relaxes the small-mesh night-net ban (section 69).
    - Removes the pre-departure check of crew lists and seafarers' books (section 82), according to one secondary analysis.
- **Status:** a secondary source says the amendment was still not in force as of late August 2026 [S11]. **UNVERIFIED.** Confirm against the Royal Gazette (ratchakitcha.soc.go.th).
- **VMS repealed for support and transport vessels:** EJF reported that a regulation effective 29 August 2025 removed the VMS requirement for these vessels [S9]. **UNVERIFIED** against the Gazette text.
- **Effect on this design:** less checking of crew at the quayside and more offshore range for small boats both make an independent crew channel and longer-range comms more important (sections 2 and 3).

### 1.5 Go / no-go: what must be confirmed, and with whom
The project should not go past a desk study until these points are answered in writing.
1. **DoF (Fishing Vessel and Fishing Operation Management Division; FMC).**
   - Is there an acknowledged monitoring gap for commercial vessels under 30 GT, for artisanal boats, or for both?
   - Is any small-vessel VMS or AIS programme planned or under way (the 2020 trial; any 2025–26 subordinate rules)?
   - Would data from a non-certified tracker be accepted, ignored, or treated as a conflict with VMS Model 1/2 rules?
2. **Department of Labour Protection and Welfare (DLPW) and the Ministry of Labour.**
   - Where do forced-labour cases on small vessels actually happen?
   - Would position history help investigations, including missing-fisher cases [S19]?
3. **Marine Department and Thai-MECC (sea emergency line 1465 [S46]).** Would they accept distress alerts, and how?
4. **NBTC.** Type-approval class for a 920–925 MHz device at the planned EIRP. Which revision of TS 1033 applies (section 2.3)?
5. **Ministry of Social Development and Human Security (1300 Prachabodi line).** Would it accept vessel-originated alerts? What interpreter cover does it have [S45]?
6. **Fisher worker organisations: Fishers' Rights Network (FRN), Stella Maris Songkhla, MWRN, and migrant worker groups** [S47][S48][S51].
   - Do crews want this?
   - What would make them unsafe?
   - Would they help govern it?
7. **Small-scale fisher associations (for example the Federation of Thai Fisherfolk Associations [S9]).** Would artisanal owner-operators accept it? Many artisanal boats are family-run, so the forced-labour case for them is weaker and must not be assumed.

If DoF says no gap exists or that the data is unwanted, or if worker organisations judge the risk to crew too high, **stop**.

---

## 2. Hardware design

### 2.1 Architecture (LoRa baseline)
```
 [12 V-class 2 W solar panel, Voc ~21-22 V UNVERIFIED] -> fuse -> TVS + reverse-polarity FET
        -> [P-FET charge inhibit, MCU-controlled] -> [CN3795 MPPT charger, CV set ~3.50 V]
        -> BAT node: [1S LiFePO4 cell + HY2112-BB/FS8205A protector]
        -> SYS rail (2.8-3.55 V normal)
             |== [HW shunt clamp: TL431 sense + power N-MOSFET, trip ~3.95 V, no MCU involvement]
             |- TPS7A0233 3.3 V LDO (25 nA Iq) -> 3V3 rail
             |      |- RAK3172-T-9 (STM32WLE5: MCU + LoRa radio) -> 923 MHz marine whip
             |      |- P-FET load switch -> ATGM336H-5N31 GNSS (VBAT backup pin from 3V3)
             |      |- LIS2DH12 accelerometer (wake on motion, tilt/tamper)
             |- [option A, opt-in] NB-IoT modem (BC660K-GL/SIM7022) on SYS rail (VBAT 2.2-4.3 V)
             |- [option B] 5 V boost -> RockBLOCK 9603 (Iridium SBD), fixed schedule
        Inputs: SOS button (guarded), lid tamper switch; Output: status LED ("link OK / last send")
```
Why the electronics look like this:
- **A 3.3 V LDO now isolates the RF module and the GNSS.**
  - The STM32WLE5 runs from 1.8–3.6 V and its absolute maximum is 3.9 V [S52]. The RAK3172 module is specified at 2.0–3.6 V [S37]. The ATGM336H is listed at 2.7–3.6 V [S41].
  - The HY2112-BB overcharge trip is 3.75 V ±25 mV [S40]. That will not destroy the chip, but it is outside the operating range. And every in-stock LiFePO4 HY2112 variant on LCSC trips at 3.75 V.
  - So a **TPS7A0233** (fixed 3.3 V, 1.5–6.0 V input, 6.5 V absolute maximum, **25 nA quiescent even in dropout**, 270 mV maximum dropout at 200 mA [S53]) now feeds the module, the GNSS and the accelerometer.
    - In the 3.75 V fault case it holds 3.3 V.
    - On a normal 3.2–3.3 V LiFePO4 plateau it sits in dropout and passes roughly VBAT minus a few tens of mV at about 25 mA. That figure is **UNVERIFIED** and scaled from the datasheet maximum.
    - The firmware cuts the GNSS when the 3V3 rail falls below about 2.97 V (3.3 V −10% [S38]).
- **A hardware clamp protects against a charger short to the panel.** If the CN3795's high-side switch fails short, the panel connects straight to the BAT node. A standard failure mode to design against; this specific part's failure modes are **UNVERIFIED**.
  - The HY2112 then opens the cell path at 3.75 V. The SYS rail could rise toward the panel's open-circuit voltage: about 21–22 V for a typical 36-cell "12 V" panel (**UNVERIFIED**). That would destroy the TPS7A02 (6.5 V absolute maximum [S53]) and everything after it.
  - **Fix:** an MCU-independent **shunt clamp** across the SYS rail. A TL431AIDBZR (2.495–36 V adjustable shunt reference) senses the rail through a high-value divider. It drives a power N-MOSFET that conducts above about 3.95 V.
  - A small solar panel is a current source (a 2 W "12 V" panel has an Isc of roughly 0.15–0.2 A, **UNVERIFIED**). So the clamp can hold the rail near 4 V indefinitely, dissipating about 0.6–0.8 W. That needs a **SOT-89/DPAK-class MOSFET rated 2 W or more, not a SOT-23 part** (part not selected, **UNVERIFIED**).
  - A fuse in the panel lead bounds the fault if a larger panel is ever fitted.
  - The trip point sits above the 3.50 V charge voltage and the 3.75 V protector trip, and below the NB-IoT modem's 4.3 V maximum and the LDO's 6.0 V maximum input.
  - The divider plus TL431 off-state leakage adds a few µA to sleep current (**UNVERIFIED**).
  - **An OVP load switch was considered and rejected.** The Prisemi P14C13 (C5274035) is verified active on LCSC. But its 3.5–32 V operating range cuts out below the LiFePO4 plateau (3.2 V), and its fixed 6.0 V OVLO is above the LDO's 6.0 V maximum input.
  - Prototype item 1: a simulated charger short on the bench.
- **LiFePO4 is kept** for its thermal stability. The cell still must not be charged above about 45 °C (**UNVERIFIED** generic limit; check the chosen cell). The MCU-controlled P-FET in front of the charger opens on over-temperature, sensed by an NTC.
  - The HY2112-HB (3.65 V trip) stays out of stock. Its over-discharge trip is 2.5 V [S40], which would also cut the low-battery reserve state short.
- **The CN3795 needs at least 6.6 V input**, so use a 9 V or 12 V panel. Its absolute maximum input must exceed the panel's Voc with margin (**UNVERIFIED**: check the CN3795 datasheet against the chosen panel).
- **Module choice:** RAK3172-T-9-SM-I (C19723909). It is the 9xx MHz (AS923) band, has a ±2.5 ppm TCXO, is rated −40…+85 °C [S37], had 428 in stock and is slightly cheaper than the non-TCXO -9 [S41]. Its IPEX connector suits a cable to the external marine whip. Second source: an SX1262IMLTRT (C191341) plus a separate MCU. That route adds RF matching and its own type approval, and it rules out RAK's RUI3 firmware licence (section 4.7).

### 2.2 Parts list (single table)
Verified = Y means that on 18 Nov 2026 I read the LCSC product page for that number and confirmed the MPN, the "productCycle: normal" status, stock and the price ladder [S41]. "normal" is **LCSC's own distributor label, not a manufacturer lifecycle or longevity statement**, and stock moves by the hour. Prices are LCSC USD unit prices at the tier that applies to quantities of 1, 100 and 1000 (MOQ noted). "1000" uses the top tier listed if no 1000 tier exists.

| # | Part (MPN) | Function | LCSC # | Unit USD @1 / @100 / @1000 | Lifecycle (LCSC) | Stock seen | Verified |
|---|---|---|---|---|---|---|---|
| 1 | RAKwireless **RAK3172-T-9-SM-I** | MCU (STM32WLE5) + LoRa, 9xx MHz/AS923, TCXO, −40…+85 °C, IPEX | C19723909 | 7.9804 / 7.7722 / 7.7722 (top tier 10+) | normal | 428 | Y |
| 1a | RAKwireless RAK3172-T-9-SM-NI | Same, no IPEX (pad antenna) | C19723907 | 8.3575 / 8.1395 | normal | 139 | Y |
| 1b | RAKwireless RAK3172-9-SM-NI | Rev 1 choice; no TCXO, −20…+85 °C | C18548052 | 8.0133 / 7.7982 | normal | 8 | Y (replaced) |
| 1c | RAKwireless RAK3172-T-8-SM-NI | **Wrong band (8xx MHz); do not order** | C19723906 | 9.3504 / 9.1127 | normal | 425 | Y |
| 1d | Semtech SX1262IMLTRT | Second-source radio (needs MCU, RF match, own approval) | C191341 | 3.2406 / 1.9061 / 1.7178 | normal | 37,816 | Y |
| 2 | Zhongkewei ATGM336H-5N31 | GNSS (GPS+BDS), UART, <25 mA | C90770 | 3.2683 / 1.9840 / 1.8312 | normal | 3,809 | Y |
| 2a | Zhongkewei ATGM336H-5N71 | Alternate GNSS variant | C2940946 | 3.3361 / 2.1267 / 1.9254 | normal | 1,832 | Y |
| 3 | Consonance CN3795 | Solar MPPT charger, adjustable CV (LiFePO4) | C150555 | 0.6254 / 0.3861 / 0.3201 | normal | 13,383 | Y |
| 4 | HYCON HY2112-BB | 1S LiFePO4 cell protector (OV 3.75 V, UV 2.10 V) | C161942 | 0.1322 (MOQ 5) / 0.1045 / 0.0804 | normal | 2,970 | Y |
| 4a | HYCON HY2112-HB | OV 3.65 V / UV 2.50 V; no stock | C22375648 | 10.38 / 7.38 | normal | 0 | Y (unusable) |
| 5 | Fuxinsemi FS8205A | Dual N-MOSFET for protector | C908265 | 0.0724 (MOQ 10) / 0.0590 / 0.0522 | normal | 17,000 | Y |
| 6 | AOS AO3401A (×2) | P-FET: GNSS load switch; charge inhibit | C15127 | 0.0821 / 0.0647 / 0.0483 each | normal | 891,435 | Y |
| 7 | ST LIS2DH12TR | Accelerometer: motion wake, tilt/tamper | C110926 | 0.8959 / 0.5412 / 0.4950 | normal | 9,025 | Y |
| 8 | **TI TPS7A0233PDQNR** | 3.3 V nanopower LDO for 3V3 rail (X2SON 1×1) | C2860046 | 0.5801 / 0.3520 / 0.3008 | normal | 2,841 | Y |
| 8a | TI TPS7A0233DBVR | Same LDO in SOT-23-5 (easier hand assembly) | C5142805 | 0.7652 / 0.5570 / 0.5305 | normal | 7,494 | Y |
| 9 | **TI TL431AIDBZR** | Shunt clamp sense/reference (SOT-23-3) | C23892 | 0.0779 (MOQ 5) / 0.0620 / 0.0471 | normal | 130,740 | Y |
| 9a | JSMSEMI TL431 | Cheaper TL431 alternative | C963380 | 0.0350 (MOQ 20) / 0.0350 / 0.0224 | normal | 265,500 | Y |
| 10 | Power N-MOSFET, SOT-89/DPAK, ≥2 W | Shunt clamp pass element | not selected | est. 0.3 / 0.2 / 0.15 | - | - | N (UNVERIFIED) |
| 10x | Prisemi P14C13 OVP load switch | **Rejected:** 3.5 V min operating > LiFePO4 plateau; 6 V OVLO | C5274035 | 0.0552 (MOQ 10) | normal | 30,640 | Y (rejected) |
| 11 | Quectel BC660K-GL | Option A NB-IoT (lists NBTC certification [S54]) | C5126639 (valid on JLCPCB, 0 stock [S55]) | authorised distributor, price UNVERIFIED | Active per DigiKey [S54] | n/a on LCSC | N on LCSC |
| 11a | SIMCom SIM7022 | NB-IoT alternative; too few to pilot | C7363803 | 5.5934 / 5.4459 | normal | 5 | Y (insufficient) |
| 12 | Ebyte E22-900M22S | Not used (SX1262 + MCU, same as 1d) | C411293 (valid on JLCPCB/EasyEDA [S55]) | - | - | n/a on LCSC | N on LCSC |
| 13 | MICRONE ME6211C33M5G-N | **Dropped** (replaced by TPS7A02; higher Iq, UNVERIFIED value) | C82942 | 0.0630 / 0.0511 / 0.0399 | normal | 261,690 | Y (unused) |
| 14 | Ground Control RockBLOCK 9603 | Option B: Iridium SBD | n/a (not LCSC) | 302.00 (USD, maker store) | maker-listed | - | Y price [S29]; lifecycle UNVERIFIED (a newer 9704/IMT family exists [S56]) |
| 15 | Passives, CN3795 inductor and sense resistor, Schottky, TVS, NTC, fuse, divider, connectors | Support | not selected | est. 3.2 / 2.1 / 1.55 | - | - | N |
| 16 | 2-layer PCB + assembly | | - | est. 15 / 4 / 2 | - | - | N |
| 17 | GNSS patch antenna | | not selected | est. 4 / 3 / 2 | - | - | N |
| 18 | 923 MHz marine fibreglass whip + IPEX-to-N cable | | not selected | est. 25 / 15 / 10 | - | - | N |
| 19 | LiFePO4 cell, about 3 Ah | | not selected | est. 6 / 4 / 3 | - | - | N |
| 20 | 12 V-class 2 W solar panel (Voc < CN3795 maximum) | | not selected | est. 8 / 5 / 4 | - | - | N |
| 21 | IP67 UV-stable PC enclosure, gland, vent, seals | | not selected | est. 15 / 9 / 7 | - | - | N |
| 22 | Guarded IP67 SOS push-button | | not selected | est. 6 / 4 / 3 | - | - | N |

**About the disputed C-numbers (rows 11 and 12).**
- **C411293 = E22-900M22S and C5126639 = BC660K-GL** on JLCPCB and EasyEDA [S55], so the numbers are valid.
- They have no live LCSC product today. When I opened `lcsc.com/product-detail/C411293.html` and `…/C5126639.html` (twice), the pages returned unrelated parts with no lifecycle field (a KOTL crystal and a TI SN74LVC8T245PWR). A peer reviewer using LCSC's data endpoint got "no product".
- Either way, neither part can be bought from LCSC now.

**Rough per-unit cost (USD)**

| Build | @1 | @100 | @1000 |
|---|---|---|---|
| LCSC-verified core (rows 1, 2, 3, 4, 5, 6×2, 7, 8, 9) | 13.80 | 11.39 | 11.00 |
| Unverified rest (rows 10, 15–22) | ~82.5 | ~46.3 | ~32.7 |
| **LoRa variant total** | **~96** | **~58** | **~44** |
| + NB-IoT option (BC660K-GL from an authorised distributor + antenna + SIM holder) | UNVERIFIED (~+9 if similar to SIM7022) | UNVERIFIED | UNVERIFIED |
| + Iridium option (RockBLOCK 9603 + 5 V boost, UNVERIFIED ~1) | +~303 | volume pricing UNVERIFIED | UNVERIFIED |

For comparison, THB 20,000 for a Thai VMS install [S22] is about USD 615 at THB 32.5 per USD. That rate is derived from 2024 export figures [S9] and should be treated as approximate.

### 2.3 Comms options compared

| Option | Coverage near Thai coast | Unit HW | Recurring | Status / notes |
|---|---|---|---|---|
| **Private LoRaWAN, AS923 (920–925 MHz)** | Radio horizon is about 4.12(√h1+√h2) km [S36]. A 3 m boat antenna and a 30 m shore mast give about 30 km line of sight. Deck-level nodes in practice reach about 2–5 km, and masthead nodes 10–30 km [S36] (community reports, **UNVERIFIED** for Thai conditions). Needs shore gateways near PIPO ports. | RAK3172-T-9 about $8 | About $0 airtime. Gateways plus backhaul plus server. Gateway price **UNVERIFIED**, roughly a few hundred USD each. | **Recommended default.** NBTC TS 1033-2560 (2017): 920–925 MHz, up to 4 W EIRP, 10% duty cycle per hour above 50 mW EIRP (1% below), Class A type approval above 50 mW [S32]. A 2026 Meshtastic change cites the 2022 revision TS 1033-2565 as 27 dBm (500 mW) and 10% [S33]. **Conflict UNVERIFIED**, so design at 25 dBm EIRP or below (22 dBm module plus 3 dBi). Access is by duty cycle or by FHSS with a 0.4 s dwell limit [S32]. If dwell applies, SF10 can carry only 11 bytes [S58], so plan SF9 (section 4.2). Avoid the public TTN network: 96 reports a day at SF10 use 43.5 s of airtime, over its 30 s/day fair-use limit [S35]. |
| NB-IoT (AIS, True) | Operators claim national coverage: AIS across 77 provinces, True across all districts [S34]. No offshore range is published. **UNVERIFIED** at sea. Roughly 10–15 km rural [S34]. | SIM7022 about $5.6 | True 2018 price: THB 330 per SIM per year [S34]. Current price **UNVERIFIED**. | **Opt-in only; not in the first pilot.** The operator sees the IMEI and IMSI and holds a cell-level location trail outside the steward's governance (section 4.4). No healthy LCSC source: buy BC660K-GL from an authorised distributor. |
| LTE-M | Not assessed | - | - | Not costed. Operator support in Thailand **UNVERIFIED**. |
| **Iridium SBD** | Global | RockBLOCK 9603: $302 [S29] | PAYG: $17 per month line rental plus $0.08–0.20 per 50-byte credit. Contract "Low": $15.75 per month for 5 KB [S28]. | Use on a **fixed 6-hourly schedule** with the SOS flag carried in the next slot (4 messages a day, about 120 a month at about 20 B each, about 2.4 KB, inside the 5 KB plan). Iridium computes the transmitter's coarse position on its own (section 4.4). Draws about 45–50 mA average over 60 s per send at 5 V [S30] (search summary, **UNVERIFIED** against the PDF). |
| Swarm | - | - | - | **Discontinued.** Service ended March 2025 [S25]. |
| Astrocast | - | - | - | **Do not use.** Bankruptcy decision 19 March 2026; company in liquidation [S26]. |
| Myriota HyperPulse | Listed markets (July 2026): US, MX, AR, BR, AU, NZ, SA; Thailand not listed [S27] | - | From $0.99 per device per month (hybrid) [S27] | Thailand availability **UNVERIFIED**. Re-check later. |
| Argos / Kinéis (CLS NEMO) | Global; NEMO is hybrid GPRS plus Kinéis, built for artisanal VMS [S31] | not priced | not published [S31] | Worth asking CLS for a Thai quote. |
| AIS Class B (SO/SOTDMA) | VHF, about 10–20+ nm ship to shore; also seen by satellite-AIS | Retail about $600–950 for 5 W SOTDMA units [S43] (**UNVERIFIED**) | No airtime fee | **Not recommended as the tracker.** It broadcasts the vessel's MMSI and position to anyone, including brokers or traffickers. It can be switched off. It is already required on 60–300 GT vessels [S24]. Fine where the law requires it; wrong for crew protection. |

### 2.4 Power budget
Assumptions:
- LoRa TX draws 87 mA at 20 dBm [S37] (868 MHz figure; the 923 MHz figure is **UNVERIFIED**).
- Frame on air is **30 bytes**: 17-byte payload plus 13 bytes of LoRaWAN overhead (MHDR 1, DevAddr 4, FCtrl 1, FCnt 2, FPort 1, MIC 4).
- My recomputation with the Semtech time-on-air formula (125 kHz, CR 4/5, 8-symbol preamble, explicit header, CRC on; low-data-rate optimisation at SF11/12): **SF7 71.9 ms, SF9 226.3 ms, SF10 452.6 ms, SF12 1,646.6 ms.** This matches the peer reviewer's figures. Revision 1's 412 ms was wrong: it assumed a 25-byte frame.
- A compact 11-byte payload (24 B on air, section 4.3) takes 370.7 ms at SF10 and 205.8 ms at SF9.
- GNSS draws 25 mA (<25 mA spec) [S38].
- Sleep draws about 27 µA in total: RAK3172 1.69 µA [S37], TPS7A02 0.025 µA [S53], GNSS backup about 10 µA (**UNVERIFIED**), accelerometer, protector, clamp divider and leakage about 15 µA (**UNVERIFIED**). The LDO's quiescent current is negligible; that is why it replaced the ME6211.

| Mode | Data rate | GNSS on-time per fix | Reports per day | mAh/day | Airtime s/day |
|---|---|---|---|---|---|
| At sea, 15 min, warm start | SF9 (dwell-compliant) | 30 s | 96 | 21.5 | 21.7 |
| At sea, 15 min, hot start | SF9 | 5 s (**UNVERIFIED** TTFF) | 96 | 4.8 | 21.7 |
| At sea, 15 min, warm start | SF10 (only if no dwell limit) | 30 s | 96 | 22.0 | 43.5 |
| NB-IoT, 15 min (about 100 mA × 3 s per send, UNVERIFIED) | - | 30 s | 96 | 28.6 | - |
| Iridium, fixed 6-hourly (4 per day) | - | 30 s | 4 | about 7 (1.5 mAh/message, UNVERIFIED) | - |

Sizing:
- **Duty cycle:** about 0.9–1.8 s of TX per hour, far inside the NBTC 10% (360 s per hour) limit [S32].
- **TTN would not work:** 21.7 s/day fits its fair-use limit only at SF9, and 43.5 s/day at SF10 breaks it [S35]. A private network is still recommended.
- **Battery:** 7 days with no sun at 22 mAh/day needs 154 mAh. With 2× margin and 80% usable that is about 400 mAh. A 3 Ah cell gives more than 3 weeks of autonomy.
- **Panel:** coastal Thai provinces average about 4.7–5.1 kWh/m²/day [S42]. That gives an energy need of about 0.03 W of panel, so 2 W leaves a large margin. The 12 V class is there for the CN3795 input range.
- **Heat and salt are the real constraints, not energy.**
- **Cover traffic (section 4.2) adds about 2 extra reports a day:** under 0.5 mAh/day and about 0.5 s of airtime.

### 2.5 Enclosure and mechanics
- IP67 UV-stabilised polycarbonate, in white or a light colour to cut solar heating.
- Stainless (A4) screws.
- ePTFE membrane vent to stop condensation pumping.
- Conformal coat on the PCB.
- Marine-rated cable glands.
- Mounted high (on the wheelhouse roof) to give the GNSS and LoRa antennas clear sky and height.
- **Tamper evidence, not concealment.** Numbered security seals on the lid, a lid switch and an accelerometer orientation baseline are logged (section 4.6). The unit is clearly labelled in Thai, Burmese, Khmer and Lao: "Vessel position tracker. Does not record sound, images or people."
- SOS button under a flip guard to cut accidental presses. It sits where crew can reach it, not inside the wheelhouse only.
- Salt fog and UV testing would be needed later. IEC 60068-2-52 and IEC 60945 are the likely references (**UNVERIFIED** applicability).

---

## 3. Ethics and data governance

### 3.1 Who could be harmed, and how
| Risk | Who | Mitigation in this design |
|---|---|---|
| Owners or brokers use the tracks to surveil crew: catch escapes, prove "desertion", punish people | Crew | No crew identifiers on the device. Owners see their own vessel at **coarse delay** (for example 1 h) by default, not live SOS state. The labour-help SOS is never routed to the owner. |
| Retaliation after an SOS press | Crew | SOS is silent, and its radio traffic is shaped to look exactly like routine reports (revision 2, sections 3.3 and 4.2). The LED shows only "sent", identical to a normal send. It reaches an independent responder with a pre-agreed protocol, usually acting at next port-in, not by confronting the vessel at sea. Fewer false alarms through a guarded, hold-for-3-s press. |
| Location of migrant workers leaks to traffickers or recruiters | Crew, families | Data steward, encrypted end to end, no public feed (this is why AIS is rejected for this role). Access logging and a purpose limit. |
| State misuse: corrupt officials warn owners or protect them [S19]; immigration enforcement against undocumented crew | Crew | Governance board with worker organisations. Authorities get access by written request for stated purposes. Every access is logged and the logs are reviewable by the board. Data is not shared for immigration enforcement (to be agreed in the charter; legal feasibility **UNVERIFIED**). |
| Artisanal family fishers over-policed with little labour benefit | Small-scale fishers | Participation is voluntary unless DoF mandates it. Pilot only where worker organisations and fisher associations agree (section 1.5). |
| False sense of safety | Crew | Clear briefing: the device does not watch over individuals and cannot stop abuse. It provides a channel and evidence. |

### 3.2 Design commitments (hard rules)
1. The tracker is mounted **on the vessel**, never on or near a person. **No covert beacons and nothing hidden on a person.** Concealed devices can endanger victims if found.
2. The tracker is **visible** and labelled. Crew are briefed before they sail.
3. **No microphone, camera, or Bluetooth or Wi-Fi scanning.** The STM32WLE5 has no Bluetooth or Wi-Fi radio, so phone MAC addresses cannot be collected.
4. **Data minimisation:** the message carries position, time, speed, course, battery and flags only (section 4.3).
5. **Retention (proposed, to be co-designed):**
   - Full-resolution tracks: 90 days.
   - After that, aggregate only (port calls, trip length), kept for 2 years.
   - Longer only under a documented legal hold for a specific case.
6. **Access tiers:**
   - Owner: own vessel, delayed.
   - Data steward: operations.
   - Authorities: by written purpose-bound request.
   - Worker-organisation responders: SOS events only.
   - Every read is logged.
7. **Independent stewardship:** the data is held by a neutral steward, for example a university or a civil-society trust, with a board that includes fisher worker organisations (FRN, Stella Maris and MWRN [S47][S48][S51]). It is not held by owners or by a single agency.
8. **Open design:** firmware source and hardware files are published so worker organisations can have them audited.

### 3.3 SOS: two functions, independent routing, and traffic that looks routine
- **"Danger / medical"** → Thai-MECC 1465 [S46] and the data steward. This is an ordinary maritime distress route, and the owner may be told.
- **"Labour help"** → an independent responder staffed in Thai, Burmese, Khmer and Lao (a worker organisation). That responder can escalate to the 1300 Prachabodi line (24 h, interpreters) [S45], with the crew's agreement where possible. It is **never** sent to the owner automatically.
- **Revision 2, the headline change: the press must not be detectable on the air.**
  - AES encryption hides the content, but every LoRaWAN uplink carries DevAddr, FCnt, FPort, frame length and timing in the clear [S57].
  - Revision 1's behaviour (send at once, repeat every 2 min, confirmed uplinks, a shore ACK) was a pattern a skipper with a cheap LoRa gateway or SDR could have spotted without breaking any encryption.
  - Revision 2 makes the SOS indistinguishable from routine traffic (firmware rules in section 4.2):
    - one FPort and one padded frame length for every message type;
    - always unconfirmed;
    - the SOS rides in normal report slots, with constant-rate random "early" cover reports sent by every unit;
    - no change of data rate, power or GNSS pattern;
    - acknowledgement only in the fixed-size daily downlink that every unit receives.
  - The LED behaves identically.
  - **Latency is the cost.** A labour-help SOS may take up to one report interval (15 min at sea) or, on Iridium, up to the next fixed 6-hourly slot. "Danger / medical" uses the same shaping. The co-design workshops must decide whether a faster, *visibly different* distress mode is worth the exposure. Crews can still use VHF channel 16 for life-threatening emergencies.
  - **This has to be prototyped and tested with an off-the-shelf LoRa sniffer before any other firmware work.** Pass criterion: an observer with a gateway beside the boat cannot tell SOS reports from routine reports better than chance (method UNVERIFIED; to be designed with a statistician).
- **Risks that remain:**
  - Someone nearby may see the button being pressed. Guard placement is part of co-design.
  - A press identifies the *vessel*, not a person.
  - False alarms.
  - Responders with no capacity at sea.
  - Officials leaking alerts [S19].
- **Still required before deployment:** a written response protocol (what happens in the next 1 h, 24 h and at port-in), safe follow-up interviews away from the captain [S19], and an anonymous call-back at port as the crew-facing confirmation, because a visible on-device acknowledgement would give the press away.

### 3.4 Co-design, consent and language
- Co-design workshops with crews (through FRN, Stella Maris and MWRN) and with small-scale fisher associations, before the hardware is fixed.
- Crew notice and briefing in Thai, Burmese, Khmer and Lao. Pictograms and an audio briefing for low literacy.
- The labels on the unit match the briefing.
- Consent: crew consent cannot be freely given where owners hold power over them, so the design relies on minimisation and governance rather than on consent alone.
- A complaint route about the tracker itself, run by the board.

### 3.5 PDPA (Personal Data Protection Act B.E. 2562)
- **In force:** fully effective from 1 June 2022 [S44].
- **Position data counts as personal data:** vessel position linked to a registered owner, skipper or crew list is personal data.
- **Not "sensitive" data:** location is not in the section 26 list of sensitive categories [S44].
- **Obligations that apply:**
  - Lawful basis. That is legal obligation if DoF mandates the tracker; otherwise legitimate interest and/or consent, with a documented assessment.
  - Privacy notice in the crew's languages.
  - Data-subject rights.
  - Security measures.
  - Breach notification.
  - Limits on cross-border transfer if the servers are outside Thailand. Host in Thailand.
  - A DPO is likely required for regular, large-scale monitoring.
  - Section numbers for the last two points come from secondary overviews (**UNVERIFIED** — confirm with Thai counsel) [S44].
- **Penalties:** administrative fines of up to THB 5 M, with criminal penalties for some misuse of sensitive data [S44].

---

## 4. Firmware plan (design level only, no code)

**Language and base.**
- The firmware is written in **C on the STM32WLE5** (Arm Cortex-M4, Thumb-2).
- Assembly is limited to the CMSIS startup, the vector table and fault handlers.
- The LoRaWAN MAC, AES-CMAC/CTR, ECDSA and the NMEA parser use vetted C libraries. A pure-assembly firmware is not realistic and is not planned.
- The stack and licence choice is in section 4.7.

### 4.1 State machine
```
BOOT -> SELFTEST (battery, GNSS UART alive, radio init, tamper baseline, log integrity, image hash)
     -> PROVISIONED? --no--> SAFE_IDLE (LED pattern; no transmit)
                     --yes-> JOIN (LoRaWAN 1.0.4 OTAA; DevNonce persisted in NVM) -> SLEEP

SLEEP (Stop2, RTC + EXTI armed; ~27 uA total)
  wake: RTC slot timer | accelerometer motion | SOS button | tamper switch | charger/temperature event
  (SOS and tamper wakes only SET A FLAG and return to sleep; they never trigger a transmission by themselves)

On RTC slot wake -> classify:
  IN_PORT  : stationary + within port geofence -> report every 4 h (EJF suggested 4 h in-port VMS [S3])
  AT_SEA   : moving or outside geofence -> report every 15 min (Thai VMS is hourly [S23])
  LOW_BATT : VBAT < ~3.05 V -> 60 min; 3V3 < ~2.97 V -> GNSS off, last-fix reports; < 2.8 V -> reserve
  In every state: also draw "early cover slots" at random (Poisson, ~2/day, same for all units)
Build the one fixed-length report (pending SOS/tamper flags included, encrypted) -> unconfirmed uplink -> SLEEP
```
- **Leaving port triggers fast reporting:** motion while in IN_PORT moves the unit to AT_SEA, so leaving port is never missed [S3]. Every unit does this, so it reveals nothing about an SOS.
- **Over-temperature:** the MCU opens the charge-inhibit P-FET (hysteresis). The overvoltage clamp is hardware and works without the MCU (section 2.1).

### 4.2 GNSS, report duty cycle and SOS traffic shaping
- **GNSS:**
  - Powered through its P-FET, with the VBAT backup pin kept on for hot starts.
  - Each fix has a 60 s timeout and needs at least 4 satellites and reasonable HDOP. If there is no fix, the report carries the last fix with a "stale" flag.
  - The GNSS on-time follows the slot schedule, never the SOS.
- **Data rate:**
  - **SF9 (DR3) by default.** If NBTC type approval uses the FHSS access route with its 0.4 s dwell limit [S32], AS923 with UplinkDwellTime = 1 forbids DR0/DR1 and caps DR2 (SF10) at an 11-byte application payload [S58]. The 17-byte report then needs SF9 or faster (DR3 allows 53 B [S58]).
  - If more range is needed under dwell, use the compact 11-byte format at SF10 (370.7 ms on air).
  - Confirm which access route applies with NBTC (section 1.5). Redo the range estimate at SF9.
  - Respect the 10%-per-hour duty cycle. Add ±60 s of jitter to every slot.
- **SOS indistinguishability rules (revision 2):**
  1. **One FPort** (a single application port) for all message types. The type field is inside the encrypted payload.
  2. **One frame length**: every report is padded to the same size (17 B, or 11 B in compact mode).
  3. **Always unconfirmed uplinks.** Reliability comes from several steward gateways and from repeating the SOS flag in *every subsequent normal report* until it is cleared by downlink. No extra sends.
  4. **No SOS-only cadence.** The flag waits for the next scheduled slot or the next random cover slot. Every unit sends cover slots at the same rate whether or not anything is pending, so an early report proves nothing.
  5. **Same data rate, TX power, channel plan and GNSS pattern** whatever the content. No ADR change is triggered by SOS.
  6. **Acknowledgement only through the regular downlink schedule.** The network server sends every unit one fixed-size downlink a day, in the receive window after a scheduled uplink. It carries an encrypted "SOS received" bit, or padding. There is no immediate ACK.
  7. **The backlog is shaped:** the store-and-forward flush is rate-limited, for example one extra frame per slot, so a returning unit doesn't produce a distinctive burst. 672 frames at SF9 would be about 152 s of airtime.
  8. **No join requests or MAC commands are triggered by SOS.**
  9. **Iridium variant:** fixed 6-hourly sends whatever the content (an L-band burst is also detectable with an SDR nearby). **NB-IoT variant:** fixed schedule, with the same padding and no immediate ACK.
- **Store-and-forward:** up to about 7 days of reports. The ring is **encrypted at rest**. Use a shorter window, such as 48 h, if the co-design finds the backlog isn't needed.

### 4.3 Message format (fixed length; no identifiers in the payload)
**Standard report (17 bytes, all types share this layout; unused fields zero-filled):**

| Field | Bits | Notes |
|---|---|---|
| version (4) + msg type (4) | 8 | position, heartbeat, cover, boot. *SOS and tamper are flags, not types* |
| sequence | 16 | wraps; gap and replay detection |
| time | 32 | minutes since a project epoch |
| latitude | 24 | about 1.2 m resolution |
| longitude | 24 | about 2.4 m resolution |
| speed | 8 | 0.2 kn steps |
| course | 8 | about 1.4° steps |
| battery | 8 | 10 mV steps above 2.5 V |
| flags | 8 | SOS-danger, SOS-labour, tamper-since-last, in-port, stale-fix, low-batt, charge-inhibit, GNSS-antenna-fault |

That is 136 bits = 17 B, plus 13 B of LoRaWAN overhead = **30 B on air** (452.6 ms at SF10, 226.3 ms at SF9).

**Compact report (11 bytes, for SF10 under dwell):**
- type/flags-high 1 B
- sequence 1 B
- time offset 2 B (minutes, about 45-day wrap)
- latitude 3 B
- longitude 3 B
- battery+flags-low 1 B

That is 24 B on air and 370.7 ms at SF10. Speed and course are dropped.

Vessel identity is resolved only on the steward's server.

### 4.4 Identifiers: what leaks, and how it is scoped to the vessel
| Identifier / signal | Where | Seen by | Policy and honest limits |
|---|---|---|---|
| LoRaWAN DevEUI (64-bit) | **Sent in the clear in every OTAA Join-Request**, with JoinEUI and DevNonce [S57] | Anyone listening in range | **A stable vessel pseudonym.** Monthly rejoin changes DevAddr, but the next Join-Request re-announces the same DevEUI, so sessions *can* be linked to one boat. That is acceptable only because the DevEUI is steward-assigned, mapped to the vessel registration (vessels already carry visible registration markings; Thai marking rule UNVERIFIED), and never to a person. Override the RAK factory DevEUI, which is printed on the module label and tied to RAK sales records. Never print the DevEUI or a QR code on the outside label. Re-key on change of owner. |
| DevAddr, FCnt, FPort, frame length, timing | Clear-text header of every uplink [S57] | Anyone listening | Shaped per section 4.2 so that content and SOS state don't leak. |
| Session keys (AppKey, NwkKey) | On device, under read-out protection | Steward | Generated by the steward or the STM32WLE5 hardware RNG at provisioning (TRNG presence per ST docs, **UNVERIFIED**). Key custody is written down, and every re-key is logged by the board. |
| STM32 96-bit unique ID | On chip | Any code on the chip, or a debug probe | **Not secret, so never used as key material or seed** (corrects revision 1). Never transmitted or logged off the device. |
| NB-IoT IMEI (module), IMSI (SIM) | Network attach | Mobile operator (AIS, True) | Unavoidable. SIM registered to the steward organisation (Thai IoT SIM registration rules **UNVERIFIED**). **Every NB-IoT session also gives the operator a cell-level location history held outside the steward's governance and access logs.** Thai authorities can presumably reach it through the operator (legal route **UNVERIFIED**). The modem's AT firmware is closed and may accept vendor or operator FOTA (BC660K-GL lists DFOTA [S54]). So **NB-IoT is opt-in only, disclosed in the crew briefing, and not in the first pilot** unless there is a clear coverage need. |
| Iridium IMEI + Iridium-computed position | SBD network | Iridium and the reseller (US jurisdiction) | Iridium attaches its own estimate of the transmitter's location (CEP about 10 km) to messages delivered by DirectIP, even with an empty payload [S59]. Low risk at 4 messages a day, but disclosed. Account held by the steward. |
| AIS MMSI | Public VHF broadcast | Everyone | The tracker never adds AIS. Where the law requires AIS [S24], the tracker links no crew data to the MMSI. |
| RF emissions (any transmitter) | Air | Anyone with direction-finding or multi-gateway RSSI/timing | **A transmitter can always be located; this is mitigated, not removed.** The unit is visibly on the vessel, so direction-finding reveals nothing beyond the boat. The network and application servers and the gateways are run only by the steward. Governance covers gateway metadata too. |
| GNSS module | Receive only | - | Transmits nothing. |

### 4.5 Flash budget, update policy and signing
- **Flash (256 KB on the STM32WLE5CC [S37]) is the tight resource, not RAM (64 KB).** Indicative layout, all **UNVERIFIED** until a first build is measured:
  - signed bootloader with ECDSA P-256 verification and an anti-rollback counter: about 24–32 KB;
  - application with the LoRaWAN stack (AS923): about 120–160 KB;
  - LoRaWAN NVM (DevNonce, frame counters): 2–4 KB;
  - hash-chained tamper log: 8 KB;
  - 7-day encrypted ring (672 × 17 B, about 11.4 KB plus overhead): 12–16 KB.
- An **A/B dual image probably won't fit**. Plan a single slot, with a recovery path through the service port. The RAK3172-F variant has 512 KB of external flash, but its LCSC stock is **UNVERIFIED**.
- **No over-the-air updates over LoRa by default.** Updates happen only over a sealed service connector, at a PIPO port or an authorised workshop, and are logged as tamper-class events.
- Images are signed. **Two-party signing:** the steward and a worker-organisation technical reviewer must both sign off.
- Release notes and image hashes are published, with reproducible builds as a goal.
- **Read-out protection versus auditability:**
  - **Do not permanently lock RDP Level 2**: it disables debug forever, so worker-organisation auditors could never verify a deployed unit.
  - Use RDP Level 1 (regressing it wipes flash; STM32WL behaviour per ST reference manual, **UNVERIFIED**).
  - Add a service-port **attestation**: the bootloader reports the hash of the running image, auditors compare it with the published hash, and the result is logged.
- Whether the chip has a PKA accelerator for ECDSA is **UNVERIFIED**. Software verification at boot on a Cortex-M4 is feasible either way.
- A feature that would collect audio, images or person-level data is a charter violation, not a firmware option.

### 4.6 Tamper-evidence log
- **Events logged:**
  - Lid open or close.
  - Orientation change beyond a threshold.
  - Main power or panel disconnect.
  - Brown-out reset.
  - Clamp activation (sensed by an ADC on the SYS rail).
  - GNSS antenna open or short (ATGM336H antenna detection [S41]).
  - Service-port use, firmware updates and attestation reads.
  - Repeated GNSS no-fix.
- **How the log is kept:** append-only and hash-chained in on-chip flash. Each entry holds a monotonic counter, the time, an event code and the hash of the previous entry.
- **How it is reported:** a flag bit in the next *normal* report (same shaping as SOS). The full log is read at port.
- **Who sees it:** tamper events go to the **steward**, not straight to owners. The response protocol assumes tampering may come from anyone, on board or off.

### 4.7 Firmware and hardware licences (checked 18 Nov 2026)
| Component | Licence | Decision |
|---|---|---|
| RAK RUI3 (RAK-STM32-RUI) | BSD-3-style **plus clause 4: "must only be used with a RAKwireless product"** [S60] | **Not used.** It would break the SX1262 second-source plan. |
| STM32CubeWL: HAL, LoRaWAN and SubGHz_Phy middleware, Utilities | BSD-3-Clause (CMSIS and mbed-crypto: Apache-2.0) [S61] | **Chosen base** (option 1) |
| Semtech LoRaMac-node | Revised BSD [S62] | **Chosen base** (option 2; ports to the SX1262 plan B) |
| STM32CubeWL Secure Engine, KMS and "Projects" (including the secure-boot examples) | **ST SLA0044**: restricted to ST devices; BSD-3-Clause only for basic examples [S61] | Read the terms before reusing any secure-boot example. A bootloader written from scratch on BSD code is the safer route for open publication. |
| NB-IoT modem AT firmware (BC660K-GL, SIM7022) | Proprietary, closed (terms **UNVERIFIED**) | Can't be audited; treated as a black box (section 4.4). |
| Iridium/RockBLOCK libraries; ECDSA library choice; Meshtastic (cited only) | **UNVERIFIED** | Record each licence before implementation. Do not copy GPL code into BSD firmware. |
| This hardware design (schematic, PCB, enclosure drawings) | Recommend **CERN-OHL-S v2** (https://ohwr.org/cern_ohl_s_v2.txt) | Anyone may build, repair and modify it but must share changes, so a vendor cannot close the design. RAK and modem module layouts stay the vendors'; only the carrier board is covered. |
| Module footprints and reference layouts (RAK3172, modem vendors), if copied | **UNVERIFIED** | Read each vendor's design-file terms before copying a reference layout; drawing footprints from datasheet dimensions avoids the question. |

---

## 5. Risks, limitations and open questions

**Technical**
- **SOS traffic shaping is unproven.** It must pass a sniffer test before anything else (section 4.2). Statistical leaks through timing, backlog behaviour or downlink presence are subtle.
- LoRa range at sea depends on antenna height, sea state and gateway siting. 30 km is a line-of-sight best case, and SF9 (if the dwell limit applies) shortens it. Boats that range further offshore if the section 34 repeal takes effect will drop out of coverage.
- The overvoltage shunt clamp is a design concept only. The trip accuracy, MOSFET dissipation, panel Isc/Voc and the CN3795's failure modes are all **UNVERIFIED** (bench item 1).
- Supply: the module risk eased with RAK3172-T-9-SM-I at 428 in stock. **NB-IoT has no healthy LCSC source:** SIM7022 had 5 in stock, and the peer reviewer saw SIM7020E listed as `stop_product`.
- Flash is tight: 256 KB with a signed bootloader, log and ring (section 4.5).
- **Conflicting NBTC limits** (4 W EIRP in TS 1033-2560 against 27 dBm cited for TS 1033-2565) and an open question of duty-cycle versus FHSS-dwell access [S32][S33]. Type approval costs are **UNVERIFIED**.
- The device is not a certified Thai VMS (Model 1/2 [S23]). It complements VMS and cannot replace it.
- Heat: enclosure interiors on deck can far exceed the cell's charge-temperature limits.

**Social and ethical**
- Location data does not show forced labour directly. Benefit to crew depends mostly on the SOS channel and on governance.
- Risk that the device is captured by owners or officials. Mitigated by governance and audit, not eliminated.
- Consent under unequal power is weak (section 3.4).
- **SOS latency against concealment** is a trade-off for crews to decide (section 3.3).

**Open questions**
1. Which segment does DoF see as the gap (section 1.5)?
2. Will responders commit to a 24/7 multilingual protocol?
3. Will Thai-MECC 1465 accept machine-originated alerts?
4. Final status of the 2025 amendment and of the 29 August 2025 VMS regulation [S9][S11].
5. Does NBTC approval use duty-cycle or FHSS (dwell) access?
6. Myriota or Kinéis availability in Thailand [S27][S31].

## 6. Next steps
1. Send the section 1.5 questions to DoF/FMC, DLPW, Thai-MECC and NBTC, and to FRN, Stella Maris and MWRN. **Stop if the answer is no gap or too risky.**
2. Run co-design workshops to fix the SOS semantics and latency, retention, access tiers and labels.
3. Write the governance charter and the PDPA assessment with Thai counsel.
4. Bench prototype (later, and only with approval), in this order:
   1. LDO and shunt clamp under a simulated charger short (power only).
   2. **SOS indistinguishability test with an off-the-shelf LoRa sniffer.**
   3. Real sleep current and GNSS TTFF.
   4. Flash map with the stack, bootloader and log.
   5. Range from one PIPO-port gateway at the dwell-compliant data rate.
5. Pilot of about 20 vessels at one port for 3 months, LoRa only (no NB-IoT), chosen with worker organisations. Publish the results, including harms found.
6. Decide whether to scale, change or stop, based on crew-safety outcomes, not tracking coverage.

## 7. Revision history and review response

**Revision 1** (18 Nov 2026): first desk study.

**Revision 2** (18 Nov 2026): responds to a written peer review of the parts list and firmware plan. Each item is listed with how it was resolved.

| # | Review item | Resolution |
|---|---|---|
| 1 (**headline**) | **The SOS is encrypted but visible on air** (repeat burst, confirmed uplinks, clear-text FPort and length, shore ACK) | **Redesigned.** One FPort, one padded length, always unconfirmed, the SOS flag carried only in normal or cover slots, constant-rate random cover traffic from every unit, ACK only in the fixed daily downlink, shaped backlog, the same rules on Iridium and NB-IoT. The latency trade-off is handed to co-design. **Sniffer test is the first firmware prototype.** (Sections 0, 3.3, 4.1–4.3, 6) |
| 2 | 3.75 V protector trip outside the STM32WLE5's 1.8–3.6 V operating range (3.9 V absolute maximum) | Added a TPS7A0233 3.3 V LDO (C2860046 / C5142805, verified on LCSC; 25 nA Iq, 6.0 V maximum input, from the TI datasheet) feeding the RAK3172, GNSS and accelerometer. The 3.9 V absolute maximum was confirmed in DS13105 Table 21. Parts table, BOM and sleep budget updated. (Sections 2.1, 2.2, 2.4) |
| 3 | No MCU-independent overvoltage protection if the charger fails short to the panel | Added a TL431-sensed (C23892, verified) shunt clamp at about 3.95 V with a ≥2 W MOSFET and a panel fuse. It relies on the panel's limited Isc. Panel Voc of about 21–22 V is noted (UNVERIFIED). An OVP load switch (P14C13) was checked and rejected: its 3.5 V minimum operating voltage is too high for LiFePO4. (Section 2.1) |
| 4 | Identifier leaks understated | Identifier table rewritten. The DevEUI is a stable vessel pseudonym at every join, and rejoin doesn't make it unlinkable. The STM32 UID is never used for keys. NB-IoT gives the operator a cell-level location trail outside the steward, so it is opt-in and not in the first pilot. Iridium computes coarse position. Any transmitter is direction-findable, mitigated by visible on-vessel placement and steward governance. (Section 4.4) |
| 5 | Sourcing | Module switched to RAK3172-T-9-SM-I (C19723909: normal, 428 in stock, USD 7.9804, TCXO, −40…+85 °C; verified). NB-IoT is to come from an authorised distributor as BC660K-GL (NBTC certification listed in the Quectel spec, verified). C411293 (E22-900M22S) and C5126639 (BC660K-GL) were corrected to *valid on JLCPCB/EasyEDA, no live LCSC product today*. (Section 2.2) |
| 6 | Firmware arithmetic and feasibility | Written in C on the STM32WLE5, not assembly. 30 B on air. Recomputed time on air: SF10 452.6 ms, SF9 226.3 ms. If the AS923 400 ms dwell applies, DR2 is capped at an 11-byte payload (RP002 table), so SF9 is planned or the 11-byte compact format at SF10 (370.7 ms). Flash budget added, with a single-slot image. RDP Level 1 plus image attestation, no permanent Level 2. (Sections 2.4, 4.2, 4.3, 4.5) |
| 7 | Firmware licences | RUI3's RAK-only clause was confirmed, so RUI3 is not used. The base is STM32CubeWL middleware (BSD-3-Clause) or Semtech LoRaMac-node (Revised BSD), both confirmed from the licence files. ST SLA0044 covers the secure-boot examples. Modem AT firmware is closed. (Section 4.7) |

Credit: peer review (18 Nov 2026). Items in that review that I could not verify are carried as UNVERIFIED in the text and in `SOURCES.md`.
