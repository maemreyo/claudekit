---
name: test-driven-development
description: Strict TDD methodology following Red-Green-Refactor cycle. Fundamental principle: "If you didn't watch the test fail, you don't know if it tests the right thing." Requires writing failing test first, implementing minimal code to pass, then refactoring. Use for new feature development, bug fixes, or refactoring existing code.
---

# Test-Driven Development (TDD)

## Quick Start

1. **Write failing test** for desired behavior
2. **Verify it fails** for the right reason
3. **Write minimal code** to make test pass
4. **Refactor** while keeping tests green

## Instructions

### When to Use TDD
✅ **Always use for**:
- New feature development
- Bug fixes (write test that reproduces bug first)
- Refactoring (ensure tests exist before changing)
- Any behavior change

❌ **NOT for** (requires explicit approval):
- Throwaway prototypes
- Generated/scaffolded code
- Pure configuration changes

### Core Principle
**"If you didn't watch the test fail, you don't know if it tests the right thing."**

### The Red-Green-Refactor Cycle

#### Step 1: RED - Write Failing Test
```typescript
describe('calculateTotal', () => {
  it('should sum item prices', () => {
    const items = [{ price: 10 }, { price: 20 }];
    expect(calculateTotal(items)).toBe(30);
  });
});
```

#### Step 2: Verify Test Fails
```bash
npm test -- --grep "sum item prices"
# Expected: FAIL - "calculateTotal is not defined"
```
The failure should be because feature doesn't exist, not syntax errors.

#### Step 3: GREEN - Write Minimal Code
```typescript
function calculateTotal(items: Item[]): number {
  return items.reduce((sum, item) => sum + item.price, 0);
}
```
Write the simplest code that passes. Don't over-engineer.

#### Step 4: Verify Test Passes
```bash
npm test -- --grep "sum item prices"
# Expected: PASS
```

#### Step 5: REFACTOR - Clean Up
- Extract functions
- Rename variables
- Remove duplication
- Run tests after each change

### The Non-Negotiable Rule

**NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST**

This is a rule, not a guideline.

### Already Wrote Code?
Delete it completely. Don't keep as reference.

```
WRONG: "I'll keep this code as reference"
RIGHT: Delete, write test, rewrite from scratch
```

### Why So Strict?
- Tests written after code just verify what was written
- True TDD produces better designs
- Prevents rationalization and bias

## Examples

### Example 1: User Validation
```typescript
// Step 1: RED - Write failing test
describe('User Validation', () => {
  it('should reject invalid email', () => {
    const user = { email: 'invalid-email', name: 'Test' };
    expect(() => validateUser(user)).toThrow('Invalid email format');
  });
});

// Step 3: GREEN - Minimal implementation
function validateUser(user: User): void {
  if (!user.email.includes('@')) {
    throw new Error('Invalid email format');
  }
}

// Step 5: REFACTOR - Improve implementation
function validateUser(user: User): void {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (!emailRegex.test(user.email)) {
    throw new Error('Invalid email format');
  }
  if (!user.name || user.name.trim().length === 0) {
    throw new Error('Name is required');
  }
}
```

### Example 2: Bug Fix TDD
```typescript
// Bug: calculateTotal doesn't handle negative prices

// Step 1: RED - Test that reproduces bug
it('should handle negative prices correctly', () => {
  const items = [
    { price: 10, quantity: 2 },
    { price: -5, quantity: 1 }, // Discount item
    { price: 20, quantity: 1 }
  ];
  expect(calculateTotal(items)).toBe(45);
});

// Step 3: GREEN - Fix to make test pass
function calculateTotal(items: Item[]): number {
  return items.reduce((sum, item) => {
    return sum + (item.price * item.quantity);
  }, 0);
}
```

### Example 3: API Endpoint
```typescript
// Step 1: RED - Test desired API behavior
describe('POST /api/users', () => {
  it('should create user and return 201', async () => {
    const userData = { name: 'John', email: 'john@example.com' };
    const response = await request(app)
      .post('/api/users')
      .send(userData)
      .expect(201);

    expect(response.body).toMatchObject({
      id: expect.any(Number),
      name: userData.name,
      email: userData.email
    });
  });
});

// Step 3: GREEN - Minimal implementation
app.post('/api/users', async (req, res) => {
  const user = await userService.create(req.body);
  res.status(201).json(user);
});
```

