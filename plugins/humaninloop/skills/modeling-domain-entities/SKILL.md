---
name: modeling-domain-entities
description: DDD-style entity modeling from requirements including extraction, attributes, relationships, and state machines. Use when extracting entities, defining attributes, modeling relationships, or when you see "entity", "data model", "relationship", "cardinality", "domain model", or "state machine".
---

# Modeling Domain Entities

## Purpose

Extract and model domain entities from requirements using Domain-Driven Design principles. This skill covers entity identification, attribute definition, relationship modeling, and state machine documentation.

## Entity Extraction

### Identification Heuristics

Look for entities in:

| Source | Pattern | Example |
|--------|---------|---------|
| **User stories** | "As a [Role]..." | User, Admin, Guest |
| **Subjects** | "The [Entity] must..." | Task, Order, Product |
| **Actions** | "...create a [Entity]" | Comment, Message, Report |
| **Possessives** | "[Entity]'s [attribute]" | User's profile, Order's items |
| **Status mentions** | "[Entity] status" | TaskStatus, OrderState |

### Entity vs. Attribute Decision

```
IF concept has its own lifecycle → Entity
IF concept only exists within another → Attribute
IF concept connects two entities → Relationship (possibly join entity)
IF concept has just one value → Attribute

Examples:
- "user email" → Attribute of User (just one value)
- "user address" → Could be Entity (if reused) or Attribute (if embedded)
- "order items" → Separate entity (has own lifecycle)
- "task status" → Enum/attribute (limited values)
```

### Brownfield Entity Status

When modeling in brownfield projects:

| Status | Meaning | Action |
|--------|---------|--------|
| `[NEW]` | Entity doesn't exist | Create full definition |
| `[EXTENDS EXISTING]` | Adding to existing entity | Document new fields only |
| `[REUSES EXISTING]` | Using existing as-is | Reference only |
| `[RENAMED]` | Avoiding collision | Document new name + reason |

## Attribute Definition

### Standard Attributes

Every entity typically needs:

```markdown
### Standard Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | Identifier | Yes | Primary key |
| createdAt | Timestamp | Yes | Creation time |
| updatedAt | Timestamp | Yes | Last modification |
| deletedAt | Timestamp | No | Soft delete marker |
```

### Attribute Format

```markdown
| Attribute | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| id | UUID | Yes | auto-generated | Unique identifier |
| email | Email | Yes | - | User's email address |
| name | Text(100) | No | null | Display name |
| role | Enum[admin,member,guest] | Yes | member | Access level |
| isVerified | Boolean | Yes | false | Email verified flag |
```

### Conceptual Types

Use conceptual types (not database-specific):

| Conceptual Type | Description |
|-----------------|-------------|
| `Identifier` / `UUID` | Unique identifier |
| `Text` / `Text(N)` | String with optional max length |
| `Email` | Email format string |
| `URL` | URL format string |
| `Integer` | Whole number |
| `Decimal` / `Decimal(P,S)` | Decimal with precision |
| `Boolean` | True/false |
| `Timestamp` | Date and time |
| `Date` | Date only |
| `Enum[values]` | Fixed set of values |
| `JSON` | Structured data |
| `Reference(Entity)` | Foreign key reference |

### PII Identification

Mark sensitive fields:

```markdown
| Attribute | Type | Required | PII | Description |
|-----------|------|----------|-----|-------------|
| email | Email | Yes | **PII** | User's email |
| phone | Text(20) | No | **PII** | Phone number |
| ssn | Text(11) | No | **PII-SENSITIVE** | Social security |
```

## Relationship Modeling

### Relationship Types

| Type | Cardinality | Example |
|------|-------------|---------|
| **One-to-One** | 1:1 | User ←→ Profile |
| **One-to-Many** | 1:N | User ←→ Tasks |
| **Many-to-Many** | N:M | Users ←→ Projects |

### Relationship Format

```markdown
## Relationships

### User → Tasks (1:N)

| Aspect | Value |
|--------|-------|
| **Type** | One-to-Many |
| **From** | User (one) |
| **To** | Task (many) |
| **Foreign Key** | task.userId |
| **Required** | Task requires User |
| **On Delete** | Cascade (delete tasks) |

### Users ↔ Projects (N:M)

| Aspect | Value |
|--------|-------|
| **Type** | Many-to-Many |
| **From** | User |
| **To** | Project |
| **Join Entity** | ProjectMember |
| **Additional Fields** | role, joinedAt |
```

### Relationship Diagram (Text)

```markdown
## Entity Relationships

```
User ──1:N──▶ Task (owns)
User ──1:N──▶ Session (has)
User ◀──N:M──▶ Project (via ProjectMember)
Task ──N:1──▶ Project (belongs to)
```
```

## State Machine Modeling

### When to Model State

Model state machines when:
- Entity has a `status` or `state` field
- Requirements mention workflow or lifecycle
- Specific actions change entity state
- Certain actions only valid in certain states

### State Machine Format

