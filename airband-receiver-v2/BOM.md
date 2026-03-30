# Bill of Materials — AM Airband Receiver V2

All components sourced from LCSC unless otherwise noted. Components shared with V1 reuse the same LCSC part numbers.

> **Status: Pre-schematic draft.** Component values and quantities are based on the circuit description in HARDWARE.md. Exact quantities and some resistor/capacitor values will be finalised during schematic capture. Designators follow the same page-based convention as V1.
>
> **New in V2:** AD8342 active mixer (U501, U701). All other ICs are inherited from V1.

---

## ICs

| No. | Qty | Value | Designator | Package | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 1 | 1 | LDO 3.3 V / 800 mA | U101 | SOT-223 | AMS | AMS1117-3.3 | C6186 | Basic |
| 2 | 1 | LDO 5.0 V / 5 A | U102 | TO-263 | AMS | AMS1084CM-5.0 | C56105 | Extended |
| 3 | 1 | LDO 5.0 V / 800 mA | U103 | SOT-223 | AMS | AMS1117-5.0 | C6187 | Basic |
| 4 | 1 | PLL clock gen. | U201 | MSOP-10 | 杭州瑞盟 | MS5351M | C1509083 | Extended |
| 5 | 1 | MMIC LNA (optional) | U401 | SOT-363 | NXP | BGA2869,115 | C515583 | Extended |
| 6 | 2 | Active mixer | U501, U701 | LFCSP-16 3×3 mm | ADI | AD8342ACPZ-REEL7 | C182567 | Extended |
| 7 | 1 | Op-amp | U1101 | SOT-23-5 | ADI | LT6202IS5#TRMPBF | C580227 | Extended |
| 8 | 1 | Class-D stereo amp | U1201 | SOIC-16 | DIODES | PAM8406DR | C86270 | Extended |

> **Note on row 6:** AD8342ACPZ-REEL7 confirmed at JLCPCB as C182567 (LFCSP-16 3×3 mm). Verify stock before ordering. The exposed pad (EP) on LFCSP is GND — connect to GND plane with vias.
>
> **Note on row 5:** BGA2869 is optional. Install U401 only if additional sensitivity is needed. Verify stock (C515583) — low stock reported in V1 BOM notes.

---

## Crystal / Oscillator

| No. | Qty | Value | Designator | Package | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 9 | 1 | 26 MHz TCXO | X201 | SMD 4-pin (3.2×2.5 mm) | KDS | 1XTW26000MAA | C213404 | Extended |

---

## RF Bandpass Filter (118–137 MHz)

