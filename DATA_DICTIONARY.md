# Data Dictionary

This document explains the CSV datasets currently stored in this repository. It covers the derived data under [data/derived](data/derived) and the tabular outputs under [output](output).

## Conventions

- Grain means the row-level unit of observation.
- Boolean flags may appear as `TRUE` and `FALSE` or as `1` and `0`, depending on the export step.
- Coverage rates, shares, probabilities, and performance metrics are stored as decimals unless the file explicitly labels them otherwise.
- Missingness flags ending in `_is_missing` use `1` for missing and `0` for observed.
- `risk_rank = 1` always means the highest model-implied risk within a prediction table.

## Shared Field Definitions

### Company Identity and Classification

- `symbol`: standardized stock ticker used throughout the derived datasets.
- `symbol_raw`: original ticker text before standardization.
- `price_symbol`: ticker symbol used by the price-history source.
- `edgar_symbol`: ticker symbol found in the EDGAR ESG cache.
- `company_name`: company name used in the current working table.
- `snapshot_name`: company name recorded in the snapshot ESG source file.
- `cik`: SEC Central Index Key used to match firms to EDGAR filings.
- `gics_sector`: broad GICS sector classification.
- `gics_sub_industry`: detailed GICS sub-industry classification.
- `sector_standardized`: cleaned sector field used in modeling outputs.
- `snapshot_sector`: sector name stored in the snapshot ESG source.
- `snapshot_industry`: industry name stored in the snapshot ESG source.
- `headquarters_location`: headquarters location from the constituent source.
- `date_added`: date on which the company was added to the S&P 500.
- `founded`: founding year or founding-date text from the constituent source.

### Time Fields

- `date`: trading date for daily price observations.
- `year`: year key used for annual aggregation or annual labels.
- `first_trade`: first trading date observed in a company-year.
- `last_trade`: last trading date observed in a company-year.
- `feature_year`: year from which model input features are taken.
- `target_year`: next-year outcome year used in the one-year warning setup.
- `window_start_year`: first future year covered by a two-year label window.
- `window_end_year`: last future year covered by a two-year label window.
- `validation_year`: one-year cross-validation holdout year.
- `validation_feature_year`: two-year cross-validation holdout feature year.
- `validation_window`: text label describing the future window used in a two-year validation fold.
- `coverage_period`: text label for the time scope used in a coverage summary.

### Snapshot ESG Fields

- `snapshot_total_esg_risk`: overall snapshot ESG risk score from the external rating file.
- `snapshot_environment_risk`: environmental component of the snapshot ESG score.
- `snapshot_social_risk`: social component of the snapshot ESG score.
- `snapshot_governance_risk`: governance component of the snapshot ESG score.
- `snapshot_controversy_level`: controversy severity label from the snapshot ESG source.
- `snapshot_controversy_score`: numeric controversy score from the snapshot ESG source.
- `snapshot_esg_percentile`: snapshot ESG percentile label or value from the source file.
- `snapshot_esg_level`: overall ESG category label from the source file.
- `snapshot_available`: indicator that snapshot ESG data is available for the firm.

### Price Fields

- `adj_close`: adjusted closing price for a trading day.
- `volume`: trading volume for a trading day.
- `n_trading_days`: number of valid trading days observed in a company-year.
- `start_price`: adjusted close at the first trading day of the year.
- `end_price`: adjusted close at the last trading day of the year.
- `annual_return`: simple annual return computed from start and end prices.
- `annual_log_return`: log annual return.
- `annual_volatility`: annualized volatility derived from daily returns.
- `avg_volume`: average daily trading volume within the year.
- `median_volume`: median daily trading volume within the year.
- `max_drawdown`: largest peak-to-trough loss within the year.
- `price_available`: indicator that yearly price features are available for the row.

### EDGAR ESG Text Fields

