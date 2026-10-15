# ai-spotter

**Find out whether a piece of writing reads like AI, and see exactly which sentences give it away.**

This is a skill for [Claude Code](https://claude.com/claude-code), Anthropic's AI assistant that works on your computer. Once it's installed, you can say *"check this essay with ai-spotter"* and get back a plain report:

- Which sentences and habits look machine-written, quoted, each with a source for why it's a known sign.
- How the writing compares to text you know a human wrote, such as your own older work.
- What three independent "judges" think. Each judge is a fresh AI reviewer that sees only the text, nothing else, and gives a probability with quotes.

It's built for checking your **own** drafts before you send them. It is **not** a way to prove someone else cheated. No AI detector is accurate enough for that (see "Limits" below).

---

## How to install (about 2 minutes)

You need Claude Code installed. Then open a terminal and paste:

```bash
git clone https://github.com/subhankarshukla04/ai-writing-detector-skill ~/.claude/skills/ai-spotter
cd ~/.claude/skills/ai-spotter && python3 -m venv .venv && .venv/bin/pip install -r requirements.txt
```

That's it. Restart Claude Code and it will find the skill.

## How to use it

Just ask Claude Code in plain English:

- *"Run ai-spotter on my-essay.docx"*
- *"Check this cover letter for AI tells and show me the worst sentences"*
- *"Compare my report against my old essays in /samples and tell me what reads as AI"*

Works with Word (.docx), PowerPoint (.pptx), Markdown and plain text files.

---

## What it checks

| Layer | What it looks at | Based on |
|---|---|---|
| 1. Known tells | Words and phrasings AI overuses ("delve", "pivotal role", "it's not just X, it's Y", "serves as"), chatbot leftovers, formatting habits | Wikipedia's *Signs of AI writing* guide; studies of millions of research papers |
| 2. Writing rhythm | How much sentence lengths vary, vocabulary range, grammar habits | Published research on how AI prose differs from human prose |
| 3. Model signals | How predictable the text is to a small AI model (optional) | The methods behind GPTZero and *Binoculars* (a 2024 research detector) |
| 4. Blind judges | Three fresh AI reviewers who see only the text | Research showing experienced readers beat most detectors |

## How well does it work? We tested it

We ran it on 25 passages: 15 written by people before 2022 (essays and a management paper) and 10 written by a 2026 AI model on similar topics.

| Signal | Correct out of 25 | What that means |
|---|---|---|
| Sentence-length variation | **21** | The best single number. AI sentences are more even in length. |
| Binoculars (small models) | 19 | Useful supporting evidence. |
| Model predictability (GPT-2) | 17 | Weak. Here the AI text was actually *less* predictable than the human essays, the opposite of the old rule. |
| Count of known tells | 16 | Weak on its own. Newer models avoid the famous tells. |

**Takeaway:** no single number is reliable. The report shows each signal next to your human baseline, and only leans one way on the two signals that held up. The blind judges and the quoted tells are the most useful part.

This was a small test of one genre, so treat the numbers as a rough guide, not a guarantee.

## Limits (please read)

- No detector, including this one, can prove a person used AI. OpenAI withdrew its own detector in 2023 for low accuracy.
- Detectors wrongly flag non-native English writers more often (Stanford study, 2023).
- Very short texts (under about 300 words) can't be judged reliably.
- Use it to improve your own writing, or to decide where a human should look more closely. Never as the only evidence against someone.

---

## For technical users

```bash
# tells + style, compared with known-human text
.venv/bin/python scripts/ai_spotter.py draft.md --human my_old_essay.txt
# add model signals (downloads ~2.5 GB of small open models once, runs on CPU)
.venv/bin/python scripts/ai_spotter.py draft.md --human my_old_essay.txt --lm --binoculars
# measure which signals work for your own genre
.venv/bin/python scripts/ai_spotter.py --calibrate human_samples/ ai_samples/ --binoculars
```

| File | What it is |
|---|---|
| `SKILL.md` | Instructions Claude follows |
| `scripts/ai_spotter.py` | The checker: tells, stylometry, GPT-2 perplexity and burstiness, Binoculars (Qwen2.5-0.5B pair), calibration |
| `reference/detectors.md` | How GPTZero, Turnitin, Pangram, Binoculars and others work, with sources |
| `reference/judge-prompt.md` | The blind-judge prompt |
| `reference/limits.md` | When not to trust a result |

MIT licensed.
