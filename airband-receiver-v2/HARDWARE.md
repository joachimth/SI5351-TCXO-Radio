# Hardware Description — AM Airband Receiver V2

Stage-by-stage circuit description for the double-conversion 118–137 MHz AM airband receiver. Three AD8342 mixers, no baluns, 10.7 MHz first IF for image rejection, 455 kHz second IF for channel selectivity.

---

## Page 1 — Power Supply

**Identical to V1.** Three regulated rails from a single DC input (VCC, 6–15 V):

| Rail | Regulator | Package | Max current | Load in V2 |
|---|---|---|---|---|
| +3.3 V | AMS1117-3.3 (U101) | SOT-223 | 800 mA | MS5351M, TCXO |
| +5 V | AMS1117-5.0 (U103) | SOT-223 | 800 mA | AD8342 ×3, BGA2869 (optional), LT6202 |
| +5V_P_AMP | AMS1084CM-5.0 (U102) | TO-263 | 5 A | PAM8406 only |

DC input via DC101 (DC-005 2.0 barrel jack, 5.5/2.0 mm). Bulk decoupling: 100 µF tantalum capacitors (CASE-B) on each rail.

**Tab pad warning (identical to V1):** All three regulators have their exposed tab/pad connected to Vout — NOT GND. Each requires an isolated copper polygon on its output net.

---

## Page 2 — Clock Generator

**Same hardware as V1.** MS5351M (U201) PLL locked to KDS 1XTW26000MAA 26 MHz TCXO (X201).

All three CLK outputs are now used:

| Output | Frequency | Purpose |
|---|---|---|
| CLK0 | RF − 10.7 MHz = 107.3–126.3 MHz | LO1 — RF mixer (low-side injection) |
| CLK1 | 10.7 MHz − 455 kHz = 10.245 MHz | LO2 — second IF mixer |
| CLK2 | 455 kHz (fixed) | LO3 — product detector |

**Low-side injection (CLK0 = RF − IF):** The image frequency falls at RF − 2×IF = 96.6–115.6 MHz, well below the airband and in the FM broadcast band or below. The RF BPF provides 20–35 dB rejection at these frequencies. This is significantly better than high-side injection, which places images within 2–12 MHz of the airband top.

Each active CLK output is AC-coupled to its load via a 100 pF series capacitor and a 22 Ω series resistor to reduce ringing.

I²C pull-ups: R201, R202 = 2 kΩ to +3.3 V. Programming header J201 (4-pin: GND/+3.3V/SDA/SCL) provides microcontroller access.

---

## Page 3 — RF Bandpass Filter (118–137 MHz)

### Purpose

Reject out-of-band signals before the mixer. With low-side injection (CLK0 = RF − IF), the image falls at:

```
f_image = RF − 2 × IF
```

| RF channel | LO1 (CLK0) | Image |
|---|---|---|
| 118.0 MHz | 107.3 MHz | 96.6 MHz (below FM band) |
| 127.5 MHz | 116.8 MHz | 106.1 MHz (FM broadcast band) |
| 137.0 MHz | 126.3 MHz | 115.6 MHz (2.4 MHz below airband edge) |

The worst case is 137 MHz reception, where the image at 115.6 MHz is 2.4 MHz below the filter lower passband edge (~6–10 dB rejection). All other channels have images in or below the FM band with 20–35 dB rejection from the 3-pole RF BPF.

### Topology — Reused from V1

The V2 RF preselector uses the **identical topology and component values** as the V1 design ("Pi with series resonance"). L302//C301 resonates at:

```
f = 1 / (2π × √(18 nH × 100 pF)) = 118.7 MHz
```

This places the parallel-resonant tank at the lower airband edge, giving shaped bandpass coverage from 118 to ~137 MHz. CT301 is adjusted after assembly to peak the filter at the desired channel centre.

```
                  L302 (18 nH)        L303 (18 nH)
RF_ANT ──[D301]──────────────[C302]──────────────── RF_IN_LNA
                    │         3.3 pF         │
                   [C301]   [CT301]         [C303]
                   100 pF   1.5–3 pF       100 pF
                   [L301]                    │
                   47 nH                    GND
                    │
                   GND
```

