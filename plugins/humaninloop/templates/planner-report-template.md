# Planner Report

> Feature: {{feature_id}}
> Phase: {{phase}}
> Iteration: {{iteration}}
> Generated: {{timestamp}}

---

## Summary

| Metric | Value |
|--------|-------|
| **Phase** | {{phase}} |
| **Artifact** | {{artifact_path}} |
| **Completion** | {{completion_status}} |

---

## What Was Produced

{{production_summary}}

---

## Key Outputs

### Research Phase
<!-- If phase == research -->
| Decision | Choice | Rationale |
|----------|--------|-----------|
| {{decision}} | {{choice}} | {{rationale}} |

### Data Model Phase
<!-- If phase == datamodel -->
| Entity | Attributes | Relationships | Status |
|--------|------------|---------------|--------|
| {{entity}} | {{attr_count}} | {{rel_count}} | {{status}} |

### Contracts Phase
<!-- If phase == contracts -->
| Endpoint | Method | Description |
|----------|--------|-------------|
| {{path}} | {{method}} | {{description}} |

---

## Constitution Alignment

{{constitution_alignment}}

---

## Open Questions

{{open_questions}}

---

## Ready for Review

{{ready_for_review}}
