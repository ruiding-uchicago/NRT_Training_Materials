# Module 2a staging notes (text to dataset extraction)

Source: the T3 FET-biosensor project (R. Ding et al.), `T3_Apr04_KDD_revise/`. This folder holds the self-contained teaching slice used by `Module2a_Text_to_Dataset.ipynb`. Students can swap in their own corpus to reuse the workflow.

## What's here
- `data/papers/<DOI>.txt` - 150 papers, balanced across gas, liquid, and bio FET-sensor examples, full scraped text.
- `data/gold_core.json` - hand-curated ground truth, keyed by DOI, list-valued: `{DOI: [record, ...]}`. All 150 examples are single-record in this teaching slice.
- `data/split3.json` - train / validation / test split of 60 / 39 / 51 papers.
- `data/cache/` - cached model and judge outputs so the notebook runs offline with `USE_CACHE = True`.
- `reference/human_init_prompt.md` - the seed prompt. Later files in `reference/` show optimized, few-shot, and final prompt versions.

## Schema: 8 core fields
| field | type | match metric |
|---|---|---|
| sensor_type | categorical (gas / bio / liquid) | exact match |
| detect_target | chemical | LLM-as-judge (synonym tolerant) |
| probe_material | chemical | LLM-as-judge |
| test_medium | chemical | LLM-as-judge |
| lower_detection_limit | number + unit | normalize unit, then compare |
| upper_detection_limit | number + unit | normalize unit, then compare |
| test_operating_temperature (celcius) | number | numeric |
| pH_value | number or range (-1 for gas) | numeric |

Extraction output is list-valued: `{"records": [ {8 fields}, ... ]}`. A paper can report several devices or analytes; in this slice each is one record, so the notebook stays simple while the schema stays honest.

The three field types map cleanly onto the three things worth teaching about extraction metrics: categorical needs exact match, numeric needs unit normalization, and chemical names need an LLM judge because `P3HT` = `poly(3-hexylthiophene)`.

## Implemented flow
- Use a Pydantic schema as the local contract for validated records.
- Worker model is `deepseek/deepseek-v4-flash`; prompt optimizer is `deepseek/deepseek-v4-pro`, both through OpenRouter in live mode.
- Default `USE_CACHE = True` reproduces the module without an API key.
- Flow: setup -> schema -> single-paper extraction -> field-specific evaluation -> prompt optimization on validation -> held-out test reporting -> scale to all papers -> audit missing fields and unit parsing -> export CSV.
- The notebook uses train / validation / test discipline even though no model weights are fit. Validation chooses prompts; test is reported once for the headline number.

## Scale-up resources (left in the source project, not copied)
- 30-field full schema and production prompt: `T3_Apr04_KDD_revise/final_prompt.docx`.
- Real evaluation logic (7 metrics, Hungarian record matching, LLM chemical-judge with cache): `T3_Apr04_KDD_revise/#0fix_science_correctness/scientific_audit.py`.
- 30-field reference outputs: `T3_Apr04_KDD_revise/json_output_raw_nov25_1688/<DOI>.json`.

## Citation
R. Ding et al., the T3 / text-to-data paradigm (FET-sensor corpus). Cite the corresponding work when using this material.
