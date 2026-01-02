# Specification Checks

> Validation checks for spec.md artifact.
> Used by Validator Agent during the specify workflow validation phase.

---

## Check Categories

### Structural Completeness

| ID | Check | Description | Tier |
|----|-------|-------------|------|
| SPEC-001 | Header complete | Header section has feature ID, created date, status, original input | auto-resolve |
| SPEC-002 | User stories present | At least 2 user stories with priority levels | auto-retry |
| SPEC-003 | Requirements present | At least 3 functional requirements with FR-XXX IDs | auto-retry |
| SPEC-004 | Success criteria present | At least 2 success criteria with SC-XXX IDs | auto-retry |
| SPEC-005 | Edge cases present | At least 1 edge case scenario documented | auto-retry |

### Content Quality

| ID | Check | Description | Tier |
|----|-------|-------------|------|
| SPEC-006 | RFC 2119 keywords used | Requirements use MUST/SHOULD/MAY for clarity | auto-resolve |
| SPEC-007 | No implementation details | Spec contains no technology-specific references | auto-retry |
| SPEC-008 | Testable requirements | All requirements are independently verifiable | auto-retry |
| SPEC-009 | Measurable criteria | Success criteria have quantifiable thresholds | auto-retry |

### Clarity & Consistency

| ID | Check | Description | Tier |
|----|-------|-------------|------|
| SPEC-010 | No vague terms | Terms like "fast", "easy", "user-friendly" are quantified | auto-retry |
| SPEC-011 | Priority justified | Each user story priority has business rationale | auto-resolve |
| SPEC-012 | Unique IDs | All FR-XXX and SC-XXX IDs are unique | auto-resolve |
| SPEC-013 | Cross-references valid | All internal references resolve to existing items | auto-resolve |

### Clarification Handling

| ID | Check | Description | Tier |
|----|-------|-------------|------|
| SPEC-014 | Clarification limit | Maximum 3 [NEEDS CLARIFICATION] markers | auto-retry |
| SPEC-015 | Clarifications structured | Each marker has ID, question, and options | auto-resolve |

### Constitution Alignment [phase:specify]

| ID | Check | Description | Tier |
|----|-------|-------------|------|
| SPEC-016 | Principles respected | Spec aligns with constitution principles | escalate |
| SPEC-017 | Deviations justified | Any deviations from principles are documented | escalate |

---

## Tier Behavior

| Tier | Behavior | Max Retries |
|------|----------|-------------|
| `auto-retry` | Spec Writer Agent retries with guidance | 2 |
| `auto-resolve` | Validator flags, applies automatic fix | N/A |
| `escalate` | Return to Supervisor for user decision | N/A |

---

## Gap Classification

| Priority | Checks | Rationale |
|----------|--------|-----------|
| Critical | SPEC-002, SPEC-003, SPEC-016 | Missing core content blocks downstream; constitution violations require approval |
| Important | SPEC-004, SPEC-005, SPEC-007, SPEC-008, SPEC-009, SPEC-010, SPEC-014, SPEC-017 | Quality issues that should be fixed |
| Minor | SPEC-001, SPEC-006, SPEC-011, SPEC-012, SPEC-013, SPEC-015 | Can be auto-resolved |

---

## Validation Process

```
1. Load spec.md
2. Load constitution (if present)
3. For each check in order:
   a. Execute check logic
   b. If FAIL:
      - Classify gap priority
      - Assign tier behavior
      - Add to gap queue
4. Return validation_report with:
   - total_checks
   - passed_count
   - gaps[] with priority, tier, description
   - result: pass | fail | partial
```

---

## Example Gap Output

```json
{
  "gap_id": "G-SPEC-007",
  "check_id": "SPEC-007",
  "priority": "Important",
  "tier": "auto-retry",
  "description": "Spec mentions 'PostgreSQL' - implementation detail detected",
  "location": "FR-005",
  "suggested_action": "Spec Writer should rephrase in technology-agnostic terms"
}
```
