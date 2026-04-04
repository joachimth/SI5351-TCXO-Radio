# CurrentErrors.md — V2 Schematic Action Items

This file lists all known errors and open items in the V2 schematic (`SCH_SI5351_TCXO_WithMixerAndFilters_Version2_2026-04-03.pdf`) that require action by the designer before PCB ordering.

Items are ordered by severity. Each item includes the exact location, the problem, and the required fix.

---

## CRITICAL — Must fix before PCB order

### ERR-01 — R702 / R703 wrong value (Mixer 2 IFOP/IFON pull-ups)

| Field | Detail |
|---|---|
| Page | 7 (IF_MIXER_BF) |
| Components | R702, R703 |
| Current value | 33 Ω |
| Required value | **330 Ω** |
| Severity | Critical — will destroy functionality |

**Problem:** At 33 Ω the pull-up current is (5 V / 33 Ω) ≈ 150 mA per pin — far exceeding the AD8342 IFOP/IFOM output capability. The source impedance driving the 455 kHz ceramic filter (L801, ~1–2 kΩ impedance) would be completely mismatched, causing severe insertion loss and possible pin damage.

**Fix:** Change R702 and R703 to **330 Ω** in the schematic. Confirm the EasyEDA BOM row (currently LCSC C25105 = 33 Ω) is updated to a 330 Ω part, e.g. UNI-ROYAL 0402WGF3300TCE (LCSC C25109).

---

## SIGNIFICANT — Fix before finalising PCB

### ERR-02 — Squelch circuit absent from schematic

| Field | Detail |
|---|---|
| Page | 11 (SPK_AMP) — not yet reviewed |
| Components | LM393, BAT54S, squelch passives |
| Severity | Feature missing |

**Problem:** The squelch circuit (LM393 + BAT54S envelope detector → PAM8406 SHDN#) is documented in HARDWARE.md but does not appear in the EasyEDA BOM CSV (2026-04-03). It is either on schematic page 11 (not yet shared) or has been intentionally omitted.

**Action:** Confirm whether page 11 contains the squelch. If omitted, decide whether to add it or permanently remove it from documentation. If page 11 is blank for squelch, at minimum ensure R1102 (10 kΩ) holds SHDN# high permanently so the PAM8406 is always enabled.

---

### ERR-03 — LO coupling capacitors C513 / C713 / C913 are through-hole

| Field | Detail |
|---|---|
| Pages | 5, 7, 9 (all three AD8342 mixers) |
| Components | C513, C713, C913 |
| Footprint | CAP-TH_L4.8-W3.2-P5.08-D0.5 (TORCH C400081) |
| Severity | Assembly — requires hand-soldering |

**Problem:** These 100 pF LO coupling capacitors have through-hole footprints. JLCPCB SMT assembly will not populate them. They must be hand-soldered after board delivery, or the footprint must be changed to SMD 0402.

**Action (choose one):**
- Replace with SMD equivalent: FH 0402CG101J500NT (LCSC C1546, same 100 pF NP0/C0G value) and update footprint to C0402.
- OR accept hand-soldering and ensure the three capacitors are included in the manual assembly list.

---

### ERR-04 — BGA2869 (U401) JLCPCB stock = 0

| Field | Detail |
|---|---|
| Page | 4 (RF_LNA) |
| Component | U401, BGA2869,115 (LCSC C515583) |
| Severity | Procurement — verify before ordering |

**Problem:** JLCPCB stock for C515583 is listed as 0 in the 2026-04-03 CSV export. If not restocked, JLCPCB cannot populate U401.

**Action:** Check JLCPCB Extended Parts catalogue and LCSC direct stock at time of order. If unavailable:
- Order JLCPCB to leave U401 unpopulated and solder manually after delivery.
- OR bridge JP401 (LNA bypass) — the receiver will work without the LNA at reduced sensitivity (~22 dB NF vs ~5.5 dB with LNA).

---

## MINOR — Review / clarify

### ERR-05 — Schematic annotation inconsistency (page 2)

| Field | Detail |
|---|---|
| Page | 2 (SI5351_TCXO) |
| Detail | Note reads "Pull-up resistors @ 1k enable fast I2" but R201/R202 = 2 kΩ |
| Severity | Cosmetic — annotation only |

**Action:** Update the schematic text note to read "2 kΩ" to match the actual resistor values. No circuit change needed — 2 kΩ pull-ups are correct for standard-mode I²C (≤100 kbit/s) with the MS5351M.

---

### ERR-06 — Volume control is a trimmer, not a user-accessible pot

| Field | Detail |
|---|---|
| Page | 10 (OP_AMP_AUDIO) |
| Component | R1005 = Bourns 3296W-1-103LF (10 kΩ multi-turn trimmer, C34846) |
| Severity | Design intent clarification |

**Problem:** R1005 is a PCB-mounted trimmer, not a panel-mounted volume control. There is no user-accessible volume knob.

**Action:** Confirm this is intentional. If user volume control is desired, replace R1005 with a panel-mount 10 kΩ logarithmic potentiometer (e.g. Alps RK09L, TH, A-taper) and route the three pins to a connector or panel cutout. If a fixed drive level is acceptable, the trimmer is fine — set once at bring-up.

---

### ERR-07 — Decoupling scheme (page 2 designer question)

| Field | Detail |
|---|---|
| Page | 2 (SI5351_TCXO) |
| Severity | Optional simplification |

**Designer's own note:** *"Skal decoupling fortsætte som den er nu? Eller simplificeres?"*

**Assessment:** Current decoupling (100 nF + 1 µF + 100 pF per supply pin on each AD8342) is correct and conservative. No change is required. If board area is tight, the 1 nF (C504/C704/C904) caps can be omitted without functional impact — the 100 pF already covers HF bypass.

---

## Resolved items (for reference)

| ID | Issue | Resolution |
|---|---|---|
| RESOLVED-01 | EXRB pin connected to VCC (previous schematic) | Fixed in 2026-04-03 version: R501/R701/R901 = 1.82 kΩ, EXRB → GND ✓ |
| RESOLVED-02 | LO matching network (47 nH + 4.7 pF) on AD8342 LO input | Removed in 2026-04-03 version; AD8342 has CMOS LO input, no matching needed ✓ |
| RESOLVED-03 | Wrong designator series (1200-series) in first draft | Corrected to page-based numbering (500/700/900-series) ✓ |