- `edgar_available`: indicator that a valid annual EDGAR filing-based ESG observation exists.
- `edgar_e_score`: environmental keyword hits per 1,000 filing words.
- `edgar_s_score`: social keyword hits per 1,000 filing words.
- `edgar_g_score`: governance keyword hits per 1,000 filing words.
- `edgar_esg_total`: total ESG keyword hits per 1,000 filing words.
- `edgar_e_count`: raw count of environmental keyword hits.
- `edgar_s_count`: raw count of social keyword hits.
- `edgar_g_count`: raw count of governance keyword hits.
- `edgar_word_count`: number of words extracted from the filing text.
- `edgar_filing_date`: filing date of the EDGAR document used for that observation.

### Crash-Risk Label Fields

- `NCSKEW`: negative conditional skewness of transformed weekly returns for the company-year.
- `CRASH_DUMMY`: crash-event flag equal to `1` when at least one transformed weekly return is below `mean - 3.09 * sd` during the year.
- `SIGMA`: standard deviation of transformed weekly returns.
- `RET_MEAN`: mean of transformed weekly returns.
- `DTURN`: year-over-year change in average monthly trading volume.
- `next_year_ncskew`: next-year `NCSKEW` used as a one-year prediction target.
- `next_year_crash_dummy`: next-year crash-event flag used as a one-year classification label.
- `crash_t1`: crash flag for the first year in a two-year future window.
- `ncskew_t1`: `NCSKEW` for the first year in a two-year future window.
- `crash_t2`: crash flag for the second year in a two-year future window.
- `ncskew_t2`: `NCSKEW` for the second year in a two-year future window.
- `window_complete`: indicator that both years in the two-year future window are observed.
- `future_2y_crash_dummy`: two-year label equal to `1` when either `crash_t1` or `crash_t2` equals `1`.

### Coverage and Audit Fields

- `is_post_index_addition`: indicator that the row year is on or after the company's S&P 500 addition year.
- `panel_row_status`: row-level availability label with values such as `price_and_edgar`, `price_only`, `edgar_only`, or `missing_both`.
- `any_price`: company-level indicator that price data exists in at least one year.
- `any_edgar`: company-level indicator that EDGAR ESG data exists in at least one year.
- `first_price_year`: first year in which price data exists for the company.
- `last_price_year`: last year in which price data exists for the company.
- `first_edgar_year`: first year in which EDGAR ESG data exists for the company.
- `last_edgar_year`: last year in which EDGAR ESG data exists for the company.
- `metric`: machine-readable metric name used in audit and overview tables.
- `metric_label`: human-readable label for `metric`.
- `summary_group`: summary scope used in overview tables.
- `denominator_type`: denominator definition used to compute a coverage rate.
- `denominator_label`: human-readable label for `denominator_type`.
- `numerator`: count of observations satisfying the metric condition.
- `denominator`: total count used as the base for a rate.
- `coverage_rate`: `numerator / denominator`.
- `firms_total`: number of firms in the relevant year.
- `firms_with_price`: firms with usable price data.
- `firms_with_edgar`: firms with usable EDGAR ESG data.
- `firms_with_snapshot`: firms with snapshot ESG data.
- `firms_with_price_and_edgar`: firms with both price and EDGAR ESG data.
- `field`: field name being audited for missingness.
- `missing_count`: number of missing values in the audited field.
- `missing_rate`: proportion of missing values in the audited field.
- `non_missing_rate`: proportion of non-missing values in the audited field.
- `field_group`: logical grouping of fields in the missingness audit.
- `company_bucket`: company usability bucket label.
- `company_count`: number of companies in a bucket.
- `share`: share of the relevant total represented by the row.
- `scope`: scope label used in special-case summaries.
- `value`: numeric or text value stored for a metric row.
- `note`: explanatory text attached to a metric row.

### Modeling and Prediction Fields

