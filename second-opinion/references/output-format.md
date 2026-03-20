# Phase 4: Output Format

Use this template to synthesize the final report. Adapt the section headers based on mode (diagnosis vs. plan review).

---

## Report Template

```markdown
## Second Opinion Report

### Summary

{1-2 sentence summary: what was challenged, what emerged}

### Agents Used

{List which brainstorming agents were selected and why, e.g.: "Outside View, Inside View, Devil's Advocate, Change Analyst (symptoms appeared suddenly after a known event), Problem Frame (vague symptom description)"}

### Original Hypothesis

> {The original hypothesis, or "No prior hypothesis — alternatives were generated from scratch."}

### Ranked Alternatives

{For each hypothesis, ranked by plausibility after research:}

#### #{RANK}: {Hypothesis title}

- **Plausibility**: {High/Medium/Low} {— adjusted from initial ranking if research changed it}
- **Scores**: {Explanatory power / Specificity / Parsimony / Falsifiability, with multipliers}
- **Sources**: {Which generators proposed it; note if multiple converged independently}
- **Evidence summary**: {Key supporting and contradicting evidence from research, or from ranking if --quick}
- **How to verify**: {What specific evidence would prove this hypothesis}
- **How to rule out**: {What specific evidence would disprove this hypothesis}

{Repeat for each hypothesis}

### Key Insight

{The single most valuable thing that emerged from this analysis. What should the user pay attention to that they might not have considered? This might be a new hypothesis, a flaw in the original, or a connection between alternatives.}

### Recommended Next Steps

1. {Most impactful action}
2. {Second action}
3. {Third action, if applicable}
```

---

## Adaptation Notes

**For diagnosis**: Focus on root causes, available evidence, and diagnostic steps.

**For plan review**: Reframe as:
- "Ranked Alternatives" → "Identified Risks & Gaps"
- "How to verify" → "How to detect this risk"
- "How to rule out" → "How to mitigate"
- "Recommended Next Steps" → "Recommended Plan Adjustments"

**For --quick mode** (no research phase): The evidence summary will be thinner — based only on the generators' reasoning and the ranker's assessment, not investigation. Note this limitation in the summary.

**When the original hypothesis survives**: If the original hypothesis ends up ranked #1 even after challenge, that's a positive signal — say so explicitly. The value was in stress-testing the assumption, not in replacing it.
