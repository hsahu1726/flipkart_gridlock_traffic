# Relevant Source Files

## `source_code.ipynb`

This is the only executable source file in the package.

### Inputs

The notebook locates an official competition zip or extracted dataset folder containing:

```text
train.csv
test.csv
sample_submission.csv
```

### Execution Order

1. Locate and extract the competition dataset.
2. Define shared parsing and geohash helpers.
3. Build spatial, temporal, statistical, and categorical features.
4. Train the XGBoost/LightGBM ensemble and high-demand classifier.
5. Apply the official sample anchor and residual-based adjustments.
6. Train raw-categorical CatBoost.
7. Learn the RoadType-aware gate.
8. Build and blend the day-48 temporal-shape prediction.
9. Write an intermediate `submission.csv`.
10. Generate rolling day-49 validation predictions.
11. Rebuild the validation-ranked temporal slope ensemble.
12. Add circular rolling and phase-shift features.
13. Apply the final phase-local Ridge residual.
14. Overwrite `submission.csv` with the final prediction.

The notebook must run through step 14. The earlier CSV is not the final advanced output.

## `approach.md`

Explains the modelling architecture, time-ordered validation, temporal-shape transfer, and advanced residual stages.

## `feature_engineering.md`

Documents every feature family, including the late-stage slope, circular, and phase-shift features.

## `tools_used.md`

Lists runtime dependencies and the role of each library.

## `README.md`

Provides reviewer instructions, dataset placement, package contents, and final-output guidance.

## Output

The final file is:

```text
submission.csv
```

with columns:

```text
Index,demand
```

The complete advanced configuration corresponds to the `93.4017` reference-check candidate.
