# Quality Dimensions

Patterns for identifying and selecting quality dimensions for any artifact.

---

## What is a Quality Dimension?

A quality dimension is a named aspect of quality that can be:
- **Defined**: What does it mean?
- **Measured**: How do we evaluate it?
- **Prioritized**: How important is it?

---

## Universal Dimension Categories

Most artifacts can be evaluated across these categories:

### Content Dimensions

| Dimension | Question | Examples |
|-----------|----------|----------|
| **Completeness** | Is everything required present? | All fields defined, all cases covered |
| **Correctness** | Is it accurate and valid? | No errors, satisfies constraints |
| **Consistency** | Do parts agree with each other? | Naming consistent, no contradictions |

### Form Dimensions

| Dimension | Question | Examples |
|-----------|----------|----------|
| **Clarity** | Is it understandable? | Unambiguous, well-explained |
| **Structure** | Is it well-organized? | Logical flow, proper hierarchy |
| **Readability** | Is it easy to consume? | Good formatting, scannable |

### Process Dimensions

| Dimension | Question | Examples |
|-----------|----------|----------|
| **Traceability** | Can we track origins and impacts? | Links to sources, audit trail |
| **Testability** | Can we verify it works? | Measurable success criteria |
| **Maintainability** | Can we update it easily? | Modular, documented |

---

## Domain-Specific Dimensions

### For Specifications

| Dimension | What It Means |
|-----------|---------------|
| **Completeness** | All requirements present, no gaps |
| **Clarity** | No ambiguous language, no interpretation needed |
| **Consistency** | Requirements don't contradict each other |
| **Measurability** | Success criteria are quantifiable |
| **Traceability** | Each requirement links to user need |

### For Code

| Dimension | What It Means |
|-----------|---------------|
| **Correctness** | Produces expected outputs |
| **Performance** | Meets speed/resource requirements |
| **Security** | No vulnerabilities |
| **Maintainability** | Easy to understand and modify |
| **Testability** | Can be verified via tests |

### For APIs

| Dimension | What It Means |
|-----------|---------------|
| **Consistency** | Follows conventions across endpoints |
| **Discoverability** | Easy to understand without documentation |
| **Evolvability** | Can be extended without breaking clients |
| **Completeness** | All operations needed are present |

### For Documentation

| Dimension | What It Means |
|-----------|---------------|
| **Accuracy** | Matches reality |
| **Completeness** | All topics covered |
| **Clarity** | Easy to understand |
| **Currency** | Up to date |

---

## Dimension Selection Process

### Step 1: Identify Artifact Type

```
Artifact: {what are you evaluating?}
Domain: {what category does it belong to?}
```

### Step 2: List Candidate Dimensions

Start with universal categories, then add domain-specific:

```
Universal:
- Completeness
- Correctness
- Clarity
- Consistency

Domain-Specific:
- {dimension from domain table}
- {dimension from domain table}
```

### Step 3: Prioritize by Impact

| Dimension | Impact if Missing | Priority |
|-----------|-------------------|----------|
| {dim_1} | {consequence} | Critical |
| {dim_2} | {consequence} | Important |
| {dim_3} | {consequence} | Minor |

### Step 4: Select Final Set

- Keep 4-7 dimensions (cognitive limit)
- Must include at least one from each category
- Prioritize by risk and stakeholder needs

---

## Dimension Documentation Format

```markdown
## Quality Dimensions for {artifact_type}

| Dimension | Definition | Why It Matters |
|-----------|------------|----------------|
| {name} | {what it means} | {consequence if missing} |
```

---

## Dimension Relationships

Dimensions can interact:

| Relationship | Example |
|--------------|---------|
| **Supports** | Clarity supports Correctness (can spot errors) |
| **Tensions** | Completeness vs. Clarity (more detail, more noise) |
| **Requires** | Testability requires Measurability |

Document known tensions to guide trade-off decisions.
