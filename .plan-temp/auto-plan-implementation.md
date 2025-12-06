# Implementation Plan: /auto-plan Command

## Goal

Create a new `/auto-plan` command that **always** runs the full workflow: explore → analyze → plan, using subagent methodology for each phase.

**Key Difference from existing commands**:
- `/plan-react --auto-explore`: Optional exploration
- `/plan-migration`: Migration-focused
- **`/auto-plan`**: ALWAYS explores, works for ANY feature type, uses subagents

---

## User Review Required

> [!IMPORTANT]
> **Command Design Decision**
> 
> Name: `/auto-plan` (alternative: `/plan-smart`, `/intelligent-plan`)
> - Short, clear, implies automation
> - Works for React, API, utilities, any code type
> - No flags needed for basic usage (explore is automatic)

> [!IMPORTANT]
> **Subagent Methodology**
> 
> This command will use 3 subagents sequentially:
> 1. **Explorer Subagent**: Find and explore old code
> 2. **Analyzer Subagent**: Extract requirements and patterns
> 3. **Planner Subagent**: Generate detailed plan with context
> 
> Each subagent has fresh context, with review gates between phases.

---

## Proposed Changes

### New File

#### [NEW] [auto-plan.md](file:///Users/trung.ngo/Documents/zaob-dev/claudekit/.claude/commands/auto-plan.md)

**Purpose**: Intelligent planning command that always explores before planning

**Structure**:
```markdown
# /auto-plan - Intelligent Planning with Auto-Exploration

## Core Principle
ALWAYS explore existing code → analyze patterns → generate plan

## Subagent Methodology
Phase 1: Explorer Subagent (find & document)
Phase 2: Analyzer Subagent (extract requirements)
Phase 3: Planner Subagent (create detailed plan)

## Usage
/auto-plan "feature to implement/migrate"
/auto-plan "authentication" --source=old-app/auth
/auto-plan "payment flow" --output=plans/payment.md
```

**Key Sections**:
1. **Purpose & Philosophy**: Why explore-first matters
2. **Subagent Workflow**: 3-phase execution with fresh agents
3. **Usage Examples**: Simple to complex scenarios
4. **Flags**: Minimal (source, output, analysis, type hints)
5. **Output Format**: What plan looks like
6. **Integration**: How to execute plans

---

### Implementation Details

#### Phase 1: Explorer Subagent

**Dispatch**: Fresh subagent for exploration

```markdown
Subagent Task:
1. **Find Code**:
   - Search query: $FEATURE_NAME
   - Locations: Auto-detect or use --source
   - Scope: Components, hooks, utils, APIs, tests

2. **Deep Exploration**:
   - Run /explore-codebase equivalent
   - Capture:
     - Business logic
     - Data models
     - API integrations
     - Validation rules
     - Edge cases
     - Test coverage
     - Dependencies

3. **Return**:
   - Exploration report
   - Key findings summary
   - Files analyzed list
```

**Output Example**:
```markdown
Explorer Subagent Report:

Found: src/features/salary-calculator/
Files: 8 components, 3 hooks, 2 utils, 1 API client

Key Findings:
✅ Tax calculation: 7 progressive brackets
✅ Insurance: Employee 10.5%, Employer 21.5%
✅ Regions: 4 zones with minimums
✅ Validation: 1M-1B VND range
⚠️  Tests: Only 23% coverage
⚠️  Large components: >200 lines

Business Logic:
- calculateTax(): Progressive tax algorithm
- calculateInsurance(): Fixed percentage rates
- regionalMinimum(): Zone-based validation

Dependencies:
- react-hook-form: Form handling
- zod: Validation
- zustand: State management
```

#### Phase 2: Analyzer Subagent

**Dispatch**: Fresh subagent for analysis (receives Explorer report)

```markdown
Subagent Task:
1. **Extract Requirements**:
   - Business rules from code
   - Validation constraints
   - Edge cases
   - Expected behaviors

2. **Pattern Analysis**:
   - Component architecture
   - State management
   - Data flow
   - Error handling
   - Testing approach

3. **Identify Preservation vs Improvement**:
   - MUST PRESERVE: Critical business logic
   - CAN IMPROVE: Technical patterns
   - SHOULD ADD: Missing tests, validations

4. **Return**:
   - Structured requirements
   - Pattern summary
   - Migration strategy
```

