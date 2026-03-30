# Hardware Description — AM Airband Receiver V2

Stage-by-stage circuit description. References use the designator convention Uxxx, Cxxx, Rxxx, Lxxx inherited from V1 where components are shared; new V2 designators start from their respective page-equivalent numbering.

---

## Page 1 — Power Supply

**Identical to V1.** Three regulated rails from a single DC input (VCC, 6–15 V):

| Rail | Regulator | Package | Max current | Load in V2 |
|---|---|---|---|---|
| +3.3 V | AMS1117-3.3 (U101) | SOT-223 | 800 mA | MS5351M, TCXO |
| +5 V | AMS1117-5.0 (U103) | SOT-223 | 800 mA | AD8342 ×2, BGA2869 (optional), LT6202 |
| +5V_P_AMP | AMS1084CM-5.0 (U102) | TO-263 | 5 A | PAM8406 only |

DC input via DC101 (DC-005 2.0 barrel jack, 5.5/2.0 mm). Bulk decoupling: 100 µF tantalum capacitors (CASE-B) on each rail.

**Tab pad warning (identical to V1):** All three regulators have their exposed tab/pad connected to Vout — NOT GND. Each requires an isolated copper polygon on its output net.

---

## Page 2 — Clock Generator

**Identical to V1.** MS5351M (U201) PLL locked to KDS 1XTW26000MAA 26 MHz TCXO (X201).

- Supply: 3.3 V
- I²C address: 0x60 (7-bit)
- I²C pull-ups: R201, R202 = 2 kΩ to +3.3 V
- Output bypassing: 100 nF (C201, C202) + 1 µF (C203) on VCC

In V2 only **CLK0** and **CLK1** are used:

| Output | Frequency | Purpose |
|---|---|---|
| CLK0 | 128.7–147.7 MHz (RF + 10.7 MHz) | RF mixer LO1 |
| CLK1 | 10.7 MHz (fixed) | Product detector LO2 |
| CLK2 | disabled | unused |

Each active CLK output is AC-coupled to its load via a 100 pF series capacitor and a 22 Ω series resistor to reduce ringing and limit LO drive level at the AD8342 LO input.

**Frequency note:** The SI5351 CLK0 must track the desired receive frequency. For a station at 127.5 MHz (airband midpoint), CLK0 = 127.5 + 10.7 = 138.2 MHz. The SI5351 is set via I²C by a microcontroller; CLK1 is always 10.7 MHz.

---

## Page 3 — RF Bandpass Filter (118–137 MHz)

### Purpose

Reject out-of-band signals before the mixer — in particular the image frequency, which for high-side LO injection (LO1 = RF + IF) falls at:

```
f_image = LO1 + IF = (RF + IF) + IF = RF + 2 × IF
```

| RF channel | LO1 | Image |
|---|---|---|
| 118.0 MHz | 128.7 MHz | 139.4 MHz (+2.4 MHz above airband top) |
| 127.5 MHz | 138.2 MHz | 148.9 MHz |
| 137.0 MHz | 147.7 MHz | 158.4 MHz |

The worst case is 139.4 MHz (for 118 MHz reception), only 2.4 MHz above the filter's upper passband edge. A 3-pole filter with 19 MHz bandwidth provides limited attenuation this close to the passband — approximately 6–10 dB. This is an accepted limitation of the 10.7 MHz IF with high-side injection over a wide tuning range; see ANALYSIS.md for detailed image rejection estimates and the trade-off with low-side injection.

### Topology — Reused from V1

The V2 RF preselector uses the **identical topology and component values** as the V1 design ("Pi with series resonance"), because L302//C301 resonates at:

```
f = 1 / (2π × √(18 nH × 100 pF)) = 118.7 MHz
```

This is exactly the lower edge of the airband, giving naturally shaped bandpass coverage from 118 to ~137 MHz with CT301 trimmer adjustment for peak alignment.

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

