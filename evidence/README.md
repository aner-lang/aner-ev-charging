# Recorded native evidence

This directory retains the successful frozen evaluation. New runs write to a fresh `generated/` directory rather than overwriting this reference.

The source hashes in `run-manifest.json` identify the [evaluated revision](https://github.com/aner-lang/aner-ev-charging/tree/0c52e4deba2dd0080f5a82ee0190c69666750cd1); the notebook's explanatory text was revised afterward. Its executable cells, the `.aner` programs, and these recorded outputs are unchanged. [The public package manifest](../PUBLIC_MANIFEST.json) records the current file hashes.

- `county-forecasts.csv`, `scenario-ranks.csv`, `county-priorities.csv`, and `bootstrap-*.csv` are native Aner CSV exports. Their exact field meanings are in [output columns](../docs/output-columns.md).
- `forecast.log` and `priorities.log` retain execution provenance, native replay records, and named `RESULT`, `SCENARIO`, and `PRIORITY` console rows.
- `county_results.csv`, `scenario_ranks.csv`, and `county_priorities.csv` are readable copies of those named console records with their record prefixes removed. Their numerical values come from the native programs.
- `decision_trace.csv` connects each county to the same source keys, station audit, bootstrap sample, and retained source-manifest hash. Garfield remains an explicit zero-L2 flag.
- `run-manifest.json` binds exact source/input/output bytes to runtime identity, native commands, parameters, and seeds. `verification.json` records the replay and arithmetic parity checks; `evaluation-manifest.json` binds these records to the source snapshot.
- `stations.svg` is the retained native point map. The interactive HTML is generated locally by `maps.aner` and is not distributed as source code in this archive.

The recorded outputs support a planning screen, not a construction specification, measured congestion estimate, causal policy effect, or calibrated future probability.