- `eligible_by_year`: indicator that the firm-year is eligible to enter the modeling sample.
- `data_split`: partition label such as `pre_2024_training_pool`, `external_2024_test`, `pre_external_training_pool`, or `external_2024_2025_test`.
- `model_key`: unique model identifier combining feature set and algorithm family.
- `model_display`: fully formatted model name shown in result tables.
- `feature_set_id`: machine-readable feature-set identifier.
- `feature_set_label`: human-readable feature-set label.
- `model_type`: machine-readable model family identifier.
- `model_label`: human-readable model family name.
- `selected_threshold`: decision threshold chosen for warning classification.
- `threshold`: candidate decision threshold evaluated during tuning.
- `risk_probability`: predicted probability or risk score from the model.
- `warning_flag`: classification flag set to `1` when `risk_probability >= selected_threshold`.
- `risk_rank`: descending rank of predicted risk within a prediction table.
- `actual_label`: observed one-year crash label used for external validation.
- `actual_ncskew`: observed one-year `NCSKEW` used for reference in external validation.
- `actual_class`: observed class used in confusion-matrix tables.
- `predicted_class`: predicted class used in confusion-matrix tables.
- `cell`: confusion-matrix cell label.
- `count`: row count for an aggregated table.
- `n_eval`: number of rows evaluated when computing a metric table.
- `positive_count`: number of positive outcome rows in the evaluation set.
- `positive_rate`: `positive_count / n_eval`.
- `predicted_positive_count`: number of rows flagged positive by the model at the chosen threshold.
- `train_rows`: number of rows used for model training.
- `validation_rows`: number of rows used for fold validation.
- `fold_count`: number of validation folds included in an average.
- `hits_in_top20`: number of true positives found among the top 20 ranked firms.
- `top20_evaluated_n`: number of evaluable firms in the top-20 ranking slice.
- `true_positive`: count of true positives.
- `true_negative`: count of true negatives.
- `false_positive`: count of false positives.
- `false_negative`: count of false negatives.

### Performance Metrics

- `accuracy`: share of correct predictions.
- `balanced_accuracy`: average of sensitivity and specificity.
- `precision`: positive predictive value.
- `recall`: sensitivity or true-positive rate.
- `specificity`: true-negative rate.
- `f0_5`: $F_{0.5}$ score, which weights precision more heavily than recall.
- `roc_auc`: area under the ROC curve.
- `pr_auc`: area under the precision-recall curve.
- `precision_at_top10`: precision among the 10 highest-risk firms.
- `top20_hit_rate`: hit rate among the 20 highest-risk firms.
- `train_positive_rate`: positive-class rate in the training fold.
- `validation_positive_rate`: positive-class rate in the validation fold.
- `mean_roc_auc`: average `roc_auc` across folds.
- `mean_pr_auc`: average `pr_auc` across folds.
- `mean_precision_at_top10`: average `precision_at_top10` across folds.
- `mean_top20_hit_rate`: average `top20_hit_rate` across folds.
- `mean_train_positive_rate`: average training positive rate across folds.
- `mean_validation_positive_rate`: average validation positive rate across folds.
- `mean_precision`: average precision across folds.
- `mean_recall`: average recall across folds.
- `mean_f0_5`: average $F_{0.5}$ across folds.
- `mean_accuracy`: average accuracy across folds.
- `mean_accuracy_cv`: cross-validation mean accuracy in the two-year comparison table.
- `mean_balanced_accuracy_cv`: cross-validation mean balanced accuracy in the two-year comparison table.
- `mean_precision_cv`: cross-validation mean precision in the two-year comparison table.
- `mean_recall_cv`: cross-validation mean recall in the two-year comparison table.
- `mean_f0_5_cv`: cross-validation mean $F_{0.5}$ in the two-year comparison table.
- `mean_accuracy_threshold`: average threshold-based accuracy in the two-year comparison table.
- `mean_balanced_accuracy_threshold`: average threshold-based balanced accuracy in the two-year comparison table.
- `mean_precision_threshold`: average threshold-based precision in the two-year comparison table.
- `mean_recall_threshold`: average threshold-based recall in the two-year comparison table.
- `mean_f0_5_threshold`: average threshold-based $F_{0.5}$ in the two-year comparison table.

### Missingness Indicator Fields

