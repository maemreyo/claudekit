# /plan-react - React/Next.js Feature Planning

## Purpose

Create detailed implementation plans optimized for React/Next.js development with component-driven approach, testing strategy, and TDD workflow.

## Usage

```bash
/plan-react [feature description]
/plan-react --tdd [feature]           # Include TDD micro-tasks
/plan-react --nextjs [feature]        # Next.js specific (SSR, API routes)
/plan-react --detailed [feature]      # Extra detailed steps
/plan-react --output=path [feature]   # Save plan to specific file
```

## Arguments

- `$ARGUMENTS`: Description of the React/Next.js feature to implement

## Flags

| Flag | Description | Example |
|------|-------------|---------|
| `--tdd` | Include TDD micro-tasks (2-5 min cycles) | `/plan-react --tdd "login feature"` |
| `--nextjs` | Next.js specific patterns (App Router, API) | `/plan-react --nextjs "blog with SSG"` |
| `--detailed` | Extra detailed task breakdown | `/plan-react --detailed "complex form"` |
| `--output=path` | Save plan to specified file | `/plan-react --output=plans/migration.md "tools migration"` |

---

## Workflow

Create a React/Next.js implementation plan for: **$ARGUMENTS**

{{ if --output flag provided }}
**Output file**: Save this plan to `$OUTPUT_PATH` for later execution with `/execute-plan`
{{ endif }}

### Planning Principles

**This command generates plans optimized for `/execute-plan` automation with**:
- ✅ **TDD Micro-Tasks**: 2-5 minute cycles (Test → Implement → Verify → Commit)
- ✅ **Explicit File Paths**: Every task specifies exact file location
- ✅ **Acceptance Criteria**: Clear checkboxes for each task
- ✅ **Code Examples**: Test and implementation snippets included
- ✅ **Sequential Execution**: Tasks designed for one-by-one completion

### Phase 0: Pre-Planning Analysis

Before generating tasks, analyze:

1. **Component Breakdown**
   - Identify all components (presentational vs container)
   - Determine component hierarchy
   - Plan file structure explicitly

2. **State Management**
   - Identify state types (local, server, global, form)
   - Choose appropriate tools per state type
   - Plan state flow through component tree

3. **File Paths**
   - Map exact file locations for all files
   - Consider existing project structure
   - Plan imports and exports

4. **Testing Strategy**
   - Determine test types needed per component
   - Plan MSW handlers for API mocking
   - Identify integration test scenarios

### Phase 1: Component Planning

1. **Break Down UI**
   - Identify component hierarchy
   - Determine which components are presentational vs container
   - **Output**: Component tree diagram with explicit file paths
   
2. **Plan File Structure**
   - Map all files to be created/modified
   - Include full paths (e.g., `src/features/auth/components/LoginForm.tsx`)
   - Group by feature/domain

3. **State Strategy**
   - Local state (useState, useReducer)
   - Server state (React Query, SWR) 
   - Global state (Context, Zustand, Redux)
   - Form state (React Hook Form, Formik)
   - **Output**: State management plan per component

4. **Data Flow**
   - Props drilling analysis
   - Context providers needed
   - API integration points
   - **Output**: Data flow diagram

### Phase 2: Testing Strategy

1. **Component Tests** (React Testing Library)
   - User interactions
   - Render variations
   - Accessibility
   - **Output**: Test file paths and key test cases

2. **Hook Tests**
   - Custom hooks logic
   - State updates
   - Side effects
   - **Output**: Hook test structure

3. **Integration Tests**
   - API mocking (MSW)
   - User flows
   - Navigation
   - **Output**: MSW handlers structure

### Phase 3: Task Generation (TDD Micro-Tasks)

Generate tasks following this pattern for EACH component/feature:

#### Standard Micro-Task Pattern

For each component, create 4-5 micro-tasks:

1. **Test Task** (2-3 min)
   - Write failing test
   - File: `path/to/Component.test.tsx`
   - Expected: ❌ Test fails (component doesn't exist)
   
2. **Minimal Implementation** (3-5 min)
   - Create component file
   - File: `path/to/Component.tsx`
   - Minimal code to pass test
   - Expected: ✅ Test passes
   
3. **Enhance & Refactor** (3-5 min)
   - Add proper structure, types, styling
   - Add accessibility
   - Keep tests passing
   - Expected: ✅ All tests pass
   
4. **Additional Tests** (2-4 min)
   - Add edge case tests
   - Add interaction tests
   - Expected: ✅ All tests pass
   
5. **Commit** (1 min)
   - Git add and commit
   - Clear commit message
   - Expected: Clean git status

#### Task Format

Each micro-task MUST include:

```markdown
## Task X.Y: [Clear Description] (Est: Xm)

**File**: `explicit/path/to/file.tsx`

**Context** (NEW):
- Existing patterns: Reference `path/to/similar-component.tsx`
- Tech stack: Uses [library] (already installed)
- Related files: `path/to/related-file.tsx`

**Acceptance Criteria**:
- [ ] Specific checklist item 1
- [ ] Specific checklist item 2
- [ ] Specific checklist item 3
- [ ] Follows existing code style in project
- [ ] Uses established patterns from similar components

**Implementation Strategy** (NOT exact code):
1. Step-by-step approach
2. What to reference from existing code
3. Key patterns to follow
4. Libraries/components to use

**Example Pattern** (reference only, adapt to your project):
```typescript
// Example showing the APPROACH, not exact code
// Study existing components and adapt this pattern
export function Component({ prop }: Props) {
  // 1. Use existing hooks pattern
  const { data } = useExistingPattern();
  
  // 2. Follow project's component structure
  return (
    <ExistingLayout>
      {/* Adapt this to match project style */}
    </ExistingLayout>
  );
}
```

**References to Study**:
- Similar component: `path/to/similar.tsx` (study its pattern)
- Existing hooks: `path/to/hooks.ts` (reuse if applicable)
- Style conventions: Check other components in same directory

**Expected Result**: ✅ Tests pass / Component renders correctly
**Code should**: Match existing project style and patterns
```

---

### Key Differences from Old Format

**OLD (Too prescriptive)** ❌:
```markdown
**Implementation**:
```typescript
// 100 lines of exact code that AI will copy
export function LoginForm({ onSubmit }: Props) {
  const { register, handleSubmit } = useForm({
    resolver: zodResolver(loginSchema)
  });
  
  return (
    <form onSubmit={handleSubmit(...)}>
      <label htmlFor="email">Email</label>
      <input {...register('email')} />
      {/* 50 more lines... */}
    </form>
  );
}
```
```

**Problem**: AI just copies, doesn't think

---

**NEW (Guidance-based)** ✅:
```markdown
**Implementation Strategy**:
1. **Reference existing form**: Check `src/components/auth/RegisterForm.tsx` 
   - Uses same react-hook-form + zod pattern
   - Copy validation approach
   - Match error display style

2. **Reuse existing components**:
   - Use `<Input>` from `@/components/ui/input` (already styled)
   - Use `<Button>` from `@/components/ui/button`
   - Use `<Label>` for accessibility

3. **Form structure**:
   - Email field with validation (min 3 chars, valid email)
   - Password field (min 8 chars, show/hide toggle)
   - Submit button with loading state

4. **Follow project patterns**:
   - Error messages: Check how RegisterForm displays errors
   - Loading state: See how other forms handle `isLoading`
   - Styling: Match Tailwind classes from existing forms

**Example Pattern**:
```typescript
// EXAMPLE ONLY - Study RegisterForm.tsx and adapt
export function LoginForm({ onSubmit, isLoading }: Props) {
  // Use same validation pattern as RegisterForm
  const { register, formState: { errors } } = useForm({
    // ... your validation
  });
  
  return (
    <form>
      {/* Follow RegisterForm's input structure */}
      <div>
        <Label>Email</Label>
        <Input {...register('email')} />
        {errors.email && <ErrorMessage />}
      </div>
      {/* ... */}
    </form>
  );
}
```

**Must check before implementing**:
- [ ] Read `RegisterForm.tsx` to understand pattern
- [ ] Verify `Input` component API in Storybook/docs
- [ ] Test with invalid data to see error states
```

**Result**: AI must:
- Read existing code ✅
- Understand patterns ✅
- Adapt, not copy ✅
- Think about edge cases ✅

---

### Example: Complete Task with New Format

```markdown
#### Phase 2: Build LoginForm Component [20min] 🎨

##### Task 2.1: Write Test - LoginForm Rendering (3 min)

**File**: `src/features/auth/components/__tests__/LoginForm.test.tsx`

**Context**:
- Existing test patterns: Check `RegisterForm.test.tsx` in same directory
- Test utilities: `src/test/utils/render.tsx` (wraps with providers)
- Mock functions: Use `vi.fn()` (Vitest, not Jest)

**Acceptance Criteria**:
- [ ] Test file created following existing test structure
- [ ] Uses same test utilities as RegisterForm tests
- [ ] Tests email and password input rendering
- [ ] Tests submit button rendering

**Implementation Strategy**:
1. Copy test setup pattern from `RegisterForm.test.tsx`
2. Use same `renderWithProviders` helper
3. Test basic rendering (inputs, button exist)
4. Follow existing test naming: `should render X when Y`

**Expected**: ❌ LoginForm not found (test fails)

---

##### Task 2.2: Minimal LoginForm Implementation (5 min)

**File**: `src/features/auth/components/LoginForm.tsx`

**Context**:
- Pattern reference: `RegisterForm.tsx` (same directory)
- UI components: `@/components/ui` (Input, Button, Label)
- Form library: react-hook-form (already used in project)
- Validation: zod (check RegisterForm's schema)

**Acceptance Criteria**:
- [ ] Component renders email and password inputs
- [ ] Submit button present
- [ ] Uses same form components as RegisterForm
- [ ] Props interface matches project conventions (see RegisterFormProps)

**Implementation Strategy**:
1. **Study RegisterForm.tsx first** - understand:
   - How it structures the form
   - Which UI components it uses
   - How props are typed
   - How errors are displayed

2. **Create component following same pattern**:
   - Same prop interface style (onSubmit, isLoading)
   - Use same Input/Button components
   - Match HTML structure (form > div > label + input)

3. **Minimal implementation for now**:
   - Just render inputs (no validation yet)
   - Basic onSubmit handler
   - Match RegisterForm's styling classes

**Example Pattern** (study RegisterForm, don't copy this):
```typescript
// This is JUST a pattern reference
// Read RegisterForm.tsx and adapt to match its style
interface LoginFormProps {
  onSubmit: (data: LoginData) => void;
  isLoading?: boolean;
}

export function LoginForm({ onSubmit, isLoading }: LoginFormProps) {
  // Check how RegisterForm handles form submission
  const handleSubmit = (e) => {
    e.preventDefault();
    // ... get form data and call onSubmit
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* Copy the structure from RegisterForm */}
      {/* Use same Input/Label components */}
    </form>
  );
}
```

**Must verify**:
- [ ] LoginForm looks similar to RegisterForm (consistent UX)
- [ ] Uses same Tailwind classes for spacing
- [ ] Imports from same locations

**Expected**: ✅ Task 2.1 test passes

---

##### Task 2.3: Add Validation (7 min)

**File**: `src/features/auth/components/LoginForm.tsx`

**Context**:
- Validation pattern: Check how RegisterForm uses zod + react-hook-form
- Schema location: `src/features/auth/schemas/` (if exists)
- Error display: See how RegisterForm shows validation errors

**Implementation Strategy**:
1. **Create validation schema**:
   - Study `RegisterForm`'s validation setup
   - Use zod (same as RegisterForm)
   - Email: required, valid email format
   - Password: required, min 8 characters

2. **Integrate with react-hook-form**:
   - Use `zodResolver` like RegisterForm does
   - Get `formState: { errors }` from useForm
   - Display errors same way as RegisterForm

3. **Error display**:
   - Match RegisterForm's error message styling
   - Use same error text component/class
   - Show errors below each input

**Expected**: ✅ Tests pass, form validates correctly
```

