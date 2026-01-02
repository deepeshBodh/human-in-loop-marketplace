---
name: prioritization-patterns
description: Patterns for classifying items by severity, detecting staleness, and grouping related work. Use when triaging issues, prioritizing backlogs, classifying gaps, or detecting stale items in any workflow.
---

# Prioritization Patterns

## Purpose

Systematic approaches for prioritizing work items, classifying issues by severity, detecting stale items, and grouping related work to reduce cognitive load. These patterns apply to any domain requiring triage or prioritization.

## Core Principles

1. **Severity-Based Classification**: Not all items are equal—classify by impact
2. **Staleness Detection**: Items unresolved after N iterations need escalation
3. **Smart Grouping**: Related items should be addressed together
4. **Bounded Queues**: Limit work-in-progress to maintain focus
5. **Clear Escalation**: Know when to escalate vs. retry

## Quick Reference

| Task | Reference |
|------|-----------|
| Classify by severity | [SEVERITY.md](SEVERITY.md) |
| Detect stale items | [STALENESS.md](STALENESS.md) |
| Group related items | [GROUPING.md](GROUPING.md) |

## Severity Classification Overview

Three-tier priority system:

| Priority | Impact | Action |
|----------|--------|--------|
| **Critical** | Blocks downstream work | Must fix immediately |
| **Important** | Should fix before proceeding | Fix or document deferral |
| **Minor** | Can be deferred | Track for later |

See [SEVERITY.md](SEVERITY.md) for detailed classification criteria.

## Staleness Detection Overview

Track items across iterations:

```
if item.iterations_unresolved >= threshold:
  mark_as_stale(item)
  escalate_to_human()
```

See [STALENESS.md](STALENESS.md) for detection algorithms and thresholds.

## Grouping Pattern Overview

Reduce cognitive load by grouping related items:

1. Group by **same source** (same requirement, same file)
2. Group by **same domain** (auth, API, data)
3. Group by **related concepts** (timeout + retry + error handling)

See [GROUPING.md](GROUPING.md) for grouping algorithms.

## Anti-Patterns to Avoid

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| **All Critical** | Nothing is prioritized | Use full severity range |
| **No Grouping** | Overwhelming item count | Group related items |
| **Infinite Retry** | Never escalates | Cap retries, then escalate |
| **No Staleness Tracking** | Items languish forever | Track iteration counts |
| **Unbounded Queues** | Paralysis by volume | Limit active items |

## When to Use These Patterns

- Triaging bugs, issues, or gaps
- Prioritizing backlogs or task lists
- Classifying validation failures
- Managing clarification queues
- Any workflow with item prioritization