- `snapshot_total_esg_risk_is_missing`: snapshot total ESG risk missing flag.
- `snapshot_environment_risk_is_missing`: environmental snapshot score missing flag.
- `snapshot_social_risk_is_missing`: social snapshot score missing flag.
- `snapshot_governance_risk_is_missing`: governance snapshot score missing flag.
- `snapshot_controversy_score_is_missing`: snapshot controversy score missing flag.
- `snapshot_esg_percentile_is_missing`: snapshot ESG percentile missing flag.
- `edgar_e_score_is_missing`: EDGAR environmental score missing flag.
- `edgar_s_score_is_missing`: EDGAR social score missing flag.
- `edgar_g_score_is_missing`: EDGAR governance score missing flag.
- `edgar_esg_total_is_missing`: EDGAR total ESG score missing flag.
- `edgar_word_count_is_missing`: EDGAR filing word-count missing flag.

## File-by-File Schema Map

### Universe and Company Master Tables

#### [data/derived/sp500_constituents_current.csv](data/derived/sp500_constituents_current.csv)

- Grain: one row per current S&P 500 constituent.
- Columns: `symbol`, `symbol_raw`, `company_name`, `cik`, `gics_sector`, `gics_sub_industry`, `headquarters_location`, `date_added`, `founded`, `snapshot_name`, `snapshot_sector`, `snapshot_industry`, `snapshot_total_esg_risk`, `snapshot_environment_risk`, `snapshot_governance_risk`, `snapshot_social_risk`, `snapshot_controversy_level`, `snapshot_controversy_score`, `snapshot_esg_percentile`, `snapshot_esg_level`, `price_symbol`, `snapshot_available`.

#### [data/derived/sp500_wiki_current.csv](data/derived/sp500_wiki_current.csv)

- Grain: one row per current constituent as cached from Wikipedia.
- Columns:
- `Symbol`: raw ticker symbol from Wikipedia.
- `Security`: company name from Wikipedia.
- `GICS Sector`: broad GICS sector from Wikipedia.
- `GICS Sub-Industry`: detailed GICS sub-industry from Wikipedia.
- `Headquarters Location`: headquarters location text from Wikipedia.
- `Date added`: date added to the S&P 500, as published by Wikipedia.
- `CIK`: SEC Central Index Key from Wikipedia.
- `Founded`: founding-year text from Wikipedia.

### Daily and Annual Price Tables

#### [data/derived/sp500_price_daily_2011_2023.csv](data/derived/sp500_price_daily_2011_2023.csv), [data/derived/sp500_price_daily_2011_2025.csv](data/derived/sp500_price_daily_2011_2025.csv), [data/derived/sp500_price_daily_2024.csv](data/derived/sp500_price_daily_2024.csv), [data/derived/sp500_price_daily_2025.csv](data/derived/sp500_price_daily_2025.csv)

- Grain: one row per company-day.
- Columns: `price_symbol`, `date`, `adj_close`, `volume`, `symbol`, `company_name`, `cik`, `gics_sector`, `gics_sub_industry`.

#### [data/derived/sp500_price_annual_features_2012_2023.csv](data/derived/sp500_price_annual_features_2012_2023.csv)

- Grain: one row per company-year.
- Columns: `symbol`, `company_name`, `cik`, `gics_sector`, `gics_sub_industry`, `year`, `n_trading_days`, `first_trade`, `last_trade`, `start_price`, `end_price`, `annual_return`, `annual_log_return`, `annual_volatility`, `avg_volume`, `median_volume`, `max_drawdown`.

### ESG and Merged Panel Tables

#### [data/derived/sp500_edgar_esg_annual_2012_2023.csv](data/derived/sp500_edgar_esg_annual_2012_2023.csv)

- Grain: one row per company-year.
- Columns: `symbol`, `cik`, `year`, `edgar_symbol`, `edgar_available`, `edgar_e_score`, `edgar_s_score`, `edgar_g_score`, `edgar_esg_total`, `edgar_e_count`, `edgar_s_count`, `edgar_g_count`, `edgar_word_count`, `edgar_filing_date`.

