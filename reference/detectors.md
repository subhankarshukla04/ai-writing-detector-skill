# How AI-text detectors work, and how well

Compiled October 2026. Vendor accuracy numbers are the vendors' own claims unless marked independent.

## Commercial detectors

| Detector | Method | Claimed / measured error | Source |
|---|---|---|---|
| GPTZero | Launched (2023) on perplexity (how predictable the text is to a language model) and burstiness (how much that predictability varies). Since late 2023 a deep-learning classifier trained on millions of documents, with sentence-level highlighting and a "Paraphraser Shield" against paraphrase and homoglyph tricks. Perplexity and burstiness remain as explanations. | Claims about 1% FPR on ESL essays after tuning | https://gptzero.me/news/how-ai-detectors-work/ |
| Turnitin | Splits a submission into overlapping segments of about 5-10 sentences, scores each sentence 0-1 and aggregates to a document %. Scores of 1-19% show only an asterisk, because false positives are higher there. | Claims under 1% document FPR when 20% or more of the text is AI; about 4% sentence-level FPR | https://www.turnitin.com/blog/understanding-false-positives-within-our-ai-writing-detection-capabilities |
| Pangram | Neural classifier. Trained with hard negative mining (retrain on what it got wrong) and "mirror prompts" (an AI twin for every human document). | Claims 0.01-0.02% FPR. Independent: the only detector to meet a 0.5% FPR cap in a Chicago Booth study of 1,992 pre-2020 human texts vs 1,992 AI texts (Jabarian & Imas 2025) | https://arxiv.org/abs/2402.14873 ; https://bfi.uchicago.edu/wp-content/uploads/2025/09/BFI_WP_2025-116.pdf |
| Originality.ai | Transformer classifier trained ELECTRA-style | Claims 98-99%. Independent reviews report 83-98% | https://originality.ai/blog/how-to-choose-an-ai-detector |
| Copyleaks | Proprietary classifier plus "AI Phrases", which shows how often a phrase comes from AI | Claims 0.03-0.2% FPR; independent figures vary and we could not verify one | https://copyleaks.com/ai-detector |

## Research methods (open)

- **DetectGPT** (Mitchell et al., ICML 2023). Model text sits at a local peak of the model's own log-probability, so small rewrites lower its score more than they lower human text's. https://arxiv.org/abs/2301.11305
- **Fast-DetectGPT** (Bao et al., ICLR 2024). The same idea computed by sampling, about 340x faster. MIT-licensed code. https://arxiv.org/abs/2310.05130
- **Binoculars** (Hans et al., ICML 2024). The score divides the text's log-perplexity under one model by the cross-perplexity between two related models. The divisor corrects for prompts that make any text look predictable. It reports above 90% detection at 0.01% FPR with Falcon-7B and Falcon-7B-instruct, with no training. BSD-3 code, "for academic purposes", English-strongest. https://arxiv.org/abs/2401.12070 ; https://github.com/ahans30/Binoculars
- **Ghostbuster** (Verma et al. 2023). Features from weaker models feed a trained classifier. https://arxiv.org/abs/2305.15047
- **Watermarks.** Kirchenbauer et al. 2023 bias token choice toward a secret "green list". SynthID-Text (Nature 2024) is deployed in Gemini. They only work if the vendor embeds them, and paraphrasing weakens them. https://arxiv.org/abs/2301.10226 ; https://www.nature.com/articles/s41586-024-08025-4

## What this skill implements, and what it doesn't

- **Implemented:**
  - Documented tells (Wikipedia)
  - Stylometry (sentence-length variance, lexical diversity, participial clauses, nominalisations; Reinhart et al. 2025)
  - GPT-2 perplexity and burstiness
  - Binoculars with a small Qwen2.5-0.5B base/instruct pair
- **Not implemented:** trained classifiers (Pangram, GPTZero's current model and Turnitin are closed) and watermark detection (needs the vendor's key).
- **Accuracy:** expect the local LM signals to be much weaker than the commercial classifiers. `--calibrate` measures how weak on your own genre. Treat tells and the blind judge as the main evidence, and the LM numbers as supporting.

## Reliability problems everyone should know

- **Bias against non-native writers.** GPT detectors "consistently misclassify non-native English writing samples as AI-generated" (Liang et al., Patterns 2023). https://arxiv.org/abs/2304.02819
- **Paraphrase attacks.** Recursive paraphrasing defeats watermark, neural and retrieval detectors (Sadasivan et al. 2023). https://arxiv.org/abs/2303.11156
- **OpenAI withdrew its own classifier** in July 2023. It caught 26% of AI text and flagged 9% of human text.
- **The RAID benchmark** (Dugan et al., ACL 2024): 6M+ generations, 11 models and 11 attacks. Detectors break under attacks, sampling changes and unseen models. https://aclanthology.org/2024.acl-long.674/
- **Experienced humans are good detectors.** Annotators who use ChatGPT heavily misclassified only 1 of 300 articles by majority vote, even after paraphrasing and "humanizer" tools. Their cues were vocabulary, formality, originality and clarity (Russell, Karpinska & Iyyer, ACL 2025). https://aclanthology.org/2025.acl-long.267/ . That is why this skill's last step is a blind judge.
