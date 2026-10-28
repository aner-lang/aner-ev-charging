# Recorded native evidence

This directory retains the successful frozen evaluation. New runs write to a fresh `generated/` directory rather than overwriting this reference.

The source hashes in `run-manifest.json` identify the [original evaluated revision](https://github.com/aner-lang/aner-ev-charging/tree/0c52e4deba2dd0080f5a82ee0190c69666750cd1). That historical manifest remains unchanged. Later revisions update notebook explanatory text and the map display program. The notebook executable cells, forecast/ranking programs, original analytical inputs, and frozen numerical outputs remain unchanged. [The public package manifest](../PUBLIC_MANIFEST.json) records current file hashes; [the separate map-update manifest](map-update-manifest.json) records the new map rendering.

- `county-forecasts.csv`, `scenario-ranks.csv`, `county-priorities.csv`, and `bootstrap-*.csv` are native Aner CSV exports. Their exact field meanings are in [output columns](../docs/output-columns.md).
- `forecast.log` and `priorities.log` retain execution provenance, native replay records, and named `RESULT`, `SCENARIO`, and `PRIORITY` console rows.
- `county_results.csv`, `scenario_ranks.csv`, and `county_priorities.csv` are readable copies of those named console records with their record prefixes removed. Their numerical values come from the native programs.
- `decision_trace.csv` connects each county to the same source keys, station audit, bootstrap sample, and retained source-manifest hash. Garfield remains an explicit zero-L2 flag.
- `run-manifest.json` binds exact source/input/output bytes to runtime identity, native commands, parameters, and seeds. `verification.json` records the replay and arithmetic parity checks; `evaluation-manifest.json` binds these records to the source snapshot.
- `stations.svg` is the original native point map retained with the historical evaluation.
- [`stations-counties.svg`](stations-counties.svg) is the updated native map with all 39 Washington counties and 3,224 station points. [`map-update-manifest.json`](map-update-manifest.json), [`map-update-check.log`](map-update-check.log), and [`map-update.log`](map-update.log) bind the checked source, display geometry, runtime, and native exports. The generated native HTML is static; its hash is recorded, but that HTML is not distributed in this repository.
- The [website station viewer](https://anerlang.org/applications/ev-charging/stations.html) adds browser pan, zoom, county selection, and station inspection. Its [builder](https://github.com/abastias/AnerWeb/blob/codex/ev-application-publication/scripts/build-ev-map-viewer.mjs) and [browser controls](https://github.com/abastias/AnerWeb/blob/codex/ev-application-publication/scripts/ev-map-viewer.js) live in the website repository. Website presentation changes do not change the frozen analysis.

The recorded outputs support a planning screen, not a construction specification, measured congestion estimate, causal policy effect, or calibrated future probability.
