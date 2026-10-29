# Run 20261029-074028

- status: **ok**  |  mode: full  |  9.9 min  |  started 2026-10-29T07:40:28
- name/note: - / -
- git: c5545d7  |  python 3.11.4  |  dataset fb3e4aae715163c5

## Steps

| step | status | seconds | log |
|---|---|---|---|
| profile | ok | 2.0 | 01_profile.log |
| views | ok | 0.4 | 02_views.log |
| baselines | ok | 76.6 | 03_baselines.log |
| experiments | ok | 256.2 | 04_experiments.log |
| design | ok | 9.8 | 05_design.log |
| detectors | ok | 32.8 | 06_detectors.log |
| compare | ok | 137.0 | 07_compare.log |
| forward | ok | 34.1 | 08_forward.log |
| tests | ok | 42.7 | 09_tests.log |

## Headline

| | |
|---|---|
| **TRAIN + TEST, blocked CV** |  |
| macro-F1, all 52 features | 0.729 +/- 0.036 |
| macro-F1, station layers (20) | 0.719 +/- 0.025 |
| attack coverage, all features | 0.590 |
| same data, shuffled CV (leaks neighbours) | 0.782 |
| same data, published Train->Test (same recording) | 0.989 |
| fold-stable features / smallest sufficient k | 6 / 8 |
| **TEST ON LATER DATA, forward hold-out** |  |
| macro-F1, all features | 0.541 |
| fused detector: attack coverage / false-alert rate | 0.493 / 0.033  (target 0.010) |
| sentinel detector: attack coverage / false-alert rate | 0.528 / 0.016 |
| **DETECTOR TIERS, attack coverage at 1% false alerts** |  |
| sentinel (6 features) | 0.626  (95% CI 0.551 to 0.694) |
| station (20) | 0.528  (95% CI 0.470 to 0.610) |
| fused (52) | 0.580  (95% CI 0.538 to 0.671) |
| **GRID: DEFAULT vs OPTIMIZED** |  |
| verdict vs naive default | OPTIMIZED BETTER |
| verdict vs budget-matched default | OPTIMIZED BETTER |
| verdict vs same-lines control | OPTIMIZED BETTER (narrow) |
| coverage, 2 targeted losses: naive -> optimized | 0.123 -> 0.555 |
| **CHECKS** |  |
| unit tests | OK (153 ran) |

## Settings that change a number

- CV: 5 folds over 20 time blocks, seed 42
- model: {"n_estimators": 300, "max_depth": 6, "learning_rate": 0.1, "subsample": 0.9, "colsample_bytree": 0.9, "reg_lambda": 1.0}
- detectors: false-alert budget 1%, bootstrap B=300
- grid assumptions hash: 13ccc446d3
- environment overrides: none
- packages: {"numpy": "2.4.6", "pandas": "3.1.0", "scikit-learn": "1.9.1", "xgboost": "3.2.0", "scipy": "1.17.1", "matplotlib": "3.11.2", "joblib": "1.6.0"}

## Every recorded number

<details><summary>show</summary>

