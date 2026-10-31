# Reproduction routes and their limits

No scientific computation was executed during archive preparation. This document separates saved-evidence inspection, future reevaluation, and future retraining. Do not run archived dispatchers in the evidence tree: many deliberately reject existing outputs and some write to fixed experiment directories. Always work in a separate copy.

## 1. Restore and locate the correct version

Extract code + all core volumes together. Use `tools/materialize.py --destination ./review_work --scope core` to restore deduplicated files. To bind portable path markers in a **new working copy**, add `--bind-paths --training-python /your/env/bin/python --generation-python /your/generator/env/bin/python`. Those paths are user-supplied examples, not archived environments. Unknown external paths remain `__UNAVAILABLE_EXTERNAL_PATH__`; resolve only if needed. The utility does not execute experiments or modify public payloads.

`RESULTS_MAP.csv` is the entry point. Select by group, condition, generation seed, protocol, method and training seed. `checkpoint_payload` is the single physical weight copy. Do not use a C checkpoint to explain B. Original SHA fields inside historical manifests remain original evidence; public-copy SHA values are in MANIFEST. Relocation changes JSON/source hashes. Original workers' integrity gates will therefore require an explicitly reviewed, newly recorded relocation manifest before execution; this preparation does not bypass or silently rewrite those gates.

## 2. A: historical data, model and saved-score route

Data: `data/processed/riwa_synthetic_timeseries.csv`, profile, `data/processed/context_spectral_v3/*_raw_split.csv`, and final `*_w96_s16.npz` plus matching window manifests. Per-run `config.json`, `thresholds.json`, `threshold_metrics.json`, `window_scores.npz`, point CSVs and run summaries accompany the final checkpoint.

The actual generator is `scripts/simulate_fault_scenarios.py`, with `scripts/experiment_utils.py` and the recorded Open-Meteo weather input. `scripts/prepare_context_spectral_v3.py` prepares historical inputs. `scripts/run_context_spectral_v3.py` provides `worker --job`, `search`, and `final --methods ... --seeds ... --protocols ...` entry points; `src/context_spectral_v3/training.py` contains the actual training/score code. The archive includes frozen per-run configurations; do not rerun search to redefine historical choices. Seed 1337 and the original generator numeric environment matter. No generation/retraining command was run here.

For saved-score reevaluation, load the final window cache with `load_window_data` from the historical `data.py`. For validation/test select that split's window IDs; take the corresponding saved `validation_scores`/`test_scores` from `window_scores.npz`. The original `training._point_frame(raw_index, labels, scores, raw_df)` aggregates overlapping occurrences and joins raw metadata. Use the stored `target_fpr_5pct` threshold and historical `metrics.evaluate_point_frame`. Follow the exact run's saved scoring branch for TranAD and other baselines. The point CSVs are convenient for inspection; binary window scores retain precision needed for exact replay. Do not replace this route with rounded CSV-derived AP while claiming byte-level equivalence.

Historical Scenario omitted segments shorter than 96 samples. A intentionally preserves that population and historical event implementation. Its shared TIME-validation selection overlaps other protocols' test populations; this limitation is not fixed by archiving.

## 3. B: canonical reevaluation of A weights

Use `experiments/R1_5_scoring_event_sensitivity/data/<PROTOCOL>/canonical_windows.npz`, `canonical_windows.csv`, raw split manifest, and historical-window mapping. The exact inference/calibration procedure is preserved in `scripts/infer.py` under that experiment; its CLI is `--protocol <PROTOCOL> --gpu <index>`. It loads A's checkpoint and uses the `implementation/canonical` model/scoring/metrics snapshot. **Do not invoke it inside the archive:** it creates run directories and performs inference. The future run must use a separate output location and verified relocated paths.

The stored `components.npz`, `calibration.json`, `validation_points.npz`, `test_points.npz` and `summary.json` permit future score/metric reevaluation without retraining. Canonical point NPZs contain `__metadata_json__` with column order, pandas dtypes, threshold and timestamp encoding; load with `allow_pickle=False`, restore the frame in that order/dtypes, and call the matching `metrics.evaluate_point_frame(frame, threshold, EventRules())`. Do not include `__metadata_json__` as a point column.

