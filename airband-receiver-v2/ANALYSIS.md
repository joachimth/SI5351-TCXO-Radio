# Signal Chain Analysis — AM Airband Receiver V2

Complete signal level, noise figure, and SNR analysis for the 118–137 MHz single-conversion superheterodyne. Used to evaluate whether the GALI-84+ IF gain block is required and under what conditions the BGA2869 LNA is necessary.

---

## Assumptions and Reference Conditions

| Parameter | Value | Notes |
|---|---|---|
| Receive frequency | 127.5 MHz | Airband midpoint |
| IF frequency | 10.7 MHz | Single conversion |
| LO injection | High-side (LO1 = RF + IF) | LO1 range: 128.7–147.7 MHz |
| Channel bandwidth | 8 kHz | AM voice, two-sided |
| Noise bandwidth (audio) | 4 kHz | One-sided baseband after detection |
| Reference temperature | 290 K (17°C) | Standard for noise calculations |
| Thermal noise floor (kTB, 8 kHz) | −135 dBm | −174 dBm/Hz + 10·log₁₀(8000) |
| Target SNR for intelligible voice | ≥ 10 dB | Minimum; ≥ 20 dB preferred |
| Reference impedance | 50 Ω | Antenna, filter, LNA domains |
| AD8342 single-ended use | −3 dB gain derating | Applied to convert differential spec to SE |

---

## Component Specifications

| Stage | Component | Gain (dB) | NF (dB) | Notes |
|---|---|---|---|---|
| RF BPF | LC pi, 3-pole | −2 | 2 | Estimated IL for 18 nH air-core inductors with ~100 Q; verify on assembled board |
| LNA (optional) | BGA2869 at ~130 MHz | +32 | 3.1 | Datasheet spec is at 950 MHz; gain at 130 MHz typically slightly higher |
| AD8342 Mixer 1 | Single-ended, 5 V | 0 | 13 | Datasheet: +3.5 dB gain, ~10 dB NF (differential); −3 dB SE derating applied to gain; +3 dB applied to NF |
| IF BPF | HCCF3 ceramic, 10.7 MHz | −4 | 4 | HCCF3 series typical IL; measure on assembled board |
| GALI-84+ (optional) | IF gain block, 10.7 MHz | +18 | 3 | Gain and NF from datasheet at 10.7 MHz |
| AD8342 Mixer 2 (PD) | Product detector, SE | 0 | 13 | Same SE conditions as Mixer 1 |
| LT6202 | Audio amp, inverting | +14 | ~12 | Configured for ~14 dB gain; NF not dominant at this stage |

---

## Cascaded Noise Figure — Friis Analysis

Friis formula:
```
F_total = F₁ + (F₂−1)/G₁ + (F₃−1)/(G₁G₂) + (F₄−1)/(G₁G₂G₃) + …
```

All values use linear noise factor (F = 10^(NF_dB/10)) and linear gain (G = 10^(gain_dB/10)).

### Scenario 1: No LNA, No GALI-84+

Chain: **RF BPF → Mixer 1 → IF BPF → PD Mixer → LT6202**

| Stage | NF (dB) | F (linear) | Gain (dB) | G (linear) | Cumulative G | (Fₙ−1)/G_prev |
|---|---|---|---|---|---|---|
| RF BPF | 2 | 1.585 | −2 | 0.631 | 0.631 | — (first stage) |
| AD8342 Mixer 1 | 13 | 19.95 | 0 | 1.000 | 0.631 | (19.95−1)/0.631 = **30.0** |
| IF BPF | 4 | 2.512 | −4 | 0.398 | 0.251 | (2.512−1)/0.631 = **2.40** |
| AD8342 PD | 13 | 19.95 | 0 | 1.000 | 0.251 | (19.95−1)/0.251 = **75.5** |
| LT6202 | 12 | 15.85 | +14 | 25.12 | 6.31 | (15.85−1)/0.251 = **59.2** |

```
F_total = 1.585 + 30.0 + 2.40 + 75.5 + 59.2 = 168.7
NF_system = 10 · log₁₀(168.7) = 22.3 dB
```

The product detector (PD Mixer) dominates because the cumulative gain at that point is only −6 dB (0.631 × 1.0 × 0.398 = 0.251). This is the fundamental weakness of a direct-conversion or low-gain IF chain.

