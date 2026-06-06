# NRT Training Materials

AI and machine learning teaching modules for UChicago PME PhD students, built from published research (DASH, *Science Advances* 2025; and the T3 / WetSenseBench text-to-data work). Each notebook is self-contained and reproduces from shipped cache with no API key.

## Modules

| Module | Topic | Open in Colab |
|--------|-------|---------------|
| 1a | Tabular ML on an activity dataset: featurize, model lineup, committee, SHAP | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ruiding-uchicago/NRT_Training_Materials/blob/main/module_1_tabular_ML/Module1_Tabular_ML_OER.ipynb) |
| 1b | Tabular ML on a stability dataset: the same template on a new target | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ruiding-uchicago/NRT_Training_Materials/blob/main/module_1_tabular_ML/Module1b_Tabular_ML_Stability.ipynb) |
| 2a | LLM extraction: turn papers into a clean table, with schema, evaluation, and prompt optimization | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ruiding-uchicago/NRT_Training_Materials/blob/main/module_2a_text_to_data/Module2a_Text_to_Dataset.ipynb) |
| 2b | Multimodal extraction: read PDF pages as images, text vs image, and a selectivity ordering metric | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ruiding-uchicago/NRT_Training_Materials/blob/main/module_2b_multimodal_pdf/Module2b_Multimodal_PDF_Extraction.ipynb) |

## How to run

- **Colab (no local setup):** click a badge above. The first cell of each notebook clones this repo into the Colab VM and enters the module folder, so `Run all` reproduces everything with no API key and no cost.
- **Locally:** clone the repo, `cd` into a module folder, and run the notebook top to bottom. The first cell is a no-op outside Colab.
- **Live mode (your own data):** set `USE_CACHE = False` and an OpenRouter key. Module 2b live mode also needs `poppler-utils` (`apt-get install poppler-utils` on Colab) to render PDFs.

## Note on sources

These modules reproduce analyses over data derived from published papers. Paper texts (Module 2a) and rendered pages (Module 2b) are included only as needed to reproduce the teaching examples and remain the property of their respective publishers. They are provided here for noncommercial educational use. Rights holders who want material removed can open an issue.

## Citation

R. Ding et al., *Science Advances* 11, eadr9038 (2025), doi 10.1126/sciadv.adr9038, and the DASH repository. Cite the corresponding work when using this material.