CT301 (trimmer) is placed in parallel with the series coupling network to fine-tune the centre frequency after assembly, compensating for inductor tolerances. TP301 provides a 0.5 mm test point at the filter output for signal injection and spectrum analyser probing.

| Ref | Value | Manufacturer | LCSC | Notes |
|---|---|---|---|---|
| RF301 | SMA 3 GHz | 皇捷 2LC15SF087 | C22363778 | Through-hole, same as V1 |
| D301 | ESD 140 V | Littelfuse PESD0402-140 | C10662 | On RF_ANT net |
| L301 | 47 nH | muRata LQW15AN47NJ00D | C192855 | Series inductor (shunt arm) |
| L302, L303 | 18 nH | muRata LQW15AN18NJ00D | C82917 | Series inductors; resonates with C301/C303 at 118.7 MHz |
| C301, C303 | 100 pF | FH 0402CG101J500NT | C1546 | Shunt capacitors |
| C302 | 3.3 pF | FH 0402CG3R3C500NT | C1565 | Series coupling cap |
| CT301 | 1.5–3 pF | SEHWA STC3MA03-T1 | C22468119 | Centre frequency trimmer |
| TP301 | — | — | — | Test point at RF_IN_LNA |

All V1 LCSC part numbers carry over unchanged. The only assembly difference is that CT301 should be adjusted to peak at the desired channel, typically 127–128 MHz (airband centre).

**Net names:**
- `RF_ANT` — SMA RF301 → D301 → filter input
- `RF_IN_LNA` — filter output → JP401 (LNA bypass) / LNA input / Mixer 1 RF input

---

## Page 4 — Optional LNA (BGA2869)

### Purpose

Improve system noise figure when operating with a short or lossy antenna. Bypassable via a solder jumper (or 2-position header + jumper cap) so it can be excluded during initial bring-up or strong-signal testing.

### Circuit

| Ref | Value | Notes |
|---|---|---|
| U401 | BGA2869 | SOT-363, internally 50 Ω matched |
| C401, C402 | 100 nF | VCC decoupling, 0805 |
| C403 | 100 nF | RF supply decoupling, 0805 |
| C404, C405 | 220 pF | RF input/output coupling |
| JP401 | 2-pin solder jumper | Bypass LNA — short = bypass, open = LNA in circuit |

The BGA2869 is identical to U401 and U801 in V1. It has no exposed thermal pad (SOT-363). GND pins 2, 4, 5 connect to GND plane with short traces and vias.

**Key specs:**
- Gain: 31.7 dB at 950 MHz (slightly lower at 130 MHz, consult datasheet Table 3)
- NF: 3.1 dB
- Supply: +5 V, ~24 mA typical
- No external matching components required (internally matched to 50 Ω)

**Note:** If the LNA causes overload from strong local transmitters, install a 3–6 dB resistive attenuator pad between the filter and the LNA input. An attenuator position is included in the signal chain for this purpose.

---

## Page 5 — RF Mixer (AD8342 Mixer 1)

### Purpose

Down-convert the 118–137 MHz RF signal to 10.7 MHz IF using the SI5351 CLK0 output as LO1.

### AD8342 Overview

The AD8342 is an active double-balanced mixer from Analog Devices:

- Supply: 2.7–5.5 V (5 V used here)
- Supply current: ~55 mA typical at 5 V
- LO drive: −10 to +3 dBm (very low — compatible with SI5351 direct drive)
- Conversion gain: ~3.5 dB typical at 5 V
- IIP3: +24 dBm (5 V supply)
- RF input: differential (RFIN+/RFIN−), ~200 Ω internally biased
- IF output: differential current output (IFOP/IFON), requires pull-up load
- Package: 16-lead LFCSP 3×3 mm (AD8342ACPZ-REEL7, LCSC C182567)

### Single-Ended Connections

The AD8342 is used single-ended in V2 to avoid baluns:

**RF input (single-ended):**
- RFIN+ connected to signal via 100 pF AC coupling capacitor
- RFIN− connected to GND via 100 pF AC coupling capacitor