The canonical `training.py` defines `_robust_error_scale`, `_mix_scores`, `_point_frame` and `_assert_point_population`. It calibrates on valid normal validation window positions, mixes scores per position, excludes padding, averages overlapping occurrences of each unique raw point, and uses valid unique normal validation points for the 95th-percentile threshold. Right-side last-value extension covers short segments; padding never becomes evaluated raw points. Threshold and event-gap changes affect thresholded/event metrics, not AP. B changes coverage and residual calibration jointly; their individual contributions are not isolated.

R3 `FULL_ALIGNED` rows reuse these components/scores. Its own other conditions, mappings and new train-only weights are supplemental; see `experiments/R3_2_shuffled_context_mechanism/commands/{prepare_inputs,scoring,evaluate_worker,summarize}.py`. Do not call its dispatch scripts merely to verify a download.

## 4. C: formal development and G0/G1

Root: `experiments/R1_1_generator_robustness/formal_v1/`.

- `configs/formal_protocol_frozen.json`, `configs/selected_configs_frozen.json` and `results/development_selection.json` establish the development/final separation. Development generation seed is 2026092321; formal seeds are 2026092311, 2026092312, 2026092313. Training seeds are 42, 52, 62, 72, 82.
- Actual generator snapshot: `implementation/scripts/simulate_fault_scenarios.py`, with the reference source in `provenance/pilot_source_snapshot/scripts/`. `commands/prepare_formal_data.py` exposes `--stage generate|prepare|compare --condition G0|G1|G2 --generation-seed ...` and `--development`. These are recorded entry points, **not an instruction to generate new data during review**. Saved final datasets and worker inputs are provided instead.
- Each dataset's manifest links its master raw data, split/quarantine records and per-protocol `worker_input/{raw.csv,windows.npz,windows_manifest.csv,manifest.json}`. Development worker inputs contain train/val only; formal inputs contain train/val/test. Keep protocol and generation identity together.
- The executed training implementation is **implementation_v2**, not the earlier snapshot. `commands/train_worker_v2.py --job <job.json> --gpu <index> --output <new-attempt-path>` is the actual worker interface. It checks original source, input and protocol manifests, requires an assigned GPU, and refuses existing worker status. This interface is documented, not rerun or certified portable here.
- The worker imports `load_window_data` and `TrainConfig, train_matched_method` from `implementation_v2/src/context_spectral_v3`. Its actual call is `train_matched_method(TrainConfig(**job['train_config']), bundle, raw, run_dir, run_dir/'checkpoint.pt', FORMAL, stage=stage)`. Frozen per-run jobs/configs, selected final checkpoints and outputs are archived. Development intermediate checkpoints and discarded search snapshots are intentionally not needed for reevaluating final checkpoints.
- Saved canonical point NPZs and the matching v2 metrics implementation support the same future NPZ-to-frame reevaluation route as B, using **C's own** files and thresholds. Generation-block aggregation is defined in the archived formal summary scripts/results: compute a mean over five training seeds per generation block, then mean/sample SD across three blocks. Do not pool 15 runs as independent datasets or reuse A/B five-seed SD.

## 5. Validation status

Actually performed here: source hashing; record-based result/checkpoint mapping; static checkpoint ZIP/pickle-opcode inspection without unpickling; safe array-header/string inspection without pickle; public-copy path redaction; Python parsing; deduplication; archive member/hash verification and safe extraction tests. See STATIC_AUDIT.json and ARCHIVE_VALIDATION.json for measured counts.

Not performed here: loading models in PyTorch, inference, generating/rebuilding inputs, retraining, recomputing AP/F1/events, installing the recorded environments, GPU portability/determinism tests, or executing original hash-gated workers after relocation. Existing experimental verification reports describe earlier runs only. A successful archive check is not evidence of new full reproduction.
