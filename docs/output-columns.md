# Native CSV outputs

Run all programs from the repository root, with a **fresh, empty `generated/` directory**. Aner's native writers reject an existing destination. Keep recorded reference artifacts in `evidence/`; do not run into that folder.

```sh
aner check code/ev_charging.aner --strict-declarations
aner run code/ev_charging.aner --max-steps 50000000
aner check code/county_priorities.aner --strict-declarations
aner run code/county_priorities.aner --max-steps 50000000
aner check code/maps.aner --strict-declarations
aner run code/maps.aner --max-steps 50000000
```

The native `SimulationResults.save_csv` writer supplies `iteration,seed,value_0,...` headers. The following dictionaries make those columns explicit. `iteration` is the zero-based exported row order, and `seed` is the derived identity-export task seed. Neither is a predictor. The bootstrap files instead retain the actual simulation iteration and its derived random seed.

Five-digit Washington county FIPS values are represented exactly in numeric output. Original identities remain Strings in `data/county_context.csv`; every Washington county starts with `53`, so numeric output loses no leading zero. County names are preserved in that table and in the semantic console records.

## `generated/county-forecasts.csv` — 39 counties

| Native field | Meaning |
|---|---|
| `value_0` | County FIPS |
| `value_1` | Observed BEV+PHEV registrations, September 2026 |
| `value_2` | Baseline 12-month projected stock |
| `value_3` | Bootstrap p10, assumption-based and uncalibrated |
| `value_4` | Bootstrap p90, assumption-based and uncalibrated |
| `value_5` | Projection with 0.75 × baseline net increase |
| `value_6` | Projection with 1.25 × baseline net increase |
| `value_7` | Mean absolute 12-month drift error across 12 historical origins |
| `value_8` | Mean absolute 12-month persistence error across the same origins |
| `value_9` | Listed public Level 2 ports |
| `value_10` | Listed public DC fast ports, shown separately |
| `value_11` | ACS 2020–2024 renter-occupied / all occupied units |

The program validates the complete prepared panel before saving: 39 unique sorted Washington county identities, at least 48 unique consecutive calendar months and indices, common first and last months, nonnegative stock bounded by all-vehicle totals, and complete bounded county context. The declared input schemas reject malformed or missing required fields.

For each county, the program also checks all 1,000 ordered paths, all three numerical values per path, and each derived seed against a one-worker replay. A failed check stops native execution. Successful console records begin `REPLAY,county_fips,1000,3,seeds_and_values_identical`. These are exact numerical and seed comparisons, not a speedup or forecast-coverage claim.

## `generated/bootstrap-<FIPS>.csv` — 1,000 paths per county

| Native field | Meaning |
|---|---|
| `iteration` | Simulation identity, 0–999 |
| `seed` | Derived seed for that actual simulation |
| `value_0` | Stock after 12 resampled monthly increments |
| `value_1` | Stock after multiplying that sampled net increase by 0.75 |
| `value_2` | Stock after multiplying it by 1.25 |

Four circular blocks of three changes are drawn from the last 24 monthly changes. These outputs retain all draws needed to inspect the empirical p10/p90 values. The main program uses `parallel.local(workers: 4)` and verifies a one-worker replay internally. Its execution-policy declaration can be changed deliberately to an admitted `parallel.cluster(config: "private.cluster")` after following [Aner's cluster guide](https://anerlang.org/docs/cluster). No private cluster configuration is included.

## `generated/scenario-ranks.csv` — 342 scenario/county rows

| Native field | Meaning |
|---|---|
| `value_0` | County FIPS |
| `value_1` | Growth multiplier: 0.75, 1, or 1.25 |
| `value_2` | Renter weight: 0, 0.25, or 0.5 |
| `value_3` | Scenario projected stock |
| `value_4` | Current listed public L2 ports |
| `value_5` | Scenario projected stock / current L2 ports |
| `value_6` | Midrank percentile of that pressure among ranked counties |
| `value_7` | Midrank percentile of renter share among ranked counties |
| `value_8` | `(1-weight) * pressure_percentile + weight * renter_percentile` |
| `value_9` | Descending score rank; exact ties use ascending FIPS |

Rows follow growth setting, renter weight, and ascending county FIPS. Ranking reads the freshly generated native forecast file and verifies its identities and context against the prepared county table. All percentile calculations, scenario scores and ranks execute in `county_priorities.aner`. No external reporting script is required.

## `generated/county-priorities.csv` — 38 finite-ratio counties

| Native field | Meaning |
|---|---|
| `value_0` | County FIPS |
| `value_1` | Observed EV stock |
| `value_2` | Baseline projected EV stock |
| `value_3` | Current listed L2 ports |
| `value_4` | Current listed DC fast ports |
| `value_5` | Renter share |
| `value_6` | Baseline projected EV stock / listed L2 ports |
| `value_7` | Best rank across the nine settings |
| `value_8` | Worst rank across the nine settings |
| `value_9` | Number of settings with rank at most 10 |
| `value_10` | Number of scenario settings, 9 |

Garfield's undefined zero-L2 ratio is excluded from this finite-ratio table and explicitly reported in a `ZERO_L2` console record. Its observed fleet, forecast and zero inventory remain in the complete 39-county forecast file. Do not replace the zero denominator with one or infer that an excluded county has no need.

`STABLE` console records list counties present in all nine top-ten screens. `RESULT`, `SCENARIO` and `PRIORITY` records provide additional named-field console headers and rows. Execution provenance and numeric CSVs remain the durable native artifacts.

## Geographic exports

`maps.aner` exports `generated/stations.svg` and `generated/stations.html` directly from the prepared station inventory. The HTML is a generated interactive visualization, not a prerequisite or a script distributed in the public source archive. Combined L2/DC symbol counts describe inventory rather than interchangeable charging capacity. One zero-port listing is omitted from the map; unknown counts would also be omitted explicitly. No map pack is required for this point layer.
