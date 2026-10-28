# Open research supporting the application

This example addresses three questions: county fleet growth over twelve months; public L2 inventory pressure with renter context; and stability under explicit growth and equity assumptions. It is a compact reproducible planning screen rather than a station-sizing or causal policy model.

- **Coenen, Aemmer, Malarkey and MacKenzie (2023), _Analytic Tools for Locating, Sizing, and Evaluating Electric Vehicle Charging Stations_.** The Washington-specific report connects adoption forecasts, vehicle stock turnover, and highway charging demand. It establishes the practical planning problem and shows why detailed charging demand and station sizing require more than a county ratio. [Open WSDOT report, WA-RD 930.1](https://www.wsdot.wa.gov/research/reports/fullreports/930.1.pdf).
- **Lou, Shen, Niemeier and Hultman (2024), _Income and racial disparity in household publicly available electric vehicle infrastructure accessibility_.** The national household analysis motivates studying distributional differences beyond infrastructure totals. County renter share in this example is a limited contextual proxy; it does not replicate household accessibility. [Open Nature Communications article](https://www.nature.com/articles/s41467-024-49481-w).
- **Bhatt, Giang, Javed and Kandlikar (2024), _Equitable charging infrastructure for electric vehicles: access and experience_.** Its framework includes proximity, reliability, affordability, payment, safety, and surrounding services. These dimensions explain why the shortlist needs local review. [Open Progress in Energy article](https://doi.org/10.1088/2516-1083/ad4b8f).

The declared renter weights are decision preferences. The shared growth multipliers are sensitivities, not estimated responses to an incentive. The nine parameter combinations are not independent validations and do not imply a probability that a county requires investment.

## Policy and air quality extensions

The versioned policy table includes Washington's vehicle purchase sales/use tax exemption cutoff, federal clean-vehicle acquisition-credit cutoff, and the EV-ready building rule's listed amendment date. The application preserves their dates and sources but does not estimate their causal effects. City ordinances can be added with their jurisdictions, effective dates, and permit applicability.

An air-quality extension could test associations between adoption and ambient NO2 using [EPA AirData](https://aqs.epa.gov/aqsweb/airdata/download_files.html), with consistent monitor definitions and explicit geographic coverage. It would need season, weather, traffic, wildfire, and broader trend controls. Missing monitors do not establish clean air. An ecological county association would not identify individual health benefits or an incentive's causal effect.

**Garcia et al. (2023), _California's early transition to electric vehicles: observed health and air quality co-benefits_**, motivates this extension, without providing a transferable Washington effect size. [Open manuscript](https://pmc.ncbi.nlm.nih.gov/articles/PMC10465173/pdf/nihms-1871751.pdf).

A versioned [Washington Environmental Health Disparities map](https://doh.wa.gov/data-and-statistical-reports/washington-tracking-network-wtn/washington-environmental-health-disparities-map) could supply spatial burden context. Preserve indicator vintages and use appropriate population weighting when combining tract data. A cumulative disparity rank is not measured emissions avoided by a charger.
