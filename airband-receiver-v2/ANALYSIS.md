# Signal Chain Analysis — AM Airband Receiver V2

Complete signal level, noise figure, and SNR analysis for the 118–137 MHz double-conversion superheterodyne. Three AD8342 mixers, 10.7 MHz first IF, 455 kHz second IF, synchronous AM product detection.

---

## Assumptions and Reference Conditions

| Parameter | Value | Notes |
|---|---|---|
| Receive frequency | 127.5 MHz | Airband midpoint |
| First IF | 10.7 MHz | Image rejection stage |
| Second IF | 455 kHz | Channel selectivity stage |
| LO injection | Low-side (CLK0 = RF − IF) | CLK0 range: 107.3–126.3 MHz |
| Channel bandwidth | 8 kHz | AM voice, two-sided |
| Noise bandwidth (audio) | 4 kHz | One-sided baseband after detection |
| Thermal noise floor (kTB, 8 kHz) | −135 dBm | −174 dBm/Hz + 10·log₁₀(8000) |
| Target SNR for intelligible voice | ≥ 10 dB | Minimum; ≥ 20 dB preferred |
| AD8342 single-ended derating | −3 dB gain, +3 dB NF | Applied vs. differential spec |

---

## Component Specifications

| Stage | Component | Gain (dB) | NF (dB) | Notes |
|---|---|---|---|---|
| RF BPF | LC pi, 3-pole | −2 | 2 | Estimated IL; verify on assembled board |
| LNA (optional) | BGA2869 at ~130 MHz | +32 | 3.1 | Datasheet spec (950 MHz); gain at 130 MHz typically ≥32 dB |
| AD8342 Mixer 1 (RF→10.7 MHz) | Single-ended, 5 V | 0 | 13 | +3.5 dB diff − 3 dB SE derating; NF 10 dB + 3 dB SE |
| 10.7 MHz BPF | HCCF3 ceramic | −4 | 4 | Typical IL; measure on board |
| AD8342 Mixer 2 (10.7→455 kHz) | Single-ended, 5 V | 0 | 13 | Same SE conditions |
| 455 kHz BPF | HCCF2 ceramic | −5 | 5 | Typical IL for HCCF2; slightly higher than HCCF3 |
| AD8342 Mixer 3 (product det.) | Single-ended, 5 V | 0 | 13 | Same SE conditions |
| LT6202 | Audio amp, 28 dB | +28 | ~12 | Not dominant at this stage |

---

## Cascaded Noise Figure — Friis Analysis

Friis formula:
```
F_total = F₁ + (F₂−1)/G₁ + (F₃−1)/(G₁G₂) + …
```

Linear noise factor F = 10^(NF_dB/10), linear gain G = 10^(gain_dB/10).

### Scenario 1: No LNA

Chain: **RF BPF → Mixer 1 → 10.7 MHz BPF → Mixer 2 → 455 kHz BPF → PD Mixer → LT6202**

| Stage | NF (dB) | F | Gain (dB) | G | Cumul. G | (Fₙ−1)/G_prev |
|---|---|---|---|---|---|---|
| RF BPF | 2 | 1.585 | −2 | 0.631 | 0.631 | — |
| AD8342 Mixer 1 | 13 | 19.95 | 0 | 1.000 | 0.631 | 30.0 |
| 10.7 MHz BPF | 4 | 2.512 | −4 | 0.398 | 0.251 | 2.40 |
| AD8342 Mixer 2 | 13 | 19.95 | 0 | 1.000 | 0.251 | 75.5 |
| 455 kHz BPF | 5 | 3.162 | −5 | 0.316 | 0.0794 | 8.56 |
| AD8342 PD | 13 | 19.95 | 0 | 1.000 | 0.0794 | 238.5 |
| LT6202 | 12 | 15.85 | +28 | 631 | 50.1 | 188.1 |

```
F_total = 1.585 + 30.0 + 2.40 + 75.5 + 8.56 + 238.5 + 188.1 = 544.6
NF_system = 10 · log₁₀(544.6) = 27.4 dB
```

The product detector (Mixer 3) dominates because the cumulative gain at that point is only −11 dB (0.631 × 1 × 0.398 × 1 × 0.316 = 0.0794). This is the fundamental sensitivity limitation without an LNA.

**MDS (10 dB SNR):**
```
Noise floor = −135 + 27.4 = −107.6 dBm
MDS = −107.6 + 10 = −97.6 dBm ≈ −98 dBm
```

---

### Scenario 2: With BGA2869 LNA

Chain: **RF BPF → BGA2869 → Mixer 1 → 10.7 MHz BPF → Mixer 2 → 455 kHz BPF → PD → LT6202**

