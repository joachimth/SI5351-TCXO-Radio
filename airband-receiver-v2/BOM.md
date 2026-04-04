# Bill of Materials — AM Airband Receiver V2

All components sourced from LCSC unless otherwise noted. Components shared with V1 reuse the same LCSC part numbers.

> **Status: Pre-schematic draft.** Quantities and some passive values will be finalised during schematic capture. Designators follow the same page-based convention as V1.
>
> **Architecture: double conversion.** RF → 10.7 MHz → 455 kHz → audio. Three AD8342 mixers (U501, U701, U1001). No baluns.

---

## ICs

| No. | Qty | Value | Designator | Package | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 1 | 1 | LDO 3.3 V / 800 mA | U101 | SOT-223 | AMS | AMS1117-3.3 | C6186 | Basic |
| 2 | 1 | LDO 5.0 V / 800 mA | U102 | SOT-223 | AMS | AMS1117-5.0 | C6187 | Basic |
| 3 | 1 | LDO 5.0 V / 5 A | U103 | TO-263 | AMS | AMS1084CM-5.0 | C56105 | Extended |
| 4 | 1 | PLL clock gen. | U201 | MSOP-10 | 杭州瑞盟 | MS5351M | C1509083 | Extended |
| 5 | 1 | MMIC LNA (optional) | U401 | SOT-363 | NXP | BGA2869,115 | C515583 | Extended |
| 6 | 3 | Active mixer | U501, U701, U901 | LFCSP-16 3×3 mm | ADI | AD8342ACPZ-REEL7 | C182567 | Extended |
| 7 | 1 | Op-amp | U1001 | SOT-23-5 | ADI | LT6202IS5#TRMPBF | C580227 | Extended |
| 8 | 1 | Class-D stereo amp | U1101 | SOIC-16 | DIODES | PAM8406DR | C86270 | Extended |

> **Note on row 2/3:** In V2, U102 = AMS1117-5.0 (+5V, 800 mA) and U103 = AMS1084CM-5.0 (+5V_P_AMP, 5 A). This is reversed from V1. Tab pad = Vout on both — isolated polygons required.
>
> **Note on row 6:** AD8342ACPZ-REEL7 confirmed at JLCPCB as C182567 (LFCSP-16 3×3 mm). EP = GND — connect to GND plane with vias. Three units: U501 (RF mixer), U701 (IF mixer), U901 (product detector).
>
> **Note on row 5:** BGA2869 JLCPCB stock = 0 at time of writing — verify availability before ordering.
>
> **Note on squelch:** LM393 squelch circuit was planned but is not present in the current schematic (2026-04-03). Omitted from BOM pending schematic update.

---

## Crystal / Oscillator

| No. | Qty | Value | Designator | Package | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 10 | 1 | 26 MHz TCXO | X201 | SMD 4-pin (3.2×2.5 mm) | KDS | 1XTW26000MAA | C213404 | Extended |

---

## RF Bandpass Filter (118–137 MHz)

Same topology and component values as V1. All LCSC numbers identical to V1.

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 11 | 1 | 47 nH | L301 | 0402 | muRata | LQW15AN47NJ00D | C192855 | Extended |
| 12 | 2 | 18 nH | L302, L303 | 0402 | muRata | LQW15AN18NJ00D | C82917 | Extended |
| 13 | 2 | 100 pF | C301, C303 | 0402 | FH | 0402CG101J500NT | C1546 | Basic |
| 14 | 1 | 3.3 pF | C302 | 0402 | FH | 0402CG3R3C500NT | C1565 | Basic |
| 15 | 1 | 1.5–3 pF trimmer | CT301 | 4.5×3.2 mm | SEHWA | STC3MA03-T1 | C22468119 | Extended |

---

## IF Filters

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 16 | 1 | 10.7 MHz BPF (~280 kHz BW) | L601 | HCCF3 4-pin | HCI | HCCF3-10.700-F280IL03A5L | C42392418 | Extended |
| 17 | 1 | 455 kHz BPF (±10 kHz BW) | L801 | HCCF2 4-pin | HCI | HCCF2-455.000-LTWCDL | C42392417 | Extended |

