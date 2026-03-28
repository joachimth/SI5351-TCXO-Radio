# Bill of Materials

All components sourced from LCSC unless otherwise noted. Part numbers reference the LCSC catalogue.

> **Updated: 2026-03-28 — Aligned with schematic Rev4. Major changes vs Ver3: bulk decoupling redesigned (100 µF tantalum replaces multi-layer 100 nF arrays on power rails), 1 µF and 10 µF decoupling changed from tantalum to MLCC, DC input changed to barrel jack (DC101), speaker terminal changed to screw block (P1201), C1206 changed from 1000 V film cap to standard 100 nF MLCC, R502 restored to precision 50 Ω, AMS1084CM-5.0 LCSC number confirmed (C56105), AMS1117-5.0 renumbered U103.**

---

## Capacitors

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 1 | 16 | 100 nF | C101, C102, C103, C108, C111, C114, C121, C201, C202, C204, C401, C402, C403, C504, C802, C803 | 0805 | YAGEO | CC0805KRX7R9BB104 | C49678 | Basic |
| 2 | 14 | 100 µF | C104, C105, C106, C107, C109, C110, C112, C113, C115, C116, C117, C118, C119, C120 | CASE-B 3528 (tantalum) | Kyocera AVX | TAJB107K006RNJ | C16133 | Basic |
| 3 | 1 | 1 µF | C203 | 0805 | SAMSUNG | CL21B105KBFNNNE | C28323 | Basic |
| 4 | 2 | 100 pF | C301, C303 | 0402 | FH | 0402CG101J500NT | C1546 | Basic |
| 5 | 1 | 3.3 pF | C302 | 0402 | FH | 0402CG3R3C500NT | C1565 | Basic |
| 6 | 2 | 220 pF | C404, C405 | 0603 | KEMET | C0603Y221K5RACAUTO | C2170700 | Extended |
| 7 | 11 | 1 nF | C501, C502, C503, C701, C702, C704, C804, C1001, C1004, C1201, C1205 | 0402 | TAIYO YUDEN | UMK105CG102JVHF | C650981 | Extended |
| 8 | 3 | 1 µF | C505, C705, C1005 | 0402 | SAMSUNG | CL05A105KA5NQNC | C52923 | Basic |
| 9 | 1 | 4.7 pF | C506 | 0402 | FH | 0402CG4R7C500NT | C1569 | Basic |
| 10 | 1 | 270 pF | C507 | 0402 | FH | 0402B271K500NT | C1533 | Basic |
| 11 | 2 | 4.7 pF | C703, C1003 | 0402 | muRata | GJM1555C1H4R7WB01D | C88894 | Extended |
| 12 | 1 | 15 pF | C706 | 0402 | FH | 0402CG150J500NT | C1548 | Basic |
| 13 | 1 | 220 µF | C801 | SMD 6.3×6.6 mm (electrolytic) | JIERR | RVT6.3V220M6X5 | C49233121 | Extended |
| 14 | 4 | 100 nF | C805, C806, C1101, C1103 | 0603 | YAGEO | CC0603KRX7R9BB104 | C14663 | Basic |
| 15 | 4 | 10 µF | C1002, C1202, C1203, C1204 | 0603 | SAMSUNG | CL10A106KP8NNNC | C19702 | Basic |
| 16 | 1 | 1 nF | C1006 | 0402 | FH | 0402B102K500NT | C1523 | Basic |
| 17 | 1 | 15 pF | C1007 | 0402 | YAGEO | CC0402GRNPO9BN150 | C325460 | Extended |
| 18 | 3 | 10 nF | C1102, C1104, C1107 | 0805 | SAMSUNG | CL21B103KBANNNC | C1710 | Basic |
| 19 | 2 | 4.7 µF | C1105, C1106 | 0603 | SAMSUNG | CL10A475KO8NNNC | C19666 | Basic |
| 20 | 1 | 100 nF | C1206 | 0402 | SAMSUNG | CL05B104KO5NNNC | C1525 | Basic |

