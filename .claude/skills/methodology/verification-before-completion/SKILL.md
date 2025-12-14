---
name: verification-before-completion
description: Mandatory verification process before claiming any task is complete. Enforces evidence-based completion through 5-step process: IDENTIFY command, EXECUTE fully, READ complete output, VERIFY matches claim, then CLAIM with evidence. Use when verifying tests pass, builds succeed, bugs are fixed, or marking any task complete.
---

# Verification Before Completion

## Quick Start

1. **IDENTIFY** the verification command
2. **EXECUTE** the command fully
3. **READ** complete output carefully
4. **VERIFY** output matches claim
5. **CLAIM** with specific evidence

## Instructions

### When to Use This Skill
✅ **Always before claiming**:
- Tests pass/fail
- Build succeeds/fails
- Bug is fixed
- Task is complete
- Feature is implemented

### Core Principle
**Evidence over assumptions** - Never claim without verification.

### The 5-Step Verification Process

#### Step 1: IDENTIFY
What specific command proves your claim?
```markdown
Claim: "Tests pass" → Command: npm test
Claim: "Build succeeds" → Command: npm run build
Claim: "Lint clean" → Command: npm run lint
```

#### Step 2: EXECUTE
Run the command freshly - no cached results:
```bash
npm test  # Full run, not partial
```

#### Step 3: READ
Read complete output and exit codes:
- Check exit code (0 = success)
- Note pass/fail counts
- Look for errors or warnings

#### Step 4: VERIFY
Does output match your claim?
```
Claim: "All tests pass"
Output: "42 passing, 0 failing"
Result: ✓ Verified
```

#### Step 5: CLAIM
Now make the claim with evidence:
```
✓ All tests pass (42 passing, 0 failing) - verified at [timestamp]
```

### Required Validations

#### Testing Verification
```bash
# Run full test suite
npm test

# Must verify:
- Zero failures
- Expected test count
- No unexpected skipped tests
```

#### Build Verification
```bash
# Run full build
npm run build

# Must verify:
- Exit code 0
- Build artifacts created
- No build errors
```

#### Bug Fix Verification
Must complete red-green cycle:
```bash
# 1. Reproduce bug
npm test -- --grep "bug scenario"  # Should fail

# 2. Apply fix

# 3. Verify fix
npm test -- --grep "bug scenario"  # Should pass
```

#### Requirements Verification
Create checklist for each requirement:
```markdown
Requirements:
- [ ] Req 1: User login (Test: test_login_flow)
- [ ] Req 2: Password reset (Test: test_reset_password)
- [ ] Req 3: Session expiry (Test: test_session_timeout)
```

## Examples

### Example 1: Test Suite Verification
```markdown
## Task: Implement user authentication

### Step 1: IDENTIFY
Claim: "All tests pass"
Command: npm test

### Step 2: EXECUTE
$ npm test

### Step 3: READ
Test Suites: 12 passed, 12 total
Tests:       156 passed, 156 total
Snapshots:   0 total
Time:        3.456 s

### Step 4: VERIFY
- ✓ 156 tests passing
- ✓ 0 tests failing
- ✓ All test suites passing
- Matches claim "All tests pass"

### Step 5: CLAIM
✓ All authentication tests pass (156/156) - verified at 14:32:10
```

### Example 2: Bug Fix Verification
```markdown
## Task: Fix login timeout error

### Before Fix - Step 1-3:
$ npm test -- --grep "login timeout"
FAIL: should handle login timeout (2.3ms)

### After Fix - Step 1-5:
$ npm test -- --grep "login timeout"
PASS: should handle login timeout (15ms)

### Step 4: VERIFY
- Test now passes
- Specific error case fixed
- No regression in related tests

### Step 5: CLAIM
✓ Login timeout bug fixed - test passes, verified with red-green cycle
```

### Example 3: Build Verification
```markdown
## Task: Optimize bundle size

### Step 1: IDENTIFY
Claim: "Build succeeds and bundle is under 1MB"
Command: npm run build && du -h dist/

### Step 2: EXECUTE
$ npm run build && du -h dist/
Build completed in 2.3s
987K    dist/

### Step 4: VERIFY
- ✓ Build succeeds (exit code 0)
- ✓ Bundle size: 987K (< 1MB requirement)
- ✓ No build errors

### Step 5: CLAIM
✓ Build optimized - succeeds at 987K (under 1MB target)
```

