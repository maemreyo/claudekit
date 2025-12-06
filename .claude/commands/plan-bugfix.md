# /plan-bugfix - Bug Fix Planning

## Purpose

Create a structured plan for debugging and fixing issues, including root cause analysis, fix implementation, and regression prevention.

## Usage

```bash
/plan-bugfix [bug description or issue reference]
```

## Arguments

- `$ARGUMENTS`: Description of the bug, symptoms, or issue number

---

## Workflow

Create a bug fix plan for: **$ARGUMENTS**

### Phase 1: Investigation

1. **Reproduce the Bug**
   - Document exact steps to reproduce
   - Identify affected environments
   - Note any patterns (intermittent, specific conditions)

2. **Gather Information**
   - Error messages and stack traces
   - Logs and monitoring data
   - User reports and impact assessment

3. **Identify Scope**
   - Which components are affected?
   - How many users impacted?
   - Severity and priority

### Phase 2: Root Cause Analysis

1. **Trace the Flow**
   - Follow data/execution path
   - Identify where things diverge from expected
   - Find the actual bug location

2. **Understand Why**
   - Why did this bug happen?
   - Why wasn't it caught earlier?
   - Are there similar bugs elsewhere?

### Phase 3: Fix Implementation

1. **Design the Fix**
   - Minimal change to fix the issue
   - Consider side effects
   - Plan for backwards compatibility

2. **Write Tests First**
   - Test that fails with the bug
   - Test that passes with the fix
   - Regression tests

3. **Implement and Verify**
   - Apply the fix
   - Verify tests pass
   - Manual verification

### Phase 4: Prevention

1. **Documentation**
   - Update relevant docs
   - Add code comments if needed

2. **Prevent Recurrence**
   - Add monitoring/alerting
   - Improve test coverage
   - Consider process improvements

---

## Output Format

```markdown
## 🐛 Bug Fix Plan: [Bug Title]

**Severity**: [Critical/High/Medium/Low] | **Priority**: [P1/P2/P3] | **Estimate**: X-Y hours

### Bug Summary

**What's happening**: [Brief description of the bug]

**Expected behavior**: [What should happen]

**Actual behavior**: [What's actually happening]

**Impact**: [Users/systems affected, business impact]

---

### Reproduction Steps

1. [Step 1]
2. [Step 2]
3. [Step 3]
4. **Result**: [Error/unexpected behavior]

**Environment**:
- Browser/OS: [if relevant]
- Version: [app version]
- Conditions: [any specific conditions]

**Reproduction Rate**: [Always/Sometimes/Rare]

---

### Investigation Notes

**Error Details**:
```
[Error message, stack trace, or log output]
```

**Affected Files**:
- `path/to/file1.ts` - [suspected issue]
- `path/to/file2.ts` - [related code]

**Timeline**:
- When introduced: [commit/date if known]
- First reported: [date]
- Affected versions: [versions]

---

### Root Cause Analysis

**What went wrong**:
[Detailed explanation of the bug cause]

**Why it happened**:
- [Technical reason]
- [Process reason, if any]

**Why it wasn't caught**:
- [Gap in testing]
- [Missing validation]

**Related issues** (if any):
- [Similar bugs or related code paths]

---

### Proposed Fix

**Approach**: [Brief description of fix strategy]

**Code Changes**:

```typescript
// Before (buggy code)
function processData(data) {
  return data.items.map(item => item.value); // ❌ Fails if items is undefined
}

// After (fixed code)
function processData(data) {
  return (data.items || []).map(item => item.value); // ✅ Handles undefined
}
```

**Alternative Approaches Considered**:
1. [Alternative 1] - Why not chosen
2. [Alternative 2] - Why not chosen

---

### Tasks

#### Phase 1: Reproduce & Investigate [Xh] 🔍
| # | Task | Size | Est |
|---|------|------|-----|
| 1 | Set up reproduction environment | S | 15m |
| 2 | Reproduce bug consistently | S | 20m |
| 3 | Add debug logging if needed | S | 15m |
| 4 | Trace to root cause | M | 30m |

#### Phase 2: Write Failing Test [Xm] 🧪
| # | Task | Size | Est |
|---|------|------|-----|
| 5 | Write test that exposes the bug | M | 30m |
| 6 | Verify test fails as expected | XS | 5m |

#### Phase 3: Implement Fix [Xh] 🔧
| # | Task | Size | Est |
|---|------|------|-----|
| 7 | Implement minimal fix | M | 30m |
| 8 | Verify test now passes | XS | 5m |
| 9 | Check for side effects | S | 20m |

#### Phase 4: Regression Testing [Xm] ✅
| # | Task | Size | Est |
|---|------|------|-----|
| 10 | Run full test suite | S | 15m |
| 11 | Manual verification | S | 20m |
| 12 | Test edge cases | S | 20m |

#### Phase 5: Documentation & Prevention [Xm] 📝
| # | Task | Size | Est |
|---|------|------|-----|
| 13 | Update documentation | S | 15m |
| 14 | Add monitoring/alerts | S | 20m |
| 15 | Create PR with detailed notes | S | 15m |

---

### Testing Checklist

**Bug Verification**:
- [ ] Bug reproduces before fix
- [ ] Bug is fixed after change
- [ ] Fix works in all affected environments

**Regression**:
- [ ] All existing tests pass
- [ ] New test covers the bug
- [ ] Related functionality still works

**Edge Cases**:
- [ ] [Edge case 1]
- [ ] [Edge case 2]

---

### Rollback Plan

If the fix causes issues:

1. **Immediate**: Revert commit `[hash]`
2. **Verification**: Run smoke tests
3. **Communication**: Notify [stakeholders]

```bash
# Rollback command
git revert [commit-hash]
git push origin main
```

---

### Prevention Measures

**Short-term**:
- [ ] Add monitoring for this error type
- [ ] Add input validation

**Long-term**:
- [ ] Improve test coverage in [area]
- [ ] Add CI check for [condition]
- [ ] Document [pattern/pitfall]

---

### Success Criteria

- [ ] Bug no longer reproduces
- [ ] New test covers the scenario
- [ ] No regression in existing tests
- [ ] Fix deployed to production
- [ ] Monitoring confirms issue resolved

---

### 🚀 Ready to Debug?

Start with Task #1 to reproduce the bug.
```

---

## Investigation Tips

1. **Check recent changes**: `git log --oneline -20`
2. **Search for similar issues**: Check issue tracker
3. **Add strategic logging**: Temporary debug output
4. **Isolate the problem**: Minimal reproduction case
5. **Rubber duck it**: Explain the problem out loud

---

## Related Commands

```bash
/plan           # General planning
/debug          # Interactive debugging help
/fix            # Quick fix without full plan
```
