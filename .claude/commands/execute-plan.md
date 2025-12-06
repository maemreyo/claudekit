# /execute-plan - Subagent-Driven Plan Execution

## Purpose

Execute a detailed implementation plan using fresh subagents per task with mandatory code review gates between tasks.

## Usage

```
/execute-plan [plan-file-path]
```

## Arguments

- `$ARGUMENTS`: Path to the plan file (created with `/plan --detailed`)

---

Execute plan from: **$ARGUMENTS**

## Methodology

**Reference**: `.claude/skills/methodology/executing-plans/SKILL.md`

This command uses the superpowers execution methodology for quality-gated implementation.

## Core Pattern

**"Fresh subagent per task + review between tasks = high quality, fast iteration"**

### Why Fresh Agents?

- Prevents context pollution between tasks
- Each task gets focused attention
- Failures don't cascade
- Easier to retry individual tasks

### Why Code Review Between Tasks?

- Catches issues early
- Ensures code matches intent
- Prevents technical debt accumulation
- Creates natural checkpoints

## Workflow

### Step 1: Load & Parse Plan

1. **Read the plan file**
2. **Verify plan structure**:
   - Has clear task sections
   - Tasks have explicit file paths
   - Acceptance criteria present
   - Expected results defined
3. **Parse task list**:
   - Extract all tasks (support both table and ## Task X.Y formats)
   - Identify TDD micro-task cycles (Test → Implement → Enhance → Test → Commit)
   - Group related tasks (e.g., all tasks for one component)
4. **Create TodoWrite** with all parsed tasks
5. **Set first task to `in_progress`**

---

### Step 2: Detect Task Type

For each task, identify type:

| Pattern | Type | Execution Strategy |
|---------|------|-------------------|
| "Write Test", "Test -" | Test Task | Write test code, run, expect fail |
| "Implement", "Minimal" | Implementation | Write minimal code to pass test |
| "Enhance", "Refactor" | Enhancement | Improve code, keep tests passing |
| "Additional Tests" | Test Addition | Add more test cases |
| "Commit" | Commit | Git add + commit |
| "Create Types", "Data" | Foundation | No test needed, just implement |

---

### Step 3: Execute Task (TDD Cycle)

#### For "Write Test" Tasks

```markdown
1. Parse task for:
   - File path (from **File**: marker)
   - Test code (from **Test**: code block)
   - Expected result (from **Expected**: marker)

2. Create test file at specified path

3. Write test code from plan

4. Run test:
   ```bash
   npm test [test-file-path]
   ```

5. Verify test fails (as expected):
   - If fails as expected: ✅ Mark task complete
   - If passes unexpectedly: ⚠️ Warning, proceed
   - If error: Report error to user

6. Report:
   ```
  

 ✅ Task X.Y Complete: Test created and failing as expected
   File: src/components/Button.test.tsx
   ```
```

#### For "Implementation" Tasks

```markdown
1. Parse task for:
   - File path
   - Implementation code (from **Implementation**: block)
   - Acceptance Criteria checkboxes
   - Related test file

2. Create implementation file

3. Write code from plan (or improved version)

4. Run related test:
   ```bash
   npm test [related-test-file]
   ```

5. Verify test passes:
   - If passes: ✅ Mark complete
   - If fails: Attempt fix (max 2 retries)
   - If still fails: Pause, report to user

6. Report:
   ```
   ✅ Task X.Y Complete: Component implemented, tests passing
   File: src/components/Button.tsx
   Tests: ✅ All passing (3/3)
   ```
```

#### For "Enhancement" Tasks

```markdown
1. Read current implementation

2. Apply enhancements from plan:
   - Add structure, styling
   - Add accessibility
   - Add TypeScript types
   - Keep tests passing

3. Run all related tests

4. Verify no regressions:
   - If all pass: ✅ Complete
   - If some fail: Revert, try again
   - If persistent: Pause, report

5. Report:
   ```
   ✅ Task X.Y Complete: Component enhanced
   Changes: Added a11y, styling, types
   Tests: ✅ All passing (5/5)
   ```
```

#### For "Commit" Tasks

```markdown
1. Parse commit message from plan

2. Stage files:
   ```bash
   git add [files-from-plan]
   ```

3. Commit:
   ```bash
   git commit -m "[message-from-plan]"
   ```

4. Verify:
   - Clean working tree: ✅ Complete
   - Uncommitted changes: ⚠️ Warning

5. Report:
   ```
   ✅ Task X.Y Complete: Changes committed
   Files: 2 files committed
   Message: "feat(auth): add LoginForm component"
   ```
```

---

### Step 4: Code Review (Configurable)

**After each logical group** (e.g., all micro-tasks for one component):

```markdown
IF --review flag enabled:

  1. Dispatch code-reviewer subagent
  
  2. Review scope: Changes from recent tasks
  
  3. Reviewer checks:
     - Tests actually test the right things
     - Implementation follows plan
     - Accessibility present
     - TypeScript types correct
     - No obvious bugs
  
  4. Reviewer returns findings:
     - ✅ Looks good: Proceed
     - ⚠️ Minor issues: Note for later
     - 🔴 Critical issues: Pause, fix now

ELSE:
  Skip review, proceed to next task
```

**Note**: For TDD micro-tasks, review every 3-5 tasks (one complete component) instead of every task.

---

### Step 5: Handle Failures

#### Test Fails Unexpectedly

```markdown
1. Capture error output

2. Analyze failure:
   - Syntax error → Fix automatically
   - Logic error → Attempt fix
   - Environment issue → Report to user

3. Retry (max 2 attempts):
   - Attempt 1: Fix obvious issues
   - Attempt 2: Try alternative approach
   - Still failing: Pause execution

4. Report to user:
   ```
   ⚠️ Task X.Y Failed: Test not passing
   Error: Expected 'value' but got 'undefined'
   File: src/components/Button.test.tsx:15
   
   Attempted fixes: 2
   Suggestions:
   - Check if props are passed correctly
   - Verify component exports
   
   Options:
   1. Skip this task
   2. Manual fix (pause execution)
   3. Modify plan
   ```
```

#### File Path Issues

```markdown
1. If file path not found in plan:
   - Look for **File**: marker
   - Look for code block comments
   - Infer from task description
   - Ask user if still unclear

2. If directory doesn't exist:
   - Create parent directories
   - Log creation
   - Proceed

3. If file already exists (unexpected):
   - Ask user: Overwrite or skip?
   - If critical file: Always ask
   - If test file: Can overwrite
```

---

### Step 6: Progress Tracking

Track and report progress continuously:

```markdown
## Execution Progress

### Phase 1: Foundation & Types ✅ COMPLETE
- Task 1.1: Create Types ✅ (10min)
- Task 1.2: Write Test - API Client ✅ (3min)
- Task 1.3: Implement - API Client ✅ (8min)
- Task 1.4: Write Test - useFeature Hook ✅ (3min)
- Task 1.5: Implement - useFeature Hook ✅ (10min)
- Task 1.6: Commit Foundation ✅ (1min)
**Phase Total**: 35min

### Phase 2: Presentational Components 🔄 IN PROGRESS (60% - 18/30 tasks)
- Task 2.1: Write Test - FeatureItem ✅ (3min)
- Task 2.2: Implement - FeatureItem ✅ (4min)
- Task 2.3: Enhance - FeatureItem ✅ (4min)
- Task 2.4: Additional Tests - FeatureItem ✅ (3min)
- Task 2.5: Commit - FeatureItem ✅ (1min)
- Task 2.6: Write Test - Button ▶️ IN PROGRESS
- Task 2.7-2.30: Pending

### Overall Progress
**Completed**: 21/89 tasks (24%)
**Time Spent**: 1h 15min / est. 4h total
**Tests**: 18 passing, 0 failing
**Commits**: 6
```

---

### Step 7: Final Verification

After all tasks complete:

```markdown
1. Run comprehensive test suite:
   ```bash
   npm test -- --coverage
   ```

2. Verify coverage meets goals (from plan)

3. Check all acceptance criteria from plan

4. Verify all commits made

5. Run linter:
   ```bash
   npm run lint
   ```

6. Generate summary report

7. Suggest next steps:
   - Run `/review code` for deeper review
   - Run `/ship` to create PR
   - Runn `/ execute-plan [next-phase]` if phased approach
```

## Critical Rules

### Never Skip Code Reviews

Every task must be reviewed before proceeding. No exceptions.

### Never Proceed with Critical Issues

Critical issues must be fixed:
```
implement → review → fix critical → re-review → proceed
```

### Never Run Parallel Implementation

Tasks run sequentially:
```
WRONG: Run Task 1, 2, 3 simultaneously
RIGHT: Task 1 → Review → Task 2 → Review → Task 3 → Review
```

### Always Read Plan Before Implementing

```
WRONG: Start coding based on memory of plan
RIGHT: Read plan file, extract task details, then implement
```

## Error Handling

### Task Fails

1. Capture error details
2. Attempt fix (max 2 retries)
3. If still failing, pause execution
4. Report to user with:
   - Which task failed
   - Error details
   - Suggested resolution
5. Wait for user decision

### Review Finds Major Issues

1. List all Critical/Important issues
2. Dispatch fix subagent for each
3. Re-run code review
4. If issues persist after 2 cycles:
   - Pause execution
   - Report to user
   - May need plan revision

## Output

### Progress Updates

```markdown
## Execution Progress

### Task 1: Create User model ✓
- Files modified: src/models/user.ts
- Tests added: 3
- Review: Passed

### Task 2: Add validation ✓
- Files modified: src/models/user.ts
- Tests added: 2
- Review: Passed (1 minor deferred)

### Task 3: Create endpoint [IN PROGRESS]
- Status: Implementing...
```

### Completion Summary

```markdown
## Execution Complete

### Summary
- Tasks completed: 8/8
- Tests added: 24
- Coverage: 92%

### Files Created
- src/models/user.ts
- src/services/user-service.ts
- src/routes/user.ts

### Files Modified
- src/routes/index.ts
- src/types/index.ts

### Deferred Items
- Minor: Variable rename in user-service.ts line 12

### Next Steps
- Run full test suite
- Use /ship to create PR
```

## Prerequisites

Before using this command:

1. Plan file exists and is complete
2. Plan was created with `/plan --detailed`
3. Plan has been reviewed and approved
4. Tests can be run (`npm test` or `pytest`)

## Related Commands

- `/plan --detailed` - Create detailed plan
- `/brainstorm` - Design before planning
- `/ship` - Create PR after execution
