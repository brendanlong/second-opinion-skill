# Sub-Agent Definitions

These are the available brainstorming agents. The orchestrator selects which ones are appropriate based on the mode, hypothesis, and problem context. **Always include the core agents; select 1-3 on-demand agents that fit the situation.**

Typical run: 4-5 brainstorming agents (3 core + 1-2 on-demand).

## Core Agents (always include)

### Outside View
- **ID**: `outside`
- **Sees hypothesis**: No
- **Purpose**: Independent consultant reasoning from base rates, common patterns, and statistical thinking. Asks "what usually causes problems like this?" or "what usually goes wrong with plans like this?"
- **Prompt guidance**: Frame as an independent consultant who has been deliberately not given the team's current theory. Ask for 2-3 alternatives, each with: the proposed cause/risk, why it's plausible, and **what specific evidence would confirm or rule it out**.
- **Best for**: All scenarios — provides empirical grounding that prevents tunnel vision.

### Inside View
- **ID**: `inside`
- **Sees hypothesis**: No
- **Purpose**: Domain expert reasoning from deep knowledge of the specific subject matter, how the relevant components interact, and domain-specific failure modes. Asks "given what we know about this specific situation, what subtle factors or interactions could explain these symptoms?"
- **Prompt guidance**: Frame as a domain expert who has been deliberately not given the team's current theory. Adapt the expertise to match the problem domain — a senior engineer for software bugs, a tax specialist for tax questions, a horticulturist for lawn problems, etc. Ask for 2-3 alternatives, each with: the proposed cause/risk, why it's plausible, and **what specific evidence would confirm or rule it out**.
- **Best for**: All scenarios — catches mechanism-level issues through detailed domain-specific analysis.

### Devil's Advocate
- **ID**: `devil`
- **Sees hypothesis**: Yes
- **Purpose**: Argues against the current hypothesis and proposes alternatives that are specifically different. Asks "what if we're looking at the right symptoms but drawing the wrong conclusion?"
- **Prompt guidance**: Give it both the problem context AND the hypothesis. Its job is to challenge the theory. Ask for 2-3 alternatives that are specifically different from the hypothesis, each with: the proposed cause/risk, why it's plausible, and **what specific evidence would confirm or rule it out**.
- **Skip when**: No hypothesis exists.
- **Best for**: Any scenario where a hypothesis has been identified.

## On-Demand Agents (select based on context)

### Change Analyst
- **ID**: `change`
- **Sees hypothesis**: No
- **Purpose**: Reasons about what changed recently. Asks "what recent changes could explain these symptoms?" Focuses on temporal correlation — what's different now compared to when things worked? This could be anything: a software deployment, a new medication, a seasonal shift, a policy change, a move to a new environment, etc.
- **Prompt guidance**: Frame as an investigator focused on recency who has been deliberately not given the team's current theory. Emphasize that the single most predictive signal in diagnosis is often what changed right before the problem started. Ask for 2-3 change-based hypotheses, each with: the proposed change, why it's plausible, and **what specific evidence would confirm or rule it out**.
- **Select when**: Diagnosis mode, especially when symptoms appeared recently or suddenly. Also useful in plan review if the plan involves changing existing systems or processes.
- **Best for**: Sudden onset problems, issues that started after a known event, situations where "it used to work fine."

### Problem Frame
- **ID**: `frame`
- **Sees hypothesis**: No
- **Purpose**: Challenges whether the problem statement itself is accurate. Asks "are we looking at the right problem?" In diagnosis: "could the reported symptoms be misleading, misattributed, or a symptom of a completely different underlying issue?" In plan review: "are we solving the right problem, or is the real need something different?"
- **Prompt guidance**: Frame as a skeptical analyst who questions the premise before generating solutions. Give it only the problem context. Ask it to propose 2-3 alternative framings of the problem, each with: the reframed problem, why the original framing might be wrong, and **what specific evidence would distinguish between the original and reframed problem**.
- **Select when**: The problem description seems vague, the symptoms don't obviously connect, or the user's framing makes assumptions that could be wrong. In plan review, select when the stated goal could be questioned.
- **Best for**: Vague or contradictory symptoms, problems that have resisted prior solutions, plans where the goal itself is debatable.

### Pre-mortem
- **ID**: `premortem`
- **Sees hypothesis**: Yes
- **Purpose**: Assumes the plan/hypothesis has already failed and works backward to explain why. Uses prospective hindsight to surface implementation risks — sequencing problems, resource gaps, timing issues, overlooked dependencies — that logical critique misses.
- **Prompt guidance**: Give it the problem context AND the hypothesis/plan. Tell it: "It's 6 months from now and this plan has failed. Tell the story of why it failed." Ask for 2-3 failure scenarios, each with: the failure mode, why it's plausible, and **what specific evidence or early warning signs would indicate this failure is happening**.
- **Select when**: Plan review mode, especially for multi-step plans, major decisions, or anything with significant implementation risk.
- **Best for**: Project plans, strategies, proposals, any plan where execution risk matters as much as the idea itself.

### Simplicity Advocate
- **ID**: `simplicity`
- **Sees hypothesis**: Yes
- **Purpose**: Argues for the simplest possible solution. Challenges complexity, questions whether each component is necessary, and proposes radically simpler alternatives. Asks "what if we just didn't do this?" and "what's the 20% effort that gets 80% of the value?"
- **Prompt guidance**: Give it the problem context AND the hypothesis/plan. Ask it to propose 1-2 dramatically simpler alternatives and identify which parts of the current plan may be unnecessary. Each alternative should include: the simplified approach, what it sacrifices, and **what evidence would show whether the sacrificed parts are actually needed**.
- **Select when**: Plan review mode, especially when the proposed plan feels heavy, has many steps, or adds significant complexity.
- **Best for**: Over-engineered proposals, complex multi-step plans, scope creep, early-stage planning.

### Red Team
- **ID**: `redteam`
- **Sees hypothesis**: Yes
- **Purpose**: Actively tries to break the proposed solution. Looks for vulnerabilities, abuse vectors, failure modes under adversarial conditions, and ways the plan could be exploited, gamed, or fail under stress.
- **Prompt guidance**: Give it the problem context AND the hypothesis/plan. Tell it to think like an adversary — someone actively trying to make this fail, exploit it, or find loopholes. Ask for 2-3 attack vectors or failure modes, each with: the vulnerability, how it could be exploited, and **what specific evidence would confirm the vulnerability exists**.
- **Select when**: The problem or plan involves security, trust, incentive design, public-facing systems, financial matters, or any situation where adversarial behavior is possible.
- **Best for**: Security-sensitive work, public-facing systems, anything involving trust, authorization, financial incentives, or adversarial actors.

## Agent Selection Guide

After extracting the mode and problem context in Step 1, select agents using these heuristics:

| Mode | Always | Strongly consider | If applicable |
|------|--------|-------------------|---------------|
| **Diagnosis** | Outside, Inside, Devil's Advocate* | Change Analyst | Problem Frame, Red Team |
| **Plan review** | Outside, Inside, Devil's Advocate* | Pre-mortem, Simplicity Advocate | Problem Frame, Red Team |

*Skip Devil's Advocate when no hypothesis exists.

**Selection criteria for on-demand agents:**
- Pick agents whose purpose directly addresses an aspect of the problem context
- Prefer 1-2 on-demand agents to keep total generation time reasonable (4-5 agents total)
- When in doubt, prefer the agent that covers the most likely blind spot for this specific problem
- State which agents you selected and why before spawning them
