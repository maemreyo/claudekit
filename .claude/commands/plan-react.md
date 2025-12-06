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

### Phase 1: Component Planning

1. **Break Down UI**
   - Identify component hierarchy
   - Determine which components are presentational vs container
   - Plan component composition

2. **State Strategy**
   - Local state (useState, useReducer)
   - Server state (React Query, SWR)
   - Global state (Context, Zustand, Redux)
   - Form state (React Hook Form, Formik)

3. **Data Flow**
   - Props drilling analysis
   - Context providers needed
   - API integration points

### Phase 2: Testing Strategy

1. **Component Tests** (React Testing Library)
   - User interactions
   - Render variations
   - Accessibility

2. **Hook Tests**
   - Custom hooks logic
   - State updates
   - Side effects

3. **Integration Tests**
   - API mocking (MSW)
   - User flows
   - Navigation

### Phase 3: Implementation Plan

Generate tasks following Component-Driven Development:
1. **Bottom-up**: Build leaf components first
2. **Test-first**: Write tests before implementation
3. **Iterative**: Add features incrementally

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

### Tasks (Component-Driven Development)

#### Phase 1: Foundation & Types [Xh] 🏗️
| # | Task | Size | Est | Test |
|---|------|------|-----|------|
| 1 | Define TypeScript types/interfaces | S | 20m | - |
| 2 | Set up API client/functions | S | 25m | Mock |
| 3 | Create custom hooks | M | 40m | ✅ |

#### Phase 2: Presentational Components [Xh] 🎨
| # | Task | Size | Est | Test |
|---|------|------|-----|------|
| 4 | Build FeatureItem component | M | 35m | ✅ |
| 5 | Build Button component | S | 20m | ✅ |
| 6 | Build Input component | S | 20m | ✅ |
| 7 | Build FeatureHeader | S | 25m | ✅ |

**Testing Focus**: Rendering, props, user events

#### Phase 3: Container Components [Xh] 🔗
| # | Task | Size | Est | Test |
|---|------|------|-----|------|
| 8 | Build FeatureList container | M | 45m | ✅ |
| 9 | Build FeatureForm container | M | 45m | ✅ |
| 10 | Build FeatureContainer | M | 40m | ✅ |

**Testing Focus**: State, side effects, integration

#### Phase 4: Integration & Routes [Xh] 🚀
| # | Task | Size | Est | Test |
|---|------|------|-----|------|
| 11 | Create page/route | S | 25m | ✅ |
| 12 | Connect API endpoints | M | 35m | Mock |
| 13 | Add loading/error states | S | 20m | ✅ |

#### Phase 5: Polish & Optimization [Xh] ✨
| # | Task | Size | Est | Test |
|---|------|------|-----|------|
| 14 | Add animations (Framer Motion) | S | 25m | - |
| 15 | Optimize renders (memo, useMemo) | S | 20m | - |
| 16 | Accessibility audit (a11y) | M | 30m | ✅ |
| 17 | Responsive design tweaks | S | 25m | - |

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
