# Run 20261029-070938_smoke

- status: **ok**  |  mode: SMOKE (not reportable)  |  1.8 min  |  started 2026-10-29T07:09:38
- name/note: - / -
- git: c5545d7  |  python 3.11.4  |  dataset fb3e4aae715163c5

> SMOKE run: fewer folds/trees/replicates, written to outputs_smoke/. Checks that the pipeline runs; never quote these numbers.

## Steps

| step | status | seconds | log |
|---|---|---|---|
| profile | ok | 2.0 | 01_profile.log |
| views | ok | 0.4 | 02_views.log |
| baselines | ok | 10.2 | 03_baselines.log |
| experiments | ok | 31.1 | 04_experiments.log |
| design | ok | 0.8 | 05_design.log |
| detectors | ok | 6.1 | 06_detectors.log |
| compare | ok | 4.3 | 07_compare.log |
| forward | ok | 6.3 | 08_forward.log |
| tests | ok | 44.2 | 09_tests.log |

## Headline

| | |
|---|---|
| **TRAIN + TEST, blocked CV** |  |
| macro-F1, all 52 features | 0.762 +/- 0.014 |
| macro-F1, station layers (20) | 0.760 +/- 0.008 |
| attack coverage, all features | 0.597 |
| same data, shuffled CV (leaks neighbours) | 0.766 |
| same data, published Train->Test (same recording) | 0.956 |
| fold-stable features / smallest sufficient k | 7 / 12 |
| **TEST ON LATER DATA, forward hold-out** |  |
| macro-F1, all features | 0.639 |
| fused detector: attack coverage / false-alert rate | 0.273 / 0.016  (target 0.010) |
| sentinel detector: attack coverage / false-alert rate | 0.558 / 0.016 |
| **DETECTOR TIERS, attack coverage at 1% false alerts** |  |
| sentinel (6 features) | 0.605  (95% CI 0.565 to 0.705) |
| station (20) | 0.628  (95% CI 0.585 to 0.683) |
| fused (52) | 0.635  (95% CI 0.594 to 0.682) |
| **GRID: DEFAULT vs OPTIMIZED** |  |
| verdict vs naive default | OPTIMIZED BETTER |
| verdict vs budget-matched default | OPTIMIZED BETTER |
| verdict vs same-lines control | OPTIMIZED BETTER (narrow) |
| coverage, 2 targeted losses: naive -> optimized | 0.145 -> 0.603 |
| **CHECKS** |  |
| unit tests | OK (153 ran) |

## Settings that change a number

- CV: 3 folds over 20 time blocks, seed 42
- model: {"n_estimators": 40, "max_depth": 6, "learning_rate": 0.1, "subsample": 0.9, "colsample_bytree": 0.9, "reg_lambda": 1.0}
- detectors: false-alert budget 1%, bootstrap B=60
- grid assumptions hash: 13ccc446d3
- environment overrides: {'PP_OUTPUT_DIR': 'outputs_smoke', 'PP_MODELS_DIR': 'models_smoke', 'PP_N_SPLITS': '3', 'PP_N_ESTIMATORS': '40', 'PP_BOOTSTRAP_B': '60', 'PP_ENSEMBLE_SEEDS': '3'}
- packages: {"numpy": "2.4.6", "pandas": "3.1.0", "scikit-learn": "1.9.1", "xgboost": "3.2.0", "scipy": "1.17.1", "matplotlib": "3.11.2", "joblib": "1.6.0"}

## Every recorded number

<details><summary>show</summary>

