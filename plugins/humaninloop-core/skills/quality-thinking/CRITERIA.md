# Defining Quality Criteria

Patterns for turning quality dimensions into measurable, testable criteria.

---

## What Makes a Good Criterion?

A quality criterion must be:

| Property | Description | Bad Example | Good Example |
|----------|-------------|-------------|--------------|
| **Specific** | Clear what to check | "Is it good?" | "All fields have type definitions" |
| **Testable** | Can objectively evaluate | "Feels complete" | "All user stories have acceptance criteria" |
| **Actionable** | Know what to fix if fail | "Needs improvement" | "Missing validation for email field" |
| **Scoped** | Clear boundary | "Is fast enough" | "Response time < 200ms for 95th percentile" |

---

## Criterion Definition Pattern

For each criterion:

```markdown
**ID**: {unique identifier}
**Dimension**: {which dimension this checks}
**Check**: {what to verify}
**Test**: {how to evaluate}
**Pass Threshold**: {what counts as passing}
**Fail Severity**: {Critical | Important | Minor}
```

---

## Check Types

### Presence Checks

Verify something exists:

```markdown
**Check**: All entities have ID field
**Test**: For each entity, verify "id" field is defined
**Pass**: 100% of entities have ID
```

### Consistency Checks

Verify agreement between parts:

```markdown
**Check**: Field types match across definitions
**Test**: Compare type declarations in model vs. API
**Pass**: 0 type mismatches
```

### Completeness Checks

Verify nothing is missing:

```markdown
**Check**: All requirements have success criteria
**Test**: For each FR-xxx, verify SC-xxx exists
**Pass**: 100% coverage
```

### Format Checks

Verify structure/format:

```markdown
**Check**: All IDs follow naming convention
**Test**: Regex match against ID pattern
**Pass**: 100% match
```

### Semantic Checks

Verify meaning/intent:

```markdown
**Check**: Requirements are unambiguous
**Test**: No vague quantifiers (some, many, fast)
**Pass**: 0 vague terms found
```

---

## Threshold Patterns

### Binary Thresholds

Pass or fail, no middle ground:

```
Pass: All items present
Fail: Any item missing
```

Best for: Critical requirements, security checks

### Percentage Thresholds

Allow partial compliance:

```
Pass: >= 90% complete
Partial: >= 70% complete
Fail: < 70% complete
```

Best for: Coverage metrics, optional enhancements

### Count Thresholds

Limit number of issues:

```
Pass: 0 Critical, <= 2 Important
Partial: 0 Critical, <= 5 Important
Fail: Any Critical OR > 5 Important
```

Best for: Aggregating multiple checks

---

## Severity Assignment

| Severity | Assign When | Example |
|----------|-------------|---------|
| **Critical** | Blocks downstream, safety issue | Missing auth check |
| **Important** | Should fix before proceeding | Incomplete error handling |
| **Minor** | Can defer, polish item | Inconsistent formatting |

---

## Criterion Sets by Domain

### Specification Criteria

| ID | Dimension | Check | Severity |
|----|-----------|-------|----------|
| SPEC-001 | Completeness | All user stories have acceptance criteria | Critical |
| SPEC-002 | Clarity | No vague quantifiers | Important |
| SPEC-003 | Consistency | No contradicting requirements | Critical |
| SPEC-004 | Traceability | All FRs link to user stories | Important |
| SPEC-005 | Measurability | Success criteria are quantifiable | Important |

### Code Criteria

| ID | Dimension | Check | Severity |
|----|-----------|-------|----------|
| CODE-001 | Correctness | All tests pass | Critical |
| CODE-002 | Security | No known vulnerabilities | Critical |
| CODE-003 | Maintainability | Cyclomatic complexity < 10 | Important |
| CODE-004 | Testability | Code coverage > 80% | Important |

### API Criteria

| ID | Dimension | Check | Severity |
|----|-----------|-------|----------|
| API-001 | Consistency | All endpoints follow naming convention | Important |
| API-002 | Completeness | All endpoints have error responses defined | Critical |
| API-003 | Discoverability | All endpoints have descriptions | Important |

---

## Criteria Documentation Format

```markdown
## Quality Criteria for {artifact_type}

### {Dimension_1}

| ID | Check | Test | Pass | Severity |
|----|-------|------|------|----------|
| {id} | {what} | {how} | {threshold} | {level} |

### {Dimension_2}

...
```

---

## Evolving Criteria

Criteria should evolve:

1. **Start minimal**: Core checks only
2. **Add as needed**: When issues recur
3. **Remove when obsolete**: When no longer relevant
4. **Document rationale**: Why each criterion exists