### Example 4: Agent Work Verification
```markdown
## Task: Review agent's database migration fix

### Agent Report:
"Fixed migration script - all good"

### Independent Verification:
$ npm run migration:up
Migration successful

$ npm run migration:status
All migrations applied

$ npm test -- --grep "database"
Tests: 45 passing, 0 failing

### Step 5: CLAIM
✓ Migration fix verified - applied successfully, tests pass
```

## Best Practices

### 🔍 Verification Mindset

#### Always Verify Independently
- Don't trust memory of previous runs
- Don't assume based on related successes
- Run fresh verification each time

#### Be Specific in Claims
```
Vague: "Tests should pass"
Specific: "All 156 tests pass (verified just now)"
```

#### Include Evidence
- Numbers and counts
- Exit codes
- Timestamps
- Specific outputs

### ✅ What to Verify

#### Before Claiming Tests Pass
- Run full test suite
- Check for skipped tests
- Verify test coverage if required
- Note any warnings

#### Before Claiming Build Success
- Run clean build (rm -rf dist && build)
- Check all artifacts created
- Verify no build errors
- Check bundle size if relevant

#### Before Claiming Bug Fixed
- Reproduce original failure
- Apply fix
- Verify fix works
- Check for regressions
- Run red-green cycle if possible

#### Before Claiming Task Complete
- Verify all requirements met
- Check related functionality works
- Run acceptance tests if available
- Document verification results

### 🚫 Common Anti-patterns

#### Assuming Based on Code
```markdown
❌ BAD: "I added the null check, so it should work"
✅ GOOD: "Added null check, verified by running tests - all pass"
```

#### Partial Verification
```markdown
❌ BAD: "Ran one test, it passed"
✅ GOOD: "Ran full test suite, all 156 tests pass"
```

#### Trust Without Verification
```markdown
❌ BAD: "Agent said it's fixed"
✅ GOOD: "Agent completed fix, I verified by running tests"
```

#### Vague Claims
```markdown
❌ BAD: "Done!"
✅ GOOD: "Complete: tests pass (156/156), build succeeds, requirements met"
```

### 📝 Verification Templates

#### Simple Task Template
```markdown
## Task: [Name]

### Verification:
- [ ] Tests: `npm test` → [X passing, Y failing]
- [ ] Build: `npm run build` → [Success/Failed]
- [ ] Lint: `npm run lint` → [Clean/Errors]

### Result:
✓ Task complete - [specific verification results]
```

#### Complex Feature Template
```markdown
## Feature: [Name]

### Requirements Check:
- [ ] Req 1: [Description] → [How verified]
- [ ] Req 2: [Description] → [How verified]
- [ ] Req 3: [Description] → [How verified]

### Automated Tests:
- Unit: [X/Y] passing
- Integration: [X/Y] passing
- E2E: [X/Y] passing

### Manual Verification:
- [ ] Scenario 1: [Result]
- [ ] Scenario 2: [Result]

### Conclusion:
✓ Feature complete, all requirements verified
```

## Requirements

### Prerequisites
- Access to command line/terminal
- Understanding of project's build/test processes
- Ability to interpret test results and build outputs

### Dependencies
- Test framework (Jest, Vitest, Pytest, etc.)
- Build tools (Webpack, Vite, etc.)
- Linting/formatting tools

### Tools
- Terminal with command history
- CI/CD dashboard for build status
- Test coverage reporters
- Bundle analyzers for build verification

## Common Verification Commands

### JavaScript/TypeScript
```bash
npm test          # Run tests
npm run test:coverage  # Test with coverage
npm run build      # Production build
npm run lint       # Code quality
npm run typecheck  # TypeScript checking
```

### Python
```bash
pytest            # Run tests
pytest --cov     # With coverage
python -m build   # Build package
flake8            # Linting
mypy              # Type checking
```

### General
```bash
git status        # Check changes
git diff          # Review modifications
```

## Verification Checklist

Before claiming ANY task is complete:

- [ ] Identified verification command
- [ ] Executed command fully
- [ ] Read complete output
- [ ] Verified output matches claim
- [ ] Claim includes specific evidence
- [ ] No assumptions made
- [ ] Related functionality checked

### Step 1: IDENTIFY

Determine the command that proves your assertion:

```markdown
Claim: "Tests pass"
Verification command: npm test

Claim: "Build succeeds"
Verification command: npm run build

Claim: "Linting passes"
Verification command: npm run lint
```

