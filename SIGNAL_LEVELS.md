# Signal Level Analysis

Comprehensive signal-level budget for the SI5351-TCXO-Radio triple-conversion superheterodyne receiver.

All values at 25°C unless stated. Values marked **Spec** are taken directly from component datasheets in this repository. Values marked **Est.** are estimated from first principles, datasheet extrapolations, or industry-typical figures for the component type. Where exact ceramic filter data was unavailable, conservative estimates are used.

---

## 1. Component Parameters

### 1.1 BGA2869 MMIC LNA (U401, U801)

| Parameter | Value | Source |
|---|---|---|
| Gain @ 250 MHz | 31.1 dB typ | Spec |
| Gain @ 950 MHz | 31.7 dB typ | Spec |
| Noise figure @ 250–2150 MHz | 3.1 dB typ | Spec |
| Output P1dB @ 250 MHz | 10 dBm | Spec |
| Output P1dB @ 950 MHz | 10 dBm | Spec |
| Saturated output power | 12–13 dBm | Spec |
| OIP3 @ 250 MHz, 5 V | 23 dBm | Spec |
| IIP3 @ 250 MHz, 5 V (derived) | **−9 dBm** | Spec (OIP3 − Gain) |
| Input P1dB (derived) | **−22 dBm** | Spec (OP1dB − Gain) |
| Supply current | 24 mA typ | Spec |
| Frequency range | DC – 2.2 GHz | Spec |

> **Note on gain at AM/SW frequencies:** The BGA2869 is specified from DC to 2.2 GHz. At AM broadcast frequencies (530 kHz–30 MHz), gain is likely ≥32 dB and NF is approximately 3 dB. No datasheet characterisation exists below 250 MHz; these values are conservative estimates based on the typical flat-gain behaviour of InGaP MMIC amplifiers at low frequency.

### 1.2 LT5560 Active Mixer (U501, U701, U1001)

Specified at VCC = 3 V, ICC = 10 mA. At VCC = 5 V the device draws slightly more current, and IIP3/P1dB improve marginally.

| Parameter | Conditions | Value | Source |
|---|---|---|---|
| Conversion gain (downconvert) | 450 MHz in → 70 MHz IF | +2.7 dB typ | Spec |
| Conversion gain (downconvert) | 900 MHz in → 140 MHz IF | +2.6 dB typ | Spec |
| SSB noise figure (downconvert) | 450→70 MHz | 10.5 dB typ | Spec |
| SSB noise figure (downconvert) | 900→140 MHz | 10.1 dB typ | Spec |
| IIP3 (downconvert) | 450→70 MHz | +10.1 dBm typ | Spec |
| IIP3 (downconvert) | 900→140 MHz | +9.7 dBm typ | Spec |
| Input P1dB | 900 MHz→140 MHz | −0.8 dBm typ | Spec |
| LO drive required | — | −2 dBm typ | Spec |
| LO drive range | — | −6 to +1 dBm | Spec |
| Supply voltage | — | 2.7–5.3 V | Spec |
| ICC (with 3 Ω centre-tap) | VCC = 5 V | ~10 mA | Spec/Est. |

> **Note on mixer gain at sub-200 MHz:** The LT5560 is characterised down to 0.01 MHz. At RF inputs of 0.5–30 MHz (AM/SW) converting to a 10.7 MHz IF, performance is expected to be similar to or slightly better than the 450→70 MHz figures. Conversion gain is used as **+2.6 dB** throughout this analysis.

### 1.3 WBC1-1TLC Balun Transformer (U502, U503, U702, U703, U1002)

| Parameter | Value | Source |
|---|---|---|
| Impedance ratio | 1:1 | Spec |
| Specified bandwidth (−3 dB) | **250–750 MHz** | Spec |
| Max insertion loss (in-band) | 0.58 dB | Spec |
| Primary inductance Lp min | 9.5 µH | Spec |

**Frequency-dependent insertion loss (estimated from magnetising inductance model):**

```
IL_dB ≈ −10 × log10[1 + (Z0 / (2ωLp_min))²]   where Z0 = 50 Ω
```