| metric | value |
|---|---|
| compare.vs_budget-matched.cost_total_kusd.default | 3,579,907 |
| compare.vs_budget-matched.cost_total_kusd.optimized | 3,616,092 |
| compare.vs_budget-matched.cov_delivered_n2_targeted.default | 0.3732 |
| compare.vs_budget-matched.cov_delivered_n2_targeted.optimized | 0.5551 |
| compare.vs_budget-matched.cov_delivered_single.default | 0.5577 |
| compare.vs_budget-matched.cov_delivered_single.optimized | 0.5886 |
| compare.vs_budget-matched.cov_delivered_worst.default | 0 |
| compare.vs_budget-matched.cov_delivered_worst.optimized | 0.459 |
| compare.vs_budget-matched.spof_total.default | 48.32 |
| compare.vs_budget-matched.spof_total.optimized | 0 |
| compare.vs_naive.cost_total_kusd.default | 2,697,853 |
| compare.vs_naive.cost_total_kusd.optimized | 3,616,092 |
| compare.vs_naive.cov_delivered_n2_targeted.default | 0.1231 |
| compare.vs_naive.cov_delivered_n2_targeted.optimized | 0.5551 |
| compare.vs_naive.cov_delivered_single.default | 0.4701 |
| compare.vs_naive.cov_delivered_single.optimized | 0.5886 |
| compare.vs_naive.cov_delivered_worst.default | 0 |
| compare.vs_naive.cov_delivered_worst.optimized | 0.459 |
| compare.vs_naive.spof_total.default | 79.28 |
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
| detectors.fused.coverage | 0.5796 |
| detectors.fused.coverage_hi | 0.6708 |
| detectors.fused.coverage_lo | 0.5385 |
| detectors.sentinel.coverage | 0.6263 |
| detectors.sentinel.coverage_hi | 0.6939 |
| detectors.sentinel.coverage_lo | 0.5508 |
| detectors.station.coverage | 0.5281 |
| detectors.station.coverage_hi | 0.6099 |
| detectors.station.coverage_lo | 0.47 |
| e1.all.attack_coverage | 0.5905 |
| e1.all.macro_f1 | 0.7287 |
| e1.all.macro_f1_sd | 0.03625 |
| e1.all.recall_Fault | 0.9714 |
| e1.all.recall_Injection | 0.4339 |
| e1.all.recall_Masquerade | 0.939 |
| e1.all.recall_Normal | 0.8818 |
| e1.all.recall_Replay | 0.3986 |
| e1.consistency.attack_coverage | 0.3226 |
| e1.consistency.macro_f1 | 0.4955 |
| e1.consistency.macro_f1_sd | 0.02208 |
| e1.control_counters_only.attack_coverage | 0.2848 |
| e1.control_counters_only.macro_f1 | 0.2391 |
| e1.control_counters_only.macro_f1_sd | 0.09174 |
| e1.control_time_only.attack_coverage | 0.3585 |
| e1.control_time_only.macro_f1 | 0.1734 |
| e1.control_time_only.macro_f1_sd | 0.03693 |
| e1.goose.attack_coverage | 0.6348 |
| e1.goose.macro_f1 | 0.6329 |
| e1.goose.macro_f1_sd | 0.04051 |
| e1.process.attack_coverage | 0.1642 |
| e1.process.macro_f1 | 0.3026 |
| e1.process.macro_f1_sd | 0.02379 |
| e1.relay.attack_coverage | 0.01614 |
| e1.relay.macro_f1 | 0.2531 |
| e1.relay.macro_f1_sd | 0.03816 |
| e1.station.attack_coverage | 0.6124 |
| e1.station.macro_f1 | 0.7192 |
| e1.station.macro_f1_sd | 0.0253 |
| e3.k_star | 8 |
| e3.stable_features | 6 |
| e4.tier1_edge_kstar.p95_ms | 1.55 |
| e4.tier1_edge_process_only.p95_ms | 2.474 |
| e4.tier2_zone_fused.p95_ms | 3.813 |
| e4.tier3_widearea_fused.p95_ms | 2.932 |
| e6.recall_at_2000_false_alerts_per_day | 0.786 |
| e8.mean_gap_all | 0.2463 |
| forward.all.macro_f1 | 0.5409 |
| forward.fused.test_coverage | 0.4932 |
| forward.fused.test_fpr | 0.03279 |
| forward.process.macro_f1 | 0.2598 |
| forward.sentinel.test_coverage | 0.5281 |
| forward.sentinel.test_fpr | 0.01639 |
| forward.station.macro_f1 | 0.5578 |
| forward.station.test_coverage | 0.2721 |
| forward.station.test_fpr | 0.01639 |
| split.all.blocked_cv_macro_f1 | 0.7287 |
| split.all.published_train_to_test_macro_f1 | 0.9886 |
| split.all.shuffled_cv_macro_f1 | 0.7817 |
| split.process.blocked_cv_macro_f1 | 0.3026 |
| split.process.published_train_to_test_macro_f1 | 1 |
| split.process.shuffled_cv_macro_f1 | 0.2927 |
| split.station.blocked_cv_macro_f1 | 0.7192 |
| split.station.published_train_to_test_macro_f1 | 0.9831 |
| split.station.shuffled_cv_macro_f1 | 0.8124 |
| tests.ran | 153 |
| compare.vs_bm-lines+tiered | OPTIMIZED BETTER (narrow) |
| compare.vs_budget-matched | OPTIMIZED BETTER |
| compare.vs_naive | OPTIMIZED BETTER |
| design.feasible | True |
| tests.result | OK |

</details>
