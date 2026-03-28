# Bill of Materials

All components sourced from LCSC unless otherwise noted. Part numbers reference the LCSC catalogue.

> **Updated: 2026-03-28 — Aligned with schematic Ver3. Basic part substitutions applied where available.**

---

## Capacitors

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 1 | 24 | 100 nF | C101, C102, C103, C104, C105, C106, C107, C108, C109, C110, C201, C202, C204, C402, C403, C404, C802, C803, C804, C806, C1201, C1202, C1203, C1205 | 0805 | YAGEO | CC0805KRX7R9BB104 | C49678 | Basic |
| 2 | 1 | 1 µF | C203 | 0805 | SAMSUNG | CL21B105KBFNNNE | C28323 | Basic |
| 3 | 4 | 100 pF | C301, C303, C401, C801 | 0402 | FH | 0402CG101J500NT | C1546 | Basic |
| 4 | 1 | 3.3 pF | C302 | 0402 | muRata | GRM1555C1H3R3BA01D | C88929 | Extended |
| 5 | 2 | 220 pF | C405, C805 | 0603 | SAMSUNG | CL10B221KB8NNNC | C1603 | Basic |
| 6 | 12 | 1 nF | C501, C502, C503, C504, C701, C702, C703, C704, C1001, C1005, C1103, C1104 | 0402 | FH | 0402B102K500NT | C1523 | Basic |
| 7 | 1 | 4.7 pF | C506 | 0402 | muRata | GJM1555C1H4R7WB01D | C1669 (verify) | Basic |
| 8 | 3 | 1 µF | C505, C705, C1004 | 3.2×1.6 mm (tantalum) | CEC | CA45A-A-16V-1UF-K | C2976901 | Extended |
| 9 | 1 | 270 pF | C507 | 0402 | FH | 0402B271K500NT | C1533 | Basic |
| 10 | 1 | 15 pF | C706 | 0402 | FH | 0402CG150J500NT | C1792 (verify) | Basic |
| 11 | 3 | 10 µF | C1002, C1003, C1204 | 3.2×1.6 mm (tantalum) | CEC | CA45-A-16V-10uF-K | C128254 | Extended |
| 12 | 1 | 220 µF | C111 | 6.3×6.6 mm (electrolytic) | JIERR | RVT6.3V220M6X5 | C49233121 | Extended |
| 13 | 4 | 100 nF | C1006, C1007, C1101, C1102 | 0603 | YAGEO | CC0603KRX7R9BB104 | C14663 | Basic |
| 14 | 2 | 4.7 µF | C1105, C1106 | 0603 | SAMSUNG | CL10A475KO8NNNC | C19666 | Basic |
| 15 | 1 | 10 nF | C1107 | 0805 | SAMSUNG | CL21B103KBANNNC | C1710 | Basic |
| 16 | 1 | 100 nF / 1000 V film | C1206 | TH radial | — | MPP104K1000D131206B1015 | C9900014948 | Extended |

> **Note on row 11:** C1204 (page 12) is a 10 µF tantalum; combined with C1002, C1003 for a total of 3 units.
> **Note on row 12:** C111 is the sole large bulk electrolytic on the +5V\_P\_AMP rail. C112, C113 (if populated) are likely 10 µF tantalum (C128254) and 100 nF 0805 (C49678) respectively — confirm against schematic before ordering.

---

## Connectors

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 17 | 1 | Spring clamp 2-pin (speaker) | CN1201 | JL212V-SMT-35002BP1 | JILN | JL212V-SMT-35002BP1 | C5118798 | Extended |

---

## Trimmers

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 18 | 1 | 1.5–3 pF | CT301 | 4.5×3.2 mm | SEHWA | STC3MA03-T1 | C22468119 | Extended |

---

## Diodes / Protection

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 19 | 1 | ESD 140 V | D301 | 0402 | Littelfuse | PESD0402-140 | C10662 | Extended |

---

## Inductors and Filters

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 20 | 2 | 18 nH | L302, L303 | 0402 | Sunlord | SWPA4010S18NT | C24562 (verify) | Basic |
| 21 | 1 | 47 nH | L301 | 0402 | muRata | LQW15AN47NJ00D | C192855 | Extended |
| 22 | 3 | 47 nH | L501, L701, L1001 | 0402 | muRata | LQG15HH47NH02D | C2042001 | Extended |
| 23 | 6 | 12 nH | L502, L503, L702, L703, L1002, L1003 | 0402 | muRata | LQG15HN12NJ02D | C113052 | Extended |
| 24 | 1 | 10.7 MHz BP filter | L601 | HCCF3 4-pin | HCI | HCCF3-10.700-F280IL03A5L | C42392418 | Extended |
| 25 | 1 | 455 kHz BP filter | L901 | HCCF2 4-pin | HCI | HCCF2-455.000-LTWCDL | C42392417 | Extended |