| Frequency | XL = ωLp (Ω) | Z0/(2XL) | Estimated IL | Notes |
|---|---|---|---|---|
| 455 kHz | 27 Ω | 0.92 | **−2.7 dB** | Well below specified range |
| 1 MHz | 60 Ω | 0.42 | **−0.7 dB** | Below specified range |
| 10.7 MHz | 639 Ω | 0.04 | **< 0.1 dB** | Below specified range but negligible |
| 250 MHz+ | — | — | **0.58 dB max** | In specification |

> **Critical note:** U703 (mixer 2 output, 455 kHz) and U1002 (mixer 3 input, 455 kHz) are operating approximately 550× below the transformer's specified lower frequency limit. The estimated −2.7 dB per balun is likely optimistic — actual loss depends on the actual Lp (which may exceed the 9.5 µH minimum), core saturation, and winding parasitics. If the actual Lp is 20 µH, the loss at 455 kHz would be approximately −0.9 dB. This is the most uncertain element in the chain.

### 1.4 IF Bandpass Filters

| Filter | Part | Centre freq | BW (est.) | Insertion loss (est.) | Source |
|---|---|---|---|---|---|
| L601 | HCCF3-10.700-F280IL03A5L | 10.700 MHz | **280 kHz** (3 dB) | **3–5 dB** | Est. (HCCF3 series typical) |
| L901 | HCCF2-455.000-LTWCDL | 455.0 kHz | **~10 kHz** (3 dB) | **4–6 dB** | Est. (HCCF2 series typical) |

> The "F280" suffix in the 10.7 MHz filter designation specifies a 280 kHz 3 dB bandwidth — this is a standard FM intermediate-frequency filter, much wider than required for AM selectivity. The 455 kHz filter (HCCF2) provides the final channel selectivity; standard AM receivers use 9–10 kHz bandwidth at this stage.

### 1.5 RF Preselector (L301–L303, C301–C303, CT301)

| Parameter | Value | Notes |
|---|---|---|
| Topology | LC π with series resonance | Pages 3 of schematic |
| Series inductors | L302 = L303 = 18 nH | |
| Shunt inductor | L301 = 47 nH | |
| Shunt capacitors | C301 = C303 = 100 pF | |
| Series coupling | C302 = 3.3 pF | |
| Tuning trimmer | CT301 = 1.5–3 pF | Adjusts centre frequency |
| Estimated in-band IL | **2–4 dB** | Depends on inductor Q; Est. |
| Centre frequency | ~1 MHz (Med. Wave, typical) | Adjustable via CT301 |
| Estimated 3 dB BW | ~500 kHz–2 MHz | Est.; depends on Q |

### 1.6 LT6202 Op-Amp (U1101)

| Parameter | Value | Source |
|---|---|---|
| Configured gain | R1101/R1103 = 5.1 kΩ / 200 Ω = **25.5 × (+28.1 dB)** | Spec (from component values) |
| Input-referred noise | 1.9 nV/√Hz @ 100 kHz | Spec |
| Input noise current | 1.1 pA/√Hz | Spec |
| GBW product | 100 MHz | Spec |
| Supply | 5 V (single) | — |
| Max output swing | ~4.9 Vpp ≈ 1.73 Vrms (rail-to-rail) | Spec |
| −3 dB bandwidth at ×25.5 | ~4 MHz | Spec (GBW/gain) |

### 1.7 PAM8406 Audio Amplifier (U1201)

| Parameter | Value | Source |
|---|---|---|
| Closed-loop gain (fixed) | **24 dB (×15.8)** | Spec |
| Output power @ 4 Ω, 5 V, 10% THD | **3 W** | Spec |
| Output power @ 4 Ω, 5 V, 1% THD | ~1.5 W (estimated) | Est. |
| Supply voltage | 5 V (from +5V_P_AMP) | — |
| Mode | Class-D (MODE pin high) | — |
| Input impedance | ~20 kΩ internal (RI min = 18 kΩ) | Spec |
| Required input for 3 W out | **~220 mVrms** | Calc (3 W BTL into 4 Ω) |
| Required input for 1.5 W out | ~155 mVrms | Calc |

### 1.8 MS5351M LO (U201) Output Level

The MS5351M generates CMOS-level square-wave outputs on a 3.3 V supply. The LT5560 requires a −2 dBm LO drive with a range of −6 to +1 dBm.

