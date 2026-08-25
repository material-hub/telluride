# Telluride — remaining work checklist

Every item is either RUNNING with a live job id, QUEUED behind a named blocker, or OPEN as an
explicit decision for the PI. Nothing here is a "someone should look at this" note. The live
numbers below the AUTO marker are regenerated from the data by `log/checklist_update.py` and from
both clusters by `~/bin/telluride_campaign_refresh.sh` — never hand-edit below that marker.

Last hand-revision: 2026-08-25 18:30 UTC (README ra39–ra43).

---

## RUNNING NOW

| # | what | where | job id | width |
|---|---|---|---|---|
| R1 | **Exact-cell pristine statics** — 323 cells. Closes the biggest single blocker: the "settings gate" class, 6,326 cells across 656 rows | **Anvil** `wholenode` | `20136540` | 40 |
| R2 | **Unrelaxed-geometry relaxations** — 588 runs over the 17 rows that are single-point AND above 10 eV; this is the +28.63 eV CdSe0.2Te0.8 `O_i+N_i+O_i` class | **Anvil** `wholenode` | `20136541` | 40 |
| R3 | **Diverged-SCF resubmissions** — 247 runs, `ALGO=All / NELM=200`; every one finished so far converged | Gautschi standby | `15494338` | 24 |
| R4 | **ddos** — LOCPOT + per-ion DOSCAR, feeds eFNV (Q4) and the DOS panel | Gautschi + Anvil | arrays | 24 / running |
| R5 | **pdos + leps (dielectrics)** — ε for eFNV and the dielectric column | Gautschi + Anvil | arrays | 24 / 12 |
| R6 | **TACE fine-tune** — 6 nodes / 24 GPUs, 25k-step cosine cycle, 1.56 it/s, val 12.28 meV/atom · 10.92 meV/Å | NERSC | `57598785` | chain fixed (ra42) |

R1 and R2 were moved off Gautschi on 2026-08-25: a 4 h standby wall with `LWAVE=.FALSE.` meant a
216-atom hybrid static restarted its SCF from zero every chunk and could never converge no matter
how often the array retried. Anvil's `wholenode` has no wall limit over 750 nodes (ra42, ra43).

## QUEUED — derivation only, no new DFT

| # | what | closes | blocked on |
|---|---|---|---|
| Q1 | **VBM re-derivation for the 4 flagged hosts** — PBEsol K0.5Rb0.5Al0.5Ga0.5Se2 (+ its stannite twin) imply defect levels **−4.97 eV below VBM**, HSE ZnIn2S4 −1.64 eV, PBE CdSe **+1.46 eV against a 0.53 eV gap** (611 cells, donor-inflated) | the systematic character inversions on those hosts, incl. the K_Mg / Cs_Mg "wrongly neutral" family | R1 |
| Q2 | **Chem-pot vertices for the multinary alloy hosts** — `chempot_alloy.py` extension | 5,115 no-vertex cells / 444 rows | — |
| Q3 | **Level-2 dopant solubility caps** | 280 cells / 28 rows over 14 elements (Cu 40, Ag 20, Al 20, Be 20, Ca 20, Ga 20 …) | — |
| Q4 | **eFNV charge corrections** | every charged cell now marked "uncorrected"; the V_Se-looks-neutral artifact in small-gap PBEsol hosts | R4 (core potentials) + R5 (ε) |
| Q5 | **Re-derive → republish → browser-verify 10/10** | the whole withheld count; target is min Ef ≥ −2 eV and no cell above ~15 eV | R1–R3 |

## OPEN — decisions, not forgotten work

| # | what | size | why it is a decision |
|---|---|---|---|
| O1 | Extend R2 to **every** single-point row carrying a multi-atom exchange | 2,078 rows sit on single-point ground states | R2 covers the 17 worst. Measured: 1-step rows have median max-Ef 5.52 eV with 127 cells > 10 eV; 12–34-step rows sit at 1.4–3.0 eV with ~none. Vacancies/simple substitutions barely relax, so the rest is a cost call |
| O2 | **47 ladder-convexity violations** — Ef(q) bowing > 0.75 eV past negative-U tolerance, 42 of them PBEsol | 47 rows | overlaps R3; re-measure once it lands rather than fixing twice |
| O3 | **30 vacancy character inversions** (chemistry-robust) + **19 vacancies neutral with a full ladder** | 49 rows | mostly small-gap PBEsol hosts, so expected to resolve via Q4 |
| O4 | **13,726 records whose raw OUTCAR was never archived** | — | energies imported from processed summaries: unverifiable, NOT known-bad. Kept and disclosed; re-archive only if a specific number is challenged |
| O5 | Search placeholder advertises `V_Cd` / `vacancy`, both of which return 0 rows | 1 string | real vocabulary is `Vac_Cd` / `native` / `dopant` / `complex`; one-line bundle edit |
| O6 | **TACE epoch target** — `max_epochs: 80` is dead config; `max_steps: 25000` ends the run at **1.15 epochs** | 80 epochs = 1.74 M steps ≈ **13 days** of continuous 6-node compute | raise `max_steps` AND `first_cycle_steps` together (truncating the cosine alone is a bug we already fixed once), or accept 1.15 epochs — already 12.28 meV/atom, better than the previous run reached at 24k steps |
| O7 | **TACE stress head is untrained** — `valid_fid6.xyz` carries **zero** stress labels, yet `loss_property` includes stress; `val/stress_rmse` is exactly 0.000000 because `stress_weight` is 0 | — | harmless to training, but any stress TACE predicts is meaningless. Drop stress from the loss, or add stress labels |

