# Limits: read before using any result against a person

1. **No detector, including this one, is proof.** OpenAI withdrew its own classifier for low accuracy. Every vendor publishes a false-positive rate above zero.
2. **Non-native and very formal writers get flagged more often** (Liang et al. 2023). So do people writing in a template-heavy genre: legal, medical abstracts, grant boilerplate.
3. **Edited and mixed text is a grey zone.** Human drafts polished by a model, or model drafts rewritten by a human, have no clean label.
4. **Short texts are noise.** Under about 300 words, every statistic here is unstable.
5. **Local LM scores depend on the model and the genre.** Run `--calibrate` with known human and known AI samples from the same genre before trusting a threshold.
6. **Use this to edit your own work,** or to decide where a human reviewer should look. Never use it as the sole basis for an academic-integrity or employment decision about someone else.
7. **Judge percentages are not probabilities of authorship.** In our tests, blind AI judges gave the same text scores from 8% to 60%. Report what they quote, not the number.
