# Decision Rationale

Patterns for capturing and documenting the reasoning behind decisions.

---

## Why Document Rationale?

Without documented rationale:
- Future team members wonder "why did we do this?"
- Same decision gets relitigated
- Context is lost when people leave
- Can't learn from past decisions

With documented rationale:
- Decisions are reviewable
- Onboarding is faster
- Mistakes aren't repeated
- Success patterns are preserved

---

## Rationale Components

A complete rationale includes:

| Component | Question Answered |
|-----------|-------------------|
| **Context** | What situation required a decision? |
| **Constraints** | What limited our options? |
| **Options** | What alternatives did we consider? |
| **Decision** | What did we choose? |
| **Reasoning** | Why did we choose this? |
| **Trade-offs** | What did we accept to gain? |
| **Consequences** | What follows from this decision? |

---

## Rationale Template

```markdown
# Decision: {title}

**Date**: {when decided}
**Deciders**: {who was involved}
**Status**: {proposed | accepted | superseded}

## Context

{What situation or problem required this decision?
What triggered the need to decide?}

## Constraints

{What factors limited our options?}
- {constraint 1}
- {constraint 2}

## Options Considered

### Option 1: {name}
{brief description}
- Pros: {advantages}
- Cons: {disadvantages}

### Option 2: {name}
{brief description}
- Pros: {advantages}
- Cons: {disadvantages}

### Option 3: {name}
{brief description}
- Pros: {advantages}
- Cons: {disadvantages}

## Decision

We chose **{option name}**.

## Reasoning

{Why this option over the others?
What made this the best choice given our constraints?}

Key factors:
- {factor 1}
- {factor 2}
- {factor 3}

## Trade-offs Accepted

- {what we gave up to get this benefit}
- {what we gave up to get this benefit}

## Consequences

### Positive
- {good things that follow}

### Negative
- {challenges or costs that follow}

### Neutral
- {changes that are neither good nor bad}

## Related Decisions

- {link to related decision record}
```

---

## Writing Good Rationale

### Be Specific

**Bad**: "We chose React because it's popular."

**Good**: "We chose React because:
- Team has 3 years React experience
- Large component ecosystem reduces build time
- Hiring pool is larger than Vue or Angular"

### Document Rejected Options

**Bad**: Only document the chosen option.

**Good**: Document why alternatives were rejected:
"We didn't choose Angular because:
- Steeper learning curve for new team members
- Less flexible for our micro-frontend architecture"

### Include Context That May Change

**Bad**: Assume context is obvious.

**Good**: Document assumptions:
"This decision assumes:
- Team size stays under 10
- Budget remains stable
- We don't need offline support"

---

## Lightweight Rationale

For smaller decisions, use inline rationale:

```markdown
// We use UUIDs instead of auto-increment because:
// 1. Enables distributed ID generation
// 2. Prevents enumeration attacks
// 3. Matches existing user service pattern
```

Or in commit messages:

```
feat: use PostgreSQL for user storage

Chose PostgreSQL over MongoDB because:
- Team expertise (3 years experience)
- ACID needed for financial data
- Query patterns favor relational model

Trade-off: Horizontal scaling harder, acceptable for current scale.
```

---

## Rationale Review Triggers

Review past rationales when:

| Trigger | Action |
|---------|--------|
| Context changed | Check if decision still valid |
| Problem recurs | Learn from past attempts |
| New team member asks | Use to onboard |
| Similar decision needed | Reference for patterns |

---

## Rationale Discovery Questions

When decisions lack documentation, reconstruct by asking:

1. What were we trying to achieve?
2. What options did we consider?
3. Who made the decision?
4. What did we know at the time?
5. What would we do differently now?

---

## Rationale Anti-Patterns

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| **No rationale** | Can't understand decisions | Always document why |
| **Vague rationale** | "It seemed best" | Be specific about factors |
| **Outcome-only** | What, but not why | Include reasoning |
| **Post-hoc rationalization** | Invented after the fact | Document at decision time |
| **Hero narrative** | "I just knew" | Document actual reasoning |
