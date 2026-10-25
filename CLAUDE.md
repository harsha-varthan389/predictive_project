# CLAUDE.md — project context

Working notes for an assistant picking this repo up cold. Written 2026-10-25 from a full
read of the code, docs and recorded runs. Facts here were verified against the files; where
something is inferred it says so.

## 1. What this project is

A **Predictive Learning course project** on smart-grid substation security. Two halves that
meet in one place:

- **Half 1 (ML, steps 1-4, 6, 8)** — detect cyber-physical attacks in IEC 61850 **GOOSE**
  substation traffic. 5 classes: `Normal`, `Fault` (natural event), `Masquerade`,
  `Injection`, `Replay`. The question is not only "how accurate" but "which *layer* of
  evidence carries the signal" and "how do we measure it honestly".
- **Half 2 (graph design, steps 5, 7)** — use the measured detection numbers to **design**
  a grid's detection hierarchy (lines, PMU placement, zones/hubs, control centre) with
  classical graph algorithms, then test whether the designed ("optimized") grid beats
  reasonable non-optimized defaults under failure and attack scenarios.

The single most load-bearing finding: **detection lives on the station bus, not in the raw
phasors** (macro-F1: station-20 features 0.719 vs process-32 phasors 0.298), and the three
detector tiers are statistically indistinguishable — so grid differences come from *which
substations are still watched after a failure*, not from which model runs where.

The project's whole character is **anti-overclaiming**: every number is tagged measured vs
simulated vs assumed, the verdict rule is pre-declared, and the code contains deliberate
self-checks that make it return "NOT ESTABLISHED" when it should. Preserve that posture in
any change.

## 2. Repo at a glance

- Folder on disk: `~/Documents/projects/predective_project` (note: "predective", a typo).
- Remote: `https://github.com/harsha-varthan389/predictive_project.git`, branch `main`
  (GitHub repo title is `SmartGrid_Security_Optimization` — three different names for the
  same thing).
- ~7,300 lines of Python. No framework; stdlib + numpy/pandas/sklearn/scipy/xgboost.
- Originally developed on Windows (`C:\Users\ggcha\...` appears in the run logs); the docs
  are PowerShell-flavoured. Now on macOS.

```
run_pipeline.py        one command for steps 1-8 + unit tests; records each run in runs/
runs.py                list / show / diff / watch recorded runs
step1..step8_*.py      thin CLI wrappers; all real logic is in src/
src/config.py          EVERY choice in one place (paths, classes, blocks, model budget,
                       GRID_ASSUMPTIONS). Change behaviour here, not inline.
src/data.py            loader + structural asserts T1-T4, time blocks, Train-vs-Test audit
src/modalities.py      column -> layer (process / goose / consistency / relay / station)
src/splits.py          blocked (honest) and naive_rows (leaky, kept to size the inflation)
src/models.py          one frozen model budget; XGBoost, sklearn HistGB fallback
src/metrics.py         macro-F1, per-class recall, PR-AUC, attack_coverage, alerts/day
src/experiments/       e1 views, e3 feature budget, e4 compute profile,
                       e6 operating point, e8 unseen attack
src/detectors.py       step 6: three tiers, threshold at a fixed false-alert budget,
                       block bootstrap
src/forward_test.py    step 8: train-early / test-late hold-out
src/tracking.py        run records: run.json, summary.md, status.json, index, diff, MLflow
src/design/            graph.py (BFS/Dijkstra/MST/bridges/max-flow, hand-rolled on purpose)
                       power.py sites.py observability.py hierarchy.py security.py
                       evaluate.py params.py design.py  (step 5)
                       cases.py baseline.py compare.py  (step 7)
tests/                 153 tests (6 skip without networkx/mlflow)
tools/export_cases.py  pandapower -> dataset/grids CSV (not needed to run anything)
dataset/grids/         7 standard grids as CSV (IEEE 14/30/39/57/118/300, Illinois 200)
outputs/               everything the steps write; later steps read earlier steps' files
runs/                  one dir per pipeline run
explaination.txt       120 KB plain-text walkthrough — the best single document, read PART 0,
                       8, 10 first. Identical copy in materials/.
final_t&t.txt          console transcript of the last full run (20261018-223917)
rehersal_t&t.txt       transcript of the smoke run
materials/             copies of the above + testing_smoke.txt
```

