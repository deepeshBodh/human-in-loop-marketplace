---
name: reviewing-plan-artifacts
description: Review planning artifacts (research, data model, contracts) for quality and completeness. Use when reviewing plan phase outputs, finding gaps in design decisions, or when you see "review research", "review data model", "review contracts", "plan quality", or "phase review".
---

# Reviewing Plan Artifacts

## Purpose

Find gaps in planning artifacts and generate issues that need resolution before proceeding to the next phase. Focus on design completeness and quality, not implementation details. This skill provides phase-specific review criteria for the Devil's Advocate.

## Review Focus by Phase

### Phase A0: Codebase Discovery Review

| Check | Question | Severity |
|-------|----------|----------|
| Coverage | Were all source directories scanned? | Important |
| Entity detection | Were all entity patterns tried? | Critical |
| Endpoint detection | Were all route patterns tried? | Critical |
| Collision assessment | Are risk levels appropriate? | Critical |
| Evidence | Are file paths cited for all findings? | Important |

**Key Questions:**
- Did we miss any obvious source directories?
- Are there entities or endpoints that should have been found?
- Are collision risk levels realistic?

### Phase B0: Research Review

| Check | Question | Severity |
|-------|----------|----------|
| Marker resolution | Are all `[NEEDS CLARIFICATION]` resolved? | Critical |
| Alternative analysis | Were 2+ alternatives considered? | Critical |
| Rationale quality | Is the "why" documented? | Critical |
| Constitution alignment | Do choices follow principles? | Important |
| Brownfield consideration | Was existing stack evaluated? | Important |
| Trade-off documentation | Are downsides acknowledged? | Important |

**Key Questions:**
- What unknowns were NOT addressed?
- Are any decisions made without considering alternatives?
- Is the rationale convincing, or just restating the choice?
- Were brownfield constraints properly considered?

### Phase B1: Data Model Review

| Check | Question | Severity |
|-------|----------|----------|
| Entity coverage | Is every noun from requirements modeled? | Critical |
| Attribute completeness | Do all entities have required fields? | Critical |
| Relationship definition | Are all connections documented? | Critical |
| Validation rules | Are constraints from requirements captured? | Important |
| State machines | Are stateful entities properly modeled? | Important |
| PII identification | Are sensitive fields marked? | Critical |
| Traceability | Can we trace entities to requirements? | Important |

**Key Questions:**
- What entities from the spec are missing?
- Are there relationships that should exist but don't?
- Are validation rules comprehensive?
- Did we miss any PII fields?

### Phase B2: Contract Review

| Check | Question | Severity |
|-------|----------|----------|
| Endpoint coverage | Does every user action have an endpoint? | Critical |
| Schema completeness | Are request/response schemas defined? | Critical |
| Error handling | Are failure modes documented? | Critical |
| Schema-model consistency | Do schemas match the data model? | Critical |
| Authentication | Are auth requirements clear? | Important |
| Examples | Are realistic scenarios documented? | Important |
| Naming consistency | Do endpoints follow conventions? | Minor |

**Key Questions:**
- What user actions don't have endpoints?
- Are there error scenarios not handled?
- Do the schemas actually match our data model?
- Is the quickstart documentation usable?

### Phase B3: Final Cross-Artifact Review

| Check | Question | Severity |
|-------|----------|----------|
| Spec-Research alignment | Do research decisions serve spec goals? | Critical |
| Research-Model consistency | Are model choices consistent with decisions? | Critical |
| Model-Contract consistency | Do schemas reflect the data model? | Critical |
| Requirement traceability | Can we trace from FR to endpoint? | Important |
| Constitution compliance | Do all artifacts follow principles? | Important |

## Issue Classification

### Severity Levels

| Severity | Definition | Action |
|----------|------------|--------|
| **Critical** | Blocks progress; must resolve | Return to responsible agent |
| **Important** | Significant gap; should resolve | Flag for this iteration |
| **Minor** | Polish item; can defer | Note for later |

### Classification Rules

```
CRITICAL if:
- Missing entity/endpoint from requirements
- Unresolved NEEDS CLARIFICATION marker
- No alternatives considered for major decision
- Security/privacy issue (unmarked PII, etc.)
- Broken traceability (can't trace FR to implementation)

IMPORTANT if:
- Missing validation rules
- Incomplete error handling
- Weak rationale documentation
- State machine gaps
- Brownfield misalignment

MINOR if:
- Naming inconsistency
- Missing examples
- Documentation formatting
- Non-critical missing details
```

## Issue Documentation Format

### Issue Template

```markdown
### Issue: [Short description]

**Severity**: Critical | Important | Minor
**Phase**: A0 | B0 | B1 | B2 | B3
**Artifact**: [Which file has the issue]
**Check Failed**: [Which review check]

**Problem**:
[What is wrong or missing]

**Evidence**:
[Where this gap appears - cite requirements or artifacts]

**Impact**:
[What happens if not resolved]

**Suggested Fix**:
[How the responsible agent should address this]
```

### Issue Table Format

```markdown
## Issues Found

| # | Severity | Phase | Description | Artifact | Suggested Action |
|---|----------|-------|-------------|----------|------------------|
| 1 | Critical | B1 | Session entity missing expiresAt | data-model.md | Add expiresAt timestamp |
| 2 | Important | B2 | No 429 error for login | contracts/ | Add rate limit response |
| 3 | Minor | B2 | Inconsistent path naming | contracts/ | Standardize to kebab-case |
```

