# /cook - Pragmatic Task Iterator (V2)

## Purpose

Execute the **next uncompleted task** from an implementation plan, verify it works, and stop for developer review. Acts as a focused iterator - one task per invocation.

**"One task at a time, solid execution"** - Stateless, atomic, developer-controlled.

## Aliases

```bash
/cook [plan-file]
/cook-next [plan-file]
```

## Usage

```bash
# Execute next uncompleted task
/cook plans/multi-theme.md

# Execute next task in specific phase
/cook plans/feature.md --phase=A

# Dry run (preview without changes)
/cook plans/feature.md --dry-run

# Skip verification (fast mode, use carefully)
/cook plans/feature.md --skip-verify

# Allow auto-fix on test failures
/cook plans/feature.md --auto-fix
```

## Arguments

- `$ARGUMENTS`: Path to implementation plan markdown file

## Flags

| Flag | Description | Example |
|------|-------------|---------|
| `--phase=ID` | Limit to specific phase | `--phase=A` |
| `--dry-run` | Preview without executing | `--dry-run` |
| `--skip-verify` | Skip test verification | `--skip-verify` |
| `--auto-fix` | Auto-retry once on test failure | `--auto-fix` |
| `--no-update` | Don't update plan checkbox | `--no-update` |

---

## Core Philosophy

### Iterator Pattern

**Not a loop, but a single step**:
- Each `/cook` = Exactly 1 task
- Reads plan → Finds first `[ ]` → Executes → Stops
- Developer reviews → Commits → Runs `/cook` again

### Stateless Design

**No hidden state**:
- Source of truth: Markdown plan file (`[ ]` vs `[x]`)
- No progress.json, no memory files
- Each invocation reads fresh from plan

### Developer Control

**Human in the loop**:
- AI implements + tests
- Developer reviews (`git diff`)
- Developer commits (approval)
- `/cook` updates plan `[x]` automatically (unless --no-update)
- Developer runs next iteration

---

## Workflow (4 Phases)

### Phase 1: Discovery (2-5 min)

```markdown
Task Scanner

GOAL: Find next task to execute

STEPS:

1. Read plan file: $ARGUMENTS

2. Parse markdown for tasks:
   - Pattern: `- [ ] [Task ID]: [Description]`
   - Example: `- [ ] A.1: Implement CSS Sanitization`

3. Find next uncompleted task:
   {{ if --phase provided }}
   - Search within Phase {{ phase ID }} section only
   - Find first `- [ ]` in that phase
   {{ else }}
   - Find first `- [ ]` in entire plan
   {{ endif }}

4. {{ if no uncompleted task found }}
   🎉 **PLAN COMPLETE**
   All tasks are marked [x]!
   
   Summary:
   - Total tasks: {{ count }}
   - All completed ✅
   
   Next steps:
   - Review final code
   - Run full test suite
   - Deploy to staging
   
   EXIT
   {{ endif }}

5. Extract task details:
   ```markdown
   ### A.1: Task Name [Estimate]
   - **File**: `path/to/file.ts`
   - **Action**: What to do
   - **Depends on**: (optional)
   - **Verify**: npm test command
   ```

6. Parse:
   - Task ID (e.g., A.1)
   - Description
   - File path(s)
   - Operation: CREATE | MODIFY | DELETE
   - Verification command
   - Dependencies

7. {{ if task has dependencies }}
   Validate dependencies:
   - Check if dependency tasks marked [x]
   - {{ if any incomplete }}
     ⚠️ ERROR: Dependencies not met
     - Task {{ dep ID }}: Still [ ] (incomplete)
     
     Recommend:
     /cook {{ plan file }} --phase={{ dep phase }}
     
     STOP
   {{ endif }}
   {{ endif }}

OUTPUT: Task object with all details
```

---

### Phase 2: Contextualization (5-10 min for MODIFY, skip for CREATE)

{{ if not --dry-run }}

```markdown
Context Loader

GOAL: Understand current code before changes

{{ if operation is MODIFY }}

STEPS:

1. Read target file: `{{ file path }}`

2. Analyze current implementation:
   - File structure
   - Existing functions/classes
   - Patterns used
   - Dependencies (imports)

3. Identify modification points:
   - Where to add new code
   - What to preserve
   - Potential conflicts

4. Create modification strategy:
   - Specific lines to change
   - New code to add
   - Logic to preserve

OUTPUT: Context summary + modification plan

{{ else if operation is CREATE }}

STEPS:

1. Identify directory structure
2. Determine file template (component, utility, type, etc.)
3. Gather necessary imports

OUTPUT: Creation plan

{{ else if operation is DELETE }}

STEPS:

1. Find files that depend on target
2. List cleanup required

OUTPUT: Deletion checklist

{{ endif }}
```

