# Power Budget and Thermal Analysis — AM Airband Receiver V2

The power supply architecture is identical to V1: three regulated rails from a single DC input. The main difference is the signal chain — V2 uses two AD8342 mixers instead of three LT5560 mixers, with no second LNA. The AD8342 draws more current per device (~55 mA) than the LT5560 (~11.5 mA), but the reduced device count results in a similar total +5 V load.

---

## Power Rails

| Rail | Regulator | Package | Max current | Load in V2 |
|---|---|---|---|---|
| +3.3 V | AMS1117-3.3 (U101) | SOT-223 | 800 mA | MS5351M, TCXO, I²C pull-ups |
| +5 V | AMS1117-5.0 (U103) | SOT-223 | 800 mA | AD8342 ×2, BGA2869 (optional), LT6202 |
| +5V_P_AMP | AMS1084CM-5.0 (U102) | TO-263 | 5 A | PAM8406 audio amplifier only |

---

## Current Budget

### +3.3 V Rail

Unchanged from V1.

| Component | Min (mA) | Typ (mA) | Max (mA) | Source |
|---|---|---|---|---|
| MS5351M U201 (CLK0 + CLK1 active) | 23 | 28 | 33 | MS5351M datasheet (2 of 3 CLK outputs active) |
| TCXO 26 MHz X201 | 1.5 | 2.0 | 3.0 | KDS 1XTW26000MAA datasheet |
| I²C pull-ups R201+R202 (2 kΩ) | 0 | 1.7 | 3.3 | Calculated: 3.3 V / 2 kΩ |
| **Total +3.3 V** | **24.5** | **31.7** | **39.3** | |

**Margin:** 39.3 mA vs 800 mA limit = **95% headroom** ✓

### +5 V Rail (signal chain)

Three AD8342 mixers in V2 final architecture. Two scenarios: without and with optional BGA2869 LNA.

#### Without LNA

| Component | Min (mA) | Typ (mA) | Max (mA) | Source |
|---|---|---|---|---|
| AD8342 RF Mixer U501 | 50 | 55 | 60 | AD8342 datasheet (5 V supply) |
| AD8342 2nd IF Mixer U701 | 50 | 55 | 60 | AD8342 datasheet |
| AD8342 Product Detector U1001 | 50 | 55 | 60 | AD8342 datasheet |
| LT6202 op-amp U1101 | 12 | 14 | 16 | LT6202 datasheet |
| LM393 comparator U1151 | 0.5 | 1.0 | 2.0 | LM393 datasheet |
| **Total (no LNA)** | **162.5** | **180** | **198** | |

**Margin:** 198 mA vs 800 mA limit = **75% headroom** ✓

#### With Optional BGA2869 LNA

| Component | Min (mA) | Typ (mA) | Max (mA) | Source |
|---|---|---|---|---|
| AD8342 RF Mixer U501 | 50 | 55 | 60 | AD8342 datasheet |
| BGA2869 LNA U401 | 21.8 | 24.0 | 26.0 | BGA2869 datasheet, Table 6 |
| AD8342 2nd IF Mixer U701 | 50 | 55 | 60 | AD8342 datasheet |
| AD8342 Product Detector U1001 | 50 | 55 | 60 | AD8342 datasheet |
| LT6202 op-amp U1101 | 12 | 14 | 16 | LT6202 datasheet |
| LM393 comparator U1151 | 0.5 | 1.0 | 2.0 | LM393 datasheet |
| **Total (with LNA)** | **184.3** | **204** | **224** | |

**Margin:** 224 mA vs 800 mA limit = **72% headroom** ✓

### +5V_P_AMP Rail (audio amplifier)

Identical to V1 — PAM8406 is unchanged.

| Condition | Current |
|---|---|
| Quiescent (no audio) | 8–12 mA |
| Typical listening level (~25% volume) | ~100–200 mA |
| Full output (2×3 W into 4 Ω) | ~900 mA |

**Margin:** 900 mA vs 5 A AMS1084CM limit = **82% headroom at full power** ✓

---

## Total Input Power

At 9 V DC input and typical loads (with LNA, moderate audio level):