---

### Benefits of This Approach

| Aspect | Old (Exact Code) | New (Guidance) | Winner |
|--------|------------------|----------------|---------|
| **AI thinks** | ❌ No (copy-paste) | ✅ Yes (must understand) | ✅ New |
| **Adapts to project** | ❌ No | ✅ Yes (forced to read existing) | ✅ New |
| **Code quality** | ⚠️ Generic | ✅ Matches project style | ✅ New |
| **Learning** | ❌ No | ✅ AI learns patterns | ✅ New |
| **Flexibility** | ❌ Rigid | ✅ Adapts to changes | ✅ New |
| **Edge cases** | ⚠️ Only what's in code | ✅ AI considers more | ✅ New |

---

## When to Use Each Approach

### Use EXACT CODE when:
- ❌ Never! (Too prescriptive)

### Use DETAILED GUIDANCE when:
- ✅ Complex logic that's hard to describe
- ✅ Critical algorithms (tax calculation)
- ✅ Security-sensitive code
- ✅ BUT: Still as "reference", not exact

### Use LIGHT GUIDANCE when:
- ✅ Simple CRUD components
- ✅ UI components (if project has clear patterns)
- ✅ Standard patterns already established

### Example Spectrum

**Light Guidance**:
```markdown
Task: Add delete button to UserCard

**Strategy**:
- Reference how EditButton is implemented in same component
- Use trash icon from lucide-react
- Follow same onClick pattern
- Add confirmation dialog (see other delete actions)
```

**Medium Guidance** (RECOMMENDED):
```markdown
Task: Add form validation

**Strategy**:
1. Study RegisterForm.tsx validation pattern
2. Use zod + react-hook-form (already in project)
3. Follow same error display approach
4. Schema: email (required, valid), password (min 8 chars)

**Pattern reference** (adapt, don't copy):
```typescript
const schema = z.object({ email, password });
const { register, formState } = useForm({ resolver });
```
```

**Heavy Guidance** (for complex cases):
```markdown
Task: Implement Vietnamese tax calculation

**Context**:
- Tax law reference: 2024 Vietnamese tax brackets
- Existing: Check if calculateTax() exists in utils/

**Strategy**:
1. Progressive tax brackets (7 levels in Vietnam)
2. Deductions: 11M personal + 4.4M per dependent
3. Calculate per bracket, accumulate

**Reference implementation**:
```typescript
// Reference only - adapt to project
function calculateTax(income: number, dependents: number) {
  const deduction = 11_000_000 + (dependents * 4_400_000);
  const taxable = income - deduction;
  
  // Apply brackets progressively
  let tax = 0;
  // ... bracket logic (implement properly)
  
  return tax;
}
```

**Must verify**:
- Deduction amounts are 2024 values
- Bracket cutoffs are correct
- Rounding matches Vietnamese standards
```


### Phase 4: Task Sequencing

Order tasks for optimal execution:

1. **Foundation First**
   - Types/interfaces (no tests needed)
   - Data modules
   - Utilities (with tests)
   
2. **Shared Components**
   - UI primitives (Button, Input)
   - Common containers (Layout)
   - Can be parallelized mentally but execute sequentially
   
3. **Feature Components (Bottom-Up)**
   - Leaf components first (presentational)
   - Container components after
   - Pages/routes last
   
4. **Integration**
   - API integration
   - State management hookup
   - Navigation/routing
   
5. **Polish**
   - Optimization
   - Accessibility audit
   - Performance tuning

---

## Output Format

```markdown
## ⚛️ React Plan: [Feature Name]

**Stack**: React [version] / Next.js [version] | **Estimate**: X-Y hours | **TDD**: [Yes/No]

### Component Architecture

**Component Hierarchy**:
```
App
├── FeatureContainer (Smart)
│   ├── FeatureHeader (Presentational)
│   ├── FeatureList (Smart)
│   │   └── FeatureItem (Presentational)
│   └── FeatureForm (Smart)
│       ├── Input (Presentational)
│       └── Button (Presentational)
```

**Component Breakdown**:

| Component | Type | Responsibility | State | Props |
|-----------|------|----------------|-------|-------|
| FeatureContainer | Container | Data fetching, orchestration | Server state | - |
| FeatureList | Container | List logic, filtering | Local state | items[] |
| FeatureItem | Presentational | Display single item | None | item, onEdit, onDelete |
| FeatureForm | Container | Form handling, validation | Form state | onSubmit |

---

### State Management

**Local State** (useState/useReducer):
```typescript
// Component-level state
const [isOpen, setIsOpen] = useState(false);
const [filter, setFilter] = useState('all');
```

**Server State** (React Query/SWR):
```typescript
// API data fetching and caching
const { data, isLoading, error } = useQuery({
  queryKey: ['features'],
  queryFn: fetchFeatures
});
```

**Global State** (Context/Zustand):
```typescript
// Shared state across components
const { theme, setTheme } = useThemeStore();
```

**Form State** (React Hook Form):
```typescript
// Form management
const { register, handleSubmit, formState: { errors } } = useForm();
```

---

### File Structure

```
src/
├── features/
│   └── [feature-name]/
│       ├── components/
│       │   ├── FeatureContainer.tsx
│       │   ├── FeatureList.tsx
│       │   ├── FeatureItem.tsx
│       │   └── FeatureForm.tsx
│       ├── hooks/
│       │   ├── useFeature.ts
│       │   ├── useFeatureForm.ts
│       │   └── useFeatureFilter.ts
│       ├── api/
│       │   └── featureApi.ts
│       ├── types/
│       │   └── feature.types.ts
│       ├── utils/
│       │   └── featureHelpers.ts
│       └── __tests__/
│           ├── FeatureContainer.test.tsx
│           ├── FeatureList.test.tsx
│           ├── FeatureItem.test.tsx
│           ├── useFeature.test.ts
│           └── integration.test.tsx
└── (Next.js specific)
    ├── app/feature/page.tsx         # App Router
    └── pages/api/features.ts        # API routes
