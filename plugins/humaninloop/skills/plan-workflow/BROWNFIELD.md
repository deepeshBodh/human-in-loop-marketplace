# Brownfield Handling

Guidelines for plan phases when working with existing codebases.

---

## Detection

Brownfield mode applies when:
```
codebase_context.has_discovery == true AND codebase_context.is_greenfield == false
```

If greenfield (no existing codebase), skip all brownfield considerations.

---

## Phase 0: Research - Tech Stack Alignment

### Existing Stack as Baseline

When existing tech stack detected:
- Note existing technologies as baseline
- Identify existing dependencies that might solve problems
- Prefer extending existing patterns over introducing new ones

### Decision Guidelines

| Scenario | Guidance |
|----------|----------|
| Existing tech solves problem | **Prefer reuse** - Document why existing works |
| Existing tech partially solves | **Prefer extend** - Add to existing rather than replace |
| Existing tech incompatible | **Document justification** - Explain why new tech needed |
| Multiple existing options | **Pick most aligned** - Choose based on detected patterns |

### Tech Stack Alignment Checklist

- [ ] Is a similar library already in dependencies?
- [ ] Does the architecture pattern support this choice?
- [ ] Will this introduce conflicting patterns?
- [ ] Is there a migration path from existing to new?

---

## Phase 1: Domain Model - Entity Collision Handling

### Collision Detection

For each proposed entity, check:
```
IF entity_name IN codebase_context.existing_entities:
  → Apply collision resolution
```

### Resolution Strategies

| Recommended Action | Behavior |
|-------------------|----------|
| `auto_extend` | Add new fields to existing entity |
| `auto_reuse` | Use existing entity as-is |
| `rename` | Create new entity with different name |
| `skip` | Entity exists, no changes needed |
| `escalate` | User decision required |

### Entity Status Markers

Mark each entity in data-model.md:
- `[NEW]` - Entirely new entity
- `[EXTENDS EXISTING]` - Adding fields to existing entity
- `[REUSES EXISTING]` - Using existing entity as-is
- `[RENAMED]` - New entity avoiding collision

### Vocabulary Mapping

Use detected vocabulary mappings:
- Spec says "Customer" but codebase has "User" → use "User"
- Document mapping in data-model.md

---

## Phase 2: Contracts - Endpoint Collision Handling

### Existing Endpoints

When existing endpoints detected:
- Match API patterns (base path, versioning, auth, error format)
- Identify endpoint collisions

### Resolution Strategies

| Scenario | Action |
|----------|--------|
| Endpoint exists, covers requirement | `reuse` - Reference existing |
| Endpoint exists, needs extension | `extend` - Add parameters/responses |
| Endpoint exists, conflicts | `rename` - Use different path |
| No collision | `new` - Create as designed |

### API Pattern Matching

Follow detected patterns:
```json
{
  "base_path": "/api",
  "versioning": "none|path|header",
  "auth_mechanism": "jwt|session|api_key",
  "error_format": "{code, message, details}"
}
```

---

## Documentation Requirements

For all brownfield decisions, document:

1. **In artifact** (research.md, data-model.md, contracts/):
   - Existing tech/entity/endpoint referenced
   - Why reused, extended, or new

2. **In plan-context.md**:
   - Collision resolutions applied
   - Deviations from existing patterns with justification
