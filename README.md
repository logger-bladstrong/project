# S&P 500 ESG Data Pipeline and Crash-Risk Warning System

This repository builds a reusable S&P 500 research pipeline around one main R Markdown notebook: [sp500_esg_crash_risk_warning.Rmd](sp500_esg_crash_risk_warning.Rmd). The notebook assembles a 2012-2023 firm-year panel that combines price features, a snapshot ESG dataset, and EDGAR 10-K text-based ESG signals, then produces data-usability audits and crash-risk warning outputs for one-year and two-year horizons.

## Project Scope

The current project has three connected layers:

1. Data foundation: build the current S&P 500 universe and merge yearly price and ESG data.
2. Usability audit: measure coverage, missingness, and sample usability by year and sector.
3. Risk warning outputs: create one-year and two-year machine-learning datasets, cross-validation summaries, external-test metrics, and company-level warning tables.

The pipeline is centered on the current constituent universe, so the resulting historical panel still carries survivorship bias.

## Results Summary

We trained two model families — probability random forest (ranger) and weighted logistic regression — across three nested feature sets (Price Baseline, Price + Snapshot ESG, Price + Full ESG) to predict whether an S&P 500 firm would experience a stock-price crash within the next two years. Models were validated via rolling feature-year cross-validation (2018--2022) and evaluated on a held-out external test covering feature year 2023 with crash outcomes observed over 2024--2025 (n = 483 firms, 47.4% crash rate).

**ESG signals materially improved crash prediction.** The champion model — Price + Full ESG with probability random forest — achieved a ROC-AUC of 0.66, PR-AUC of 0.65, and F0.5 of 0.61 on the external test set. The same model family on Price Baseline alone reached only 0.52 ROC-AUC and 0.50 PR-AUC, meaning the addition of ESG features (snapshot ratings plus EDGAR 10-K text-derived keyword intensities) delivered gains of 14 and 15 percentage points respectively. At the optimized decision threshold, the champion model identified 18 of the 20 highest-risk firms correctly (90% top-20 hit rate) and achieved 0.82 precision among top-10-ranked firms.

**Random forest consistently outperformed weighted logistic regression** across all feature sets, and the full ESG feature set outperformed both the price-only and snapshot-ESG variants. The external-test confusion matrix shows the champion model flagged 247 of 483 firms as high risk, with 148 true positives and 99 false positives, while missing 81 crashes. Snapshot ESG alone provided a moderate lift over the price baseline, but the largest performance jump came from adding EDGAR text-derived ESG signals on top of snapshot ratings. These results suggest that ESG disclosures — particularly those extracted from mandatory 10-K filings — contain forward-looking information not fully captured by historical price patterns alone. All model outputs should be interpreted as screening signals, not investment recommendations.

## Repository Structure

- [sp500_esg_crash_risk_warning.Rmd](sp500_esg_crash_risk_warning.Rmd): main notebook and end-to-end pipeline.
- [sp500_esg_crash_risk_warning.html](sp500_esg_crash_risk_warning.html): rendered HTML report generated from the notebook.
- [data/raw](data/raw): local source files required to run the project on any machine using relative paths only.
- [data/derived](data/derived): derived master tables, annual features, crash labels, and modeling panels.
- [output](output): coverage summaries, usability audit tables, and machine-learning results.
- [DATA_DICTIONARY.md](DATA_DICTIONARY.md): detailed dictionary for the CSV files currently stored in the repository.

## Main Outputs

### Data Foundation

- [data/derived/sp500_constituents_current.csv](data/derived/sp500_constituents_current.csv): current S&P 500 company master table with identifiers, sectors, and snapshot ESG fields.
- [data/derived/sp500_price_daily_2011_2023.csv](data/derived/sp500_price_daily_2011_2023.csv): filtered daily price history used to build annual price features.
- [data/derived/sp500_price_annual_features_2012_2023.csv](data/derived/sp500_price_annual_features_2012_2023.csv): annual return, volatility, volume, and drawdown features.
- [data/derived/sp500_edgar_esg_annual_2012_2023.csv](data/derived/sp500_edgar_esg_annual_2012_2023.csv): annual EDGAR text-derived ESG signals.
- [data/derived/sp500_price_esg_panel_2012_2023.csv](data/derived/sp500_price_esg_panel_2012_2023.csv): merged firm-year panel for downstream analysis.

### Coverage and Usability Audit

- [output/sp500_coverage_overview.csv](output/sp500_coverage_overview.csv): project-level coverage summary.
- [output/sp500_coverage_by_year.csv](output/sp500_coverage_by_year.csv): yearly coverage rates for price, snapshot ESG, and EDGAR ESG.
- [output/sp500_company_coverage_flags.csv](output/sp500_company_coverage_flags.csv): company-level availability flags and first/last available years.
- [output/01_data_usability](output/01_data_usability): dual-denominator coverage, sector summaries, field missingness, and special-case audits.

### Machine-Learning Warning Outputs

- [data/derived/sp500_ml_panel_2y_2012_2023_features_to_2024_2025_labels.csv](data/derived/sp500_ml_panel_2y_2012_2023_features_to_2024_2025_labels.csv): two-year supervised learning panel.
- [output/02_ml_warning](output/02_ml_warning): cross-validation metrics, threshold tuning, external-test metrics, confusion matrices, and company-level warning lists.

## Data Sources

The notebook combines four source streams:

1. Current S&P 500 constituents from the Wikipedia page `https://en.wikipedia.org/wiki/List_of_S%26P_500_companies`.
2. Multi-year daily price history reused from a local Yahoo Finance cache.
3. A snapshot ESG file named `SP 500 ESG Risk Ratings.csv`.
4. An annual EDGAR text-signal cache named `esg_signals_panel.csv`, with optional live backfilling from SEC 10-K filings.

The required local source files are stored directly under [data/raw](data/raw):

- `SP 500 ESG Risk Ratings.csv`
- `company_universe.csv`
- `prices_daily.csv`
- `esg_signals_panel.csv`

The main notebook reads these files through project-relative paths only, so no machine-specific environment variables or project-external file search is required.

## How to Run the Notebook

### Standard Render

From PowerShell in the project root:

```powershell
Rscript -e "rmarkdown::render('sp500_esg_crash_risk_warning.Rmd')"
```

This command rebuilds the HTML report and refreshes the CSV outputs generated by the notebook.

Because the required source files are already stored inside [data/raw](data/raw), the project can be copied to another computer and rendered directly from the project root.

### Optional Live EDGAR Backfill

The notebook keeps live EDGAR backfill disabled by default because bulk SEC downloads are slow. If you want to fill missing EDGAR observations from the SEC site, edit the YAML parameters in [sp500_esg_crash_risk_warning.Rmd](sp500_esg_crash_risk_warning.Rmd) or render with custom parameters.

## Software Requirements

- R with `rmarkdown` support.
- The notebook installs missing R packages automatically when needed.
- Internet access is only required when packages are missing or when optional live EDGAR backfill is enabled.

## Important Modeling Notes

- The panel is based on the current S&P 500 constituent set, so historical analysis is not survivorship-bias free.
- The snapshot ESG source is cross-sectional rather than year-specific.
- EDGAR ESG scores are text-derived keyword intensity measures from annual filings.
- One-year and two-year machine-learning outputs should be interpreted as screening signals rather than final investment or credit decisions.

## Data Dictionary

See [DATA_DICTIONARY.md](DATA_DICTIONARY.md) for a file-by-file schema map and column definitions for the CSV datasets in [data/derived](data/derived) and [output](output).
