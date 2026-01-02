# Options Matrix

Patterns for systematically evaluating decision alternatives.

---

## Purpose

An options matrix provides a structured way to compare alternatives using consistent criteria. It makes evaluation transparent and helps avoid bias toward first-considered options.

---

## Basic Matrix Structure

```markdown
| Option | Criterion 1 | Criterion 2 | Criterion 3 | Score |
|--------|-------------|-------------|-------------|-------|
| Option A | {rating} | {rating} | {rating} | {total} |
| Option B | {rating} | {rating} | {rating} | {total} |
| Option C | {rating} | {rating} | {rating} | {total} |
```

---

## Defining Criteria

Choose criteria based on decision context:

### Common Criteria Categories

| Category | Example Criteria |
|----------|-----------------|
| **Capability** | Features, functionality, completeness |
| **Quality** | Performance, security, reliability |
| **Cost** | License, development, maintenance |
| **Risk** | Maturity, vendor stability, lock-in |
| **Fit** | Team expertise, existing stack, standards |

### Good Criteria Properties

- **Relevant**: Actually matters for this decision
- **Distinguishing**: Options differ on this criterion
- **Measurable**: Can assign ratings objectively
- **Independent**: Doesn't duplicate other criteria

---

## Rating Scales

### Qualitative (Simple)

```
++ : Excellent fit
+  : Good fit
o  : Neutral / Acceptable
-  : Poor fit
-- : Very poor fit
```

### Numeric (Weighted)

```
5 : Fully meets criterion
4 : Mostly meets criterion
3 : Partially meets criterion
2 : Barely meets criterion
1 : Does not meet criterion
```

---

## Weighted Evaluation

When criteria have different importance:

### Step 1: Assign Weights

```markdown
| Criterion | Weight | Rationale |
|-----------|--------|-----------|
| Performance | 3 | Critical for user experience |
| Cost | 2 | Budget constrained |
| Ease of use | 1 | Nice to have |
```

### Step 2: Calculate Weighted Scores

```markdown
| Option | Performance (×3) | Cost (×2) | Ease (×1) | Total |
|--------|------------------|-----------|-----------|-------|
| A | 4 (12) | 3 (6) | 5 (5) | 23 |
| B | 5 (15) | 2 (4) | 3 (3) | 22 |
| C | 3 (9) | 5 (10) | 4 (4) | 23 |
```

---

## Pros and Cons Format

For qualitative evaluation:

```markdown
## Option A: {name}

**Pros**:
- {advantage 1}
- {advantage 2}
- {advantage 3}

**Cons**:
- {disadvantage 1}
- {disadvantage 2}

**Best For**: {when to choose this option}
```

---

## Comparison Table Format

For direct comparison:

```markdown
| Aspect | Option A | Option B | Option C |
|--------|----------|----------|----------|
| {aspect 1} | {how A handles} | {how B handles} | {how C handles} |
| {aspect 2} | {how A handles} | {how B handles} | {how C handles} |
```

---

## Must-Have vs Nice-to-Have

Filter options using requirements:

### Step 1: Define Must-Haves

```markdown
**Must-Have Requirements** (eliminates option if not met):
- [ ] Supports PostgreSQL
- [ ] Active maintenance (commits in last 6 months)
- [ ] Open source license
```

### Step 2: Filter Options

```markdown
| Option | PostgreSQL | Maintained | Open Source | Passes |
|--------|------------|------------|-------------|--------|
| A | Yes | Yes | Yes | ✓ |
| B | Yes | No | Yes | ✗ |
| C | No | Yes | Yes | ✗ |
```

### Step 3: Evaluate Survivors on Nice-to-Haves

Only options passing must-haves continue to weighted evaluation.

---

## Example: Technology Selection

```markdown
## Database Selection

**Decision**: Choose database for user data storage

**Must-Haves**:
- ACID compliance
- Horizontal scaling
- Team experience

**Options After Filtering**:

| Criterion (Weight) | PostgreSQL | MySQL | MongoDB |
|-------------------|------------|-------|---------|
| ACID Compliance (3) | 5 (15) | 4 (12) | 3 (9) |
| Scaling (2) | 3 (6) | 3 (6) | 5 (10) |
| Team Experience (2) | 5 (10) | 4 (8) | 2 (4) |
| Community Support (1) | 5 (5) | 5 (5) | 4 (4) |
| **Total** | **36** | **31** | **27** |

**Recommendation**: PostgreSQL
```

---

## When Matrix Falls Short

The matrix helps but doesn't decide:

- **Close scores**: Need qualitative judgment
- **Missing criteria**: May need to revisit criteria
- **Strategic factors**: Some factors transcend scoring
- **Gut check**: If result feels wrong, examine assumptions

Document any override of matrix recommendation with clear rationale.
