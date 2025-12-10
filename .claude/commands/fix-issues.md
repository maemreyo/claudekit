# /fix-issues - Intelligent Issue Resolution

## Purpose

Tự động phát hiện, phân loại và giải quyết các vấn đề được phát hiện từ `/how-recent-changes` hoặc bất kỳ phân tích code nào. Command này hoạt động như một "fixer" thông minh, có khả năng xử lý nhiều loại vấn đề: bugs, missing tests, tech debt, security issues, performance problems, documentation gaps, và code quality issues.

## Aliases

```bash
/fix
/resolve
/address-issues
/cleanup
```

## Usage

```bash
# Auto-detect from last analysis (default: reads latest analysis artifact)
/fix-issues

# Fix specific issue types
/fix-issues --type=tests              # Only missing tests
/fix-issues --type=bugs               # Only bugs
/fix-issues --type=security           # Security vulnerabilities
/fix-issues --type=performance        # Performance issues
/fix-issues --type=docs               # Documentation gaps
/fix-issues --type=quality            # Code quality (linting, formatting)
/fix-issues --type=debt               # Technical debt

# Multiple types
/fix-issues --type=tests,bugs,security

# Direct fix request (Ad-hoc)
/fix-issues "fix lỗi ở src/components/Header.tsx: button bị lệch"

# From specific analysis file
/fix-issues --from=.claude/artifacts/recent-changes-2024-01-15.md

# Focus on specific files/directories
/fix-issues --files=src/auth,src/api

# Severity filter
/fix-issues --severity=high           # Only high severity
/fix-issues --severity=medium,high    # Medium and high

# Execution modes
/fix-issues --interactive             # Ask before each fix
/fix-issues --plan-only               # Generate plan without execution
/fix-issues --auto                    # Auto-fix everything (default for low severity)

# With constraints
/fix-issues --max-fixes=5             # Limit number of fixes
/fix-issues --time-budget=30m         # Time constraint

# Create tracking issue
/fix-issues --create-issue            # Creates issue in project tracker
```

---

## Workflow

### Phase 0: Discovery & Triage 🔍

**Agent**: [`researcher`](.claude/agents/researcher.md)

**Skills**:
- [`pattern-analysis`](.claude/skills/methodology/pattern-analysis/SKILL.md) - Detect patterns in issues
- [`sequential-thinking`](.claude/skills/methodology/sequential-thinking/SKILL.md) - Prioritize logically

**Goal**: Thu thập và phân loại tất cả các vấn đề cần xử lý.

**Steps**:

1. **Load Source Data**
   ```bash
   # If direct string argument provided (e.g., /fix-issues "fix error..."):
   # Use it as the issue description and skip file loading
   
   # Else if --from specified, read that file
   
   # Else, find latest analysis artifact
   ls -t .claude/artifacts/recent-changes-*.md | head -1
   ```

2. **Extract Issues**
   - If direct argument: Parse the user's request as a generic 'bug' or 'task' issue.
   - If from analysis:
     - Parse "⚠️ Quan Sát & Gợi Ý" section
     - Parse "🔄 Hành Động Đề Xuất" section
     - Parse "Risk Assessment" notes
     - Parse comparison với plan (missing items)

3. **Classify Issues** (using pattern-analysis)
   
   **Issue Types**:
   - `bugs` - Logic errors, runtime issues, edge cases
   - `tests` - Missing test coverage, broken tests
   - `security` - Vulnerabilities, exposed secrets, injection risks
   - `performance` - Inefficient code, memory leaks, slow queries
   - `docs` - Missing/outdated documentation, unclear comments
   - `quality` - Linting errors, formatting, naming conventions
   - `debt` - Deprecated APIs, duplicated code, complex functions
   - `accessibility` - A11y violations, missing ARIA labels
   - `dependencies` - Outdated packages, security advisories
   
   **Severity Levels**:
   - `critical` - Breaks production, security breach, data loss
   - `high` - Major bugs, significant performance issues
   - `medium` - Minor bugs, tech debt, missing tests
   - `low` - Code style, documentation, minor improvements