## Advocate Report Format

```markdown
## Plan Artifact Review: {Phase Name}

**Artifact Reviewed**: {file path}
**Reviewer**: Devil's Advocate
**Date**: {timestamp}

---

### Verdict: [ready | needs-revision | critical-gaps]

---

### Critical Issues

[Issues that MUST be resolved before proceeding]

| # | Issue | Evidence | Suggested Fix |
|---|-------|----------|---------------|
| 1 | [Description] | [Source] | [Action] |

---

### Important Issues

[Issues that SHOULD be addressed in this iteration]

| # | Issue | Evidence | Suggested Fix |
|---|-------|----------|---------------|

---

### Minor Issues

[Polish items that can be deferred]

| # | Issue | Suggested Fix |
|---|-------|---------------|

---

### Strengths Noted

[What was done well - acknowledge good work]

- [Strength 1]
- [Strength 2]

---

### Recommended Actions

1. [Specific action for responsible archetype]
2. [Specific action for responsible archetype]

---

### Cross-Artifact Concerns

[Issues that span multiple artifacts - for final validation]

- [Concern 1]
- [Concern 2]
```

## Verdict Criteria

### ready

All of the following:
- Zero Critical issues
- Zero Important issues (or all auto-resolved)
- Minor issues documented for future

### needs-revision

Any of the following:
- 1-3 Important issues to resolve
- Minor issues affecting usability
- Gaps that can be fixed in one iteration

### critical-gaps

Any of the following:
- 1+ Critical issues
- 4+ Important issues
- Missing major artifact section
- Unrecoverable in single iteration

## Review Process

### Step 1: Gather Context

Read and understand:
- The artifact being reviewed
- The spec requirements it should satisfy
- Previous artifacts (for consistency checks)
- Constitution principles (for compliance)

### Step 2: Execute Checks

For each check in the phase-specific checklist:
1. Ask the question
2. Look for evidence in the artifact
3. If issue found, classify severity
4. Document the issue

### Step 3: Cross-Reference

- Check traceability (can trace requirement → artifact)
- Check consistency (artifacts agree with each other)
- Check completeness (nothing obviously missing)

### Step 4: Generate Report

- Classify verdict based on issues found
- Document all issues with evidence
- Provide specific, actionable suggestions
- Acknowledge what was done well

## Validation Script

A Python script automates common artifact checks before manual review.

### Location

```
scripts/check-artifacts.py
```

### How to Invoke

```bash
# Single file
python scripts/check-artifacts.py .spec/plan/data-model.md

# Multiple files (enables entity consistency check)
python scripts/check-artifacts.py .spec/plan/research.md .spec/plan/data-model.md

# All plan artifacts
python scripts/check-artifacts.py .spec/plan/*.md
```

### What It Checks

| Check | Description | Applies To |
|-------|-------------|------------|
| `unresolved_markers` | Finds `[NEEDS CLARIFICATION]`, `[TBD]`, `[TODO]`, `[PLACEHOLDER]` | All files |
| `required_sections` | Verifies expected markdown headers exist | research.md, data-model.md |
| `traceability` | Confirms FR-XXX or US-XXX references present | All files |
| `pii_markers` | Checks if PII fields (email, phone, ssn, etc.) have `[PII]` annotation | data-model.md |
| `entity_consistency` | Validates entity names appear across related files | When 2+ files provided |

**Note**: OpenAPI/contract files are skipped (use `validate-openapi.py` instead).

### Example Output

```json
{
  "files": ["data-model.md"],
  "checks": [
    {"check": "unresolved_markers", "passed": false, "issues": ["Line 45: [NEEDS CLARIFICATION] marker found"]},
    {"check": "required_sections", "passed": true, "issues": []},
    {"check": "traceability", "passed": true, "fr_count": 5, "us_count": 3, "issues": []},
    {"check": "pii_markers", "passed": false, "issues": ["Line 23: 'email' field may need [PII] annotation"]}
  ],
  "summary": {"total": 4, "passed": 2, "failed": 2}
}
```

### Exit Codes

- `0` - All checks passed
- `1` - One or more checks failed

### Usage Pattern

Run the validation script **before** starting manual review:

1. **Run automated checks first**
   ```bash
   python scripts/check-artifacts.py .spec/plan/research.md
   ```

2. **Address automated findings** - Fix obvious issues like unresolved markers

3. **Proceed with manual review** - Use the phase-specific checklists below for deeper analysis

This ensures mechanical issues are caught early, letting reviewers focus on design quality and completeness.

## Quality Checklist

Before finalizing review, verify:

- [ ] All phase-specific checks executed
- [ ] Issues properly classified by severity
- [ ] Evidence cited for each issue
- [ ] Suggested fixes are actionable
- [ ] Verdict matches issue severity
- [ ] Cross-artifact concerns noted
- [ ] Strengths acknowledged

## Anti-Patterns to Avoid

- **Implementation focus**: Review design, not code details
- **Vague issues**: "Could be better" - specify what and how
- **Severity inflation**: Not everything is Critical
- **Missing evidence**: Issues need citations to artifacts
- **No suggestions**: Every issue needs a path to resolution
- **Ignoring strengths**: Acknowledge good work
- **Rubber stamping**: Being too agreeable defeats the purpose