{{ else }}

DRY RUN: Skip context loading

{{ endif }}

---

### Phase 3: Implementation (10-45 min depending on task)

{{ if not --dry-run }}

```markdown
Code Implementer

GOAL: Execute task specification

STEP 3.1: Generate Changes

{{ if CREATE }}

**Creating new file**: `{{ file path }}`

1. Generate file content based on:
   - Task description
   - Project patterns
   - Code examples from plan (if provided)

2. Add proper structure:
   - Imports
   - Types (TypeScript)
   - Main implementation
   - Exports
   - Comments

3. Follow project conventions:
   - Formatting
   - Naming
   - Style

{{ else if MODIFY }}

**Modifying file**: `{{ file path }}`

1. Read current content

2. Apply changes per task description:
   - Add new functions/logic
   - Update existing code
   - Preserve critical sections

3. Update:
   - Imports (if needed)
   - Types (if needed)
   - Exports (if needed)

{{ else if DELETE }}

**Deleting file**: `{{ file path }}`

1. Remove target file

2. Clean up references:
   - Update files that imported it
   - Remove from index files
   - Update tests

{{ endif }}

STEP 3.2: Apply Changes

1. Write changes to file system

2. Verify syntax:
   {{ if TypeScript }}
   - Run: npx tsc --noEmit {{ file path }}
   {{ else if JavaScript }}
   - Run: node --check {{ file path }}
   {{ endif }}

3. {{ if syntax error }}
   Fix syntax immediately and retry
   {{ endif }}

OUTPUT: Files modified/created/deleted
```

{{ else }}

DRY RUN: Show what would be implemented

OUTPUT: Implementation preview

{{ endif }}

---

### Phase 4: Verification & Handoff (5-20 min)

{{ if not --dry-run and not --skip-verify }}

```markdown
Verifier & Reporter

GOAL: Test implementation and prepare handoff

STEP 4.1: Run Tests

1. Extract verification command from task:
   - Example: `npm test theme-security`

2. Execute command:
   ```bash
   {{ verification command }}
   ```

3. Capture results:
   - Exit code (0 = pass)
   - Test output
   - Coverage (if applicable)
   - Errors (if any)

4. Parse results:
   - Tests passed: {{ X/Y }}
   - Duration: {{ time }}
   - Status: {{ PASS | FAIL }}

STEP 4.2: Handle Failures

{{ if tests failed }}

Analyze error:
- Type: {{ assertion | syntax | runtime }}
- Location: {{ file:line }}
- Message: {{ error text }}

{{ if --auto-fix }}
AUTO-FIX ATTEMPT:

1. Read error carefully
2. Identify likely cause
3. Apply targeted fix
4. Re-run verification

{{ if still fails }}
Report failure (can't auto-fix)
{{ else }}
Continue (fixed!)
{{ endif }}

{{ else }}
Report failure and stop
{{ endif }}

{{ endif }}

STEP 4.3: Update Plan (if tests passed and not --no-update)

{{ if tests passed and not --no-update }}

1. Read plan file

2. Find task line:
   - Pattern: `- [ ] {{ task ID }}:`

3. Update to complete:
   - Replace: `- [ ]` → `- [x]`

4. Write updated plan

OUTPUT: Plan file updated with [x]

{{ endif }}
```

{{ else if --skip-verify }}

Skip verification (--skip-verify flag)

{{ else }}

DRY RUN: Skip verification

{{ endif }}

---

## Output Format

### Success Case ✅