| Stage | NF (dB) | F | Gain (dB) | G | Cumul. G | (Fₙ−1)/G_prev |
|---|---|---|---|---|---|---|
| RF BPF | 2 | 1.585 | −2 | 0.631 | 0.631 | — |
| BGA2869 LNA | 3.1 | 2.042 | +32 | 1585 | 1000 | 1.65 |
| AD8342 Mixer 1 | 13 | 19.95 | 0 | 1.000 | 1000 | 0.019 |
| 10.7 MHz BPF | 4 | 2.512 | −4 | 0.398 | 398 | 0.0015 |
| AD8342 Mixer 2 | 13 | 19.95 | 0 | 1.000 | 398 | 0.047 |
| 455 kHz BPF | 5 | 3.162 | −5 | 0.316 | 125.9 | 0.0054 |
| AD8342 PD | 13 | 19.95 | 0 | 1.000 | 125.9 | 0.150 |
| LT6202 | 12 | 15.85 | +28 | 631 | 79432 | 0.119 |

```
F_total = 1.585 + 1.65 + 0.019 + 0.0015 + 0.047 + 0.0054 + 0.150 + 0.119 = 3.577
NF_system = 10 · log₁₀(3.577) = 5.5 dB
```

**MDS:**
```
Noise floor = −135 + 5.5 = −129.5 dBm
MDS = −129.5 + 10 = −119.5 dBm ≈ −120 dBm
```

---

## Sensitivity Summary

| Configuration | System NF | Noise floor (8 kHz) | MDS (10 dB SNR) |
|---|---|---|---|
| No LNA | 27.4 dB | −107.6 dBm | **−97.6 dBm** |
| With BGA2869 LNA | 5.5 dB | −129.5 dBm | **−119.5 dBm** |
| Improvement with LNA | 21.9 dB | 21.9 dB | **+21.9 dB** |

The double-conversion chain adds ~5 dB to the system NF compared to the single-conversion V2 design (due to the additional Mixer 2 + 455 kHz filter loss before the product detector). The LNA compensates fully: with LNA the system NF is 5.5 dB in both single- and double-conversion configurations.

---

## Channel Selectivity

| Filter stage | Bandwidth | Adjacent channel rejection (25 kHz) | Adjacent channel rejection (8.33 kHz) |
|---|---|---|---|
| 10.7 MHz BPF (HCCF3) | ~280 kHz | ~0 dB (filter too wide) | ~0 dB |
| 455 kHz BPF (HCCF2) | ~6–8 kHz | **>30 dB** | **~10–15 dB** |
| Audio LP filter (LT6202, 3.1 kHz) | 3.1 kHz | +16 dB additional | marginal |
| **Combined (25 kHz channels)** | | **>46 dB total** | — |
| **Combined (8.33 kHz channels)** | | — | **~25–30 dB total** |

For **25 kHz channel spacing** (standard in most airband operations): adjacent channel rejection >46 dB — fully comparable to commercial monitoring receivers.

For **8.33 kHz spacing** (European IFR): rejection ~25–30 dB — adequate for typical monitoring; adjacent channels will be audible only if very strong relative to the desired signal.

---

## Signal Level Budget

Reference scenario: −90 dBm input signal (weak aircraft, adequate SNR with LNA).

### With LNA

| Point in chain | Signal level (dBm) | Cumulative gain/loss |
|---|---|---|
| Antenna input | −90.0 | reference |
| After RF BPF (−2 dB) | −92.0 | −2 dB |
| After BGA2869 LNA (+32 dB) | −60.0 | +30 dB |
| After AD8342 Mixer 1 (0 dB) | −60.0 | +30 dB |
| After 10.7 MHz BPF (−4 dB) | −64.0 | +26 dB |
| After AD8342 Mixer 2 (0 dB) | −64.0 | +26 dB |
| After 455 kHz BPF (−5 dB) | −69.0 | +21 dB |
| After AD8342 PD (0 dB) | −69.0 | +21 dB |
| After LT6202 (+28 dB) | −41.0 | +49 dB |
| PAM8406 input | −41.0 | — |

At −41 dBm, the PAM8406 (sensitivity ~0.1–0.5 V rms for rated output) receives approximately 20 mV rms (into 50 Ω equivalent) — adequate for clear audio output.

**SNR at audio output:** signal −41 dBm, noise floor −129.5 dBm → SNR = 88.5 dB (limited by system NF of 5.5 dB, not audio stages) — excellent.

### Without LNA

| Point in chain | Signal level (dBm) |
|---|---|
| After RF BPF | −92.0 |
| After Mixer 1 | −92.0 |
| After 10.7 MHz BPF | −96.0 |
| After Mixer 2 | −96.0 |
| After 455 kHz BPF | −101.0 |
| After PD Mixer | −101.0 |
| After LT6202 (+28 dB) | −73.0 |

At −73 dBm the PAM8406 receives approximately 1.6 mV rms — low but potentially audible at maximum volume. A −90 dBm input without LNA gives SNR = −90 − (−107.6) = 17.6 dB — intelligible voice.

---

## Image Rejection Analysis

