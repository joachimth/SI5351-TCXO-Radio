# Hardware Description — AM Airband Receiver V2

Stage-by-stage circuit description for the double-conversion 118–137 MHz AM airband receiver. Three AD8342 mixers, no baluns, 10.7 MHz first IF for image rejection, 455 kHz second IF for channel selectivity.

---

## Page 1 — Power Supply

**Identical to V1.** Three regulated rails from a single DC input (VCC, 6–15 V):

| Rail | Regulator | Package | Max current | Load in V2 |
|---|---|---|---|---|
| +3.3 V | AMS1117-3.3 (U101) | SOT-223 | 800 mA | MS5351M, TCXO |
| +5 V | AMS1117-5.0 (U102) | SOT-223 | 800 mA | AD8342 ×3, BGA2869 (optional), LT6202 |
| +5V_P_AMP | AMS1084CM-5.0 (U103) | TO-263 | 5 A | PAM8406 only |

> **Note:** In V2, U102 = AMS1117-5.0 (+5V) and U103 = AMS1084CM-5.0 (+5V_P_AMP). This is the reverse of the V1 designator assignment. The tab-pad net assignments remain the same: each regulator tab = its Vout rail — isolated polygons required.

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
- RFIN+ receives 10.7 MHz IF signal (from L601 output, net `IF_OUT_BP_FILTER`) via 100 pF coupling
- LOIP receives CLK1 (10.245 MHz) via 100 pF TH + 22 Ω
- IFOP pull-up: R702 = 330 Ω to +5 V (see warning below)
- IFON pull-up: R703 = 330 Ω to +5 V (termination)
- EXRB: R701 (1.82 kΩ) → GND; C702 (100 pF) optional bypass in parallel

> **⚠ SCHEMATIC ERROR — R702/R703:** The current schematic (2026-04-03) has R702 = R703 = **33 Ω**. This is incorrect — the value must be **330 Ω**. At 33 Ω, each pull-up draws ~150 mA from +5 V, overloads the IFOP/IFON pins, and provides a completely wrong source impedance for the 455 kHz ceramic filter (~1–2 kΩ). **Change R702 and R703 to 330 Ω in the schematic before ordering.**

Each of the three AD8342 supply pins (VPDC, VPLO, VPMX) has its own 1 µF + 100 pF decoupling stack. An additional 100 nF + 1 nF provides HF bypass on the shared VCC net.

| Ref | Value | Notes |
|---|---|---|
| C701 | 1 µF | VPDC bulk bypass, 0402 |
| C703 | 100 nF | VCC HF decoupling, 0402 |
| C704 | 1 nF | VCC HF decoupling (additional), 0402 |
| C705 | 100 pF | RFIN+ coupling |
| C707 | 100 pF | RFIN− AC ground |
| C708 | 1 µF | VPLO bulk bypass |
| C709 | 100 pF | VPLO HF bypass |
| C711 | 1 µF | VPMX bulk bypass |
| C712 | 100 pF | VPMX HF bypass |
| C713 | 100 pF (TH) | LOIP coupling — through-hole, hand-solder |
| C714 | 100 pF | LOIN AC ground |
| C702 | 100 pF | EXRB bypass (optional, in parallel with R701) |
| R701 | 1.82 kΩ | EXRB bias — EXRB to GND |
| R702 | **330 Ω** | IFOP pull-up to +5 V *(schematic shows 33 Ω — error)* |
| R703 | **330 Ω** | IFON pull-up to +5 V *(schematic shows 33 Ω — error)* |
| R704 | 22 Ω | LO series resistor |

**Net names:** `IF_OUT_BP_FILTER` → RFIN+; `CLK1` → LOIP; `BF_FILTER_IN` → IFOP → 455 kHz filter

---

## Page 8 — 455 kHz IF Filter (L801)

| Ref | Part | LCSC | Notes |
|---|---|---|---|
| L801 | HCCF2-455.000-LTWCDL | C42392417 | 455 kHz ceramic BPF, ±10 kHz BW, 4-pin SMD |

**Pin assignments:** Pin 1 = input, Pin 2 = output, Pin 3/4 = GND.

This filter provides the **channel selectivity**: ±10 kHz passband (20 kHz total) passes AM voice (≤4 kHz audio baseband) while rejecting adjacent channels at ±25 kHz spacing by >30 dB. Insertion loss: typically 6 dB (spec max). Measure on assembled board.

