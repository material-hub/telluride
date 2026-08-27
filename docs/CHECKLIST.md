# Telluride campaign checklist — 2026-08-27 (ra52)

## Running now
- **Anvil wholenode fix arrays** (queued behind 10 trelax_wn nodes; DOS/pristine holds steer freed nodes here):
  - `trelax2` 20165353 — 75 relaxations covering every non-SOC row >15 eV (the 28–52 eV class: CdSe0.2Te0.8 O_i+X_i+O_i, CdSe0.06Te0.94 P_Se, HSE ZnTe/CdS rows at PBEsol geometry stage). 0/75 done.
  - `tdav` 20165844 — 67 ALGO=All statics replacing DAV-under-hybrid garbage SCFs (154 eV class). 0/67 done.
  - `tpris_soc` 20165401 — 7 HSE+SOC exact-cell pristine statics (vasp_ncl). 0/7 done.
- **Anvil**: trelax_wn (throttle 10) + trelax shared — 572 of 588-run single-point cohort remaining, 16 converged so far; deduped pristine array 20161374 (44 real cells, held); ddos 548 / pdos 733 remaining (wn arrays held until fix arrays start).
- **Gautschi standby**: epsprod 83/86 remaining (3 done: CdTe ε₀ 12.13, CdS 9.58 in log/eps_prod.json); ddos 616, pdos 837, resub 237 remaining.
- **NERSC**: nothing running — TACE frozen at TACE-full-1-20000.ckpt (E 8.33 meV/atom, F 9.78 meV/Å), all jobs Priority-pending behind other campaigns.

## Queued / not yet staged
- 215 mid/large ε production inputs (17–64 at and >64 at) — need Anvil capacity.
- 195 ε hosts with no PBEsol bulk cell — need a bulk-relax lane first.
- 64-atom exact-cell pristine pack for the 5 vbm_check-flagged hosts (PBE CdSe 611 cells median +1.46 eV, HSE ZnIn2S4, PBEsol K0.5Rb0.5Al0.5Ga0.5Se2 ×2, Cu2Zn1Sn0.5Zr0.5S4) — the only remaining derivation-blocked class.
- ~500 HSE relaxations beyond the worst-30 — blocked on extending the PBEsol-relax→HSE-static substitution (PI decision).
- ~5,700 multi-atom-exchange re-relaxations — background tail (~3–4 weeks), not a this-week goal.
- 236 resub runs >4 h SCF — migrate to Anvil per the 4-hour rule.
- CdSe0.12Te0.88 HSE+SOC pristine — no coordinate donor at 216 at; build from the defect cell next pass.
- Delete the 153 dead leps dirs on Gautschi (protocol superseded by ra45 ε production).

## Shipped and verified (latest first)
- b10210d96 (ra51): ladder guard withholds broken-SCF charge members with on-page disclosure; size-before-k-mesh bulk ranking; occupancy band edges from exact-cell pristines (1,032 rows). Pages hash-matched to Eagle; 10/10 browser passes, 0 JS errors.
- f50428e36 (wave 26b): 7 re-relaxed formation-energy rows, pristine 280 cells; 10/10 verified (CdSe Mo_Se 4.14/5.38/4.67/5.91/5.20/6.43).
- 5340978e3 (wave 26a): 21 re-relaxed runs, 81 defect + 77 bulk DOS, pristine 279; 10/10 verified.
- ε protocol (ra45): PBEsol, ENCUT 500, KSPACING 0.105; PBEsol overshoots exp by +15–18%, disclosed not scaled.

## Standing counts (2026-08-27)
- Library: 13,967 DFT compounds, 4,052 defect entries / 366 hosts on site; derived 4,758 defect rows, 3,608 with vertices; pristine 280/323 cells.
- Rows >15 eV: 37 (was 40) — every one has a fix run queued (trelax2/tdav/tpris_soc).
- Staged remaining: ≈3,819 runs (Anvil 2,046 + Gautschi 1,773).
