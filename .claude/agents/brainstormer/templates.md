# Brainstorming Output Templates

This file contains reusable templates for formatting brainstorming outputs.

---

## Template 1: Brainstorm Session

Use this template for comprehensive brainstorming sessions with multiple ideas.

```markdown
## Brainstorm: [Topic]

### Challenge
[Clear problem statement in 1-2 sentences]

### Constraints
- [Technical constraint 1]
- [Business constraint 2]
- [Resource constraint 3]

### Context
[Brief background from Step 0 context gathering]
- Files analyzed: [list]
- Key findings: [summary]

---

### Ideas Generated

#### Idea 1: [Descriptive Name]
**Description**: [2-3 sentence explanation of the approach]

**Pros**: 
- [Benefit 1]
- [Benefit 2]
- [Benefit 3]

**Cons**: 
- [Drawback 1]
- [Drawback 2]

**Effort**: [Low/Medium/High] ([time estimate])

**Example**:
```[language]
// Brief code snippet or diagram
```

#### Idea 2: [Descriptive Name]
**Description**: [2-3 sentence explanation]

**Pros**: 
- [Benefit 1]
- [Benefit 2]

**Cons**: 
- [Drawback 1]
- [Drawback 2]

**Effort**: [Low/Medium/High] ([time estimate])

**Example**:
```[language]
// Brief code snippet or diagram
```

#### Idea 3: [Descriptive Name]
[Follow same format as above]

#### Idea 4: [Descriptive Name]
[Follow same format as above]

#### Idea 5: [Descriptive Name]
[Follow same format as above]

---

### Wild Card Ideas
💡 **[Unconventional Idea 1]**: [Brief description of creative/risky approach]

💡 **[Unconventional Idea 2]**: [Another outside-the-box idea]

---

### Comparison Matrix

| Criteria | Idea 1 | Idea 2 | Idea 3 | Idea 4 | Idea 5 |
|----------|--------|--------|--------|--------|--------|
| **Feasibility** (1-5) | 4 | 5 | 3 | 4 | 2 |
| **Impact** (1-5) | 5 | 3 | 5 | 4 | 5 |
| **Effort** (1-5, lower better) | 3 | 5 | 2 | 3 | 4 |
| **Risk** (1-5, lower better) | 4 | 5 | 2 | 3 | 1 |
| **Team Fit** (1-5) | 4 | 5 | 4 | 3 | 2 |
| **TOTAL** | 20 | 23 | 16 | 17 | 14 |

**Scoring Guide**:
- Feasibility: 5=can start today, 1=requires major dependencies
- Impact: 5=solves problem completely, 1=minor improvement
- Effort: 5=very low effort, 1=very high effort (inverted scale)
- Risk: 5=very low risk, 1=very high risk (inverted scale)
- Team Fit: 5=matches current skills, 1=requires new expertise

---

### Recommendation

**🎯 Recommended Approach: [Name of top-scoring or most strategic idea]**

**Rationale**:
[2-3 paragraphs explaining why this is the best choice given constraints, team context, and business goals]

**Alternative**: If [specific condition], consider [2nd choice] instead because [reason].

---

### Validation Artifact

[Pseudo-code, interface definition, or architecture diagram showing the recommended approach is implementable]

```[language]
// Concrete code example showing key implementation details
// This proves the idea is feasible and provides starting point
```

---

### Next Steps

**Immediate** (This week):
1. [Action item 1]
2. [Action item 2]

**Short-term** (Next sprint):
1. [Action item 3]
2. [Action item 4]

**Long-term** (Next quarter):
1. [Action item 5]

**Success Metrics**:
- [Measurable outcome 1]
- [Measurable outcome 2]
```

---

## Template 2: Alternative Approaches

Use this template when comparing alternatives to an existing solution.

```markdown
## Alternatives Analysis: [Problem/Feature]

### Current Approach

**Description**: [How it currently works]

**Pain Points**:
- [Issue 1]
- [Issue 2]
- [Issue 3]

**What's Working**:
- [Positive aspect 1]
- [Positive aspect 2]

---

### Alternative 1: [Approach Name]

**Description**: 
[2-3 paragraph explanation of this alternative approach]

**How it Works**:
```[language]
// Code example showing implementation
```

**Trade-offs**:
- ✅ **Advantages**:
  - [Pro 1]
  - [Pro 2]
  - [Pro 3]
- ❌ **Disadvantages**:
  - [Con 1]
  - [Con 2]

**Migration Path**: [If switching from current approach, how to migrate]

**When to Use**: 
- [Scenario 1]
- [Scenario 2]

**Real-world Examples**:
- [Company/Project using this approach]

---

### Alternative 2: [Approach Name]

**Description**: 
[2-3 paragraph explanation]

**How it Works**:
```[language]
// Code example
```

**Trade-offs**:
- ✅ **Advantages**:
  - [Pro 1]
  - [Pro 2]
- ❌ **Disadvantages**:
  - [Con 1]
  - [Con 2]

**Migration Path**: [Migration strategy]

**When to Use**: 
- [Scenario 1]
- [Scenario 2]

**Real-world Examples**:
- [Company/Project using this]

---

### Alternative 3: [Approach Name]

[Follow same structure as Alternative 1 & 2]

---

### Decision Matrix

| Factor | Weight | Current | Alt 1 | Alt 2 | Alt 3 |
|--------|--------|---------|-------|-------|-------|
| Performance | 30% | 3 | 5 | 4 | 5 |
| Maintainability | 25% | 2 | 4 | 5 | 3 |
| Cost | 20% | 4 | 3 | 4 | 2 |
| Team Familiarity | 15% | 5 | 3 | 4 | 2 |
| Scalability | 10% | 3 | 5 | 4 | 5 |
| **Weighted Total** | | **3.15** | **4.05** | **4.25** | **3.65** |

---

### Decision Guide

**Choose [Alternative 1] when**:
- [Condition 1]
- [Condition 2]
- [Condition 3]

**Choose [Alternative 2] when**:
- [Condition 1]
- [Condition 2]

**Choose [Alternative 3] when**:
- [Condition 1]
- [Condition 2]

**Stick with Current Approach when**:
- [Condition 1]
- [Condition 2]

---

### Recommendation

**For your specific case**: [Recommended alternative]

**Because**:
1. [Reason 1 based on your constraints]
2. [Reason 2 based on your context]
3. [Reason 3 based on your goals]

**Implementation Approach**: [High-level plan]

---

### Proof of Concept

[Minimal code example proving the recommendation works]

```[language]
// Working prototype or interface definition
// Demonstrates feasibility and provides confidence
```

**Expected Outcomes**:
- [Metric 1]: [Before] → [After]
- [Metric 2]: [Before] → [After]
```

---

## Quick Mode Template

For simple/urgent requests, use this condensed format:

```markdown
## Quick Ideas: [Problem]

### Option 1: [Name]
- **What**: [One sentence]
- **Pros**: [Brief]
- **Cons**: [Brief]
- **Effort**: [Time estimate]

### Option 2: [Name]
- **What**: [One sentence]
- **Pros**: [Brief]
- **Cons**: [Brief]
- **Effort**: [Time estimate]

### Option 3: [Name]
- **What**: [One sentence]
- **Pros**: [Brief]
- **Cons**: [Brief]
- **Effort**: [Time estimate]

**Recommend**: [Option X] because [one sentence rationale]
```