## 3. The data situation — read this before running anything

- The pipeline is built on the **Kaggle "Power System Intrusion Dataset" (sequincozes)**:
  `dataset/Train.csv` + `dataset/Test.csv`, 1,094 rows each, 52 features + `time` + `class`.
  These are **git-ignored and currently NOT on disk** — `config.TRAIN_CSV` points at files
  that do not exist, so **steps 1-4, 6, 8 and most tests cannot run right now**. They must be
  re-downloaded into `dataset/` first.
- **Test.csv is not a test set.** It is the *same recording* as Train.csv: all 1,094
  (time, class) pairs appear in both, 48/52 feature columns identical, only four timing
  columns differ. Train->Test scores 0.989 macro-F1 = memorisation. **No reported number
  uses Test.csv.** Everything reported is blocked-time CV inside Train.csv: 20 contiguous
  time blocks, 5-fold `StratifiedGroupKFold` grouped on block; `time` is never a feature.
- A **second, larger dataset is staged but not wired in**: `dataset/PowerSystem/`
  (Mississippi State / Oak Ridge, 78,377 rows, 128 features + `marker`, 37 scenarios,
  binary/triple/multiclass variants, with a thorough provenance README). It is **untracked
  in git** and **no code references it**. Likewise `outputs/cache/dataset.parquet` (34 MB,
  Oct 5) is referenced by nothing. Read: an in-progress migration to a bigger dataset that
  was staged and then paused. Known landmine recorded in its README: **10,906 Inf cells** in
  the feature columns.
- Also untracked: `dataset/_old_ieee_dataport/` (abandoned IEEE DataPort dataset, dropped for
  having no row-level labels) and `materials/`.

## 4. Pipeline

`python run_pipeline.py` runs steps 1-8 then the tests (~5-15 min; 12.8 min on this laptop).

| step | script | what it does | trains? |
|---|---|---|---|
| 1 | step1_profile.py | profile + the Train/Test duplication audit | no |
| 2 | step2_views.py | which column belongs to which layer | no |
| 3 | step3_split_and_baseline.py | sizes how much each protocol flatters the score | yes |
| 4 | step4_run_experiments.py | E1 E3 E4 E6 E8 under blocked CV (the slow step, ~6 min) | yes |
| 5 | step5_design_grid.py | synthesises the grid from measured inputs | no |
| 6 | step6_train_detectors.py | the 3 tier detectors + bootstrap error bars -> models/ | yes |
| 7 | step7_compare_grids.py | default vs optimized, pre-declared verdict | no |
| 8 | step8_forward_test.py | train early / test late hold-out | yes |

Experiments: **E1** view ablation (+ time-only and counters-only controls), **E3** minimal
feature set k*, **E4** compute/latency profile, **E6** operating point in false alerts/day,
**E8** leave-one-attack-class-out. (E2, E5, E7 exist only as stale `.pyc` files in
`src/experiments/__pycache__/` — `e2_lead_time`, `e5_fusion_degradation`, `e7_attribution`,
`e8_scenario_holdout`. Their sources are gone; they were renamed or dropped.)

Step 5 stages: A lines (Kruskal MST + N-1 augmentation via DFS low-link) -> B PMUs (minimum
dominating set, scipy milp with greedy fallback, + zero-injection rule) -> C hierarchy
(all-pairs Dijkstra, k-medoid/PAM zoning, smallest feasible k) -> D stress (BFS failure
sweeps, lateral movement, max-flow) -> E evaluation (routing, A/B/B+/C comparison, costs).

Step 7 compares **optimized** against three defaults: *naive* (radial MST, PMU everywhere,
one control centre), *budget-matched* (same total spend, untargeted extra corridors,
standby CC) and a *control* (untargeted lines + the tiered architecture), plus the 7
standard grids' as-built corridors.

## 5. Conventions and invariants — do not break these

- **One frozen model budget** in `config.MODEL_PARAMS`, shared by every view. E1 is only a
  fair comparison if no view gets tuned. Never tune per view.