**Impedance:** HCCF2 input/output impedance is typically 1–2 kΩ. Mixer 2 IFOP pull-up (R702 = 330 Ω, after correction) drives the filter; the mismatch causes some insertion loss — acceptable for a prototype. An LC matching network can improve this in a future revision.

**Net names:** `BF_FILTER_IN` → filter pin 1; `BF_FILTER_OUT` → filter pin 2 → Mixer 3 RFIN+

---

## Page 9 — Product Detector (AD8342 Mixer 3, U901)

Synchronous AM demodulation at 455 kHz. The 455 kHz IF (amplitude modulated) is multiplied by CLK2 (455 kHz fixed), producing baseband audio.

Circuit topology is **identical to Mixer 1** with these differences:
- RFIN+ receives 455 kHz IF signal (from L801 output, net `BF_FILTER_OUT`)
- LOIP receives CLK2 (455 kHz) via 100 pF TH + 22 Ω
- IFOP pull-up: R902 = 200 Ω to +5 V (audio output load)
- IFON pull-up: R903 = 200 Ω to +5 V (termination)
- IFOP output AC-coupled to volume control via C906 (100 nF)
- EXRB: R901 (1.82 kΩ) → GND; C902 (100 pF) optional bypass in parallel

**Product detection note:** CLK2 is a free-running 455 kHz tone from the SI5351. The phase difference between CLK2 and the received carrier is arbitrary but stable (both referenced to the same 26 MHz TCXO). Any phase offset scales the audio amplitude by cos(Δφ) — voice AM is intelligible at any phase.

| Ref | Value | Notes |
|---|---|---|
| C901 | 1 µF | VPDC bulk bypass |
| C902 | 100 pF | EXRB bypass (optional, in parallel with R901) |
| C903 | 100 nF | VCC HF decoupling |
| C904 | 1 nF | VCC HF decoupling (additional) |
| C905 | 100 pF | RFIN+ coupling |
| C906 | 100 nF | Audio output AC coupling (IFOP → volume control) |
| C907 | 100 pF | RFIN− AC ground |
| C908 | 1 µF | VPLO bulk bypass |
| C909 | 100 pF | VPLO HF bypass |
| C910 | 100 pF | IFON AC coupling (output side) |
| C911 | 1 µF | VPMX bulk bypass |
| C912 | 100 pF | VPMX HF bypass |
| C913 | 100 pF (TH) | LOIP coupling — through-hole, hand-solder |
| C914 | 100 pF | LOIN AC ground |
| R901 | 1.82 kΩ | EXRB bias — EXRB to GND |
| R902 | 200 Ω | IFOP pull-up to +5 V |
| R903 | 200 Ω | IFON pull-up to +5 V (termination) |
| R904 | 22 Ω | LO series resistor |

**Net names:** `BF_FILTER_OUT` → RFIN+; `CLK2` → LOIP; `AUDIO_RAW` → C906 → volume control

---

## Page 10 — Volume Control and Audio Gain (LT6202, U1001)

Volume control and audio amplification on a single schematic page. The volume control (R1005) is a PCB-mounted multi-turn trimmer placed in series with the audio input — set once at bring-up, not user-adjustable in normal operation.

### Volume Control

| Ref | Value | Notes |
|---|---|---|
| R1005 | 10 kΩ trimmer (Bourns 3296W-1-103LF, C34846) | In series with AUDIO_RAW; sets audio drive level into op-amp |

### LT6202 Amplifier (U1001)

Inverting amplifier with single-supply mid-rail biasing and low-pass feedback.

**Single-supply biasing:** R1001 (5.1 kΩ to +5 V) and R1006 (5.1 kΩ to GND) set the non-inverting input (+IN) to 2.5 V, biasing the output at mid-supply for maximum symmetric swing.