### Step 2: EXECUTE

Run the command fully and freshly:

```bash
# Don't rely on cached results
# Don't assume previous run is still valid
npm test
```

### Step 3: READ

Read the complete output and exit codes:

```bash
# Check output carefully
# Don't skim - read every line
# Note exit code (0 = success)
```

### Step 4: VERIFY

Confirm the output matches your claim:

```markdown
Claim: "All tests pass"
Output shows: "42 passing, 0 failing"
Verification: ✓ Claim is accurate
```

### Step 5: CLAIM

Only now make the claim, with evidence:

```markdown
✓ All tests pass (42 passing, verified at 2024-01-15 14:30)
```

---

## Required Validations by Category

### Testing

```bash
# Run test command
npm test

# Verify in output:
# - Zero failures
# - Expected test count
# - No skipped tests (unless intentional)
```

**Not valid**: "Tests should pass" without running them

### Linting

```bash
# Run linter completely
npm run lint

# Verify in output:
# - Zero errors
# - Zero warnings (or acceptable known warnings)
```

**Not valid**: Using lint as proxy for build success

### Building

```bash
# Run build command
npm run build

# Verify:
# - Exit code 0
# - Build artifacts created
# - No errors in output
```

**Not valid**: Assuming lint passing means build passes

### Bug Fixes

```bash
# Step 1: Reproduce original bug
npm test -- --grep "failing test"
# Should fail

# Step 2: Apply fix

# Step 3: Verify fix works
npm test -- --grep "failing test"
# Should pass
```

**Not valid**: Claiming fix works without reproducing original failure

### Regression Tests

Complete red-green cycle required:

```bash
# 1. Write test, run it
npm test  # Should PASS with new test

# 2. Revert the fix
git stash

# 3. Run test again
npm test  # Should FAIL (proves test catches the bug)

# 4. Restore fix
git stash pop

# 5. Run test again
npm test  # Should PASS
```

### Requirements Verification

```markdown
## Original Requirements
1. User can login with email
2. User can reset password
3. Session expires after 24 hours

## Verification Checklist
- [x] Requirement 1: Tested login flow manually + unit tests
- [x] Requirement 2: Tested reset flow manually + integration test
- [x] Requirement 3: Verified SESSION_TIMEOUT=86400 in config + test
```

### Agent Work Verification

Don't trust agent reports blindly:

```bash
# Agent claims: "Fixed the bug in user.ts"

# Verify independently:
git diff src/user.ts  # Check actual changes
npm test              # Verify tests pass
```

---

## Forbidden Language

Never use these phrases without verification:

| Forbidden | Why |
|-----------|-----|
| "should work" | Implies uncertainty |
| "probably fixed" | Not verified |
| "seems to pass" | Didn't read output |
| "I think it's done" | Guessing |
| "Great!" (before checking) | Premature celebration |
| "Done!" (before verification) | Unverified claim |

### Replace With

| Instead Say | After |
|-------------|-------|
| "Tests pass" | Running tests, seeing 0 failures |
| "Build succeeds" | Running build, exit code 0 |
| "Bug is fixed" | Reproducing bug, verifying fix |

---

## Anti-Patterns

### Partial Verification

```markdown
BAD: "I ran one test and it passed"
GOOD: "Full test suite passes (42/42)"
```

### Relying on Prior Runs

```markdown
BAD: "Tests passed earlier"
GOOD: "Tests pass now (just ran)"
```

### Skipping Verification

```markdown
BAD: "This is a small change, no need to verify"
GOOD: "Small change, but verified: tests pass, lint clean"
```

### Trusting Without Checking

```markdown
BAD: Agent said it's fixed, so it's fixed
GOOD: Agent said it's fixed, I verified by running tests
```

---

## Verification Checklist Template

Use before claiming completion:

```markdown
## Task: [Task Name]

### Verification Steps
- [ ] Tests run: `npm test`
  - Result: [X passing, Y failing]
- [ ] Lint passes: `npm run lint`
  - Result: [No errors]
- [ ] Build succeeds: `npm run build`
  - Result: [Exit code 0]
- [ ] Requirements met:
  - [ ] Requirement 1: [How verified]
  - [ ] Requirement 2: [How verified]

### Evidence
[Paste relevant output or screenshots]

### Conclusion
✓ Task complete, all verifications passed
```

---
