# NRT Training Materials

AI and machine learning teaching modules for UChicago PME PhD students, built from published research (DASH, *Science Advances* 2025; and the T3 / WetSenseBench text-to-data work). Each notebook is self-contained and reproduces from shipped cache with no API key.

## Modules

| Module | Topic | Open in Colab |
|--------|-------|---------------|
| 1a | Tabular ML on an activity dataset: featurize, model lineup, committee, SHAP | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ruiding-uchicago/NRT_Training_Materials/blob/main/module_1_tabular_ML/Module1_Tabular_ML_OER.ipynb) |
| 1b | Tabular ML on a stability dataset: the same template on a new target | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ruiding-uchicago/NRT_Training_Materials/blob/main/module_1_tabular_ML/Module1b_Tabular_ML_Stability.ipynb) |
| 1c | Dataset gallery: the same tabular pipeline across five materials domains, swap one config to your own field | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ruiding-uchicago/NRT_Training_Materials/blob/main/module_1_tabular_ML/Module1c_Dataset_Gallery.ipynb) |
| 1d | Bring your own table: click to upload a local file, mark the input columns and one output column, get an ensemble model | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ruiding-uchicago/NRT_Training_Materials/blob/main/module_1_tabular_ML/Module1d_Bring_Your_Own_Table.ipynb) |
| 2a | LLM extraction: turn papers into a clean table, with schema, evaluation, and prompt optimization | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ruiding-uchicago/NRT_Training_Materials/blob/main/module_2a_text_to_data/Module2a_Text_to_Dataset.ipynb) |
| 2b | Multimodal extraction: read PDF pages as images, text vs image, and a selectivity ordering metric | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ruiding-uchicago/NRT_Training_Materials/blob/main/module_2b_multimodal_pdf/Module2b_Multimodal_PDF_Extraction.ipynb) |
| 2c | Literature to dataset to model: a topic (OpenAlex OQL) to a full-text corpus, an LLM-extracted table, and a model (closed loop) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ruiding-uchicago/NRT_Training_Materials/blob/main/module_2c_literature_to_dataset/Module2c_Literature_to_Dataset.ipynb) |
| 3 | Distillation: a large teacher's blind, rejection-sampled reasoning fine-tunes a small student (0.01 to 0.825) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ruiding-uchicago/NRT_Training_Materials/blob/main/module_3_distillation/Module3_Distillation.ipynb) |

## How to run

- **Colab (no local setup):** click a badge above. The first cell of each notebook clones this repo into the Colab VM and enters the module folder, so `Run all` reproduces everything with no API key and no cost.
- **Locally:** clone the repo, `cd` into a module folder, and run the notebook top to bottom. The first cell is a no-op outside Colab.
- **Live mode (your own data):** set `USE_CACHE = False` and an OpenRouter key. Module 2b live mode also needs `poppler-utils` (`apt-get install poppler-utils` on Colab) to render PDFs.

## Note on sources

These modules reproduce analyses over data derived from published papers. Paper texts (Module 2a) and rendered pages (Module 2b) are included only as needed to reproduce the teaching examples and remain the property of their respective publishers. They are provided here for noncommercial educational use. Rights holders who want material removed can open an issue.

## Citation

Cite the corresponding work when using this material.

- Modules 1a, 1b: R. Ding et al., *Science Advances* 11, eadr9038 (2025), doi 10.1126/sciadv.adr9038, and the DASH repository.
- Module 1c: datasets loaded through matminer (Ward et al., *Comput. Mater. Sci.* **152**, 60-69, 2018) and the Matbench suite (Dunn et al., *npj Comput. Mater.* **6**, 138, 2020); each of the five datasets keeps its own original source, listed in full in the notebook's Citations cell.
- Module 2a: Rui Ding, Zixin Ding, Rodrigo Pires Ferreira, Yuxin Chen, and Junhong Chen. Text-Twin-Translation (T³): A Full-Stack Machine Learning Framework for Functional Material-Device Systems Discovery. In Proceedings of the ACM SIGKDD Conference on Knowledge Discovery and Data Mining (KDD '26), 2026.
- Module 2b: data and task adapted from WetSenseBench and the T³ text-to-data project (R. Ding et al.; same work as Module 2a).
- Module 2c: corpus and metadata via OpenAlex (CC0, https://openalex.org); full text from open-access publishers; extraction with DeepSeek. Cite the underlying papers and OpenAlex.
- Module 3: task adapted from solubench (the solubility benchmark); cite its source when building on this material.