#### [data/derived/sp500_price_esg_panel_2012_2023.csv](data/derived/sp500_price_esg_panel_2012_2023.csv)

- Grain: one row per company-year in the merged panel.
- Columns: `symbol`, `symbol_raw`, `company_name`, `cik`, `gics_sector`, `gics_sub_industry`, `headquarters_location`, `date_added`, `founded`, `snapshot_name`, `snapshot_sector`, `snapshot_industry`, `snapshot_total_esg_risk`, `snapshot_environment_risk`, `snapshot_governance_risk`, `snapshot_social_risk`, `snapshot_controversy_level`, `snapshot_controversy_score`, `snapshot_esg_percentile`, `snapshot_esg_level`, `snapshot_available`, `year`, `n_trading_days`, `first_trade`, `last_trade`, `start_price`, `end_price`, `annual_return`, `annual_log_return`, `annual_volatility`, `avg_volume`, `median_volume`, `max_drawdown`, `edgar_symbol`, `edgar_available`, `edgar_e_score`, `edgar_s_score`, `edgar_g_score`, `edgar_esg_total`, `edgar_e_count`, `edgar_s_count`, `edgar_g_count`, `edgar_word_count`, `edgar_filing_date`, `price_available`, `is_post_index_addition`, `panel_row_status`.

### Crash-Metric Tables

#### [data/derived/sp500_crash_metrics_2012_2025.csv](data/derived/sp500_crash_metrics_2012_2025.csv)

- Grain: one row per company-year.
- Columns: `symbol`, `year`, `NCSKEW`, `CRASH_DUMMY`, `SIGMA`, `RET_MEAN`, `DTURN`.

### Two-Year Supervised Learning Tables

#### [data/derived/sp500_ml_panel_2y_2012_2023_features_to_2024_2025_labels.csv](data/derived/sp500_ml_panel_2y_2012_2023_features_to_2024_2025_labels.csv)

- Grain: one row per company feature year, paired with a two-year-ahead outcome window.
- Columns: `symbol`, `company_name`, `sector_standardized`, `feature_year`, `window_start_year`, `window_end_year`, `eligible_by_year`, `annual_return`, `annual_log_return`, `annual_volatility`, `max_drawdown`, `avg_volume`, `median_volume`, `n_trading_days`, `snapshot_available`, `edgar_available`, `snapshot_total_esg_risk`, `snapshot_environment_risk`, `snapshot_social_risk`, `snapshot_governance_risk`, `snapshot_controversy_score`, `snapshot_esg_percentile`, `edgar_e_score`, `edgar_s_score`, `edgar_g_score`, `edgar_esg_total`, `edgar_word_count`, `crash_t1`, `ncskew_t1`, `crash_t2`, `ncskew_t2`, `snapshot_total_esg_risk_is_missing`, `snapshot_environment_risk_is_missing`, `snapshot_social_risk_is_missing`, `snapshot_governance_risk_is_missing`, `snapshot_controversy_score_is_missing`, `snapshot_esg_percentile_is_missing`, `edgar_e_score_is_missing`, `edgar_s_score_is_missing`, `edgar_g_score_is_missing`, `edgar_esg_total_is_missing`, `edgar_word_count_is_missing`, `window_complete`, `future_2y_crash_dummy`, `data_split`.

#### [output/02_ml_warning/01_training_panel_2y.csv](output/02_ml_warning/01_training_panel_2y.csv)

- Grain: one row per company feature year kept in the two-year training pool.
- Columns: same schema as [data/derived/sp500_ml_panel_2y_2012_2023_features_to_2024_2025_labels.csv](data/derived/sp500_ml_panel_2y_2012_2023_features_to_2024_2025_labels.csv).

### Coverage Summary Tables

#### [output/sp500_coverage_overview.csv](output/sp500_coverage_overview.csv)

- Grain: one row per overview metric.
- Columns: `metric`, `value`.

#### [output/sp500_coverage_by_year.csv](output/sp500_coverage_by_year.csv)