```

---

### Tasks Format (Component-Driven TDD)

**IMPORTANT**: Generate tasks as TDD micro-tasks (2-5 min each), NOT large 30-60 min tasks.

#### Micro-Task Structure

Each phase breaks down into micro-tasks following this pattern:

```markdown
#### Phase X: [Phase Name] [Total Est] 🎯

##### Task X.1: Write Test - [Component Name] (3 min)

**File**: `src/features/[feature]/components/[Component].test.tsx`

**Acceptance Criteria**:
- [ ] Test file created with describe block
- [ ] Test case for basic rendering
- [ ] Proper imports and setup

**Test**:
```typescript
import { render, screen } from '@testing-library/react';
import { ComponentName } from './ComponentName';

describe('ComponentName', () => {
  it('should render with required props', () => {
    render(<ComponentName prop="value" />);
    expect(screen.getByText('value')).toBeInTheDocument();
  });
});
```

**Expected**: ❌ Component not found

---

##### Task X.2: Minimal Implementation - [Component Name] (5 min)

**File**: `src/features/[feature]/components/[Component].tsx`

**Acceptance Criteria**:
- [ ] Component file created
- [ ] Props interface defined
- [ ] Minimal JSX to pass test
- [ ] Exports component

**Implementation**:
```typescript
interface ComponentNameProps {
  prop: string;
}

export function ComponentName({ prop }: ComponentNameProps) {
  return <div>{prop}</div>;
}
```

**Expected**: ✅ Task X.1 test passes

---

##### Task X.3: Enhance - [Component Name] (4 min)

**File**: `src/features/[feature]/components/[Component].tsx`

**Acceptance Criteria**:
- [ ] Add proper HTML structure
- [ ] Add accessibility attributes
- [ ] Add styling/className
- [ ] Add TypeScript strict mode compliance

**Enhanced Implementation**:
```typescript
export function ComponentName({ prop }: ComponentNameProps) {
  return (
    <article className="component-name" aria-label={prop}>
      <h3>{prop}</h3>
    </article>
  );
}
```

**Expected**: ✅ All tests still pass

---

##### Task X.4: Additional Tests - [Component Name] (3 min)

**File**: `src/features/[feature]/components/[Component].test.tsx`

**Acceptance Criteria**:
- [ ] Test user interactions
- [ ] Test edge cases
- [ ] Test accessibility

**Additional Tests**:
```typescript
it('should handle click events', async () => {
  const onClick = vi.fn();
  const user = userEvent.setup();
  
  render(<ComponentName prop="value" onClick={onClick} />);
  await user.click(screen.getByRole('article'));
  
  expect(onClick).toHaveBeenCalled();
});
```

**Expected**: ✅ All tests pass

---

##### Task X.5: Commit - [Component Name] (1 min)

**Command**:
```bash
git add src/features/[feature]/components/[Component].*
git commit -m "feat([feature]): add [Component] component with tests"
```

**Acceptance Criteria**:
- [ ] All new files committed
- [ ] Tests passing
- [ ] No uncommitted changes for this component

**Expected**: Clean git status

---
```

#### Phase Breakdown Example

```markdown
#### Phase 1: Foundation & Types [45min] 🏗️

##### Task 1.1: Create Types (10 min)
**File**: `src/features/[feature]/types/[feature].types.ts`
**AC**:
- [ ] Interface for main domain object
- [ ] Zod schema for validation
- [ ] Export all types
**No test needed** - Pure types

##### Task 1.2: Write Test - API Client (3 min)
**File**: `src/features/[feature]/api/__tests__/featureApi.test.ts`
**Test**: [code]
**Expected**: ❌ Function not found

##### Task 1.3: Implement - API Client (8 min)
**File**: `src/features/[feature]/api/featureApi.ts`
**Implementation**: [code]
**Expected**: ✅ Test passes

##### Task 1.4: Write Test - Custom Hook (3 min)
**File**: `src/features/[feature]/hooks/__tests__/useFeature.test.ts`
**Test**: [code]
**Expected**: ❌ Hook not found

##### Task 1.5: Implement - Custom Hook (10 min)
**File**: `src/features/[feature]/hooks/useFeature.ts`
**Implementation**: [code]
**Expected**: ✅ Test passes

##### Task 1.6: Commit Foundation (1 min)
git commit -m "feat([feature]): add types, API client, and hooks"

---

#### Phase 2: Presentational Components [30min] 🎨

##### Task 2.1: Write Test - FeatureItem (3 min)
**File**: `src/features/[feature]/components/__tests__/FeatureItem.test.tsx`
**Test**: [code]
**Expected**: ❌ Component not found

##### Task 2.2: Implement - FeatureItem Minimal (4 min)
**File**: `src/features/[feature]/components/FeatureItem.tsx`
**Implementation**: [minimal code]
**Expected**: ✅ Test passes

##### Task 2.3: Enhance - FeatureItem (4 min)
**File**: `src/features/[feature]/components/FeatureItem.tsx`
**Implementation**: [enhanced code]
**Expected**: ✅ Tests pass