4. **Build Issue Registry**
   ```json
   {
     "issues": [
       {
         "id": "ISS-001",
         "type": "tests",
         "severity": "high",
         "title": "Missing tests for authentication logic",
         "files": ["src/auth/login.js"],
         "description": "New login flow has no test coverage",
         "estimated_effort": "30m",
         "dependencies": []
       },
       {
         "id": "ISS-002",
         "type": "security",
         "severity": "critical",
         "title": "SQL injection vulnerability in search",
         "files": ["src/api/search.js"],
         "description": "User input not sanitized in SQL query",
         "estimated_effort": "15m",
         "dependencies": []
       }
     ]
   }
   ```

5. **Apply Filters**
   - Filter by `--type` if specified
   - Filter by `--severity` if specified
   - Filter by `--files` if specified
   - Filter by `--max-fixes` limit

6. **Prioritization & Grouping** (using sequential-thinking)
   - **Related Issues**: Identify issues that modify the same files or logical components.
   - **Subtask Creation**: Group related issues into a single **Subtask** to be handled together.
   - Priority Sort: Critical security → Blocking bugs → High impact → Quick wins.

---

### Phase 1: Planning & Strategy 🎯

**Agent**: [`architect`](.claude/agents/architect.md)

**Skills**:
- [`system-design`](.claude/skills/methodology/system-design/SKILL.md) - Design solutions
- [`sequential-thinking`](.claude/skills/methodology/sequential-thinking/SKILL.md) - Plan execution order

**Goal**: Tạo execution plan chi tiết cho từng issue.

**Planning Tasks**:

1. **For Each Issue, Generate Fix Strategy**

   **Strategy Template**:
   ```markdown
   ## Issue: [ISS-XXX] [Title]
   
   ### Analysis
   - Root cause: [Why this happened]
   - Impact: [What could go wrong]
   - Risk of fix: [What could break]
   
   ### Solution Approach
   - Strategy: [High-level approach]
   - Steps: [Detailed steps]
   - Validation: [How to verify fix works]
   
   ### Dependencies
   - Requires: [Other issues that must be fixed first]
   - Blocks: [Issues that can't proceed until this is done]
   
   ### Estimation
   - Effort: [Time estimate]
   - Complexity: [low|medium|high]
   ```

2. **Subtask & Dependency Resolution**
   - **Group Related Issues**: Ensure issues related to each other (e.g., fix bug + add test for same component) are in the same execution block (Subtask).
   - Build dependency graph between Subtasks.
   - Identify cycles (flag as manual intervention needed).
   - Calculate critical path.

3. **Risk Assessment**
   - High-risk fixes → Interactive mode mandatory
   - Safe fixes → Can auto-execute
   - Breaking changes → Require explicit approval

4. **Create Execution Plan**
   ```markdown
   # Fix Execution Plan
   
   ## Summary
   - Total issues: 15
   - Critical: 2
   - High: 5
   - Medium: 6
   - Low: 2
   - Estimated time: 2h 30m
   
   ## Execution Order
   
   ### Batch 1: Critical (Must fix immediately)
   1. [ISS-002] SQL injection in search (15m)
   2. [ISS-007] Exposed API keys in config (10m)
   
   ### Batch 2: High Priority (Can run in parallel)
   3. [ISS-001] Missing auth tests (30m)
   4. [ISS-003] Memory leak in WebSocket (45m)
   5. [ISS-005] N+1 query in dashboard (20m)
   
   ### Batch 3: Medium Priority
   ...
   ```

---

### Phase 2: Execution 🔧

**Dynamic Agent Selection**: Depends on issue type

**Agent Mapping**:
- `bugs`, `security`, `performance` → [`coder`](.claude/agents/coder.md)
- `tests` → [`tester`](.claude/agents/tester.md)
- `docs` → [`docs-manager`](.claude/agents/docs-manager.md)
- `quality`, `debt` → [`coder`](.claude/agents/coder.md)

