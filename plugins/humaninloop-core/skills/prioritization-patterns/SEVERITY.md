# Severity Classification

Detailed patterns for classifying items by severity and impact.

---

## Three-Tier Priority System

| Priority | Description | Examples |
|----------|-------------|----------|
| **Critical** | Blocks downstream work; foundational issue | Missing required entity, security violation, broken build |
| **Important** | Should be fixed before proceeding | Incomplete validation rules, missing edge cases, unclear requirements |
| **Minor** | Can be deferred or auto-resolved | Style issues, optimization opportunities, nice-to-haves |

---

## Classification Questions

Ask these questions in order to determine priority:

```
1. Does this BLOCK the next step?
   └─ YES → Critical
   └─ NO → Continue to step 2

2. Does this affect CORRECTNESS?
   └─ YES → Important
   └─ NO → Continue to step 3

3. Is this about POLISH or optimization?
   └─ YES → Minor
   └─ NO → Reassess (may be Important)
```

---

## Domain-Specific Classification

### Security Issues

| Finding | Priority | Rationale |
|---------|----------|-----------|
| Authentication bypass | Critical | Blocks safe operation |
| Missing input validation | Important | Correctness risk |
| Suboptimal encryption | Minor | Polish (if not compliance) |

### Data Issues

| Finding | Priority | Rationale |
|---------|----------|-----------|
| Missing required field | Critical | Blocks data model |
| No validation rules | Important | Correctness risk |
| Inconsistent naming | Minor | Style issue |

### API Issues

| Finding | Priority | Rationale |
|---------|----------|-----------|
| Endpoint not defined | Critical | Blocks implementation |
| Error response undefined | Important | Correctness risk |
| Non-standard formatting | Minor | Style issue |

---

## Priority Signals in Language

### Critical Signals

- "Cannot proceed without..."
- "Blocks all..."
- "Undefined and required"
- "Security vulnerability"
- "Data integrity risk"

### Important Signals

- "Should be defined before..."
- "Missing but workaround exists"
- "Incomplete specification"
- "Edge case not covered"

### Minor Signals

- "Nice to have"
- "Could be improved"
- "Not optimal but works"
- "Style suggestion"

---

## Priority Aggregation

When multiple items exist, aggregate by:

1. **Overall Status** = highest severity in set
2. **Critical Count** = number of blocking items
3. **Action Threshold** = if Critical > 0, must address before proceeding

Example:
```
Items: [Critical, Important, Minor, Minor]
→ Overall: Critical
→ Critical Count: 1
→ Action: Must resolve 1 Critical before proceeding
```

---

## Severity Override Rules

Some contexts override normal classification:

| Context | Override | Rationale |
|---------|----------|-----------|
| Security-related | Upgrade to Critical | Security never deferred |
| Compliance-related | Upgrade to Critical | Legal requirements |
| User-facing blocker | Upgrade to Critical | User experience |
| Internal tooling | May downgrade | Lower impact surface |

---

## Documentation Format

When recording prioritized items:

```markdown
| Priority | ID | Source | Description | Rationale |
|----------|-----|--------|-------------|-----------|
| Critical | G-001 | CHK-005 | Missing User entity | Blocks data model |
| Important | G-002 | CHK-010 | No password rules | Correctness risk |
| Minor | G-003 | CHK-015 | Inconsistent naming | Style only |
```