##### Task 2.4: Additional Tests - FeatureItem (3 min)
**Tests**: [interaction tests]
**Expected**: ✅ All pass

##### Task 2.5: Commit - FeatureItem (1 min)

[Repeat pattern for each presentational component]

---

#### Phase 3: Container Components [40min] 🔗

[Similar micro-task breakdown]

---

#### Phase 4: Pages & Routes [20min] 🚀

[Similar micro-task breakdown]

---

#### Phase 5: Integration & Polish [25min] ✨

[Integration tests, performance, accessibility]
```

---

### Task Estimation Guidelines

| Task Type | Time Range | Examples |
|-----------|-----------|----------|
| Write Test | 2-4 min | Component render test, hook test |
| Minimal Implementation | 3-6 min | Pass the test with minimal code |
| Enhancement | 3-5 min | Add structure, styling, a11y |
| Additional Tests | 2-4 min | Interactions, edge cases |
| Commit | 1 min | Git add + commit |
| Create Types | 8-15 min | Interfaces, Zod schemas |
| API Integration | 10-20 min | API client, error handling |

**Total for one component**: ~15-25 min (5-6 micro-tasks)

---

### File Path Conventions

Always use explicit full paths:

**Good ✅**:
```markdown
**File**: `src/features/auth/components/LoginForm.tsx`
**File**: `src/features/auth/hooks/useAuth.ts`
**File**: `src/app/[locale]/auth/login/page.tsx`
```

**Bad ❌**:
```markdown
**File**: LoginForm component
**File**: auth hooks
**File**: login page
```

---

### Example: Complete Component Micro-Tasks

```markdown
#### Phase 2: Build LoginForm Component [20min] 🎨

##### Task 2.1: Write Test - LoginForm Rendering (3 min)

**File**: `src/features/auth/components/__tests__/LoginForm.test.tsx`

**Acceptance Criteria**:
- [ ] Test file created with RTL imports
- [ ] Test renders email and password inputs
- [ ] Test renders submit button

**Test**:
```typescript
import { render, screen } from '@testing-library/react';
import { LoginForm } from '../LoginForm';

describe('LoginForm', () => {
  const mockOnSubmit = vi.fn();

  it('should render email and password inputs', () => {
    render(<LoginForm onSubmit={mockOnSubmit} />);
    
    expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/password/i)).toBeInTheDocument();
    expect(screen.getByRole('button', { name: /login/i })).toBeInTheDocument();
  });
});
```

**Expected**: ❌ LoginForm not found

---

##### Task 2.2: Minimal LoginForm Implementation (5 min)

**File**: `src/features/auth/components/LoginForm.tsx`

**Acceptance Criteria**:
- [ ] Component created with props interface
- [ ] Email input rendered
- [ ] Password input rendered  
- [ ] Submit button rendered
- [ ] Form has onSubmit handler

**Implementation**:
```typescript
interface LoginFormProps {
  onSubmit: (email: string, password: string) => void;
}

export function LoginForm({ onSubmit }: LoginFormProps) {
  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    // Minimal implementation
  };

  return (
    <form onSubmit={handleSubmit}>
      <label htmlFor="email">Email</label>
      <input id="email" type="email" />
      
      <label htmlFor="password">Password</label>
      <input id="password" type="password" />
      
      <button type="submit">Login</button>
    </form>
  );
}
```

**Expected**: ✅ Task 2.1 test passes

---

##### Task 2.3: Enhance LoginForm (5 min)

**File**: `src/features/auth/components/LoginForm.tsx`

**Acceptance Criteria**:
- [ ] Use React Hook Form
- [ ] Add Zod validation
- [ ] Add proper styling classes
- [ ] Add accessibility attributes
- [ ] Add loading state support

**Enhanced Implementation**:
```typescript
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { loginSchema } from '../schemas/loginSchema';

interface LoginFormProps {
  onSubmit: (email: string, password: string) => void;
  isLoading?: boolean;
}

export function LoginForm({ onSubmit, isLoading }: LoginFormProps) {
  const { register, handleSubmit, formState: { errors } } = useForm({
    resolver: zodResolver(loginSchema)
  });

  return (
    <form 
      onSubmit={handleSubmit((data) => onSubmit(data.email, data.password))}
      className="space-y-4"
    >
      <div>
        <label htmlFor="email" className="block text-sm font-medium">
          Email
        </label>
        <input
          {...register('email')}
          id="email"
          type="email"
          className="mt-1 block w-full rounded border px-3 py-2"
          aria-invalid={errors.email ? 'true' : 'false'}
        />
        {errors.email && (
          <p className="text-sm text-red-600">{errors.email.message}</p>
        )}
      </div>
      
      <div>
        <label htmlFor="password" className="block text-sm font-medium">
          Password
        </label>
        <input
          {...register('password')}
          id="password"
          type="password"
          className="mt-1 block w-full rounded border px-3 py-2"
          aria-invalid={errors.password ? 'true' : 'false'}
        />
        {errors.password && (
          <p className="text-sm text-red-600">{errors.password.message}</p>
        )}
      </div>
      
      <button
        type="submit"
        disabled={isLoading}
        className="w-full bg-blue-600 text-white rounded py-2 disabled:opacity-50"
      >
        {isLoading ? 'Logging in...' : 'Login'}
      </button>
    </form>
  );
}
```

**Expected**: ✅ All Task 2.1 tests still pass

---

##### Task 2.4: Additional LoginForm Tests (4 min)

**File**: `src/features/auth/components/__tests__/LoginForm.test.tsx`

**Acceptance Criteria**:
- [ ] Test form submission with valid data
- [ ] Test validation errors
- [ ] Test loading state

