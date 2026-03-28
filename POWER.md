# Power Budget and Thermal Analysis

## Power Rails

The design uses three regulated DC rails derived from a single input supply (VCC, 6–15 V):

| Rail | Regulator | Package | Max current | Load |
|---|---|---|---|---|
| +3.3 V | AMS1117-3.3 (U101) | SOT-223 | 800 mA | Clock generator, TCXO |
| +5 V | AMS1117-5.0 (U104) | SOT-223 | 800 mA | Signal chain |
| +5V_P_AMP | AMS1084CM-5.0 (U102) | TO-263 | 5 A | Audio amplifier only |

---

## Current Budget

### +3.3 V Rail

| Component | Min (mA) | Typ (mA) | Max (mA) | Source |
|---|---|---|---|---|
| MS5351M U201 (all CLK active) | 25 | 30 | 35 | MS5351M datasheet |
| TCXO 26 MHz X201 | 1.5 | 2.0 | 3.0 | KDS 1XTW26000MAA datasheet |
| I²C pull-ups R201+R202 (2 kΩ) | 0 | 1.7 | 3.3 | Calculated: 3.3 V / 2 kΩ |
| **Total +3.3 V** | **26.5** | **33.7** | **41.3** | |

**Margin:** 41.3 mA vs 800 mA limit = **95% headroom** ✓

### +5 V Rail (signal chain)

| Component | Min (mA) | Typ (mA) | Max (mA) | Source |
|---|---|---|---|---|
| BGA2869 RF LNA U401 | 21.8 | 24.0 | 26.0 | BGA2869 datasheet, Table 6 |
| LT5560 RF mixer U501 (ICC + VCC bias) | 10.5 | 11.5 | 13.5 | LT5560 datasheet |
| LT5560 IF mixer U701 (ICC + VCC bias) | 10.5 | 11.5 | 13.5 | LT5560 datasheet |
| BGA2869 IF LNA U801 | 21.8 | 24.0 | 26.0 | BGA2869 datasheet, Table 6 |
| LT5560 455 kHz mixer U1001 (ICC + VCC bias) | 10.5 | 11.5 | 13.5 | LT5560 datasheet |
| LT6202 op-amp U1101 | 12.0 | 14.0 | 16.0 | LT6202 datasheet |
| **Total +5 V** | **87.1** | **96.5** | **108.5** | |

**Margin:** 108.5 mA vs 800 mA limit = **86% headroom** ✓

### +5V_P_AMP Rail (audio amplifier)

| Condition | Current |
|---|---|
| Quiescent (no audio) | 8–12 mA |
| Typical listening level (~25% volume) | ~100–200 mA |
| Full output (2×3 W into 4 Ω) | ~900 mA |

**Margin:** 900 mA vs 5 A AMS1084CM limit = **82% headroom at full power** ✓

---

## LT5560 Supply Voltage Note

All three LT5560 mixers receive +5 V. The absolute maximum supply voltage for the LT5560 is **5.3 V**. The AMS1117-5.0 output is typically 5.0 V ±2%, giving a range of approximately 4.9–5.1 V under load. This is within specification under normal conditions.

During cold startup or transient conditions, verify that the +5 V rail does not exceed 5.3 V. The AMS1117 output voltage can be slightly elevated at no load before regulation stabilises.

---

## Thermal Analysis

### AMS1117-3.3 and AMS1117-5.0 (SOT-223)

Both AMS1117 regulators dissipate modest power:

| Regulator | Vdrop (at 12 V in) | Typ current | Power dissipated |
|---|---|---|---|
| AMS1117-3.3 U101 | 8.7 V | 34 mA | 0.30 W |
| AMS1117-5.0 U104 | 7.0 V | 97 mA | 0.68 W |

The SOT-223 package has θJA ≈ 50°C/W on a standard PCB. At 0.68 W, the junction temperature rise above ambient is approximately 34°C — well within the 125°C maximum.

**Tab pad note:** The SOT-223 tab pad is connected to **Vout** on both AMS1117 variants — not GND. Each requires an isolated copper polygon on its output net. This also serves as a useful supply distribution pour.

### AMS1084CM-5.0 (TO-263)

The AMS1084 handles the audio amplifier load and can see significant power dissipation depending on input voltage and audio level.

**Power dissipation formula:**

```
P = (VCC − 5.0 V) × I_load
```

**Reference values at 12 V input:**

| Load current | Power dissipated | Required Cu area (1 layer, 1oz) | Required Cu area (2 layers) |
|---|---|---|---|
| 100 mA | 0.7 W | ~3 cm² | ~2 cm² |
| 200 mA | 1.4 W | ~6 cm² | ~3 cm² |
| 400 mA | 2.8 W | ~12 cm² | ~6 cm² |
| 600 mA | 4.2 W | ~18 cm² | ~9 cm² |
| 900 mA | 6.3 W | ~27 cm² | ~14 cm² |

*Based on TO-263 θJC = 5°C/W, 1oz copper θCA ≈ 40°C·cm²/W, Tj max = 125°C, Tamb = 25°C.*

**Practical recommendation:** At typical listening levels (~200–400 mA average), plan for **8–12 cm²** of copper at the AMS1084 (top + bottom layer combined, connected by ≥8 vias). This covers most usage scenarios with comfortable margin.

**Tab pad note:** The TO-263 tab pad is connected to **+5V_P_AMP** — not GND. The thermal relief polygon must be on the +5V_P_AMP net and electrically isolated from the GND plane.

**PCB layout guidelines for U102:**
- Place a solid copper polygon on both top and bottom layers under and around U102
- Connect the two layers with at least 8 vias (≥0.3 mm drill, ≥0.6 mm annular ring)
- Extend the polygon at least 2–3 mm beyond the package on all sides
- Do not use thermal relief spokes on the tab pad connection — use solid fill

### Thermal comparison: linear vs switching regulator

If VCC is consistently 12 V and audio output is frequently high, consider replacing U102 with a buck switching regulator (e.g. MP2315, LM2596, or similar 5 V/3 A step-down). A switching regulator at 90% efficiency dissipates only ~50–100 mW at 900 mA output, compared to 6.3 W for the AMS1084. This eliminates the thermal constraint entirely.

---

## Input Voltage Recommendation

| VCC | AMS1084 at 400 mA | AMS1117-5.0 at 97 mA | AMS1117-3.3 at 34 mA | Comment |
|---|---|---|---|---|
| 6 V | 0.4 W | 0.10 W | 0.09 W | Minimal heat — ideal for battery operation |
| 7 V | 0.8 W | 0.19 W | 0.13 W | Good all-around choice |
| 9 V | 1.6 W | 0.39 W | 0.20 W | Acceptable — AMS1084 needs ~6 cm² Cu |
| 12 V | 2.8 W | 0.68 W | 0.30 W | Manageable — AMS1084 needs ~12 cm² Cu |
| 15 V | 4.0 W | 0.97 W | 0.41 W | High heat — consider a buck regulator |

**Recommended input voltage: 7–9 V** for linear regulator operation without significant thermal concerns.
