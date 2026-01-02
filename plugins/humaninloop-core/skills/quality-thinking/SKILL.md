---
name: quality-thinking
description: Meta-pattern for thinking about quality across any artifact. Use when defining quality criteria, evaluating deliverables, or establishing quality gates for any domain.
---

# Quality Thinking

## Purpose

Framework for systematically considering quality dimensions when producing or evaluating any artifact. This is a meta-pattern—it teaches HOW to think about quality, not WHAT specific dimensions to use (those are domain-specific).

## Core Principles

1. **Explicit Dimensions**: Name the aspects of quality that matter
2. **Measurable Criteria**: Define how to evaluate each dimension
3. **Tiered Expectations**: Distinguish minimum from excellent
4. **Domain Adaptation**: Apply the framework to any domain
5. **Gap Identification**: Systematically find quality issues

## Quick Reference

| Task | Reference |
|------|-----------|
| Choose quality dimensions | [DIMENSIONS.md](DIMENSIONS.md) |
| Define measurable criteria | [CRITERIA.md](CRITERIA.md) |

## The Quality Framework

### Step 1: Identify Dimensions

For any artifact, ask: "What aspects of quality matter?"

Common dimension categories:
- **Content**: Is it complete? Correct? Consistent?
- **Form**: Is it clear? Well-structured? Readable?
- **Process**: Is it traceable? Maintainable? Testable?

### Step 2: Define Criteria

For each dimension, define:
- **What to check** (the criterion)
- **How to evaluate** (the test)
- **What counts as passing** (the threshold)

### Step 3: Apply and Evaluate

For each criterion:
1. Execute the check
2. Record pass/fail
3. If fail, classify severity (Critical/Important/Minor)
4. Aggregate results

## Dimension Selection Pattern

Choose dimensions based on:

| Factor | Consider |
|--------|----------|
| **Artifact type** | Code vs. docs vs. design |
| **Audience** | End users vs. developers vs. reviewers |
| **Stage** | Draft vs. review vs. final |
| **Risk** | High stakes vs. routine |

## Quality Gate Pattern

Use quality thinking to define gates:

```
GATE: {name}
DIMENSIONS: [{dimension_1}, {dimension_2}, ...]
PASS CRITERIA: All Critical pass, X% Important pass
FAIL ACTION: {block | warn | log}
```

## Anti-Patterns to Avoid

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| **Undefined quality** | No criteria, just "looks good" | Define explicit dimensions |
| **Unmeasurable criteria** | "Must be good" | Define testable checks |
| **One-size-fits-all** | Same criteria for all artifacts | Adapt to domain |
| **All-or-nothing** | Pass or fail only | Use severity levels |
| **Quality theater** | Checks that don't matter | Focus on impactful dimensions |

## When to Use This Pattern

- Designing quality gates for any workflow
- Creating checklists for artifact review
- Defining acceptance criteria
- Building validation frameworks
- Establishing coding/writing standards
