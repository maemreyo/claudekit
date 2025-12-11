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
| `/plan-react` | React/Next.js feature planning | React components and features |
| `/plan-feature` | Feature development template | New functionality |
| `/plan-bugfix` | Bug fix template | Debugging and fixing issues |
| `/plan-refactor` | Refactoring template | Code improvements |
| `/plan-performance` | Performance optimization template | Speed improvements |

---

Create a detailed implementation plan for: **$ARGUMENTS**

## Workflow

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

**In Scope**
- [What will be done]

**Out of Scope**
- [What won't be done]

**Assumptions**
- [Key assumptions made]

---

### Tasks

#### Phase 1: Setup [Total: Xh]
| # | Task | Size | Est | Depends On | Testing |
|---|------|------|-----|------------|---------|
| 1 | Create data model | M | 45m | - | Unit |
| 2 | Set up database migration | S | 20m | 1 | - |
| 3 | Add model tests | M | 30m | 1 | Unit |

#### Phase 2: Core Implementation [Total: Xh]
| # | Task | Size | Est | Depends On | Testing |
|---|------|------|-----|------------|---------|
| 4 | Implement service layer | L | 90m | 1 | Unit |
| 5 | Add business logic | M | 45m | 4 | Unit |
| 6 | Write service tests | M | 40m | 5 | Unit |

#### Phase 3: Integration [Total: Xh]
| # | Task | Size | Est | Depends On | Testing |
|---|------|------|-----|------------|---------|
| 7 | Create API endpoints | M | 50m | 5 | API |
| 8 | Add validation | S | 25m | 7 | Unit |
| 9 | Write integration tests | M | 45m | 8 | Integration |
| 10 | Update documentation | S | 15m | 9 | - |

---

### Files to Create/Modify

**Create**
- `src/models/feature.ts` - Data model
- `src/services/feature-service.ts` - Business logic
- `src/api/routes/feature.ts` - API endpoints
- `tests/unit/feature.test.ts` - Unit tests
- `tests/integration/feature.test.ts` - Integration tests

**Modify**
- `src/api/routes/index.ts` - Register routes
- `docs/api.md` - API documentation

---

### Dependencies

**External**
- [Package X v1.2.3] - For [purpose]
- [Package Y v2.0.0] - For [purpose]

**Internal**
- Requires [existing feature] to be complete
- Uses [existing service]

---

### Risks

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| [Risk 1] | High | Medium | [How to mitigate] |
| [Risk 2] | Medium | Low | [How to mitigate] |

---

### Questions for Stakeholders
1. [Question about requirement]
2. [Question about edge case]
3. [Question about priority]

---

### Success Criteria
- [ ] All tasks completed
- [ ] Tests passing with 80%+ coverage
- [ ] API documentation updated
- [ ] Code reviewed and approved
- [ ] Performance meets requirements (if applicable)
- [ ] Accessibility requirements met (if applicable)
```

---

## Plan Execution Methodology

### Detailed Mode (Superpowers Methodology)

**For complex or critical tasks**, use `/plan-detailed` for superpowers-style plans:

```bash
/plan-detailed [task description]
```

**Reference**: `.claude/skills/methodology/writing-plans/SKILL.md`

#### Detailed Mode Features

When using detailed mode:
- **Bite-sized tasks**: 2-5 minutes each (vs standard 15-60 min)
- **Exact file paths**: Always include full paths
- **Context references**: Point to existing code to study
- **Implementation guidance**: Strategy and patterns, not exact code
- **TDD steps**: Write test → verify fail → implement → verify pass → commit
- **Execution-ready**: Optimized for `/execute-plan` with subagents

#### Detailed Task Template

```markdown
## Task [N]: [Task Name] (Est: Xm)

**File**: `path/to/file.ts`

**Context**:
- Related component: `path/to/similar-component.ts` (study this)
- Uses: [library] (already installed)
- Pattern: [description of pattern to follow]

**Acceptance Criteria**:
- [ ] Specific requirement 1
- [ ] Specific requirement 2
- [ ] Follows existing project patterns

**Implementation Strategy**:
1. Study referenced files
2. Key approach steps
3. What to verify

**Example Pattern** (reference, adapt to project):
```typescript
// Example showing APPROACH, not exact code
export function Component() {
  // Study similar components and adapt
}
```

**TDD Steps**:
1. Write failing test (2 min)
2. Verify test fails (npm test)
3. Implement minimally (3 min)
4. Verify test passes
5. Commit (git commit -m "...")
```

---

### Execution After Planning

Use `/execute-plan [plan-file]` for **subagent-driven execution with code review gates**.

**Reference**: `.claude/skills/methodology/executing-plans/SKILL.md`

#### Execution Methodology

1. **Fresh subagent per task group**
   - Dispatch subagent for 3-5 related tasks
   - Subagent reads plan context
   - Studies referenced files
   - Implements following TDD cycle
   - Returns completion summary

2. **Code review between groups**
   - Separate reviewer subagent
   - Reviews only current group's changes
   - Finds: Critical, Important, or Minor issues
   - Must fix Critical/Important before proceeding

3. **Quality gates**
   - No skip reviews
   - No proceed with critical issues
   - Sequential execution (no parallel)

**Example Flow**:
```
Plan → Task Group 1 → Review → [Fix if needed] → 
       Task Group 2 → Review → [Fix if needed] →
       Task Group 3 → Review → Final Review → Complete
```

**Benefits**:
- ✅ Fresh context per feature
- ✅ Quality gates prevent issues
- ✅ Easier to retry failed tasks
- ✅ Better code quality

---

## Estimation Guide

| Size | Time | Good For |
|------|------|----------|
| **XS** | <15m | Trivial changes, config tweaks |
| **S** | 15-30m | Simple feature, basic tests |
| **M** | 30-60m | Standard feature with tests |
| **L** | 1-2h | Complex logic, multiple files |
| **XL** | 2-4h | Major feature, needs research |

**Red Flags** 🚩:
- Task >4h → Break down further
- Multiple "and" in description → Multiple tasks
- "Maybe" or "possibly" → Unknown, add buffer

---

## Flags

| Flag | Description | Example |
|------|-------------|---------|
| `--save=[path]` | Save plan to file | `/plan --save=plans/auth.md "authentication"` |
| `--format=[fmt]` | Output format (markdown/json) | `/plan --format=json "api structure"` |
| `--depth=[1-5]` | Planning thoroughness (1=basic, 5=exhaustive) | `/plan --depth=5 "complex migration"` |

**Note**: For detailed TDD plans, use `/plan-detailed` or `/plan-react` instead

### Flag Usage Examples

```bash
# Save plan for later execution
/plan --save=plans/user-auth.md "implement user authentication"

# Then execute with subagents
/execute-plan plans/user-auth.md

# JSON output for tools
/plan --format=json "api endpoint structure"

# Exhaustive planning for complex work
/plan --depth=5 "database migration from MySQL to PostgreSQL"
```

---

## Choosing the Right Planning Command

| Scenario | Command | Why |
|----------|---------|-----|
| Need TDD micro-tasks | `/plan-detailed` | Subagent execution with review gates |
| React/Next.js feature | `/plan-react` | Component-driven, follows React patterns |
| Unclear requirements | `/plan-interactive` | AI asks questions first |
| Standard feature | `/plan-feature` | Template-based, faster |
| Bug fix | `/plan-bugfix` | Debugging workflow included |
| Performance issue | `/plan-performance` | Profiling and optimization steps |
| Tech research needed | Use `/research` first | Then plan with findings |
| Quick task (already clear) | `/plan` | Standard planning, no frills |

---

## Best Practices

### For Execution with Subagents

1. **Use detailed plans** (`/plan-detailed` or `/plan-react`)
   - Better for `/execute-plan` automation
   - Clear context and references
   - Guidance-based (AI must think)

2. **Include context references**
   - Point to similar existing code
   - Reference patterns to follow
   - Don't provide exact code (AI should adapt)

3. **Save plans to files**
   - Use `--save=path/to/plan.md`
   - Review before execution
   - Can iterate on plan

4. **Group related tasks**
   - 3-5 tasks per group (15-30 min total)
   - Same component or feature
   - Logical unit for review

### For Manual Execution

1. **Use standard plans** (`/plan`)
   - 15-60 min tasks
   - Less prescriptive
   - More flexibility

2. **Focus on dependencies**
   - Clear what blocks what
   - Parallel opportunities identified
   - Critical path highlighted

---

## Pro Tips

1. **Include testing time**: Tests often take 40-50% of development time
2. **Add buffer for unknowns**: +20-30% for unfamiliar tech
3. **Commit after each task**: Don't batch changes
4. **Update plan as you learn**: Plans are living documents
5. **Reference methodology**: Check `.claude/skills/methodology/` for patterns

---

## Related Skills & References

- `.claude/skills/methodology/writing-plans/SKILL.md` - Detailed planning methodology
- `.claude/skills/methodology/executing-plans/SKILL.md` - Execution with subagents
- `.claude/skills/methodology/code-review/SKILL.md` - Review process

---

## Variations

Customize planning behavior via your project's configuration:
- Task size definitions
- Required plan sections
- Estimation approach
- Risk assessment criteria
- Testing requirements