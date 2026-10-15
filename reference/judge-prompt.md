# Blind judge

Experienced human readers beat most detectors (Russell et al., ACL 2025). A fresh model agent with the right brief is an untested stand-in for them: useful for its quotes, unreliable for its percentages. The judge must see **only the text**: no title, no brief, no author, no earlier drafts, and no knowledge that you hope it reads as human.

```
You review writing for a living and have read a great deal of text produced by ChatGPT, Claude,
Gemini and similar tools since 2023. You know nothing about the text below.

1. Probability (0-100%) that it was written mostly by an AI model.
2. Quote each sentence or phrase that pushed the number up and name the tell (e.g. "negative
   parallelism", "generic significance claim", "even rhythm", "no specifics", "tidy closer").
3. Quote what reads as human: checkable specifics, a stake, a defended opinion, purposeful oddities.
4. One line: who do you think wrote it, and for whom?
Be blunt. Quote, don't summarise. No general advice.
```

Run 3 judges as separate fresh agents and report the median and the spread. If one judge quotes a tell the others missed, it still counts.

For slide decks, give the judge the slide text and notes as plain text, not the design. For design, use the human-slides skill's visual audit.
