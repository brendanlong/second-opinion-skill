# Ranking Prompt

Spawn a single ranking agent that sees all generated alternatives, the original hypothesis (if any), and the problem context.

## Key instructions for the ranker

- **Deduplicate**: Multiple agents may independently identify the same cause with different framing. Group these and note which analysts converged — independent convergence is a plausibility signal.
- **Include the original**: Rank the original hypothesis fairly alongside generated alternatives. If "None," rank only the alternatives.
- **Use structured scoring**: Score each hypothesis using the rubric below BEFORE producing a holistic ranking. This prevents anchoring on first impressions.
- **Note relationships**: Flag hypotheses that are mutually exclusive or potentially compounding.
- **Randomize presentation**: Re-evaluate later hypotheses to counter order effects.
- **Weight concrete evidence**: Hypotheses backed by specific evidence found during investigation should be given more weight than those based purely on abstract reasoning. However, do not penalize the Outside View agent for lacking file-level evidence — its lack of tool access is intentional and its reasoning-based evidence is a valid, complementary perspective.
- **Preserve diversity**: Ensure at least one hypothesis from a distinctly different problem frame survives into the final ranking. If the top results all cluster around the same causal mechanism, promote the highest-scoring hypothesis from a different frame, noting it was promoted for diversity.
- **Rank all hypotheses**: Do not cut off at any fixed number. The full ranked list goes into the final report.

## Scoring Rubric

For each hypothesis, score on these dimensions (1-3 scale):

| Dimension | 1 (Weak) | 2 (Moderate) | 3 (Strong) |
|-----------|----------|--------------|-------------|
| **Explanatory power** | Accounts for some observations | Accounts for most observations | Accounts for all observations, or compellingly reframes which observations matter |
| **Specificity** | Vague, hard to test | Somewhat testable | Makes precise, testable predictions |
| **Parsimony** | Requires many assumptions | Requires some assumptions | Minimal assumptions needed |
| **Falsifiability** | Difficult to disprove | Can be narrowed down | Clear test would confirm or rule out |

Then apply these multipliers:
- **Independent convergence**: If 2+ agents independently proposed the same hypothesis, multiply total score by 1.2 (capped at 1.2 because agents share the same LLM substrate, making apparent convergence less independent than it seems)
- **Evidence quality**: If the hypothesis is backed by concrete evidence found during investigation (not just reasoning), multiply by 1.2

Compute a final score for each hypothesis. Use the scores to inform ranking, but exercise judgment — the rubric is a guide, not a straitjacket. If a hypothesis scores lower but has a uniquely important insight, note that.

## Output per hypothesis (ranked #1 to #N)

1. **Hypothesis**: One-line summary
2. **Scores**: Explanatory power / Specificity / Parsimony / Falsifiability (with multipliers noted)
3. **Plausibility**: High / Medium / Low with brief justification
4. **Key evidence**: The strongest supporting or contradicting evidence found (or the most discriminating test to run, if no investigation was done)
5. **Sources**: Which analyst(s) proposed it (Outside View, Inside View, Devil's Advocate, etc.)