| Parameter | Value | Notes |
|---|---|---|
| Output voltage (CMOS, VDDO = 3.3 V) | 3.3 Vpp into CMOS load | Spec |
| Fundamental amplitude (square wave) | 4/π × 1.65 V ≈ 2.10 Vpk = 1.49 Vrms | Calc |
| Power into 50 Ω (fundamental) | ~+17 dBm | Calc (before matching) |
| After LO matching network (L501 + C506) | **+5 to +12 dBm est.** | Est. (divider + matching) |
| LT5560 LO drive requirement | −2 dBm typ, max +1 dBm | Spec |

> The LO drive level (estimated +5 to +12 dBm) substantially exceeds the LT5560's +1 dBm maximum specified LO input. The L501 (47 nH) + C506 (4.7 pF) LC network forms a matching/attenuating network to reduce the drive level to the desired range. Correct dimensioning of this network is critical; excessive LO drive degrades IIP3 and can damage the mixer.

---

## 2. Stage-by-Stage Signal Chain

Signal levels are traced from the SMA antenna input to the speaker terminal. All levels are in dBm referenced to 50 Ω unless noted. Gain values are **typical**.

| # | Stage | Component | Gain / Loss | Cumul. Gain | Notes |
|---|---|---|---|---|---|
| 0 | Antenna (reference) | RF301 | — | 0 dB | Reference point |
| 1 | RF ESD diode | D301 | ~0 dB | 0 dB | Negligible IL |
| 2 | RF preselector filter | L301–303, C301–303, CT301 | **−3 dB** | −3 dB | Est.; in-band |
| 3 | RF LNA | U401 (BGA2869) | **+32 dB** | +29 dB | Spec; typ at RF |
| 4 | LNA→Mixer balun | U502 (WBC1-1TLC) | **−0.7 dB** | +28.3 dB | Est.; at 1 MHz |
| 5 | First mixer | U501 (LT5560) | **+2.6 dB** | +30.9 dB | Spec; conv. gain |
| 6 | Mixer→IF balun | U503 (WBC1-1TLC) | **< 0.1 dB** | +30.8 dB | Est.; at 10.7 MHz |
| 7 | 10.7 MHz BPF | L601 (HCCF3) | **−4 dB** | +26.8 dB | Est.; ceramic |
| 8 | IF balun (10.7 MHz) | U702 (WBC1-1TLC) | **< 0.1 dB** | +26.7 dB | Est.; at 10.7 MHz |
| 9 | Second mixer | U701 (LT5560) | **+2.6 dB** | +29.3 dB | Spec |
| 10 | Mixer→IF balun (455 kHz) | U703 (WBC1-1TLC) | **−2.7 dB** | +26.6 dB | Est.; out of spec range |
| 11 | 455 kHz BPF | L901 (HCCF2) | **−5 dB** | +21.6 dB | Est.; ceramic |
| 12 | IF LNA | U801 (BGA2869) | **+32 dB** | +53.6 dB | Spec |
| 13 | IF→AM-det balun (455 kHz) | U1002 (WBC1-1TLC) | **−2.7 dB** | +50.9 dB | Est.; out of spec range |
| 14 | AM detector (audio) | U1001 (LT5560) | **+5 dB** | +55.9 dB | Est.; higher Z load (+200 Ω) |
| 15 | Audio op-amp | U1101 (LT6202) | **+28.1 dB** | +84.0 dB | Spec; R1101/R1103 |
| 16 | Audio power amp | U1201 (PAM8406) | **+24 dB** | +108.0 dB | Spec; fixed gain |

> **Mixer 3 gain note (row 14):** The LT5560 audio-detection stage uses 200 Ω pull-up resistors (R1002, R1003) instead of an output balun. The op-amp input resistors R1103/R1105 (200 Ω each) load the pull-ups to ~100 Ω effective per side. Compared to the 50 Ω reference condition in the conversion-gain spec, the effective voltage gain increases by approximately 6 dB from the higher impedance, reduced by ~3 dB from the parallel loading — net estimate **+3 dB above the 50 Ω spec**, giving a total of ~+5.6 dB. Used as **+5 dB (conservative)** above.

**Total system gain (antenna to PAM8406 output): ≈ +108 dB**

---

## 3. Noise Figure Cascade Analysis (Friis Formula)

The system noise figure determines receiver sensitivity. The Friis formula cascades each stage:

```
F_sys = F₁ + (F₂−1)/G₁ + (F₃−1)/(G₁·G₂) + ...
```

