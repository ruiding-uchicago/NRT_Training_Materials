# Module 2a · From Papers to a Dataset (LLM Extraction + Prompt Optimization)

UChicago PME · AI Training Materials

Turn a corpus of papers into a clean, validated table with an LLM, then climb the accuracy with a measured loop. Starting from a plain first-draft prompt, held-out test field accuracy goes from about 0.68 to 0.83 by stacking three levers: prompt optimization, few-shot examples, and a second optimization pass. The evaluation is what makes each step a decision instead of a guess.

Domain: 150 papers from the T3 FET-biosensor corpus (R. Ding et al.), with hand-curated ground truth.

## Files
| File | Description |
|------|-------------|
| `Module2_Text_to_Dataset.ipynb` | The teaching notebook (executed, with outputs) |
| `data/papers/` | 150 papers, full text |
| `data/gold_core.json` | hand-curated ground truth, 8 fields |
| `data/split3.json` | train / val / test split (60 / 39 / 51) |
| `data/cache/` | shipped DeepSeek outputs + judge cache, so the notebook runs with no key |
| `reference/*.md` | the prompt versions: baseline, optimized, few-shot, showcase |
| `FET_sensor_extracted_dataset.csv` | the dataset the notebook produces |

## How to run
- Offline: open the notebook and run top to bottom. `USE_CACHE = True` reads the shipped outputs, so no key and no cost.
- Live on your own papers: set an OpenRouter key and `USE_CACHE = False`. Worker is `deepseek-v4-flash`, optimizer is `deepseek-v4-pro`.

## What it teaches
1. A Pydantic schema as the contract for a clean dataset.
2. Three kinds of extraction metric: exact for categories, unit-normalized for numbers, and a synonym + morphology-tolerant LLM judge for chemicals.
3. train / val / test discipline and metric-driven prompt optimization (the loop DSPy and TextGrad automate).
4. Stacking levers to climb 0.68 to 0.83 on held-out test: prompt optimization, few-shot examples, and re-optimization. A stronger worker model did not help, so the cheap worker stays.
5. Scale and audit into a clean CSV.

## Citation
Data and task adapted from the T3 / text-to-data project (R. Ding et al.). Cite the corresponding paper when using this material.

## Live mode key

For live mode (set `USE_CACHE = False`), paste your OpenRouter key into the `OPENROUTER_API_KEY = ""` line near the top of the notebook, for example `"sk-or-v1-..."`. That is the only step. You can instead set an `OPENROUTER_API_KEY` environment variable and leave the line blank.
