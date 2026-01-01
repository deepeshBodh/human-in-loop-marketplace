# Phase 3: Validation Procedures

Detailed procedures for the Validation phase (plan-validator agent).

---

## Mission

Validate plan workflow artifacts against phase-specific check modules. Execute checks, classify gaps by severity, apply tiered behavior, and determine next action.

---

## Check Modules

| Phase | Check Module | Artifacts Validated |
|-------|--------------|---------------------|
| 0 | research-checks.md | research.md |
| 1 | model-checks.md | data-model.md, entity registry |
| 2 | contract-checks.md | contracts/, quickstart.md |
| 3 | final-checks.md | All artifacts (cross-artifact) |

---

## Check Execution

For each check in the module:

### 1. Parse Check Definition
- ID (CHK-xxx)
- Description
- Tier (auto-resolve, auto-retry, escalate)

### 2. Execute Check Logic
Compare artifact against criteria

### 3. Classify Gap (if failed)

| Priority | Description |
|----------|-------------|
| Critical | Blocks downstream phases |
| Important | Should be fixed before proceeding |
| Minor | Can be deferred |

### 4. Apply Tiered Behavior

| Tier | Action |
|------|--------|
| `auto-resolve` | Attempt fix, log action, mark resolved |
| `auto-retry` | Mark pending, provide guidance for responsible agent |
| `escalate` | Mark escalating, prepare user-facing question |

---

## Constitution Validation

Check principles tagged with current phase:

| Phase | Tags |
|-------|------|
| 0 | `[phase:0]` - Technology choices |
| 1 | `[phase:1]` - Data privacy |
| 2 | `[phase:2]` - API standards |
| 3 | Full sweep - All principles |

**Constitution violations are ALWAYS `escalate` tier.**

---

## Result Determination

| Result | Condition | Next Action |
|--------|-----------|-------------|
| `pass` | 0 Critical, 0 Important | Proceed to next phase |
| `partial` | 0 Critical, >0 Important (all auto-resolved) | Proceed to next phase |
| `fail` | >0 Critical OR >0 Important (not resolved) | Loop back or escalate |

---

## Gap Queue Updates

For each gap found, add to index.md:

```markdown
| Priority | Gap ID | Phase | Artifact | Issue | Status |
|----------|--------|-------|----------|-------|--------|
| Critical | G-P001 | 1 | data-model.md | Missing User entity | pending |
| Important | G-P002 | 1 | data-model.md | No validation rules | auto-resolved |
```

---

## Handoff Notes Format

```markdown
### From Validator (Phase {N})

**Check Module**: {module_name}
**Result**: {pass|partial|fail}

**Summary**:
- Total checks: {count}
- Passed: {count}
- Failed: {count}

**Gaps Found**:
- Critical: {count}
- Important: {count} ({auto_resolved_count} auto-resolved)
- Minor: {count}

**Constitution**: {pass|fail}

**Next Action**: {proceed|retry|escalate}
**Next Phase**: {N+1} (if proceeding)
```

---

## Quality Checklist

- [ ] All checks from module executed
- [ ] Every failed check has gap entry
- [ ] Gap priority and tier correctly assigned
- [ ] Auto-resolved gaps have resolution details
- [ ] Escalated gaps have user-facing questions
- [ ] Constitution principles checked
- [ ] plan-context.md has validator handoff notes
- [ ] index.md gap queue updated
- [ ] Next action correctly determined
