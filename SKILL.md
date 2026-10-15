---
name: ai-spotter
description: Check whether a piece of writing (essay, report, slide text, post, email) reads as AI-generated, and show exactly which sentences and habits give it away. Combines the documented tells (Wikipedia's Signs of AI writing), stylometry, local language-model signals (GPT-2 perplexity and burstiness, Binoculars) and blind judge agents. Use to audit your own drafts before shipping, or to point a human reviewer at where to look. Never as proof against a person.
---

# AI spotter

Run the steps in order: prepare, then tells, statistics and blind judges, then the report.

## 1. Prepare

- Get the text out of its container. `ai_spotter.py` reads `.txt`, `.md`, `.docx` and `.pptx` (slides and speaker notes are scored separately).
- Get a human baseline: known-human text in the same genre, ideally by the same author and written before late 2022. Without one, the statistical signals can't be read either way. With one, they compare like with like.
- Under about 300 words, skip the statistics and rely on the tells and the judges.

## 2. Tells (fast, explainable)

```bash
python scripts/ai_spotter.py draft.md --human baseline.txt
```

This lists every documented tell by paragraph or slide, with its source tag:
- [W] Wikipedia, *Signs of AI writing*
- [R] Reinhart et al. 2025, grammar

It also gives a weighted density per 1,000 words, set against your baseline's density. The tells it covers:
- era vocabulary (2023 to 2026 lists; counted only when 2 or more different words appear together)
- negative parallelism
- copula avoidance ("serves as")
- -ing analysis tails
- undue significance
- promotional tone
- vague attribution
- challenges/outlook endings
- template reveals
- chat residue and citation artifacts (`oaicite`, `turn0search`, `utm_source=chatgpt`)
- bold inline headers
- emoji formatting
- em dashes
- hedge stacks
- the rule of three

One tell alone means little; Wikipedia says so explicitly. Density and clustering matter.

## 3. Statistics (supporting evidence only)

```bash
python scripts/ai_spotter.py draft.md --human baseline.txt --lm --binoculars
```

| Signal | What it measures | AI-leaning when |
|---|---|---|
| Sentence-length variation | Spread of sentence lengths | Lower than baseline (even rhythm) |
| MATTR-50 | Vocabulary diversity | Different from baseline |
| Participial clauses, nominalisations | Grammar habits LLMs overuse 1.5-5x (Reinhart et al., PNAS 2025) | Higher than baseline |
| GPT-2 perplexity (median sentence) | How predictable each sentence is to a language model | Lower than baseline |
| Perplexity burstiness | How much predictability varies | Lower than baseline |
| Binoculars | Log-perplexity divided by cross-perplexity, Qwen2.5-0.5B base/instruct pair | Lower than baseline |

- Calibrate before trusting any number. Small local models are far weaker than the 7B pairs in the Binoculars paper.
  ```bash
  python scripts/ai_spotter.py --calibrate human_dir/ ai_dir/ --binoculars
  ```
  This prints each signal's medians for both sets and the best split. A signal that can't separate your own samples should be ignored for that genre. The README has the calibration we ran and what held up.
- The most predictable sentences (lowest perplexity) are listed for a by-hand check. They are usually the generic ones.
- First run downloads GPT-2 (about 0.5 GB) and the Qwen pair (about 2 GB). Everything runs on CPU.

## 4. Blind judges

Experienced human readers detect AI text better than most tools (Russell et al., ACL 2025). Fresh AI agents given the same brief are an untested stand-in for those readers. In our own runs they caught the structural tells the scripts missed, but their percentages varied widely (the same text drew 8% from one judge and 60% from another).
- Spawn **3 fresh agents** and give each only the text and the prompt in `reference/judge-prompt.md`. Don't include the brief, the author, earlier drafts, or the outcome you want.
- Report every quoted tell, plus the median and spread of the percentages. Never present a judge percentage as the probability that a person used AI.
- When re-checking after edits, use new agents. An agent that saw the earlier draft is no longer blind.

## 5. Report

Write the result as evidence, not a verdict:
- The tells, quoted, with where they occur.
- The statistics, set against the baseline, and whether calibration showed them meaningful for this genre.
- The judges: their quotes first, then the median and spread.
- The plain reading, for example: "reads as edited human writing with three generic paragraphs (2, 5, 7)", or "machine signals cluster in the intro and conclusion, and all three judges quoted the same closing line".
- What to fix. If the companion `write-human` skill is installed, hand it the flagged sentences.

## Do not

- Do not present any score as proof that a person used AI. Read `reference/limits.md`: false positives, bias against non-native writers, short texts.
- Do not "fix" flagged text with synonym swaps, typos, homoglyphs or zero-width characters. That hides the symptom and worsens the writing. Fix it by adding real substance or cutting.
- Do not let the judge see anything but the text.

How the detectors work and how reliable they are: `reference/detectors.md`.
