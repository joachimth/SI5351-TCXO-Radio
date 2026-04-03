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
| 2 | 1 | LDO 5.0 V / 5 A | U102 | TO-263 | AMS | AMS1084CM-5.0 | C56105 | Extended |
| 3 | 1 | LDO 5.0 V / 800 mA | U103 | SOT-223 | AMS | AMS1117-5.0 | C6187 | Basic |
| 4 | 1 | PLL clock gen. | U201 | MSOP-10 | 杭州瑞盟 | MS5351M | C1509083 | Extended |
| 5 | 1 | MMIC LNA (optional) | U401 | SOT-363 | NXP | BGA2869,115 | C515583 | Extended |
| 6 | 3 | Active mixer | U501, U701, U1001 | LFCSP-16 3×3 mm | ADI | AD8342ACPZ-REEL7 | C182567 | Extended |
| 7 | 1 | Op-amp | U1101 | SOT-23-5 | ADI | LT6202IS5#TRMPBF | C580227 | Extended |
| 8 | 1 | Dual comparator (squelch) | U1151 | SOIC-8 | TI | LM393DR | C7946 | Basic |
| 9 | 1 | Class-D stereo amp | U1201 | SOIC-16 | DIODES | PAM8406DR | C86270 | Extended |

> **Note on row 6:** AD8342ACPZ-REEL7 confirmed at JLCPCB as C182567 (LFCSP-16 3×3 mm). EP = GND — connect to GND plane with vias. Three units required (was 2 in earlier V2 draft).
>
> **Note on row 8:** LM393DR (SOIC-8, dual comparator). C7946 is a common JLCPCB Basic part — verify at time of order. Only one of the two comparator sections is used for squelch; the second may be used for a future AGC function.

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
| 17 | 1 | 455 kHz BPF (~6–8 kHz BW) | L901 | HCCF2 4-pin | HCI | HCCF2-455.000-LTWCDL | C42392417 | Extended |

> **Note on row 17:** Same 455 kHz filter as V1. Provides channel selectivity (~6–8 kHz bandwidth at 455 kHz IF). Input impedance ~1–2 kΩ; Mixer 2 IFOP pull-up of 330 Ω drives this with some mismatch loss — acceptable for prototype. Pins: 1 = input, 2 = output, 3/4 = GND.

---

## AD8342 Mixer Support Components

Three identical mixer stages. Mixer 1 (U501) and Mixer 2 (U701) use the same component values; Mixer 3 (U1001, product detector) has slightly different IFOP pull-up and audio coupling.

### Mixers 1 and 2 (U501, U701) — shared values

| No. | Qty | Value | Designator | Footprint | Notes |
|---|---|---|---|---|---|
| 18 | 4 | 100 pF | C501, C502, C503, C504 | 0402 | Mixer 1 RFIN±/LO± coupling and AC ground |
| 19 | 1 | 100 nF | C505 | 0402 | Mixer 1 VCC HF decoupling |
| 20 | 1 | 1 µF | C506 | 0402 MLCC | Mixer 1 VCC bulk bypass |
| 21 | 1 | 100 pF | C507 | 0402 | Mixer 1 EXRB bypass cap (optional, in parallel with R504) |
| 22 | 2 | 270 Ω | R501, R502 | 0402 | Mixer 1 IFOP/IFON pull-ups to +5 V |
| 23 | 1 | 22 Ω | R503 | 0402 | Mixer 1 LO series resistor |
| 24 | 1 | 1.82 kΩ | R504 | 0402 | Mixer 1 EXRB bias — EXRB to GND (see warning below) |
| 25 | 4 | 100 pF | C701, C702, C703, C704 | 0402 | Mixer 2 RFIN±/LO± |
| 26 | 1 | 100 nF | C705 | 0402 | Mixer 2 VCC HF decoupling |
| 27 | 1 | 1 µF | C706 | 0402 MLCC | Mixer 2 VCC bulk bypass |
| 28 | 1 | 100 pF | C707 | 0402 | Mixer 2 EXRB bypass cap (optional, in parallel with R704) |
| 29 | 2 | 330 Ω | R701, R702 | 0402 | Mixer 2 IFOP/IFON pull-ups (higher Z for 455 kHz filter) |
| 30 | 1 | 22 Ω | R703 | 0402 | Mixer 2 LO series resistor |
| 31 | 1 | 1.82 kΩ | R704 | 0402 | Mixer 2 EXRB bias — EXRB to GND |

### Mixer 3 — Product Detector (U1001)