**Additional Tests**:
```typescript
it('should call onSubmit with email and password', async () => {
  const mockOnSubmit = vi.fn();
  const user = userEvent.setup();
  
  render(<LoginForm onSubmit={mockOnSubmit} />);
  
  await user.type(screen.getByLabelText(/email/i), 'test@example.com');
  await user.type(screen.getByLabelText(/password/i), 'password123');
  await user.click(screen.getByRole('button', { name: /login/i }));
  
  expect(mockOnSubmit).toHaveBeenCalledWith('test@example.com', 'password123');
});

it('should disabled button when loading', () => {
  render(<LoginForm onSubmit={vi.fn()} isLoading={true} />);
  
  expect(screen.getByRole('button')).toBeDisabled();
  expect(screen.getByText(/logging in/i)).toBeInTheDocument();
});
```

**Expected**: ✅ All tests pass

---

##### Task 2.5: Commit LoginForm (1 min)

**Command**:
```bash
git add src/features/auth/components/LoginForm.* src/features/auth/schemas/
git commit -m "feat(auth): add LoginForm component with validation"
```

**Acceptance Criteria**:
- [ ] LoginForm.tsx committed
- [ ] LoginForm.test.tsx committed  
- [ ] Related schema committed
- [ ] All tests passing

**Expected**: Clean git status
```

---

### Testing Strategy (React Testing Library)

**Component Testing Template**:
```typescript
// FeatureItem.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { FeatureItem } from './FeatureItem';

describe('FeatureItem', () => {
  const mockItem = { id: '1', name: 'Test', status: 'active' };
  const mockOnEdit = jest.fn();
  const mockOnDelete = jest.fn();

  it('should render item details', () => {
    render(
      <FeatureItem 
        item={mockItem} 
        onEdit={mockOnEdit} 
        onDelete={mockOnDelete} 
      />
    );
    
    expect(screen.getByText('Test')).toBeInTheDocument();
    expect(screen.getByText('active')).toBeInTheDocument();
  });

  it('should call onEdit when edit button clicked', () => {
    render(<FeatureItem item={mockItem} onEdit={mockOnEdit} onDelete={mockOnDelete} />);
    
    fireEvent.click(screen.getByRole('button', { name: /edit/i }));
    expect(mockOnEdit).toHaveBeenCalledWith(mockItem.id);
  });

  it('should be accessible', () => {
    const { container } = render(
      <FeatureItem item={mockItem} onEdit={mockOnEdit} onDelete={mockOnDelete} />
    );
    
    // Check for proper ARIA labels, semantic HTML
    expect(screen.getByRole('article')).toBeInTheDocument();
  });
});
```

**Hook Testing Template**:
```typescript
// useFeature.test.ts
import { renderHook, waitFor } from '@testing-library/react';
import { useFeature } from './useFeature';

describe('useFeature', () => {
  it('should fetch feature data', async () => {
    const { result } = renderHook(() => useFeature('1'));
    
    await waitFor(() => {
      expect(result.current.isLoading).toBe(false);
    });
    
    expect(result.current.data).toEqual(mockFeature);
  });
});
```

**Integration Testing with MSW**:
```typescript
// integration.test.tsx
import { rest } from 'msw';
import { setupServer } from 'msw/node';