**Skills Required**:
- [`debugging`](.claude/skills/development/debugging/SKILL.md) - For bug fixes
- [`testing`](.claude/skills/development/testing/SKILL.md) - For test writing
- [`security-review`](.claude/skills/development/security-review/SKILL.md) - For security fixes

**Execution Flow**:

{{ if --plan-only }}
**Stop here and output the plan**
{{ endif }}

{{ if --interactive }}
**For each issue**:
1. Show issue details and proposed fix
2. Ask: "Proceed with this fix? (y/n/skip/abort)"
3. If yes → Execute
4. If skip → Mark for later, continue
5. If abort → Stop execution, save progress
{{ endif }}

{{ if --auto }}
**Auto-execute safe issues**:
- Severity: low, medium (no breaking changes)
- Type: quality, docs, simple bugs
- Skip high-risk changes
{{ endif }}

**For Each Subtask (Group of Related Issues)**:

1. **Pre-Fix Validation**
   ```bash
   # Create checkpoint
   git add -A
   git stash push -m "Before fix: [ISS-XXX]"
   
   # Run existing tests (if any)
   npm test || pytest || cargo test
   ```

2. **Execute Fix**
   
   **Example: Missing Tests**
   ```markdown
   Agent: tester
   Task: Write tests for src/auth/login.js
   
   Requirements:
   - Test success case
   - Test failure cases (wrong password, non-existent user)
   - Test edge cases (empty input, SQL injection attempts)
   - Achieve >80% coverage
   ```

   **Example: Security Vulnerability**
   ```markdown
   Agent: coder
   Task: Fix SQL injection in src/api/search.js
   
   Requirements:
   - Use parameterized queries
   - Validate and sanitize all user input
   - Add input length limits
   - Follow OWASP guidelines
   ```

   **Example: Performance Issue**
   ```markdown
   Agent: coder
   Task: Fix N+1 query in dashboard
   
   Requirements:
   - Use eager loading / JOIN
   - Add database index if needed
   - Benchmark before/after
   - Ensure <100ms response time
   ```

3. **Post-Fix Validation**
   ```bash
   # Run tests
   npm test
   
   # Run linter
   npm run lint
   
   # Check types (if TypeScript)
   npm run type-check
   
   # Security scan (if available)
   npm audit || safety check
   ```

4. **Verification**
   - Did tests pass?
   - Did fix solve the original issue?
   - Did fix introduce new issues?
   - Performance impact acceptable?

5. **Record Outcome**
   ```json
   {
     "issue_id": "ISS-001",
     "status": "fixed|failed|skipped",
     "time_taken": "28m",
     "files_changed": ["src/auth/login.test.js"],
     "verification": {
       "tests_passed": true,
       "lint_passed": true,
       "manual_check_needed": false
     },
     "notes": "Added 12 test cases, coverage increased to 87%"
   }
   ```

---

### Phase 3: Reporting & Commit 📋

**Agent**: [`git-manager`](.claude/agents/git-manager.md)

**Goal**: Tổng hợp kết quả và tạo commits có tổ chức.

**Steps**:

