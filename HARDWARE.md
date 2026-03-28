# Hardware Description

Detailed circuit description for SI5351-TCXO-Radio. Refer to the schematic PDF for component reference designators.

---

## Signal Chain Overview

The receiver uses a triple-conversion architecture. The first two conversions bring the RF signal down to 455 kHz via 10.7 MHz intermediate frequency. A third mixer performs the final detection. Throughout the chain, ceramic bandpass filters suppress adjacent channels and image frequencies.

---

## Page-by-Page Circuit Description

### Page 1 — Power Supply

Three independent regulated rails are generated from a single DC input (VCC, 6–15 V):

| Designator | Part | Output | Max current | Load |
|---|---|---|---|---|
| U101 | AMS1117-3.3 | +3.3 V | 800 mA | MS5351M, TCXO |
| U103 | AMS1117-5.0 | +5 V | 800 mA | Signal chain |
| U102 | AMS1084CM-5.0 | +5V_P_AMP | 5 A | PAM8406 only |

Bulk filtering is provided by fourteen 100 µF tantalum capacitors (Kyocera AVX TAJB107K006RNJ, CASE-B 3528) distributed across all power rails, combined with 100 nF ceramic decoupling capacitors. DC power input is via a standard 5.5/2.0 mm barrel jack (DC101).

The PAM8406 amplifier is powered from a separate AMS1084CM-5.0 (U102) to isolate audio switching noise from the signal chain and to handle the high peak currents (up to ~900 mA at full output power) without overloading AMS1117.

**Tab pad warning:** All three regulators have their tab pad connected to Vout (not GND). Each requires an isolated copper polygon on the PCB — see [POWER.md](POWER.md) for thermal calculations.

---

### Page 2 — Clock Generator (SI5351_TCXO)

The MS5351M (U201) is an I²C-programmable PLL/clock generator, pin-compatible with the popular SI5351. It is referenced to a 26 MHz TCXO (X201, KDS 1XTW26000MAA) for good frequency stability.

Three independent clock outputs are used:

| Output | Net | Destination | Purpose |
|---|---|---|---|
| CLK0 | CLK0 | U501 LO input | First LO (RF → 10.7 MHz) |
| CLK1 | CLK1 | U701 LO input | Second LO (10.7 MHz → 455 kHz) |
| CLK2 | CLK2 | U1001 LO input | Third LO (455 kHz → audio) |

I²C pull-up resistors R201 and R202 (2 kΩ each) connect SDA and SCL to +3.3 V. The MS5351M is programmed via an external microcontroller or host computer over I²C to set the three LO frequencies for the desired receive frequency.

Decoupling: C201, C202 (100 nF) and C203 (1 µF) on VDD; C204 (100 nF) on VDDO.

---

### Page 3 — RF Preselector Filter

A passive LC bandpass filter sits between the SMA antenna connector (RF301) and the first LNA. Its purpose is to reject out-of-band signals and reduce the LNA's exposure to strong interferers.

**Filter topology:** Pi with series resonance

| Component | Value | Function |
|---|---|---|
| L301 | 47 nH | Series inductor (shunt arm) |
| L302, L303 | 18 nH | Series inductors |
| C301, C303 | 100 pF | Shunt capacitors |
| C302 | 3.3 pF | Series coupling cap |
| CT301 | 1.5–3 pF | Trimmer — tunes centre frequency |

**ESD protection:** D301 (PESD0402-140, Littelfuse) on the antenna input clamps transients to 140 V. TP301 provides a test point at the filter output (RF_IN_LNA).

---

### Page 4 — RF LNA (BGA2869)

U401 is an NXP BGA2869 MMIC wideband amplifier in SOT-363. It provides approximately 32 dB of gain with a 3.1 dB noise figure, making it suitable as a front-end LNA from DC to 2.2 GHz.

**Key specifications (at 950 MHz, 5 V, 25°C):**

| Parameter | Value |
|---|---|
| Gain | 31.7 dB (typ) |
| Noise figure | 3.1 dB (typ) |
| Supply current | 24 mA (typ) |
| Output P1dB | 10 dBm (typ) |
| Supply voltage | 4.5–5.5 V |

The device is internally matched to 50 Ω and requires no external matching components.

- C404, C405 (220 pF): AC coupling on RF input and output
- C401, C402, C403 (100 nF): Supply decoupling
- Supply: +5 V