## Best Practices

### 🎯 Test Quality

#### One Behavior Per Test
```typescript
// BAD: Multiple assertions
it('should validate and save user', () => {
  expect(validateUser(user)).toBe(true);
  expect(saveUser(user).id).toBeDefined();
});

// GOOD: Single behavior
it('should validate email format', () => {
  expect(validateUser({ email: 'valid@example.com' })).toBe(true);
});

it('should throw on invalid email', () => {
  expect(() => validateUser({ email: 'invalid' }))
    .toThrow('Invalid email');
});
```

#### Clear Test Names
```typescript
// BAD: Generic names
it('test1', () => {});
it('user test', () => {});

// GOOD: Descriptive names
it('should return 0 for empty cart', () => {});
it('should apply 10% discount for premium members', () => {});
```

#### Test Observable Behavior
```typescript
// BAD: Testing implementation
it('should call database save', () => {
  createUser(user);
  expect(database.save).toHaveBeenCalled();
});

// GOOD: Testing behavior
it('should persist user and return with ID', () => {
  const result = createUser(user);
  expect(result.id).toBeDefined();
});
```

### 🔄 The TDD Mindset

#### Before Writing Code
1. What behavior do I want?
2. How would I prove it works?
3. Write the proof (test)
4. Now write the code

#### Common Anti-patterns
- **"I'll test later"** - Never works as well
- **"This is too simple"** - Simple code breaks too
- **"Manual testing is enough"** - Not repeatable
- **"I already wrote it"** - Sunk cost fallacy

### 📝 Test Organization

#### Test Structure
```typescript
describe('Feature Name', () => {
  describe('Specific Functionality', () => {
    it('should do X when Y', () => {
      // Arrange
      const input = createInput();

      // Act
      const result = functionUnderTest(input);

      // Assert
      expect(result).toBe(expected);
    });
  });
});
```

#### Test Data
```typescript
// Use factories for test data
const createTestUser = (overrides = {}) => ({
  name: 'Test User',
  email: 'test@example.com',
  ...overrides
});
```

### 🛠️ Refactoring Safely

#### With Green Tests
- Extract functions
- Rename variables
- Remove duplication
- Improve performance
- Run tests after each small change

#### When Tests Fail
- Stop refactoring immediately
- Make tests green again
- Only then continue

### 📊 Coverage Guidelines

- Aim for high coverage of business logic
- 100% of critical paths
- Edge cases and error handling
- Don't test trivial getters/setters

## Requirements

### Prerequisites
- Test framework installed (Jest, Vitest, Pytest, etc.)
- Understanding of the problem domain
- Patience to write tests first

### Dependencies
- Test framework (choose based on language)
- Mocking/stubbing libraries if needed
- Test database for integration tests

### Tools
- IDE with good test runner support
- Coverage reporting tools
- CI/CD pipeline for automated testing

### Common Test Frameworks

#### JavaScript/TypeScript
- **Vitest**: Fast, modern, Vite-based
- **Jest**: Feature-rich, widely used
- **Mocha**: Flexible, minimal

#### Python
- **Pytest**: Powerful, plugin-rich
- **Unittest**: Built-in, simple

## Edge Cases to Test

Always test:
- Empty inputs
- Null/undefined values
- Boundary conditions
- Error scenarios
- Invalid inputs
- Large datasets
- Concurrent access (if applicable)

## Troubleshooting TDD

### Test Won't Fail
- Check test is actually running
- Verify assertion is correct
- Ensure you're testing the right thing

### Implementation Too Complex
- Split into smaller functions
- Each with its own test
- Compose in higher-level test

### Tests Too Slow
- Use test databases
- Mock external services
- Run tests in parallel

### 1. RED: Write Failing Test

Write a minimal test demonstrating the desired behavior:

```typescript
describe('calculateTotal', () => {
  it('should sum item prices', () => {
    const items = [{ price: 10 }, { price: 20 }];
    expect(calculateTotal(items)).toBe(30);
  });
});
```

### 2. VERIFY RED: Confirm Test Fails

Run the test and confirm it fails **for the right reason**:

