# Module 2b · From a PDF to a Dataset with a Multimodal Model

UChicago PME · AI Training Materials

Read papers as page images with a vision model, extract structured selectivity records, and score them against a curated reference. The controlled experiment is the core of the module: the same worker and the same prompt, given `pdftotext` on one arm and the rendered page images on the other. Only the input modality changes, so any difference is the modality.

Domain: 8 sensor papers from WetSenseBench (R. Ding et al.), each with curated pairwise selectivity labels. One is a scanned PDF with no text layer, where only the image arm works.

## Files
| File | Description |
|------|-------------|
| `Module3_Multimodal_PDF_Extraction.ipynb` | the teaching notebook (executed, with outputs) |
| `data/papers/` | the 8 source PDFs |
| `data/text/` | `pdftotext` output per paper (the text arm input) |
| `data/images/` | pages rendered to PNG (the image arm input) |
| `data/gold/` | curated gold extraction per paper |
| `data/pairs_gold.csv` | the 92 pair RAPIDS comparison table, filtered to these 8 papers |
| `cache/{text,image,image_v2}/` | shipped worker outputs (precise and exhaustive prompts), so the notebook runs with no key |
| `figures/*.png` | the headline chart and the climb chart |
| `wetsensebench_multimodal_extracted.csv` | the dataset the notebook produces |

## How to run
- Offline: open the notebook and run top to bottom. `USE_CACHE = True` reads the shipped outputs, so no key and no cost.
- Live on your own papers: render PDFs into `data/images/<id>/` with `pdftoppm -png -r 150` and `data/text/<id>.txt` with `pdftotext`, set an OpenRouter key, and set `USE_CACHE = False`. The worker is `qwen/qwen3-vl-30b-a3b-thinking`. Chemical name matching is a deterministic synonym map, no judge model needed.

## What it teaches
1. Two ways to feed a PDF to a model: linearized text, and rendered page images sent as `image_url` blocks.
2. A Pydantic schema as the contract for the dataset you want out.
3. A controlled experiment: same worker, same prompt, text input vs image input, scored against gold.
4. Honest chemical scoring: pair recall and precision, a synonym tolerant but isomer strict name matcher, direction, and figure or table grounding.
5. The result: on clean born-digital papers the two arms are close. The image arm is more precise. On the scanned paper the text arm scores zero and only the image arm works. Multimodal is not always better, but it is the only option when the text layer fails.
6. Raising the multimodal arm from 0.69 to 0.86 honestly: a selectivity ordering recovery metric (the exact pair metric is unfair when the gold curates its pairs unevenly), plus a two pass read with an exhaustive prompt.

## Result
Controlled experiment, pooled over 97 gold pairs: text F1 0.68, image F1 0.69, with the image arm more precise (0.81 vs 0.68). On the scanned paper the text arm scores 0.00 and the image arm 0.80. Raising the multimodal arm: under selectivity ordering recovery, a two pass read reaches F1 0.86 (recall 0.79, precision 0.95), up from 0.69. Extraction passes cost about 0.20 dollars total on the worker.

## Citation
Data and task adapted from WetSenseBench and the T3 text to data project (R. Ding et al.). Cite the corresponding work when using this material.

## Live mode key on Colab

For live mode (`USE_CACHE = False`), provide an OpenRouter key one of two ways:
- Colab Secret (recommended): click the key icon in the left sidebar, add a secret named `OPENROUTER_API_KEY`, and toggle notebook access on. The notebook reads it automatically.
- Or run the cell and paste the key when prompted (getpass; it is not stored in the notebook).