- **Honest splitting only.** `blocked` is the reported protocol; `naive_rows` exists solely
  to quantify the inflation. Never report a shuffled or Train->Test number as a result.
- **Feature ranking happens inside the CV loop** (E3), never once on all the data.
- **Thresholds come from a false-alert budget** (`config.OPERATING_FPR = 0.01` of
  Normal+Fault), never from maximising F1, and are re-derived inside every bootstrap
  replicate.
- **Asserts are the contract**: T1 same columns, T2 exactly the five classes, T3 numeric and
  no NaN, T4 usable clock. A fold never has a block on both sides. `time`/`t_ms`/labels/
  block ids are never features. Unknown columns are reported, not guessed.
- **Provenance tagging**: step 5/7 inputs are tagged measured / FALLBACK / STALE (older than
  Train.csv); `--require-measured` exits 2 on anything not fresh.
  `outputs/step5_inputs.json` records the dataset fingerprint.
- **Measured vs simulated**: detector recall is measured; every grid is simulated. Keep that
  distinction visible in any new output.
- **The verdict rule is pre-declared** (`compare.overall`): needs >= 1 security *outcome*
  win, 0 security losses, 0 performance losses; objectives alone can never establish a win;
  **price never vetoes and is never netted against security**. "narrow" unless most outcomes
  are wins.
- Graph routines are hand-rolled deliberately (every design decision names its traversal);
  tests cross-check against networkx when it is installed. Don't swap in networkx.
- Smoke runs (`--smoke`) write to `outputs_smoke/`/`models_smoke/` with reduced settings and
  are flagged **not reportable**. Any `PP_*` env override also marks a run non-reportable.

## 6. Current state and known issues

1. ~~Merge conflict markers in `README.md`.~~ **FIXED 2026-10-25** (uncommitted at time of
   writing). Commit `9d923a9 "Merge remote changes"` had merged GitHub's auto-generated
   `ea4e009 "Initial commit"` (unrelated history, README just
   `# SmartGrid_Security_Optimization`) into the real work, leaving `<<<<<<< HEAD` /
   `=======` / `>>>>>>>` in the file. The four marker/stub lines were deleted.
2. **The last full run failed at the tests step — cause still UNDIAGNOSED.**
   `runs/20261018-223917`: steps 1-8 all ok, then 153 tests ran with **1 failure, 6 skipped**.
   The failure is `tests/test_compare.py:452 test_price_is_never_netted_against_security` —
   `assertTrue(CP.overall(tally)[0].startswith("OPTIMIZED BETTER"))` got False.
   **Do not assume this is the CPU-dependent latency flip of issue 3.** That test is
   hermetic: it calls `load_measured(output_dir=tests/_no_measurements)`, a directory that
   does not exist, so it uses the FALLBACK placeholder latencies in `params.py`
   (`edge_p95_ms=1.8`, `station_p95_ms=5.5`) — a tiered penalty of roughly +3 ms against a
   10 ms tolerance, comfortably inside it. So the test should be deterministic on every
   machine, and something else in the tally is tripping `overall()`. Reproduce it in
   isolation and print the reasons before changing any code; the test needs sklearn but
   **not** Train.csv, so it runs without the dataset:
   `python -m unittest tests.test_compare.TestVerdict.test_price_is_never_netted_against_security -v`
   Note the tree was `dirty` at commit `9b3f9fa` when the run was recorded, so the failure
   may come from an edit that was never committed.
3. **The documented headline verdict does not match the outputs on disk.** README claims
   "OPTIMIZED BETTER (5 of 5 outcomes)"; `outputs/step7_verdict.csv` from this machine has
   `latency_ms` = "default better" (+11.4 ms against a 10 ms tolerance), so the recorded
   verdicts are **NOT ESTABLISHED vs naive and vs budget-matched**, and OPTIMIZED BETTER
   (narrow) only vs the `bm-lines+tiered` control. This is *expected and documented*
   (RUNNING.md, explaination.txt PART 6.6): the tiered design scores each message three
   times, so on a slow CPU the serial compute cost exceeds the fixed 10 ms tolerance and the
   pre-declared rule flips the label — all 10 security metrics still win. Report it as it is;
   do not quietly widen the tolerance.
