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

**Acceptance Criteria**:
- [ ] Specific checklist item 1
- [ ] Specific checklist item 2
- [ ] Specific checklist item 3

**Test** (if applicable):
```typescript
// Exact test code
it('should do something specific', () => {
  // ...
});
```

**Implementation** (if applicable):
```typescript
// Exact or example implementation
export function Component() {
  // ...
}
```

**Expected Result**: ✅ Tests pass / ❌ Tests fail
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