```markdown
═══════════════════════════════════════════════
🍳 COOK COMPLETE

**Task**: {{ task ID }}: {{ description }}
**Phase**: {{ phase ID }}
**Duration**: {{ time taken }}

═══════════════════════════════════════════════

## Changes Made

{{ if CREATE }}
✅ Created: `{{ file path }}` ({{ lines }} lines)
{{ else if MODIFY }}
✅ Modified: `{{ file path }}`
   - {{ added }} lines added
   - {{ modified }} lines modified
   - {{ deleted }} lines removed
{{ else if DELETE }}
✅ Deleted: `{{ file path }}`
✅ Cleaned up {{ X }} dependent files
{{ endif }}

═══════════════════════════════════════════════

## Verification

{{ if not --skip-verify }}
**Command**: `{{ verification command }}`
**Result**: ✅ PASS ({{ X/Y }} tests)
**Coverage**: {{ coverage% }}
**Duration**: {{ test time }}
{{ else }}
⚠️ Verification skipped (--skip-verify)
{{ endif }}

═══════════════════════════════════════════════

## Plan Updated

{{ if not --no-update }}
✅ Marked complete in plan:
   `- [x] {{ task ID }}: {{ description }}`
{{ else }}
⏸️ Plan not updated (--no-update flag)
Manual update required
{{ endif }}

═══════════════════════════════════════════════

## Next Steps

**1. Review changes**:
```bash
git diff
```

**2. If satisfied, commit**:
```bash
git add .
git commit -m "{{ commit message suggestion }}"
```

**3. Continue to next task**:
```bash
/cook {{ plan file }}
```

{{ if next task preview }}
**Next task**: {{ next task ID }}: {{ next description }}
{{ else }}
🎉 **This was the last task!** Plan complete.
{{ endif }}

═══════════════════════════════════════════════
```

---

### Failure Case ❌

```markdown
═══════════════════════════════════════════════
❌ TASK FAILED

**Task**: {{ task ID }}: {{ description }}
**Phase**: {{ phase ID }}

═══════════════════════════════════════════════

## Changes Made

{{ changes summary }}

═══════════════════════════════════════════════

## Verification FAILED

**Command**: `{{ verification command }}`
**Exit Code**: {{ non-zero }}

**Errors**:
```
{{ error output }}
```

**Failed Tests**:
- {{ test 1 name }}: {{ assertion failure }}
- {{ test 2 name }}: {{ error message }}

═══════════════════════════════════════════════

{{ if --auto-fix }}
## Auto-Fix Attempted

Attempted fix:
- {{ what was tried }}

Result: ❌ Still failing

{{ endif }}

═══════════════════════════════════════════════

## Diagnosis

**Error Type**: {{ TypeScript | Test Assertion | Runtime }}
**Location**: `{{ file }}:{{ line }}`
**Cause**: {{ likely cause }}

**Suggested Fix**:
```typescript
{{ suggested code change }}
```

═══════════════════════════════════════════════

## Recovery Options

**Option 1: Fix manually**
1. Review error above
2. Edit: `{{ file path }}`
3. Re-run: `/cook {{ plan file }}`
   (Will retry same task)

**Option 2: Skip this task**
1. Mark task as [x] in plan (manual)
2. Add TODO comment about issue
3. Run: `/cook {{ plan file }}`
   (Will move to next task)

**Option 3: Rollback**
```bash
git checkout {{ files modified }}
```

═══════════════════════════════════════════════

⚠️ Plan NOT updated (task still [ ])
Task will retry on next `/cook` run

═══════════════════════════════════════════════
```

---

### Dry Run Case 🔍

```markdown
═══════════════════════════════════════════════
🔍 DRY RUN: Next Task Preview

**Task**: {{ task ID }}: {{ description }}
**Phase**: {{ phase ID }}
**Estimate**: {{ time estimate }}

═══════════════════════════════════════════════

## Files Involved

**Operation**: {{ CREATE | MODIFY | DELETE }}
**Target**: `{{ file path }}`

{{ if MODIFY }}
### Current State
- File exists: {{ size }}
- Key functions: {{ list }}

### Planned Changes
- {{ change 1 }}
- {{ change 2 }}
{{ endif }}

═══════════════════════════════════════════════

## Implementation Plan

{{ step-by-step plan }}

═══════════════════════════════════════════════

## Verification

**Command**: `{{ verification command }}`
**Expected**: {{ tests should pass }}

═══════════════════════════════════════════════

## To Execute

Remove --dry-run flag:
```bash
/cook {{ plan file }}
```

═══════════════════════════════════════════════
```

---

## Examples

### Example 1: Basic Usage

```bash
/cook plans/multi-theme.md
```

**What happens**:
1. Finds first `[ ]` task (e.g., A.1)
2. Implements code
3. Runs tests
4. Updates plan: `[ ]` → `[x]`
5. Shows git diff suggestion
6. Stops

Developer then: `git commit` → `/cook` again

