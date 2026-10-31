# Archive status — review candidate, 2026-10-29

Not uploaded. No public repository created. No DOI obtained or reserved. No manuscript or reviewer response edited. No training, inference, data generation or metric recomputation performed in this task.

## Included coverage

- A: all 60 Table-4 main-comparison final weights (four methods × three protocols × five seeds).
- B: all 15 WC-CATCH Full-aligned evaluations reference the same A weights; 30 canonical CATCH/RAW companions also reference A. R3 FULL_ALIGNED is an alias, not new training. The local manuscript assistant has confirmed, from the author-provided second-round submission ZIP, that Table 4 is `tab:main_results` (Scenario WC-CATCH AP 0.167) and Table 8 is `tab:shuffle_mechanism` (Full aligned Scenario AP 0.1844). This server has not independently read that ZIP or verified its hash or full generation chain. The existing B experimental-provenance mapping is retained.
- C: all 270 final G0/G1 checkpoints and corresponding formal input/split/config/saved-score artifacts. Protocol-specific development choices and accepted development-run evidence retained. No intermediate training snapshots copied.
- Supplemental synthetic evidence: historical other baselines/ablations, R3 train-only/shuffle diagnostics, R2 lambda and score-mixture sensitivity records, R1 context-relation shift, and partial G2. G2 has only 24 archived completed runs and is not part of formal C. Per-run RESULTS_MAP enumerates selected weight-bearing/evaluation conditions; additional fixed-weight sensitivity grids are linked through their original experiment CSVs/manifests rather than being relabeled new training runs.

See CHECKPOINT_COVERAGE.csv for counts. A/B/C require 330 distinct final weight paths in total. All included checkpoints are original bytes; content aliases never create another model or training seed.

## Scope and exclusions

The core release supports inspection and future reevaluation of A/B/C and documented selection inputs. Supplement volumes extend this to the selected synthetic controls. This is **not a complete dump of every project artifact**: excluded intermediate/search/development weights, redundant old ZIPs, dispatch logs, raw chat/task attachments, private telemetry, real-data payloads, and 15 supplementary IsolationForest joblib checkpoints are listed in CANDIDATES.csv. The latter require a safe metadata review; no unsafe deserialization was attempted. One historical `audit/training_source/tail.py` fragment does not parse independently and is withheld; complete executable training sources are retained. This does not remove an A/B/C checkpoint or data dependency.

Initial scope census: 31,081 files, about 12.15 GiB; final selected/deduplicated sizes are in STATIC_AUDIT.json and ARCHIVES.csv. Code includes split manifests, freeze/selection records, result summaries and relevant source snapshots. Core data/checkpoint volumes plus code form the minimum **selected-result evidence core**, not a claim that every pipeline is executable without relocation review. Supplements are the fuller selected synthetic evidence set; neither tier reproduces excluded real-data conclusions or all discarded training attempts.

## Static checks and boundaries

Static inspection examined 579 tensor checkpoint files without unpickling, parsed Python source, checked source SHA-256 before copying, checked array types/strings with pickle disabled, sanitized private paths/host identifiers, and deduplicated public bytes. Public originals remain untouched. STATIC_AUDIT.json lists holds and transformations; PUBLIC_COPY_CHANGES.csv links original/public hashes. The automatic credential scan uses documented high-confidence patterns and scoped source selection; no credential values are printed or included in its reports. It is not a formal security proof.

Actual archive validation results are delivered as **external ARCHIVE_VALIDATION.json**, generated after the archives are finalized, to avoid circular self-hashing. Every volume also contains its own exact member/hash manifest. MANIFEST.csv inventories scientific logical files and aliases; top-level documentation and tools are covered by the per-volume manifest. MANIFEST does not attempt to hash itself.

## Open decisions / unverifiable items

1. Authors must choose licenses for their code, generated data, weights and documentation and confirm ownership. No root LICENSE was invented. Resolve local CATCH adaptation permissions, missing upstream root LICENSE, and other baseline lineage/attribution before uploading. Retain existing third-party notices and Open-Meteo CC BY 4.0 attribution.
2. The local manuscript assistant has confirmed, from the author-provided second-round submission ZIP, that Table 4 is `tab:main_results` (Scenario WC-CATCH AP 0.167) and Table 8 is `tab:shuffle_mechanism` (Full aligned Scenario AP 0.1844). This server has not independently read that ZIP or verified its hash or full generation chain. The existing B experimental-provenance mapping is retained.
3. Exact original environment/source binding is incomplete for some historical baselines (including missing TranAD Time seed42 stdout and complete historical dependency/time/source hash records). Current dependency metadata does not repair that absence.
4. Review relocation of private absolute paths. Historical hash gates remain original evidence; modified public text will not satisfy them automatically. Reissue a reviewed relocation manifest and validate the workers in a separate environment before claiming a turnkey end-to-end reproduction.
5. Choose final public scope, authors/ORCIDs, repository/deposit names and release version. Include all core volumes if advertising A/B/C checkpoint coverage. Decide whether separately licensed real-data evidence and the withheld joblib checkpoints belong in a later supplement.
6. No new GPU/model loading, score rerun, generation, training, deterministic replay, clean-environment installation or public-download test has been performed. Prior validation reports are historical evidence, not work rerun during packaging.

Packaging helper interruptions/compatibility fixes (NumPy header-reader API, then optimized static scanning) concern this archive builder only; no scientific computation or result was changed. The final archive validation record, not an intermediate build log, defines completion.

The extraction/hash validation result is external and definitive: ARCHIVE_VOLUMES.csv and ARCHIVE_VALIDATION.json. No end-to-end scientific reproduction was executed.


归档完成状态（仅外置交接补记，包内副本保持冻结）：17卷，5108285022字节，全部通过、失败0。详见 [DELIVERY.md](DELIVERY.md) 与 [ARCHIVE_VOLUMES.csv](ARCHIVE_VOLUMES.csv)。未公开、无DOI、未执行端到端复现。