| No. | Qty | Value | Designator | Footprint | Notes |
|---|---|---|---|---|---|
| 32 | 4 | 100 pF | C1001, C1002, C1003, C1004 | 0402 | RFIN±/LO± coupling and AC ground |
| 33 | 1 | 100 nF | C1005 | 0402 | VCC HF decoupling |
| 34 | 1 | 1 µF | C1006 | 0402 MLCC | VCC bulk bypass |
| 35 | 1 | 100 pF | C1007 | 0402 | EXRB bypass cap (optional, in parallel with R1004) |
| 36 | 1 | 100 nF | C1008 | 0402 | Audio output AC coupling (IFOP → volume pot) |
| 37 | 2 | 200 Ω | R1001, R1002 | 0402 | IFOP/IFON pull-ups to +5 V (audio load) |
| 38 | 1 | 22 Ω | R1003 | 0402 | LO series resistor |
| 39 | 1 | 1.82 kΩ | R1004 | 0402 | EXRB bias — EXRB to GND |

> **EXRB warning (applies to all three AD8342 mixers):** The EXRB pin is internally biased to 1.17 V. Connect EXRB → R_EXRB (1.82 kΩ) → GND. Minimum allowed value: 1.8 kΩ. **Do NOT connect VCC to EXRB** — this causes permanent device damage. A suitable 1.82 kΩ 0402 resistor is Yageo RC0402FR-071K82L (LCSC C106233) or equivalent ±1% tolerance.

---

## Optional LNA Support Components (BGA2869)

| No. | Qty | Value | Designator | Footprint | Notes |
|---|---|---|---|---|---|
| 40 | 3 | 100 nF | C401, C402, C403 | 0805 | LNA VCC and RF supply decoupling |
| 41 | 2 | 220 pF | C404, C405 | 0603 | RF input/output coupling |

---

## Audio Chain

| No. | Qty | Value | Designator | Footprint | Notes |
|---|---|---|---|---|---|
| 42 | 1 | 10 kΩ log pot, TH | RV1101 | TH 3-pin | Volume control — logarithmic taper |
| 43 | 1 | 200 Ω | R1101 | 0402 | LT6202 input resistor |
| 44 | 1 | 5.1 kΩ | R1102 | 0402 | LT6202 feedback resistor (sets gain 25.5×) |
| 45 | 1 | 10 nF | C1101 | 0402 | LT6202 feedback LP cap (−3 dB at 3.1 kHz) |
| 46 | 1 | 51 Ω | R1103 | 0603 | LT6202 output series isolator |
| 47 | 2 | 100 nF | C1102, C1103 | 0603 | LT6202 VCC and bias decoupling |
| 48 | 1 | 4.7 µF | C1104 | 0603 | AC input coupling at LT6202 (from vol. pot) |
| 49 | 1 | 100 nF | C1206 | 0402 | AUDIO_OUT coupling to PAM8406 |
| 50 | 4 | 10 µF | C1202, C1203, C1204, C1205 | 0603 | PAM8406 supply decoupling |
| 51 | 1 | 10 kΩ | R1202 | 0603 | MUTE# pull-up to +5V_P_AMP |
| 52 | 1 | 0 Ω | R1201 | 0805 | PAM8406 gain select |

---

## Squelch Circuit

| No. | Qty | Value | Designator | Footprint | Notes |
|---|---|---|---|---|---|
| 53 | 1 | BAT54S (dual Schottky) | D1201 | SOT-23 | Envelope detector — LCSC C14288 |
| 54 | 1 | 10 kΩ trimmer, TH | RV1201 | TH 3-pin | Squelch threshold — set once at bring-up |
| 55 | 1 | 10 kΩ | R1203 | 0603 | Envelope RC filter resistor |
| 56 | 1 | 10 kΩ | R1204 | 0603 | Threshold upper divider |
| 57 | 1 | 10 kΩ | R1205 | 0603 | LM393 output pull-up to +5V_P_AMP |
| 58 | 1 | 10 µF | C1207 | 0603 | Envelope RC filter capacitor (~100 ms tail) |
| 59 | 1 | 100 nF | C1208 | 0603 | LM393 VCC decoupling |

> **Note on D1201:** BAT54S is a dual Schottky in SOT-23 with common anode. LCSC C14288 — Basic part, widely stocked. One diode is used as envelope detector; the second is spare.

---

## Power Supply Decoupling

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 60 | 14 | 100 µF | C104–C120 | CASE-B 3528 (tantalum) | Kyocera AVX | TAJB107K006RNJ | C16133 | Basic |
| 61 | ~18 | 100 nF | (various supply pins) | 0805 | YAGEO | CC0805KRX7R9BB104 | C49678 | Basic |
| 62 | 1 | 1 µF | C203 | 0805 | SAMSUNG | CL21B105KBFNNNE | C28323 | Basic |