**MDS (Minimum Detectable Signal) at 10 dB SNR:**
```
Noise floor = −135 dBm + 22.3 dB = −112.7 dBm (in 8 kHz BW)
MDS = −112.7 + 10 = −102.7 dBm ≈ −103 dBm
```

---

### Scenario 2: No LNA, With GALI-84+ after IF Filter

Chain: **RF BPF → Mixer 1 → IF BPF → GALI-84+ → PD Mixer → LT6202**

| Stage | NF (dB) | F | Gain (dB) | G | Cumul. G | (Fₙ−1)/G_prev |
|---|---|---|---|---|---|---|
| RF BPF | 2 | 1.585 | −2 | 0.631 | 0.631 | — |
| AD8342 Mixer 1 | 13 | 19.95 | 0 | 1.000 | 0.631 | 30.0 |
| IF BPF | 4 | 2.512 | −4 | 0.398 | 0.251 | 2.40 |
| GALI-84+ | 3 | 2.000 | +18 | 63.10 | 15.84 | (2.0−1)/0.251 = **3.98** |
| AD8342 PD | 13 | 19.95 | 0 | 1.000 | 15.84 | (19.95−1)/15.84 = **1.20** |
| LT6202 | 12 | 15.85 | +14 | 25.12 | 398 | (15.85−1)/15.84 = **0.94** |

```
F_total = 1.585 + 30.0 + 2.40 + 3.98 + 1.20 + 0.94 = 40.1
NF_system = 10 · log₁₀(40.1) = 16.0 dB
```

GALI-84+ provides **6.3 dB improvement** over Scenario 1. The first mixer still dominates the noise figure, but the product detector contribution is reduced from 75.5 to 1.2 (because GALI-84+ raises the cumulative gain from 0.251 to 15.84 before the PD).

**MDS:**
```
Noise floor = −135 + 16.0 = −119 dBm
MDS = −119 + 10 = −109 dBm
```

---

### Scenario 3: With LNA, No GALI-84+

Chain: **RF BPF → BGA2869 LNA → Mixer 1 → IF BPF → PD Mixer → LT6202**

| Stage | NF (dB) | F | Gain (dB) | G | Cumul. G | (Fₙ−1)/G_prev |
|---|---|---|---|---|---|---|
| RF BPF | 2 | 1.585 | −2 | 0.631 | 0.631 | — |
| BGA2869 LNA | 3.1 | 2.042 | +32 | 1585 | 1000 | (2.042−1)/0.631 = **1.65** |
| AD8342 Mixer 1 | 13 | 19.95 | 0 | 1.000 | 1000 | (19.95−1)/1000 = **0.019** |
| IF BPF | 4 | 2.512 | −4 | 0.398 | 398 | (2.512−1)/1000 = **0.0015** |
| AD8342 PD | 13 | 19.95 | 0 | 1.000 | 398 | (19.95−1)/398 = **0.047** |
| LT6202 | 12 | 15.85 | +14 | 25.12 | 10000 | (15.85−1)/398 = **0.037** |

```
F_total = 1.585 + 1.65 + 0.019 + 0.0015 + 0.047 + 0.037 = 3.34
NF_system = 10 · log₁₀(3.34) = 5.2 dB
```

The BGA2869 LNA almost entirely determines the system noise figure. All subsequent stages contribute less than 0.1 dB to the cascade.

**MDS:**
```
Noise floor = −135 + 5.2 = −129.8 dBm ≈ −130 dBm
MDS = −130 + 10 = −120 dBm
```

---

### Scenario 4: With LNA + GALI-84+

Adding GALI-84+ after the IF filter changes the LT6202 and PD contributions by < 0.1 dB. System NF remains 5.2 dB. **GALI-84+ provides no meaningful benefit when the LNA is installed.**

---

## Sensitivity Summary

| Configuration | System NF | Noise floor (8 kHz) | MDS (10 dB SNR) | Improvement vs. base |
|---|---|---|---|---|
| No LNA, no GALI | 22.3 dB | −112.7 dBm | **−102.7 dBm** | baseline |
| No LNA, + GALI-84+ | 16.0 dB | −119.0 dBm | **−109.0 dBm** | +6.3 dB |
| + LNA, no GALI | 5.2 dB | −129.8 dBm | **−119.8 dBm** | +17.1 dB |
| + LNA, + GALI-84+ | 5.2 dB | −129.8 dBm | **−119.8 dBm** | +17.1 dB |

---

## Signal Level Budget

Reference scenario: −90 dBm input signal (weak aircraft, adequate SNR in most configurations).

