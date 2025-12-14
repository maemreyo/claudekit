---
name: systematic-debugging
description: Four-phase debugging methodology: Root Cause Investigation, Pattern Analysis, Hypothesis Testing, Implementation. Core principle: "NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST". Use when debugging bug reports with unclear cause, production errors, test failures, intermittent issues, or complex multi-component failures.
---

# Systematic Debugging

## Quick Start

1. **Investigate First**: Read errors, reproduce bug, gather evidence
2. **Analyze Patterns**: Find working code, identify differences
3. **Form & Test Hypothesis**: Make specific theory, test with minimal changes
4. **Implement Fix**: Write test first, fix root cause, verify no regressions

## Instructions

### When to Use This Skill
✅ **Perfect for**:
- Bug reports with unclear cause
- Production errors without obvious source
- Tests failing unexpectedly
- Intermittent/flaky issues
- Complex multi-component failures

❌ **NOT for**:
- Simple syntax errors (fix directly)
- Missing imports (add directly)
- Clear, single-location issues

### Core Principle
**"NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST"**

Never skip straight to fixing. Always investigate first.

### Phase 1: Root Cause Investigation

#### 1.1 Read Error Messages Carefully
Document exactly:
- The full error message
- Complete stack trace with line numbers
- Variable values shown in error
- Context when error occurred

#### 1.2 Reproduce Consistently
```markdown
Steps to reproduce:
1. Open URL: [specific URL]
2. Click: [specific element]
3. Fill form with: [specific data]
4. Submit form
5. Error appears: [exact error]
```

#### 1.3 Track Recent Changes
```bash
# Check recent commits
git log --oneline -20

# Check recent deployments
git log --since="1 week ago" --author="[author]"

# Find when it last worked
git bisect start
git bisect bad HEAD
git bisect good [last-working-commit]
```

#### 1.4 Gather Evidence
- Collect relevant logs
- Check monitoring/metrics
- Review related code changes
- Note any environmental factors

#### 1.5 Add Instrumentation (if needed)
```typescript
// Add at system boundaries
console.error('[DEBUG] Phase 1 - Input:', JSON.stringify(input));
console.error('[DEBUG] Phase 1 - After validation:', JSON.stringify(validated));
console.error('[DEBUG] Phase 1 - Before database:', JSON.stringify(query));
```

### Phase 2: Pattern Analysis

#### 2.1 Find Working Code
- Look for similar functionality that works
- Check previous versions of the code
- Find reference implementations
- Review documentation

#### 2.2 Study Reference Thoroughly
- How does working version handle this case?
- What dependencies does it use?
- What assumptions does it make?
- What's the key difference?

#### 2.3 Identify Key Differences
- Configuration differences
- Data structure differences
- Environment differences
- API version differences

#### 2.4 Understand Dependencies
- What does this code depend on?
- What depends on this code?
- Are dependencies behaving correctly?

### Phase 3: Hypothesis and Testing

#### 3.1 Form Specific Hypothesis
Write it explicitly:
```
"The bug occurs because [X] causes [Y] when [Z]"

Example:
"The bug occurs because stale cache data
 returns when user session expires during request"
```

#### 3.2 Test with Minimal Changes
- Change ONE variable at a time
- Never combine multiple fixes
- Verify results after each change
- Document what each test reveals

#### 3.3 Validate Hypothesis
- Does the fix address the root cause?
- Can you explain WHY it works?
- Does it make the bug impossible, not just unlikely?

### Phase 4: Implementation

#### 4.1 Write Failing Test First
```typescript
it('should handle the bug scenario', () => {
  // Reproduce exact conditions
  const result = functionUnderTest(bugTriggeringInput);

  // Should fail before fix
  expect(result.error).toBeDefined();
});
```

#### 4.2 Implement Single Targeted Fix
- Fix root cause, not symptom
- Make minimal, surgical change
- Don't add unnecessary complexity

#### 4.3 Verify Fix Works
```bash
# Run the specific test
npm test -- --grep "should handle the bug scenario"

# Should pass
```

#### 4.4 Verify No Regressions
```bash
# Run full test suite
npm test

# All tests should pass
```

### The Three-Fix Rule

**STOP after 3 failed fix attempts**

This indicates architectural problem, not simple bug:
1. Document all attempted fixes
2. Explain why each failed
3. Discuss with user/team
4. Propose architectural changes

## Examples