where F = linear noise factor, G = linear available gain. Calculations use typical (not worst-case) values.

| Stage | F (lin) | NF (dB) | Cumul. G (lin) | Contribution to F_sys | % of total |
|---|---|---|---|---|---|
| 1 RF preselector (−3 dB) | 2.000 | 3.0 | 0.501 | **2.000** | 46.3 % |
| 2 BGA2869 LNA (+32 dB) | 2.042 | 3.1 | 794 | **2.084** | 48.3 % |
| 3 U502 balun (−0.7 dB) | 1.175 | 0.7 | 501 | 0.000 35 | 0.01 % |
| 4 LT5560 Mixer 1 (NF 10.5 dB) | 11.22 | 10.5 | 912 | 0.011 | 0.25 % |
| 5 U503 balun + L601 BPF (−4.1 dB) | 2.570 | 4.1 | 363 | 0.004 | 0.09 % |
| 6+ All remaining stages | — | — | >> 1 | < 0.005 | < 0.1 % |

**F_sys ≈ 4.32 → System NF ≈ 6.4 dB**

> Noise is dominated almost entirely by the RF preselector insertion loss (~46 %) and the BGA2869 NF (~48 %). Everything from the first mixer onward contributes less than 0.5 % of the total noise figure. This confirms that the front-end design (filter loss and LNA NF) sets the system performance.

---

## 4. Receiver Sensitivity

### 4.1 Minimum Detectable Signal (MDS)

Using the standard formula at ambient temperature (T₀ = 290 K):

```
MDS (dBm) = −174 + NF_sys + 10·log₁₀(B_Hz) + SNR_min
```

| Bandwidth | SNR target | MDS |
|---|---|---|
| 10 kHz (AM channel) | 10 dB (threshold) | **−117.6 dBm** |
| 10 kHz (AM channel) | 20 dB (good audio) | **−107.6 dBm** |
| 10 kHz (AM channel) | 30 dB (hi-fi AM) | **−97.6 dBm** |
| 1 kHz (CW / narrow AM) | 10 dB | **−127.6 dBm** |

### 4.2 Equivalent Antenna Voltage (MDS in µVrms, 50 Ω)

| MDS | Power | Voltage (50 Ω) | Voltage (µVrms) |
|---|---|---|---|
| −117.6 dBm | 1.74 × 10⁻¹² W | 0.295 µVrms | **0.30 µV** |
| −107.6 dBm | 1.74 × 10⁻¹¹ W | 0.933 µVrms | **0.93 µV** |

> As reference: ITU-R minimum field strength for good AM reception is typically ≥ 1 mV/m at the antenna; a 1-metre whip antenna in a 1 mV/m field develops ~0.5 mV = +500 µVrms = **−53 dBm** — this is approximately **55 dB above the 10 dB SNR threshold**, meaning even a short antenna in a typical broadcast environment has an enormous margin over the noise floor.

---

## 5. Signal Level Budget — Three Scenarios

Antenna input is at the SMA connector (RF301), 50 Ω impedance, AM broadcast band (~1 MHz assumed).

### 5.1 Weak Distant Station — Antenna: −110 dBm (0.71 µVrms)

| Stage | Gain | Signal level | Notes |
|---|---|---|---|
| Antenna input | — | **−110 dBm** | Weak station |
| After RF preselector | −3 dB | −113 dBm | |
| After BGA2869 U401 | +32 dB | **−81 dBm** | |
| After balun U502 | −0.7 dB | −81.7 dBm | |
| After LT5560 U501 | +2.6 dB | **−79.1 dBm** | IF at 10.7 MHz |
| After balun U503 | −0.1 dB | −79.2 dBm | |
| After 10.7 MHz BPF L601 | −4 dB | **−83.2 dBm** | |
| After balun U702 | −0.1 dB | −83.3 dBm | |
| After LT5560 U701 | +2.6 dB | **−80.7 dBm** | IF at 455 kHz |
| After balun U703 | −2.7 dB | −83.4 dBm | |
| After 455 kHz BPF L901 | −5 dB | **−88.4 dBm** | Channel filtered |
| After BGA2869 U801 | +32 dB | **−56.4 dBm** | |
| After balun U1002 | −2.7 dB | −59.1 dBm | |
| After LT5560 U1001 (audio) | +5 dB | **−54.1 dBm** | Audio signal |
| After LT6202 U1101 | +28.1 dB | **−26.0 dBm** | ≈ 11.2 mVrms |
| PAM8406 input | — | −26.0 dBm | 11.2 mVrms |
| PAM8406 output | +24 dB | **−2.0 dBm** | ≈ 178 mW into 4 Ω |
| Speaker output | — | **~178 mW** | Audible, reduced volume |