With **low-side LO injection** (CLK0 = RF − IF):

```
f_image = RF − 2 × IF
```

| Receive freq | CLK0 | Image | BPF rejection |
|---|---|---|---|
| 118.0 MHz | 107.3 MHz | 96.6 MHz | >35 dB (well below FM band bottom) |
| 120.0 MHz | 109.3 MHz | 98.6 MHz | >35 dB (below FM band) |
| 127.5 MHz | 116.8 MHz | 106.1 MHz | ~25 dB (middle of FM band, 107.5 MHz typical) |
| 133.0 MHz | 122.3 MHz | 111.6 MHz | ~20 dB (below airband, quiet) |
| 137.0 MHz | 126.3 MHz | 115.6 MHz | ~8–12 dB (2.4 MHz below filter lower edge) |

**Worst case:** 137 MHz reception — image at 115.6 MHz receives only ~8–12 dB rejection from the 3-pole RF BPF. The 115.6 MHz image falls in a relatively quiet frequency range (between FM broadcast and airband), so practical interference is unlikely. If needed, a 4-pole RF BPF would increase rejection to ~20 dB at this offset.

**Best case:** Channels below 133 MHz have images well into or below the FM band (88–108 MHz), where the RF BPF provides 25–40 dB rejection.

**Comparison with high-side injection:** With high-side injection (CLK0 = RF + IF), images fall at 139–158 MHz — within or just above the airband, with 2–12 dB rejection at the lower end. Low-side injection is clearly superior for this band.

---

## GALI-84+ Assessment

With the double-conversion architecture and BGA2869 LNA:

| Config | System NF | Adding GALI-84+ after 455 kHz filter |
|---|---|---|
| With LNA | 5.5 dB | <0.1 dB improvement |
| Without LNA | 27.4 dB | ~8 dB improvement (moves PD contribution) |

**GALI-84+ verdict: Not needed.** With LNA the system NF of 5.5 dB already gives MDS −120 dBm, which equals or exceeds most commercial monitoring receivers. Without LNA, the BGA2869 is a far better investment (21.9 dB improvement vs. 8 dB for GALI-84+).

---

## Dynamic Range

| Metric | Without LNA | With LNA |
|---|---|---|
| Noise floor (8 kHz) | −107.6 dBm | −129.5 dBm |
| AD8342 IIP3 (single-ended, typ.) | ~+18 dBm | ~+18 dBm at RF input (after LNA) |
| BGA2869 OIP3 (estimated) | — | ~+47 dBm |
| System IIP3 (LNA limits) | — | IIP3 = OIP3_LNA − G_LNA = 47 − 32 = +15 dBm |
| Spurious-free DR (approx.) | ~(2/3)×(18+107.6) = 83 dB | ~(2/3)×(15+129.5) = 96 dB |

These are theoretical estimates. Practical SFDR will be lower due to close-in spurs, LO feedthrough, and IMD products. At a busy airport with many simultaneous strong transmissions, bypass the LNA via JP401 if overload symptoms appear (audio distortion, desensitisation).

---

## Squelch Calibration

The squelch threshold (RV1201) should be set at bring-up:

1. Connect antenna, tune to a known active frequency
2. Verify audio is present with squelch fully open (RV1201 at minimum resistance)
3. Slowly increase threshold until PAM8406 just mutes during transmission gaps
4. Verify the 100 ms tail keeps audio connected through brief pauses in speech

The LM393 open-collector output requires the R1205 pull-up to +5V_P_AMP (not +5V) so that the PAM8406 SHDN# receives the correct supply-referenced logic level.

---

## Bring-Up Measurement Plan

| Test point | Expected (−90 dBm input, with LNA) | Confirms |
|---|---|---|
| TP301 (RF_IN_LNA) | −92 dBm @ 127.5 MHz | RF BPF passing, LNA input present |
| LNA output (probe at U501 RFIN+) | −60 dBm @ 127.5 MHz | LNA gain ≈ 32 dB |
| TP501 (IF1_IN) | −60 dBm @ 10.7 MHz | Mixer 1 converting correctly |
| TP601 (IF1_OUT) | −64 dBm @ 10.7 MHz | 10.7 MHz filter inserting ~4 dB |
| TP701 (IF2_IN) | −64 dBm @ 455 kHz | Mixer 2 converting correctly |
| TP901 (IF2_OUT) | −69 dBm @ 455 kHz | 455 kHz filter inserting ~5 dB |
| TP1001 (AUDIO_RAW) | Audio envelope ~−69 dBm | Product detector demodulating AM |
| LT6202 output | ~−41 dBm audio | 28 dB gain applied |

**Most common bring-up issue:** No signal at TP501 despite LNA output present → CLK0 frequency wrong in MS5351M. Use I²C header J201 to re-program CLK0 = f_receive − 10.7 MHz. Verify with frequency counter or spectrum analyser on the LO output pad.
