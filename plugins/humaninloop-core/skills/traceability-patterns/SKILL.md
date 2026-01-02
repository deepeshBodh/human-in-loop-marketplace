---
name: traceability-patterns
description: Patterns for maintaining bidirectional links between artifacts and detecting orphans. Use when establishing requirement-to-implementation tracing, impact analysis, or finding disconnected items.
---

# Traceability Patterns

## Purpose

Ensure every artifact has clear lineage (where it came from) and impact (what it affects). These patterns enable impact analysis, coverage tracking, and orphan detection across any artifact chain.

## Core Principles

1. **Bidirectional Links**: Every link goes both ways
2. **Orphan Detection**: Items without links are suspicious
3. **Coverage Analysis**: Track what percentage is linked
4. **Chain Validation**: No broken links in the chain
5. **ID-Based Linking**: Use stable identifiers, not positions

## Quick Reference

| Task | Reference |
|------|-----------|
| Create bidirectional links | [BIDIRECTIONAL.md](BIDIRECTIONAL.md) |
| Find orphaned items | [ORPHAN-DETECTION.md](ORPHAN-DETECTION.md) |

## Traceability Chain Concept

Artifacts form a chain from origin to implementation:

```
Origin → Derived → Implementation → Verification
```

Example chains:
- User Need → Requirement → Task → Code → Test
- Business Goal → Feature → Stories → Components
- Spec Item → Design Decision → Implementation

Each step should link to previous and next.

## Bidirectional Linking Pattern

For every link A → B, ensure B → A exists:

```
Item A
  └─ references: [B, C]

Item B
  └─ referenced_by: [A]

Item C
  └─ referenced_by: [A]
```

See [BIDIRECTIONAL.md](BIDIRECTIONAL.md) for implementation patterns.

## Orphan Detection Pattern

An orphan is an item with missing links:

| Orphan Type | Missing | Risk |
|-------------|---------|------|
| **No upstream** | No source reference | Why does this exist? |
| **No downstream** | No implementations | Is this done? |
| **Both** | Completely isolated | Dead artifact |

See [ORPHAN-DETECTION.md](ORPHAN-DETECTION.md) for detection algorithms.

## Coverage Analysis Pattern

Track traceability completeness:

```
Coverage = (linked items / total items) × 100

By direction:
- Upstream coverage: Items with source reference
- Downstream coverage: Items with implementations
```

## Chain Validation Pattern

Verify no broken links:

```
for each reference in all_items:
  target = resolve(reference)
  if target is None:
    report_broken_link(reference)
  if reference not in target.referenced_by:
    report_missing_backlink(reference)
```

## Anti-Patterns to Avoid

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| **One-way links** | Can't trace back | Always create backlinks |
| **Implicit links** | "See section 3" | Use explicit IDs |
| **Broken links** | Reference non-existent item | Validate on create/update |
| **No link validation** | Orphans accumulate | Regular orphan scans |
| **Position-based refs** | Breaks when order changes | Use stable IDs |

## When to Use These Patterns

- Building requirements traceability matrices
- Tracking implementation coverage
- Impact analysis for changes
- Audit trails and compliance
- Finding dead/unused artifacts