### 5.2 Typical Local Station — Antenna: −80 dBm (22 µVrms)

| Stage | Gain | Signal level | Notes |
|---|---|---|---|
| Antenna input | — | **−80 dBm** | Typical local station |
| After RF preselector | −3 dB | −83 dBm | |
| After BGA2869 U401 | +32 dB | **−51 dBm** | |
| After balun U502 | −0.7 dB | −51.7 dBm | |
| After LT5560 U501 | +2.6 dB | **−49.1 dBm** | 10.7 MHz IF |
| After balun U503 | −0.1 dB | −49.2 dBm | |
| After 10.7 MHz BPF L601 | −4 dB | **−53.2 dBm** | |
| After balun U702 | −0.1 dB | −53.3 dBm | |
| After LT5560 U701 | +2.6 dB | **−50.7 dBm** | 455 kHz IF |
| After balun U703 | −2.7 dB | −53.4 dBm | |
| After 455 kHz BPF L901 | −5 dB | **−58.4 dBm** | |
| After BGA2869 U801 | +32 dB | **−26.4 dBm** | |
| After balun U1002 | −2.7 dB | −29.1 dBm | |
| After LT5560 U1001 | +5 dB | **−24.1 dBm** | Audio |
| After LT6202 U1101 | +28.1 dB | **+4.0 dBm** | ≈ 355 mVrms |
| PAM8406 input | — | +4.0 dBm | 355 mVrms |
| PAM8406 output | +24 dB | **+28.0 dBm** | ≈ 0.63 W into 4 Ω |
| Speaker output | — | **~0.6 W** | Loud, clean audio |

> **Note:** 355 mVrms at PAM8406 input exceeds the ~220 mVrms for full 3 W output. At −80 dBm antenna input, the PAM8406 output is clipping (>3 W into 4 Ω). In practice, audio output will be limited by PAM8406 clipping before the calculated level is reached. A more realistic output will be 3 W with some distortion.

### 5.3 Strong Nearby Transmitter — Antenna: −50 dBm (5 mVrms)

| Stage | Gain | Signal level | Notes |
|---|---|---|---|
| Antenna input | — | **−50 dBm** | Strong local station |
| After RF preselector | −3 dB | −53 dBm | |
| After BGA2869 U401 | +32 dB | **−21 dBm** | |
| LNA input P1dB | — | −22 dBm | 1 dB below LNA compression |

> **⚠ Compression warning:** At −50 dBm antenna input, the BGA2869 is within 1 dB of its input compression point (IIP1dB = −22 dBm). Gain compression will begin, causing non-linear distortion and intermodulation products. With two signals present (desired station + interferer), third-order intermodulation products can fall in-band and cause interference even before the compression point is reached. This is the dominant dynamic range limitation of this design.

---

## 6. Dynamic Range

### 6.1 Receiver Dynamic Range Summary

| Parameter | Value | Notes |
|---|---|---|
| System NF | ~6.4 dB | Friis cascade |
| MDS (10 kHz BW, 10 dB SNR) | **−117.6 dBm** | Calculated |
| LNA input P1dB | **−22 dBm** | Limited by BGA2869 |
| LNA IIP3 | **−9 dBm** | Derived from OIP3 = +23 dBm |
| 1 dB compression range (antenna) | **−117.6 to −22 dBm** | **95.6 dB** |
| 3rd-order SFDR (10 kHz BW) | **~78 dBc** | Calculated |

### 6.2 Spurious-Free Dynamic Range (SFDR) Calculation

SFDR is the signal range over which useful signals can be distinguished from intermodulation distortions:

```
SFDR (dBc) = (2/3) × (IIP3 − MDS_1Hz)
MDS_1Hz = −174 + NF_sys = −174 + 6.4 = −167.6 dBm/Hz
IIP3_sys ≈ IIP3_LNA + filter_loss = −9 + (−3) = −12 dBm  (pre-LNA path limits sys IIP3)
SFDR_1Hz = (2/3) × (−12 − (−167.6)) = (2/3) × 155.6 = 103.7 dBc
In 10 kHz bandwidth: SFDR_10kHz = 103.7 − (2/3) × 40 = 103.7 − 26.7 ≈ 77 dBc
```

**System SFDR ≈ 77 dBc at 10 kHz bandwidth**

### 6.3 Large-Signal Behaviour and AGC

The design has no automatic gain control (AGC). With 108 dB total gain:

| Antenna signal | Expected PAM8406 output |
|---|---|
| −120 dBm (−128 dBm noise threshold) | ~8 mW → very quiet |
| −110 dBm | ~178 mW → audible |
| −90 dBm | ~1.8 W → loud |
| **−82 dBm** | **~3 W → PAM8406 at full power (clipping threshold)** |
| −60 dBm | LNA begins compressing, distorted output |
| −50 dBm | LNA at hard compression |

> With a strong local station (−50 to −30 dBm typical) the BGA2869 will be overdriven. A switchable RF attenuator before the LNA input (even a simple π resistive pad) or a reduction in IF LNA gain (U801 bypassed, or gain-stepped via an additional attenuator) would extend the useful operating range. Alternatively, I²C-programmable LO amplitude on the MS5351M could vary mixer conversion gain.

---

## 7. Image Frequency Analysis

### 7.1 First Conversion (RF → 10.7 MHz)

For a local oscillator CLK0 above the received frequency (high-side injection):

```
CLK0 = f_RF + f_IF1 = f_RF + 10.7 MHz
Image = f_RF + 2 × f_IF1 = f_RF + 21.4 MHz
```

| Receive freq | CLK0 | Image frequency |
|---|---|---|
| 531 kHz (MW low) | 11.231 MHz | 21.931 MHz |
| 1000 kHz (MW) | 11.700 MHz | 22.400 MHz |
| 1602 kHz (MW high) | 12.302 MHz | 22.902 MHz |
| 7.1 MHz (40m SW) | 17.800 MHz | 28.500 MHz |
| 14.0 MHz (20m SW) | 24.700 MHz | 35.400 MHz |

**Image rejection:** Provided by the RF preselector filter. With an estimated 500 kHz–2 MHz 3 dB bandwidth centred at the receive frequency, the image (21+ MHz away) is rejected by ≥40–60 dB. This is a significant advantage of the high first IF (10.7 MHz).

### 7.2 Second Conversion (10.7 MHz → 455 kHz)

```
CLK1 = 10.7 ± 0.455 MHz
Image = 10.7 ± 2 × 0.455 = 11.610 MHz or 9.790 MHz
```

**Image rejection:** Provided by the 10.7 MHz BPF (L601) with 280 kHz bandwidth. At the image frequency (±910 kHz from centre), the filter provides ≥30–40 dB attenuation.

### 7.3 Third Conversion (455 kHz → audio)

For synchronous AM detection (CLK2 = 455.000 kHz exactly):
- Product detector mode: AM audio is demodulated coherently
- Image: audio image at 2 × 455 kHz = 910 kHz above the audio, well above audio band — no issue
- The 455 kHz BPF (L901, ~10 kHz BW) suppresses out-of-band noise and adjacent channels before this stage

For slightly offset CLK2 (beat reception):
- CLK2 = 455 kHz ± f_beat: produces an audible carrier tone on CW and SSB signals

### 7.4 Half-IF Spur

The half-IF spurious response is a known weakness of superheterodyne designs:

```
Half-IF spur = f_RF ± f_IF1 / 2 = f_RF ± 5.35 MHz
```

At 1 MHz receive: half-IF image is at 1 ± 5.35 MHz = 6.35 MHz or −4.35 MHz. The RF preselector provides some rejection here, but this frequency is within the preselector passband if it is tuned broadly. The LT5560's specified half-IF rejection is:
- 2RF−2LO product: **−68 dBc** (at 450/70 MHz) → very good
- 3RF−3LO product: **−79 dBc** → excellent

---

## 8. Local Oscillator Requirements

The MS5351M (U201) must be programmed with three frequencies:

| Output | Net | Formula | Example: 1 MHz RF |
|---|---|---|---|
| CLK0 | CLK0 → U501 LO | f_RF + 10.7 MHz | **11.700 MHz** |
| CLK1 | CLK1 → U701 LO | 10.7 ± 0.455 MHz | **11.155 MHz** (or 10.245 MHz) |
| CLK2 | CLK2 → U1001 LO | 455.000 kHz | **455.000 kHz** |

CLK1 injection side:
- **High-side (CLK1 = 11.155 MHz):** Image at 9.790 MHz — well outside 10.7 MHz BPF passband (280 kHz)
- **Low-side (CLK1 = 10.245 MHz):** Image at 11.610 MHz — also outside passband
Both are valid; high-side injection avoids overlap with 10 MHz WWV/WWVH time signals.

**CLK2 at 455 kHz:** The MS5351M is specified down to 2.5 kHz, so 455 kHz is well within range. The CMOS output at 455 kHz is a square wave; the fundamental component at −2 dBm is extracted by the L1001 (47 nH) + C1001 (1 nF) LC network.

> **Phase noise:** The 26 MHz TCXO (X201, KDS 1XTW26000MAA) provides a stable reference. The MS5351M's phase noise is multiplied by 20·log10(N) at the output, where N is the PLL multiplication ratio. At CLK0 = 11.7 MHz (N ≈ 0.45), phase noise actually improves relative to the TCXO reference.

---

## 9. Audio Output Stage

### 9.1 Signal Path from 455 kHz IF to Speaker

| Stage | Input | Gain | Output | Notes |
|---|---|---|---|---|
| LT5560 U1001 AM detector | 455 kHz IF | +5 dB est. | Audio (diff.) | Carrier × IF → audio |
| R1002, R1003 (200 Ω) | — | Load | — | Pull-ups to +5 V |
| C1105, C1106 (4.7 µF) | Audio | AC-coupled | — | HPF: f_c = 1/(2π × 4.7µF × 200Ω) ≈ 170 Hz |
| LT6202 U1101 | Diff. audio | +28.1 dB | Single-ended | Diff-to-SE conversion |
| R1104 (51 Ω), C1206 (100 nF) | — | Low-pass | — | f_c = 1/(2π × 51Ω × 100nF) ≈ 31 kHz |
| PAM8406 U1201 | Single-ended | +24 dB | BTL to speaker | Class-D, 4 Ω load |

### 9.2 Audio Bandwidth

| Stage | High-pass fc | Low-pass fc | Notes |
|---|---|---|---|
| C1105/C1106 + 200 Ω | ~170 Hz | — | Low-end roll-off at mixer output |
| LT6202 at ×25.5 | ~10 Hz (DC-coupled) | ~4 MHz (GBW/25.5) | Wide passband |
| R1104 + C1206 (51 Ω + 100 nF) | — | ~31 kHz | Anti-alias / RF bypass |
| 455 kHz BPF L901 | — | ~±5 kHz from 455 kHz | Final IF selectivity |
| **Overall audio response** | **~170 Hz** | **~5 kHz** | Limited by 455 kHz BPF BW |

> The AM audio bandwidth is primarily limited by the 455 kHz BPF (L901). With an estimated ~10 kHz 3 dB bandwidth, the audio extends to approximately **5 kHz**. This is standard for AM broadcast audio quality. The 170 Hz low-frequency roll-off (from C1105/C1106 and R1103 = 200 Ω) removes audio below ~170 Hz, which is acceptable for voice and music (though bass reproduction is reduced).

To improve low-frequency audio response, C1105/C1106 could be increased from 4.7 µF (giving fc ≈ 170 Hz) to 47 µF (fc ≈ 17 Hz), noting that larger capacitors may cause pop-on-enable at the op-amp input.

### 9.3 Maximum Audio Output

At full drive (3 W into 4 Ω), with an antenna signal ≥ −82 dBm:

| Metric | Value |
|---|---|
| Output power (10% THD) | **3 W** |
| Output voltage (BTL into 4 Ω) | 3.46 Vrms differential |
| SPL estimate @ 1 m (87 dB/W/m speaker) | **~92 dB SPL** |
| Efficiency (Class-D, 5 V) | ~85–90 % |

---

## 10. Noise Floor and SNR at Key Points

Using the system NF analysis, the noise floor at each key point:

| Point | Equivalent noise power | In 10 kHz BW |
|---|---|---|
| Antenna input (thermal) | −174 dBm/Hz | −134 dBm in 10 kHz |
| Antenna input (with NF = 6.4 dB) | −167.6 dBm/Hz | −127.6 dBm in 10 kHz |
| After LNA (−51 dBm signal at −80 dBm input) | +29 dB × noise | Noise: −96 dBm in 10 kHz |
| At 455 kHz BPF output | — | SNR reduced by insertion losses |
| At op-amp output | — | SNR ≈ (antenna SNR − NF − filter losses) |

For an antenna signal of −110 dBm with 10 kHz BW:
- Signal-to-noise ratio: −110 − (−127.6) = **+17.6 dB** → adequate audio quality

---

## 11. Important Caveats and Limitations

### 11.1 Balun Frequency Range
The WBC1-1TLC is specified for 250–750 MHz. Its use at 455 kHz and at AM RF frequencies (0.5–30 MHz) is outside specification. Performance at these frequencies depends on the actual primary inductance (minimum 9.5 µH; may be significantly higher in practice). If actual Lp ≈ 20 µH, the 455 kHz insertion loss improves from −2.7 dB to approximately −0.9 dB.

**Practical impact:** The IF LNA (U801, +32 dB) more than compensates for any additional balun loss at 455 kHz. The signal chain remains functional but with some additional noise figure degradation, which is second-order given the large IF LNA gain.

### 11.2 IF Filter Insertion Losses
Exact IL for HCCF3-10.700-F280IL03A5L and HCCF2-455.000-LTWCDL were not available in the manufacturers' published datasheet. Values of −4 dB and −5 dB are used as conservative estimates. Actual IL may be 1–2 dB lower (better), improving sensitivity slightly.

### 11.3 No AGC
The design has no automatic gain control. With 108 dB total gain, strong signals (> −82 dBm) clip the PAM8406, and very strong signals (> −50 dBm) compress the BGA2869 LNA. A practical installation may benefit from:
- A front-end attenuator (switchable 10–20 dB π-pad before the LNA)
- Bypassing U801 (IF LNA) on strong-signal bands
- I²C control of PAM8406 gain if an external resistor is fitted for gain reduction

### 11.4 LO Drive Level
The MS5351M outputs a full 3.3 V CMOS swing. The calculated fundamental power before the LO matching network is ~+17 dBm into 50 Ω, far above the LT5560's +1 dBm maximum. The L501/C506 (and L701/C706, L1001/C1007) networks must attenuate this adequately. An error in LO matching can degrade IIP3 or permanently damage the mixer.

### 11.5 Conversion Gain at Non-Characterised Frequencies
The LT5560 downconverting conversion gain of +2.6 dB is from the 450→70 MHz test point. At the actual operating frequencies (RF band → 10.7 MHz, 10.7 MHz → 455 kHz), the device operates outside its characterised range but within the specified 0.01–4000 MHz. Small deviations from +2.6 dB are expected; practical measurements may show ±1–2 dB variation.

---

## 12. Summary

| Parameter | Value |
|---|---|
| System noise figure | **~6.4 dB** |
| Minimum detectable signal (10 kHz BW, 10 dB SNR) | **−117.6 dBm (≈ 0.30 µVrms)** |
| Practical sensitivity (20 dB SNR for good audio) | **−107.6 dBm (≈ 0.93 µVrms)** |
| Total system gain (antenna → speaker) | **~108 dB** |
| 1 dB compression (antenna input) | **−22 dBm** (limited by BGA2869 IIP1dB) |
| SFDR (10 kHz BW) | **~77 dBc** |
| Full-power antenna threshold | **−82 dBm** (PAM8406 clipping) |
| Maximum audio output | **3 W into 4 Ω** (10% THD) |
| Audio bandwidth | **~170 Hz – 5 kHz** |
| Image rejection (1st conversion) | **≥ 40 dB** (RF preselector) |
| Image rejection (2nd conversion) | **≥ 30 dB** (10.7 MHz BPF) |

> Values derived from component datasheets, Friis cascade calculations, and engineering estimates. Actual performance may differ by ±2–5 dB depending on PCB layout, component tolerances, filter characteristics, and LO matching. Measurements on the assembled board are recommended to verify the signal budget and identify any layout-related degradation.