| metric | value |
|---|---|
| compare.vs_budget-matched.cost_total_kusd.default | 2,365,280 |
| compare.vs_budget-matched.cost_total_kusd.optimized | 2,404,489 |
| compare.vs_budget-matched.cov_delivered_n2_targeted.default | 0.3995 |
| compare.vs_budget-matched.cov_delivered_n2_targeted.optimized | 0.6031 |
| compare.vs_budget-matched.cov_delivered_single.default | 0.5989 |
| compare.vs_budget-matched.cov_delivered_single.optimized | 0.6417 |
| compare.vs_budget-matched.cov_delivered_worst.default | 0 |
| compare.vs_budget-matched.cov_delivered_worst.optimized | 0.4911 |
| compare.vs_budget-matched.spof_total.default | 31.17 |
| compare.vs_budget-matched.spof_total.optimized | 0 |
| compare.vs_naive.cost_total_kusd.default | 1,770,017 |
| compare.vs_naive.cost_total_kusd.optimized | 2,404,489 |
| compare.vs_naive.cov_delivered_n2_targeted.default | 0.1452 |
| compare.vs_naive.cov_delivered_n2_targeted.optimized | 0.6031 |
| compare.vs_naive.cov_delivered_single.default | 0.4897 |
| compare.vs_naive.cov_delivered_single.optimized | 0.6417 |
| compare.vs_naive.cov_delivered_worst.default | 0 |
| compare.vs_naive.cov_delivered_worst.optimized | 0.4911 |
| compare.vs_naive.spof_total.default | 50 |
| compare.vs_naive.spof_total.optimized | 0 |
| data.merging_units | 4 |
| data.row_rate_hz | 2.294 |
| data.rows | 1094 |
| design.levels | 4 |
| design.lines | 47 |
| design.n1_premium_pct | 30.1 |
| design.pmus | 12 |
| design.total_cost_kusd | 3,081,620 |
| design.zones | 6 |
| detectors.fused.coverage | 0.6354 |
| detectors.fused.coverage_hi | 0.6816 |
| detectors.fused.coverage_lo | 0.5937 |
| detectors.sentinel.coverage | 0.6047 |
| detectors.sentinel.coverage_hi | 0.7049 |
| detectors.sentinel.coverage_lo | 0.5654 |
| detectors.station.coverage | 0.628 |
| detectors.station.coverage_hi | 0.6828 |
| detectors.station.coverage_lo | 0.5848 |
| e1.all.attack_coverage | 0.5971 |
| e1.all.macro_f1 | 0.7619 |
| e1.all.macro_f1_sd | 0.0137 |
| e1.all.recall_Fault | 1 |
| e1.all.recall_Injection | 0.4778 |
| e1.all.recall_Masquerade | 0.9621 |
| e1.all.recall_Normal | 0.9524 |
| e1.all.recall_Replay | 0.3515 |
| e1.consistency.attack_coverage | 0.314 |
| e1.consistency.macro_f1 | 0.4963 |
| e1.consistency.macro_f1_sd | 0.01184 |
| e1.control_counters_only.attack_coverage | 0.3214 |
| e1.control_counters_only.macro_f1 | 0.2948 |
| e1.control_counters_only.macro_f1_sd | 0.005079 |
| e1.control_time_only.attack_coverage | 0.2739 |
| e1.control_time_only.macro_f1 | 0.2086 |
| e1.control_time_only.macro_f1_sd | 0.01308 |
| e1.goose.attack_coverage | 0.606 |
| e1.goose.macro_f1 | 0.6612 |
| e1.goose.macro_f1_sd | 0.02137 |
| e1.process.attack_coverage | 0.1322 |
| e1.process.macro_f1 | 0.2761 |
| e1.process.macro_f1_sd | 0.02541 |
| e1.relay.attack_coverage | 0.017 |
| e1.relay.macro_f1 | 0.2558 |
| e1.relay.macro_f1_sd | 0.007326 |
| e1.station.attack_coverage | 0.6136 |
| e1.station.macro_f1 | 0.7596 |
| e1.station.macro_f1_sd | 0.007781 |
| e3.k_star | 12 |
| e3.stable_features | 7 |
| e4.tier1_edge_kstar.p95_ms | 1.317 |
| e4.tier1_edge_process_only.p95_ms | 1.742 |
| e4.tier2_zone_fused.p95_ms | 2.7 |
| e4.tier3_widearea_fused.p95_ms | 2.682 |
| e6.recall_at_2000_false_alerts_per_day | 0.8294 |
| e8.mean_gap_all | 0.2493 |
| forward.all.macro_f1 | 0.6387 |
| forward.fused.test_coverage | 0.2726 |
| forward.fused.test_fpr | 0.01639 |
| forward.process.macro_f1 | 0.2634 |
| forward.sentinel.test_coverage | 0.5578 |
| forward.sentinel.test_fpr | 0.01639 |
| forward.station.macro_f1 | 0.6495 |
| forward.station.test_coverage | 0.2367 |
| forward.station.test_fpr | 0.01639 |
| split.all.blocked_cv_macro_f1 | 0.7619 |
| split.all.published_train_to_test_macro_f1 | 0.9565 |
| split.all.shuffled_cv_macro_f1 | 0.7661 |
| split.process.blocked_cv_macro_f1 | 0.2761 |
| split.process.published_train_to_test_macro_f1 | 0.7092 |
| split.process.shuffled_cv_macro_f1 | 0.2832 |
| split.station.blocked_cv_macro_f1 | 0.7596 |
| split.station.published_train_to_test_macro_f1 | 0.9364 |
| split.station.shuffled_cv_macro_f1 | 0.7823 |
| tests.ran | 153 |
| compare.vs_bm-lines+tiered | OPTIMIZED BETTER (narrow) |
| compare.vs_budget-matched | OPTIMIZED BETTER |
| compare.vs_naive | OPTIMIZED BETTER |
| design.feasible | True |
| tests.result | OK |

</details>