**LO input (single-ended):**
- LOIP connected to CLK0 via 100 pF coupling + 22 Ω series resistor
- LOIN connected to GND via 100 pF coupling capacitor

**IF output (single-ended):**
- IFOP pulled up to +5 V through R501 (270 Ω) — IF signal taken from this node
- IFON pulled up to +5 V through R502 (270 Ω) — terminated, not used further
- The differential output current flows through R501/R502; the voltage at IFOP is the single-ended IF output

### Impedance at IF Output

The IFOP node (signal taken here) presents the output as a ~270 Ω Thevenin source driving the 10.7 MHz ceramic filter input. The ceramic filter input impedance is approximately 270–330 Ω, giving a reasonable direct match. A 0–50 Ω series resistor in the IF output path can be used to adjust the loaded Q.

### Circuit Components

| Ref | Value | Notes |
|---|---|---|
| U501 | AD8342 | Active mixer, TSSOP-16 |
| C501 | 100 pF | RF input coupling (RFIN+) |
| C502 | 100 pF | RF input AC ground (RFIN−) |
| C503 | 100 pF | LO coupling (LOIP) |
| C504 | 100 pF | LO AC ground (LOIN) |
| C505 | 1 µF | VCC decoupling, 0402 MLCC |
| C506 | 100 nF | VCC decoupling, 0402 |
| R501 | 270 Ω | IFOP pull-up to +5 V |
| R502 | 270 Ω | IFON pull-up to +5 V (termination) |
| R503 | 22 Ω | LO series resistor (ringing suppression) |
| EN501 | — | EN pin tied to +5 V (always enabled) |

**Net names:**
- `RF_OUT_LNA` — LNA output / Mixer 1 RFIN+ coupling cap input
- `LO1` — CLK0 output → R503 → LOIP
- `IF_IN` — IFOP → IF filter input

---

## Page 6 — 10.7 MHz IF Filter

Identical to V1 page 6.

| Ref | Value | Notes |
|---|---|---|
| L601 | HCCF3-10.700-F280IL03A5L | 10.7 MHz ceramic BPF, 4-pin, ~280 kHz bandwidth |

The ceramic filter has a nominal input and output impedance of approximately 270–330 Ω. The Mixer 1 IFOP pull-up resistor (R501 = 270 Ω) drives the filter input; the filter output drives the Mixer 2 RF input.

**Insertion loss:** approximately 3–5 dB typical for HCCF3 series filters.

**Note on 50 Ω filters:** If a 50 Ω IF filter variant is used in a later revision, the AD8342 IF output pull-up resistors and the Mixer 2 RF input coupling must be adjusted accordingly. A 50 Ω filter eliminates the need for resistive matching and is recommended for production.

**Net names:**
- `IF_IN` — Mixer 1 IFOP → filter pin 1
- `IF_OUT` — filter pin 4 → Mixer 2 RFIN+

---

## Page 7 — Product Detector (AD8342 Mixer 2)

### Purpose

Synchronous AM demodulation. The 10.7 MHz IF (amplitude modulated) is multiplied by the fixed 10.7 MHz LO2 (CLK1), producing a baseband audio output proportional to the modulating signal.

### AM Product Detection

For an AM signal at 10.7 MHz with modulation m(t):

```
IF(t) = A × [1 + m(t)] × cos(2π × 10.7 MHz × t + φ_IF)
LO2(t) = cos(2π × 10.7 MHz × t)
```

Product = IF × LO2 → after low-pass filtering:
```
Audio ≈ (A/2) × [1 + m(t)] × cos(φ_IF)
```

The audio amplitude depends on cos(φ_IF), the phase difference between LO2 and the received carrier. For a free-running LO2, this phase difference is arbitrary but stable (both derived from the same TCXO). In practice, voice AM reception is fully intelligible with any phase offset — only the amplitude scales, not the waveform.

### Circuit

