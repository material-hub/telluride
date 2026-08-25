# Telluride — remaining work checklist

Every item below is either RUNNING (a job id is given) or QUEUED behind one that is. Nothing
here is a "someone should look at this" note. Live numbers are regenerated from the data at the
bottom of this file by `log/checklist_update.py` on every site build — never hand-edit below the
AUTO marker.

Last hand-revision: 2026-08-25 (after the root-cause session, README ra39–ra41).

---

## RUNNING NOW

| # | what | where | id | state |
|---|---|---|---|---|
| R1 | **Exact-cell pristine statics** — 323 cells; closes the "settings gate" class (6,326 cells / 656 rows) | Gautschi | `15497267` | running, throttle 24 |
| R2 | **Unrelaxed-geometry relaxations** — 588 runs over the 17 rows that are single-point AND above 10 eV; this is the +28.63 eV CdSe0.2Te0.8 `O_i+N_i+O_i` class | Gautschi | `15503213` | running, throttle 6 |
| R3 | **Diverged-SCF resubmissions** — 247 runs, `ALGO=All / NELM=200`; every one finished so far converged | Gautschi | `15494338` | running, throttle 4 |
| R4 | **ddos** (LOCPOT + per-ion DOSCAR, feeds eFNV and the DOS panel) | Gautschi + Anvil | arrays | running |
| R5 | **pdos / leps (dielectrics)** | Gautschi + Anvil | arrays | running |
| R6 | **TACE fine-tune**, 6 nodes / 24 GPUs, 25k-step cosine cycle | NERSC | `57541508` | running, 1.56 it/s |

## QUEUED — derivation only, no new DFT, unblocked by R1

| # | what | closes | blocked on |
|---|---|---|---|
| Q1 | **VBM re-derivation for the 4 flagged hosts** — PBEsol K0.5Rb0.5Al0.5Ga0.5Se2 (+stannite twin) sit −4.97 eV below VBM, HSE ZnIn2S4 −1.64 eV, PBE CdSe +1.46 eV against a 0.53 eV gap | the systematic donor/acceptor inversions on those hosts, incl. the K_Mg / Cs_Mg "wrongly neutral" family | R1 |
| Q2 | **Chem-pot vertices for the multinary alloy hosts** (`chempot_alloy.py` extension) | 5,115 no-vertex cells / 444 rows | — |
| Q3 | **Level-2 dopant solubility caps** | 280 cells / 28 rows across 14 elements (Cu 40, Ag 20, Al 20, Be 20, Ca 20, Ga 20 …) | — |
| Q4 | **eFNV charge corrections** — needs ε (R5) plus atomic-site core potentials (R4) | every charged cell currently marked "uncorrected"; the V_Se-neutral-looking artifact in small-gap PBEsol hosts | R4 + R5 |
| Q5 | **Re-derive + republish after R1–R3 land**, then browser-verify 10/10 | the whole withheld count | R1–R3 |

## OPEN — decided but not yet started

| # | what | size | note |
|---|---|---|---|
| O1 | Extend the relaxation lane (R2) to **every** single-point row with a multi-atom exchange | 2,078 rows sit on single-point ground states | R2 covers only the 17 worst; most of the rest are vacancies/simple substitutions that barely relax, so this is a judgement call on cost |
| O2 | **47 ladder-convexity violations** (Ef(q) bowing > 0.75 eV beyond negative-U tolerance, 42 PBEsol) | 47 rows | overlaps R3; re-measure after it lands |
| O3 | **30 chemistry-robust vacancy character inversions** + 19 vacancies neutral with a full ladder | 49 rows | mostly small-gap PBEsol hosts → expected to resolve with Q4 |
| O4 | **13,726 records whose raw OUTCAR was never archived** (energies imported from processed summaries) | — | kept and disclosed as unverifiable, NOT excluded; re-archive only if a specific number is challenged |
| O5 | Search placeholder advertises `V_Cd` / `vacancy`, which return 0 rows; real vocabulary is `Vac_Cd` / `native` / `dopant` / `complex` | 1 string | one-line bundle edit |
| O6 | TACE: `max_epochs: 80` is dead config — `max_steps: 25000` ends the run at **1.15 epochs**. 80 epochs = 1.74 M steps ≈ 13 days of continuous 6-node compute | — | raise `max_steps` AND `first_cycle_steps` together, or accept 1.15 epochs (already at 12.28 meV/atom, better than the previous run at 24k steps) |

## CLOSED THIS SESSION (2026-08-25)

- SCF-convergence gate: 137,595 OUTCARs scanned, 247 demonstrated failures excluded and resubmitted (README ra39)
- Formula-unit basis bug: the As_i+Cd_i di-interstitial no longer publishes as "As_Te donor" (ra39)
- Whole-exchange defect naming: an archived `Hg_In` that also gained an H now reads `Hg_In+H_i` (ra39)
- Physics cross-check of all 4,758 rows, heuristic-free (ra40)
- Cross-cluster sweep: 6 + 4 Globus tasks, all checksum-verified, 769 source dirs deleted; PBE+U elemental refs merged (ra39)
- Defect Ef(E_F) envelope line 4.5 → 2.6 px, browser-verified 10/10 (ra41)

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

Refreshed `2026-08-25T16:39:14+00:00` (0.0 h ago).

| campaign | finished | total |
|---|---|---|
| pristine exact-cell statics | 14 | 324 |
| dielectrics (LEPSILON) | 8 | 181 |
| defect DOS | 3 | 634 |
| projected DOS | 2 | 840 |
| diverged-SCF resubmissions | 7 | 248 |
| unrelaxed-geometry relaxations | 0 | 589 |

Queue: **15 running, 32 pending** — 13 ddos(PD), 9 pdos_b(PD), 5 pris2(R), 5 binch(PD), 4 resub(R)

<!--AUTO:END-->
