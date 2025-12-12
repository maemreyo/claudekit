# /cook - Execute Implementation Plans

## Purpose

Execute implementation plans with intelligent task management, verification gates, and real developer workflow. Transforms detailed plans (from `/plan`) into working code through structured, verifiable execution.

**"Cook like a real developer, not a script"** - Sequential execution with context, verification, and recovery.

## Aliases

```bash
/cook [plan-file]
/execute-plan [plan-file]
/build-from-plan [plan-file]
```

## Usage

```bash
# Basic execution (run all tasks sequentially)
/cook plans/multi-theme-implementation.md

# Execute specific phase only
/cook plans/feature.md --phase=A

# Execute specific tasks
/cook plans/feature.md --task=A.1,B.3

# Interactive mode (approve each task)
/cook plans/feature.md --interactive

# Resume from checkpoint
/cook plans/feature.md --resume

# Dry run (preview without changes)
/cook plans/feature.md --dry-run

# With auto-fix on errors
/cook plans/feature.md --auto-fix --max-retries=3

# Parallel execution for independent tasks
/cook plans/feature.md --parallel --workers=3
```

## Arguments

- `$ARGUMENTS`: Path to implementation plan markdown file (from `/plan` command)

## Flags

| Flag | Description | Example |
|------|-------------|---------|
| `--phase=PHASES` | Execute specific phases only | `--phase=A,E` |
| `--task=TASKS` | Execute specific tasks only | `--task=A.1,B.3` |
| `--interactive` or `-i` | Pause for approval after each task | `--interactive` |
| `--dry-run` | Preview execution without changes | `--dry-run` |
| `--auto-fix` | Auto-retry on test/build failures | `--auto-fix` |
| `--max-retries=N` | Max retry attempts (default: 2) | `--max-retries=3` |
| `--resume` | Resume from last checkpoint | `--resume` |
| `--parallel` | Execute independent tasks in parallel | `--parallel` |
| `--workers=N` | Number of parallel workers (default: 2) | `--workers=3` |
| `--checkpoint` | Save progress after each task | `--checkpoint` |
| `--rollback-on-error` | Auto-rollback phase on failure | `--rollback-on-error` |
| `--skip-tests` | Skip verification tests (not recommended) | `--skip-tests` |
| `--verbose` | Detailed execution logs | `--verbose` |

---

## Core Philosophy

### Why Cook Needs Intelligence

1. **Context Matters**: Reading current code before modifying
2. **Verification is Critical**: Every task must prove it works
3. **Errors Happen**: Smart recovery > blind execution
4. **Progress Tracking**: Resume capability for long plans
5. **Real Developer Flow**: Think → Code → Test → Fix → Commit

### Execution Loop (Per Task)

```
┌─────────────────────────────────────────────┐
│ 1. READ: Parse task from plan              │
│ 2. CONTEXTUALIZE: Read existing code       │
│ 3. IMPLEMENT: Write/modify code            │
│ 4. VERIFY: Run tests/build                 │
│ 5. FIX: Auto-retry if verification fails   │
│ 6. COMMIT: Mark task done, git commit      │
│ 7. CHECKPOINT: Save progress               │
└─────────────────────────────────────────────┘
         ↓ (Next task)
```

---

## Agent Workflow (4 Phases)

### Phase 0: Plan Analysis & Setup (5-10 min)

**Agent**: Plan Analyzer (`code-reviewer` with analysis focus)

**Goal**: Parse plan, build execution roadmap, validate readiness