Identical topology to Mixer 1, but:
- RF input receives the 10.7 MHz IF signal (not a VHF RF signal)
- LO input receives CLK1 (10.7 MHz, fixed)
- IF output carries audio-frequency content (300 Hz – 3 kHz)

| Ref | Value | Notes |
|---|---|---|
| U701 | AD8342 | Active mixer, TSSOP-16 |
| C701 | 100 pF | IF input coupling (RFIN+) |
| C702 | 100 pF | IF input AC ground (RFIN−) |
| C703 | 100 pF | LO coupling (LOIP) |
| C704 | 100 pF | LO AC ground (LOIN) |
| C705 | 1 µF | VCC decoupling, 0402 MLCC |
| C706 | 100 nF | VCC decoupling, 0402 |
| R701 | 200 Ω | IFOP pull-up to +5 V (audio output load) |
| R702 | 200 Ω | IFON pull-up to +5 V (termination) |
| R703 | 22 Ω | LO series resistor |

**At audio frequencies**, the IF output pull-up value is less critical than at IF. 200 Ω values are used here to set a DC operating point. The audio signal is AC-coupled to the LT6202 input.

**Net names:**
- `IF_OUT` — IF filter output → Mixer 2 RFIN+
- `LO2` — CLK1 → R703 → LOIP
- `AUDIO_RAW` — IFOP → C707 → LT6202 input

---

## Page 8 — Audio Filter and Gain (LT6202)

**Largely identical to V1 page 11**, with adjusted component values for audio-band filtering.

The LT6202 operates in an inverting configuration providing:
- DC blocking at input (series coupling capacitor C707)
- Low-pass filtering to limit bandwidth to ~3–4 kHz (voice audio)
- Voltage gain of approximately 5–6× (~14–16 dB)

### Topology

Single-ended input from Mixer 2 IFOP output (via AC coupling). Single-ended output to PAM8406.

The V1 design used the LT6202 in a differential-to-single-ended configuration (to receive the LT5560 differential output). In V2, with a single-ended signal from AD8342, the circuit is simplified to a standard inverting amplifier with low-pass feedback:

```
AUDIO_RAW ──[C707 100nF]──[R1101 5.1kΩ]──┬──[R1102 5.1kΩ]──► AUDIO_OUT
                                           │
                                        [U1101 LT6202]
                                           │
                            ┌──────────────┘
                            │
                         [R1103 + C_feedback]  (low-pass feedback)
```

| Ref | Value | Notes |
|---|---|---|
| U1101 | LT6202IS5 | High-speed op-amp, SOT-23-5 |
| C707 | 100 nF | AC input coupling (AUDIO_RAW → R1101) |
| R1101 | 5.1 kΩ | Input resistor |
| R1102 | 5.1 kΩ | Gain setting / feedback resistor |
| R1103 | 51 Ω | Output series isolator (cap load protection) |
| C1101 | 100 nF | VCC decoupling |
| C1102 | 10 nF | Feedback cap in parallel with R1102 (sets −3 dB at ~3.1 kHz) |
| C1103 | 100 nF | V+ biasing / decoupling |

**Gain calculation:**
```
Av = −R1102 / R1101 = −5.1k / 5.1k = −1 (inverting, unity gain)
```

Adjust R1102 to increase gain if audio level is insufficient. A gain of 5× requires R1102 = 25.5 kΩ. The values above are starting points for bring-up; tune during testing.

**Low-pass −3 dB frequency:**
```
f_LP = 1 / (2π × R1102 × C1102) ≈ 1 / (2π × 5.1k × 10n) ≈ 3.1 kHz
```

**Net names:**
- `AUDIO_RAW` — Mixer 2 IFOP (AC coupled via C707)
- `AUDIO_OUT` — LT6202 output → C1206 → PAM8406 input

---

## Page 9 — Audio Power Amplifier (PAM8406)

**Identical to V1 page 12.**

