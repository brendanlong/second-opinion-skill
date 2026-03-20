# Sub-Agent Definitions

These are the available agents. The orchestrator selects which ones are appropriate based on the mode, hypothesis, and problem context. **Always include the core agents; add an on-demand agent only when it covers a blind spot the core agents genuinely cannot address.**

Typical run: 2-3 agents (Outside View + Inside View, plus Devil's Advocate if a hypothesis exists). On-demand agents are added only when warranted by the specific problem.

## Tool Access

Agents differ in whether they can use tools (read files, grep code, web search, etc.):

- **No tools**: The agent reasons from general knowledge only. Instruct it in the prompt: "Do not use tools. Reason from general knowledge, base rates, and common patterns only." Its value comes from NOT being anchored to implementation details.
- **With tools**: The agent actively investigates — reads files, searches code, checks configs, queries data. Instruct tool-using agents: "Explicitly state what you searched for but did not find."

This distinction is what makes Outside View and Inside View genuinely different perspectives. Without it, they converge on the same output.

## Common Output Format

Every agent must produce 2-3 alternatives (or risks/gaps in plan review mode), each with:
- The proposed cause/risk
- Why it's plausible
- **Evidence found** (for tool-using agents: specific files, code patterns, data; for non-tool agents: patterns, base rates, reasoning)
- **What specific evidence would prove it**
- **What specific evidence would disprove it**

## Core Agents (always include)

### Outside View
- **Sees hypothesis**: No
- **Tool access**: No — reasons purely from base rates and general knowledge without examining the specific system.
- **Purpose**: Independent consultant reasoning from base rates, common patterns, and statistical thinking. Asks "what usually causes problems like this?" or "what usually goes wrong with plans like this?"
- **Prompt guidance**: Frame as an independent consultant. Instruct it NOT to use tools — it should reason from general knowledge only.

### Inside View
- **Sees hypothesis**: No
- **Tool access**: Yes — can read files, grep code, check configs, inspect data, and use any available tools.
- **Purpose**: Domain expert who actively investigates the specific system. Reads code, checks configurations, examines data. Asks "given what I can see in this specific system, what subtle factors or interactions could explain these symptoms?"
- **Prompt guidance**: Frame as a domain expert. Adapt the expertise to match the problem domain — a senior engineer for software bugs, a tax specialist for tax questions, a horticulturist for lawn problems, etc. Instruct it to **actively investigate using available tools**.

### Devil's Advocate
- **Sees hypothesis**: Yes
- **Tool access**: Yes — can investigate to find specific evidence against the hypothesis.
- **Purpose**: Argues against the current hypothesis and proposes alternatives that are specifically different. Asks "what if we're looking at the right symptoms but drawing the wrong conclusion?"
- **Prompt guidance**: Give it both the problem context AND the hypothesis. Its job is to challenge the theory. Instruct it to **look for concrete evidence** that contradicts the hypothesis, not just reason abstractly. Ask for alternatives that are specifically different from the hypothesis.
- **Skip when**: No hypothesis exists.

## On-Demand Agents (add only when warranted)

Most runs need only the core agents. Add an on-demand agent only when the problem context has a specific characteristic that warrants a perspective the core agents cannot provide.

### Change Analyst
- **Sees hypothesis**: No
- **Tool access**: Yes — can check git history, changelogs, recent modifications, deployment logs, etc.
- **Purpose**: Reasons about what changed recently. Asks "what recent changes could explain these symptoms?" Focuses on temporal correlation.
- **Prompt guidance**: Frame as an investigator focused on recency. Instruct it to **investigate what changed** — check git history, recent commits, config changes, deployments.
- **Select when**: Diagnosis mode, especially when symptoms appeared recently or suddenly, or issues that started after a known event. Also useful when the plan involves changing existing systems.

### Problem Frame
- **Sees hypothesis**: No
- **Tool access**: No — its value is in reframing the problem conceptually, not in investigating specifics.
- **Purpose**: Challenges whether the problem statement itself is accurate. Asks "are we looking at the right problem?"
- **Prompt guidance**: Frame as a skeptical analyst who questions the premise. Instruct it NOT to use tools. Ask it to propose alternative framings of the problem.
- **Select when**: The problem description seems vague, symptoms don't obviously connect, the user's framing makes assumptions that could be wrong, or problems that have resisted prior solutions.

### Pre-mortem
- **Sees hypothesis**: Yes
- **Tool access**: Yes — can examine the actual implementation to project realistic failure modes.
- **Purpose**: Assumes the plan/hypothesis has already failed and works backward to explain why. Surfaces implementation risks that logical critique misses.
- **Prompt guidance**: Give it the problem context AND the hypothesis/plan. Tell it: "It's 6 months from now and this plan has failed. Tell the story of why it failed." Instruct it to **examine the actual system** to ground its failure scenarios in real implementation details. Ask for failure scenarios with early warning signs.
- **Select when**: Plan review mode, especially for multi-step plans, major decisions, or anything with significant implementation risk.

### Simplicity Advocate
- **Sees hypothesis**: Yes
- **Tool access**: Yes — needs to see the actual code/plan to propose concrete simplifications.
- **Purpose**: Argues for the simplest possible solution. Challenges complexity, questions whether each component is necessary, and proposes radically simpler alternatives.
- **Prompt guidance**: Give it the problem context AND the hypothesis/plan. Instruct it to **examine the actual implementation** and propose 1-2 dramatically simpler alternatives. Each alternative should include what it sacrifices and what evidence would show whether the sacrificed parts are actually needed.
- **Select when**: Plan review mode, especially when the proposed plan feels heavy or adds significant complexity.

### Red Team
- **Sees hypothesis**: Yes
- **Tool access**: Yes — needs to examine actual attack surface, configurations, permissions, etc.
- **Purpose**: Actively tries to break the proposed solution. Looks for vulnerabilities, abuse vectors, and failure modes under adversarial conditions.
- **Prompt guidance**: Give it the problem context AND the hypothesis/plan. Tell it to think like an adversary. Instruct it to **examine the actual system** for concrete vulnerabilities — check permissions, configurations, input handling, trust boundaries.
- **Select when**: The problem involves security, trust, incentive design, public-facing systems, or adversarial behavior.

## Agent Selection Guide

After extracting the mode and problem context in Step 1, select agents using these heuristics:

| Mode | Always | Add if warranted |
|------|--------|-----------------|
| **Diagnosis** | Outside View (no tools), Inside View (tools), Devil's Advocate* (tools) | Change Analyst, Problem Frame, Red Team |
| **Plan review** | Outside View (no tools), Inside View (tools), Devil's Advocate* (tools) | Pre-mortem, Simplicity Advocate, Problem Frame, Red Team |

*Skip Devil's Advocate when no hypothesis exists.

**Selection criteria for on-demand agents:**
- Add an on-demand agent only when its perspective addresses a specific aspect of the problem that the core agents cannot cover
- Most runs need only the 2-3 core agents — don't add on-demand agents by default
- When in doubt, prefer the agent that covers the most likely blind spot for this specific problem
- State which agents you selected and why before spawning them
