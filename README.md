# Second Opinion

A Claude Code skill that challenges the current working hypothesis by generating independent alternatives from multiple debiased perspectives, ranking them, and optionally researching the top candidates.

## Why

When debugging or planning, it's easy to anchor on the first plausible explanation. Once you have a theory, confirmation bias kicks in — you look for evidence that supports it and overlook evidence that doesn't. This skill fights that by spawning independent agents that **don't know your current hypothesis**, so they generate genuinely fresh alternatives.

## How It Works

```
/second-opinion [--quick] [explicit hypothesis override]
    │
    ▼
Extract PROBLEM_CONTEXT and HYPOTHESIS from conversation
    │  (hypothesis can come from the user or the agent)
    │  (auto-detects: debugging/diagnosis vs. plan review)
    │
    ▼
Phase 1: Generate alternatives (3 agents in parallel)
    ├── Outside View    → PROBLEM_CONTEXT only, no hypothesis
    ├── Inside View     → PROBLEM_CONTEXT only, no hypothesis
    └── Devil's Advocate → PROBLEM_CONTEXT + HYPOTHESIS
    │
    ▼
Phase 2: Rank all hypotheses (1 agent)
    │  (sees all generated alternatives + the original)
    │
    ├── --quick → skip to Phase 4
    │
    ▼
Phase 3: Research top 3 (parallel agents with full tool access)
    │  (each investigates one hypothesis, looking for supporting
    │   AND contradicting evidence in the codebase)
    │
    ▼
Phase 4: Synthesize structured report
```

### Anti-Anchoring Design

The key insight: the Outside View and Inside View agents **never see your hypothesis**. They only get the problem context (symptoms, errors, constraints). This means their alternatives aren't biased by what you already think.

Only the Devil's Advocate sees the hypothesis — because its job is specifically to argue against it.

### Dual Mode

The skill auto-detects whether you're in a **debugging/diagnosis** scenario or a **plan review** scenario:

- **Debugging**: Generators produce alternative root causes. Researchers look for evidence in code/logs.
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

- **3 generator agents**: Outside view (statistical/pattern-based), inside view (domain/codebase-specific), devil's advocate (argues against current hypothesis)
- **No hypothesis leaking**: 2 of 3 generators never see the hypothesis, preventing anchoring
- **Research phase is optional**: `--quick` skips it for faster feedback (4 agents instead of 7)
- **Hypothesis source agnostic**: Works whether "I think it's X" came from you or from Claude
- **No hypothesis is fine**: If there's no working theory, the skill skips devil's advocate and adjusts prompts
- **All agents inherit your model**: No hardcoded model — uses whatever you're running

## License

MIT