1. **Generate Summary Report**
   ```markdown
   # 🔧 Fix Issues - Execution Report
   
   **Execution Date**: 2024-01-15 14:30:00
   **Source Analysis**: recent-changes-2024-01-15.md
   **Mode**: auto
   
   ## 📊 Summary
   - **Total Issues**: 15
   - **Fixed**: 12 ✅
   - **Failed**: 1 ❌
   - **Skipped**: 2 ⏭️
   - **Time Taken**: 2h 15m (planned: 2h 30m)
   
   ## ✅ Successfully Fixed
   
   ### Critical (2/2)
   - **[ISS-002]** SQL injection in search ✅
     - Files: src/api/search.js
     - Fix: Implemented parameterized queries
     - Validation: Security tests passed
   
   - **[ISS-007]** Exposed API keys ✅
     - Files: config/env.example, .gitignore
     - Fix: Moved to environment variables
     - Validation: No secrets in git history
   
   ### High Priority (4/5)
   - **[ISS-001]** Missing auth tests ✅
     - Files: src/auth/login.test.js
     - Fix: Added 12 test cases
     - Coverage: 45% → 87%
   
   - **[ISS-003]** Memory leak in WebSocket ✅
     - Files: src/websocket/connection.js
     - Fix: Proper cleanup on disconnect
     - Validation: Memory usage stable after 1000 connections
   
   - **[ISS-005]** N+1 query in dashboard ✅
     - Files: src/api/dashboard.js, migrations/add-index.sql
     - Fix: Added JOIN + database index
     - Performance: 850ms → 45ms
   
   - **[ISS-009]** Unhandled promise rejection ✅
     - Files: src/utils/async-handler.js
     - Fix: Added proper error handling
     - Validation: No unhandled rejections in logs
   
   ### Medium Priority (5/6)
   ...
   
   ## ❌ Failed Fixes
   
   - **[ISS-011]** Complex refactoring in payment flow
     - Reason: Requires architectural changes beyond scope
     - Recommendation: Create separate task for manual review
     - Created issue: JIRA-1234
   
   ## ⏭️ Skipped Issues
   
   - **[ISS-013]** Update React 17 → 18
     - Reason: Breaking changes require team discussion
     - Action: Added to tech debt backlog
   
   - **[ISS-014]** Redesign error messages
     - Reason: Requires UX review
     - Action: Assigned to design team
   
   ## 📈 Impact Analysis
   
   ### Code Quality
   - Test coverage: 45% → 72% (+27%)
   - Linting errors: 23 → 0 (-23)
   - Security vulnerabilities: 2 critical → 0 (-2)
   
   ### Performance
   - Dashboard load time: 850ms → 45ms (-95%)
   - Memory usage: -15% after fixes
   - API response time: -30% average
   
   ### Technical Debt
   - Deprecated API usage: 8 → 2 (-6)
   - TODO comments: 15 → 7 (-8)
   - Code complexity: High → Medium
   
   ## 📝 Files Changed
   
   Total: 18 files
   
   **Added**:
   - src/auth/login.test.js
   - src/websocket/connection.test.js
   - migrations/20240115-add-dashboard-index.sql
   
   **Modified**:
   - src/api/search.js (security fix)
   - src/api/dashboard.js (performance fix)
   - src/websocket/connection.js (memory leak fix)
   - src/utils/async-handler.js (error handling)
   - config/env.example
   - .gitignore
   - ... (9 more)
   
   ## 🔄 Recommended Next Steps
   
   1. **Manual Review Required**
      - [ISS-011] Payment flow refactoring (high complexity)
      - Create design document for approach
   
   2. **Team Discussion Needed**
      - [ISS-013] React upgrade strategy
      - Schedule planning meeting
   
   3. **Follow-up Tasks**
      - Monitor memory usage in production
      - Run full regression test suite
      - Update deployment docs
   
   ## 🎯 Commit Strategy
   
   Fixes organized into logical commits:
   
   1. `fix(security): resolve SQL injection and exposed secrets [ISS-002, ISS-007]`
   2. `test(auth): add comprehensive test coverage [ISS-001]`
   3. `perf(dashboard): fix N+1 query with JOIN and index [ISS-005]`
   4. `fix(websocket): resolve memory leak on disconnect [ISS-003]`
   5. `fix(async): add proper error handling for promises [ISS-009]`
   6. `style(lint): fix linting errors across codebase [ISS-012, ISS-015]`
   ```

2. **Commit Strategy**

   **Group by Type** (follow Conventional Commits):
   ```bash
   # Security fixes (critical) - separate commit
   git add src/api/search.js config/env.example .gitignore
   git commit -m "fix(security): resolve SQL injection and exposed secrets
   
   - Implement parameterized queries in search endpoint
   - Move API keys to environment variables
   - Update .gitignore to prevent secret leaks
   
   Fixes: ISS-002, ISS-007
   Severity: critical"
   
   # Tests - separate commit
   git add src/auth/login.test.js
   git commit -m "test(auth): add comprehensive test coverage
   
   - Add 12 test cases for login flow
   - Cover success, failure, and edge cases
   - Increase coverage from 45% to 87%
   
   Fixes: ISS-001
   Coverage-increase: +42%"
   
   # Performance - separate commit
   git add src/api/dashboard.js migrations/
   git commit -m "perf(dashboard): fix N+1 query with JOIN and index
   
   - Replace multiple queries with single JOIN
   - Add database index for faster lookups
   - Reduce load time from 850ms to 45ms
   
   Fixes: ISS-005
   Performance-gain: 95%"
   ```

