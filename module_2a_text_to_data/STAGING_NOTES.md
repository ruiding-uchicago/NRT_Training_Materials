# Module 2 staging notes (text → dataset extraction)

Source: the T3 FET-biosensor project (R. Ding et al.), `T3_Apr04_KDD_revise/`. This folder holds a small, self-contained slice to seed the teaching notebook. Students swap in their own corpus to reuse it.

## What's here
- `data/papers/<DOI>.txt` — 75 papers (25 gas / 25 liquid / 25 bio), full scraped text (~20-40 KB each), dense with numeric values and units. Drawn from a pool of 421 gas / 272 liquid / 58 bio single-record gold papers.
- `data/gold_core.json` — hand-curated ground truth, keyed by DOI, list-valued: `{DOI: [record, ...]}`. All 75 are single-record. Each record has the 8 core fields below. Split into dev/test (~37 each) inside the notebook.
- `reference/human_init_prompt.md` — the real seed prompt (8 core fields). The clean starting point a student would write.

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

Extraction output is list-valued: `{"records": [ {8 fields}, ... ]}`. A paper can report several devices or analytes; in this slice each is one record (89% of the full corpus is single-record, so the slice stays simple while the schema stays honest).

The three field types map cleanly onto the three things worth teaching about extraction metrics: categorical needs exact match, numeric needs unit normalization, and chemical names need an LLM judge because `P3HT` = `poly(3-hexylthiophene)`.

## Build plan (decisions already settled)
- Enforce the schema with the API structured-output / tool-use feature, not "ask for JSON, then parse."
- Worker model is cheap (Claude Haiku); optimizer and judge are strong (Sonnet or Opus).
- Add a `source_quote` field to the notebook schema (not in the real gold, so not scored). The model returns the sentence it pulled each record from. This compresses hallucination and turns the audit into "check the value against its quote."
- Flow: setup (key via getpass) → Pydantic schema → single-doc extract and eyeball one → dev/test split of the 14 gold → per-field metric with the three match types → iterate the prompt on dev → measure once on held-out test → scale to all docs → audit (schema-valid rate, missing-field rate, unit sanity, spot-check rows against source_quote) → make-it-yours.
- It is dev/test, not train/test (no weights are fit). The test set is tiny, so report the before/after delta as directional, not precise. That caveat is itself a lesson, and it echoes Module 1.
- Optional capstone: one strong-model pass that reads the dev failures and rewrites the prompt, scored on the same held-out test. Real auto-optimizers (TextGrad, DSPy) are an extension pointer, not built here.

## Scale-up resources (left in the source project, not copied)
- 30-field full schema and production prompt: `T3_Apr04_KDD_revise/final_prompt.docx`.
- Real evaluation logic (7 metrics, Hungarian record matching, LLM chemical-judge with cache): `T3_Apr04_KDD_revise/#0fix_science_correctness/scientific_audit.py`.
- 30-field reference outputs: `T3_Apr04_KDD_revise/json_output_raw_nov25_1688/<DOI>.json`.

## Citation
R. Ding et al., the T3 / text-to-data paradigm (FET-sensor corpus). Cite the corresponding paper when the module is finalized.
