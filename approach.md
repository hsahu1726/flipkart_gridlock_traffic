# Approach

## Problem Framing

The dataset contains a complete day-48 demand pattern and the first nine 15-minute slots of day 49. The test set continues day 49 after those known slots. We therefore treat the task as short-horizon temporal extrapolation supported by spatial and road metadata.

The solution combines two views:

1. Tabular models learn stable relationships among geohash, time, road, weather, and infrastructure fields.
2. Temporal models transfer the observed day-48 curve and early day-49 movement into later day-49 slots.

## Execution Stages

### 1. Base Feature Pipeline

The notebook parses timestamps, decodes geohashes, creates hierarchical location prefixes, imputes temperature, and calculates day-48 and early day-49 statistics. These features capture recurring traffic structure and the current-day level relative to the previous day.

### 2. XGBoost And LightGBM Ensemble

Three XGBoost regressors use different seeds and hyperparameters. Their average is blended with LightGBM. Model diversity reduces sensitivity to one tree configuration.

### 3. High-Demand Classifier

A LightGBM classifier estimates whether demand belongs to the upper-demand regime. Its probability is passed to the regressors because the largest errors were concentrated near traffic peaks.

### 4. Raw-Categorical CatBoost

CatBoost receives original categorical values such as geohash, timestamp, RoadType, Weather, and Landmarks. Its representation differs from numeric label encoding and provides complementary predictions.

### 5. RoadType Gate

Early day-49 validation rows are used to estimate how much each RoadType should trust the main ensemble versus CatBoost. Small groups are shrunk toward a global blend value to reduce instability.

### 6. Temporal Shape Transfer

The latest known day-49 level is projected forward using the corresponding day-48 curve. Exact geohash-slot values are preferred, with geo4, geo3, slot, and global fallbacks where necessary.

### 7. Residual-Based Adjustments

Prediction-range and location-time factors address recurring residual patterns. Values were finalized by generating previous submissions, studying grouped residuals, changing one factor at a time, and retaining stable improvements. Displayed factors are rounded to four decimal places.

### 8. Validation-Ranked Temporal Slope Ensemble

Rolling day-49 validation predictions are generated from official train rows. Ridge residual models then learn:

- local day-48 slope before and after a slot
- early day-49/day-48 ratio level and trend
- the disagreement between the tabular and temporal-shape predictions
- distance from the latest known day-49 anchor

The selected slope variants are averaged and blended with the baseline.

### 9. Circular And Phase-Shift Correction

Circular rolling features examine neighboring day-48 slots around each target time and calculate local rolling means, slopes, acceleration, peak ratios, and valley ratios.

Phase-shift features test whether a geohash's early day-49 curve aligns better with day 48 shifted slightly earlier or later. This handles locations whose peak timing changes between days.

### 10. Final Phase-Local Residual

A final Ridge model uses the phase feature family to correct remaining temporal-alignment residuals. The fixed settings are `alpha=3000.0000` and `shrink=0.2000`.

## Validation

Validation follows time order instead of random row splitting. For rolling folds, each known day-49 slot is predicted using day 48 and only the earlier available day-49 slots. This mirrors the forecasting task and reduces temporal leakage.

## Output

The baseline cell writes an intermediate `submission.csv`. The advanced final cell overwrites that file after completing the slope, circular, phase-shift, and phase-local stages. The overwritten file is the result to submit.

## Score Progression

| Stage | Reference-check score |
|---|---:|
| Initial strong baseline | 92.2473 |
| High-demand classifier | 92.4847 |
| CatBoost and hierarchy diversity | 92.6897 |
| Raw-categorical diversity | 92.8071 |
| RoadType routing | 93.0789 |
| Temporal baseline | 93.2458 |
| Validation-ranked slope ensemble | 93.3261 |
| Circular plus phase-shift residual | 93.3959 |
| Final phase-local residual | 93.4017 |