| Component | Value | Function |
|---|---|---|
| R1001 | 5.1 kΩ | +IN bias divider — top (to +5 V) |
| R1006 | 5.1 kΩ | +IN bias divider — bottom (to GND) |
| R1003 | 200 Ω | Input resistor (−IN) |
| R1002 | 5.1 kΩ | Feedback resistor |
| C1002 | 10 nF | Feedback LP cap — sets −3 dB at ~3.1 kHz |
| R1004 | 51 Ω | Output series isolator (cap load protection) |
| C1005 | 4.7 µF | Input AC coupling (from R1005 wiper to R1003) |
| C1006 | 4.7 µF | Additional input AC coupling / DC blocking |
| C1001 | 100 nF | VCC decoupling |
| C1003 | 10 nF | VCC HF decoupling |
| C1004 | 100 nF | VCC decoupling (output stage) |
| C1007 | 10 nF | Output coupling / HF bypass |

**Gain:**
```
Av = −R1002 / R1003 = −5.1 kΩ / 200 Ω = −25.5 (≈ 28 dB, inverting)
```

**Low-pass −3 dB:**
```
f_LP = 1 / (2π × R1002 × C1002) = 1 / (2π × 5.1k × 10n) ≈ 3.1 kHz
```

**Net names:** `AUDIO_RAW` → C906 → R1005 → C1005 → R1003 → U1001 −IN; `AUDIO_OUT` → R1004 → PAM8406

---

## Page 11 — Audio Power Amplifier (PAM8406, U1101)

**PAM8406** class-D stereo amplifier. The squelch circuit documented below was planned but is **not present in the current schematic (2026-04-03)**. Verify page 11 content and add squelch if required.

| Ref | Value | Notes |
|---|---|---|
| U1101 | PAM8406DR | Class-D stereo amp, SOIC-16 |
| C1102–C1104 | 10 µF | Supply decoupling, 0603 |
| C1106 | 100 nF | VDD decoupling, 0402 |
| R1101 | 0 Ω | Gain select |
| R1102 | 10 kΩ | MUTE#/SHDN# pull-up to +5V_P_AMP |
| P1101 | WJ500V-5.08-2P | Speaker screw terminal (C8465) |

**Net names:** `AUDIO_OUT` → U1101 input; `+5V_P_AMP` → U1101 supply; P1101 → Speaker

---

## Signal Chain Summary

| Stage | IC | Supply | Net in | Net out |
|---|---|---|---|---|
| RF BPF | passive | — | RF_ANT | RF_IN_LNA |
| LNA (optional) | BGA2869 U401 | +5 V | RF_IN_LNA | RF_OUT_LNA |
| RF Mixer | AD8342 U501 | +5 V | RF_OUT_LNA + CLK0 | IF_IN_BP_FILTER |
| 10.7 MHz filter | L601 | — | IF_IN_BP_FILTER | IF_OUT_BP_FILTER |
| 2nd IF Mixer | AD8342 U701 | +5 V | IF_OUT_BP_FILTER + CLK1 | BF_FILTER_IN |
| 455 kHz filter | L801 | — | BF_FILTER_IN | BF_FILTER_OUT |
| Product Detector | AD8342 U901 | +5 V | BF_FILTER_OUT + CLK2 | AUDIO_RAW |
| Volume control | R1005 (trimmer) | — | AUDIO_RAW | → R1003 |
| Audio gain/LPF | LT6202 U1001 | +5 V | → R1003 | AUDIO_OUT |
| Audio power amp | PAM8406 U1101 | +5V_P_AMP | AUDIO_OUT | Speaker (P1101) |

---

## PCB Layout Notes

| Component | Critical note |
|---|---|
| U501, U701, U901 (AD8342) | LFCSP-16: EP = GND, connect to GND plane with vias. Each supply pin (VPDC/VPLO/VPMX) needs its own 1 µF + 100 pF within 1 mm. |
| L601 (10.7 MHz filter) | Short, direct traces. Keep away from CLK1 (10.245 MHz) trace — only 455 kHz separation. |
| L801 (455 kHz filter) | Short direct traces. Low frequency — less critical for trace length but avoid RF coupling. |
| U401 (BGA2869) | GND pins 2, 4, 5 to GND plane. No thermal pad. |
| U101 tab | +3.3 V — isolated polygon. |
| U102 tab | +5 V — isolated polygon. |
| U103 tab | +5V_P_AMP — isolated polygon. |
| C513, C713, C913 (LO coupling) | Through-hole 100 pF — hand-solder after SMT assembly. |
| LO traces (CLK0–CLK2) | Short traces from MS5351M. 22 Ω series resistors close to PLL outputs. |
| R1005 (volume trimmer) | Through-hole; accessible for one-time calibration at bring-up. |