```bash
npm test -- --grep "sum item prices"
# Expected: FAIL
# Reason: calculateTotal is not defined
```

**Critical**: The failure should be because the feature doesn't exist, not because of typos or syntax errors.

### 3. GREEN: Write Minimal Code

Write the simplest code that makes the test pass:

```typescript
function calculateTotal(items: Item[]): number {
  return items.reduce((sum, item) => sum + item.price, 0);
}
```

**Don't over-engineer**. If the test passes with simple code, stop.

### 4. VERIFY GREEN: Confirm Test Passes

Run the test and confirm it passes:

```bash
npm test -- --grep "sum item prices"
# Expected: PASS
```

### 5. REFACTOR: Clean Up

With green tests, refactor safely:
- Extract functions
- Rename variables
- Remove duplication
- Run tests after each change

---

## The Non-Negotiable Rule

**NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST**

This is not a guideline. It's a rule.

### What If I Already Wrote Code?

Delete it. Completely.

```
WRONG: "I'll keep this code as reference while writing tests"
RIGHT: Delete the code, write test, rewrite implementation
```

### Why So Strict?

- Code written before tests wasn't driven by tests
- Keeping it as reference leads to rationalization
- Tests written after code often just verify what was written
- True TDD produces different (usually better) designs

---

## Test Quality Standards

### One Behavior Per Test

```typescript
// BAD: Multiple behaviors
it('should validate and save user', () => {
  expect(validateUser(user)).toBe(true);
  expect(saveUser(user)).toBe(1);
});

// GOOD: Single behavior
it('should validate user email format', () => {
  expect(validateUser({ email: 'test@example.com' })).toBe(true);
});

it('should save valid user', () => {
  const user = createValidUser();
  expect(saveUser(user)).toBe(1);
});
```

### Clear Naming

Test names should describe the behavior:

```typescript
// BAD
it('test1', () => {});
it('calculateTotal', () => {});

// GOOD
it('should return 0 for empty cart', () => {});
it('should apply discount when coupon is valid', () => {});
```

### Real Code Over Mocks

Use real implementations when possible:

```typescript
// PREFER: Real database (test container)
const db = await startTestDatabase();
const result = await userRepo.save(user);

// AVOID: Excessive mocking
const mockDb = { save: jest.fn().mockResolvedValue(1) };
```

### Test Observable Behavior

Test what the code does, not how it does it:

```typescript
// BAD: Testing implementation
it('should call helper function', () => {
  calculateTotal(items);
  expect(helperFn).toHaveBeenCalled();
});

// GOOD: Testing behavior
it('should return correct total', () => {
  expect(calculateTotal(items)).toBe(30);
});
```

---

## Common Rationalizations (Reject These)

### "I'll write tests after"

Tests written after code verify what was written, not what should happen. The test can't prove the code is correct if it was shaped to match existing code.

### "Manual testing is enough"

Ad-hoc testing is not systematic. It misses edge cases, isn't repeatable, and doesn't prevent regressions.

### "This code is too simple to test"

Simple code breaks too. A test takes seconds and provides permanent verification.

### "I don't have time"

TDD is faster in the medium term. Debugging time saved far exceeds test-writing time.

### "I already wrote it, might as well keep it"

Sunk cost fallacy. Delete and rewrite properly.

---

## Edge Cases to Test

Always include tests for:

- Empty inputs
- Null/undefined values
- Boundary conditions
- Error scenarios
- Large inputs
- Invalid inputs

```typescript
describe('calculateTotal', () => {
  it('should return 0 for empty array', () => {
    expect(calculateTotal([])).toBe(0);
  });

  it('should handle null items array', () => {
    expect(() => calculateTotal(null)).toThrow();
  });

  it('should handle negative prices', () => {
    const items = [{ price: -10 }, { price: 20 }];
    expect(calculateTotal(items)).toBe(10);
  });
});
```

---

## TDD Catches Bugs

The methodology catches bugs before commit:
- Writing test first forces you to think about edge cases
- Seeing test fail proves it can catch failures
- Green bar confirms the fix works
- Test prevents regression forever

This is faster than:
1. Write code
2. Manual test (miss edge case)
3. Ship
4. Bug reported
5. Debug
6. Fix
7. Ship again

---