> **Note on row 17:** L801 in V2 (was L901 in earlier drafts). Provides channel selectivity (±10 kHz = 20 kHz total BW at 455 kHz IF, IL ≤6 dB). Input impedance ~1–2 kΩ; Mixer 2 IFOP pull-up of 330 Ω (corrected from 33 Ω) drives this with some mismatch loss — acceptable for prototype. Pins: 1 = input, 2 = output, 3/4 = GND.

---

## AD8342 Mixer Support Components

Three mixer stages. All EXRB bias resistors (1.82 kΩ) confirmed correct in schematic 2026-04-03. **R702/R703 (Mixer 2 pull-ups) must be corrected from 33 Ω to 330 Ω — see CurrentErrors.md.**

> **EXRB warning (all three mixers):** EXRB pin is internally biased to 1.17 V. Connection must be EXRB → R_bias (1.82 kΩ) → GND. Minimum 1.8 kΩ. **Never connect VCC to EXRB — permanent device damage results.**

### Mixer 1 (U501) — RF → 10.7 MHz

| No. | Qty | Value | Designator | Footprint | Notes |
|---|---|---|---|---|---|
| 18 | 3 | 1 µF | C501, C508, C511 | 0402 MLCC | VPDC/VPLO/VPMX bulk bypass |
| 19 | 1 | 100 nF | C503 | 0402 | VCC HF decoupling |
| 20 | 1 | 1 nF | C504 | 0402 | VCC HF decoupling (additional) |
| 21 | 2 | 100 pF | C505, C507 | 0402 | RFIN+/RFIN− coupling |
| 22 | 3 | 100 pF | C509, C512, C514 | 0402 | Supply HF bypass + LOIN AC ground |
| 23 | 1 | 100 pF (TH) | C513 | TH 5.08 mm | LOIP coupling — hand-solder (TORCH C400081) |
| 24 | 1 | 100 pF | C502 | 0402 | EXRB bypass (optional, in parallel with R501) |
| 25 | 1 | 100 pF | C506 | 0402 | IF output coupling |
| 26 | 1 | 100 pF | C510 | 0402 | IFON AC ground |
| 27 | 2 | 270 Ω | R502, R503 | 0402 | IFOP/IFON pull-ups to +5 V |
| 28 | 1 | 22 Ω | R504 | 0402 | LO series resistor |
| 29 | 1 | 1.82 kΩ | R501 | 0402 | EXRB bias — EXRB to GND (LIZ C100277) |

### Mixer 2 (U701) — 10.7 MHz → 455 kHz

| No. | Qty | Value | Designator | Footprint | Notes |
|---|---|---|---|---|---|
| 30 | 3 | 1 µF | C701, C708, C711 | 0402 MLCC | VPDC/VPLO/VPMX bulk bypass |
| 31 | 1 | 100 nF | C703 | 0402 | VCC HF decoupling |
| 32 | 1 | 1 nF | C704 | 0402 | VCC HF decoupling (additional) |
| 33 | 2 | 100 pF | C705, C707 | 0402 | RFIN+/RFIN− coupling |
| 34 | 3 | 100 pF | C709, C712, C714 | 0402 | Supply HF bypass + LOIN AC ground |
| 35 | 1 | 100 pF (TH) | C713 | TH 5.08 mm | LOIP coupling — hand-solder (TORCH C400081) |
| 36 | 1 | 100 pF | C702 | 0402 | EXRB bypass (optional, in parallel with R701) |
| 37 | 2 | **330 Ω** | R702, R703 | 0402 | IFOP/IFON pull-ups to +5 V *(schematic shows 33 Ω — must be corrected)* |
| 38 | 1 | 22 Ω | R704 | 0402 | LO series resistor |
| 39 | 1 | 1.82 kΩ | R701 | 0402 | EXRB bias — EXRB to GND |

### Mixer 3 / Product Detector (U901) — 455 kHz → audio