**Output Example**:
```markdown
Analyzer Subagent Report:

Requirements Extracted:

1. Tax Calculation (CRITICAL):
   - 7 brackets: 5%, 10%, 15%, 20%, 25%, 30%, 35%
   - Progressive algorithm
   - Formula: (lines 65-120 in old code)
   - MUST NOT CHANGE

2. Insurance Deductions:
   - Employee: SI 8%, HI 1.5%, UI 1%
   - Employer: SI 17.5%, HI 3%, UI 1%
   - Base: Insurance salary (configurable)
   - MUST PRESERVE RATES

3. Allowances:
   - Self: 11,000,000 VND
   - Dependent: 4,400,000 VND each
   - LEGAL REQUIREMENT

Patterns Found:
- State: Zustand store
- Forms: react-hook-form + zod
- API: POST to backend for calculation
- UI: Breakdown display with details

Improvements Needed:
- Add comprehensive tests (current: 23%)
- Split large components
- Add TypeScript strict types
- Improve error handling
```

#### Phase 3: Planner Subagent

**Dispatch**: Fresh subagent for planning (receives Analysis report)

```markdown
Subagent Task:
1. **Generate Plan Structure**:
   - Phases based on analysis
   - Task breakdown (2-5 min micro-tasks)
   - Dependency ordering

2. **Create Tasks**:
   For each task:
   - File path (explicit)
   - Context (reference to old code)
   - Strategy (not exact code)
   - Acceptance criteria
   - Test expectations
   - PRESERVE notes (critical logic)

3. **Add Metadata**:
   - Time estimates
   - Risk assessment
   - Dependencies
   - References to old code

4. **Return**:
   - Complete execution-ready plan
   - Ready for /execute-plan
```

**Output Example**:
```markdown
Planner Subagent Report:

Generated Plan: Salary Calculator Migration
Phases: 6
Tasks: 42 micro-tasks
Estimate: 6-8 hours
Risk: Medium

Sample Task:

### Task 2.3: Tax Calculation Function (30m)

**File**: `src/lib/salary/calculate-tax.ts`

**Context**:
- Old implementation: old-app/salary/calculator.ts (lines 65-120)
- CRITICAL: 7-bracket progressive algorithm
- Must return identical results

**MUST PRESERVE**:
```typescript
// Tax bracket rates (LEGAL REQUIREMENT - DO NOT CHANGE)
const BRACKETS = [
  { max: 5_000_000, rate: 0.05 },
  { max: 10_000_000, rate: 0.10 },
  // ... (exact values from old code)
];
```

**Strategy**:
1. Study old calculateTax function
2. Write test cases (use old system's outputs)
3. Implement progressive algorithm
4. Verify against test cases

**Acceptance Criteria**:
- [ ] All 7 brackets implemented
- [ ] Progressive calculation correct
- [ ] Matches old system output
- [ ] Edge cases handled

**Expected**: ✅ Tests pass, outputs match old system

---

Plan saved to: plans/salary-migration.md
Ready for: /execute-plan plans/salary-migration.md
```

---

### Quality Gates Between Phases

**After Explorer Subagent**:
```markdown
Review Exploration:
✅ Sufficient code coverage?
✅ Business logic captured?
✅ Dependencies identified?
❌ Missing critical files? → Re-explore

Decision: Proceed to Analysis or Re-explore
```

**After Analyzer Subagent**:
```markdown
Review Analysis:
✅ Requirements clear?
✅ Patterns identified?
✅ Preservation items noted?
❌ Ambiguous requirements? → Clarify with user

Decision: Proceed to Planning or Clarify
```

**After Planner Subagent**:
```markdown
Review Plan:
✅ Complete task breakdown?
✅ References to old code present?
✅ PRESERVE notes included?
✅ Ready for execution?
❌ Missing context? → Revise plan

Decision: Save Plan or Revise
```

---

## Command Structure

### Workflow Diagram

```
User: /auto-plan "salary calculator"
  ↓
┌─────────────────────────────────────┐
│ Phase 1: EXPLORE (Subagent 1)      │
│ - Find old code                     │
│ - Deep analysis                     │
│ - Document findings                 │
│ Returns: Exploration Report         │
└────────────┬────────────────────────┘
             │
    ┌────────▼────────┐
    │ Review Gate     │
    │ Complete? Y/N   │
    └────────┬────────┘
             │ YES
             ▼
┌─────────────────────────────────────┐
│ Phase 2: ANALYZE (Subagent 2)      │
│ - Extract requirements              │
│ - Identify patterns                 │
│ - Note preservation items           │
│ Returns: Analysis Report            │
└────────────┬────────────────────────┘
             │
    ┌────────▼────────┐
    │ Review Gate     │
    │ Clear? Y/N      │
    └────────┬────────┘
             │ YES
             ▼
┌─────────────────────────────────────┐
│ Phase 3: PLAN (Subagent 3)         │
│ - Generate task breakdown          │
│ - Add context references            │
│ - Include PRESERVE notes            │
│ Returns: Complete Plan              │
└────────────┬────────────────────────┘
             │
    ┌────────▼────────┐
    │ Final Review    │
    │ Ready? Y/N      │
    └────────┬────────┘
             │ YES
             ▼
    Save Plan to File
             │
             ▼
    Display Summary
             │
             ▼
    Ready for /execute-plan! ✅
```

