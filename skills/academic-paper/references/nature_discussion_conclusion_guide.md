# Nature-Style Discussion & Conclusion Guide

> **Purpose**: Supplementary writing patterns for Discussion and Conclusion sections, adapted from nature-writing curated examples. These emphasise technical interpretation, rival explanations, and bounded claims — suited to papers in CS, engineering, and natural sciences.

## Discussion

### Default Structure

`central advance -> what the evidence means -> relation to prior work -> constraints / limitations -> future use or open questions`

### Core Rule

**Results = what we observed.** **Discussion = how we understand it, and when it may fail.**

Never re-summarise Results figure by figure. Interpretation, not repetition.

### Drafting Rules

1. **Address rival explanations before generalising** — reviewers look for this
2. **Hedging must match evidence strength** — do not promote a "consistent with" finding to "demonstrates" wording
3. **Limitations come from inside the paper**, not from generic disclaimers — name the specific condition or dataset where the result stops holding
4. **Future-work statements should be honest open questions**, not marketing

### Sentence Syntax

Discussion sentences interpret:
- `may reflect`
- `suggests that`
- `could indicate`
- `is likely due to`
- `may facilitate`

### Common Failure Modes

| Failure | Fix |
|---------|-----|
| Re-summarising Results instead of interpreting | Each paragraph should add meaning, not repeat numbers |
| Skipping rival explanations | Dedicate one paragraph to alternative interpretations |
| Omitting boundaries — when does the interpretation stop? | Name the specific condition where the claim breaks |
| Generic future-work | State a concrete next question, not "more research is needed" |

### Comparison with the IMRaD Discussion Template

The IMRaD template (Discussion §5) adds two subsections that are standard in social-science papers but often absent or embedded in Nature-style discussion: **Theoretical Implications** and **Practical Implications**. If these are relevant to the paper, keep them; the funnel above flows into them naturally after the "relation to prior work" paragraph.

---

## Conclusion

### Default Structure

`contribution -> decisive evidence -> implication -> boundary`

### Drafting Rules

1. **No new data. No unsupported promises.**
2. Restate the central contribution in one sentence — do not summarise each Result figure
3. The implication must be narrower than or equal to the scope of the evidence
4. A bounded future-work pointer is acceptable; generic "more work is needed" is not

### Overclaim Check

Before finalising, run this:

- [ ] Does each claim trace back to evidence in this paper?
- [ ] Are mechanism words (`demonstrates`, `proves`, `establishes`) backed by the right study design?
- [ ] Is the scope of the implication narrower than or equal to the scope of the evidence?
- [ ] Is any "first" claim genuinely first within a stated scope?

### Limitation Guidance

Prefer limitations tied to task/setting boundaries:
1. **Data regime limitation** — e.g., only short sequences, specific sensor setup
2. **Assumption limitation** — e.g., controlled viewpoints only
3. **Deployment scope limitation** — e.g., requires offline processing

Avoid framing conclusion around fixable implementation flaws (unless they critically define the method's scope).

### Distinction: Technical Defect vs Scope Limitation

| Type | Definition |
|------|-----------|
| Technical defect | Underperforms strong baselines on key metrics |
| Scope limitation | Bounded by current task setting; still competitive vs SOTA |

### Template

> This paper addresses [problem] by proposing [method]. The key idea is [core insight], which enables [main benefit]. Experiments show [main gains] across [datasets/settings]. A current limitation is [scope boundary], and extending to [future setting] is an important next step.

## Reference

Patterns adapted from `nature-writing/static/fragments/section/discussion.md`, `nature-writing/static/fragments/section/conclusion.md`, `nature-writing/references/conclusion.md`, and `nature-writing/references/article-architecture.md`.