```markdown
Plan Analyzer Agent

GOAL: Parse implementation plan and create execution strategy

INPUT:
- Plan file: $ARGUMENTS
- Flags: {{ all flags }}
- Workspace: {{ current directory }}

---

SUBTASK 0.1: Parse Plan Structure (5 min)

**Steps**:
1. Read plan markdown file
2. Extract all phases (A, B, C, etc.)
3. For each phase, extract tasks with:
   - Task ID (e.g., A.1, B.2)
   - Description
   - File paths involved
   - Estimated time
   - Dependencies
   - Verification steps

**Detection patterns**:
```markdown
### A.1: Task Name [XYZ]
- **File**: `path/to/file.ts`
- **Action**: What to do
- **Depends on**: (if any)
- **Verify**: How to test
```

**Output**: Task graph with dependencies

---

SUBTASK 0.2: Validate Readiness (3 min)

**Steps**:
1. Check all file paths are valid
2. Verify dependencies installed (package.json)
3. Check git status (warn if uncommitted changes)
4. Validate test commands exist
5. Check for conflicting tasks

**Output**: Readiness report

---

SUBTASK 0.3: Build Execution Plan (2 min)

**Steps**:
{{ if --phase provided }}
1. Filter tasks to specified phases only
{{ else if --task provided }}
1. Filter to specified tasks only
{{ else if --resume }}
1. Find last completed task (marked [x])
2. Start from next uncompleted task
{{ else }}
1. Include all tasks in dependency order
{{ endif }}

2. Group independent tasks (for --parallel if specified)
3. Calculate total estimated time
4. Identify critical path

**Output**: Execution roadmap

---

OUTPUT FORMAT:

```markdown
# Execution Roadmap

## Plan: {{ plan filename }}
- Total Phases: X
- Total Tasks: Y
- Estimated Time: Z hours
- Mode: {{ Sequential | Parallel | Interactive }}

## Task Breakdown

### Phase A: {{ phase name }} ({{ X tasks }})
- A.1: {{ description }} [{{ time }}] {{ status }}
- A.2: {{ description }} [{{ time }}] {{ status }} (deps: A.1)

### Phase B: {{ phase name }} ({{ Y tasks }})
...

## Execution Order
1. A.1 → A.2 → A.3
2. B.1, B.2 (parallel) → B.3
3. C.1 → ...

## Risks Identified
- ⚠️ Task B.3 modifies critical file
- ⚠️ Phase D has no verification steps

## Ready to Execute
{{ if --dry-run }}
DRY RUN MODE: No changes will be made
{{ else }}
{{ warnings if any }}
{{ endif }}
```
```

---

{{ if --dry-run }}
**END EXECUTION** (Dry run complete)
{{ else }}
→ Proceed to Phase 1
{{ endif }}

---

### Phase 1: Task Execution Engine (Main Loop)

**Agent**: Task Executor (dedicated subagent per task OR phase)

**Goal**: Execute tasks sequentially with proper context and verification

**Key Insight**: Based on user feedback, **group related tasks into larger subtasks** to warrant dedicated subagents.

---

#### Execution Strategy

{{ if task size < 30 min AND related tasks exist }}
**GROUPED EXECUTION**: Combine related tasks into single subagent
{{ else }}
**INDIVIDUAL EXECUTION**: One subagent per task
{{ endif }}

---

#### For Each Task (or Task Group)

**Dispatch Task Executor Subagent**:

```markdown
Task Executor - {{ Task ID(s) }}

GOAL: Implement {{ task description }}

INPUT:
- Plan section: {{ task details from plan }}
- Current code: {{ files that will be modified }}
- Dependencies: {{ completed tasks }}
- Verification command: {{ from plan }}

CONTEXT WINDOW:
{{ read existing files if MODIFY operation }}
{{ read related files for understanding }}

---

SUBTASK 1.1: Contextualize (5-10 min for MODIFY, skip for CREATE)

{{ if task involves MODIFY }}
**Steps**:
1. Read target file: `{{ file path }}`
2. Understand current implementation:
   - Identify patterns used
   - Note existing logic
   - Find integration points
3. Read related files for context
4. Identify potential conflicts

**Output**: Context summary
{{ else }}
**Skip** (Creating new file, no context needed)
{{ endif }}

---

SUBTASK 1.2: Implement Changes ({{ estimate from plan }})

**Steps**:
1. {{ if CREATE }}
   - Create new file: `{{ file path }}`
   - Write initial structure
   {{ else if MODIFY }}
   - Open file: `{{ file path }}`
   - Locate modification point
   - Apply changes carefully (preserve existing logic)
   {{ else if DELETE }}
   - Remove file: `{{ file path }}`
   - Update imports in dependent files
   {{ endif }}

2. Follow plan specifications:
   {{ paste relevant code from plan if provided }}

3. Ensure code quality:
   - Proper TypeScript types
   - Consistent formatting
   - Add comments for complex logic
   - Update imports/exports

**Output**: Modified code files

---

SUBTASK 1.3: Verification ({{ 5-15 min }})

**Critical**: This step determines if task succeeded

**Steps**:
1. Run verification command from plan:
   ```bash
   {{ verification command from plan, e.g., "npm test theme-security" }}
   ```

2. Check for errors:
   - Compilation errors (TypeScript, ESLint)
   - Test failures
   - Runtime errors

3. Capture output

**OUTPUT**:
{{ if verification passes }}
✅ **VERIFICATION PASSED**
- All tests green
- No compilation errors
- Ready to mark complete
{{ else }}
❌ **VERIFICATION FAILED**
- Error details: {{ error log }}
→ Proceed to AUTO-FIX
{{ endif }}

---

{{ if verification failed and --auto-fix }}
SUBTASK 1.4: Auto-Fix (10-20 min)

**Retry Logic**:

Attempt 1:
1. Read error message carefully
2. Identify root cause
3. Apply targeted fix
4. Re-run verification

{{ if still fails and retries < --max-retries }}
Attempt 2:
1. Broaden fix scope
2. Check for missing dependencies
3. Re-run verification
{{ endif }}

{{ if still fails }}
❌ **AUTO-FIX FAILED**
- Manual intervention required
- Preserve partial changes
- Log issue for review
→ STOP EXECUTION (unless --continue-on-error)
{{ endif }}

{{ endif }}

---

SUBTASK 1.5: Commit & Mark (2-3 min)

{{ if verification passed }}
**Steps**:
1. Git commit:
   ```bash
   git add {{ modified files }}
   git commit -m "{{ task type }}: {{ task description }} ({{ task ID }})"
   ```

2. Update plan file:
   - Change `- [ ] {{ task ID }}` to `- [x] {{ task ID }}`
   - Add completion timestamp

3. {{ if --checkpoint }}
   Save checkpoint: progress.json
   {{ endif }}

**Output**: Task marked complete
{{ endif }}

---

OUTPUT FORMAT:

```markdown
## Task {{ task ID }}: {{ description }}

**Status**: {{ ✅ Complete | ❌ Failed | ⏸️ Paused }}
**Time**: {{ actual time taken }}
**Files Modified**: {{ list }}

### Implementation Summary
- {{ what was done }}

### Verification
- Command: {{ verification command }}
- Result: {{ PASS | FAIL }}
{{ if failed }}
- Error: {{ error message }}
{{ endif }}

### Git Commit
- Hash: {{ commit hash }}
- Message: "{{ commit message }}"

---

{{ if --interactive }}
⏸️ **PAUSED FOR REVIEW**

Review changes:
- Files: {{ list }}
- Tests: {{ test results }}

Continue? (y/n)
{{ endif }}
```
```

---

**Loop Control**:
- If task succeeds → Next task
- If task fails and --auto-fix → Retry
- If retry exhausted → Stop (or continue if --continue-on-error)
- If --interactive → Wait for approval

---

### Phase 2: Verification Gates (Per Phase)

**Agent**: Verification Agent (`code-reviewer` with test focus)

**Goal**: Comprehensive verification after each phase completes

**Trigger**: After all tasks in a phase are marked [x]

```markdown
Verification Agent - Phase {{ phase ID }} Complete

GOAL: Verify all Phase {{ phase}} objectives met

INPUT:
- Completed tasks: {{ list from phase }}
- Modified files: {{ aggregate list }}
- Plan objectives: {{ from plan markdown }}

---

SUBTASK 2.1: Run Phase Tests (10-20 min)

**Steps**:
1. Identify test suites for this phase
2. Run comprehensive tests:
   ```bash
   {{ if phase A (Security) }}
   npm run test:security
   npm run lint:security
   {{ else if phase B (Architecture) }}
   npm run test:unit
   npm run test:integration
   npm run build
   {{ else if phase C (Components) }}
   npm run test:components
   npm run test:e2e
   {{ else if phase D (Performance) }}
   npm run test:performance
   npm run build:analyze
   {{ else if phase E (Docs) }}
   npm run test:coverage
   npm run docs:build
   {{ endif }}
   ```

3. Collect results

**Output**: Test report

---

SUBTASK 2.2: Phase Objectives Check (5 min)

**Steps**:
1. Read phase objectives from plan
2. Verify each objective met:

Example for Phase A (Security):
- [ ] All security tests passing?
- [ ] No vulnerabilities in code scan?
- [ ] Input validation working?
- [ ] CSS sanitization active?

**Output**: Checklist status

---

SUBTASK 2.3: Quality Gates (5 min)

**Steps**:
1. Code quality checks:
   - TypeScript compilation: ✅/❌
   - ESLint: ✅/❌
   - Test coverage: {{ percentage }}
   - Bundle size: {{ size }} ({{ within limit? }})

2. Breaking changes check:
   - Existing tests still pass?
   - API compatibility maintained?

**Output**: Quality report

---

OUTPUT FORMAT:

```markdown
# Phase {{ phase ID }} Verification