| Ref | Value | LCSC | Notes |
|---|---|---|---|
| RF301 | SMA 3 GHz | C22363778 | Through-hole |
| D301 | ESD 140 V (PESD0402-140) | C10662 | On RF_ANT net |
| L301 | 47 nH (LQW15AN47NJ00D) | C192855 | Series inductor (shunt arm) |
| L302, L303 | 18 nH (LQW15AN18NJ00D) | C82917 | Resonates with C301/C303 at 118.7 MHz |
| C301, C303 | 100 pF (0402CG101J500NT) | C1546 | Shunt capacitors |
| C302 | 3.3 pF (0402CG3R3C500NT) | C1565 | Series coupling cap |
| CT301 | 1.5–3 pF (STC3MA03-T1) | C22468119 | Centre frequency trimmer |
| TP301 | test point | — | RF_IN_LNA |

**Net names:**
- `RF_ANT` — SMA → D301 → filter input
- `RF_IN_LNA` — filter output → JP401 (LNA bypass) → LNA or Mixer 1

---

## Page 4 — Optional LNA (BGA2869)

Improves system NF from 22 dB to 5 dB. Bypassable via JP401 for strong-signal environments.

| Ref | Value | Notes |
|---|---|---|
| U401 | BGA2869 | SOT-363, internally 50 Ω matched |
| C401, C402 | 100 nF | VCC decoupling, 0805 |
| C403 | 100 nF | RF supply decoupling, 0805 |
| C404, C405 | 220 pF | RF input/output coupling |
| JP401 | 2-pin header | Open = LNA in circuit; short = bypass |

GND pins 2, 4, 5 of BGA2869 to GND plane with short traces and vias. No external matching components. See ANALYSIS.md for sensitivity comparison.

---

## Page 5 — RF Mixer (AD8342 Mixer 1, U501)

Down-converts 118–137 MHz RF to 10.7 MHz IF using CLK0 (107.3–126.3 MHz, low-side injection).

### AD8342 at 5 V — Key Specs

| Parameter | Value |
|---|---|
| Conversion gain (differential) | +3.5 dB typical |
| SSB NF (differential) | ~10 dB |
| IIP3 | +24 dBm (5 V supply) |
| LO drive | −10 to +3 dBm (compatible with SI5351 direct drive) |
| Supply current | ~55 mA at 5 V |
| Package | LFCSP-16 3×3 mm (AD8342ACPZ-REEL7, LCSC C182567) |
| Exposed pad (EP) | GND — connect to GND plane with vias |

### Single-Ended Connections

| Port | Connection |
|---|---|
| RFIN+ | Signal via 100 pF AC coupling cap |
| RFIN− | GND via 100 pF AC coupling cap |
| LOIP | CLK0 via 100 pF coupling + 22 Ω series resistor |
| LOIN | GND via 100 pF coupling cap |
| IFOP | +5 V via R501 (270 Ω) — IF output taken here |
| IFON | +5 V via R502 (270 Ω) — terminated, not used |
| EXRB | R504 (1.82 kΩ) → GND — sets mixer core bias current |

The 270 Ω pull-up at IFOP provides a Thevenin source impedance matching the 10.7 MHz ceramic filter input (~270–330 Ω).

### EXRB Bias Resistor — Critical Note

The AD8342 EXRB pin is internally biased to 1.17 V. An external resistor (R504) from EXRB to GND sets the mixer core bias current:

```
I_bias = V_EXRB / R_EXRB = 1.17 V / 1820 Ω = 0.643 mA
```

**Connection: EXRB → R504 (1.82 kΩ) → GND.**

> **WARNING:** Do NOT connect EXRB to VCC (even through a resistor). The AD8342 datasheet states: *"Permanent damage to the device can result if values below 1.8 kΩ are used."* Connecting a supply voltage to EXRB drives excessive current into the pin and irreversibly damages the device.

An optional 100 pF bypass capacitor (C507) in parallel with R504 reduces bias node noise, but is not required.

| Ref | Value | Notes |
|---|---|---|
| C501 | 100 pF | RFIN+ coupling |
| C502 | 100 pF | RFIN− AC ground |
| C503 | 100 pF | LOIP coupling |
| C504 | 100 pF | LOIN AC ground |
| C505 | 1 µF | VCC bulk bypass, 0402 |
| C506 | 100 nF | VCC HF decoupling, 0402 |
| C507 | 100 pF | EXRB bypass (optional, in parallel with R504) |
| R501 | 270 Ω | IFOP pull-up to +5 V |
| R502 | 270 Ω | IFON pull-up to +5 V (termination) |
| R503 | 22 Ω | LO series resistor |
| R504 | 1.82 kΩ | EXRB bias — EXRB to GND |