- Grain: one row per year.
- Columns: `year`, `firms_total`, `firms_with_price`, `firms_with_edgar`, `firms_with_snapshot`, `firms_with_price_and_edgar`, `price_coverage`, `edgar_coverage`, `snapshot_coverage`.
- `price_coverage`: `firms_with_price / firms_total`.
- `edgar_coverage`: `firms_with_edgar / firms_total`.
- `snapshot_coverage`: `firms_with_snapshot / firms_total`.

#### [output/sp500_company_coverage_flags.csv](output/sp500_company_coverage_flags.csv)

- Grain: one row per company.
- Columns: `symbol`, `company_name`, `cik`, `gics_sector`, `gics_sub_industry`, `any_price`, `any_edgar`, `snapshot_available`, `first_price_year`, `last_price_year`, `first_edgar_year`, `last_edgar_year`.

### Usability Audit Tables

#### [output/01_data_usability/01_overview_dual_denominator.csv](output/01_data_usability/01_overview_dual_denominator.csv)

- Grain: one row per overview metric and denominator definition.
- Columns: `summary_group`, `denominator_type`, `denominator_label`, `metric`, `metric_label`, `numerator`, `denominator`, `coverage_rate`.

#### [output/01_data_usability/02_coverage_by_year_dual_denominator.csv](output/01_data_usability/02_coverage_by_year_dual_denominator.csv)

- Grain: one row per year, metric, and denominator definition.
- Columns: `year`, `metric`, `metric_label`, `numerator`, `denominator`, `coverage_rate`, `denominator_type`, `denominator_label`.

#### [output/01_data_usability/03_coverage_by_sector_dual_denominator.csv](output/01_data_usability/03_coverage_by_sector_dual_denominator.csv)

- Grain: one row per sector, metric, and denominator definition.
- Columns: `sector_standardized`, `metric`, `metric_label`, `firms_in_sector`, `numerator`, `denominator`, `coverage_rate`, `denominator_type`, `coverage_period`, `denominator_label`.

#### [output/01_data_usability/04_field_missingness.csv](output/01_data_usability/04_field_missingness.csv)

- Grain: one row per audited field.
- Columns: `field`, `missing_count`, `missing_rate`, `non_missing_rate`, `field_group`.

#### [output/01_data_usability/05_company_coverage_buckets.csv](output/01_data_usability/05_company_coverage_buckets.csv)

- Grain: one row per company-usability bucket.
- Columns: `company_bucket`, `company_count`, `share`.

#### [output/01_data_usability/06_special_cases_summary.csv](output/01_data_usability/06_special_cases_summary.csv)

- Grain: one row per special-case metric.
- Columns: `scope`, `metric`, `value`, `note`.

### Two-Year Validation and Prediction Tables

#### [output/02_ml_warning/02_validation_fold_metrics_2y.csv](output/02_ml_warning/02_validation_fold_metrics_2y.csv)

- Grain: one row per two-year validation fold and model.
- Columns: `selected_threshold`, `n_eval`, `positive_count`, `positive_rate`, `predicted_positive_count`, `accuracy`, `balanced_accuracy`, `precision`, `recall`, `specificity`, `f0_5`, `roc_auc`, `pr_auc`, `precision_at_top10`, `top20_hit_rate`, `hits_in_top20`, `top20_evaluated_n`, `true_positive`, `true_negative`, `false_positive`, `false_negative`, `model_key`, `model_display`, `feature_set_id`, `feature_set_label`, `model_type`, `model_label`, `validation_feature_year`, `validation_window`, `train_rows`, `validation_rows`, `train_positive_rate`, `validation_positive_rate`.

#### [output/02_ml_warning/03_threshold_tuning_2y.csv](output/02_ml_warning/03_threshold_tuning_2y.csv)

