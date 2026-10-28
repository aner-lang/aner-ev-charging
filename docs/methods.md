# Forecasting and county screening

The included government tables are a frozen, curated input snapshot. All executable analysis distributed in this repository is Aner source. The analysis starts at typed CSV ingestion; source collection and geographic preparation are documented in [data sources](data-sources.md).

## Registered fleet and historical evaluation

The fleet is registered BEV+PHEV stock in the DOL Passenger and Truck primary-use categories. It includes net registrations, removals, and movement; it is not a new-sales series. For each county, the baseline projects twelve months from the last stock level by adding half the preceding 24-month net change. Negative future stocks are floored at zero.

Twelve rolling origins, October 2024–September 2025, each use only their preceding 24 changes and predict a target twelve months ahead. Mean absolute error is reported in vehicles beside persistence, which predicts unchanged stock. The statewide summary is the equal-weight average of county MAEs. Overlapping horizons make these errors dependent. The future September 2027 target remains unobserved.

## Repeated simulation and execution

For every county, 1,000 native tasks resample four circular blocks of three consecutive monthly changes from its latest 24 changes. Twelve sampled changes yield an alternative future stock. Sorted paths provide nearest-rank p10 and p90 values: the 100th and 900th of 1,000 samples. These are uncalibrated assumption ranges, not a guaranteed 80% prediction interval.

The declared seed is `20261028 + zero-based sorted county index`. Each task receives its derived seed. Ordered results allow one-worker and four-worker execution to be checked sample by sample. Execution equality tests the calculation and random-stream assignment; it does not establish future forecast validity or a performance gain.

## Explicit priority rule

The charging-pressure indicator is projected BEV+PHEV stock divided by currently listed public Level 2 ports. Current inventory is held fixed; the ratio does not predict future charger supply or measured congestion. DC inventory is reported separately because the combined registered stock does not measure compatible DC fast charging demand.

Each scenario multiplies the projected net increase by 0.75, 1, or 1.25, then recomputes county pressure. Pressure and renter share become midrank percentiles among the 38 counties with positive L2 inventory. A renter weight of 0, 0.25, or 0.5 produces:

`score = (1 − weight) × pressure percentile + weight × renter percentile`

Higher scores rank first. Equal scores use ascending county FIPS. Garfield has zero listed public L2 ports and is flagged separately; no artificial denominator is assigned. The weights encode a declared preference, not a learned optimum. ACS source margins of error are retained but not propagated into rank confidence intervals.

In this snapshot, the shared growth multipliers do not change the top-ten order or membership at a fixed renter weight. Nine combinations therefore yield three distinct shortlist rankings. The six persistent candidates are Douglas, Cowlitz, Snohomish, Kitsap, Grant, and Thurston. Kitsap reaches rank ten at the strongest renter weight and is less secure at the cutoff than Douglas or Cowlitz. A broader robustness study should test county-specific adoption, proposed charging additions, uncertain inventory, ACS uncertainty, neighborhood access, and other decision objectives.

## Map and audit boundaries

The native map displays station coordinates over bundled 2025 shoreline-clipped Census county boundaries, with combined listed L2/DC inventory encoded at each station. County fill supplies geographic context and does not encode a county statistic. The technologies are not interchangeable capacity. One zero-combined-port listing is omitted; 3,224 points remain. The current AFDC inventory has 48 public listings carrying restricted-access flags. Listed-available status does not establish observed uptime, opening hours, affordability, or unrestricted access.

The display geometry is separate from the original TIGERweb polygons used for the frozen station assignments. The map update preserves every station assignment, forecast, rank, and simulation sample. [The map-update manifest](../evidence/map-update-manifest.json) records the checked map source, boundary preparation, runtime identity, input hashes, and [native SVG](../evidence/stations-counties.svg). Native SVG/HTML exports retain the static authored view. The [website viewer](https://anerlang.org/applications/ev-charging/stations.html) adds local pan, zoom and county/station selection to that view; interaction changes the presentation and does not rerun the analysis. [The published display manifest](../evidence/map-browser-display-manifest.json) records the native map and browser presentation hashes.

Native CSV scans and county filters print their recipes and receipts. Numerical Lists form an explicit lineage boundary; retained source/input/output hashes, execution settings, logs, and identifiers bind the later computations to the evaluated snapshot. The example does not claim automatic native lineage for arbitrary arithmetic or upstream API collection.

A recommendation supports local investigation. A construction decision also needs demand, road access, land, electrical capacity, utilization, costs, and community input. Policy dates annotate the context; the analysis estimates no causal tax, regulatory, health, or pollution effect.