---

### Example 2: Preview Mode

```bash
/cook plans/feature.md --dry-run
```

**What happens**:
1. Shows what task would be executed
2. Previews changes
3. No files modified
4. Plan not updated

---

### Example 3: Phase-Specific

```bash
/cook plans/feature.md --phase=A
```

**What happens**:
1. Finds first `[ ]` in Phase A
2. Executes that task
3. Stops (even if Phase A has more tasks)

Run again to do next Phase A task.

---

### Example 4: Fast Mode (Skip Tests)

```bash
/cook plans/docs.md --skip-verify
```

⚠️ **Use only for low-risk tasks** (e.g., documentation)

---

### Example 5: Auto-Fix on Failure

```bash
/cook plans/refactor.md --auto-fix
```

If tests fail, attempts one auto-fix before giving up.

---

## Typical Developer Workflow

### Iteration Loop

```bash
# Iteration 1
/cook plan.md           # AI: implements A.1, tests ✅, marks [x]
git diff                # Dev: reviews changes
git commit -m "..."     # Dev: approves

# Iteration 2
/cook plan.md           # AI: implements A.2, tests ✅, marks [x]
git diff                # Dev: reviews
git commit -m "..."     # Dev: approves

# Iteration 3
/cook plan.md           # AI: implements A.3, tests ❌
# Dev: reads error, fixes manually
/cook plan.md           # AI: retries A.3, tests ✅, marks [x]
git commit -m "..."     # Dev: commits fix + implementation

# ... repeat until plan complete
```

---

## Integration

### With /plan Command

```bash
# Create plan
/plan "multi-theme" --current=docs/theme/ --tdd --output=plans/theme.md

# Execute plan
/cook plans/theme.md
# (repeat until done)
```

### With Git Workflow

```bash
# Feature branch
git checkout -b feature/multi-theme

# Cook tasks
/cook plans/theme.md
git commit -m "feat: implement CSS sanitization (A.1)"

/cook plans/theme.md
git commit -m "feat: add input validation (A.2)"

# ... continue ...

# Push when phase complete
git push origin feature/multi-theme
```

### With CI/CD

```bash
# In CI pipeline (for automated testing)
/cook test-plan.md --skip-verify  # Just implement
npm test                           # Separate test step
```

---

## Key Differences from V1

### V1 (Monolithic)
```bash
/cook plan.md --auto
# → Runs ALL 33 tasks automatically
# → 33 commits created
# → Hard to debug
# → Low control
```

### V2 (Iterator)
```bash
/cook plan.md  # Task 1
git commit
/cook plan.md  # Task 2
git commit
# ... manual iteration
# → 1 task at a time
# → Full control
# → Easy to debug
```

---

## Safety Features

1. **Idempotency**: Safe to re-run (retries current `[ ]` task)
2. **Dependency Validation**: Won't run task if deps incomplete
3. **Automatic Plan Update**: Marks `[x]` when verified
4. **Test Gate**: Stops if verification fails (unless --skip-verify)
5. **No Hidden State**: Everything visible in plan markdown
6. **Syntax Check**: Validates code before testing

---

## Success Criteria

- [ ] Finds next uncompleted task correctly
- [ ] Validates dependencies
- [ ] Implements code per spec
- [ ] Runs verification tests
- [ ] Updates plan checkbox automatically
- [ ] Stops after one task
- [ ] Handles failures gracefully
- [ ] Provides clear next steps

---

## Related Commands

- `/plan` - Create implementation plans to cook
- `/task-progress` - View overall progress dashboard
- `/task-next` - Preview next task (alternative manual approach)

---

## Pro Tips

1. **Use dry-run to preview**:
   ```bash
   /cook plan.md --dry-run
   ```

2. **Commit after each successful cook**:
   ```bash
   /cook plan.md && git add . && git commit -m "$(git diff --cached --name-only | head -1)"
   ```

3. **Focus on one phase at a time**:
   ```bash
   /cook plan.md --phase=A  # Security phase
   # Complete all Phase A
   /cook plan.md --phase=B  # Architecture phase
   ```

4. **Use --auto-fix for routine refactoring**:
   ```bash
   /cook refactor-plan.md --auto-fix
   ```

5. **Check progress anytime**:
   ```bash
   /task-progress plan.md
   ```

---

**Remember**: `/cook` is an iterator, not a loop. One task, one review, one commit. Repeat. 🍳✨