**Status**: {{ ✅ PASSED | ❌ FAILED }}

## Test Results
- Unit tests: {{ X/Y passed }}
- Integration tests: {{ X/Y passed }}
- E2E tests: {{ X/Y passed }}
- Security scans: {{ clean | issues found }}

## Objectives Met
{{ checklist from plan }}

## Quality Metrics
- TypeScript: {{ ✅ No errors }}
- ESLint: {{ ✅ Clean }}
- Coverage: {{ XX% }}
- Bundle size: {{ XYZ KB }} {{ within limit }}

## Issues Found
{{ if any }}
- Issue 1: {{ description }}
  → Fix required before next phase
{{ else }}
No issues found
{{ endif }}

---

{{ if passed }}
✅ **PHASE {{ phase ID }} COMPLETE**
→ Proceed to Phase {{ next phase }}
{{ else }}
❌ **PHASE VERIFICATION FAILED**
→ {{ if --rollback-on-error }}Rolling back phase changes{{ else }}Manual fix required{{ endif }}
{{ endif }}
```
```

---

### Phase 3: Progress Tracking & Reporting

**Agent**: Progress Tracker (lightweight, inline)

**Goal**: Real-time status updates and checkpoint management

**Runs**: After each task completion

```markdown
Progress Tracker

TASKS:

1. **Update Progress Bar**:
   ```
   Progress: ████████░░░░ 60% (12/20 tasks)
   Current: Phase B - Task B.3
   Elapsed: 4h 25m
   ETA: 3h 10m remaining
   ```

2. **Save Checkpoint** (if --checkpoint):
   ```json
   {
     "planFile": "multi-theme-implementation.md",
     "lastCompletedTask": "B.3",
     "completedPhases": ["A"],
     "currentPhase": "B",
     "timestamp": "2025-12-12T16:45:00Z",
     "timeElapsed": "4h 25m",
     "commits": ["abc123", "def456"]
   }
   ```

3. **TodoWrite Integration**:
   - Auto-update task.md if exists
   - Sync completion status

4. **Display Summary**:
   ```markdown
   🍳 COOKING: Multi-Theme System
   
   ✅ Phase A: Security Fixes (10 tasks) - 100%
   🔄 Phase B: Architecture (4/6 tasks) - 67%
      ✅ B.1 ThemeManager
      ✅ B.2 CSS Structure  
      ✅ B.3 Theme Provider
      ⏳ B.4 Update Components (In Progress)
      ⏸️ B.5 Add Dark Mode
      ⏸️ B.6 Migrate Legacy
   ⏸️ Phase C: Components (0/8 tasks)
   ⏸️ Phase D: Performance (0/5 tasks)
   ⏸️ Phase E: Documentation (0/4 tasks)
   
   Last Checkpoint: 2m ago
   Next: B.4 - Update Components (Est: 45m)
   ```
```

---

### Phase 4: Error Handling & Recovery

**Agent**: Error Handler (inline, reactive)

**Goal**: Intelligent error recovery and rollback

**Triggers**: On any task failure

```markdown
Error Handler

INPUT:
- Failed task: {{ task ID }}
- Error type: {{ compilation | test failure | runtime }}
- Error message: {{ full error log }}
- Retry count: {{ current / max }}

---

DECISION TREE:

{{ if error == "missing dependency" }}
STRATEGY: Auto-install
- Run: npm install {{ missing package }}
- Retry task
{{ else if error == "TypeScript compilation" }}
STRATEGY: {{ if --auto-fix }}Fix types{{ else }}Report{{ endif }}
- Read error carefully
- Identify type mismatch
- Apply minimal fix
- Retry compilation
{{ else if error == "test failure" }}
STRATEGY: {{ if retries < max }}Retry{{ else }}Report{{ endif }}
- Capture test output
- Identify assertion failure
- {{ if --auto-fix }}Attempt fix{{ else }}Log for manual review{{ endif }}
{{ else if error == "runtime error" }}
STRATEGY: Stop and report
- Cannot auto-fix runtime errors
- Preserve state
- Request manual intervention
{{ endif }}

---

ROLLBACK LOGIC:

{{ if --rollback-on-error }}
**Phase Rollback**:
1. Identify commits in current phase
2. Git soft reset to phase start
3. Preserve error log
4. Update plan: mark phase as failed
5. Report rollback complete
{{ else }}
**Preserve State**:
1. Leave partial changes
2. Mark failed task in plan
3. Save error log
4. Prompt for manual fix or skip
{{ endif }}

---

OUTPUT:

```markdown
❌ ERROR in Task {{ task ID }}