3. **Create Tracking Issue** (if --create-issue)
   ```markdown
   Title: Fix Issues from Recent Changes Analysis (2024-01-15)
   
   ## Summary
   Addressed 12/15 issues identified in recent changes analysis.
   
   ## Completed
   - [x] SQL injection vulnerability (ISS-002)
   - [x] Exposed API keys (ISS-007)
   - [x] Missing auth tests (ISS-001)
   - [x] Memory leak in WebSocket (ISS-003)
   - [x] N+1 query in dashboard (ISS-005)
   - ... (7 more)
   
   ## Remaining
   - [ ] Payment flow refactoring (ISS-011) - Requires design
   - [ ] React 17 → 18 upgrade (ISS-013) - Breaking changes
   - [ ] Error message redesign (ISS-014) - Needs UX review
   
   ## Impact
   - Test coverage: +27%
   - Performance: +95% on dashboard
   - Security: 2 critical vulnerabilities resolved
   
   See full report: .claude/artifacts/fix-issues-report-2024-01-15.md
   ```

---

## Output Artifacts

1. **Main Report**: `.claude/artifacts/fix-issues-report-[timestamp].md`
2. **Execution Log**: `.claude/logs/fix-issues-[timestamp].log`
3. **Failed Issues**: `.claude/artifacts/failed-issues-[timestamp].json`
4. **Git Commits**: Organized by type/severity

---

## Integration with Subagents & Skills

### Agent Collaboration

1. **researcher** → Discovery & triage
2. **architect** → Planning & strategy
3. **coder** → Bug fixes, refactoring, performance
4. **tester** → Test writing, validation
5. **docs-manager** → Documentation updates
6. **git-manager** → Commit organization

### Skill Application

1. **pattern-analysis** → Classify issues, detect patterns
2. **sequential-thinking** → Prioritize, plan execution order
3. **system-design** → Design solutions for complex issues
4. **debugging** → Root cause analysis
5. **testing** → Test creation and validation
6. **security-review** → Security fix validation

---

## Best Practices

1. **Always run in --interactive for production code**
2. **Use --plan-only first** to review strategy
3. **Run tests after each fix** to catch regressions
4. **Create checkpoints** before risky changes
5. **Group related fixes** in same commit
6. **Document failed fixes** for manual review

---

## Error Handling

```markdown
## Error Scenarios

1. **Fix causes test failures**
   - Rollback: `git stash pop`
   - Mark as failed
   - Log error details
   - Continue with next issue

2. **Dependency cycle detected**
   - Flag for manual resolution
   - Skip affected issues
   - Document in report

3. **Time budget exceeded**
   - Complete current fix
   - Pause execution
   - Save progress report
   - Return remaining issues

4. **Ambiguous issue**
   - Request clarification (interactive mode)
   - Skip in auto mode
   - Log for manual review
```

---

## Related Commands

- [`/how-recent-changes`](.claude/commands/how-recent-changes.md) - Analyze changes
- [`/review`](.claude/commands/review.md) - Code review
- [`/test`](.claude/commands/test.md) - Run tests
- [`/commit`](.claude/commands/commit.md) - Create commits

---

## Examples

### Example 1: Auto-fix safe issues
```bash
> /how-recent-changes
> /fix-issues --auto --severity=low,medium
```

### Example 2: Interactive fix with plan
```bash
> /fix-issues --plan-only
# Review plan...
> /fix-issues --interactive
```

### Example 3: Focus on tests only
```bash
> /fix-issues --type=tests --files=src/auth
```

### Example 4: Full workflow with tracking
```bash
> /how-recent-changes --deep
> /fix-issues --interactive --create-issue
# Fix issues...
> /commit
```