> **Note on row 2:** C104–C120 (14 units) are 100 µF tantalum CASE-B 3528 providing bulk supply decoupling across all power rails. These are JLCPCB Basic and replace the multi-layer 100 nF 0805 array used in Ver3.
> **Note on row 7:** C703 changed from 1 nF to 4.7 pF (LO matching) and C1003 changed from 10 µF tantalum to 4.7 pF (LO matching) vs Ver3 — reflects schematic Rev4 changes.
> **Note on row 8:** 1 µF bypass caps changed from tantalum 3.2×1.6 mm (Extended) to SAMSUNG 0402 MLCC (Basic).
> **Note on row 11:** GJM1555C1H4R7WB01D is a muRata Extended part. Verify availability at time of order; FH equivalent (C1569) may be usable if C0G dielectric specification is met.
> **Note on row 15:** 10 µF supply decoupling changed from tantalum 3.2×1.6 mm (Extended) to SAMSUNG 0603 MLCC (Basic).
> **Note on row 20:** C1206 changed from 100 nF/1000 V radial film cap (C9900014948) to standard 100 nF 0402 MLCC. Function is unchanged (AUDIO_OUT AC coupling to PAM8406 input).

---

## Trimmers

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 21 | 1 | 1.5–3 pF | CT301 | 4.5×3.2 mm | SEHWA | STC3MA03-T1 | C22468119 | Extended |

---

## Diodes / Protection

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 22 | 1 | ESD 140 V | D301 | 0402 | Littelfuse | PESD0402-140 | C10662 | Extended |

---

## DC Power Input

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 23 | 1 | Barrel jack 5.5/2.0 mm | DC101 | DC-IN-TH DC-005-5.5-2.0 | BOOMELE | DC-005 2.0 | C16214 | Basic |

> **Note:** Replaces the spring-clamp SMT connector (JL212R-SMT-50002BP1) used in Ver3. Designator changed from U103 to DC101.

---

## Inductors and Filters

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 24 | 1 | 47 nH | L301 | 0402 | muRata | LQW15AN47NJ00D | C192855 | Extended |
| 25 | 2 | 18 nH | L302, L303 | 0402 | muRata | LQW15AN18NJ00D | C82917 | Extended |
| 26 | 3 | 47 nH | L501, L701, L1001 | 0402 | muRata | LQG15HH47NH02D | C2042001 | Extended |
| 27 | 6 | 12 nH | L502, L503, L702, L703, L1002, L1003 | 0402 | muRata | LQG15HN12NJ02D | C113052 | Extended |
| 28 | 1 | 10.7 MHz BP filter | L601 | HCCF3 4-pin | HCI | HCCF3-10.700-F280IL03A5L | C42392418 | Extended |
| 29 | 1 | 455 kHz BP filter | L901 | HCCF2 4-pin | HCI | HCCF2-455.000-LTWCDL | C42392417 | Extended |

> **Note on row 25:** L302, L303 restored to muRata LQW15AN18NJ00D (C82917) in Rev4 — the Sunlord SWPA4010S18NT (C24562) alternative listed in Ver3 BOM is no longer used.

---

## Speaker Terminal

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 30 | 1 | Screw terminal 2-pin, 5.08 mm | P1201 | CONN-TH_2P-P5.00 | KANGNEX | WJ500V-5.08-2P | C8465 | Extended |

> **Note:** Replaces the spring-clamp SMT connector (JL212V-SMT-35002BP1) used in Ver3. Designator changed from CN1201 to P1201.

---

