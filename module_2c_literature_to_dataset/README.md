# Module 2c · From a Live Literature Search to a Dataset to a Model

UChicago PME · AI Training Materials

Type a topic, harvest the open-access papers for it, read their **full text**, turn them into a clean table with an LLM, and model it. A closed loop from a search box to a prediction: **OQL search → full-text corpus → extracted dataset → model.** Built for the step where people actually get stuck: turning a pile of papers into a dataset.

## The loop
1. **Query (OQL).** Build a query on [openalex.org](https://openalex.org) by clicking filters; it writes it in OpenAlex Query Language, e.g. `works where open access is (true) and year >= (2019) and title/abstract has (battery capacity and mAh) and type is (article)`. Paste it in; the notebook translates it to the API (no key, no login).
2. **Corpus.** OpenAlex returns the matching papers (it shows the total count) and which have a downloadable open-access PDF. One-click download of the paper list.
3. **Full text.** Each open-access PDF is downloaded and parsed to text. Publishers that block automated download are skipped; Nature Communications, DOE OSTI, Scientific Reports and similar come through.
4. **Extract.** An LLM (DeepSeek, same idea as Module 2a) reads each full text and returns one structured record → `battery_dataset.csv`, with a one-click download.
5. **Model.** The table feeds a Module-1-style model to predict specific capacity, closing the loop.

## What a student edits (two highlighted blocks)
- **(1) the OQL query** — your topic/filters.
- **(2) the field definition** — `DOMAIN`, `TARGET`, `CATEGORICAL`, `NUMERIC`, `HINTS`, `CLASS_THRESHOLD`. This single block drives **both** the extraction prompt and the model features, so they can never fall out of sync. The prompt built from it is printed so you see exactly what gets extracted. Everything else runs as is.

## Two tracks
- **Cache (default, offline, no key):** `USE_CACHE = True` reads the shipped pre-crawl and runs the whole loop in seconds. Good for a live demo with no network risk.
- **Live:** `USE_CACHE = False` does it for real — OpenAlex needs no key; only the extraction step needs a DeepSeek key pasted into `DEEPSEEK_KEY`.

## Files
| File | Description |
|------|-------------|
| `Module2c_Literature_to_Dataset.ipynb` | the notebook (executed) |
| `cache/corpus_papers.csv` | pre-crawled paper list (top 1,500 by citation of the broadened battery query) |
| `cache/fulltext.jsonl.gz` | parsed full text for the 337 open-access papers that downloaded |
| `cache/battery_dataset.csv` | the LLM-extracted table, 295 rows, target (`specific_capacity_mAh_g`) last |
| `figures/2c_capacity_parity.png` | the closed-loop parity plot |

## Result (battery default)
The broadened query matches ~6,700 open-access battery papers; the shipped cache holds full text for 337 and an extracted table of 295. On a single 80/20 split (same style as Modules 1a–1d): predicting log10 specific capacity gives **R² ≈ 0.72**, and classifying high-capacity (>500 mAh/g) vs conventional gives **accuracy ≈ 0.87** (baseline 0.64). As a generalization test, 30 papers are set aside at the start (never in training or the test split); on these **brand-new** papers the classifier reaches **≈ 0.93** accuracy and regression R² ≈ 0.72. Top drivers: chemistry family (Li-S / sulfur high), electrode role, voltage window, cycle number. Energy density and other near-answer quantities are deliberately excluded.

## Honesty notes
- Full-text harvest is limited to open-access PDFs the publisher lets you download (~one quarter of OA links resolve; the reliable ones are Nature Communications, DOE OSTI, Scientific Reports, and repositories). It is targeted downloading of OA papers, not site crawling; keep the volume modest and use publisher TDM / CORE / Europe PMC for large-scale harvesting.
- Predicting an exact capacity value from literature-extracted descriptors tops out around R² 0.6–0.75; capacity is set mostly by chemistry plus many uncaptured, hard-to-standardize test conditions. The classification framing is the more robust result.

## Citation
Corpus and metadata via [OpenAlex](https://openalex.org) (CC0). Extraction with DeepSeek. Cite the underlying papers and OpenAlex when you build on this.