Same topology and component values as V1 (L302//C301 parallel resonant tank resonates at 118.7 MHz = lower airband edge). All LCSC numbers are identical to V1.

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 10 | 1 | 47 nH | L301 | 0402 | muRata | LQW15AN47NJ00D | C192855 | Extended |
| 11 | 2 | 18 nH | L302, L303 | 0402 | muRata | LQW15AN18NJ00D | C82917 | Extended |
| 12 | 2 | 100 pF | C301, C303 | 0402 | FH | 0402CG101J500NT | C1546 | Basic |
| 13 | 1 | 3.3 pF | C302 | 0402 | FH | 0402CG3R3C500NT | C1565 | Basic |
| 14 | 1 | 1.5–3 pF | CT301 | 4.5×3.2 mm | SEHWA | STC3MA03-T1 | C22468119 | Extended |

> **Note on rows 10–14:** Directly ported from V1 BOM. L302//C301 and L303//C303 form parallel resonant tanks at 118.7 MHz (lower airband edge). CT301 is adjusted after PCB assembly to peak the filter at the desired channel centre (typically 127–128 MHz midband). See HARDWARE.md Page 3 for topology diagram and image frequency analysis.

---

## IF Filter

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 15 | 1 | 10.7 MHz BP filter | L601 | HCCF3 4-pin | HCI | HCCF3-10.700-F280IL03A5L | C42392418 | Extended |

> **Note:** Same part as V1. ~280 kHz bandwidth, ~270–330 Ω nominal impedance.

---

## AD8342 Mixer Support Components

Two identical sets — one per AD8342. Component references follow U501 (Mixer 1) and U701 (Mixer 2) page numbering.

| No. | Qty | Value | Designator | Footprint | Notes |
|---|---|---|---|---|---|
| 15 | 4 | 100 pF | C501, C502, C503, C504 | 0402 | Mixer 1 RF+/RF− coupling, LO+/LO− coupling |
| 16 | 2 | 100 nF | C505, C506 | 0402 | Mixer 1 VCC decoupling |
| 17 | 1 | 1 µF | C507 | 0402 | Mixer 1 VCC bulk bypass |
| 18 | 4 | 100 pF | C701, C702, C703, C704 | 0402 | Mixer 2 (same as Mixer 1) |
| 19 | 2 | 100 nF | C705, C706 | 0402 | Mixer 2 VCC decoupling |
| 20 | 1 | 1 µF | C707 | 0402 | Mixer 2 VCC bulk bypass (or AC coupling to audio — verify in schematic) |
| 21 | 2 | 270 Ω | R501, R502 | 0402 | Mixer 1 IFOP/IFON pull-ups to +5 V |
| 22 | 1 | 22 Ω | R503 | 0402 | Mixer 1 LO series resistor |
| 23 | 2 | 200 Ω | R701, R702 | 0402 | Mixer 2 IFOP/IFON pull-ups to +5 V |
| 24 | 1 | 22 Ω | R703 | 0402 | Mixer 2 LO series resistor |

---

## Optional LNA Support Components (BGA2869)

Install only if U401 is populated.

| No. | Qty | Value | Designator | Footprint | Notes |
|---|---|---|---|---|---|
| 25 | 3 | 100 nF | C401, C402, C403 | 0805 | VCC + RF supply decoupling |
| 26 | 2 | 220 pF | C404, C405 | 0603 | RF input/output coupling |

---

## Power Supply Decoupling

Shared with V1. Bulk 100 µF tantalum on all three rails.

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 27 | 14 | 100 µF | C104–C120 (shared with V1 scheme) | CASE-B 3528 (tantalum) | Kyocera AVX | TAJB107K006RNJ | C16133 | Basic |
| 28 | 16 | 100 nF | (various supply pins) | 0805 | YAGEO | CC0805KRX7R9BB104 | C49678 | Basic |
| 29 | 1 | 1 µF | C203 | 0805 | SAMSUNG | CL21B105KBFNNNE | C28323 | Basic |

> **Note:** Exact decoupling capacitor count and designators will be confirmed during schematic capture. Quantities above are estimates based on supply pin counts.

---

## Audio Chain

| No. | Qty | Value | Designator | Footprint | Notes |
|---|---|---|---|---|---|
| 30 | 1 | 100 nF | C707 (or separate ref) | 0402 | Audio input AC coupling (Mixer 2 IFOP → LT6202) |
| 31 | 2 | 5.1 kΩ | R1101, R1102 | 0402 | Op-amp input + feedback resistors |
| 32 | 1 | 10 nF | C1102 | 0805 | Feedback LP cap (sets −3 dB ~3.1 kHz) |
| 33 | 1 | 51 Ω | R1103 | 0603 | Op-amp output series isolator |
| 34 | 2 | 100 nF | C1101, C1103 | 0603 | LT6202 VCC and bias decoupling |
| 35 | 1 | 100 nF | C1206 | 0402 | AUDIO_OUT AC coupling to PAM8406 |
| 36 | 4 | 10 µF | C1202–C1204, C1002 | 0603 | PAM8406 VCC decoupling |
| 37 | 1 | 10 kΩ | R1202 | 0603 | MUTE#/SHDN# pull-up to +5V_P_AMP |
| 38 | 1 | 0 Ω | R1201 | 0805 | PAM8406 gain select |

---

## I²C Pull-ups (Clock Generator)

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 39 | 2 | 2 kΩ | R201, R202 | 0805 | UNI-ROYAL | 0805W8F2001T5E | C17604 | Basic |

---

## Connectors

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 40 | 1 | SMA, 3 GHz | RF301 | SMA TH | 皇捷 | 2LC15SF087 | C22363778 | Extended |
| 41 | 1 | Barrel jack 5.5/2.0 mm | DC101 | DC-005-5.5-2.0 TH | BOOMELE | DC-005 2.0 | C16214 | Basic |
| 42 | 1 | Screw terminal 2-pin, 5.08 mm | P1201 | CONN-TH_2P-P5.00 | KANGNEX | WJ500V-5.08-2P | C8465 | Extended |
| 43 | 1 | 2-pin header (LNA bypass) | JP401 | 2.54 mm TH | standard | — | C2337 | Basic |
| 44 | 1 | 4-pin header (I²C + power) | J201 | 2.54 mm TH, 1×4 | standard | — | C2337 | Basic |

> **Note on J201:** I²C programming header for the MS5351M (SDA, SCL, +3.3 V, GND) — required to configure LO frequencies from a microcontroller or PC. Pin order: 1=GND, 2=+3.3V, 3=SDA, 4=SCL.

---

## ESD Protection

| No. | Qty | Value | Designator | Footprint | Manufacturer | Part Number | LCSC | JLCPCB |
|---|---|---|---|---|---|---|---|---|
| 45 | 1 | ESD 140 V | D301 | 0402 | Littelfuse | PESD0402-140 | C10662 | Extended |

---

## Test Points

| No. | Qty | Designator | Notes |
|---|---|---|---|
| 45 | 1 | TP301 | RF filter output / LNA input (RF_IN_LNA) |
| 46 | 1 | TP501 | Mixer 1 IF output (IF_IN, before filter) |
| 47 | 1 | TP601 | IF filter output (IF_OUT) |
| 48 | 1 | TP701 | Mixer 2 audio output (AUDIO_RAW) |

> Test points are strongly recommended on all inter-stage nodes to allow scope/spectrum analyser verification at each stage during bring-up.

---

## PCB Layout Critical Notes

| Component | Critical note |
|---|---|
| U501, U701 (AD8342) | LFCSP-16: exposed pad (EP) = GND — connect to GND plane with vias. Decouple VCC pins with 100 nF + 1 µF placed within 1 mm. |
| U401 (BGA2869) | SOT-363, no exposed thermal pad. GND pins 2, 4, 5 to GND plane with short traces and vias. |
| U101, U102, U103 | Tab pad = Vout (NOT GND) — isolated copper polygons required on respective Vout nets. Identical to V1. |
| L601 (IF filter) | Trace lengths before/after filter must be minimised. Avoid 90° bends. Route IF signal away from RF input and LO traces. |
| RF301 (SMA) | Through-hole — hand solder after JLCPCB SMT assembly. |
| JP401 (LNA bypass) | 2-pin 2.54 mm header; short = bypass LNA, open = LNA active. Orient for easy jumper access. |

---

## JLCPCB Extended Part Summary

Parts with an asterisk (*) have no known LCSC number yet — verify before ordering.

| Designator | Part | LCSC | Notes |
|---|---|---|---|
| U102 | AMS1084CM-5.0 | C56105 | 5 A LDO — confirmed stock |
| U201 | MS5351M | C1509083 | SI5351-compatible PLL |
| U401 | BGA2869,115 | C515583 | Optional LNA — verify stock |
| U501, U701 | AD8342ACPZ-REEL7 | C182567 | Active mixer, LFCSP-16 — confirmed JLCPCB |
| U1101 | LT6202IS5#TRMPBF | C580227 | Audio op-amp |
| U1201 | PAM8406DR | C86270 | Class-D amp |
| X201 | 1XTW26000MAA | C213404 | 26 MHz TCXO |
| L601 | HCCF3-10.700-F280IL03A5L | C42392418 | 10.7 MHz IF filter |
| D301 | PESD0402-140 | C10662 | ESD protection |
| RF301 | 2LC15SF087 | C22363778 | SMA connector (TH) |
| L301 | LQW15AN47NJ00D | C192855 | RF BPF series inductor (shunt arm) |
| L302, L303 | LQW15AN18NJ00D | C82917 | RF BPF series inductors — resonates with C301/C303 at 118.7 MHz |
| CT301 | STC3MA03-T1 | C22468119 | RF BPF centre-freq trimmer |
