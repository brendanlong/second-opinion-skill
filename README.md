# Second Opinion

A Claude Code skill that challenges the current working hypothesis by generating independent alternatives from multiple debiased perspectives, then ranking them with structured scoring.

## Why

When diagnosing a problem or evaluating a plan, it's easy to anchor on the first plausible explanation. Once you have a theory, confirmation bias kicks in — you look for evidence that supports it and overlook evidence that doesn't. This skill fights that by spawning independent agents that **don't know your current hypothesis**, so they generate genuinely fresh alternatives.

Works for any domain — software bugs, tax questions, medical symptoms, dying lawns, business strategy, or anything else where you want your assumptions challenged.

## How It Works

```
/second-opinion [explicit hypothesis override]
    │
    ▼
Extract PROBLEM_CONTEXT and HYPOTHESIS from conversation
    │  (hypothesis can come from the user or the agent)
    │  (auto-detects: diagnosis vs. plan review)
    │
    ▼
Select agents based on mode and context
    │  (2 core + Devil's Advocate if hypothesis exists + on-demand if warranted)
    │
    ▼
Generate alternatives WITH evidence (agents in parallel)
    ├── Outside View       → PROBLEM_CONTEXT only, no hypothesis, NO tools
    ├── Inside View        → PROBLEM_CONTEXT only, no hypothesis, WITH tools
    ├── Devil's Advocate   → PROBLEM_CONTEXT + HYPOTHESIS, WITH tools
    └── [On-demand agents] → selected based on context, tools as specified
    │
    ▼
Rank all hypotheses (1 agent, structured scoring rubric)
    │  (sees all generated alternatives + the original)
    │  (diversity requirement: at least one different problem frame)
    │
    ▼
Synthesize structured report
    (main agent can do follow-up investigation if needed)
```

### Agent Menu

The skill selects from a menu of agents based on the problem context. Not every agent runs every time.

**Core agents** (always included):

| Agent | Sees hypothesis? | Tool access? | Purpose |
|-------|:---:|:---:|---------|
| Outside View | No | No | Base rates, common patterns, statistical thinking |
| Inside View | No | Yes | Deep investigation of the specific system |
| Devil's Advocate | Yes | Yes | Argues against the current theory (skipped if no hypothesis) |

**On-demand agents** (added only when warranted):

| Agent | Sees hypothesis? | Tool access? | Best for |
|-------|:---:|:---:|---------|
| Change Analyst | No | Yes | Diagnosis — "what changed recently?" |
| Problem Frame | No | No | Both — "are we solving the right problem?" |
| Pre-mortem | Yes | Yes | Plan review — works backward from failure |
| Simplicity Advocate | Yes | Yes | Plan review — challenges unnecessary complexity |
| Red Team | Yes | Yes | Adversarial scenarios, security, trust, incentives |

### Anti-Anchoring Design

The key insight: agents marked "No" in the hypothesis column **never see your hypothesis**. They only get the problem context (symptoms, observations, constraints). This means their alternatives aren't biased by what you already think.

Agents that see the hypothesis do so because their role requires it — the Devil's Advocate needs to know what to argue against, the Pre-mortem needs to know what plan to project forward, etc.

Additionally, the tool access distinction creates genuine perspective diversity. Outside View reasons from general patterns without examining the specific system, while Inside View actively investigates files, code, and data. This prevents the two agents from converging on identical generic output.

### Structured Ranking

The ranker uses a scoring rubric (explanatory power, specificity, parsimony, falsifiability) with a multiplier for independent convergence (1.2x, kept low because agents share the same LLM substrate) and evidence quality (1.2x). Each hypothesis is scored before ranking to prevent anchoring on first impressions. A diversity requirement ensures at least one hypothesis from a different problem frame survives.

### Verification Criteria

Every agent must state what evidence would **prove** and **disprove** each hypothesis. Tool-using agents must also state what they searched for but didn't find. This feeds directly into actionable next steps.

### Dual Mode

The skill auto-detects whether you're in a **diagnosis** scenario or a **plan review** scenario:

- **Diagnosis**: Agents produce alternative root causes. Tool-using agents investigate the system for evidence.
- **Plan review**: Agents identify gaps, risks, and alternative approaches. Tool-using agents examine the implementation for feasibility.

## Installation

Copy or symlink the `second-opinion/` directory into your project's `.claude/skills/`:

```bash
# Option 1: Symlink (recommended — stays up to date)
ln -s /path/to/second-opinion-skill/second-opinion ~/.claude/skills/second-opinion

# Option 2: Copy
cp -r /path/to/second-opinion-skill/second-opinion ~/.claude/skills/second-opinion
```

## Usage

```
# Challenge the current hypothesis
/second-opinion

# Override the hypothesis explicitly
/second-opinion "maybe it's a memory leak in the connection pool"

# Plan review (auto-detected when conversation context is a plan)
/second-opinion
```

## Design Decisions

- **Tool access as perspective lever**: Outside View has no tools (general patterns), Inside View has tools (specific investigation) — this is what makes them genuinely different
- **Integrated investigation**: Agents gather evidence as they generate hypotheses, eliminating the need for a separate research phase
- **Lean defaults**: 2-3 core agents by default; on-demand agents added only when warranted
- **Domain agnostic**: Works for any problem — software, taxes, gardening, strategy, whatever needs challenging
- **No hypothesis leaking**: Unanchored agents never see the hypothesis, preventing anchoring
- **Structured scoring with diversity preservation**: Prevents the ranker from anchoring on first impressions; ensures novel reframings survive
- **Prove/disprove criteria**: Every hypothesis comes with specific verification and falsification tests
- **Hypothesis source agnostic**: Works whether "I think it's X" came from you or from Claude
- **No hypothesis is fine**: If there's no working theory, the skill adjusts and generates alternatives from scratch
- **All agents inherit your model**: No hardcoded model — uses whatever you're running

## License

MIT