### Example 1: API Returns Wrong Data
```typescript
// Error: API returning null values for user field

// Phase 1: Investigation
// Error: "Cannot read property 'name' of null"
// Location: user-profile.ts:45
// Reproduces: When user has no posts

// Phase 2: Pattern Analysis
// Working: /api/users/{id} returns full user object
// Broken: /api/users/{id}/profile returns null user
// Difference: Profile endpoint uses different query

// Phase 3: Hypothesis
// "Profile endpoint query LEFT JOINs posts,
//  returns NULL user when user has no posts"

// Phase 4: Implementation
it('should return user even with no posts', async () => {
  const user = await createUserWithNoPosts();
  const profile = await getUserProfile(user.id);
  expect(profile.user).not.toBeNull();
});

// Fix: Change LEFT JOIN to separate queries
```

### Example 2: Test Fails Intermittently
```typescript
// Error: Test sometimes fails with timeout

// Phase 1: Investigation
// Error: "Test timeout after 5000ms"
// Occurs: 1 in 10 runs
// Recent change: Added async operation

// Phase 2: Pattern Analysis
// Working tests: All use await properly
// Broken test: Missing await on async call
// Pattern: Always await async operations

// Phase 3: Hypothesis
// "Missing await causes test to finish before async operation"

// Phase 4: Implementation
it('should save user asynchronously', async () => {
  const user = new User({ name: 'Test' });
  await user.save(); // Added missing await
  expect(user.id).toBeDefined();
});
```

### Example 3: Memory Leak in Production
```typescript
// Error: Memory usage increases over time

// Phase 1: Investigation
// Error: "Heap out of memory"
// Occurs: After 1000+ API calls
// Pattern: Memory never decreases

// Phase 2: Pattern Analysis
// Working endpoints: Clean up event listeners
// Broken endpoint: Adds listener but never removes
// Pattern: Always cleanup on response complete

// Phase 3: Hypothesis
// "Event listeners accumulate because never removed"

// Phase 4: Implementation
app.use((req, res, next) => {
  res.on('finish', () => {
    // Cleanup resources
    req.removeAllListeners();
  });
  next();
});
```

## Best Practices

### 🔍 Investigation Phase
- **Document everything**: Write down exact error messages
- **Reproduce first**: Never fix without reliable reproduction
- **Check git history**: Often reveals the breaking change
- **Add logging strategically**: At boundaries, not everywhere

### 📊 Analysis Phase
- **Find working examples**: Always look for similar working code
- **Study differences**: What makes working code different?
- **Check dependencies**: Version conflicts cause many bugs
- **Consider environment**: Different from dev to prod

### 🧪 Hypothesis Phase
- **Be specific**: "X causes Y when Z" not "something is wrong"
- **Test one thing**: Change only one variable at a time
- **Document results**: Keep track of what each test reveals
- **Stay curious**: Be ready to abandon initial hypothesis

### 🛠️ Implementation Phase
- **Test first**: Write failing test before fix
- **Minimal changes**: Fix root cause, don't rewrite everything
- **Verify thoroughly**: Run specific test + full suite
- **Explain why**: You should be able to articulate the fix

### 🚫 Common Anti-patterns
- **Jumping to conclusions**: Fixing without investigation
- **Shooting in the dark**: Making random changes
- **Fix and forget**: Not understanding why fix worked
- **Over-engineering**: Making problem worse with complex fixes

### ✅ Success Indicators
- Clear root cause identified
- Fix makes bug impossible, not just unlikely
- Test proves fix works
- No regressions introduced
- Can explain fix to others

## Requirements

### Prerequisites
- Access to error logs and stack traces
- Ability to reproduce the bug
- Permission to add instrumentation/logging
- Understanding of the codebase

### Dependencies
- `root-cause-tracing` skill (for deep stack analysis)
- `defense-in-depth` skill (for preventing similar bugs)
- Test framework (pytest, jest, vitest, etc.)

### Tools
- Debugger (VS Code, Chrome DevTools, etc.)
- Git for history analysis
- Logging tools
- Monitoring/observability platforms

## Debugging Checklist

### Before Any Fix
- [ ] Error message fully documented
- [ ] Bug consistently reproduced
- [ ] Recent changes reviewed
- [ ] Similar working code found
- [ ] Specific hypothesis formed
- [ ] Root cause (not symptom) identified

### After Fix
- [ ] Failing test written first
- [ ] Minimal fix implemented
- [ ] Test passes
- [ ] Full test suite passes
- [ ] No performance regressions
- [ ] Fix documented/explained

### Phase 1: Root Cause Investigation

**Goal**: Understand what's happening before attempting to fix.

**Steps**:

1. **Read error messages carefully**
   ```markdown
   - What is the exact error message?
   - What is the stack trace?
   - What line numbers are mentioned?
   - What values are shown?
   ```

2. **Reproduce consistently**
   ```markdown
   - Can you trigger the bug reliably?
   - What exact steps reproduce it?
   - What environment is required?
   - Document the reproduction steps
   ```

