# Module 1 · Classical Machine Learning on Tabular Data

UChicago PME · AI Training Materials

Predict acidic-OER electrocatalyst performance from a *composition + synthesis + testing* recipe (literature-mined records), then use SHAP to find the driving factors. A generic composition-to-property template you can adapt to your own chemistry or materials project. Two parallel targets:

- **1a, Activity:** predict overpotential η₁₀ (lower is better).
- **1b, Stability:** predict log decay rate (lower is more stable). Same template, new target.

## Files
| File | Description |
|------|-------------|
| `Module1_Tabular_ML_OER.ipynb` | 1a, activity: the full teaching walkthrough |
| `Module1b_Tabular_ML_Stability.ipynb` | 1b, stability: the same pipeline on a new target |
| `OER_activity.csv` | Activity dataset (1847 rows, GBK-encoded) |
| `OER_stability.csv` | Stability dataset (453 rows, UTF-8) |
| `requirements.txt` | Dependencies |
| `figures/` | Exported figures (for slides; `1b_*` are stability) |

## How to run
- **Local:** open a notebook in `jupyter lab` and run top to bottom. Install with `pip install -r requirements.txt`.
- **Google Colab:** upload the notebook and its CSV; Section 0 auto-installs `shap` and `xgboost`.

## What you'll learn (1a teaches it; 1b reuses it on a new target)
1. Real-world table gotchas: encoding (GBK vs UTF-8), and selecting a high-quality subset by bibliometrics (impact factor, citations, recency) to align with the paper. XGBoost test R² is about 0.84 for activity; Random Forest and the committee reach about 0.88 to 0.90 for stability.
2. Feature engineering: map each element to 8 atomic properties instead of one-hot. This is the step that matters most.
3. Progressive modeling: linear, decision tree (overfitting), Random Forest and XGBoost (the strong tabular baseline), MLP (scale first), and a committee ensemble that adds an uncertainty estimate.
4. SHAP interpretation: which factors drive performance, and how to spot testing-condition confounders. Comparing 1a and 1b shows that activity and stability are governed by partly different factors.
5. (Optional, in 1a) Unsupervised views: correlation ranking and PCA / t-SNE maps.

## Citation

Teaching rewrite of the data-mining workflow from:

> R. Ding, J. Liu, K. Hua, X. Wang, X. Zhang, M. Shao, Y. Chen, J. Chen, "Leveraging data mining, active learning, and domain adaptation for efficient discovery of advanced oxygen evolution electrocatalysts," *Science Advances* **11**, eadr9038 (2025). doi:10.1126/sciadv.adr9038

- Paper: https://www.science.org/doi/full/10.1126/sciadv.adr9038
- Code and data (DASH): https://github.com/ruiding-uchicago/DASH · Dryad: 10.5061/dryad.nk98sf83g