## CLOSED 2026-08-25

- SCF-convergence gate — 137,595 OUTCARs scanned; 247 demonstrated failures excluded **and resubmitted** (ra39)
- Formula-unit basis bug — the 218-atom As_i+Cd_i di-interstitial no longer publishes as "As_Te donor" (ra39)
- Whole-exchange naming — an archived `Hg_In` whose cell also gained an H now reads `Hg_In+H_i` (ra39)
- Heuristic-free physics cross-check of all 4,758 rows; 4 broken hosts and 47 convexity violations identified (ra40)
- Root cause of the +28.63 eV class found: **unrelaxed geometry**, not a bad reference (ra41)
- Cross-cluster sweep — 10 Globus tasks, all checksum-verified; 769 source dirs deleted; PBE+U elemental refs merged (ra39, ra41)
- Defect Ef(E_F) envelope line 4.5 → 2.6 px, browser-verified 10/10 (ra41)
- **TACE chain break** — successor now queued up front via `--dependency=afterany`, internal timeout read from the granted allocation (ra42)
- **Hybrid pristine could never finish** — `LWAVE=.TRUE.` makes chunks cumulative; lane moved to Anvil's unlimited wall (ra42, ra43)
- Checklist auto-block now polls **both** clusters, so a stale hop is reported rather than faked (ra43)

## Verification standard for every item

A number is not "done" until it is (a) recomputed from the canonical chain, (b) cross-checked by a
second independent route, and (c) for anything that renders, read off the real page in a browser
driven by CDP, ten times, with the console-error count quoted.

---
<!--AUTO:BEGIN-->

# Live status — regenerated automatically, do not hand-edit below this line

Rewritten by `log/checklist_update.py` at the end of every site build, so these numbers
cannot drift from the data. The prose above this marker is hand-written.

**Formation energies: 28,636 of 40,517 cells (70.7%) across 4,758 defect rows.**

| blocker | cells |
|---|---|
| settings gate | 6,326 |
| no chem-pot vertex | 5,115 |
| missing dopant cap | 280 |
| other | 160 |

Range of published values: **-10.03 eV** to **159.63 eV**; 3,442 cells below 0, 619 below −2 eV, 228 below −5 eV.

Missing chemical potentials, worst first: **Cu** 40, **Ag** 20, **Al** 20, **Be** 20, **Ca** 20, **Ga** 20 (across 14 elements).

**Diverged charge states held back from trust: 95 charge states across 58 rows**, each carrying `charge_states_off_neutral_eV`. They are flagged in place, never deleted.

| row | worst charge state, eV off its own neutral |
|---|---|
| `PBEsol|Cs0.5Cu0.5Al1S1Se1|Al_S` | 4,875 |
| `PBEsol|Cs0.5Cu0.5Al1S1Se1_kesterite|Al_S` | 4,875 |
| `HSE|ZnIn2S4|Cr_In` | 1,598 |
| `PBEsol|K0.5Rb0.5Ag1Zn1Ge1S4|K_Ge` | 1,463 |
| `HSE+SOC|ZnTe|N_Te+N_Zn` | 174 |

| theory | alloy hosts solved | refused on reference inconsistency |
|---|---|---|
| HSE | 5 | 6 |
| HSE+SOC | 5 | 0 |
| PBE | 6 | 0 |
| PBEsol | 218 | 99 |

## Compute campaigns

Refreshed `2026-08-25T18:30:24+00:00` (0.0 h ago).

| campaign | finished | total |
|---|---|---|
| pristine exact-cell statics (Anvil) | 0 | 324 |
| unrelaxed-geometry relaxations (Anvil) | 0 | 589 |
| diverged-SCF resubmissions (Gautschi) | 8 | 248 |
| defect DOS (Gautschi) | 5 | 634 |
| defect DOS (Anvil) | 62 | 644 |
| projected DOS (Gautschi) | 3 | 840 |
| projected DOS (Anvil) | 56 | 807 |
| dielectrics LEPSILON (Gautschi) | 11 | 181 |

Queue: **37 running, 30 pending** — Gautschi 37R/30PD, Anvil 55R/5PD

<!--AUTO:END-->
