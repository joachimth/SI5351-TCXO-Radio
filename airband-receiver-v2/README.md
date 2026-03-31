# AM Airband Receiver V2 — 118–137 MHz

A double-conversion superheterodyne AM receiver designed for the aviation airband (118–137 MHz). Combines the selectivity of V1 with a simplified mixer topology: no baluns, three AD8342 active mixers, and an optional BGA2869 LNA — all on the same three-rail power supply and MS5351M + TCXO clock reference as V1.

**Design philosophy:** Best end result with minimal complexity. No baluns, resistive matching, every stage independently measurable, all three SI5351 CLK outputs put to use.

---

## Overview

```
Antenna (50Ω SMA)
→ RF Bandpass Filter (118–137 MHz, LC, trimmer-tuned)
→ [Optional attenuator pad]
→ [Optional BGA2869 LNA, bypassable]
→ AD8342 Mixer 1   (RF → 10.7 MHz IF)
→ 10.7 MHz Ceramic BPF  (~280 kHz, image rejection)
→ AD8342 Mixer 2   (10.7 MHz → 455 kHz IF)
→ 455 kHz Ceramic BPF   (~6–8 kHz, channel selectivity)
→ AD8342 Mixer 3   (product detector, 455 kHz → audio)
→ Volume control (10 kΩ log pot)
→ LT6202 op-amp    (audio LPF + gain)
→ PAM8406 class-D amp + squelch
→ Speaker
```

All three SI5351 CLK outputs are used:

| CLK output | Frequency | Function |
|---|---|---|
| CLK0 | RF − 10.7 MHz = 107.3–126.3 MHz | LO1 — RF to 10.7 MHz IF (low-side injection) |
| CLK1 | 10.7 MHz − 455 kHz = 10.245 MHz | LO2 — 10.7 MHz to 455 kHz IF |
| CLK2 | 455 kHz (fixed) | LO3 — product detector / AM demodulation |

> **Low-side injection (CLK0 = RF − IF):** Images fall at RF − 2×IF = 96.6–115.6 MHz (FM broadcast band and below), well attenuated by the RF BPF. This is a free improvement over high-side injection with no hardware changes.

---

## Features

- **Target band:** 118–137 MHz AM (aviation VHF airband)
- **First IF:** 10.7 MHz (ceramic BPF, image rejection)
- **Second IF:** 455 kHz (ceramic BPF, ~6–8 kHz channel selectivity)
- **Mixer:** AD8342 active mixer ×3 — low LO drive, no baluns required
- **LNA:** NXP BGA2869 (optional, bypassable via jumper, ~32 dB gain, 3.1 dB NF)
- **AM demodulation:** Synchronous product detector (AD8342 Mixer 3 at 455 kHz)
- **LO source:** MS5351M PLL + 26 MHz TCXO, I²C programmable, all 3 outputs used
- **Volume control:** 10 kΩ log potentiometer
- **Squelch:** LM393 comparator + threshold trimmer → PAM8406 SHDN#
- **Audio output:** PAM8406 class-D stereo amplifier (2×3 W into 4 Ω)
- **Power supply:** Same three-rail architecture as V1 (3.3 V, 5 V, 5V_P_AMP)
- **Input connector:** SMA

---

## Key Differences from V1

| Feature | V1 (SI5351-TCXO-Radio) | V2 (Airband) |
|---|---|---|
| Target frequency | Tunable (preselector trimmer) | 118–137 MHz fixed airband |
| Conversion stages | Triple (10.7 MHz + 455 kHz) | Double (10.7 MHz + 455 kHz) |
| Mixer IC | LT5560 ×3 | AD8342 ×3 |
| Baluns | WBC1-1TLC ×5 | **None** |
| IF amplifier | BGA2869 ×1 at 455 kHz | None (1st LNA at RF input only) |
| AM detection | Synchronous (3rd mixer at 455 kHz) | Synchronous (3rd mixer at 455 kHz) |
| LO outputs used | CLK0, CLK1, CLK2 | CLK0, CLK1, CLK2 |
| Volume control | None | Log potentiometer |
| Squelch | None | LM393 comparator |
| Signal chain ICs | 8 (3×LT5560 + 2×LNA + 5×balun + PLL + TCXO) | 5–6 (3×AD8342 + opt. LNA + PLL + TCXO) |

---

## Block Diagram

```
                +3.3 V                         +5 V              +5V_P_AMP
                  │                              │                    │
           ┌──────┴──────┐                       │                    │
           │  MS5351M    │ CLK0 (107–126 MHz) ──►│                    │
           │  + 26 MHz   │ CLK1 (10.245 MHz)  ──►│                    │
           │  TCXO X201  │ CLK2 (455 kHz)     ──►│                    │
           └─────────────┘                       │                    │
                                                 ▼                    ▼
SMA ──► [RF BPF] ──► [opt. atten.] ──► [opt. BGA2869 LNA] ──► [AD8342 Mixer 1]
                                                                      │ 10.7 MHz IF
                                                             [10.7 MHz ceramic BPF]
                                                                      │
                                                             [AD8342 Mixer 2]
                                                                      │ 455 kHz IF
                                                             [455 kHz ceramic BPF]
                                                                      │
                                                             [AD8342 Mixer 3 / PD]
                                                                      │ audio
                                                             [Volume pot]
                                                                      │
                                                             [LT6202 audio filter/gain]
                                                                      │
                                                             [PAM8406 + squelch]
                                                                      │
                                                                  Speaker
```

---

## Repository Structure

```
airband-receiver-v2/
├── README.md     — this file
├── HARDWARE.md   — stage-by-stage circuit description
├── POWER.md      — power budget and thermal analysis
├── BOM.md        — bill of materials (LCSC part numbers)
└── ANALYSIS.md   — signal levels, cascaded NF, SNR, image rejection, GALI-84+ decision
```

---

## Recommended Bring-Up Sequence

1. Build and verify power supply rails (+3.3 V, +5 V, +5V_P_AMP)
2. Verify SI5351 (MS5351M) I²C communication and all three CLK output frequencies
3. Build audio chain (LT6202 + volume pot + PAM8406) — inject test tone at volume pot input
4. Verify squelch circuit — adjust threshold pot, confirm SHDN# behaviour
5. Build product detector (AD8342 Mixer 3) — inject 455 kHz AM signal, verify audio output
6. Build 455 kHz IF filter — verify passband and insertion loss
7. Build 2nd IF mixer (AD8342 Mixer 2) — inject 10.7 MHz signal, verify 455 kHz IF output
8. Build 10.7 MHz IF filter — verify passband
9. Build RF mixer (AD8342 Mixer 1) — inject ~120 MHz test carrier, verify 10.7 MHz IF output
10. Build RF bandpass filter — verify 118–137 MHz passband
11. Full chain test — receive a live airband transmission
12. (Optional) add BGA2869 LNA if sensitivity is insufficient

---

## Known Trade-offs

| Area | Trade-off |
|---|---|
| Ceramic IF filters | Resistive impedance matching required (~270–330 Ω at 10.7 MHz, ~1–2 kΩ at 455 kHz) |
| No AGC | Manual LNA bypass (JP401) available; AGC deferred to future revision |
| Product detector LO phase | Free-running CLK2 at 455 kHz; phase offset scales audio amplitude but voice is intelligible |
| Image (low-side injection) | Image at 96.6–115.6 MHz; RF BPF provides 20–35 dB rejection across most of the band |
| Single-ended AD8342 | ~3 dB NF and gain degradation vs. fully differential; acceptable for this application |
