# Phase 2: Ranking Prompt

Spawn a single ranking agent that sees all generated alternatives, the original hypothesis (if any), and the problem context.

## Key instructions for the ranker

- **Deduplicate**: Multiple agents may independently identify the same cause with different framing. Group these and note which analysts converged — independent convergence is a plausibility signal (but note it is a weaker signal when all agents share the same LLM substrate).
- **Include the original**: Rank the original hypothesis fairly alongside generated alternatives. If "None," rank only the alternatives.
- **Use structured scoring**: Score each hypothesis against each symptom/requirement using the rubric below BEFORE producing a holistic ranking. This prevents anchoring on first impressions.
- **Note relationships**: Flag hypotheses that are mutually exclusive or potentially compounding.
- **Randomize presentation**: If you notice yourself favoring hypotheses presented earlier, explicitly re-evaluate later ones.

## Scoring Rubric

For each hypothesis, score on these dimensions (1-3 scale):

| Dimension | 1 (Weak) | 2 (Moderate) | 3 (Strong) |
|-----------|----------|--------------|-------------|
| **Explanatory power** | Accounts for some symptoms | Accounts for most symptoms | Accounts for all symptoms |
| **Specificity** | Vague, hard to test | Somewhat testable | Makes precise, testable predictions |
| **Parsimony** | Requires many assumptions | Requires some assumptions | Minimal assumptions needed |
| **Falsifiability** | Difficult to disprove | Can be narrowed down | Clear test would confirm or rule out |

Then apply these multipliers:
- **Independent convergence**: If 2+ agents independently proposed the same hypothesis, multiply total score by 1.5
- **Evidence quality**: If the hypothesis came with specific, concrete evidence to check (not generic), multiply by 1.2

Compute a final score for each hypothesis. Use the scores to inform ranking, but exercise judgment — the rubric is a guide, not a straitjacket. If a hypothesis scores lower but has a uniquely important insight, note that.

## Output per hypothesis (ranked #1 to #N)

1. **Hypothesis**: One-line summary
2. **Scores**: Explanatory power / Specificity / Parsimony / Falsifiability (with multipliers noted)
3. **Plausibility**: High / Medium / Low with brief justification
4. **Key evidence to check**: The single most discriminating test (drawn from what the generators proposed)
5. **Sources**: Which analyst(s) proposed it (Outside View, Inside View, Devil's Advocate, Change Analyst, etc.)
