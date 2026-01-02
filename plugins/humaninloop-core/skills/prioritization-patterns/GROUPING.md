# Related Item Grouping

Patterns for grouping related items to reduce cognitive load and improve efficiency.

---

## Core Concept

Instead of presenting N individual items, group related items into coherent clusters. Benefits:

- **Reduced cognitive load**: Fewer decisions to make
- **Better context**: Related items presented together
- **Efficient resolution**: Address multiple items at once
- **Clearer communication**: Focused questions/actions

---

## Grouping Criteria (Priority Order)

Apply these criteria in order:

1. **Same Source**: Items from the same requirement, file, or origin
2. **Same Domain**: Items in the same functional area (auth, API, data)
3. **Same Section**: Items in the same document section
4. **Related Concepts**: Items about related topics (timeout + retry + error)

---

## Grouping Algorithm

```
groups = []

for each item in items_to_process:
  domain = extract_domain(item.source, item.description)

  matching_group = find_group_by_domain(groups, domain)

  if matching_group:
    matching_group.add(item)
  else:
    groups.append(new_group(domain, item))

# Merge singleton groups with related groups
for group in groups where len(group) == 1:
  if can_merge_with_related(group, groups):
    merge_groups(group, related_group)

return groups
```

---

## Domain Extraction

Extract domain from item metadata:

| Source Pattern | Domain |
|---------------|--------|
| FR-001 to FR-010 | auth |
| FR-011 to FR-020 | data |
| FR-021 to FR-030 | api |
| Contains "login", "password", "session" | auth |
| Contains "endpoint", "route", "request" | api |
| Contains "entity", "model", "field" | data |

---

## Group Size Limits

| Constraint | Value | Rationale |
|------------|-------|-----------|
| **Max items per group** | 5-7 | Cognitive limit |
| **Max groups per iteration** | 3 | Focus limit |
| **Min items for group** | 1 | Singletons allowed |

If items exceed limits:
1. Split large groups by sub-domain
2. Prioritize groups by highest-severity item
3. Defer lower-priority groups to next iteration

---

## Group Presentation Format

### Single Item in Group

```markdown
**Group**: {domain}
**Priority**: {priority}

**Item**: {description}
**Source**: {source_reference}
```

### Multiple Items in Group

```markdown
**Group**: {domain}
**Priority**: {highest_priority_in_group}
**Items**: {count}

Regarding {domain}:
- {item_1.description}
- {item_2.description}
- {item_3.description}

**Sources**: {combined_sources}
```

---

## Grouping Examples

### Example 1: Authentication Domain

**Input Items**:
```
[
  {id: "G-001", source: "FR-003", desc: "Auth failure handling undefined"},
  {id: "G-002", source: "FR-003", desc: "Retry count not specified"},
  {id: "G-003", source: "FR-004", desc: "Password reset flow undefined"}
]
```

**Output Group**:
```markdown
**Group**: Authentication
**Priority**: Critical
**Items**: 3

Regarding authentication, these items need attention:
- What happens on authentication failure?
- How many retries before lockout?
- What is the password reset flow?

**Sources**: FR-003, FR-004
```

### Example 2: API Domain

**Input Items**:
```
[
  {id: "G-010", source: "FR-020", desc: "Rate limiting undefined"},
  {id: "G-011", source: "FR-021", desc: "Error format not specified"},
  {id: "G-012", source: "FR-020", desc: "Timeout behavior undefined"}
]
```

**Output Group**:
```markdown
**Group**: API Behavior
**Priority**: Important
**Items**: 3

Regarding API behavior, these items need attention:
- What are rate limiting thresholds?
- What is the standard error response format?
- What is request timeout and retry behavior?

**Sources**: FR-020, FR-021
```

---

## Overflow Handling

When more groups than can be processed:

```markdown
**Processed This Iteration**: 3 groups (12 items)
**Deferred to Next Iteration**: 2 groups (8 items)

Deferred groups will be prioritized in next iteration.
```

---

## Group State Tracking

Track group resolution across iterations:

```markdown
| Group | Domain | Items | Priority | Status | Iteration |
|-------|--------|-------|----------|--------|-----------|
| GRP-1 | auth | 3 | Critical | resolved | 1 |
| GRP-2 | api | 3 | Important | pending | 1 |
| GRP-3 | data | 2 | Minor | deferred | 1 |
```

---

## Integration with Severity

Groups inherit highest severity in set:

```
group.priority = max(item.priority for item in group.items)
```

Group ordering:
1. Critical groups first
2. Important groups second
3. Minor groups last (often deferred)
