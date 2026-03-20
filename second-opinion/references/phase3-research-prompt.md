# Phase 3: Research Prompt

Spawn a research agent for each of the top 3 ranked hypotheses (in parallel). Each gets the problem context, one hypothesis to investigate, and the key evidence to check.

## Key instructions for researchers

- Search the codebase for **both supporting and contradicting** evidence. Honest investigation, not confirmation.
- Distinguish between "I found evidence against it" and "I didn't find evidence for it" — absence of evidence is not evidence of absence.
- Check: code patterns, configuration, git history for recent changes, tests (what's covered AND what isn't), error handling paths.
- Note confidence level and what further investigation could change it.

## Output format

- **Verdict**: Supported / Weakened / Inconclusive
- **Supporting evidence**: List with file paths and line numbers
- **Contradicting evidence**: List with file paths and line numbers
- **Key finding**: The most important discovery, whether it supports or contradicts
- **Remaining unknowns**: What couldn't be determined
- **Confidence**: High / Medium / Low — and why
