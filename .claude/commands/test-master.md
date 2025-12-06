# /test-master (The QA Lead) 🛡️

## Purpose

Comprehensive test generation that goes beyond happy paths. It uses an adversarial "Attacker Mindset" to fortify code, creating robust test suites that cover edge cases and error states.

**Required Skills**: `comprehensive-testing`
**References**:
- `.claude/skills/methodology/comprehensive-testing/SKILL.md`

## Usage

```bash
/test-master "Cover the Salary Calculation logic" --target=src/utils/salary.ts
```

## Workflow

### Phase 1: HUNT (Explorer Subagent) 🏹

**Goal**: Map the surface area for logic and vulnerabilities.

#### Subtask 1.1: Control Flow Analysis (10-15 min)

**Context**:
- We need to know every possible path code can take.

**Steps**:
1. Read the Target File.
2. Identify **Branching Points**: `if`, `else`, `switch`, ternary operators.
3. Identify **Looping**: `for`, `while`, `map`, `reduce`.
4. Identify **Exits**: `return`, `throw`.

**Example Output**:
```markdown
Control Flow Map:
- Function `calculateTax`:
  - Branch 1: `income < 0` (Early exit)
  - Loop: `brackets.forEach`
  - Exit: Returns number
```

#### Subtask 1.2: Input/Output & State Analysis (10 min)

**Context**:
- What goes in, what comes out, what global state is touched?

**Steps**:
1. List all arguments and their types.
2. List all implicit inputs (dependency injection, environment vars).
3. List return types.

**Example Output**:
```markdown
I/O Analysis:
- Input: `income` (number), `config` (object)
- Output: `CalculatedTax` (object) OR throws `ValidationError`
```

---

### Phase 2: ATTACK (Analyzer Subagent) ⚔️

**Goal**: Devise test scenarios to break the code.

#### Subtask 2.1: Scenario Generation (15-20 min)

**Guidance**: Use `comprehensive-testing` skill.

**Steps**:
1. **Happy Path**: Standard valid input.
2. **Edge Cases**:
   - Null/Undefined (if applicable).
   - Empty values (strings, arrays).
   - Boundaries (0, -1, MAX_SAFE_INTEGER).
3. **Type Mismatch**: (If JS/Runtime) Pass string to number.
4. **State**: Call function in invalid object state (e.g. `submit` before `init`).

**Example Output**:
```markdown
Attack Matrix:
1. Scenario: Income = -5000 -> Expect: Throw "Negative Income"
2. Scenario: Income = 0 -> Expect: Tax = 0
3. Scenario: Income = 100M -> Expect: High Bracket Calculation
4. Scenario: Config = null -> Expect: Use defaults OR Throw
```

#### Subtask 2.2: Mock Design (10 min)

**Steps**:
1. Identify external dependencies found in Phase 1.
2. Define Mock Behavior for each scenario.
   - "Mock DB returns success"
   - "Mock DB throws ConnectionError"

> **REVIEW GATE**: Do the scenarios cover at least 3 "Failure Modes" (Error paths)? If only happy paths, RETRY.

---

### Phase 3: STRATEGIZE (Planner Subagent) ♟️

**Goal**: Plan implementation details.

#### Subtask 3.1: Test Stack Selection (5 min)

**Steps**:
1. Check existing tests to determine framework (Jest/Vitest/Playwright).
2. Decide functionality tier: Unit (isolate) vs Integration (connect).

#### Subtask 3.2: Implementation Plan (10 min)

**Steps**:
1. Draft the `describe` / `it` structure.
2. Plan the setup/teardown (`beforeEach`).

---

### Phase 4: FORTIFY (Executor Subagent) 🛡️

**Goal**: Implement the test suite.

#### Subtask 4.1: Test Skeleton (5 min)

**Action**: Create test file (e.g., `src/utils/salary.test.ts`).

#### Subtask 4.2: Implementation (20-30 min)

**Steps**:
1. Write test cases for each scenario in the Matrix.
2. **Pattern**: Arrange (Mock) -> Act (Call) -> Assert (Expect).
3. **Constraint**: Ensure tests are independent (no shared state).

#### Subtask 4.3: Verification (10 min)

**Steps**:
1. Run the new tests.
2. Verify all pass.
3. (Optional) Run with `--coverage`.

## Success Criteria

- [ ] Happy path covered.
- [ ] At least 3 Edge cases/Boundary values covered.
- [ ] At least 1 Error case covered.
- [ ] Mocks properly clean up after themselves.
