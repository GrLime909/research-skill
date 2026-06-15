# Nature-Style Introduction Variants

> **Purpose**: Supplementary introduction-writing patterns adapted from nature-writing's curated examples. These variants suit empirical papers where contribution is framed around a technical challenge or pipeline module — common in CS, engineering, and natural sciences. Use alongside `paper_structure_patterns.md` §IMRaD Introduction, not as a replacement.
>
> **When to use**: The paper introduces a new method/pipeline/system rather than testing a hypothesis in a social-science sense. The existing IMRaD Introduction (Context → Problem → Gap → RQs → Significance) still works; these variants offer finer-grained structures for the "Problem → Prior Attempts → Gap" and "Present Study" framing.

## Core Funnel (default)

`field scale -> bottleneck -> prior attempts -> unresolved gap -> present study`

This is the same funnel IMRaD uses, just expressed more compactly. Each paragraph does one job:

1. **Establish the field stake** — why the topic matters at a discipline level
2. **Identify bottleneck** — specific limitation in existing practice
3. **Prior work + remaining gap** — synthesise, do not list; the last gap is the one this paper addresses
4. **Present study** — contribution and approach, not detailed results

## Pipeline/Technical-Challenge Variants

For method-heavy papers, replace the generic "prior work → gap" flow with one of these patterns.

### Task-then-application (task is niche)
1. Define the task in one sentence (`what output` from `what input`)
2. Explain task objective
3. Introduce application value with 2-3 representative scenarios

### Application-first (task is familiar)
1. Open with application importance
2. Skip formal task definition
3. Optionally state target requirement (accuracy/efficiency/robustness)

### Open-with-challenge
1. Start with task/application importance
2. Immediately show how representative previous methods work
3. Immediately expose unresolved failure + technical reason (same paragraph)
4. Use this opening as bridge to deeper prior-work discussion

### Technical-challenge (3 variants, for the prior-work paragraphs)

**Version 1 — existing task, existing methods:**
`general challenge -> traditional methods -> recent methods -> remaining challenge this paper solves`

Build a challenge chain. Ensure the final limitation is exactly the challenge your method solves.

**Version 2 — existing task, insight rooted in classical work:**
`mainstream methods + limitation -> classical method containing similar insight -> why still insufficient -> modern methods + unresolved reason -> bridge`

Use when your insight has historical roots that lend it conceptual backing.

**Version 3 — novel task, no direct prior methods:**
`state goal -> decompose into N concrete challenge points (First/Second/Finally) -> transition`

For each point, state both the observable limitation and the technical reason.

### Pipeline-variant (4 versions, for the "present study" paragraph)

**Version 1 — one contribution, multiple advantages:**
1. Introduce core framework/representation
2. Point to teaser figure
3. Key novelty in one sentence
4. Implementation steps (`Specifically, ...`)
5. Multiple advantages (`In contrast ...`, `Another advantage ...`)

**Version 2 — two contributions:**
1. Framework + novelty sentence
2. Teaser figure
3. Contribution 1 + advantage
4. Remaining challenge
5. Contribution 2 as response

**Version 3 — one new module on an existing pipeline:**
1. Prior pipeline setup
2. New module as key innovation
3. Observation motivating module design
4. Module mechanism
5. Comparison against generic alternatives

**Version 4 — observation-driven:**
1. State key innovation first
2. Intuitive observation as motivation
3. Implementation details
4. Technical advantage + empirical gain

## Not-Recommended Pattern

Avoid "abstract-only" introductions that hide concrete method design behind abstract insights to appear novel. Reviewers detect this. Better to state core contribution implementation clearly.

## Quality Checklist

1. Does the first sentence of each paragraph state its message?
2. Does each paragraph carry exactly one message?
3. Are technical challenge, technical reason, and solved mechanism all explicit?
4. Are claims aligned with experiment evidence?
5. Is terminology stable across all sections?
6. Is the transition from "what is known" to "what this paper does" explicit, not implied?
7. Does the final paragraph preview the contribution, not detailed results?

## Reference

Patterns adapted from `nature-writing/references/introduction.md` and `nature-writing/static/fragments/section/intro.md`. See `nature-writing/references/examples/introduction/` for worked examples of each variant.