```markdown
## State Machine: Task Status

### States

| State | Description | Entry Condition |
|-------|-------------|-----------------|
| `draft` | Initial state | Created by user |
| `active` | Work in progress | User starts task |
| `completed` | Work finished | User marks done |
| `archived` | No longer active | User archives |

### Transitions

| From | To | Trigger | Guard | Side Effects |
|------|-----|---------|-------|--------------|
| draft | active | user.startTask() | - | Set startedAt |
| active | completed | user.completeTask() | - | Set completedAt |
| active | draft | user.unpublish() | User is owner | Clear startedAt |
| completed | archived | user.archive() | - | - |
| * | archived | admin.archive() | Is admin | Log action |

### Diagram

```
[draft] ──start──▶ [active] ──complete──▶ [completed]
   │                  │                        │
   │                  ▼                        │
   └──────────▶ [archived] ◀───archive─────────┘
```
```

## data-model.md Structure

```markdown
# Data Model: {feature_id}

> Entity definitions and relationships for the feature.
> Generated by Domain Architect.

---

## Summary

| Entity | Attributes | Relationships | Status |
|--------|------------|---------------|--------|
| User | 8 | 3 | [EXTENDS EXISTING] |
| Session | 5 | 1 | [NEW] |
| ...

---

## Entity: User [EXTENDS EXISTING]

> Existing entity extended with authentication fields.

### Attributes

| Attribute | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| passwordHash | Text | Yes | - | Hashed password |
| lastLoginAt | Timestamp | No | null | Last login time |

### Existing Attributes (Not Modified)

| Attribute | Type | Description |
|-----------|------|-------------|
| id | UUID | Existing primary key |
| email | Email | Existing email field |

---

## Entity: Session [NEW]

> User authentication session.

### Attributes

| Attribute | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| id | UUID | Yes | auto | Session identifier |
| userId | Reference(User) | Yes | - | Owning user |
| token | Text(255) | Yes | - | Session token |
| expiresAt | Timestamp | Yes | - | Expiration time |
| createdAt | Timestamp | Yes | auto | Creation time |

### Relationships

| Relationship | Type | Target | Description |
|--------------|------|--------|-------------|
| user | N:1 | User | Session belongs to user |

---

## Relationships

[Relationship documentation]

---

## State Machines

[State machine documentation if applicable]

---

## Traceability

| Entity | Source Requirements |
|--------|---------------------|
| User | FR-001, FR-002, US#1 |
| Session | FR-003, US#2 |
```

## Validation Script

A Python validation script is provided to check data-model.md files for completeness and consistency.

### Location

```
scripts/validate-model.py
```

### Usage

```bash
python scripts/validate-model.py path/to/data-model.md
```

### Checks Performed

| Check | Description |
|-------|-------------|
| **entity_format** | Entities follow `## Entity: Name` format with PascalCase names |
| **required_attributes** | Each entity has an attributes table with data |
| **relationships** | Relationship keywords or sections present (belongs to, has many, N:1, etc.) |
| **state_machines** | Entities with status/state Enum fields have transitions documented |
| **validation_rules** | Validation constraints documented (required, unique, type constraints) |
| **audit_fields** | New entities have createdAt/updatedAt fields |
| **id_fields** | New entities have identifier field (id, UUID) |

### Example Output

```json
{
  "file": "data-model.md",
  "entities_found": ["User", "Task", "Project"],
  "checks": [
    {"check": "entity_format", "passed": true, "issues": []},
    {"check": "required_attributes", "passed": true, "issues": []},
    {"check": "relationships", "passed": false, "issues": ["Task: No relationships defined"]},
    {"check": "state_machines", "passed": true, "issues": []},
    {"check": "validation_rules", "passed": true, "issues": []},
    {"check": "audit_fields", "passed": false, "issues": ["Project: Missing audit fields (createdAt/updatedAt)"]},
    {"check": "id_fields", "passed": true, "issues": []}
  ],
  "summary": {"total": 7, "passed": 5, "failed": 2}
}
```

### Exit Codes

- `0` - All checks passed
- `1` - One or more checks failed

### Integration Pattern

Run validation after generating or modifying data-model.md:

```bash
# Validate and show results
python scripts/validate-model.py .hil/data-model.md

# Use in CI/automation
if python scripts/validate-model.py .hil/data-model.md > /dev/null 2>&1; then
    echo "Data model valid"
else
    echo "Data model has issues"
fi
```

---

## Quality Checklist

Before finalizing entity model, verify:

- [ ] Every noun from requirements evaluated for entity status
- [ ] Each entity has id, createdAt, updatedAt fields
- [ ] All attributes have type, required flag, description
- [ ] Relationships include cardinality and direction
- [ ] PII fields marked and documented
- [ ] State machines documented for stateful entities
- [ ] Brownfield status indicated for each entity
- [ ] Traceability to requirements documented

## Anti-Patterns to Avoid

- **Missing entities**: Don't skip entities mentioned in requirements
- **Anemic entities**: Entities with only ID are suspicious
- **Implementation types**: Use conceptual types, not VARCHAR(255)
- **Undefined relationships**: Every reference needs relationship docs
- **Hidden state**: Status fields need state machine documentation
- **Unmarked PII**: Always identify sensitive data
- **Orphan entities**: Every entity needs at least one relationship
