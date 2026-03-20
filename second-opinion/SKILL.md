---
name: second-opinion
description:
  Challenge a working hypothesis by generating debiased alternatives. Use when asked to stress-test an assumption,
  play devil's advocate, get a fresh perspective on a bug, or question whether a plan is solid.
license: MIT
compatibility: Requires an agent that supports spawning sub-agents (e.g. Claude Code Agent tool)
---

# Second Opinion

You're about to challenge the current working hypothesis — whether it's yours or the user's — by getting independent perspectives that aren't anchored to the existing theory.

The core idea: when you already have a theory, confirmation bias makes you look for supporting evidence and overlook contradictions. To counter this, you'll spawn agents that **don't know the hypothesis**, so their alternatives are genuinely independent.

## Gotchas

- **Never leak the hypothesis into Outside View or Inside View prompts.** The entire value of the skill depends on these agents being unanchored. Double-check your prompt substitutions before spawning.
- If the problem context is too vague (e.g., "something is broken"), generators will produce generic output. Ask the user for specific symptoms, errors, or constraints before proceeding.
- When all generators converge on the same cause, that's a strong plausibility signal — but verify the problem context wasn't so narrow that convergence was trivial.
- The original hypothesis surviving as #1 is a valid outcome. The value was in stress-testing, not replacing.

## Step 1: Extract Context

Read the conversation and identify two things:

**Problem context** — the observable facts. Symptoms, errors, constraints, requirements, what's been tried. Strip out interpretations and theories — just the raw situation. This is what most agents will see.

**Hypothesis** — the current working theory. This might be:
- Something the user stated ("I think it's a race condition")
- Something you proposed ("This looks like a connection pool exhaustion issue")
- A plan being evaluated ("this plan is good and complete")
- Nothing — sometimes there's no theory yet, and that's fine

**Mode** — detect from context whether this is:
- **Debugging/diagnosis**: A bug, error, or unexpected behavior. Hypothesis = proposed root cause.
- **Plan review**: A proposed plan or approach. Hypothesis = "this plan is sound." Agents look for gaps and risks instead of root causes.

**Arguments** — check `$ARGUMENTS` for:
- `--quick` flag: Skip the research phase (Phase 3). Just brainstorm and rank.
- Any other text: Treat as an explicit hypothesis override, replacing whatever you extracted from conversation.

If you can't identify a hypothesis and none was provided in arguments, that's okay. You'll skip the devil's advocate agent and adjust the other prompts slightly. The skill still works — it just generates alternatives without a specific theory to challenge.

**Before proceeding**: verify the problem context contains enough specific detail (symptoms, errors, components involved) for generators to produce concrete hypotheses. If it's too vague, ask the user for more detail.

## Step 2: Generate Alternatives

Spawn three agents **in parallel** using the Agent tool (type: `general-purpose`):

1. **Outside View** — gets problem context only (NO hypothesis). Prompt it as an independent consultant reasoning from base rates, common patterns, and statistical thinking: "What are the most common causes of this type of symptom in general?"

2. **Inside View** — gets problem context only (NO hypothesis). Prompt it as a senior engineer reasoning from deep system knowledge, component interactions, and domain-specific failure modes: "Given this specific system, what subtle interactions could cause these symptoms?"

3. **Devil's Advocate** — gets problem context AND hypothesis. Prompt it to argue against the current theory and propose alternatives that are specifically different: "What if the team is looking at the right symptoms but the wrong layer?"

If there's no hypothesis, skip the Devil's Advocate (spawn only 2 agents).

Each agent should produce 2-3 alternatives, each with: the proposed cause/risk, why it's plausible, and what evidence would confirm or rule it out. For plan review mode, reframe as risks/gaps rather than root causes.

Tell each agent it has been **deliberately not given** the team's current theory (or for the devil's advocate, that its job is to challenge it). This framing matters — it prevents anchoring.

## Step 3: Rank

Read `references/phase2-ranking-prompt.md` for the detailed prompt.

Once all generators finish, spawn a single ranking agent. It sees:
- All generated alternatives from Phase 2
- The original hypothesis (so it can rank it fairly alongside the alternatives)
- The problem context

The ranker deduplicates (multiple agents may independently identify the same cause — that's a signal of plausibility), consolidates, and ranks all hypotheses by explanatory power, specificity, and parsimony.

## Step 4: Research (skip if --quick)

Read `references/phase3-research-prompt.md` for the detailed prompt.

Take the top 3 ranked hypotheses and spawn a research agent for each **in parallel**. Each gets:
- The problem context
- One hypothesis to investigate
- Its rank and the key evidence the ranker identified

Research agents have full tool access. Their job is to search the codebase for **both supporting and contradicting** evidence. Honest investigation, not confirmation.

If `--quick` was specified, skip this phase entirely. The report will note that it's based on reasoning alone, not codebase investigation.

## Step 5: Synthesize

Read `references/output-format.md` for the report template.

Present the final report directly to the user. The report should:
- State the original hypothesis and what emerged
- Rank all hypotheses with evidence summaries
- Highlight the single most valuable insight
- Give actionable next steps

If the original hypothesis survived as #1 after being challenged, say so — that's a positive outcome. The value was in stress-testing it, not in replacing it.

Adapt the language for the mode (debugging vs. plan review) as described in the output format reference.