| Calculation | Value |
|---|---|
| +3.3 V rail: 9 V × 32 mA | 0.29 W |
| +5 V rail: 9 V × 148 mA | 1.33 W |
| +5V_P_AMP rail: 9 V × 150 mA (typical listening) | 1.35 W |
| **Estimated total input power** | **~2.97 W** |

(Efficiency of linear regulators reduces delivered power to loads; remainder is heat — see thermal section.)

---

## Comparison with V1

| Metric | V1 | V2 (no LNA) | V2 (with LNA) |
|---|---|---|---|
| +3.3 V typ | 33.7 mA | 31.7 mA | 31.7 mA |
| +5 V typ | 96.5 mA | 180 mA | 204 mA |
| +5V_P_AMP | same | same | same |

V2 now uses three AD8342 mixers — identical count to V1's three LT5560 mixers, but higher current per device (~55 mA vs ~11.5 mA). The absence of baluns, a second LNA, and a second IF amplifier keeps total current at a similar level. Both designs are well within the AMS1117-5.0 800 mA limit.

---

## Thermal Analysis

### AMS1117-3.3 and AMS1117-5.0 (SOT-223)

| Regulator | Vdrop (at 9 V in) | Typ current | Power dissipated |
|---|---|---|---|
| AMS1117-3.3 U101 | 5.7 V | 32 mA | 0.18 W |
| AMS1117-5.0 U103 | 4.0 V | 204 mA (with LNA) | 0.82 W |

SOT-223 θJA ≈ 50°C/W on a standard PCB. At 0.82 W, junction rise above ambient ≈ 41°C — within the 125°C maximum.

At 12 V input with 204 mA load, AMS1117-5.0 dissipates (12 − 5) × 0.204 = 1.43 W → junction rise ≈ 71°C. Still within limits but warm — **prefer 7–9 V input voltage**.

**Tab pad note (identical to V1):** SOT-223 tab = Vout on both AMS1117 variants. Isolated copper polygons required on their respective output nets.

### AMS1084CM-5.0 (TO-263)

Identical analysis to V1. The PAM8406 load is unchanged.

**Power dissipation formula:**
```
P = (VCC − 5.0 V) × I_load
```

| VCC | Load current | Power dissipated | Required Cu area (1 layer) | Required Cu area (2 layers) |
|---|---|---|---|---|
| 7 V | 200 mA | 0.4 W | ~2 cm² | ~1 cm² |
| 9 V | 200 mA | 0.8 W | ~4 cm² | ~2 cm² |
| 9 V | 400 mA | 1.6 W | ~7 cm² | ~4 cm² |
| 12 V | 400 mA | 2.8 W | ~12 cm² | ~6 cm² |

**Practical recommendation:** At 9 V input and typical listening levels, plan for **4–8 cm²** of copper for U102 (top + bottom layers combined, ≥8 vias). This matches V1 guidance.

**Tab pad note:** AMS1084CM-5.0 tab = +5V_P_AMP. Isolated polygon — do NOT connect to GND.

### AD8342 Mixers (TSSOP-16)

Each AD8342 dissipates approximately:
```
P = 5 V × 55 mA = 0.275 W per device
```

TSSOP-16 θJA ≈ 90–110°C/W (junction to ambient, still air). At 0.275 W:
```
ΔTJ ≈ 0.275 × 100 = 27.5°C above ambient
```

Junction temperature at 25°C ambient ≈ 52°C — well below 150°C maximum. No special thermal provisions required beyond standard 0.1 mm pour on GND pins and adequate power supply decoupling.

---

## Input Voltage Recommendation

| VCC | AMS1084 at 400 mA | AMS1117-5.0 at 148 mA | AMS1117-3.3 at 32 mA | Comment |
|---|---|---|---|---|
| 6 V | 0.4 W | 0.15 W | 0.06 W | Minimal heat — good for battery |
| 7 V | 0.8 W | 0.30 W | 0.10 W | Good all-around choice |
| 9 V | 1.6 W | 0.59 W | 0.19 W | Acceptable — AMS1084 needs ~6 cm² Cu |
| 12 V | 2.8 W | 1.04 W | 0.29 W | AMS1117-5.0 at thermal limit — not recommended |

**Recommended input voltage: 7–9 V DC.** Same as V1.
