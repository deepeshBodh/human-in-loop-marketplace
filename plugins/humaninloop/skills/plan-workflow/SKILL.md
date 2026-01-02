---
name: plan-workflow
description: Reference manual for the plan workflow phases (Research, Domain Model, Contracts, Validation). Provides shared patterns for context loading, brownfield handling, and phase-specific procedures. Referenced by plan-builder and plan-validator agents.
---

# Plan Workflow Skill

## Purpose

Comprehensive guidance for executing the multi-phase plan workflow that transforms specifications into implementation artifacts. This skill provides shared patterns across all plan phases and phase-specific procedures.

## Plan Workflow Overview

```
Phase 0: Research      → research.md (technical decisions)
    ↓
Phase 1: Domain Model  → data-model.md (entities, relationships)
    ↓
Phase 2: Contracts     → contracts/, quickstart.md (API design)
    ↓
Phase 3: Validation    → Final cross-artifact validation
```

## Quick Reference

| Task | Reference |
|------|-----------|
| Load context files | **context-patterns** (core) |
| Handle brownfield projects | **brownfield-patterns** (core) |
| Phase 0: Research unknowns | [RESEARCH.md](RESEARCH.md) |
| Phase 1: Entity extraction | [DOMAIN.md](DOMAIN.md) |
| Phase 2: API design | [CONTRACTS.md](CONTRACTS.md) |
| Phase 3: Validation | **validation-expertise** (core) |

## Shared Artifacts

| Artifact | Purpose | Created By |
|----------|---------|------------|
| `plan-context.md` | Workflow state, registries, handoffs | All phases |
| `index.md` | Cross-workflow sync | All phases |
| `research.md` | Technical decisions | Phase 0 |
| `data-model.md` | Entity definitions | Phase 1 |
| `contracts/api.yaml` | OpenAPI spec | Phase 2 |
| `quickstart.md` | Integration guide | Phase 2 |

## Constitution Principles by Phase

| Phase | Constitution Tags |
|-------|------------------|
| 0: Research | `[phase:0]` - Technology choices |
| 1: Domain | `[phase:1]` - Data privacy, PII handling |
| 2: Contracts | `[phase:2]` - API standards |
| 3: Validation | Full sweep - All principles |

## Common Constraints

Both plan-builder and plan-validator share these constraints:
- **No user interaction** - Supervisor handles communication
- **Autonomous execution** - Complete phase without external input
- **Context sync required** - Update plan-context.md and index.md
- **Constitution compliance** - Check relevant principles
- **Traceability** - Link artifacts to FR-xxx requirements