4. ~~Test-count drift in the docs.~~ **FIXED 2026-10-25** (uncommitted): README said 152 and
   run_pipeline.py's docstring said 117; both now say 153, matching README_CODE and RUNNING.
5. `materials/final_t&t.txt` differs from the root `final_t&t.txt`; `explaination.txt` is
   identical in both places. Duplication worth collapsing.
6. **Nothing in this repo has ever been run on this Mac.** Both recorded runs
   (`runs/*/run.json`) report `Windows-11`, Python 3.14.7, numpy 2.5.4, pandas 3.0.6,
   scikit-learn 1.9.1, xgboost 3.4.1. Every file in `outputs/` — including the step 5 grid
   and the step 7 verdict — was produced on that Windows machine and arrived here through
   git; their 2026-10-25 mtimes are just the checkout date. A local full run will overwrite
   `outputs/` with this machine's numbers, and the E4 latencies (and possibly the step 7
   performance verdict) will differ. The `.venv/` in the folder is a stale macOS venv from
   September and is git-ignored; recreate it rather than trusting it.

## 7. Running it (macOS; the docs are Windows-flavoured)

```bash
cd ~/Documents/projects/predective_project
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt          # needs Python 3.11+
# put Train.csv and Test.csv in dataset/ first — nothing ML runs without them

python run_pipeline.py --smoke           # ~1-3 min, writes outputs_smoke/, NOT reportable
python run_pipeline.py                   # full: steps 1-8 + 153 tests, recorded in runs/
python run_pipeline.py --only 4 6 8      # just the train/test steps
python runs.py                           # list runs; show last | diff prev last | watch
python -m unittest discover -s tests -v  # tests alone
```

Env overrides (all mark the run non-reportable): `PP_N_ESTIMATORS`, `PP_N_SPLITS`,
`PP_BOOTSTRAP_B`, `PP_ENSEMBLE_SEEDS`, `PP_OUTPUT_DIR`, `PP_MODELS_DIR`.

**Note for an assistant working through the device bridge:** the Linux VM that `device_bash`
runs in has pandas but **no sklearn/scipy/xgboost and no network**, and the macOS `.venv` is
not executable from it. So the pipeline and tests **cannot be run from the bridge** — only
read, searched and edited. Running requires the user's own macOS terminal.

## 8. Headline numbers (blocked CV, for sanity-checking a rerun)

- macro-F1: all 52 features **0.711 +/- 0.032**; station 20 **0.719**; process 32 **0.298**.
  Leaky comparisons: shuffled 0.788, Train->Test 0.989.
- Per-class recall (all features): Normal 0.87, Fault 0.94, Masquerade 0.93,
  **Injection 0.40, Replay 0.39** (the two weak classes are also the rarest, ~100 rows each).
- E3: **k* = 8**; six features stable in every fold, none a phasor —
  `consistency, state_cb, any_relay, stDiff, recentChange, MU3Log`.
- E6: ~181 false alerts/day at best; 0.79 attack recall at <= 2,000/day.
- E8: mean recall gap when an attack class is unseen **+0.28** (Masquerade 0.95 -> 0.34).
- Step 6 tiers (coverage at 1% false alerts): sentinel 0.538, station 0.524, fused 0.558 —
  **not distinguishable**.
- Step 8 forward hold-out: macro-F1 0.551 (all), 0.573 (station) — expect ~0.15 below
  blocked CV on later data.
- Capture: 477 s, 2.29 messages/s, 4 MUs per row.

## 9. Standing limits to repeat, not bury

1,094 rows from one 8-minute capture of one testbed. Grids in steps 5/7 are synthetic; costs,
latencies and the blast-radius cap are assumptions in `config.GRID_ASSUMPTIONS`. N-1 is
topological, with no power-flow check. The adversary is a scenario set, not a threat model.
Cross-substation fusion gain is not credited (it would favour the tiered design). On the
standard grids, do **not** read the cost column against as-built — the optimized lines are
not checked against loads or ratings and the coordinates are schematic.