**Net names:** `RF_OUT_LNA` → RFIN+; `LO1` → LOIP; `IF1_IN` → IFOP → 10.7 MHz filter

---

## Page 6 — 10.7 MHz IF Filter (L601)

| Ref | Part | LCSC | Notes |
|---|---|---|---|
| L601 | HCCF3-10.700-F280IL03A5L | C42392418 | 10.7 MHz ceramic BPF, ~280 kHz BW, 4-pin SMD |

**Pin assignments:** Pin 1 = input (IF1_IN), Pin 2 = GND, Pin 3 = float, Pin 4 = output (IF1_OUT).

At this stage the filter provides **image rejection** (attenuates LO leakage and the 10.7 MHz image), not channel selectivity — channel selectivity is handled by the narrower 455 kHz filter downstream.

**Insertion loss:** typically 3–5 dB. Measure on assembled board.

**Net names:** `IF1_IN` → filter pin 1; `IF1_OUT` → filter pin 4 → Mixer 2 RFIN+

---

## Page 7 — Second IF Mixer (AD8342 Mixer 2, U701)

Converts 10.7 MHz first IF to 455 kHz second IF using CLK1 (10.245 MHz).

Circuit topology is **identical to Mixer 1** (Page 5) with these differences:
- RFIN+ receives 10.7 MHz IF signal (from L601 output) via 100 pF coupling
- LOIP receives CLK1 (10.245 MHz) via 100 pF + 22 Ω
- IFOP pull-up resistor: R701 = 330 Ω (higher impedance to better match 455 kHz filter input ~1–2 kΩ)
- IFON pull-up: R702 = 330 Ω (termination)
- EXRB: R704 (1.82 kΩ) → GND (same requirement as Mixer 1; see EXRB note on Page 5)

| Ref | Value | Notes |
|---|---|---|
| C701 | 100 pF | RFIN+ coupling |
| C702 | 100 pF | RFIN− AC ground |
| C703 | 100 pF | LOIP coupling |
| C704 | 100 pF | LOIN AC ground |
| C705 | 1 µF | VCC bulk bypass, 0402 |
| C706 | 100 nF | VCC HF decoupling, 0402 |
| C707 | 100 pF | EXRB bypass (optional, in parallel with R704) |
| R701 | 330 Ω | IFOP pull-up to +5 V |
| R702 | 330 Ω | IFON pull-up to +5 V (termination) |
| R703 | 22 Ω | LO series resistor |
| R704 | 1.82 kΩ | EXRB bias — EXRB to GND |

**Net names:** `IF1_OUT` → RFIN+; `LO2` → LOIP; `IF2_IN` → IFOP → 455 kHz filter

---

## Page 8 — 455 kHz IF Filter (L901)

| Ref | Part | LCSC | Notes |
|---|---|---|---|
| L901 | HCCF2-455.000-LTWCDL | C42392417 | 455 kHz ceramic BPF, ~6–8 kHz BW, 4-pin SMD |

**Pin assignments:** Pin 1 = input (IF2_IN), Pin 2 = output (IF2_OUT), Pin 3/4 = GND.

This filter provides the **channel selectivity**: 6–8 kHz bandwidth passes AM voice (≤4 kHz audio baseband) while rejecting adjacent channels at ±25 kHz spacing by >30 dB.

**Insertion loss:** typically 3–6 dB for HCCF2 series. Measure on assembled board.

**Impedance:** HCCF2 input/output impedance is typically 1–2 kΩ. Mixer 2 IFOP pull-up (R701 = 330 Ω) drives the filter; the mismatch causes some loss but is acceptable for a prototype. An LC matching network between R701 and L901 can improve insertion loss in a future revision.

**Net names:** `IF2_IN` → filter pin 1; `IF2_OUT` → filter pin 2 → Mixer 3 RFIN+

---

## Page 9 — Product Detector (AD8342 Mixer 3, U1001)

Synchronous AM demodulation at 455 kHz. The 455 kHz IF (amplitude modulated) is multiplied by CLK2 (455 kHz fixed), producing baseband audio.

