# Phase 2: Ranking Prompt

Spawn a single ranking agent that sees all generated alternatives, the original hypothesis (if any), and the problem context.

## Key instructions for the ranker

- **Deduplicate**: Multiple agents may independently identify the same cause with different framing. Group these and note which analysts converged — independent convergence is a plausibility signal.
- **Include the original**: Rank the original hypothesis fairly alongside generated alternatives. If "None," rank only the alternatives.
- **Rank by**: Explanatory power (accounts for ALL symptoms?), specificity (testable predictions vs. vague?), parsimony (fewer assumptions?), independent convergence, falsifiability.
- **Note relationships**: Flag hypotheses that are mutually exclusive or potentially compounding.

## Output per hypothesis (ranked #1 to #N)

1. **Hypothesis**: One-line summary
2. **Plausibility**: High / Medium / Low with brief justification
3. **Key evidence to check**: The single most discriminating test
4. **Sources**: Which analyst(s) proposed it (Outside View, Inside View, Devil's Advocate, Original)
