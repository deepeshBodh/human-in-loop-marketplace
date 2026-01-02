# Orphan Detection

Patterns for finding items with missing or incomplete traceability.

---

## What is an Orphan?

An orphan is an artifact missing expected links:

| Orphan Type | Definition | Symptom |
|-------------|------------|---------|
| **Upstream orphan** | No source reference | "Why does this exist?" |
| **Downstream orphan** | No implementations | "Is this done?" |
| **Complete orphan** | No links at all | "Dead artifact" |

---

## Orphan Detection Algorithm

### Step 1: Identify Expected Links

For each artifact type, define expected links:

```
Requirements:
  - MUST have: derived_from (upstream)
  - SHOULD have: implemented_by (downstream)

Tasks:
  - MUST have: implements (upstream)
  - MAY have: depends_on (lateral)
```

### Step 2: Scan for Missing Links

```
orphans = {
  "upstream": [],
  "downstream": [],
  "complete": []
}

for each item in artifacts:
  has_upstream = len(item.references) > 0
  has_downstream = len(item.referenced_by) > 0

  if not has_upstream and not has_downstream:
    orphans.complete.append(item)
  elif not has_upstream:
    orphans.upstream.append(item)
  elif not has_downstream:
    orphans.downstream.append(item)
```

### Step 3: Classify Severity

| Orphan Type | Expected Links | Severity |
|-------------|----------------|----------|
| Complete orphan | Both required | Critical |
| Upstream orphan | Upstream required | Critical |
| Upstream orphan | Upstream optional | Minor |
| Downstream orphan | Downstream required | Important |
| Downstream orphan | Downstream optional | Minor |

---

## Detection by Artifact Type

### Requirements without Implementation

```
for each requirement in requirements:
  if requirement.implemented_by is empty:
    report_orphan(
      type: "downstream",
      item: requirement,
      message: "Requirement has no implementation tasks"
    )
```

### Tasks without Requirements

```
for each task in tasks:
  if task.implements is empty:
    report_orphan(
      type: "upstream",
      item: task,
      message: "Task has no source requirement"
    )
```

### Checks without Requirements

```
for each check in checks:
  if check.validates is empty:
    report_orphan(
      type: "upstream",
      item: check,
      message: "Check has no requirement reference"
    )
```

---

## Orphan Report Format

```markdown
## Orphan Detection Report

**Scan Date**: {timestamp}
**Artifacts Scanned**: {count}
**Orphans Found**: {count}

### Critical Orphans

| ID | Type | Missing | Impact |
|----|------|---------|--------|
| T-015 | Task | implements | Unknown purpose |
| FR-022 | Requirement | derived_from | No user story |

### Important Orphans

| ID | Type | Missing | Impact |
|----|------|---------|--------|
| FR-003 | Requirement | implemented_by | May not be done |
| US-010 | Story | derives | No requirements |

### Minor Orphans

| ID | Type | Missing | Notes |
|----|------|---------|-------|
| T-050 | Task | depends_on | Optional link |
```

---

## Orphan Resolution

### For Upstream Orphans

Options:
1. **Add missing link**: Find and connect to source
2. **Create source**: If source doesn't exist, create it
3. **Delete orphan**: If artifact is truly unneeded
4. **Document exception**: If orphan is intentional

### For Downstream Orphans

Options:
1. **Add implementation**: Create tasks/tests to cover
2. **Mark as deferred**: Explicitly defer to later
3. **Mark as out-of-scope**: Document exclusion
4. **Delete orphan**: If no longer needed

---

## Orphan Prevention

### On Creation

```
validate_upstream_exists(new_item.references)
if new_item.references is empty:
  warn("Creating item without upstream reference")
  require_justification()
```

### On Deletion

```
if target.referenced_by is not empty:
  warn("Deleting will create orphans:")
  list(target.referenced_by)
  require_confirmation()
```

---

## Coverage Metrics

Track orphan rates over time:

```markdown
## Traceability Coverage

| Metric | Value | Target |
|--------|-------|--------|
| Requirements with tasks | 85% | 100% |
| Tasks with requirements | 100% | 100% |
| Requirements with tests | 60% | 80% |
| Orphan rate | 5% | < 2% |
```

---

## Integration with Validation

Include orphan detection in validation:

```markdown
| Check ID | Check | Tier |
|----------|-------|------|
| TRC-001 | No orphan requirements | auto-retry |
| TRC-002 | No orphan tasks | auto-retry |
| TRC-003 | Coverage > 80% | escalate |
```

---

## Scheduled Scans

Run orphan detection regularly:

| Trigger | When |
|---------|------|
| On commit | Scan changed artifacts |
| On phase complete | Scan all phase artifacts |
| Weekly | Full artifact scan |
