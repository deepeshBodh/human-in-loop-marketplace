# Staleness Detection

Patterns for detecting items that remain unresolved across iterations.

---

## Core Concept

An item is **stale** when it has been present in the queue for multiple iterations without resolution. Stale items indicate:

- The issue cannot be resolved automatically
- Human judgment is needed
- The process is stuck

---

## Staleness Thresholds

| Threshold | Meaning | Action |
|-----------|---------|--------|
| **1 iteration** | Normal | Continue processing |
| **2 iterations** | Warning | Flag for attention |
| **3+ iterations** | Stale | Escalate to human |

---

## Detection Algorithm

### Per-Item Tracking

```
for each item in queue:
  if item.id in previous_iteration_items:
    item.iterations_unresolved++
  else:
    item.iterations_unresolved = 1

  if item.iterations_unresolved >= STALE_THRESHOLD:
    mark_as_stale(item)
    escalate_to_human(item)
```

### Batch Staleness Detection

Compare entire queue across iterations:

```
current_hash = hash(items.map(i => i.id).sort().join(","))
if current_hash == previous_hash:
  batch_stale_count++
  if batch_stale_count >= 2:
    escalate_entire_batch()
```

---

## Staleness States

| State | Iteration Count | Action |
|-------|-----------------|--------|
| `fresh` | 1 | Normal processing |
| `aging` | 2 | Log warning, continue |
| `stale` | 3+ | Escalate, require human |

---

## Escalation Format

When escalating stale items:

```markdown
## Stale Item Escalation

**Item ID**: {id}
**First Seen**: Iteration {n}
**Current Iteration**: {current}
**Iterations Unresolved**: {count}

**Original Issue**:
{description}

**Resolution Attempts**:
- Iteration {n}: {what was tried}
- Iteration {n+1}: {what was tried}

**Why Escalating**:
{brief explanation of why auto-resolution failed}

**Decision Needed**:
{specific question for human}
```

---

## Preventing False Staleness

### Valid Reasons to Reset Counter

| Reason | Action |
|--------|--------|
| Item was partially addressed | Reset to 1 |
| Dependencies resolved | Reset to 1 |
| Scope clarified | Reset to 1 |

### Invalid Reasons (Don't Reset)

| Reason | Why Not |
|--------|---------|
| Just re-tagged | Cosmetic change |
| Moved to different queue | Still unresolved |
| Priority changed | Still unresolved |

---

## Staleness Metrics

Track these metrics across workflow:

| Metric | Description |
|--------|-------------|
| `stale_rate` | % of items that become stale |
| `avg_resolution_iterations` | Average iterations to resolve |
| `escalation_rate` | % of items escalated to human |

---

## Integration with Prioritization

Staleness interacts with severity:

| Severity | Stale Threshold | Rationale |
|----------|-----------------|-----------|
| Critical | 2 iterations | Can't block long |
| Important | 3 iterations | Standard threshold |
| Minor | 5 iterations | More tolerance |

---

## Stale Item Documentation

When marking item as stale:

```markdown
| ID | Priority | First Seen | Iterations | Status | Escalation |
|----|----------|------------|------------|--------|------------|
| G-001 | Critical | Iter 1 | 3 | stale | Awaiting user decision |
| G-005 | Important | Iter 2 | 3 | stale | Blocked by G-001 |
```