### Subagent Benefits

1. **Fresh Context per Phase**:
   - Explorer doesn't carry analysis bias
   - Analyzer gets clean exploration data
   - Planner focuses only on planning

2. **Specialized Attention**:
   - Each subagent optimized for its task
   - No context pollution
   - Better quality per phase

3. **Review Gates**:
   - Check quality before proceeding
   - Catch issues early
   - Can retry individual phases

4. **Easier Debugging**:
   - Know which phase failed
   - Can re-run specific phase
   - Clear separation of concerns

---

## Flags

| Flag | Description | Example |
|------|-------------|---------|
| `--source=path` | Specify source directory | `--source=old-app/auth` |
| `--output=path` | Save plan to file | `--output=plans/auth.md` |
| `--analysis=path` | Save analysis to file | `--analysis=docs/auth-analysis.md` |
| `--type=TYPE` | Hint: react/api/utility | `--type=react` |
| `--save-exploration` | Save exploration report | `--save-exploration` |

**Note**: Exploration is ALWAYS ON, no flag needed to enable it.

---

## Usage Examples

### Example 1: Simple Smart Planning

```bash
/auto-plan "authentication feature"

# Automatic workflow:
# 1. Explorer: Finds old auth code
# 2. Analyzer: Extracts auth requirements
# 3. Planner: Creates migration plan
# 4. Output: Displays plan in chat
```

### Example 2: With File Outputs

```bash
/auto-plan "payment processing" \
  --output=plans/payment-migration.md \
  --analysis=docs/payment-analysis.md

# Saves:
# - Analysis: docs/payment-analysis.md
# - Plan: plans/payment-migration.md
```

### Example 3: Specific Source

```bash
/auto-plan "user dashboard" \
  --source=legacy/dashboard \
  --type=react \
  --output=plans/dashboard.md
```

---

## Verification Plan

### Automated Tests

1. **Test Subagent Dispatch**:
   ```bash
   # Verify 3 subagents dispatched
   test: /auto-plan "simple feature"
   expect: 3 subagent calls (explore, analyze, plan)
   ```

2. **Test Review Gates**:
   ```bash
   # Verify gates between phases
   test: Mock incomplete exploration
   expect: Re-explore triggered
   ```

3. **Test Output Files**:
   ```bash
   # Verify file creation
   test: /auto-plan "feature" --output=test.md --analysis=analysis.md
   expect: Both files created
   ```

### Manual Verification

1. **Compare with Manual Workflow**:
   ```bash
   # Manual:
   /explore "feature" --output=a.md
   /plan-react --context=a.md --output=p.md
   
   # Auto:
   /auto-plan "feature" --output=p2.md
   
   # Verify: p2.md has same quality/completeness as p.md
   ```

2. **Verify Preservation**:
   - Check plan includes "MUST PRESERVE" notes
   - Verify references to old code
   - Confirm critical logic documented

3. **Execute Plan**:
   ```bash
   /auto-plan "feature" --output=plan.md
   /execute-plan plan.md
   
   # Verify: Execution works correctly
   ```

---

## Success Criteria

- [x] Command created: `/auto-plan.md`
- [ ] Always runs explore → analyze → plan
- [ ] Uses 3 fresh subagents (one per phase)
- [ ] Review gates between phases
- [ ] Quality output comparable to manual workflow
- [ ] Saves to files when flags provided
- [ ] Ready for `/execute-plan`
- [ ] Clear user feedback during execution
- [ ] Error handling for each phase

---

## Related Commands

- `/explore-codebase`: Manual exploration only
- `/plan-react`: Plan with optional context
- `/plan-migration`: Migration-specific auto-planning
- **`/auto-plan`**: Universal auto-planning (this command)

---

## Key Advantages

| Feature | /explore + /plan | /plan-react --auto-explore | **/auto-plan** |
|---------|------------------|----------------------------|----------------|
| **Auto-explore** | ❌ Manual | ✅ Optional | ✅ Always |
| **Subagents** | ❌ No | ⚠️ Partial | ✅ Full (3 phases) |
| **Review gates** | ❌ No | ⚠️ Partial | ✅ Yes (between phases) |
| **Universal** | ✅ Yes | ⚠️ React-focused | ✅ Yes (any code) |
| **Simplicity** | ❌ 2 commands | ✅ 1 command | ✅ 1 command |
| **Quality** | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