---

## Resistors

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 26 | 2 | 2 kΩ | R201, R202 | 0805 | UNI-ROYAL | 0805W8F2001T5E | C17604 | Basic |
| 27 | 1 | 51 Ω | R502 | 0402 | UNI-ROYAL | 0402WGF510JTCE | C25125 | Basic |
| 28 | 2 | 200 Ω | R1002, R1003 | 0402 | UNI-ROYAL | 0402WGF2000TCE | C25087 | Basic |
| 29 | 3 | 5.1 kΩ | R1101, R1102, R1103 | 0402 | UNI-ROYAL | 0402WGF5101TCE | C25905 | Basic |
| 30 | 4 | 10 kΩ | R501, R701, R1001, R1202 | 0603 | UNI-ROYAL | 0603WAF1002T5E | C25804 | Basic |
| 31 | 2 | 51 Ω | R1104, R1105 | 0603 | UNI-ROYAL | 0603WAF510JT5E | C23197 | Basic |
| 32 | 2 | 3 Ω | R702, R1004 | 0603 | HKR | RCA033RFLF | C22356394 | Extended |
| 33 | 1 | 0 Ω | R1201 | 0805 | yezhan | YLRY05-W2-0Z | C22468035 | Extended |

> **Note on row 27:** R502 replaces the old 50 Ω ±0.1% precision film part (C853312) with the UNI-ROYAL 51 Ω ±1% Basic part. The +2% deviation is fully acceptable for IF port termination on U501.
> **Note on row 28:** R1003 is the second output pull-up resistor on the third mixer (U1001) — it was missing from the previous BOM.
> **Note on row 29:** R1103 is the feedback-network resistor on U1101 — it was missing from the previous BOM.
> **Note on row 31:** R1105 is the second output resistor on U1101 — added to match schematic page 11.

---

## RF Connectors

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 34 | 1 | SMA, 3 GHz | RF301 | SMA TH | 皇捷 | 2LC15SF087 | C22363778 | Extended |

---

## Test Points

| No. | Qty | Value | Designator | Footprint | Notes |
|---|---|---|---|---|---|
| 35 | 1 | Test point | TP301 | 0.5 mm | RF filter output |

---

## Power Input Connector

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 36 | 1 | Spring clamp 2-pin (DC input) | U103 | JL212R-SMT-50002BP1 | JILN | JL212R-SMT-50002BP1 | C5118805 | Extended |

---

## ICs

| No. | Qty | Value | Designator | Package | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 37 | 1 | LDO 3.3 V / 800 mA | U101 | SOT-223 | AMS | AMS1117-3.3 | C6186 | Basic |
| 38 | 1 | LDO 5.0 V / 5 A | U102 | TO-263 | AMS | AMS1084CM-5.0 | — | Extended |
| 39 | 1 | LDO 5.0 V / 800 mA | U104 | SOT-223 | AMS | AMS1117-5.0 | C6187 | Basic |
| 40 | 1 | PLL clock gen. | U201 | MSOP-10 | 杭州瑞盟 | MS5351M | C1509083 | Extended |
| 41 | 2 | MMIC LNA | U401, U801 | SOT-363 | NXP | BGA2869 | C515583 | Extended |
| 42 | 3 | Active mixer | U501, U701, U1001 | DFN-8 (3×3 mm) | ADI | LT5560EDD#PBF | C117332 | Extended |
| 43 | 5 | 1:1 RF balun | U502, U503, U702, U703, U1002 | SMD 6-pin | Coilcraft | WBC1-1TLC | C19191631 | Extended |
| 44 | 1 | Op-amp | U1101 | SOT-23-5 | ADI | LT6202IS5#TRMPBF | C580227 | Extended |
| 45 | 1 | Class-D stereo amp | U1201 | SOIC-16 | DIODES | PAM8406DR | C86270 | Extended |

---

## Crystal / Oscillator

| No. | Qty | Value | Designator | Package | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 46 | 1 | 26 MHz TCXO | X201 | SMD 4-pin (3.2×2.5 mm) | KDS | 1XTW26000MAA | C213404 | Extended |

---

## PCB Layout Critical Notes

| Component | Tab pad net | Action required |
|---|---|---|
| LT5560 ×3 (U501, U701, U1001) | **PGND** | Connect EP (pin 9) to GND plane with vias — mandatory for thermal and RF |
| AMS1084CM-5.0 (U102) | **+5V\_P\_AMP** | Isolated polygon on +5V\_P\_AMP net — do NOT connect to GND |
| AMS1117-3.3 (U101) | **+3.3 V** | Isolated polygon on +3.3 V net — do NOT connect to GND |
| AMS1117-5.0 (U104) | **+5 V** | Isolated polygon on +5 V net — do NOT connect to GND |
| BGA2869 (U401, U801) | No tab pad | Standard SOT-363 — GND pins (2, 4, 5) to GND plane with short traces and vias |
| PAM8406 (U1201) | No tab pad | Standard SOIC-16 |

