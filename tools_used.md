# Tools Used

## Runtime Libraries

| Library | Purpose |
|---|---|
| `pandas` | CSV loading, joins, grouped statistics, feature tables, and output |
| `numpy` | Vectorized numerical calculations, clipping, cyclic features, and residual arithmetic |
| `xgboost` | Multi-seed boosted-tree regression ensemble |
| `lightgbm` | Base regressor and high-demand classifier |
| `catboost` | Raw-categorical auxiliary regression model |
| `scikit-learn` | KMeans, StratifiedKFold, StandardScaler, Ridge, and validation utilities |
| `zipfile` | Extraction of the official competition zip |
| `pathlib` | Portable paths |
| `math` | Exponential temporal-curve calculations |
| `warnings` | Suppression of non-critical training warnings |

Install the main dependencies with:

```bash
pip install pandas numpy scikit-learn xgboost lightgbm catboost
```

## Why Each Model Family Is Used

- XGBoost and LightGBM provide strong numeric tabular predictions.
- LightGBM classification supplies an explicit high-demand probability.
- CatBoost learns categorical interactions without imposing ordinal meaning.
- Ridge provides stable, regularized residual corrections for the temporal feature families.

## Development Tools

| Tool | Purpose |
|---|---|
| Jupyter Notebook | Complete executable source and reviewer-facing workflow |
| Google Colab / local Jupyter | Notebook execution |
| PowerShell `Compress-Archive` | Final package creation |

## Runtime Note

The final advanced section performs rolling validation and repeatedly retrains model components. It is substantially slower than the baseline stage. Reviewers should allow the final cell to finish before collecting `submission.csv`.