**Error Type**: {{ type }}
**Message**: {{ error message }}

{{ if auto-fix attempted }}
**Auto-Fix Attempts**: {{ count }}
**Result**: {{ success | failed }}
{{ endif }}

{{ if rollback executed }}
**Rollback**: Phase {{ phase ID }} changes reverted
**Status**: Ready for manual fix
{{ else }}
**Preserved State**: Partial changes saved
**Next Steps**:
1. Review error: {{ log file }}
2. Fix manually or run: /cook {{ plan }} --resume --skip={{ task ID }}
{{ endif }}
```
```

---

## Output & Reporting

### Real-Time Console Output

```markdown
🍳 COOK STARTED: Multi-Theme System Implementation

Plan: plans/multi-theme-implementation.md
Phases: 5 (A-E)
Tasks: 33 total
Estimated: 40-60 hours
Mode: Sequential {{ if --interactive }}+ Interactive{{ endif }}

═══════════════════════════════════════════════

📋 Phase A: Security Fixes (Week 1) - CRITICAL

├─ A.1: Implement CSS Sanitization [5h]
│  ├─ Creating src/lib/theme-security.ts... ✅
│  ├─ Installing dompurify... ✅
│  ├─ Writing sanitization logic... ✅
│  ├─ Running tests... ✅ (12/12 passed)
│  └─ Commit: abc1234 "feat: implement CSS sanitization (A.1)"
│  ✅ Complete (4h 15m)
│
├─ A.2: Add Input Validation [3h]
│  ├─ Creating validation.ts... ✅
│  ├─ Implementing Zod schemas... ✅
│  ├─ Running tests... ❌ FAILED
│  │  Error: Type mismatch in ThemeInput
│  ├─ Auto-fix attempt 1... ✅
│  ├─ Re-running tests... ✅ (8/8 passed)
│  └─ Commit: def5678 "feat: add input validation (A.2)"
│  ✅ Complete (3h 25m)
│
└─ A.3: Update Theme Application [2h]
   ⏳ In Progress...
   
Progress: ████████░░░░░░ 40% (13/33 tasks)
Elapsed: 12h 35m | ETA: 18h 20m
Last Checkpoint: 1m ago

═══════════════════════════════════════════════
```

### Final Summary

```markdown
✅ COOK COMPLETE: Multi-Theme System

**Duration**: 42h 15m (Est: 40-60h)
**Tasks Completed**: 33/33 (100%)
**Phases**: 5/5 (100%)
**Tests**: All passing (328/328)
**Coverage**: 94.2%

═══════════════════════════════════════════════

## Phase Summary

✅ **Phase A: Security Fixes** (10h 15m)
- 3 tasks completed
- 0 errors
- 24 tests added, all passing

✅ **Phase B: Architecture Migration** (14h 30m)
- 6 tasks completed
- 2 auto-fixes applied
- Performance improved 35%

✅ **Phase C: Component Updates** (8h 20m)
- 8 tasks completed
- 45 components migrated
- UI consistency validated

✅ **Phase D: Performance Optimization** (6h 10m)
- 5 tasks completed
- FOUC eliminated
- Bundle size reduced 12%

✅ **Phase E: Testing & Documentation** (3h 00m)
- 4 tasks completed
- 94.2% test coverage
- Docs published

═══════════════════════════════════════════════

## Git Commits

33 commits created:
abc1234 - abc1235 - abc1236 ... xyz9999

View full history:
git log --oneline --grep="(A\|B\|C\|D\|E)\.[0-9]"

═══════════════════════════════════════════════

## Verification Results

✅ All security tests passing
✅ No vulnerabilities detected
✅ Performance benchmarks met
✅ 94.2% test coverage
✅ Documentation complete
✅ No breaking changes

═══════════════════════════════════════════════

🎉 **READY FOR PRODUCTION**

Next steps:
1. Review changes: git diff main
2. Run full QA: npm run test:all
3. Deploy to staging
4. Monitor metrics

═══════════════════════════════════════════════
```