| Ref | Value | Notes |
|---|---|---|
| U1201 | PAM8406DR | Class-D stereo amp, SOIC-16, 2×3 W into 4 Ω |
| C1206 | 100 nF | Input AC coupling (AUDIO_OUT → PAM8406) |
| C1202–C1204 | 10 µF | Supply decoupling, 0603 MLCC |
| C1201, C1205 | 1 nF | Filter capacitors |
| R1201 | 0 Ω | Gain select (BTL configuration) |
| R1202 | 10 kΩ | MUTE# / SHDN# pull-up to +5V_P_AMP |
| P1201 | WJ500V-5.08-2P | Speaker screw terminal, 2-pin |

The PAM8406 MUTE# and SHDN# pins are pulled high via R1202 to keep the amplifier active. Both pins can be driven low by an external signal (e.g. from a microcontroller) to mute or shut down the amplifier.

**Net names:**
- `MUTE` — R1202 pull-up → PAM8406 MUTE# and SHDN#
- `+5V_P_AMP` — dedicated audio supply rail

---

## Signal Chain Summary

| Stage | IC | Supply | Net in | Net out |
|---|---|---|---|---|
| RF BPF | passive | — | RF_ANT | RF_IN_LNA |
| LNA (optional) | BGA2869 U401 | +5 V | RF_IN_LNA | RF_OUT_LNA |
| RF Mixer | AD8342 U501 | +5 V | RF_OUT_LNA + LO1 | IF_IN |
| IF Filter | L601 | — | IF_IN | IF_OUT |
| Product Detector | AD8342 U701 | +5 V | IF_OUT + LO2 | AUDIO_RAW |
| Audio gain/LPF | LT6202 U1101 | +5 V | AUDIO_RAW | AUDIO_OUT |
| Audio power amp | PAM8406 U1201 | +5V_P_AMP | AUDIO_OUT | Speaker |

---

## Impedance Summary

| Domain | Impedance | Notes |
|---|---|---|
| Antenna / SMA | 50 Ω | Standard coaxial |
| RF BPF | 50 Ω in/out | Designed for 50 Ω termination |
| BGA2869 LNA | 50 Ω in/out | Internally matched |
| AD8342 RFIN | ~200 Ω (differential), ~100 Ω (single-ended) | High-impedance input; mismatch loss from 50 Ω source is ~6 dB — acceptable for prototype |
| AD8342 IF output | ~270 Ω Thevenin | Set by pull-up resistors R501/R502 |
| 10.7 MHz ceramic filter | ~270–330 Ω | Resistive match from Mixer 1 output |
| AD8342 product det. RF input | ~100 Ω single-ended | Driven from ceramic filter output (~330 Ω) — light mismatch |
| Audio stages | Non-critical | LT6202 drives PAM8406 via AC coupling |

**Important:** Do NOT attempt to force 50 Ω throughout. The AD8342 RF input is high impedance by design. Inserting a 50 Ω shunt pad before the AD8342 would cause ~6 dB additional loss and is unnecessary in a receive-only application.

---

## PCB Layout Notes

| Component | Critical note |
|---|---|
| U501, U701 (AD8342) | Place VCC decoupling (100 nF + 1 µF) as close as possible to VCC pins. Use vias to GND plane on GND pins. |
| L601 (IF filter) | Short, direct traces from Mixer 1 output to filter pin 1, and from filter pin 4 to Mixer 2. Minimise stray capacitance. |
| RF signal path | Use 50 Ω trace width from antenna to LNA output. After the LNA the impedance is no longer 50 Ω — trace width is less critical but keep short. |
| U401 (BGA2869) | GND pins 2, 4, 5 directly to GND plane with short traces and multiple vias. SOT-363 has no thermal pad. |
| LO traces | Keep CLK0 and CLK1 traces short and away from IF/audio paths. Add 22 Ω series resistors close to the MS5351M outputs. |
| Tab pads (U101, U102, U103) | All three regulators: tab = Vout. Isolated polygons on respective supply nets — identical requirement to V1. |
