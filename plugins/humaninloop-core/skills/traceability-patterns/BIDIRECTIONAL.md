# Bidirectional Linking

Patterns for creating and maintaining two-way links between artifacts.

---

## Core Principle

Every reference should be bidirectional:

```
If A references B:
  A.references = [..., B]
  B.referenced_by = [..., A]
```

This enables:
- **Forward tracing**: A → what does A affect?
- **Backward tracing**: B → where does B come from?
- **Impact analysis**: If B changes, what needs updating?

---

## Link Types

### Derivation Links

Shows origin/derivation:

```
Requirement FR-001
  └─ derived_from: [User Story US-001]

User Story US-001
  └─ derives: [FR-001, FR-002]
```

### Implementation Links

Shows implementation relationship:

```
Task T-001
  └─ implements: [FR-001]

Requirement FR-001
  └─ implemented_by: [T-001, T-002]
```

### Verification Links

Shows testing relationship:

```
Test TEST-001
  └─ verifies: [FR-001]

Requirement FR-001
  └─ verified_by: [TEST-001]
```

---

## ID Patterns

Use stable, meaningful identifiers:

| Artifact Type | Pattern | Example |
|--------------|---------|---------|
| User Story | US-{number} | US-001 |
| Requirement | FR-{number} | FR-001 |
| Check | CHK-{number} | CHK-015 |
| Gap | G-{number} | G-001 |
| Task | T-{number} | T-001 |
| Test | TEST-{number} | TEST-001 |

---

## Creating Links

### On Creation

When creating a new artifact that references another:

```
1. Add forward reference to new artifact
   new_item.references = [target_id]

2. Add backward reference to target
   target.referenced_by.append(new_item.id)

3. Validate target exists
   if not exists(target_id): raise BrokenLinkError
```

### On Update

When updating references:

```
1. Identify removed references
   removed = old_refs - new_refs

2. Identify added references
   added = new_refs - old_refs

3. Update backward links
   for ref in removed:
     ref.referenced_by.remove(item.id)
   for ref in added:
     ref.referenced_by.append(item.id)
```

### On Deletion

When deleting an artifact:

```
1. Remove from all referenced_by lists
   for ref in item.references:
     ref.referenced_by.remove(item.id)

2. Check for orphaned dependents
   for dependent in item.referenced_by:
     if len(dependent.references) == 1:
       warn("Will orphan: " + dependent.id)
```

---

## Traceability Matrix Format

Document links in a matrix:

```markdown
## Traceability Matrix

| Source | ID | Targets | Status |
|--------|-----|---------|--------|
| US-001 | FR-001 | T-001, T-002 | Covered |
| US-001 | FR-002 | T-003 | Covered |
| US-002 | FR-003 | - | Gap |
```

### Matrix Views

**Requirements → Tasks**:
```markdown
| Requirement | Tasks | Coverage |
|-------------|-------|----------|
| FR-001 | T-001, T-002 | 100% |
| FR-002 | T-003 | 100% |
| FR-003 | - | 0% |
```

**Tasks → Requirements**:
```markdown
| Task | Requirement | Source |
|------|-------------|--------|
| T-001 | FR-001 | US-001 |
| T-002 | FR-001 | US-001 |
```

---

## Link Validation

Regular validation checks:

### Check 1: Forward Links Exist

```
for each item:
  for each ref in item.references:
    assert exists(ref), "Broken link: " + ref
```

### Check 2: Backward Links Exist

```
for each item:
  for each ref in item.references:
    target = get(ref)
    assert item.id in target.referenced_by
```

### Check 3: No Circular References

```
for each item:
  visited = set()
  check_cycle(item, visited)

def check_cycle(item, visited):
  if item.id in visited:
    raise CircularReferenceError
  visited.add(item.id)
  for ref in item.references:
    check_cycle(get(ref), visited)
```

---

## Documentation Format

For artifacts with links:

```markdown
## FR-001: User Authentication

**Derived From**: US-001 (User Login)
**Implemented By**: T-001, T-002, T-003
**Verified By**: TEST-001, TEST-002

{content}
```

---

## Batch Link Operations

For bulk updates:

```markdown
## Link Update Summary

| Operation | Count |
|-----------|-------|
| Links added | 15 |
| Links removed | 3 |
| Backlinks updated | 18 |
| Broken links fixed | 2 |
| New orphans | 0 |
```
