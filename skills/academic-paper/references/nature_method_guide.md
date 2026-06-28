# Nature-Style Method Writing Guide

> **Purpose**: Supplementary method-section writing patterns adapted from nature-writing's curated examples. Suits papers with technical pipelines or modular methods — common in CS, engineering, and natural sciences. Complements the IMRaD methodology template, which provides a broader social-science structure (Design → Participants → Instruments → Analysis → Ethics).

## Overview

A well-written Method section answers three questions per module:
1. **Why this module exists** (motivation)
2. **What it actually does** (mechanism)
3. **How it helps the overall result** (evidence/role)

If any element is missing, the module reads as a black box.

## Pre-Writing Checklist

Before drafting Method, confirm:

- [ ] Task formulation: inputs, outputs, scope are defined
- [ ] Pipeline/overview figure: does one exist? It anchors the section
- [ ] Notation: defined once and will stay consistent
- [ ] Reproducibility scope: code, weights, data — what will be released

## Method Writing Workflow

```
1. Draw the pipeline figure sketch
2. Map subsections from the sketch
3. For each subsection, plan: motivation → module design → technical advantages
4. Write module design first (concrete backbone)
5. Add motivation and technical advantages afterwards
```

## The Three-Element Pattern (per module)

### 1) Module Design
- Describe representation/network/data-structure details
- Write the forward process clearly: `given input -> step 1 -> step 2 -> output`

**Sentence skeleton:**
- `We represent ... with ...`
- `Given [input], we first ... then ... finally ...`
- `This produces [output], which is used for ...`

### 2) Motivation of This Module
- Problem-driven logic: because problem X exists, we design module Y
- Keep it tied to a concrete limitation, not a generic gap

**Typical opening sentences:**
- `A remaining problem/challenge is ...`
- `However, we ...`
- `Previous methods have difficulty in ...`

### 3) Technical Advantages of This Module
- Explain why this module is better than alternatives
- Tie advantage to measurable behaviour when possible

## Overview Subsection

The overview should include:
1. One to two sentences for task setting
2. One to two sentences for core contribution
3. If pipeline/framework is novel, point to overview figure
4. Tell readers what each subsection covers

## Forbidden Vague Phrases

Replace these with actual reproducible information:

| Vague phrase | Replace with |
|-------------|-------------|
| "under standard conditions" | specific conditions |
| "using routine methods" | the actual method |
| "data were analyzed statistically" | which test, which software |
| "the method was validated" | validated against what, with what metric |
| "samples were randomly assigned" | how randomisation was performed |

## Clarity Checks

**Logic-level**: After finishing, summarise the Method writing logic. Is it smooth and easy to follow?

**Paragraph-level**: The first sentence of each paragraph should make readers immediately understand what this paragraph is about. One paragraph = one message.

**Sentence-level**: For each sentence, is the *motivation* for including that sentence explicit? Check sentence-to-sentence flow and term consistency.

## Implementation Details

Place hyperparameters (layer count, feature dimensions, coordinate transforms, normalisation) near the end of Method or in a dedicated Implementation Details subsection.

## Reference

Patterns adapted from `nature-writing/static/fragments/section/method.md` and `nature-writing/references/method.md`. See `nature-writing/references/examples/method/` for worked examples.
