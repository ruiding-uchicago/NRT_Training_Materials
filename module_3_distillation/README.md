# Module 3 · Distillation: teach a small model to reason from a large one

UChicago PME · AI Training Materials

Take a large model's *reasoning* and press it into a small model you can host yourself. The task is chemical: given a compound (SMILES) and two candidate solvents, decide which one dissolves it better at a given temperature (solubench, task 1). The dataset ships only the answer, A or B, never the why. A large teacher (DeepSeek v4-flash) writes a short step-by-step reason for each training compound, and a small student (Qwen3-1.7B) is LoRA-fine-tuned on those reasons. Then we measure the student before and after.

The module is built around one methodological point that decides whether distillation works at all.

## The lesson: how you generate the chains is the whole game

We ran both recipes on the same task, same model, same compute.

- **The trap — feed the teacher the answer.** Hand the teacher the gold label, even softened to "for checking only," and it stops predicting and starts rationalizing: when a simple principle like polarity gives the right answer it cites polarity, and when polarity would be wrong it quietly switches to pi-stacking or dual hydrogen bonding, whatever reaches the shown answer. The chains read well but which principle is invoked is set by the answer, not the chemistry. A student trained on them scored **0.41, below a coin flip.**
- **The fix — rejection sampling (STaR).** Let the teacher answer **blind**, with no gold in the prompt, then keep only the chains whose final answer is independently correct and throw the rest away. Every kept chain is now a real derivation. The blind teacher is right **84.6%** of the time here, so from 2500 attempts we keep 2114 genuinely-correct chains.

Trained on the blind, rejection-sampled chains, the student goes from **0.01 to 0.825** on 200 held-out compounds. Its accuracy where it answers (0.846) matches the blind teacher's own accuracy (0.846): the reasoning capability transferred, not just the prose.

## Files
| File | Description |
|------|-------------|
| `Module3_Distillation.ipynb` | the teaching notebook (two tracks, one `USE_CACHE` switch) |
| `data/chains_task1_train.jsonl` | 2114 blind, rejection-sampled teacher chains (the training set) |
| `data/chains_task1_train_blind.raw.jsonl` | all 2500 blind attempts before filtering, for reproducibility |
| `data/chains_task1_train.answerfed.jsonl` | the contaminated answer-fed chains, kept as the cautionary control |
| `data/task1_test.jsonl` | 500 held-out compounds, answer only |
| `data/eval_before.json`, `data/eval_after.json` | the shipped before/after metrics |
| `figures/before_after.png` | the before vs after chart |

## How to run
- **Cache (default, no GPU, no key):** open the notebook and run top to bottom with `USE_CACHE = True`. It reads the shipped chains and the before/after metrics and reproduces the story offline.
- **Live (a Colab T4 GPU):** open in Colab, set the runtime to a T4 (Runtime, Change runtime type, T4), set `USE_CACHE = False`, and Run all. This LoRA-fine-tunes the student for real and re-scores it. Regenerating the chains is optional and the only step that needs a DeepSeek key; leave `DEEPSEEK_KEY` blank to use the shipped chains.

## What it teaches
1. Knowledge distillation end to end: a dataset of answers becomes reasoning supervision a small model can absorb.
2. Why answer-conditioned chains fail: a teacher that sees the answer justifies it rather than finds it, and the justification is decorrelated from truth.
3. The correct recipe: teacher predicts blind, keep only what it gets right (rejection sampling / STaR), then fine-tune.
4. An honest before/after: a format-fair baseline and a reported answer rate, so the lift is a real reasoning gain and not a format-compliance artifact.
5. LoRA fine-tuning of a 1.7B student that fits a free Colab T4.

## Using it on your own field
Replace solubench task 1 with any answer-only dataset in the same shape, one question and a short verifiable label. Generate chains with the teacher blind, keep the correct ones, rebuild the chat set, and retrain. The pipeline does not change.

## Live mode key
Regenerating chains (optional) needs a DeepSeek key. Paste it into the `DEEPSEEK_KEY = ""` line near the teacher-generation cell. Training and evaluation need no key, only a T4 GPU.

## Citation
Task adapted from solubench. Cite the corresponding work when using this material.