- Grain: one row per two-year candidate threshold, validation fold, and model.
- Columns: `selected_threshold`, `n_eval`, `positive_count`, `positive_rate`, `predicted_positive_count`, `accuracy`, `balanced_accuracy`, `precision`, `recall`, `specificity`, `f0_5`, `roc_auc`, `pr_auc`, `precision_at_top10`, `top20_hit_rate`, `hits_in_top20`, `top20_evaluated_n`, `true_positive`, `true_negative`, `false_positive`, `false_negative`, `threshold`, `model_key`, `model_display`, `feature_set_id`, `feature_set_label`, `model_type`, `model_label`, `validation_feature_year`.

#### [output/02_ml_warning/04_model_comparison_cv_2y.csv](output/02_ml_warning/04_model_comparison_cv_2y.csv)

- Grain: one row per two-year model specification.
- Columns: `model_key`, `model_display`, `feature_set_id`, `feature_set_label`, `model_type`, `model_label`, `fold_count`, `mean_accuracy_cv`, `mean_balanced_accuracy_cv`, `mean_precision_cv`, `mean_recall_cv`, `mean_f0_5_cv`, `mean_roc_auc`, `mean_pr_auc`, `mean_precision_at_top10`, `mean_top20_hit_rate`, `mean_train_positive_rate`, `mean_validation_positive_rate`, `selected_threshold`, `mean_accuracy_threshold`, `mean_balanced_accuracy_threshold`, `mean_precision_threshold`, `mean_recall_threshold`, `mean_f0_5_threshold`.

#### [output/02_ml_warning/05_external_test_metrics_2024_2025_2y.csv](output/02_ml_warning/05_external_test_metrics_2024_2025_2y.csv)

- Grain: one row per two-year model evaluated on the 2024-2025 external test set.
- Columns: `model_key`, `model_display`, `feature_set_id`, `feature_set_label`, `model_type`, `model_label`, `selected_threshold`, `n_eval`, `positive_count`, `positive_rate`, `predicted_positive_count`, `accuracy`, `balanced_accuracy`, `precision`, `recall`, `specificity`, `f0_5`, `roc_auc`, `pr_auc`, `precision_at_top10`, `top20_hit_rate`, `hits_in_top20`, `top20_evaluated_n`, `true_positive`, `true_negative`, `false_positive`, `false_negative`.

#### [output/02_ml_warning/06_confusion_matrix_by_model_2y.csv](output/02_ml_warning/06_confusion_matrix_by_model_2y.csv)

- Grain: one row per confusion-matrix cell and model.
- Columns: `model_key`, `model_display`, `feature_set_id`, `feature_set_label`, `model_type`, `model_label`, `selected_threshold`, `actual_class`, `predicted_class`, `cell`, `count`, `n_eval`, `share`.

#### [output/02_ml_warning/07_company_predictions_2024_2025_2y.csv](output/02_ml_warning/07_company_predictions_2024_2025_2y.csv)

- Grain: one row per company-model combination in the 2024-2025 two-year external test set.
- Columns: `model_key`, `model_display`, `feature_set_id`, `feature_set_label`, `model_type`, `model_label`, `symbol`, `company_name`, `sector_standardized`, `feature_year`, `window_start_year`, `window_end_year`, `crash_t1`, `crash_t2`, `ncskew_t1`, `ncskew_t2`, `future_2y_crash_dummy`, `risk_probability`, `selected_threshold`, `warning_flag`, `risk_rank`.

#### [output/02_ml_warning/08_high_risk_companies_2024_2025_2y.csv](output/02_ml_warning/08_high_risk_companies_2024_2025_2y.csv)

- Grain: one row per high-risk company in the two-year warning output.
- Columns: `model_display`, `symbol`, `company_name`, `sector_standardized`, `feature_year`, `window_start_year`, `window_end_year`, `risk_probability`, `selected_threshold`, `warning_flag`, `crash_t1`, `crash_t2`, `future_2y_crash_dummy`, `risk_rank`.

## Files Not Covered Here

This dictionary documents CSV files only. The PNG charts in [output/01_data_usability](output/01_data_usability) and [output/02_ml_warning](output/02_ml_warning), along with the rendered HTML report [sp500_esg_crash_risk_warning.html](sp500_esg_crash_risk_warning.html), are presentation artifacts rather than raw tabular datasets.