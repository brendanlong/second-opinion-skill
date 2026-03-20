# Second Opinion

A Claude Code skill that challenges the current working hypothesis by generating independent alternatives from multiple debiased perspectives, ranking them with structured scoring, and optionally researching the top candidates.

## Why

When diagnosing a problem or evaluating a plan, it's easy to anchor on the first plausible explanation. Once you have a theory, confirmation bias kicks in — you look for evidence that supports it and overlook evidence that doesn't. This skill fights that by spawning independent agents that **don't know your current hypothesis**, so they generate genuinely fresh alternatives.

Works for any domain — software bugs, tax questions, medical symptoms, dying lawns, business strategy, or anything else where you want your assumptions challenged.

## How It Works

```
/second-opinion [--quick] [explicit hypothesis override]
    │
    ▼
Extract PROBLEM_CONTEXT and HYPOTHESIS from conversation
    │  (hypothesis can come from the user or the agent)
    │  (auto-detects: diagnosis vs. plan review)
    │
    ▼
Select agents from menu based on mode and context
    │  (3 core agents + 1-2 on-demand agents)
    │
    ▼
Phase 1: Generate alternatives (4-5 agents in parallel)
    ├── Outside View       → PROBLEM_CONTEXT only, no hypothesis
    ├── Inside View        → PROBLEM_CONTEXT only, no hypothesis
    ├── Devil's Advocate   → PROBLEM_CONTEXT + HYPOTHESIS
    └── [On-demand agents] → selected based on context
    │
    ▼
Phase 2: Rank all hypotheses (1 agent, structured scoring rubric)
    │  (sees all generated alternatives + the original)
    │
    ├── --quick → skip to Phase 4
    │
    ▼
Phase 3: Research top 3 (parallel agents with full tool access)
    │  (each investigates one hypothesis, looking for supporting
    │   AND contradicting evidence using all available sources)
    │
    ▼
Phase 4: Synthesize structured report
```

### Agent Menu

The skill selects from a menu of brainstorming agents based on the problem context. Not every agent runs every time.

**Core agents** (always included):

| Agent | Sees hypothesis? | Purpose |
|-------|:---:|---------|
| Outside View | No | Base rates, common patterns, statistical thinking |
| Inside View | No | Deep domain expertise, specific to the problem at hand |
| Devil's Advocate | Yes | Argues against the current theory (skipped if no hypothesis) |

**On-demand agents** (selected based on context):

| Agent | Sees hypothesis? | Best for |
|-------|:---:|---------|
| Change Analyst | No | Diagnosis — "what changed recently?" |
| Problem Frame | No | Both — "are we solving the right problem?" |
| Pre-mortem | Yes | Plan review — works backward from failure |
| Simplicity Advocate | Yes | Plan review — challenges unnecessary complexity |
| Red Team | Yes | Adversarial scenarios, security, trust, incentives |

### Anti-Anchoring Design

The key insight: agents marked "No" in the hypothesis column **never see your hypothesis**. They only get the problem context (symptoms, observations, constraints). This means their alternatives aren't biased by what you already think.

Agents that see the hypothesis do so because their role requires it — the Devil's Advocate needs to know what to argue against, the Pre-mortem needs to know what plan to project forward, etc.

### Structured Ranking

The ranker uses a scoring rubric (explanatory power, specificity, parsimony, falsifiability) with multipliers for independent convergence and evidence quality. Each hypothesis is scored before ranking to prevent anchoring on first impressions.

### Verification Criteria

Every brainstorming agent must state what evidence would **prove** and **disprove** each hypothesis. Research agents must state what they searched for but didn't find. This feeds directly into actionable next steps.

### Dual Mode

The skill auto-detects whether you're in a **diagnosis** scenario or a **plan review** scenario:

- **Diagnosis**: Generators produce alternative root causes. Researchers investigate using available sources.
- **Plan review**: Generators identify gaps, risks, and alternative approaches. Researchers validate feasibility.

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
# Challenge the current hypothesis (full analysis with research)
/second-opinion

# Quick mode — brainstorm + rank only, skip research (faster, fewer agents)
/second-opinion --quick

# Override the hypothesis explicitly
/second-opinion "maybe it's a memory leak in the connection pool"

# Plan review (auto-detected when conversation context is a plan)
/second-opinion
```

## Design Decisions

- **Adaptive agent selection**: 3 core + up to 5 on-demand agents, selected based on mode and context
- **Domain agnostic**: Works for any problem — software, taxes, gardening, strategy, whatever needs challenging
- **No hypothesis leaking**: Unanchored agents never see the hypothesis, preventing anchoring
- **Structured scoring rubric**: Prevents the ranker from anchoring on first impressions
- **Prove/disprove criteria**: Every hypothesis comes with specific verification and falsification tests
- **Research phase is optional**: `--quick` skips it for faster feedback
- **Hypothesis source agnostic**: Works whether "I think it's X" came from you or from Claude
- **No hypothesis is fine**: If there's no working theory, the skill adjusts and generates alternatives from scratch
- **All agents inherit your model**: No hardcoded model — uses whatever you're running

## License

MIT
