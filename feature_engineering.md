# Feature Engineering

## Time Representation

- minute of day
- 15-minute slot
- hour and minute
- sine/cosine encodings for slot and hour

These features represent daily periodicity and avoid treating midnight as far away from late evening.

## Geohash And Spatial Representation

- geohash prefixes `geo_2` through `geo_5`
- decoded latitude and longitude
- KMeans spatial cluster
- categorical geohash codes

Prefixes capture hierarchical neighborhoods, while coordinates and clusters provide continuous and regional spatial context.

## Categorical Features

The numeric ensemble receives consistent train/test codes for:

- geohash and its prefixes
- RoadType
- LargeVehicles
- Landmarks
- Weather
- timestamp

CatBoost separately receives the original categorical strings to learn non-ordinal category interactions.

## Temperature Imputation

Missing temperature is filled using:

1. `(geo_4, day)` median
2. `geo_4` median
3. global train median

This preserves local conditions while ensuring every row has a numeric value.

## Day-48 Demand Statistics

Mean, standard deviation, and count features are calculated for:

- geohash x slot
- geohash
- slot
- hour
- geo4 x slot
- geo3 x slot
- RoadType x slot
- Weather x slot
- LargeVehicles x slot
- NumberofLanes x slot

An exact geohash-slot lookup is filled through progressively broader location and slot levels.

## Early Day-49 Ratio Features

Known day-49 demand is compared with matching day-48 demand. The notebook creates:

- geohash mean and latest ratios
- geo3 ratio summaries
- a global ratio curve
- day-49 known mean, latest value, and count

These features indicate whether day 49 is currently running above or below the previous day.

## High-Demand Probability

A binary classifier estimates the probability of demand entering a high regime. This probability helps the regressors distinguish ordinary demand from near-saturation traffic.

## Official Sample Anchor

Organizer-provided values in `sample_submission.csv` are used as direct anchors where present. A time-decayed correction extends an anchor to nearby rows in the same geo5 region.

## Temporal Shape Features

The temporal model uses:

- matching day-48 demand
- latest known day-49 demand
- day-49/day-48 ratios
- hierarchical geohash fallbacks
- RoadType and prediction-bin blend factors

This produces a carry-forward estimate of the later day-49 curve.

## Temporal Slope Residual Features

- `d48_delta_prev`
- `d48_delta_next`
- `d48_delta_center`
- geohash ratio mean and standard deviation
- geohash and geo4 ratio trend
- base-versus-shape prediction gap
- alpha-weighted gap
- distance from the latest anchor slot

These features explain how demand is changing, rather than only its current level.

## Circular Rolling Features

Day-48 values are sampled at circular offsets around the target slot. Derived features include:

- 3-slot, 5-slot, 9-slot, and wide rolling means
- short and medium local slopes
- local acceleration
- peak and valley ratios

Circular indexing preserves continuity across the day boundary.

## Phase-Shift Features

For each geohash, shifts around the day-48 curve are compared against known early day-49 observations. The selected shift, alignment correlation, shifted demand, and shifted-minus-same-slot difference capture changes in peak timing.

Additional geohash and geo4 phase estimates use shifts from `-3` through `+3` slots.

## Final Residual Models

Ridge regression is used for the slope, circular/phase, and phase-local corrections. Standardization keeps feature scales comparable, and shrink factors limit the size of each residual adjustment.

Calibration, temporal blend, Ridge regularization, and residual shrink values are displayed to four decimal places. They were finalized through repeated controlled prediction and residual-analysis experiments.
