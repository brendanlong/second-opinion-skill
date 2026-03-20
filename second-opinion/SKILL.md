---
name: second-opinion
description:
  Challenge a working hypothesis by generating debiased alternatives. Use when asked to stress-test an assumption,
  play devil's advocate, get a fresh perspective on a problem, or question whether a plan is solid.
license: MIT
compatibility: Requires an agent that supports spawning sub-agents (e.g. Claude Code Agent tool)
---

# Second Opinion

You're about to challenge the current working hypothesis — whether it's yours or the user's — by getting independent perspectives that aren't anchored to the existing theory.

The core idea: when you already have a theory, confirmation bias makes you look for supporting evidence and overlook contradictions. To counter this, you'll spawn agents that **don't know the hypothesis**, so their alternatives are genuinely independent. Some agents investigate the specific system with tools while others reason purely from general knowledge — this tool access distinction is what creates genuine perspective diversity.

## Gotchas

- **Never leak the hypothesis into prompts for agents that shouldn't see it.** The entire value of the skill depends on unanchored agents staying unanchored. Double-check your prompt substitutions before spawning.
- **Be thorough when extracting problem context.** List all observable facts before filtering. Review for hypothesis-driven omissions — don't unconsciously select details that support the hypothesis you're trying to exclude.
- If the problem context is too vague (e.g., "something is broken"), generators will produce generic output. Ask the user for specific symptoms, observations, or constraints before proceeding.
- When all generators converge on the same cause, that's a plausibility signal — but a weaker one than it appears, since all agents share the same LLM substrate. Verify the problem context wasn't so narrow that convergence was trivial.
- The original hypothesis surviving as #1 is a valid outcome. The value was in stress-testing, not replacing.
- **Respect the tool access rules.** Outside View must NOT use tools (its value is general-knowledge reasoning). Inside View and other tool-using agents SHOULD actively investigate. If you blur this distinction, the agents converge on identical output.

## Step 1: Extract Context

Read the conversation and identify:

**Problem context** — the observable facts. Symptoms, observations, constraints, requirements, what's been tried. Strip out interpretations and theories — just the raw situation. **Be thorough**: enumerate all observable facts before filtering. Then review your problem context for hypothesis-driven omissions — are you unconsciously including details that support the hypothesis and omitting ones that don't?

**Hypothesis** — the current working theory. This might be:
- Something the user stated ("I think it's a race condition", "I bet the soil pH is off")
- Something you proposed ("This looks like a connection pool exhaustion issue", "This is likely a nutrient deficiency")
- A plan being evaluated ("this plan is good and complete")
- Nothing — sometimes there's no theory yet, and that's fine

**Mode** — detect from context whether this is:
- **Diagnosis**: A problem, unexpected behavior, or something that needs explaining. Hypothesis = proposed root cause.
- **Plan review**: A proposed plan or approach. Hypothesis = "this plan is sound." Agents look for gaps and risks instead of root causes.

**Arguments** — check `$ARGUMENTS` for any text: treat as an explicit hypothesis override, replacing whatever you extracted from conversation.

If you can't identify a hypothesis and none was provided in arguments, that's okay. You'll skip agents that require a hypothesis and adjust accordingly. The skill still works — it just generates alternatives without a specific theory to challenge.

**Before proceeding**: verify the problem context contains enough specific detail for generators to produce concrete hypotheses. If it's too vague, ask the user for more detail.

## Step 2: Select and Spawn Agents

Read `references/sub-agents.md` for the full agent menu, tool access rules, and selection guide.

**Select agents**: Default to 2 core agents: Outside View (without tools) and Inside View (with tools). Add Devil's Advocate (with tools) if a hypothesis exists. Add an on-demand agent only when its perspective addresses a specific characteristic of the problem that the core agents cannot cover. State which agents you selected and why.

**Spawn all selected agents in parallel** using the Agent tool (type: `general-purpose`). Follow the prompt guidance in the sub-agents reference for each agent. Key rules:

- Agents marked "Sees hypothesis: No" get **only** the problem context. Never include the hypothesis.
- Agents marked "Sees hypothesis: Yes" get both problem context and hypothesis.
- Agents marked "Tool access: No" must be instructed to reason from general knowledge only, without using tools.
- Agents marked "Tool access: Yes" should actively investigate — read files, search code, check configs, query data — and provide concrete evidence alongside their hypotheses. They must also state what they searched for but did not find.
- Every agent must produce 2-3 alternatives (or risks/gaps in plan review mode), each with:
  - The proposed cause/risk
  - Why it's plausible
  - **Evidence found** (for tool-using agents: specific files, code patterns, data; for non-tool agents: patterns, base rates, reasoning)
  - **What specific evidence would prove it**
  - **What specific evidence would disprove it**
- Tell unanchored agents they have been **deliberately not given** the team's current theory. This framing matters.

## Step 3: Rank

Read `references/phase2-ranking-prompt.md` for the detailed prompt and scoring rubric.

Once all agents finish, spawn a single ranking agent. It sees:
- All generated alternatives from Step 2
- The original hypothesis (so it can rank it fairly alongside the alternatives)
- The problem context

The ranker uses a structured scoring rubric (explanatory power, specificity, parsimony, falsifiability) with multipliers for independent convergence (1.2x) and evidence quality (1.2x). It scores each hypothesis before ranking to prevent anchoring on first impressions. The ranker must preserve diversity — at least one hypothesis from a different problem frame should survive into the final ranking.

## Step 4: Synthesize

Read `references/output-format.md` for the report template.

Present the final report directly to the user. The report should:
- State which agents were used and why (noting tool access)
- State the original hypothesis and what emerged
- Rank all hypotheses with scores, evidence summaries, and verification/falsification criteria
- Highlight the single most valuable insight
- Give actionable next steps

If the original hypothesis survived as #1 after being challenged, say so — that's a positive outcome. The value was in stress-testing it, not in replacing it.

Adapt the language for the mode (diagnosis vs. plan review) as described in the output format reference.

If the ranked hypotheses suggest further investigation is needed, note that in the next steps — the main agent (you, after the skill completes) can do that investigation directly.
