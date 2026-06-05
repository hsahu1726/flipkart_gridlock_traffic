# Bangaluru vs Traffic - Gridlock Hackathon 2.0

This package contains one complete Jupyter notebook for traffic-demand prediction:

`source_code.ipynb`

## Reviewer Instructions

1. Open `source_code.ipynb` in Google Colab or Jupyter.
2. Place the official competition zip beside the notebook. Supported names are:
   - `e88186124ec611f1.zip`
   - `competition_dataset.zip`
   - `dataset.zip`
3. Install the required libraries if they are missing:

```bash
pip install pandas numpy scikit-learn xgboost lightgbm catboost
```

4. Run every cell in order.
5. Allow the advanced rolling-validation cell to finish. It retrains multiple models and is the slowest section.
6. Use the final `submission.csv` written after the last cell completes.

The baseline training cell writes an intermediate `submission.csv`. The advanced temporal residual cell then overwrites it with the final prediction, so the notebook must be run through the last cell.

## Input Files :

The competition zip should contain:

```text
dataset/
  train.csv
  test.csv
  sample_submission.csv
```

The notebook can also locate an already extracted `dataset` folder.

## Package Contents

| File | Purpose |
|---|---|
| `source_code.ipynb` | Complete executable solution |
| `approach.md` | Modelling and validation approach |
| `feature_engineering.md` | Feature families and their motivation |
| `relevant_source_files.md` | Execution order and file inventory |
| `tools_used.md` | Runtime libraries and development tools |

## Final Pipeline

The notebook combines:

- XGBoost and LightGBM base models
- high-demand probability classification
- raw-categorical CatBoost
- RoadType-aware model routing
- day-48 to day-49 temporal-shape transfer
- validation-ranked temporal slope residuals
- circular rolling and phase-shift features
- a final phase-local Ridge correction

The advanced configuration corresponds to the `93.4017` reference-check candidate.