## Resistors

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 31 | 2 | 2 kΩ | R201, R202 | 0805 | UNI-ROYAL | 0805W8F2001T5E | C17604 | Basic |
| 32 | 4 | 10 kΩ | R501, R701, R1001, R1202 | 0603 | UNI-ROYAL | 0603WAF1002T5E | C25804 | Basic |
| 33 | 1 | 50 Ω | R502 | 0402 | YAGEO | RT0402BRE0750RL | C853312 | Extended |
| 34 | 2 | 3 Ω | R702, R1004 | 0603 | HKR | RCA033RFLF | C22356394 | Basic |
| 35 | 4 | 200 Ω | R1002, R1003, R1103, R1105 | 0402 | UNI-ROYAL | 0402WGF2000TCE | C25087 | Basic |
| 36 | 2 | 5.1 kΩ | R1101, R1102 | 0402 | UNI-ROYAL | 0402WGF5101TCE | C25905 | Basic |
| 37 | 1 | 51 Ω | R1104 | 0603 | UNI-ROYAL | 0603WAF510JT5E | C23197 | Basic |
| 38 | 1 | 0 Ω | R1201 | 0805 | UNI-ROYAL | 0805W8F0000T5E | C17477 | Basic |

> **Note on row 33:** R502 is restored to precision 50 Ω ±0.1% film (YAGEO C853312) for accurate IF output termination on U501. Verify Extended fee at time of order.
> **Note on row 35:** R1103 changed from 5.1 kΩ to 200 Ω vs Ver3 (op-amp input resistor change in schematic Rev4). R1105 changed from 51 Ω to 200 Ω vs Ver3.
> **Note on row 38:** R1201 changed to UNI-ROYAL 0805W8F0000T5E (C17477, Basic) from the Extended yezhan alternative used in Ver3.

---

## RF Connectors

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 39 | 1 | SMA, 3 GHz | RF301 | SMA TH | 皇捷 | 2LC15SF087 | C22363778 | Extended |

---

## Test Points

| No. | Qty | Value | Designator | Footprint | Notes |
|---|---|---|---|---|---|
| 40 | 1 | Test point | TP301 | 0.5 mm | RF filter output (RF_IN_LNA) |

---

## ICs

| No. | Qty | Value | Designator | Package | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 41 | 1 | LDO 3.3 V / 800 mA | U101 | SOT-223 | AMS | AMS1117-3.3 | C6186 | Basic |
| 42 | 1 | LDO 5.0 V / 5 A | U102 | TO-263 | AMS | AMS1084CM-5.0 | C56105 | Extended |
| 43 | 1 | LDO 5.0 V / 800 mA | U103 | SOT-223 | AMS | AMS1117-5.0 | C6187 | Basic |
| 44 | 1 | PLL clock gen. | U201 | MSOP-10 | 杭州瑞盟 | MS5351M | C1509083 | Extended |
| 45 | 2 | MMIC LNA | U401, U801 | SOT-363 | NXP | BGA2869,115 | C515583 | Extended |
| 46 | 3 | Active mixer | U501, U701, U1001 | DFN-8 (3×3 mm) | ADI | LT5560EDD#PBF | C117332 | Extended |
| 47 | 5 | 1:1 RF balun | U502, U503, U702, U703, U1002 | SMD 6-pin | Coilcraft | WBC1-1TLC | C19191631 | Extended |
| 48 | 1 | Op-amp | U1101 | TSOT-23-5 | ADI | LT6202IS5#TRMPBF | C580227 | Extended |
| 49 | 1 | Class-D stereo amp | U1201 | SOIC-16 | DIODES | PAM8406DR | C86270 | Extended |

> **Note on row 42:** AMS1084CM-5.0 LCSC number confirmed as C56105 in Rev4 (was listed as "—" in Ver3 BOM).
> **Note on row 43:** AMS1117-5.0 renumbered from U104 (Ver3) to U103 (Rev4) following removal of the spring-clamp U103 designator.

---

## Crystal / Oscillator

| No. | Qty | Value | Designator | Package | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 50 | 1 | 26 MHz TCXO | X201 | SMD 4-pin (3.2×2.5 mm) | KDS | 1XTW26000MAA | C213404 | Extended |

---

## PCB Layout Critical Notes

