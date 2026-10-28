# Data sources and snapshot preparation

This example starts from **curated, frozen government CSV inputs**. Aner begins at the typed CSV boundary: it reads the prepared tables, records native scan and transformation provenance, computes forecasts and validation, runs seeded simulations, and produces the county screen and maps. The native example does not automatically download or normalize the upstream services.

The study snapshot was collected on **28 October 2026**. Monthly fleet observations end **September 2026**; charging records describe the retrieved inventory; housing tenure describes **2020–2024**. These observation periods remain distinct throughout the evaluation.

## Source inventory

| Publisher and source | Observation period or geography | Exact source-byte SHA-256 |
| --- | --- | --- |
| [Washington Department of Licensing: county EV history](https://catalog.data.gov/dataset/electric-vehicle-population-size-history-by-county), Socrata `3d5d-sdqb` | Washington, January 2017–September 2026 | `f4c726fcdfa99ae07f562957abcdb448c5bd90c6ba706c769f2c163286b57aed` |
| [DOL source metadata](https://data.wa.gov/api/views/3d5d-sdqb.json) | Snapshot metadata, including definitions and administrative caveats | `fe29ec273bb0f4d845e92aaf209248056c68917096899216cd7919a4fe0865f3` |
| [US Department of Energy AFDC station API](https://developer.nlr.gov/docs/transportation/alt-fuel-stations-v1/all/) | Electric, public, listed-available stations in Washington | `121df9ae194db840a765f4ef4ebce8834202e58352b682b0aa49afcfd6f3494a` |
| [US Census TIGERweb State/County service](https://tigerweb.geo.census.gov/arcgis/rest/services/TIGERweb/State_County/MapServer/1) | Washington county geometry and geographic identifiers, EPSG:4326 output | `f05ec8d392757bc3b2c25be78c0215eed20beac8b52976b00c21acd7ed956d60` |
| [US Census ACS table B25003: housing tenure](https://www2.census.gov/programs-surveys/acs/summary_file/2024/table-based-SF/data/5YRData/acsdt5y2024-b25003.dat) | 2020–2024 five-year estimates; Washington counties | `68e963e1ed60fcf6b0658579cefc6498b7a9eb780b9f3e87fd1b9775c4d4ac6c` |

These hashes identify the **original, uncompressed download bytes**. When an upstream download is retained as `data/raw/<name>.gz`, decompress it before comparing its content hash. The gzip container has a different hash. [The source manifest](../data/source_manifest.json) preserves the exact URLs, query parameters, byte counts, retrieval timestamps, transformations, coverage, and prepared-table hashes. It is the authoritative download record for the original analytical snapshot. The later cartographic display update has a separate [map-update manifest](../evidence/map-update-manifest.json).

An upstream portal can revise its records. Downloading the same URL later does not necessarily reproduce these bytes. A refreshed collection is a new source vintage and should receive a new manifest and evaluation.

## Prepared tables and rules

### Registered fleet: `data/ev_monthly.csv`

The DOL download retains Washington records and dates no later than the study cutoff. For each county and month, the preparation:

1. Checks that `electric_vehicle_ev_total` equals battery-electric plus plug-in-hybrid vehicles.
2. Checks that total vehicles equals electric plus non-electric vehicles.
3. Requires one distinct record for each **Passenger** and **Truck** primary-use category.
4. Sums those two categories into registered EV stock and its all-vehicle denominator.
5. Joins the source county name to the official five-digit Census county FIPS identifier.
6. Requires a complete consecutive history for every included county.

The resulting panel contains **4,563 county-month rows: 39 counties × 117 months**. A registered stock is not new vehicle sales, trips, vehicle miles traveled, or electricity demand. DOL notes that county assignment and administrative revisions can change previously reported counts; vehicles without an assigned county are outside its final county counts. The series is not forced to increase monotonically.

### Charging inventory: `data/stations.csv`

The AFDC query selects `state=WA`, `fuel_type=ELEC`, `access=public`, and `status=E`. The frozen response contains **3,225 station listings**. Station identifiers are retained as source keys.

Port totals come from the explicit `ev_charging_units` inventory, grouped by charging level:

- Level `2` contributes public Level 2 port counts.
- Level `dc_fast` contributes public DC fast port counts.
- Each unit must contain an explicit nonnegative integer port count.
- The sums must agree with the legacy level-specific fields wherever those fields are present.

A missing legacy count is not automatically converted to zero. A zero is supported by the explicit unit inventory having no units of that charging level, or by explicit zero port counts. A listing without a sufficient unit inventory would require resolution before entering the screen.

The retained inventory has **6,739 Level 2 ports and 2,428 DC fast ports**. These technologies are counted separately for interpretation. The station map combines their counts only to show inventory; the combined total does not imply interchangeable charging capacity. One listing has zero combined L2/DC ports and is omitted from that point layer, which displays 3,224 locations.

AFDC's listed-available status does not measure current uptime. **48 public listings also have a restricted-access flag**. Hours, fees, connector compatibility, access conditions, and reliability require separate local review. Current records and `open_date` cannot reliably reconstruct historical supply or closures.

The official [AFDC state-download page](https://afdc.energy.gov/corridors) also offers a Washington **CSV without filters**. The ordinary corridor download applies additional suitability restrictions and is not an equivalent replacement for the complete inventory.

### County assignment: `data/station_county_audit.csv`

Station longitude and latitude are assigned to official Census county polygons in EPSG:4326, with polygon holes and multipart geometry respected. The preparation requires exactly one county match for every retained station. The frozen study has **no unmatched station records**; it does not silently assign an unmatched point to the nearest county.

The audit table preserves each station ID, assigned county FIPS, last-confirmed date, listed status, and restricted-access flag. County totals in `data/county_context.csv` are sums of the matched station-level port records. County boundaries are administrative study units; they do not measure road access, travel time, or cross-boundary charging.

### Cartographic display: `data/wa-counties.geojson`

The station map uses **2025 U.S. Census shoreline-clipped cartographic county boundaries**, bundled as a small GeoJSON file. It includes all 39 Washington county FIPS keys. The [national 1:500,000 county source archive](https://www2.census.gov/geo/tiger/GENZ2025/shp/cb_2025_us_county_500k.zip) has SHA-256 `aa976c00b181939755d0da757f4c7c2dc0103c3b3b4530fb2a91c2bb62fc777c`. These boundaries make the coastline and county divisions visible; source vintage is separate from the October 2026 station inventory.

The display file retains the hash-pinned Aner `usa.wa.counties` catalogue's geometry coordinates. Its authoring recipe uses shared-edge-preserving Visvalingam–Whyatt coverage simplification with a **0.0001° tolerance** and an offline EPSG:4269-to-EPSG:4326 transform. The case preparation renames the catalogue's five-digit `code` property to `GEOID` and attaches names and centroids from the retained county source; it does not change geometry coordinates. The [map-update manifest](../evidence/map-update-manifest.json) records the archive, catalogue, display-file, source, runtime, and export hashes.

This file is **display geometry only**. The original, more detailed TIGERweb snapshot in `data/raw/census_counties.json.gz` remains the source used for station-to-county assignment and all retained county totals. The display update changes no station assignments, forecasts, ranks, or bootstrap samples. Shoreline-clipped display shapes are generalized thematic boundaries, rather than legal, navigation, or survey geometry.

[The map source](../code/maps.aner) reads the bundled GeoJSON and station CSV directly. No optional map pack or remote tile service is required. Native exports are static SVG/HTML. The [website viewer](https://anerlang.org/applications/ev-charging/stations.html) adds local pan, zoom, county selection and station inspection; [its builder](https://github.com/abastias/AnerWeb/blob/codex/ev-application-publication/scripts/build-ev-map-viewer.mjs) and [browser controls](https://github.com/abastias/AnerWeb/blob/codex/ev-application-publication/scripts/ev-map-viewer.js) are maintained in the website repository.

### Renter context: `data/acs2024_wa.csv`

The official keyless Census bulk table is filtered to county-level `GEO_ID` values beginning `0500000US53`, producing all 39 Washington counties. The relevant cells are:

| ACS cell | Meaning |
| --- | --- |
| `B25003_E001` | Occupied housing units estimate |
| `B25003_M001` | Occupied housing units margin of error, 90% confidence |
| `B25003_E003` | Renter-occupied housing units estimate |
| `B25003_M003` | Renter-occupied housing units margin of error, 90% confidence |

Renter share is `B25003_E003 / B25003_E001`. The numerator and denominator margins of error are retained. The screening does not propagate their sampling uncertainty into formal rank confidence intervals.

For example, Douglas County (`53017`) has **4,892 renter-occupied units / 15,923 occupied units = 30.7229%**, with source margins of error of 440 and 330 units respectively. Renter share is area-level housing context; it does not identify which EV drivers lack private parking or home charging.

## Historical policy annotations

`data/policy_events.csv` retains jurisdiction, event date, scope, and official source URL. Relevant dates include Washington's purchase sales/use tax exemption cutoff of **31 July 2025**, the federal clean-vehicle acquisition-credit cutoff of **30 September 2025**, and the **15 March 2024** effective date listed for the EV-ready building-rule amendment, WAC 51-50-0429. Existing qualifying leases and previously acquired vehicles can have distinct continuation or delivery rules. See the event table and its [Washington DOR](https://dor.wa.gov/taxes-rates/tax-incentives/tax-incentive-programs), [IRS](https://www.irs.gov/clean-vehicle-tax-credits), and [Washington rule](https://app.leg.wa.gov/wac/default.aspx?cite=51-50-0429) sources for scope.

The events supply historical context. The example does not estimate a causal incentive effect, calculate a buyer's eligibility, or infer pollution reductions.

## Follow the evidence

A recommendation can be checked through the same county FIPS key in the prepared monthly panel, county context, ACS cells, station audit, scenario rows, and retained decision trace. Native scan and filter receipts identify the CSV inputs used by Aner. The retained runtime identity, code hashes, seeds, parameters, and output hashes bind the numerical computation to that snapshot.

The nine declared parameter combinations are three growth multipliers (0.75, 1, 1.25) crossed with three renter weights (0, 0.25, 0.5). For this snapshot, changing the shared growth multiplier does **not** change top-ten membership or order at a fixed renter weight. The nine combinations therefore yield **three distinct shortlist rankings**. Frequency in the top ten is a sensitivity result within these settings, not a probability or nine independent validations.

The six stable counties are Douglas, Cowlitz, Snohomish, Kitsap, Grant, and Thurston. Garfield has no listed public L2 ports and is flagged separately rather than assigned an artificial denominator. Selecting construction sites requires additional evidence about local demand, private charging, observed utilization, traffic, electricity capacity, costs, and community priorities.

Source publishers retain their attribution and applicable terms. This document does not assign a new license to government datasets or to Aner's project marks.