> **Note:** Exact decoupling cap count to be confirmed at schematic capture. Three AD8342 × 2 caps each = 6, plus BGA2869, LT6202, LM393, PLL, TCXO.

---

## I²C Pull-ups and Programming Header

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 63 | 2 | 2 kΩ | R201, R202 | 0805 | UNI-ROYAL | 0805W8F2001T5E | C17604 | Basic |
| 64 | 1 | 4-pin header (I²C + power) | J201 | 2.54 mm TH, 1×4 | — | — | C2337 | Basic |

---

## Connectors

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 65 | 1 | SMA, 3 GHz | RF301 | SMA TH | 皇捷 | 2LC15SF087 | C22363778 | Extended |
| 66 | 1 | Barrel jack 5.5/2.0 mm | DC101 | DC-005-5.5-2.0 TH | BOOMELE | DC-005 2.0 | C16214 | Basic |
| 67 | 1 | Screw terminal 2-pin, 5.08 mm | P1201 | CONN-TH_2P-P5.00 | KANGNEX | WJ500V-5.08-2P | C8465 | Extended |
| 68 | 1 | 2-pin header (LNA bypass) | JP401 | 2.54 mm TH | — | — | C2337 | Basic |

---

## ESD Protection and Test Points

| No. | Qty | Value | Designator | Notes |
|---|---|---|---|---|
| 69 | 1 | ESD 140 V (PESD0402-140) | D301 | 0402, C10662 |
| 70 | 1 | TP301 | RF_IN_LNA | After RF BPF |
| 71 | 1 | TP501 | IF1_IN | Mixer 1 IF output (before 10.7 MHz filter) |
| 72 | 1 | TP601 | IF1_OUT | After 10.7 MHz filter |
| 73 | 1 | TP701 | IF2_IN | Mixer 2 IF output (before 455 kHz filter) |
| 74 | 1 | TP901 | IF2_OUT | After 455 kHz filter |
| 75 | 1 | TP1001 | AUDIO_RAW | Product detector output |

---

## PCB Layout Critical Notes

| Component | Critical note |
|---|---|
| U501, U701, U1001 (AD8342) | LFCSP-16: EP = GND, vias to GND plane. 100 nF + 1 µF decoupling within 1 mm of VCC pins. |
| U401 (BGA2869) | SOT-363, no thermal pad. GND pins 2, 4, 5 to GND plane via short traces. |
| U101, U102, U103 | Tab pad = Vout (NOT GND). Isolated polygons required. |
| L601 (10.7 MHz filter) | Short direct traces. Keep away from LO2 (10.245 MHz) trace — only 455 kHz apart. |
| L901 (455 kHz filter) | Short direct traces. Low-frequency signal — less critical for trace length but avoid RF coupling. |
| LO trace routing | CLK0/CLK1/CLK2 should run away from IF signal paths. 22 Ω resistors close to MS5351M. |
| RV1101, RV1201 | Through-hole potentiometers. Place near panel edge for user and calibration access. |
| RF301 (SMA) | Hand solder after JLCPCB SMT assembly. |

---

## JLCPCB Extended Part Summary

| Designator | Part | LCSC | Notes |
|---|---|---|---|
| U102 | AMS1084CM-5.0 | C56105 | 5 A LDO |
| U201 | MS5351M | C1509083 | PLL |
| U401 | BGA2869,115 | C515583 | Optional LNA — verify stock |
| U501, U701, U1001 | AD8342ACPZ-REEL7 | C182567 | ×3 units — confirm stock for 3 pcs |
| U1101 | LT6202IS5#TRMPBF | C580227 | Audio op-amp |
| U1201 | PAM8406DR | C86270 | Class-D amp |
| X201 | 1XTW26000MAA | C213404 | 26 MHz TCXO |
| L601 | HCCF3-10.700-F280IL03A5L | C42392418 | 10.7 MHz IF filter |
| L901 | HCCF2-455.000-LTWCDL | C42392417 | 455 kHz IF filter |
| L301 | LQW15AN47NJ00D | C192855 | RF BPF inductor |
| L302, L303 | LQW15AN18NJ00D | C82917 | RF BPF inductors |
| CT301 | STC3MA03-T1 | C22468119 | RF BPF trimmer |
| D301 | PESD0402-140 | C10662 | ESD |
| RF301 | 2LC15SF087 | C22363778 | SMA (TH) |
| R504, R704, R1004 | RC0402FR-071K82L | C106233 | 1.82 kΩ EXRB bias, ×3 (one per AD8342) |