| No. | Qty | Value | Designator | Footprint | Notes |
|---|---|---|---|---|---|
| 40 | 3 | 1 µF | C901, C908, C911 | 0402 MLCC | VPDC/VPLO/VPMX bulk bypass |
| 41 | 1 | 100 nF | C903 | 0402 | VCC HF decoupling |
| 42 | 1 | 1 nF | C904 | 0402 | VCC HF decoupling (additional) |
| 43 | 2 | 100 pF | C905, C907 | 0402 | RFIN+/RFIN− coupling |
| 44 | 3 | 100 pF | C909, C912, C914 | 0402 | Supply HF bypass + LOIN AC ground |
| 45 | 1 | 100 pF (TH) | C913 | TH 5.08 mm | LOIP coupling — hand-solder (TORCH C400081) |
| 46 | 1 | 100 pF | C902 | 0402 | EXRB bypass (optional, in parallel with R901) |
| 47 | 1 | 100 nF | C906 | 0402 | Audio output AC coupling (IFOP → R1005) |
| 48 | 1 | 100 pF | C910 | 0402 | IFON AC coupling |
| 49 | 2 | 200 Ω | R902, R903 | 0402 | IFOP/IFON pull-ups to +5 V |
| 50 | 1 | 22 Ω | R904 | 0402 | LO series resistor |
| 51 | 1 | 1.82 kΩ | R901 | 0402 | EXRB bias — EXRB to GND |

---

## Optional LNA Support Components (BGA2869)

| No. | Qty | Value | Designator | Footprint | Notes |
|---|---|---|---|---|---|
| 52 | 3 | 100 nF | C401, C402, C403 | 0805 | LNA VCC and RF supply decoupling |
| 53 | 2 | 220 pF | C404, C405 | 0603 | RF input/output coupling |

---

## Audio Chain (LT6202 + Volume Control + PAM8406)

### Volume Control

| No. | Qty | Value | Designator | Footprint | Notes |
|---|---|---|---|---|---|
| 54 | 1 | 10 kΩ trimmer | R1005 | TH (Bourns 3296W) | Volume — PCB trimmer, set at bring-up (C34846) |

### LT6202 Audio Amplifier (U1001)

| No. | Qty | Value | Designator | Footprint | Notes |
|---|---|---|---|---|---|
| 55 | 2 | 5.1 kΩ | R1001, R1006 | 0402 | +IN bias divider (sets +IN to 2.5 V on single supply) |
| 56 | 1 | 200 Ω | R1003 | 0402 | Input resistor (−IN) |
| 57 | 1 | 5.1 kΩ | R1002 | 0402 | Feedback resistor (gain 25.5×) |
| 58 | 1 | 10 nF | C1002 | 0805 | Feedback LP cap (−3 dB at 3.1 kHz) |
| 59 | 1 | 51 Ω | R1004 | 0603 | Output series isolator |
| 60 | 2 | 4.7 µF | C1005, C1006 | 0603 | Input AC coupling / DC blocking |
| 61 | 2 | 100 nF | C1001, C1004 | 0603 | VCC decoupling |
| 62 | 2 | 10 nF | C1003, C1007 | 0805 | VCC HF decoupling + output HF bypass |

### PAM8406 Audio Power Amplifier (U1101)

| No. | Qty | Value | Designator | Footprint | Notes |
|---|---|---|---|---|---|
| 63 | 3 | 10 µF | C1102, C1103, C1104 | 0603 | Supply decoupling |
| 64 | 1 | 100 nF | C1106 | 0402 | VDD decoupling |
| 65 | 1 | 0 Ω | R1101 | 0805 | Gain select |
| 66 | 1 | 10 kΩ | R1102 | 0603 | MUTE#/SHDN# pull-up to +5V_P_AMP |
| 67 | 1 | WJ500V-5.08-2P | P1101 | TH screw terminal | Speaker output (KANGNEX C8465) |

> **Squelch:** Not present in current schematic (2026-04-03). If added later, see previous design notes for LM393 + BAT54S envelope detector topology.

---

## Power Supply Decoupling

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 68 | 14 | 100 µF | C103–C121 | CASE-B 3528 (tantalum) | Kyocera AVX | TAJB107K006RNJ | C16133 | Basic |
| 69 | 18 | 100 nF | C101,C102,C107–C109,C116,C119,C201,C202,C204,C401–C403,C503,C703,C903,C906,C910 | 0805 | YAGEO | CC0805KRX7R9BB104 | C49678 | Basic |
| 70 | 1 | 1 µF | C203 | 0805 | SAMSUNG | CL21B105KBFNNNE | C28323 | Basic |

> **Note:** Designators and quantities confirmed from EasyEDA BOM CSV (2026-04-03). 14× 100 µF tantalum confirmed in CSV row for bulk PSU decoupling.

---

