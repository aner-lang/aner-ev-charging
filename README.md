<a href="https://anerlang.org#gh-dark-mode-only">
  <img src="assets/aner-logo-dark.svg#gh-dark-mode-only" alt="Aner programming language" width="300">
</a>
<a href="https://anerlang.org#gh-light-mode-only">
  <img src="assets/aner-logo.svg#gh-light-mode-only" alt="Aner programming language" width="300">
</a>

# Where should Washington invest in public EV charging next?

An application of [Aner](https://anerlang.org), a programming language for data science and scientific computing. Use public observations to forecast county EV fleets, inspect charging pressure, and trace an investment shortlist back to its data and code.

**Executable code in this repository is written in `.aner` and `.anernb`.** The remaining files are documentation, public data, images, and recorded evidence. The example runs from the included frozen CSV inputs without a server or an API key.

[Aner website](https://anerlang.org) · [Language documentation](https://anerlang.org/docs) · [Applications](https://anerlang.org/applications)

Author: **Alfonso G. Bastias, Ph.D.**

## Three questions

1. How much could each county's registered EV fleet grow over the next 12 months?
2. Where does projected adoption put pressure on the listed public Level 2 charging inventory?
3. Which counties stay on the shortlist when growth assumptions and renter weights change?

## What this snapshot shows

The study covers **39 Washington counties and 117 months**, January 2017–September 2026. Charging inventory was collected on 28 October 2026: **3,225 public station listings, 6,739 Level 2 ports, and 2,428 DC fast ports**. Housing tenure uses Census ACS 2020–2024 estimates.

A transparent 24-month drift baseline projects **332,818.5 registered EVs in September 2027**, from **291,499**. Across twelve historical forecast origins, mean county absolute error is **357.7 vehicles**, versus **1,113.1** for unchanged stock: **67.9% lower error**, with a better result in 38 of 39 counties. The future projection is conditional on the declared trend assumption.

**Douglas, Cowlitz, Snohomish, Kitsap, Grant, and Thurston** remain in the top ten across all nine growth/weight combinations. These are candidates for further local assessment. In this snapshot, the three shared growth multipliers leave the top-ten order unchanged at each renter weight, so nine combinations produce **three distinct shortlist rankings**. Frequency in the shortlist is a sensitivity result, not a probability.

## Run the Aner code

Start with [the forecast program](code/ev_charging.aner), [native county ranking](code/county_priorities.aner), [station maps](code/maps.aner), or [the Aner notebook](code/ev_charging.anernb).

This case was tested with the local **Aner 0.0.4 development runtime, Debug build**, identified in [the run manifest](evidence/run-manifest.json). It requires the typed CSV/provenance, Date/Result, parallel seed/value/CSV-export, and geographic-export APIs used by these sources. Compatibility with the published downloads has not been separately verified; a shared version number does not establish identical capabilities. Start with the current [downloads](https://anerlang.org/downloads) and [documentation](https://anerlang.org/docs), then strict-check all three programs with a runtime providing those APIs.

Run from the repository root, where `data/` and `code/` are visible:

```sh
mkdir generated
aner check code/ev_charging.aner --strict-declarations
aner check code/county_priorities.aner --strict-declarations
aner check code/maps.aner --strict-declarations
aner run code/ev_charging.aner --max-steps 50000000 > generated/forecast.log
aner run code/county_priorities.aner --max-steps 50000000 > generated/priorities.log
aner run code/maps.aner --max-steps 50000000
```

Use a **fresh `generated/` directory** for each run; native exports refuse to overwrite existing files. The checked runtime identity and source/input/output hashes are retained in [the evaluation manifest](evidence/evaluation-manifest.json). Binaries sharing a version string can differ in development capabilities; use the recorded identity and the current language documentation when checking compatibility. The runtime itself is not included in this repository.

The notebook in `code/` presents the workflow with explanatory cells and the same checked source. The CLI runs `.aner` files; `.anernb` opens in a compatible Aner notebook editor and uses its configured execution limits. Open it from the repository workspace so its relative CSV paths resolve from the repository root. Recorded results live in `evidence/`; newly generated results are kept separate.

## What the native programs demonstrate

- **Typed transformations:** bounded CSV ingestion and immutable county filtering with native provenance and execution receipts.
- **Parallel computation:** independent moving-block bootstrap tasks, declared seeds, ordered samples, and one-versus-four-worker verification.
- **Decision rules:** percentile normalization, explicit growth multipliers and renter weights, county ranks, and a separate flag for zero-port counties.
- **Maps:** actual station positions exported through Aner Plot without a required map pack. Optional county boundaries use the appropriate installed pack.
- **Traceability:** county identifiers, upstream snapshot hashes, runtime identity, code, parameters, receipts, samples, and derived results stay inspectable.

The parallel boundary is visible in the source:

```aner
let paths = parallel.repeat(iterations: 1000, task: bootstrap_stock,
    parameters: List(initial, changes), execution: execution,
    seed: 20261028 + county_index, max_bytes: 1048576)
```

For optional cluster execution, follow [Aner's cluster guide](https://anerlang.org/docs/cluster) and replace the explicit local policy with `parallel.cluster(config: "private.cluster")` after enrolling compatible workers. Keep real machine settings outside the repository. The retained evaluation uses local workers; no multi-computer speedup is claimed.

## Follow a recommendation

For Douglas County, FIPS **53017**, DOL stock grows from **451** in September 2024 to **697** in September 2026. Aner computes `697 + 0.5 × (697 − 451) = 820`. Four listed Level 2 ports give `820 / 4 = 205` projected EVs per listed L2 port. ACS provides **4,892 renter-occupied / 15,923 occupied housing units = 30.72%**. The declared scenario rule places Douglas first or second.

The same county key connects the monthly data, station audit, ACS cells, scenario results, and native simulation samples. Start with [data preparation and source records](docs/data-sources.md), [the method](docs/methods.md), and [the evidence directory](evidence/).

Inputs are **curated government snapshots**. The Aner example starts at typed CSV ingestion; it does not automatically download or normalize the upstream APIs. Exact source bytes and preparation rules remain available for audit.

## Repository layout

```text
code/       Aner programs and an Aner notebook
assets/     Official Aner logo variants
data/      Frozen CSV inputs, policy records, provenance, raw data snapshots
docs/      Methods, source definitions, and open research
evidence/  Recorded native outputs and verification manifests
```

`generated/` is created locally for new outputs and is excluded from version control. Runtime binaries and private cluster settings are kept outside this repository.

## Interpretation and research

This is a county-level planning screen. It does not measure queues, observed uptime, home charging, electrical capacity, required port counts, or a causal incentive effect. Level 2 and DC fast inventory remain separate for interpretation; BEV+PHEV stock is not DC-compatible charging demand. Renter share is area-level context. Bootstrap ranges are assumption-based and have not been calibrated for future coverage.

The design is informed by open work from [Coenen et al., WSDOT](https://www.wsdot.wa.gov/research/reports/fullreports/930.1.pdf), [Lou et al., Nature Communications](https://www.nature.com/articles/s41467-024-49481-w), and [Bhatt et al., Progress in Energy](https://doi.org/10.1088/2516-1083/ad4b8f). See [research support](docs/research.md) for their role and an air-quality extension.

The Aner name and logos identify the [Aner project](https://anerlang.org). Source datasets retain their original attribution and applicable terms; their publishers' rights are separate from the project marks.
