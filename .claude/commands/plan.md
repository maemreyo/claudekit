# /plan - Task Planning Command

## Purpose

Create structured implementation plans with task breakdown, dependencies, and time estimates for complex work.

## Usage

```bash
/plan [task description]
```

## Arguments

- `$ARGUMENTS`: Description of the task, feature, or work to plan

## Related Commands

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `/plan-detailed` | TDD micro-tasks (2-5 min each) | Learning, complex tasks, need hand-holding |
| `/plan-interactive` | AI asks clarifying questions first | Unclear requirements, exploration |
| `/plan-feature` | Feature development template | New functionality |
| `/plan-bugfix` | Bug fix template | Debugging and fixing issues |
| `/plan-refactor` | Refactoring template | Code improvements |
| `/plan-migration` | Data/system migration template | Database or system migrations |
| `/plan-performance` | Performance optimization template | Speed improvements |

---

## Workflow

Create a detailed implementation plan for: **$ARGUMENTS**

### Phase 1: Understanding

1. **Parse Requirements**
   - Identify core functionality needed
   - List explicit and implicit requirements
   - Identify acceptance criteria
   - Estimate complexity (Simple/Medium/Complex)

2. **Clarify Ambiguities**
   - Ask questions if unclear
   - List assumptions made
   - Note dependencies on decisions

### Phase 2: Research

1. **Explore Codebase**
   - Find related implementations
   - Identify patterns to follow
   - Locate integration points
   - Check for TODO/FIXME comments related to task

2. **Technical Research** (if needed)
   - Research unfamiliar technologies
   - Check security considerations
   - Review performance implications

### Phase 3: Task Breakdown

1. **Decompose Work**
   - Break into atomic tasks (15-60 minutes each)
   - Clear completion criteria for each task
   - Add "Definition of Done" per task

2. **Order by Dependencies**
   - What blocks what
   - Identify parallel opportunities
   - Critical path identification

3. **Add Estimates**
   - XS: <15 min (trivial changes)
   - S: 15-30 min (simple, basic tests)
   - M: 30-60 min (feature with tests)
   - L: 1-2 hours (complex logic)
   - XL: 2-4 hours (major feature, needs research)
   - Add confidence level (High/Medium/Low)

### Phase 4: Documentation

Generate the plan following the output format below.

---

## Output Format

```markdown
## 🎯 Plan: [Feature/Task Name]

**Estimate**: X-Y hours | **Risk**: [Low/Medium/High] | **Confidence**: [High/Medium/Low]

### Summary
[2-3 sentence overview of what will be built and why it matters]

**Impact**: [What problem this solves or value it adds]

### Scope

**In Scope** ✅
- [What will be done]

**Out of Scope** ❌
- [What won't be done]

**Assumptions** 💭
- [Key assumptions made]

---

### Tasks

#### Phase 1: Foundation [Total: Xh] 🏗️
| # | Task | Size | Est | Confidence | Depends On | Parallel? |
|---|------|------|-----|------------|------------|-----------|
| 1 | [Task description] | M | 45m | High | - | ✅ |
| 2 | [Task description] | S | 20m | High | 1 | ❌ |

**Definition of Done**:
- [ ] [Criteria 1]
- [ ] [Criteria 2]

#### Phase 2: Core Implementation [Total: Xh] ⚙️
| # | Task | Size | Est | Confidence | Depends On | Parallel? |
|---|------|------|-----|------------|------------|-----------|
| 3 | [Task description] | L | 1.5h | Medium | 2 | ❌ |

**Definition of Done**:
- [ ] [Criteria]

#### Phase 3: Integration & Polish [Total: Xh] ✨
| # | Task | Size | Est | Confidence | Depends On | Parallel? |
|---|------|------|-----|------------|------------|-----------|
| 4 | [Task description] | M | 45m | Medium | 3 | ❌ |

**Definition of Done**:
- [ ] [Criteria]

---

### 📁 Files to Create/Modify

**Create** ✨
- `path/to/new-file.ts` - Description

**Modify** 📝
- `path/to/existing-file.ts` - Description

---

### ⚠️ Risks & Mitigations

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| [Risk description] | High | Low | [Mitigation strategy] |

**Rollback Plan**:
1. [Step to rollback if needed]

---

### 🧪 Testing Strategy

**Unit Tests** (Target: 80%+ coverage)
- [What to test]

**Integration Tests**
- [What to test]

**Manual Testing Checklist**
- [ ] Happy path works
- [ ] Error handling works

---

### ✅ Success Criteria

- [ ] All tasks completed
- [ ] Tests passing with ≥80% coverage
- [ ] No critical bugs

---

### 🚀 Ready to Execute?

Run `/execute-plan` to start with AI assistance, or `/start-task #1` to begin manually.
```

---

## Estimation Guide

| Size | Time | Characteristics |
|------|------|-----------------|
| **XS** | <15min | Config change, trivial fix |
| **S** | 15-30min | Simple function, basic test |
| **M** | 30-60min | Feature with tests |
| **L** | 1-2h | Complex logic, extensive testing |
| **XL** | 2-4h | Major feature, needs research |

**Red Flags** 🚩:
- Task >4h → Break down further
- Multiple "and" in description → Multiple tasks
- "Maybe" or "possibly" → Unknown, add buffer

---

## Pro Tips

1. **Include testing time**: Tests often take 40-50% of development time
2. **Add buffer for unknowns**: +20-30% for unfamiliar tech
3. **Commit after each task**: Don't batch changes
4. **Update plan as you learn**: Plans are living documents

---

## Related Commands

```bash
/execute-plan        # Execute plan with AI assistance
/plan-status         # Check current progress
/start-task #N       # Start specific task
```