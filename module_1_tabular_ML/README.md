# Module 1 · Classical Machine Learning on Tabular Data

UChicago PME · AI Training Materials

Predict acidic-OER electrocatalyst performance from a *composition + synthesis + testing* recipe (literature-mined records), then use SHAP to find the driving factors. A generic composition-to-property template you can adapt to your own chemistry or materials project. Two parallel targets:

- **1a, Activity:** predict overpotential η₁₀ (lower is better).
- **1b, Stability:** predict log decay rate (lower is more stable). Same template, new target.
- **1c, Dataset Gallery:** the same pipeline across five small datasets from different domains, driven by a one-line `DATASET = "..."` config so you can run the one nearest your research.

## Files
| File | Description |
|------|-------------|
| `Module1_Tabular_ML_OER.ipynb` | 1a, activity: the full teaching walkthrough |
| `Module1b_Tabular_ML_Stability.ipynb` | 1b, stability: the same pipeline on a new target |
| `Module1c_Dataset_Gallery.ipynb` | 1c, gallery: the same pipeline across five domains (matminer datasets) |
| `OER_activity.csv` | Activity dataset (1847 rows, GBK-encoded) |
| `OER_stability.csv` | Stability dataset (453 rows, UTF-8) |
| `data/gallery/` | Cached (composition, target) CSVs for 1c, one per dataset (auto-written on first run) |
| `requirements.txt` | Dependencies |
| `figures/` | Exported figures (for slides; `1b_*` are stability, `1c_*` are the gallery) |

## How to run
- **Local:** open a notebook in `jupyter lab` and run top to bottom. Install with `pip install -r requirements.txt`.
- **Google Colab:** upload the notebook (1a/1b also need their CSV); Section 0 auto-installs the packages it needs (`shap`, `xgboost`, and for 1c also `matminer` and `pymatgen`).

## 1c, Dataset Gallery

The 1a pipeline (composition to physical-property features, model ladder, SHAP) applied across five small, composition-based datasets loaded through matminer. Swap the `DATASET` line to the domain closest to your work. Real numbers from a run (best-model test R²):

| `DATASET` | domain | rows | target | best model | test R² |
|---|---|---|---|---|---|
| `matbench_steels` | alloys / mechanical | 312 | yield strength (MPa) | XGBoost | 0.88 |
| `m2ax` | MAX-phase ceramics | 223 | bulk modulus (GPa) | XGBoost | 0.92 |
| `double_perovskites_gap` | photovoltaics | 1306 | band gap (eV) | MLP | 0.96 |
| `heusler_magnetic` | magnetics / spintronics | 1153 | magnetic moment (μ_B) | Linear | 0.55 |
| `expt_formation_enthalpy` | thermodynamics | 1196 | formation enthalpy (eV/atom) | XGBoost | 0.87 |

The low Heusler score is an honest result kept in on purpose: magnetic moment depends on structure and spin state, which composition-only features cannot fully capture. The notebook also shows one classification example (median split of the close-up target) and caches each dataset's (composition, target) under `data/gallery/` so re-runs are offline and fast.

### 1c dataset and tooling citations
Datasets load through matminer; references are matminer's own citation metadata.

- **matminer** - Ward, L. et al. "Matminer: An open source toolkit for materials data mining." *Comput. Mater. Sci.* **152**, 60-69 (2018). doi:10.1016/j.commatsci.2018.05.018
- **Matbench** (source of `matbench_steels`) - Dunn, A., Wang, Q., Ganose, A., Dopp, D., Jain, A. "Benchmarking materials property prediction methods: the Matbench test set and Automatminer reference algorithm." *npj Comput. Mater.* **6**, 138 (2020). doi:10.1038/s41524-020-00406-3. Steel data: Citrine Informatics, https://citrination.com/datasets/153092/
- `m2ax` - Cover, M. F., Warschkow, O., Bilek, M. M. M., McKenzie, D. R. "A comprehensive survey of M2AX phase elastic properties." *J. Phys.: Condens. Matter* **21**, 305403 (2009). doi:10.1088/0953-8984/21/30/305403
- `double_perovskites_gap` - Pilania, G. et al. "Machine learning bandgaps of double perovskites." *Sci. Rep.* **6**, 19375 (2016). doi:10.1038/srep19375. Data: Computational Materials Repository, https://cmr.fysik.dtu.dk/
- `heusler_magnetic` - Citrine Informatics, "University of Alabama Heusler database," https://citrination.com/datasets/150561/
- `expt_formation_enthalpy` - Kim, G., Meschel, S. V., Nash, P., Chen, W. "Experimental formation enthalpies for intermetallic phases and other inorganic compounds." *Sci. Data* **4**, 170162 (2017). doi:10.1038/sdata.2017.162

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
