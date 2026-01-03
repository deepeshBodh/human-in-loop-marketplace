---
name: reviewing-specifications
description: Review specifications to find gaps and generate clarifying questions. Use when reviewing spec.md, finding missing requirements, identifying ambiguities, or when you see "review spec", "find gaps", "what's missing", or "clarify requirements". Focuses on product decisions, not implementation details.
---

# Reviewing Specifications

## Purpose

Find gaps in specifications and generate clarifying questions that a product owner or stakeholder can answer. Focus on WHAT is missing, not HOW to implement.

## Core Principle

**Ask product questions, not implementation questions.**

| Wrong (Technical) | Right (Product) |
|-------------------|-----------------|
| "What happens if the database connection fails?" | "What should users see if the system is temporarily unavailable?" |
| "Should we use optimistic or pessimistic locking?" | "Can two users edit the same item simultaneously?" |
| "What's the retry policy for failed API calls?" | "How long should users wait before seeing an error?" |
| "What HTTP status code for invalid input?" | "What message should users see for invalid input?" |

## Question Format

Frame every question as a decision the user can make:

```markdown
**Question**: [Clear product decision]

**Options**:
1. [Concrete choice] - [What this means for users]
2. [Concrete choice] - [What this means for users]
3. [Concrete choice] - [What this means for users]

**Why this matters**: [User or business impact]
```

## Gap Categories

Focus on these user-facing gaps:

| Category | Example Questions |
|----------|-------------------|
| **User expectations** | "What should users see when...?" |
| **Business rules** | "Is X allowed? Under what conditions?" |
| **Scope boundaries** | "Is Y in scope for this feature?" |
| **Success/failure states** | "What happens if the user...?" |
| **Permissions** | "Who can do X? Who cannot?" |

## What to Avoid

- Implementation details (databases, APIs, protocols)
- Technical edge cases (connection failures, race conditions)
- Architecture decisions (caching, queuing, scaling)
- Performance specifications (latency, throughput)

These are valid concerns but belong in the planning phase, not specification.

## Severity Classification

| Severity | Definition | Action |
|----------|------------|--------|
| **Critical** | Cannot build without this answer | Must ask now |
| **Important** | Will cause rework if not clarified | Should ask now |
| **Minor** | Polish issue, can defer | Log and continue |

## Output Format

```markdown
## Gaps Found

### Critical
- **Gap**: [What's missing]
  - **Question**: [Product decision needed]
  - **Options**: [2-3 choices]

### Important
- **Gap**: [What's missing]
  - **Question**: [Product decision needed]
  - **Options**: [2-3 choices]

### Minor (Deferred)
- [Gap description] - can be resolved during planning
```