| Component | Tab pad net | Action required |
|---|---|---|
| LT5560 ×3 (U501, U701, U1001) | **PGND** | Connect EP (pin 9) to GND plane with vias — mandatory for thermal and RF |
| AMS1084CM-5.0 (U102) | **+5V\_P\_AMP** | Isolated polygon on +5V\_P\_AMP net — do NOT connect to GND |
| AMS1117-3.3 (U101) | **+3.3 V** | Isolated polygon on +3.3 V net — do NOT connect to GND |
| AMS1117-5.0 (U103) | **+5 V** | Isolated polygon on +5 V net — do NOT connect to GND |
| BGA2869 (U401, U801) | No tab pad | Standard SOT-363 — GND pins (2, 4, 5) to GND plane with short traces and vias |
| PAM8406 (U1201) | No tab pad | Standard SOIC-16 |

> **Warning — LT5560 absolute maximum VCC = 5.3 V.** The +5 V rail (AMS1117-5.0) is 5.0 V ±2% (4.9–5.1 V), within spec under normal conditions. Verify headroom during cold-start transients. Do not use a supply above 9 V DC without verifying the AMS1117 output under no-load conditions.

---

## JLCPCB Assembly Notes

The following parts are classified Extended by JLCPCB and carry an additional fee per unique part.

| No. | Designator(s) | Extended part (LCSC) | Notes |
|---|---|---|---|
| — | C703, C1003 | GJM1555C1H4R7WB01D (C88894) | muRata 4.7 pF C0G 0402; check Basic availability at time of order |
| — | C801 | RVT6.3V220M6X5 (C49233121) | SMD 220 µF electrolytic — confirm footprint orientation |
| — | C1007 | CC0402GRNPO9BN150 (C325460) | Low stock (~42 units) — verify availability before ordering |
| — | C404, C405 | C0603Y221K5RACAUTO (C2170700) | KEMET 220 pF 0603 — verify Basic/Extended status at order time |
| — | C501–C704 (row 7) | UMK105CG102JVHF (C650981) | Taiyo Yuden 1 nF 0402 — verify Basic/Extended at order time |
| — | CT301 | STC3MA03-T1 (C22468119) | Specialised trimmer — no Basic equivalent |
| — | D301 | PESD0402-140 (C10662) | Check JLCPCB Basic ESD 0402 alternatives |
| — | L301 | LQW15AN47NJ00D (C192855) | RF preselector inductor — value and Q critical |
| — | L302, L303 | LQW15AN18NJ00D (C82917) | RF preselector inductors |
| — | L501, L701, L1001 | LQG15HH47NH02D (C2042001) | LO injection inductors |
| — | L502, L503, L702, L703, L1002, L1003 | LQG15HN12NJ02D (C113052) | Balun matching inductors |
| — | L601 | HCCF3-10.700-F280IL03A5L (C42392418) | Specific 10.7 MHz ceramic filter |
| — | L901 | HCCF2-455.000-LTWCDL (C42392417) | Specific 455 kHz ceramic filter |
| — | P1201 | WJ500V-5.08-2P (C8465) | Speaker screw terminal — verify Basic/Extended at order time |
| — | R502 | RT0402BRE0750RL (C853312) | Precision 50 Ω ±0.1% film 0402 |
| — | RF301 | 2LC15SF087 (C22363778) | SMA through-hole — hand solder |
| — | U102 | AMS1084CM-5.0 (C56105) | 5 V / 5 A LDO — confirmed LCSC stock |
| — | U201 | MS5351M (C1509083) | SI5351-compatible PLL |
| — | U401, U801 | BGA2869,115 (C515583) | MMIC LNA — low stock, verify |
| — | U501, U701, U1001 | LT5560EDD#PBF (C117332) | Active mixer — low stock, verify |
| — | U502, U503, U702, U703, U1002 | WBC1-1TLC (C19191631) | RF balun transformer |
| — | U1101 | LT6202IS5#TRMPBF (C580227) | High-speed op-amp |
| — | U1201 | PAM8406DR (C86270) | Class-D audio amplifier |
| — | X201 | 1XTW26000MAA (C213404) | 26 MHz TCXO reference |