3. **Track recent changes**
   ```markdown
   - What changed recently?
   - git log --oneline -20
   - When did it last work?
   - What was deployed?
   ```

4. **Gather evidence**
   ```markdown
   - Collect logs
   - Check monitoring/metrics
   - Review related code
   - Note any patterns
   ```

5. **Add instrumentation** (for multi-component systems)
   ```typescript
   // Add diagnostic logging at each boundary
   console.error('[DEBUG] Input received:', JSON.stringify(input));
   console.error('[DEBUG] After validation:', JSON.stringify(validated));
   console.error('[DEBUG] Before database call:', JSON.stringify(query));
   console.error('[DEBUG] Database result:', JSON.stringify(result));
   ```

---

### Phase 2: Pattern Analysis

**Goal**: Find comparable working code to identify differences.

**Steps**:

1. **Find working code**
   ```markdown
   - Is there similar functionality that works?
   - What did this code look like before?
   - Are there reference implementations?
   ```

2. **Study reference thoroughly**
   ```markdown
   - How does the working version handle this case?
   - What dependencies does it use?
   - What assumptions does it make?
   ```

3. **Identify differences**
   ```markdown
   - What's different between working and broken?
   - Configuration differences?
   - Data differences?
   - Environment differences?
   ```

4. **Understand dependencies**
   ```markdown
   - What does this code depend on?
   - What depends on this code?
   - Are dependencies behaving correctly?
   ```

---

### Phase 3: Hypothesis and Testing

**Goal**: Form and test a specific theory about the cause.

**Steps**:

1. **Form specific hypothesis**
   ```markdown
   Write it down explicitly:
   "The bug occurs because [X] causes [Y] when [Z]"

   Example:
   "The bug occurs because the cache returns stale data
    when the user's session expires during an active request"
   ```

2. **Test with minimal changes**
   ```markdown
   - Change ONE variable at a time
   - Don't combine multiple fixes
   - Verify results after each change
   ```

3. **Validate hypothesis**
   ```markdown
   - Does the fix address the hypothesis?
   - Can you explain WHY it works?
   - Does it make the bug impossible, not just unlikely?
   ```

---

### Phase 4: Implementation

**Goal**: Fix properly with verification.

**Steps**:

1. **Write failing test first**
   ```typescript
   it('should handle expired session during request', () => {
     // Reproduce the bug scenario
     const session = createExpiredSession();
     const result = processRequest(session);

     // This should fail before the fix
     expect(result.error).toBe('SESSION_EXPIRED');
   });
   ```

2. **Implement single targeted fix**
   ```typescript
   // Fix addresses root cause, not symptom
   function processRequest(session: Session) {
     if (session.isExpired()) {
       return { error: 'SESSION_EXPIRED' };
     }
     // ... rest of logic
   }
   ```

3. **Verify fix works**
   ```bash
   npm test -- --grep "expired session"
   # Should pass
   ```

4. **Verify no regressions**
   ```bash
   npm test
   # All tests should pass
   ```

---

## The Three-Fix Rule

**If three or more fixes fail consecutively, STOP.**

This signals an architectural problem, not a simple bug:

```markdown
Fix attempt 1: Failed
Fix attempt 2: Failed
Fix attempt 3: Failed

STOP: This is not a bug - this is a design problem.

Action: Discuss with user/team before proceeding
- Explain what's been tried
- Explain why it's not working
- Propose architectural changes
```

---

## Key Principles

### Never Skip Error Details

```markdown
BAD: "There's an error somewhere"
GOOD: "TypeError: Cannot read property 'id' of undefined
       at UserService.getUser (user-service.ts:42)"
```

### Reproduce Before Investigating

```markdown
BAD: "I think I know what's wrong" (starts coding)
GOOD: "Let me reproduce this first" (writes repro steps)
```

### Trace Backward to Origin

```markdown
BAD: Fix where error appears
GOOD: Trace data backward to find where it became invalid
```

### One Change Per Test

```markdown
BAD: "I changed A, B, and C - now it works!"
     (Which one fixed it? Are the others safe?)

GOOD: "I changed A - still broken.
       I reverted A and changed B - now it works.
       B was the fix."
```

---

## Debugging Checklist

Before attempting any fix:

- [ ] Error message fully read and understood
- [ ] Bug reproduced consistently
- [ ] Recent changes reviewed
- [ ] Evidence gathered (logs, traces)
- [ ] Hypothesis written down
- [ ] Similar working code identified
- [ ] Root cause identified (not just symptom)

Before declaring fixed:

- [ ] Failing test written
- [ ] Fix implemented
- [ ] Test passes
- [ ] No regressions (full test suite passes)
- [ ] Fix explained (can articulate why it works)

---