const server = setupServer(
  rest.get('/api/features', (req, res, ctx) => {
    return res(ctx.json([{ id: '1', name: 'Test' }]));
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

describe('Feature Integration', () => {
  it('should fetch and display features', async () => {
    render(<FeatureContainer />);
    
    await waitFor(() => {
      expect(screen.getByText('Test')).toBeInTheDocument();
    });
  });
});
```

---

### TDD Workflow (if --tdd flag)

For each component, follow this micro-task cycle:

```markdown
## Task X.1: [Component] - Write Failing Test (3 min)

**Test**:
```typescript
// Write test that component doesn't exist yet
it('should render feature name', () => {
  render(<FeatureItem item={mockItem} />);
  expect(screen.getByText(mockItem.name)).toBeInTheDocument();
});
```

**Expected**: ❌ Component not found

## Task X.2: [Component] - Minimal Implementation (5 min)

**Implementation**:
```typescript
export const FeatureItem = ({ item }) => {
  return <div>{item.name}</div>;
};
```

**Expected**: ✅ Test passes

## Task X.3: [Component] - Refactor & Enhance (4 min)

Add proper structure, styling, accessibility:
```typescript
export const FeatureItem = ({ item }) => {
  return (
    <article className="feature-item" aria-label={`Feature: ${item.name}`}>
      <h3>{item.name}</h3>
      <span className="status">{item.status}</span>
    </article>
  );
};
```

**Expected**: ✅ All tests still pass

## Task X.4: Commit (1 min)

```bash
git add src/features/feature-name/
git commit -m "feat(feature): add FeatureItem component with tests"
```
```

---

### Next.js Specific (if --nextjs flag)

**App Router (Next.js 13+)**:
```typescript
// app/features/page.tsx
import { FeatureContainer } from '@/features/feature-name/components/FeatureContainer';

export default function FeaturesPage() {
  return <FeatureContainer />;
}

// Server Component for data fetching
export async function generateMetadata() {
  return { title: 'Features' };
}
```

**API Routes**:
```typescript
// app/api/features/route.ts
import { NextResponse } from 'next/server';

export async function GET() {
  const features = await fetchFeatures();
  return NextResponse.json(features);
}

export async function POST(request: Request) {
  const body = await request.json();
  const newFeature = await createFeature(body);
  return NextResponse.json(newFeature, { status: 201 });
}
```

**Data Fetching Strategies**:
- Server Components: Direct DB access, no API needed
- Client Components: React Query for caching
- Static: generateStaticParams for SSG
- Dynamic: fetch with revalidate for ISR

---

### Performance Optimization

**React.memo for Expensive Renders**:
```typescript
export const FeatureItem = React.memo(({ item, onEdit, onDelete }) => {
  // Component implementation
}, (prevProps, nextProps) => {
  // Custom comparison
  return prevProps.item.id === nextProps.item.id;
});
```

**useMemo for Computed Values**:
```typescript
const filteredItems = useMemo(() => {
  return items.filter(item => item.status === filter);
}, [items, filter]);
```

**useCallback for Stable Callbacks**:
```typescript
const handleEdit = useCallback((id: string) => {
  // Edit logic
}, [dependencies]);
```

**Code Splitting**:
```typescript
const FeatureForm = lazy(() => import('./FeatureForm'));

// Usage
<Suspense fallback={<Loading />}>
  <FeatureForm />
</Suspense>
```

---

### Accessibility Checklist

- [ ] Semantic HTML (article, section, nav, etc.)
- [ ] ARIA labels for interactive elements
- [ ] Keyboard navigation (Tab, Enter, Escape)
- [ ] Focus management (auto-focus on modals)
- [ ] Color contrast (WCAG AA minimum)
- [ ] Screen reader testing
- [ ] Form labels and error messages
- [ ] Skip links for navigation

---

### Success Criteria

**Functional**:
- [ ] All components render correctly
- [ ] User interactions work as expected
- [ ] Data flows properly through component tree
- [ ] API integration works

**Quality**:
- [ ] Test coverage ≥80%
- [ ] No console errors/warnings
- [ ] Accessibility score ≥90 (Lighthouse)
- [ ] TypeScript strict mode passes

**Performance**:
- [ ] First Contentful Paint < 1.5s
- [ ] No unnecessary re-renders
- [ ] Optimistic updates for better UX
- [ ] Proper loading states

---

### 🚀 Ready to Build?

**Standard workflow**: `/execute-plan`
**TDD workflow**: Follow micro-tasks with test-first approach
**Component-first**: Start with Phase 2 (presentational components)
```

---

## React/Next.js Best Practices

### Component Patterns

**Composition over Inheritance**:
```typescript
// Good: Composition
<Card>
  <Card.Header title="Feature" />
  <Card.Body>{content}</Card.Body>
</Card>

// Avoid: Complex inheritance
class FeatureCard extends BaseCard { ... }
```

**Custom Hooks for Logic Reuse**:
```typescript
// Extract logic into custom hooks
function useFeatureData(id: string) {
  const [data, setData] = useState(null);
  // ... fetch logic
  return { data, isLoading, error };
}
```

**Compound Components**:
```typescript
// Flexible, composable API
<Select value={value} onChange={onChange}>
  <Select.Trigger />
  <Select.Options>
    <Select.Option value="1">Option 1</Select.Option>
  </Select.Options>
</Select>
```

---

## Usage Examples

### Save and Execute Workflow

```bash
# 1. Create plan and save to file
/plan-react --output=plans/user-profile.md "add user profile feature"

# 2. Review the plan in plans/user-profile.md

# 3. Execute the plan
/execute-plan plans/user-profile.md
```

### Migration Planning

```bash
# For large migrations, save plan for team review
/plan-react --detailed --output=docs/migration-plan.md "migrate tools from old project"

# Team reviews the plan...

# Then execute when approved
/execute-plan docs/migration-plan.md
```

### TDD Workflow

```bash
# Create detailed TDD plan
/plan-react --tdd --output=plans/checkout.md "build checkout flow"

# Execute with test-first approach
/execute-plan plans/checkout.md
```

---

---

## Execution with Subagents

Plans from this command are **optimized for `/execute-plan`** with **subagent-driven, component-focused execution**.

**Reference**: `.claude/skills/methodology/executing-plans/SKILL.md`

### React Component Execution Methodology

**"Fresh subagent per component + review between components = consistent React patterns, high quality"**

This command generates plans with:
- **Context references** to existing React components
- **Implementation strategies** (not exact code)
- **Component-driven task groups** (test → implement → enhance per component)
- **Pattern adaptation** requirements (AI must study, not copy)

### How React Components Execute with Subagents

```
Your React Plan (from /plan-react)
  ↓
Save: /plan-react --output=plans/feature.md "description"
  ↓
Execute: /execute-plan plans/feature.md
  ↓
┌─────── Component Group 1: LoginForm
│ Subagent dispatched (fresh context)
│ 
│ Task 1: Write Test - LoginForm
│   - Reads Context: "Check RegisterForm.test.tsx"
│   - Studies: RegisterForm.test.tsx pattern
│   - Writes test: Following existing pattern
│   - Runs test → ❌ Fails (expected)
│   
│ Task 2: Minimal Implementation
│   - Reads Strategy: "Study RegisterForm.tsx"
│   - Opens RegisterForm.tsx
│   - Adapts pattern: Same structure, different props
│   - Implements: Follows project's React patterns
│   - Runs test → ✅ Passes
│   
│ Task 3: Enhance Component
│   - Adds: React Hook Form integration
│   - Adds: Zod validation
│   - Adds: Accessibility attributes
│   - Keeps tests passing
│   
│ Task 4: Additional Tests
│   - Adds: Interaction tests
│   - Adds: Validation tests
│   - Adds: Edge cases
│   
│ Task 5: Commit
│   - git commit -m "feat(auth): add LoginForm"
│ 
│ Returns: Component complete, 5 tests passing
└─────── ✅

  ↓

┌─────── Review Component Group 1
│ Reviewer subagent dispatched
│ 
│ Checks:
│   ✅ Tests actually test right behavior
│   ✅ Follows RegisterForm pattern (as required)
│   ✅ Uses same UI components (shadcn/ui)
│   ✅ Matches project's code style
│   ✅ Accessibility present
│   ✅ Props interface consistent
│   ✅ No copy-paste (adapted, not copied)
│ 
│ Returns: ✅ Approved (or issues to fix)
└─────── ✅

  ↓

┌─────── Component Group 2: Button
│ Fresh subagent (clean context)
│ Same process...
└─────── ✅

  ↓

Continue for all components...
  ↓
Final comprehensive review
  ↓
Complete! 🎉
```

### Why Subagents for React Components?

#### 1. **Pattern Consistency**
```
Subagent must:
- Study referenced component (e.g., RegisterForm)
- Understand its patterns
- Adapt to new component
- Match existing style

Result: All components feel like same codebase
```

#### 2. **Context References Work**
```
Plan says: "Check RegisterForm.tsx"
  ↓
Subagent MUST read RegisterForm.tsx
  ↓
Subagent learns: Form structure, validation, error display
  ↓
Subagent applies to LoginForm
  ↓
Consistent patterns across forms ✅
```

#### 3. **Component-Level Quality Gates**
```
Each component:
  → Implement
  → Review (catches issues early)
  → Fix if needed
  → Proceed to next

Bad component caught immediately, not after 10 components!
```

#### 4. **Fresh Context Per Component**
```
Component A: ✅ Done, context discarded
  ↓
Component B: Fresh subagent
  - No carry-over from Component A
  - No context pollution
  - Focused only on Component B
```

### React-Specific Review Checks

When reviewing React components, subagent checks:

**React Patterns**:
- ✅ Hooks used correctly (no rules violations)
- ✅ Props properly typed with TypeScript
- ✅ State management appropriate (local vs global)
- ✅ Effects have proper dependencies

**Component Structure**:
- ✅ Follows project's component pattern
- ✅ Uses existing UI components (don't reinvent)
- ✅ Proper component composition
- ✅ Consistent naming conventions

**Testing**:
- ✅ Tests use React Testing Library properly
- ✅ Tests focus on user behavior, not implementation
- ✅ Accessible queries used (getByRole, etc.)
- ✅ User interactions tested with userEvent

**Next.js Specifics** (if --nextjs):
- ✅ Server vs Client component correctly chosen
- ✅ Metadata exports for pages
- ✅ Loading/Error states handled
- ✅ App Router patterns followed

### Execution Flow Example

```bash
# Generate React plan with context references
/plan-react --output=plans/user-profile.md "add user profile feature"

# Plan includes:
# - Context: "Check UserSettings.tsx for pattern"
# - Strategy: "Study existing form components"
# - No exact code, just guidance

# Execute with subagents
/execute-plan plans/user-profile.md

# Execution:
# Group 1: ProfileForm component (5 micro-tasks)
#   → Subagent studies UserSettings.tsx
#   → Adapts pattern to ProfileForm
#   → Review catches style inconsistencies
#   → Fix applied
#   → ✅ Approved
#
# Group 2: ProfileDisplay component
#   → Fresh subagent (no ProfileForm context pollution)
#   → Studies existing display components
#   → Implements following pattern
#   → ✅ Approved
#
# Group 3: Profile page integration
#   → Fresh subagent
#   → Composes ProfileForm + ProfileDisplay
#   → ✅ Approved
#
# Final review: All components work together
# Result: Consistent React codebase ✅
```

### Benefits for React Development

1. **Consistent Component Patterns**
   - Every component follows project style
   - No "each component looks different" problem
   - Enforced through review gates

2. **Proper Pattern Adaptation**
   - AI must study existing code
   - Can't just copy-paste example
   - Learns project's specific patterns

3. **Quality Per Component**
   - Each component reviewed independently
   - Issues caught early
   - No cascading problems

4. **Fresh Context Benefits**
   - No confusion between components
   - Each component gets focused attention
   - Clean slate per component group

5. **React-Specific Reviews**
   - Hooks rules enforced
   - Accessibility checked
   - Next.js patterns verified
   - Testing best practices ensured

### Execution Tips

**Use Context Section Effectively**:
```markdown
# Good ✅
**Context**: 
- Pattern reference: `src/components/forms/RegisterForm.tsx`
- Uses: react-hook-form + zod (check RegisterForm's setup)
- Error display: See how RegisterForm shows validation errors

# Bad ❌
**Context**: 
- Make a form component
```

**Let Subagent Think**:
```markdown
# Good ✅
**Strategy**:
1. Study RegisterForm's structure
2. Copy validation approach
3. Match error display style

# Bad ❌ (too prescriptive)
**Implementation**:
```typescript
[100 lines of exact code]
```
```

**Group Related Tasks**:
```markdown
# Good ✅
Group 1: LoginForm component (test → implement → enhance → test → commit)
Group 2: LogoutButton component (test → implement → ...)

# Bad ❌
Task 1: Write all tests
Task 2: Implement all components
```

---

## Related Commands

```bash
/plan                  # General planning
/plan-feature          # Generic feature planning
/plan-detailed         # TDD micro-tasks only
/execute-plan [file]   # Execute saved plan
/test                  # Testing assistance
```

---

## Pro Tips

1. **Use `--output` for large features**: Save plan for review before execution
   ```bash
   /plan-react --output=plans/dashboard.md "admin dashboard"
   ```

2. **Combine flags for comprehensive plans**:
   ```bash
   /plan-react --tdd --nextjs --output=plans/blog.md "blog with SSR"
   ```

3. **Save plans in project docs**: Good for onboarding and reference
   ```bash
   /plan-react --output=docs/features/auth.md "authentication system"
   ```

4. **Review before executing**: Large plans benefit from team review
   ```bash
   # Create plan
   /plan-react --output=plans/complex-feature.md "feature description"
   
   # Team reviews plans/complex-feature.md
   
   # Execute when approved
   /execute-plan plans/complex-feature.md
   ```
