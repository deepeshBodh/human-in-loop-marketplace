---
name: decision-patterns
description: Patterns for making and documenting decisions systematically. Use when evaluating alternatives, documenting trade-offs, or capturing rationale for future reference.
---

# Decision Patterns

## Purpose

Systematic approaches for making decisions with clear rationale and documented trade-offs. These patterns ensure decisions are explicit, justified, and traceable—enabling future review and learning.

## Core Principles

1. **Explicit Alternatives**: Never decide without considering options
2. **Documented Trade-offs**: Know what you're gaining and losing
3. **Captured Rationale**: Record WHY, not just WHAT
4. **Reversibility Awareness**: Know the cost of changing your mind
5. **Stakeholder Context**: Understand who is affected

## Quick Reference

| Task | Reference |
|------|-----------|
| Evaluate options | [OPTIONS-MATRIX.md](OPTIONS-MATRIX.md) |
| Document trade-offs | [TRADE-OFFS.md](TRADE-OFFS.md) |
| Capture rationale | [RATIONALE.md](RATIONALE.md) |

## Decision Process Overview

### Step 1: Frame the Decision

- **Question**: What needs to be decided?
- **Context**: What constraints exist?
- **Impact**: What depends on this decision?

### Step 2: Identify Options

- List at least 2-3 alternatives
- Include "do nothing" if applicable
- Consider existing solutions (brownfield)

### Step 3: Evaluate Options

- Apply consistent criteria
- Document pros and cons
- Identify trade-offs

See [OPTIONS-MATRIX.md](OPTIONS-MATRIX.md) for detailed evaluation patterns.

### Step 4: Make and Document Decision

- Choose the best option
- Document rationale (WHY this choice)
- Note trade-offs accepted
- Record constraints considered

See [RATIONALE.md](RATIONALE.md) for documentation patterns.

## Decision Types

| Type | Reversibility | Approach |
|------|---------------|----------|
| **One-way door** | Hard to reverse | More analysis, stakeholder input |
| **Two-way door** | Easy to reverse | Faster decision, can adjust |

## When to Escalate

Escalate the decision if:

- **High impact**: Affects many components or users
- **Policy involved**: Requires judgment beyond technical
- **Precedent-setting**: Will be referenced for future decisions
- **Contested**: Strong disagreement between options
- **Irreversible**: Costly to change later

## Decision Record Pattern

Use Architecture Decision Records (ADRs) for significant decisions:

```markdown
# ADR-{number}: {title}

**Status**: Proposed | Accepted | Deprecated | Superseded
**Date**: {date}

## Context
{what is the situation that requires a decision}

## Decision
{what was decided}

## Consequences
{what follows from this decision}
```

## Anti-Patterns to Avoid

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| **No alternatives** | First idea wins | Always list 2-3 options |
| **Hidden rationale** | Can't review decisions | Document the WHY |
| **Implicit trade-offs** | Surprises later | Make trade-offs explicit |
| **Analysis paralysis** | Never decide | Set decision deadline |
| **Undocumented** | Reinvent decisions | Keep decision records |

## When to Use These Patterns

- Technology selection (databases, frameworks, tools)
- Architecture choices (patterns, structures)
- Process decisions (workflows, standards)
- Any choice requiring justification
- Brownfield decisions (reuse vs. replace)
