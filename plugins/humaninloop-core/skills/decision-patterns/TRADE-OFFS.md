# Trade-Off Documentation

Patterns for identifying and documenting trade-offs in decisions.

---

## What is a Trade-Off?

A trade-off is something you give up to get something else:

- **Gain**: What you get from the decision
- **Cost**: What you sacrifice or risk

Every decision involves trade-offs. Making them explicit prevents surprises.

---

## Trade-Off Categories

### Capability Trade-offs

| Get | Give Up |
|-----|---------|
| More features | Simplicity |
| Flexibility | Ease of use |
| Power | Learning curve |

### Quality Trade-offs

| Get | Give Up |
|-----|---------|
| Performance | Development time |
| Security | User convenience |
| Reliability | Cost |

### Resource Trade-offs

| Get | Give Up |
|-----|---------|
| Speed to market | Code quality |
| Cost savings | Capability |
| Team bandwidth | Scope |

### Strategic Trade-offs

| Get | Give Up |
|-----|---------|
| Vendor support | Lock-in |
| Standards compliance | Optimal solution |
| Future flexibility | Current simplicity |

---

## Trade-Off Documentation Pattern

For each significant trade-off:

```markdown
## Trade-Off: {name}

**What We Get**:
{benefit gained from this decision}

**What We Give Up**:
{cost or risk accepted}

**Why Acceptable**:
{why the benefit outweighs the cost}

**Mitigation** (if any):
{how we reduce the negative impact}
```

---

## Trade-Off Matrix

Document all trade-offs for a decision:

```markdown
## Trade-Offs for {decision}

| Trade-Off | Get | Give Up | Acceptable Because | Mitigation |
|-----------|-----|---------|-------------------|------------|
| T1 | {gain} | {cost} | {reason} | {action} |
| T2 | {gain} | {cost} | {reason} | {action} |
```

---

## Common Trade-Off Patterns

### Speed vs. Quality

```markdown
**Situation**: Deadline pressure
**Trade-Off**: Ship now with known issues

**Get**: Meet deadline, early user feedback
**Give Up**: Polish, edge case handling

**Acceptable If**:
- Issues are documented
- Quick fix path exists
- Users informed of limitations

**Mitigation**:
- Track as tech debt
- Schedule follow-up sprint
```

### Build vs. Buy

```markdown
**Situation**: Need new capability
**Trade-Off**: Use third-party solution

**Get**: Faster delivery, proven solution
**Give Up**: Control, customization, dependency

**Acceptable If**:
- Core competency not affected
- Exit strategy exists
- Cost is sustainable

**Mitigation**:
- Abstract behind interface
- Monitor vendor health
- Keep alternatives in view
```

### Scope vs. Schedule

```markdown
**Situation**: Can't fit everything
**Trade-Off**: Reduce scope to hit date

**Get**: Predictable delivery
**Give Up**: Features (specified which)

**Acceptable If**:
- Cut features are lowest priority
- Core value preserved
- Stakeholders aligned

**Mitigation**:
- Document deferred items
- Plan next phase
```

---

## Evaluating Trade-Off Acceptability

Questions to ask:

| Question | If No |
|----------|-------|
| Can we live with the downside? | Don't accept |
| Is the gain worth the cost? | Reconsider |
| Can we mitigate the cost? | Plan mitigation |
| Is this reversible? | More scrutiny |
| Are stakeholders aligned? | Communicate |

---

## Trade-Off Communication

When presenting trade-offs to stakeholders:

```markdown
## Recommendation: {decision}

### Key Trade-Offs

**We gain**:
- {benefit 1}
- {benefit 2}

**We accept**:
- {trade-off 1}: {what we give up}
- {trade-off 2}: {what we give up}

**Why this is the right choice**:
{summary of reasoning}

**What we're doing to mitigate downsides**:
- {mitigation 1}
- {mitigation 2}
```

---

## Trade-Off Review

Periodically review documented trade-offs:

| Review Question | Action if Changed |
|-----------------|-------------------|
| Is the gain still valuable? | Consider reversing |
| Is the cost still acceptable? | Address or mitigate |
| Has mitigation worked? | Document learning |
| Would we decide differently now? | Update decision record |
