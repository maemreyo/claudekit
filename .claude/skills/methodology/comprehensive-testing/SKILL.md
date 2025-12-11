# Comprehensive Testing

## Description

A systematic approach to test generation that goes beyond "Happy Path". It uses an adversarial mindset to find weaknesses and ensure robust coverage.

## When to Use

- Adding tests to legacy code
- Ensuring new critical features are robust
- Increasing code coverage metrics
- Regression testing before refactoring

## The Process

### Phase 1: Hunt (Analysis)

**Goal**: Identify code paths and logic branches.

**Strategy**:
- **Control Flow Graph**: Map all `if/else`, `switch`, `loops`.
- **Boundary Identification**: Find min/max values, null checks, optional parameters.
- **State Analysis**: Valid vs Invalid state transitions.

### Phase 2: Attack (Strategy)

**Goal**: Devise scenarios to break the code.

**Techniques**:
- **Null/Undefined**: Pass empty values.
- **Type Mismatch**: Pass wrong types (if JS).
- **Extremes**: Max integers, empty strings, huge arrays.
- **Concurrency**: Race conditions (theoretical).

### Phase 3: Fortify (Implementation)

**Goal**: Write the tests.

**Tiers**:
1.  **Unit**: Isolate functions. Mock dependencies.
2.  **Integration**: Test component interaction (DB, API).
3.  **E2E**: Critical user flows.

## Output Format (for Analyzer Subagent)

```markdown
# Test Strategy: [Module]

## 1. Coverage Targets
- `calculateTax`: 100% branch coverage needed.
- `UserForm`: Focus on validation errors.

## 2. Test Cases Scenarios
| Scenario | Input | Expected Output |
|----------|-------|-----------------|
| Happy Path | valid user | Success |
| Null Email | email=null | Error: Required |
| Short Pass | pass="123" | Error: Too short |

## 3. Mocking Requirements
- Mock `DatabaseService`
- Mock `EmailProvider`
```