Circuit topology is **identical to Mixer 1** with these differences:
- RFIN+ receives 455 kHz IF signal (from L901 output)
- LOIP receives CLK2 (455 kHz) via 100 pF + 22 Ω
- IFOP pull-up: R1001 = 200 Ω to +5 V (audio output load)
- IFON pull-up: R1002 = 200 Ω to +5 V (termination)
- Output (IFOP) is AC-coupled to the volume control via C1008 (100 nF)
- EXRB: R1004 (1.82 kΩ) → GND (same requirement as Mixer 1; see EXRB note on Page 5)

**Product detection note:** CLK2 is a free-running 455 kHz tone from the SI5351. The phase difference between CLK2 and the received carrier is arbitrary but stable (both referenced to the same 26 MHz TCXO). Any phase offset scales the audio amplitude by cos(Δφ) — voice AM is intelligible at any phase.

| Ref | Value | Notes |
|---|---|---|
| C1001 | 100 pF | RFIN+ coupling |
| C1002 | 100 pF | RFIN− AC ground |
| C1003 | 100 pF | LOIP coupling |
| C1004 | 100 pF | LOIN AC ground |
| C1005 | 1 µF | VCC bulk bypass, 0402 |
| C1006 | 100 nF | VCC HF decoupling, 0402 |
| C1007 | 100 pF | EXRB bypass (optional, in parallel with R1004) |
| C1008 | 100 nF | Audio output AC coupling (IFOP → volume pot) |
| R1001 | 200 Ω | IFOP pull-up to +5 V |
| R1002 | 200 Ω | IFON pull-up to +5 V (termination) |
| R1003 | 22 Ω | LO series resistor |
| R1004 | 1.82 kΩ | EXRB bias — EXRB to GND |

**Net names:** `IF2_OUT` → RFIN+; `LO3` → LOIP; `AUDIO_RAW` → C1008 → volume pot wiper

---

## Page 10 — Volume Control

A single 10 kΩ logarithmic potentiometer (RV1101) acts as a voltage divider on the audio line between the product detector output and the LT6202 input.

```
AUDIO_RAW ──[C1008 100nF]── RV1101 pin 1 (top)
                             RV1101 wiper ── AUDIO_VOL ── R1101 ── LT6202 input
                             RV1101 pin 3 (bottom) ── GND
```

| Ref | Value | Notes |
|---|---|---|
| RV1101 | 10 kΩ log, TH, 3-pin | Logarithmic taper for natural-feeling volume control |

**Important:** Use a logarithmic (audio) taper pot, not linear. Most standard TH pots from LCSC are available in both; verify taper at time of order.

---

## Page 11 — Audio Filter and Gain (LT6202, U1101)

Inverting amplifier with low-pass feedback. Single-ended input from the volume control wiper.

| Component | Value | Function |
|---|---|---|
| R1101 | 200 Ω | Input resistor |
| R1102 | 5.1 kΩ | Feedback resistor |
| C1101 | 10 nF | Feedback LP cap — sets −3 dB at ~3.1 kHz |
| R1103 | 51 Ω | Output series isolator (cap load protection) |
| C1102 | 100 nF | VCC decoupling |
| C1103 | 100 nF | V+ biasing decoupling |
| C1104 | 4.7 µF | Input AC coupling (if DC blocking needed from pot wiper) |

**Gain:**
```
Av = −R1102 / R1101 = −5.1 kΩ / 200 Ω = −25.5 (≈ 28 dB, inverting)
```

**Low-pass −3 dB:**
```
f_LP = 1 / (2π × R1102 × C1101) ≈ 1 / (2π × 5.1k × 10n) ≈ 3.1 kHz
```

This matches the V1 op-amp gain configuration exactly, giving consistent audio output levels.

**Net names:** `AUDIO_VOL` → R1101 → LT6202 input; `AUDIO_OUT` → R1103 → C1206 → PAM8406

---

## Page 12 — Audio Power Amplifier + Squelch (PAM8406, U1201)

**PAM8406** is unchanged from V1. The squelch circuit is new — it drives the PAM8406 SHDN# pin (already present in the design) to mute the amplifier when no signal is present.

### Squelch Circuit

A simple two-stage circuit: (1) envelope detector on the LT6202 output, (2) comparator against an adjustable threshold.

```
AUDIO_OUT ──[D1201 BAT54S]──[R1203 10kΩ]──┬── LM393 IN+ (pin 3)
                                            │
                                          [C1207 10µF]
                                            │
                                           GND

+5V ──[R1204 10kΩ]──┬── LM393 IN− (pin 2)
                     │
                   [RV1201 10kΩ pot wiper]
                     │
                    GND

LM393 OUT (pin 1) ──[R1205 10kΩ pull-up to +5V_P_AMP]── PAM8406 SHDN#
```