## I²C Pull-ups and Programming Header

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 71 | 2 | 2 kΩ | R201, R202 | 0805 | UNI-ROYAL | 0805W8F2001T5E | C17604 | Basic |
| 72 | 1 | 4-pin header (I²C + power) | J201 | 2.54 mm TH, 1×4 | — | — | C2337 | Basic |

---

## Connectors

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 73 | 1 | SMA, 3 GHz | RF301 | SMA TH | 皇捷 | 2LC15SF087 | C22363778 | Extended |
| 74 | 1 | Barrel jack 5.5/2.0 mm | DC101 | DC-005-5.5-2.0 TH | BOOMELE | DC-005 2.0 | C16214 | Basic |
| 75 | 1 | Screw terminal 2-pin, 5.08 mm | P1101 | CONN-TH_2P-P5.00 | KANGNEX | WJ500V-5.08-2P | C8465 | Extended |
| 76 | 1 | 2-pin header (LNA bypass) | JP401 | 2.54 mm TH | — | — | C2337 | Basic |

---

## ESD Protection and Test Points

| No. | Qty | Value | Designator | Notes |
|---|---|---|---|---|
| 77 | 1 | ESD 140 V (PESD0402-140) | D301 | 0402, C10662 |
| 78 | 1 | TP301 | RF_IN_LNA | After RF BPF |

---

## PCB Layout Critical Notes

| Component | Critical note |
|---|---|
| U501, U701, U901 (AD8342) | LFCSP-16: EP = GND, vias to GND plane. Each supply pin (VPDC/VPLO/VPMX) needs 1 µF + 100 pF within 1 mm. |
| U401 (BGA2869) | SOT-363, no thermal pad. GND pins 2, 4, 5 to GND plane via short traces. |
| U101 tab | +3.3 V — isolated polygon. |
| U102 tab | +5 V — isolated polygon. |
| U103 tab | +5V_P_AMP — isolated polygon. |
| L601 (10.7 MHz filter) | Short direct traces. Keep away from LO2 (10.245 MHz) trace — only 455 kHz apart. |
| L901 (455 kHz filter) | Short direct traces. Low-frequency signal — less critical for trace length but avoid RF coupling. |
| LO trace routing | CLK0/CLK1/CLK2 should run away from IF signal paths. 22 Ω resistors close to MS5351M. |
| RV1101, RV1201 | Through-hole potentiometers. Place near panel edge for user and calibration access. |
| RF301 (SMA) | Hand solder after JLCPCB SMT assembly. |

---

## JLCPCB Extended Part Summary

| Designator | Part | LCSC | Notes |
|---|---|---|---|
| U102 | AMS1117-5.0 | C6187 | +5 V LDO, 800 mA |
| U103 | AMS1084CM-5.0 | C56105 | +5V_P_AMP LDO, 5 A |
| U201 | MS5351M | C1509083 | PLL |
| U401 | BGA2869,115 | C515583 | Optional LNA — **JLCPCB stock = 0, verify before order** |
| U501, U701, U901 | AD8342ACPZ-REEL7 | C182567 | ×3 mixers — confirm stock for 3 pcs |
| U1001 | LT6202IS5#TRMPBF | C580227 | Audio op-amp |
| U1101 | PAM8406DR | C86270 | Class-D amp |
| X201 | 1XTW26000MAA | C213404 | 26 MHz TCXO |
| L601 | HCCF3-10.700-F280IL03A5L | C42392418 | 10.7 MHz IF filter |
| L801 | HCCF2-455.000-LTWCDL | C42392417 | 455 kHz IF filter |
| L301 | LQW15AN47NJ00D | C192855 | RF BPF inductor |
| L302, L303 | LQW15AN18NJ00D | C82917 | RF BPF inductors |
| CT301 | STC3MA03-T1 | C22468119 | RF BPF trimmer |
| D301 | PESD0402-140 | C10662 | ESD |
| RF301 | 2LC15SF087 | C22363778 | SMA (TH) |
| R501, R701, R901 | CR0402FF1821G | C100277 | 1.82 kΩ EXRB bias, ×3 (LIZ — confirmed in CSV) |
| R1005 | 3296W-1-103LF | C34846 | 10 kΩ volume trimmer (Bourns TH) |
| C513, C713, C913 | CC4-0805-CG-100V-100PF-J | C400081 | 100 pF TH LO coupling caps — hand-solder |