> **Warning — LT5560 absolute maximum VCC = 5.3 V.** The +5 V rail (AMS1117-5.0) is 5.0 V ±2% (4.9–5.1 V), within spec under normal conditions. Verify headroom during cold-start transients. Do not use a supply above 9 V DC without verifying the AMS1117 output under no-load conditions.

---

## JLCPCB Assembly Notes

The following parts are classified Extended by JLCPCB and carry an additional fee per unique part. Where a Basic alternative exists it is listed; otherwise the Extended part must be used.

| No. | Designator(s) | Extended part (LCSC) | Recommended Basic alternative | LCSC | Notes |
|---|---|---|---|---|---|
| — | C302 | GRM1555C1H3R3BA01D (C88929) | Verify 3.3 pF C0G 0402 Basic availability | — | Low-value NP0 — check JLCPCB Basic library at time of order |
| — | C506 | GJM1555C1H4R7WB01D (C88894) | 4.7 pF C0G 0402 | C1669 (verify) | Was Basic in 2020; confirm current status |
| — | C706 | CC0402GRNPO9BN150 (C325460) | 15 pF C0G 0402 | C1792 (verify) | Confirm current Basic status |
| — | C505, C705, C1004 | CA45A-A-16V-1UF-K (C2976901) | No direct Basic drop-in identified | — | Tantalum 1 µF 3.2×1.6 mm — hand solder or source separately |
| — | C1002, C1003, C1204 | CA45-A-16V-10uF-K (C128254) | No direct Basic drop-in identified | — | Tantalum 10 µF 3.2×1.6 mm — hand solder or source separately |
| — | C111 | RVT6.3V220M6X5 (C49233121) | Standard 220 µF electrolytic; verify footprint | — | Bulk cap — hand solder acceptable |
| — | C1206 | MPP104K1000D131206B1015 (C9900014948) | None — 1000 V film cap, no Basic equivalent | — | TH radial, hand solder; C1206 is the speaker amp output coupling cap |
| — | CT301 | STC3MA03-T1 (C22468119) | None — specialised trimmer | — | |
| — | D301 | PESD0402-140 (C10662) | Check JLCPCB Basic ESD 0402 alternatives | — | |
| — | L301 | LQW15AN47NJ00D (C192855) | None identified | — | RF preselector series inductor — value and Q critical |
| — | L501, L701, L1001 | LQG15HH47NH02D (C2042001) | None identified | — | LO injection inductors |
| — | L502, L503, L702, L703, L1002, L1003 | LQG15HN12NJ02D (C113052) | None identified | — | Balun matching inductors |
| — | L302, L303 | muRata LQW15AN18NJ00D (C82917) | Sunlord 18 nH 0402 | C24562 (verify) | Verify MPN and current Basic status on JLCPCB |
| — | L601 | HCCF3-10.700-F280IL03A5L (C42392418) | None — specific 10.7 MHz filter | — | |
| — | L901 | HCCF2-455.000-LTWCDL (C42392417) | None — specific 455 kHz filter | — | |
| — | U102 | AMS1084CM-5.0 (no LCSC #) | None — check LCSC for current stock | — | Source separately if LCSC unavailable |
| — | U201 | MS5351M (C1509083) | None — proprietary SI5351 clone | — | |
| — | U401, U801 | BGA2869 (C515583) | None — specific MMIC | — | |
| — | U501, U701, U1001 | LT5560EDD#PBF (C117332) | None — specific active mixer | — | |
| — | U502, U503, U702, U703, U1002 | WBC1-1TLC (C19191631) | None — specific RF balun | — | |
| — | U1101 | LT6202IS5#TRMPBF (C580227) | None — specific op-amp | — | |
| — | U1201 | PAM8406DR (C86270) | None — specific class-D amp | — | |
| — | X201 | 1XTW26000MAA (C213404) | None — 26 MHz TCXO reference | — | |
| — | RF301 | 2LC15SF087 (C22363778) | None — SMA through-hole | — | Hand solder |
| — | CN1201 | JL212V-SMT-35002BP1 (C5118798) | None | — | Speaker terminal |
| — | U103 | JL212R-SMT-50002BP1 (C5118805) | None | — | DC input terminal |
| — | R702, R1004 | RCA033RFLF (C22356394) | Verify 3 Ω 0603 Basic availability | — | |
| — | R1201 | YLRY05-W2-0Z (C22468035) | Any 0 Ω 0805 jumper | — | |