**Note:** The BGA2869 has no exposed thermal pad — all six pins of the SOT-363 package are signal or GND pins. GND pins (2, 4, 5) should be connected to the GND plane with short traces and vias.

---

### Page 5 — First Mixer: RF → 10.7 MHz (LT5560)

U501 (LT5560EDD) is a low-power double-balanced active mixer from Analog Devices (Linear Technology). It converts the amplified RF signal to a 10.7 MHz first IF using CLK0 as the local oscillator.

**Key specifications:**

| Parameter | Value |
|---|---|
| Conversion gain | 2.4 dB (typ) |
| SSB noise figure | 9.3 dB (typ at 900 MHz out) |
| IIP3 | 9 dBm (typ, ICC = 10 mA) |
| LO drive | −2 dBm (typ) |
| Supply voltage | 2.7–5.3 V |
| Supply current | 10 mA (typ, R = 3 Ω) |

**Circuit details:**

- **LO input (CLK0):** L501 (47 nH) and C506 (4.7 pF) form an LC matching network to the LO± pins. C501 (1 nF) is a DC blocking capacitor.
- **RF input:** U502 (WBC1-1TLC, 1:1 balun transformer) converts single-ended RF_OUT_LNA to differential for the IN+/IN− inputs. C502, C503 (1 nF) are DC blocking capacitors.
- **IF output:** U503 (WBC1-1TLC, 1:1 balun) converts differential OUT+/OUT− back to single-ended. C507 (270 pF) and R502 (50 Ω) form the output matching/termination. C504 (100 nF) is supply decoupling for the output network.
- **EN pin:** R501 (10 kΩ) pulls EN to +5 V, keeping the mixer enabled. The EN pin must not float.
- **VCC supply:** C505 (1 µF MLCC 0402) decoupling on pin 7.
- **Exposed pad (EP, pin 9):** PGND — must be soldered to PCB GND plane.

**Important:** LT5560 VCC maximum is 5.3 V. With +5 V from AMS1117-5.0, the margin is adequate under normal conditions. Verify that the +5 V rail does not exceed 5.3 V during transients.

---

### Page 6 — First IF Filter: 10.7 MHz

L601 (HCCF3-10.700-F280IL03A5L, HCI) is a 4-pin ceramic bandpass filter centred at 10.7 MHz. This is the standard first IF frequency used in FM receivers and is widely available.

| Pin | Function |
|---|---|
| 1 | Input |
| 2 | GND |
| 3 | Float (signal ground — leave unconnected) |
| 4 | Output |

The filter provides channel selectivity and rejects the image frequency and LO leakage before the second mixer.

---

### Page 7 — Second Mixer: 10.7 MHz → 455 kHz (LT5560)

U701 (LT5560EDD) converts the 10.7 MHz first IF to a 455 kHz second IF using CLK1 as the local oscillator. The circuit topology mirrors Page 5 with values adjusted for the lower frequency.

**Differences from first mixer:**

- R702 (3 Ω) is fitted at the centre tap of the input balun (U702). This resistor sets the LT5560 quiescent current — with 3 Ω, ICC ≈ 10 mA as per the datasheet recommendation.
- C706 (15 pF) at the output is a shunt capacitor adjusted for 455 kHz output matching.
- L702, L703 (12 nH) remain the same as the first mixer.
- EN pull-up: R701 (10 kΩ) to +5 V.
- U702, U703: WBC1-1TLC 1:1 balun transformers.

---

### Page 8 — IF LNA (BGA2869)

U801 (BGA2869) provides gain between the second mixer and the 455 kHz filter. This stage amplifies the 455 kHz IF signal to ensure adequate drive for the downstream filter and third mixer.

The circuit is identical to the RF LNA (Page 4) but operates at 455 kHz. At this frequency, the BGA2869 gain is slightly higher (~32 dB) and the noise figure is equally good.

**Decoupling on this stage is deliberately thorough:**

| Component | Value | Function |
|---|---|---|
| C801 | 220 µF | Bulk supply decoupling |
| C802, C803 | 100 nF | HF decoupling |
| C804 | 1 nF | VHF decoupling on VCC |
| C805, C806 | 100 nF | AC coupling (RF_IN and RF_OUT) |

---

### Page 9 — Second IF Filter: 455 kHz

L901 (HCCF2-455.000-LTWCDL, HCI) is a 4-pin ceramic bandpass filter centred at 455 kHz. This is the standard second IF for AM receivers.