---

## Integration Points

### 1. Git Integration

```bash
# Auto-commit after each task
git add <files>
git commit -m "feat: <description> (<task-id>)"

# Tag after each phase
git tag "phase-a-complete"

# Rollback support
git reset --soft <phase-start-commit>
```

### 2. TodoWrite Integration

```markdown
# Auto-sync with task.md if exists
- [x] A.1: Implement CSS Sanitization
- [x] A.2: Add Input Validation
- [ ] A.3: Update Theme Application
```

### 3. Testing Integration

```bash
# Auto-detect test commands
- npm test
- npm run test:unit
- npm run test:security
- npm run lint
- npm run build
```

### 4. CI/CD Hooks (Optional)

```bash
# Before cook
pre-cook: npm install, git pull

# After each phase
post-phase: notify-slack, deploy-staging

# After cook
post-cook: run-e2e, create-pr
```

---

## Examples

### Example 1: Basic Full Execution

```bash
/cook plans/multi-theme-implementation.md
```

**Behavior**:
- Parses all 5 phases (A-E)
- Executes 33 tasks sequentially
- Runs verification after each phase
- Auto-commits each task
- Shows real-time progress
- Final summary at end

---

### Example 2: Critical Security Phase Only

```bash
/cook plans/multi-theme.md --phase=A --interactive
```

**Behavior**:
- Only executes Phase A (Security Fixes)
- Pauses after each task for manual review
- Shows diff and test results
- Waits for user approval to continue

---

### Example 3: Resume After Interruption

```bash
# First run (interrupted at B.3)
/cook plans/feature.md --checkpoint

# Resume later
/cook plans/feature.md --resume
```

**Behavior**:
- Reads progress.json checkpoint
- Finds last completed task (B.3)
- Starts from B.4
- Continues to completion

---

### Example 4: Parallel Execution

```bash
/cook plans/feature.md --parallel --workers=3
```

**Behavior**:
- Analyzes task dependencies
- Identifies independent tasks
- Runs up to 3 tasks simultaneously
- Syncs at phase boundaries

---

### Example 5: Auto-Fix Enabled

```bash
/cook plans/refactor.md --auto-fix --max-retries=3
```

**Behavior**:
- On test failure: reads error
- Attempts intelligent fix
- Retries up to 3 times
- If still fails: stops and reports

---

## Success Criteria

- [ ] Can parse any plan from `/plan` command
- [ ] Executes tasks in correct dependency order
- [ ] Runs verification after each task
- [ ] Handles errors gracefully with auto-fix
- [ ] Saves progress for resume capability
- [ ] Integrates with Git (commits per task)
- [ ] Shows real-time progress updates
- [ ] Supports interactive approval mode
- [ ] Can rollback on phase failure
- [ ] Generates comprehensive final report

---

## Related Commands

- `/plan` - Create implementation plans to cook
- `/how` - Generate current-state docs before planning
- `/test` - Run specific test suites
- `/git` - Git operations and history

---

## Pro Tips

1. **Always use --checkpoint for long plans**:
   ```bash
   /cook long-plan.md --checkpoint --auto-fix
   ```

2. **Use --interactive for critical changes**:
   ```bash
   /cook security-fix.md --phase=A --interactive
   ```

3. **Combine with /plan for full workflow**:
   ```bash
   /how "feature" --output=docs/feature/
   /plan "new-feature" --current=docs/feature/ --output=plans/feature.md --tdd
   /cook plans/feature.md --auto-fix --checkpoint
   ```

4. **Use --dry-run to preview before execution**:
   ```bash
   /cook risky-refactor.md --dry-run
   ```

5. **Enable auto-fix for routine tasks**:
   ```bash
   /cook routine-update.md --auto-fix --max-retries=2
   ```

---

**Remember**: Good cooking requires good ingredients. Use quality plans from `/plan` for best results! 🍳✨