When audio is present: envelope detector output exceeds threshold → LM393 output goes high → PAM8406 active.
When no signal: envelope falls below threshold → LM393 output goes low → PAM8406 muted.

The RC time constant (R1203 × C1207 = 10kΩ × 10µF = 100 ms) sets the squelch tail — the amplifier stays active for ~100 ms after a transmission ends to avoid clipping the last syllable.

| Ref | Value | Notes |
|---|---|---|
| U1151 | LM393DR (SOIC-8) | Dual comparator; one section used for squelch |
| D1201 | BAT54S (SOT-23) | Dual Schottky envelope detector |
| RV1201 | 10 kΩ trimmer (TH) | Squelch threshold — adjust once at setup |
| R1203 | 10 kΩ | Envelope filter resistor |
| R1204 | 10 kΩ | Threshold upper divider |
| R1205 | 10 kΩ | LM393 output pull-up to +5V_P_AMP |
| C1207 | 10 µF | Envelope filter capacitor, 0603 MLCC |
| C1208 | 100 nF | LM393 VCC decoupling |

**PAM8406 components (unchanged from earlier V2 description):**

| Ref | Value | Notes |
|---|---|---|
| U1201 | PAM8406DR | Class-D stereo amp, SOIC-16 |
| C1206 | 100 nF | AUDIO_OUT AC coupling |
| C1202–C1204 | 10 µF | Supply decoupling |
| C1205 | 1 nF | VDD decoupling |
| R1201 | 0 Ω | Gain select |
| R1202 | 10 kΩ | MUTE# pull-up to +5V_P_AMP (MUTE# controlled by squelch) |
| P1201 | WJ500V-5.08-2P | Speaker screw terminal |

> **Note:** R1202 (MUTE# pull-up) and the LM393 output share the SHDN# net. The LM393 output is open-collector; R1205 pulls it high to +5V_P_AMP. When the LM393 output is low, it overrides R1202, muting the amplifier.

---

## Signal Chain Summary

| Stage | IC | Supply | Net in | Net out |
|---|---|---|---|---|
| RF BPF | passive | — | RF_ANT | RF_IN_LNA |
| LNA (optional) | BGA2869 U401 | +5 V | RF_IN_LNA | RF_OUT_LNA |
| RF Mixer | AD8342 U501 | +5 V | RF_OUT_LNA + LO1 | IF1_IN |
| 10.7 MHz filter | L601 | — | IF1_IN | IF1_OUT |
| 2nd IF Mixer | AD8342 U701 | +5 V | IF1_OUT + LO2 | IF2_IN |
| 455 kHz filter | L901 | — | IF2_IN | IF2_OUT |
| Product Detector | AD8342 U1001 | +5 V | IF2_OUT + LO3 | AUDIO_RAW |
| Volume control | RV1101 | — | AUDIO_RAW | AUDIO_VOL |
| Audio gain/LPF | LT6202 U1101 | +5 V | AUDIO_VOL | AUDIO_OUT |
| Squelch | LM393 U1151 | +5 V | AUDIO_OUT (detect) | PAM8406 SHDN# |
| Audio power amp | PAM8406 U1201 | +5V_P_AMP | AUDIO_OUT | Speaker |

---

## PCB Layout Notes

| Component | Critical note |
|---|---|
| U501, U701, U1001 (AD8342) | LFCSP-16: EP = GND, connect to GND plane with vias. Decouple VCC within 1 mm (100 nF + 1 µF). |
| L601, L901 (IF filters) | Short, direct traces. Keep L901 (455 kHz) away from RF input traces. No 90° bends. |
| U401 (BGA2869) | GND pins 2, 4, 5 to GND plane. No thermal pad. |
| U101, U102, U103 | Tab pad = Vout — isolated polygons. Identical to V1. |
| LO traces (CLK0–CLK2) | Short traces from MS5351M. 22 Ω series resistors close to PLL outputs. Keep LO2 (10.245 MHz) away from IF1 (10.7 MHz) traces — 455 kHz apart, can cause spurious mixing. |
| RV1101 (volume pot) | Through-hole; place near panel edge for access. |
| RV1201 (squelch trimmer) | Through-hole trimmer; accessible for one-time calibration. |
