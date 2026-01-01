# Context Loading & Updating

Shared patterns for reading and updating context files across all plan phases.

---

## Context Loading (All Phases)

### Required Files

| File | Purpose | Read For |
|------|---------|----------|
| `spec.md` | Source requirements | FR-xxx, user stories, constraints |
| `constitution.md` | Principles | Phase-tagged guidelines |
| `plan-context.md` | Workflow state | Previous decisions, registries |
| `index.md` | Cross-workflow | Document availability, sync state |

### Phase-Specific Files

| Phase | Additional Files |
|-------|-----------------|
| 0: Research | (none - first phase) |
| 1: Domain | `research.md` (decisions) |
| 2: Contracts | `research.md`, `data-model.md` |
| 3: Validation | All artifacts |

### Retry Handling

On retry iterations (iteration > 1):
1. Read existing phase artifact
2. Focus on `gaps_to_resolve` from input
3. Preserve correct content, fix gaps only

---

## Updating plan-context.md

### Workflow Metadata Section

```markdown
| **Status** | {phase_status} |
| **Current Agent** | {agent_name} |
| **Last Updated** | {timestamp} |
```

Status values: `researching`, `modeling`, `designing`, `validating`, `complete`

### Agent Handoff Notes

```markdown
### From {Agent Name} (Phase {N})

- **Items processed**: [count]
- **Decisions made**: [list key decisions]
- **Unresolved count**: [0 if passing]
- **Constitution principles checked**: [list]
- **Ready for**: [next agent or Validator]
```

### Registries

Each phase populates its registry:

| Phase | Registry | Contents |
|-------|----------|----------|
| 0 | Technical Decisions | Unknown → Decision mapping |
| 1 | Entity Registry | Entity definitions, fields, relationships |
| 2 | Endpoint Registry | Endpoints, methods, schemas |

---

## Syncing to index.md

### Document Availability Matrix

Update status for artifacts created:
```markdown
| research.md | present | {timestamp} |
| data-model.md | present | {timestamp} |
| contracts/ | present | {timestamp} |
```

### Plan Phase State

```markdown
| **Current Phase** | {phase_number}: {phase_name} |
| **Phase Status** | {in_progress|complete} |
| **Iteration** | {current} / 10 |
| **Last Agent** | {agent_name} |
```

### Unified Decisions Log

Add major decisions:
```markdown
| {timestamp} | plan | {agent} | {decision_summary} | {rationale} |
```

### Gap Queue (Validation)

If gaps found during validation:
```markdown
| Priority | Gap ID | Phase | Artifact | Issue | Status |
|----------|--------|-------|----------|-------|--------|
| Critical | G-P001 | 1 | data-model.md | Missing entity | pending |
```

---

## Context Sync Checklist

Before returning, verify:
- [ ] plan-context.md status updated
- [ ] plan-context.md handoff notes written
- [ ] plan-context.md registry populated (if applicable)
- [ ] index.md document availability updated
- [ ] index.md plan phase state updated
- [ ] index.md decisions log updated
- [ ] index.md last_sync timestamp updated
