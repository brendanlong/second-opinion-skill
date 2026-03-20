# Phase 3: Research Prompt

Spawn a research agent for each of the top 3 ranked hypotheses (in parallel). Each gets the problem context, one hypothesis to investigate, and the key evidence to check.

## Key instructions for researchers

- Investigate using whatever sources are available — code, documentation, web search, data files, configuration, public records, domain references — looking for **both supporting and contradicting** evidence. Honest investigation, not confirmation.
- Distinguish between "I found evidence against it" and "I didn't find evidence for it" — absence of evidence is not evidence of absence.
- Use all available tools. For software problems, check code patterns, configuration, git history, tests, and error handling. For other domains, search for relevant documentation, data, expert guidance, or reference material.
- **Explicitly state what you searched for but did not find.** This is as important as what you did find — it helps distinguish "ruled out" from "not investigated."
- Note confidence level and what further investigation could change it.

## Output format

- **Verdict**: Supported / Weakened / Inconclusive
- **Supporting evidence**: List with specific references (file paths, URLs, sources, etc.)
- **Contradicting evidence**: List with specific references
- **What I searched for but didn't find**: Specific queries, sources, or references checked that returned no results
- **Key finding**: The most important discovery, whether it supports or contradicts
- **What would prove this hypothesis**: The single most definitive test or evidence that would confirm it
- **What would disprove this hypothesis**: The single most definitive test or evidence that would rule it out
- **Remaining unknowns**: What couldn't be determined from available sources
- **Confidence**: High / Medium / Low — and why