### Without LNA

| Point in chain | Signal level (dBm) | SNR in 8 kHz (dB) | Comment |
|---|---|---|---|
| Antenna input | −90.0 | 45.0 | Reference signal |
| After RF BPF (−2 dB) | −92.0 | 43.0 | Passive filter loss |
| After AD8342 Mixer 1 (0 dB, SE) | −92.0 | ~22.7* | System NF now sets floor |
| After IF BPF (−4 dB) | −96.0 | ~22.7* | Loss, but NF already set |
| After AD8342 PD (0 dB) | −96.0 | ~22.7 | Audio-band output |
| After LT6202 (+14 dB) | −82.0 | ~22.7 | SNR preserved in gain stage |

*SNR limited by system NF of 22.3 dB: SNR = (−90) − (−135 + 22.3) − (10 dB margin) = 22.7 dB.

**Audio output level from LT6202:**
- −82 dBm into 50 Ω equivalent ≈ 5.6 µV rms
- PAM8406 input sensitivity for rated output (~0.5 V rms): additional ~39 dB gain required
- **LT6202 gain should be increased to ~28 dB** (matching V1's 25.5× / 28 dB configuration), and/or a second gain stage should be considered.

### With LNA

| Point in chain | Signal level (dBm) | SNR in 8 kHz (dB) |
|---|---|---|
| Antenna input | −90.0 | 39.8* |
| After RF BPF (−2 dB) | −92.0 | 39.8 |
| After BGA2869 LNA (+32 dB) | −60.0 | 39.8 |
| After AD8342 Mixer 1 (0 dB) | −60.0 | 39.8 |
| After IF BPF (−4 dB) | −64.0 | 39.8 |
| After AD8342 PD (0 dB) | −64.0 | 39.8 |
| After LT6202 (+28 dB) | −36.0 | 39.8 |

*SNR = (−90) − (−130) = 40 dB (signal well above noise floor).

Audio output at −36 dBm → ~11 mV rms into 50 Ω equivalent. PAM8406 gain (~24 dB) then brings this to ~173 mV rms output — **adequate for clear speaker output** from a moderate antenna.

---

## Audio Gain Recommendation

The LT6202 should be configured for **28 dB gain** (identical to V1: R1102/R1101 = 5.1 kΩ / 200 Ω) rather than the unity-gain starting point in the current HARDWARE.md description. This matches the signal level expected from the AD8342 output to the PAM8406 input sensitivity.

If overdriven on strong signals, reduce R1102 to lower gain. If audio is too quiet with typical airband signals, verify IF filter insertion loss first, then consider the GALI-84+ IF gain block.

---

## GALI-84+ Recommendation

| Scenario | Recommendation |
|---|---|
| **LNA installed** | **Not required.** System NF = 5.2 dB, MDS = −120 dBm. GALI-84+ adds <0.1 dB improvement. |
| **No LNA, good antenna (≥1 m whip)** | **Not required for initial bring-up.** MDS −103 dBm is adequate for aircraft at typical ranges and airport-area ground stations. |
| **No LNA, short antenna (<30 cm)** | **Consider GALI-84+.** The 6.3 dB improvement (MDS −103 → −109 dBm) may make the difference between marginal and reliable reception. |
| **No LNA, very weak signals / long range** | **Use LNA instead.** BGA2869 LNA provides 17 dB improvement vs. 6 dB for GALI-84+. LNA is the better investment. |

**Verdict: Build and test without GALI-84+.** If sensitivity is insufficient after adding the BGA2869 LNA, the chain is working correctly and the issue is the antenna. If sensitivity is insufficient without the LNA and a reasonable antenna is used, add the LNA first. GALI-84+ is a last resort if the LNA causes overload from strong local signals.

---

## Image Rejection Analysis

With high-side LO injection (LO1 = RF + IF), the image frequency is:

```
f_image = LO1 + IF = RF + 2 × IF
```

| Receive freq | LO1 | Image freq | Image offset from BPF edge |
|---|---|---|---|
| 118.0 MHz | 128.7 MHz | 139.4 MHz | +2.4 MHz above 137 MHz top |
| 120.0 MHz | 130.7 MHz | 141.4 MHz | +4.4 MHz above top |
| 127.5 MHz | 138.2 MHz | 148.9 MHz | +11.9 MHz above top |
| 137.0 MHz | 147.7 MHz | 158.4 MHz | +21.4 MHz above top |

**Estimated image rejection vs. frequency offset (3-pole BPF, ~15% BW):**

| Offset from passband edge | Estimated attenuation |
|---|---|
| 2–3 MHz | 6–10 dB |
| 5 MHz | 12–18 dB |
| 10 MHz | 20–28 dB |
| 20 MHz | 30–40 dB |

The worst case is reception of 118–120 MHz channels, where the image at 139–141 MHz receives only ~6–12 dB rejection. Signals in that range (upper airband: 139–158 MHz, VHF-Lo aviation navigation/communications) could appear as interference.

**Practical impact:** In typical use, aircraft transmissions are short and time-separated, so image interference is unlikely to cause persistent problems. For critical applications, consider:

1. **Low-side injection (LO1 = RF − IF):** Images fall at 96.6–115.6 MHz (FM broadcast band ends at ~108 MHz, so worst case is 115.6 MHz, 2.4 MHz below the 118 MHz passband lower edge — similar rejection to the high-side case but images are in a less active frequency range).
2. **Higher-order RF filter (5-pole):** Adds ~10 dB attenuation at 2–3 MHz from the passband edge.
3. **Higher IF (e.g., 21.4 MHz):** Places all images ≥ 42.8 MHz from the receive frequency, giving 35–45 dB rejection from a 3-pole BPF.

For prototype bring-up, high-side injection as specified is acceptable. Note: switching to low-side injection only requires changing the MS5351M CLK0 programming — no hardware changes needed.

---

## Dynamic Range Estimate

### Without LNA

- Noise floor: −112.7 dBm
- 1 dB compression point of AD8342 at RF input: from datasheet, IP1dB ≈ +10 dBm at RF input
- Spurious-free dynamic range: limited by first mixer IIP3 (~+18 dBm single-ended)
- SFDR ≈ (2/3) × (IIP3 − noise floor) = (2/3) × (18 − (−112.7)) = 87 dB (theoretical)

In practice, the AD8342 IIP3 at 127.5 MHz with single-ended drive may be lower. The absence of AGC means strong local transmitters on 118–137 MHz can desensitise or overload the mixer directly.

### With LNA

- BGA2869 OIP3 ≈ +15 + 32 = +47 dBm (estimated)
- BGA2869 output P1dB ≈ +10 dBm
- Adding the LNA improves sensitivity but reduces the strong-signal handling
- If overload from a nearby transmitter is observed: **bypass the LNA via JP401** and test without it

### Strong-signal recommendation

If the receiver is used near an active airfield with strong tower transmissions, consider installing a 3 dB or 6 dB resistive attenuator pad between the RF BPF output and the LNA input. This directly trades sensitivity for linearity without otherwise modifying the chain.

---

## Summary Table

| Metric | No LNA, No GALI | No LNA, + GALI | + LNA, No GALI |
|---|---|---|---|
| System NF | 22.3 dB | 16.0 dB | 5.2 dB |
| MDS (10 dB SNR, 8 kHz) | −103 dBm | −109 dBm | −120 dBm |
| Useful range (aircraft at FL350) | ~150 km | ~300 km | ~600 km |
| GALI-84+ cost/complexity | — | required | not needed |
| Overload risk | low | low | higher — use LNA bypass JP401 if needed |

*Range estimates are approximate, assuming −100 dBm EIRP from aircraft and free-space propagation at 130 MHz with typical airband antenna.*

---

## Bring-Up Measurement Plan

At each test point (TP301, TP501, TP601, TP701), measure with a spectrum analyser or SDR dongle:

| Test point | Expected signal level (−90 dBm input, with LNA) | What a good reading confirms |
|---|---|---|
| TP301 (RF_IN_LNA) | ~−92 dBm at 127.5 MHz | RF BPF working, LNA input present |
| After LNA (no TP, measure at Mixer 1 input) | ~−60 dBm at 127.5 MHz | LNA gain ≈ 32 dB |
| TP501 (IF_IN, before IF filter) | ~−60 dBm at 10.7 MHz + noise | Mixer 1 converting correctly |
| TP601 (IF_OUT, after IF filter) | ~−64 dBm at 10.7 MHz | IF filter inserting ~4 dB |
| TP701 (AUDIO_RAW) | Audio signal, ~−64 dBm equivalent | Product detector demodulating AM |

If the signal at TP501 is absent but LNA output is present, suspect the LO1 frequency setting in the MS5351M (most common bring-up issue).