| Pin | Function |
|---|---|
| 1 | Input |
| 2 | Output |
| 3, 4 | GND |

---

### Page 10 — Third Mixer: 455 kHz → Audio (LT5560)

U1001 (LT5560EDD) performs AM detection by mixing the 455 kHz IF down to audio using CLK2. For AM demodulation, CLK2 is set to exactly 455 kHz (synchronous detection) or offset slightly for a beat note.

**Specific features of this stage:**

- **Single transformer:** Only U1002 (WBC1-1TLC) on the input. The output is taken differentially via R1002 and R1003 (200 Ω pull-up resistors to +5 V), which provide the required DC path to VCC for the OUT+ and OUT− pins. This is the recommended topology for low-frequency applications per the LT5560 datasheet.
- **R1004 (3 Ω):** Centre-tap resistor setting ICC ≈ 10 mA.
- **C1007 (15 pF):** Output matching capacitor.
- **C1006 (1 nF):** DC blocking on output.
- **EN pull-up:** R1001 (10 kΩ) to +5 V.
- The differential audio outputs (AUDIO_MIXER_OUT_POS, AUDIO_MIXER_OUT_NEG) feed the op-amp stage.

---

### Page 11 — Audio Op-Amp (LT6202)

U1101 (LT6202IS5) is a high-speed, low-noise op-amp configured as a differential-to-single-ended amplifier. It converts the LT5560's differential audio output to single-ended for the PAM8406 input.

**Configuration:**

| Component | Value | Function |
|---|---|---|
| C1105, C1106 | 4.7 µF | Input AC coupling |
| C1107, C1102 | 10 nF | Input HF filter |
| R1103, R1105 | 200 Ω | Input resistors |
| R1101, R1102 | 5.1 kΩ | Feedback resistors |
| R1104 | 51 Ω | Output series resistor |
| C1101 | 100 nF | Supply decoupling |
| C1103, C1104 | 100 nF + 10 nF | Supply decoupling |

**Gain:** 5.1 kΩ / 200 Ω = **25.5 (≈ 28 dB)**

Supply: +5 V (single supply, pin 4 = +5 V, pin 1 = GND). The LT6202 supports single-supply operation from 3 V to 12.6 V.

---

### Page 12 — Audio Power Amplifier (PAM8406)

U1201 (PAM8406DR) is a filterless class-D stereo amplifier delivering up to 2×3 W into 4 Ω from a 5 V supply.

**Key details:**

- Supply: +5V_P_AMP on VDD (pin 6), PVDDL (pin 4), and PVDDR (pin 13)
- PGNDL (pin 2) and PGNDR (pin 15) are the power ground returns — keep these traces wide and short to the GND plane
- MUTE# and SHDN# are controlled via the MUTE net (R1202, 10 kΩ pull-up)
- C1206 (100 nF MLCC 0402): AC coupling capacitor on the AUDIO_OUT input from the op-amp
- C1202, C1203, C1204 (10 µF MLCC 0603): Supply decoupling on PVDD rails
- C1205 (1 nF): VDD decoupling
- R1201 (0 Ω): Jumper — can be used to disconnect audio input if needed
- Output connector P1201 (WJ500V-5.08-2P): Screw terminal for speaker connection

**Tab pad:** PAM8406 in SOIC-16 has no exposed thermal pad. Thermal dissipation is via the supply and GND pins.

---

## Key Design Decisions

**Why LT5560 at +5 V?**
The LT5560 is specified for 2.7–5.3 V. Running at +5 V maximises dynamic range and simplifies supply design (single +5 V rail for the whole signal chain). The +5 V rail from AMS1117-5.0 is within the absolute maximum rating under normal conditions.

**Why a separate +5V_P_AMP rail?**
The PAM8406 class-D amplifier generates switching noise on its supply and can draw peak currents up to ~900 mA. Separating it from the signal chain prevents supply noise from coupling into the sensitive IF stages.

**Why ceramic IF filters rather than LC?**
The HCCF series ceramic filters provide predictable, sharp selectivity at 10.7 MHz and 455 kHz with no tuning required. LC filters at these frequencies would require precise wound inductors.

**Why three transformers on the first two mixers but only one on the third?**
The first two mixers operate at RF/IF frequencies where balanced drive and output extraction are important for image rejection and LO leakage suppression. The third mixer works at audio frequencies where the balanced output is better handled with simple resistive pull-ups to VCC, as described in the LT5560 datasheet's low-frequency application section.
