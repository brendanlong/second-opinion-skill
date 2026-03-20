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

The core idea: when you already have a theory, confirmation bias kicks in. To counter this, you'll spawn agents that **don't know the hypothesis**, so their alternatives are genuinely independent. Some agents investigate the specific system with tools while others reason purely from general knowledge — this tool access distinction is what creates genuine perspective diversity.

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

**Spawn all selected agents in parallel** using the Agent tool (type: `general-purpose`). Construct each agent's prompt following the guidance in the sub-agents reference — it specifies what each agent sees, whether it uses tools, and what output format to request.

## Step 3: Rank

Once all agents finish, read `references/phase2-ranking-prompt.md` and spawn a single ranking agent. It sees all generated alternatives, the original hypothesis (so it can rank it fairly alongside the alternatives), and the problem context.

## Step 4: Synthesize

Read `references/output-format.md` for the report template. Present the final report directly to the user.
