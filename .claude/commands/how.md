# /how - Multi-Phase Codebase Exploration

## Purpose

Deep codebase exploration using a multi-phase SubAgent approach with per-phase documentation to prevent context loss. Each phase completes and documents before the next begins, ensuring comprehensive and detailed analysis.

**"How does this work?"** - Get comprehensive answers with organized documentation.

## Aliases

```bash
/how [topic]
/how-it-works [topic]
```

## Usage

```bash
# Basic usage
/how "authentication"

# With recent commits analysis
/how "payment flow" --recent=5

# With specific commit
/how "checkout" --commit=abc123

# With commit range
/how "user management" --commit=abc123..def456

# With source path
/how "payment flow" --source=old-app/payments

# Compare recent changes with plan
/how "authentication" --recent=3 --plan=plans/auth-update.md

# Custom output
/how "checkout" --output=docs/custom-analysis

# Specific phases only
/how "auth" --phases=1,2

# Comprehensive mode
/how "calculator" --comprehensive
```

## Arguments

- `$ARGUMENTS`: Feature name, component, or functionality to explore

## Flags

| Flag | Description | Example |
|------|-------------|---------|
| `--source=path` | Specify source directory to search | `--source=old-app/auth` |
| `--output=path` | Custom output folder | `--output=docs/auth-analysis` |
| `--phases=N,N` | Run specific phases only (1-2) | `--phases=1,2` |
| `--comprehensive` | Generate extra detailed reports | `--comprehensive` |
| `--skip-review` | Skip review gates between phases | `--skip-review` |
| `--recent=N` | Analyze changes from last N commits | `--recent=5` |
| `--commit=ID` | Analyze specific commit | `--commit=abc123` |
| `--commit=RANGE` | Analyze commit range | `--commit=abc123..def456` |
| `--plan=path` | Compare changes with plan file | `--plan=plans/feature.md` |

---

## Core Methodology

**Reference**: `.claude/skills/methodology/intelligent-planning/SKILL.md`

### Multi-Phase SubAgent Workflow

```
Phase 1: Discovery & Structure (Scout + Analyzer SubAgent)
    ↓ [Document NOW]
    docs/<topic>/phase-1-discovery-structure.md
    ↓ [Review Gate]
    
Phase 2: Code Analysis (Code-Reviewer SubAgent)
    ↓ [Document NOW]
    docs/<topic>/phase-2-analysis.md
    ✅ Complete
```

**Key Principle**: Each phase writes documentation IMMEDIATELY after completion to prevent context loss.

---

## Workflow

Exploring: **$ARGUMENTS**

{{ if --source provided }}
**Source directory**: `$SOURCE_PATH`
{{ else }}
**Source**: Auto-detect across codebase
{{ endif }}

**Output folder**: {{ if --output provided }}`$OUTPUT_PATH`{{ else }}`docs/[topic-slug]/`{{ endif }}

{{ if --phases provided }}
**Phases to run**: $PHASES
{{ else }}
**Phases to run**: All (1, 2)
{{ endif }}

{{ if --recent or --commit provided }}
**Git Analysis**:
{{ if --recent provided }}
- Last $RECENT commits
{{ endif }}
{{ if --commit provided }}
{{ if --commit contains '..' }}
- Commit range: $COMMIT
{{ else }}
- Specific commit: $COMMIT
{{ endif }}
{{ endif }}
{{ if --plan provided }}
- Plan comparison: $PLAN
{{ endif }}
{{ endif }}

---

{{ if --recent or --commit provided }}
### Pre-Phase 0: Change Analysis 🔍

**Goal**: Identify and analyze recent changes before exploration.

**Agent**: git-manager + researcher

**Steps**:

1. **Git Analysis** (git-manager):
   {{ if --recent provided }}
   - Run `git log --oneline -$RECENT` to get recent commit messages
   - Run `git diff --name-only HEAD~$RECENT..HEAD` to identify changed files
   {{ endif }}
   {{ if --commit provided }}
   {{ if --commit contains '..' }}
   - Run `git log --oneline $COMMIT` to get commit messages in range
   - Run `git diff --name-only $COMMIT` to identify changed files
   {{ else }}
   - Run `git show --name-only --format="" $COMMIT` for single commit
   {{ endif }}
   {{ endif }}

2. **Impact Assessment** (researcher):
   - Categorize changes: Business Logic, UI/UX, API, Configuration, Tests
   - Identify critical paths affected by changes
   - Detect breaking changes or risky modifications
   {{ if --plan provided }}
   - Read plan file and compare with actual changes
   - Calculate completion percentage
   {{ endif }}

3. **Change Prioritization**:
   ```
   Priority 1: Business Logic & API changes
   Priority 2: Database schema & security changes
   Priority 3: UI/UX changes
   Priority 4: Configuration & utility changes
   ```

**Output**: Change analysis report saved to `$OUTPUT_DIR/recent-changes-analysis.md`

---

{{ endif }}
### Setup: Prepare Output Folder

**Action**: Create output directory structure

```bash
# Generate topic slug from $ARGUMENTS
TOPIC_SLUG=$(echo "$ARGUMENTS" | tr '[:upper:]' '[:lower:]' | tr ' ' '-')

# Set output path
{{ if --output provided }}
OUTPUT_DIR="$OUTPUT_PATH"
{{ else }}
OUTPUT_DIR="docs/$TOPIC_SLUG"
{{ endif }}

# Create directory
mkdir -p "$OUTPUT_DIR"
```

**Created folder**: `$OUTPUT_DIR/`

---

### Phase 1: Discovery & Structure 🔍🏗️

**SubAgent**: Scout + Analyzer (`.claude/agents/scout.md`)

**Goal**: Find all code related to the topic AND map architecture, dependencies, and relationships

**Duration**: 30-45 minutes

---

**Dispatch Scout + Analyzer SubAgent**:

```markdown
Scout + Analyzer SubAgent - Discovery & Structure Mission

GOAL: Find all code related to "$ARGUMENTS" AND analyze its structure

INPUT:
- Search query: "$ARGUMENTS"
{{ if --source provided }}
- Search scope: $SOURCE_PATH
{{ else }}
- Search scope: Entire workspace
{{ endif }}
{{ if --recent or --commit provided }}
- Focus: Files changed in recent commits (see recent-changes-analysis.md)
{{ endif }}
{{ if --comprehensive }}
- Mode: Comprehensive (deep search)
{{ else }}
- Mode: Standard
{{ endif }}

---

SUBTASK 1.1: Search by File Name (5 min)

**Steps**:
1. Use find_by_name tool with patterns:
   - "$ARGUMENTS" (exact)
   - Variations: PascalCase, camelCase, kebab-case
   - Related terms

2. Search in common locations:
   - src/features/
   - app/
   - components/
   - lib/
   - old-app/ (if migration)
   - pages/

**Expected**: List of files matching by name

---

SUBTASK 1.2: Search by Content (5-10 min)

**Steps**:
1. Use grep_search for content:
   - Function/class names
   - Import statements
   - Component usage
   - API endpoints
   - Type definitions

2. Search patterns:
   - "import.*$ARGUMENTS"
   - "export.*$ARGUMENTS"
   - "function.*$ARGUMENTS"
   - "class.*$ARGUMENTS"

**Expected**: Files containing relevant code

---

SUBTASK 1.3: Create File Inventory (5 min)

**Steps**:
1. Combine search results
2. View file outlines for main files
3. Categorize files:
   - Main implementation
   - Components
   - Utilities/Helpers
   - Types/Interfaces
   - Tests
   - Configuration
   - API/Routes

4. Note file sizes and line counts

**Expected**: Complete categorized file inventory

---

SUBTASK 1.4: Map Component Hierarchy (10 min)

**Steps**:
1. Read main files identified in previous subtasks
2. Trace component tree:
   - Parent → Child relationships
   - Component composition
   - Layout structure

3. Create hierarchy diagram:
   ```
   Page/Route
   └── MainContainer
       ├── HeaderComponent
       ├── ContentArea
       │   ├── ItemList
       │   │   └── ItemCard
       │   └── FilterPanel
       └── FooterComponent
   ```

**Expected**: Complete component hierarchy tree

---

SUBTASK 1.5: Analyze Dependencies (10 min)

**Steps**:
1. External dependencies:
   - Check imports from node_modules
   - Note versions if visible
   - Identify key libraries

2. Internal dependencies:
   - Cross-file imports
   - Shared utilities
   - Type dependencies

3. Create dependency graph:
   ```
   FeatureMain
     ├→ react, react-hook-form (external)
     ├→ @/components/ui (internal)
     ├→ ./types (internal)
     └→ ./utils (internal)
   ```

**Expected**: Dependency map (external + internal)

---

SUBTASK 1.6: Identify Architecture Pattern (5-10 min)

**Steps**:
1. Determine architecture style:
   - Container/Presentational?
   - Feature-based?
   - Layered architecture?
   - MVC/MVVM?

2. State management approach:
   - useState/useReducer?
   - Context API?
   - Redux/Zustand/Jotai?
   - React Query/SWR?

3. Data flow pattern:
   - Unidirectional?
   - Props down, events up?
   - Global state?

**Expected**: Architecture pattern description

---

SUBTASK 1.7: Folder Structure Analysis (5 min)

**Steps**:
1. Map folder organization
2. Identify conventions:
   - Co-location?
   - Feature folders?
   - Type-based folders?

3. Create structure diagram

**Expected**: Folder structure with conventions noted

**Steps**:
1. Determine main entry files
2. Find where feature is initialized
3. Locate routing/navigation points
4. Identify export points

**Expected**: List of entry points and their purposes

---

OUTPUT: Return findings to main agent in structured format:

```markdown
📋 PHASE 1: DISCOVERY & STRUCTURE REPORT

Topic: "$ARGUMENTS"
{{ if --source provided }}Source: $SOURCE_PATH{{ else }}Source: Auto-detected{{ endif }}
Files Found: X files
Search Terms Used: [list]

---

## File Inventory

### Main Implementation (X files)
- `path/to/main.tsx` (250 lines) - Main component
- `path/to/feature.ts` (120 lines) - Core logic

### Components (X files)
- `path/to/ComponentA.tsx` (80 lines)
- `path/to/ComponentB.tsx` (60 lines)

### Utilities (X files)
- `path/to/utils.ts` (100 lines)
- `path/to/helpers.ts` (50 lines)

### Types (X files)
- `path/to/types.ts` (40 lines)

### Tests (X files)
- `path/to/feature.test.ts` (150 lines)

### API/Routes (X files)
- `api/route.ts` (70 lines)

### Configuration (X files)
- `config.ts` (30 lines)

---

## Entry Points

1. **Main Entry**: `path/to/index.tsx` - Feature initialization
2. **Route**: `/feature-route` - User access point
3. **Export**: `src/features/index.ts` - Public API

---

## Component Hierarchy

```
[Root Component/Page]
└── MainFeatureContainer
    ├── FeatureHeader
    ├── FeatureContent
    │   ├── ItemList
    │   │   └── Item
    │   └── FormPanel
    └── FeatureFooter
```

---

## Dependencies

### External Dependencies
- react@18.x - UI library
- react-hook-form@7.x - Form handling
- zod@3.x - Validation
- @tanstack/react-query@4.x - Server state
- [... more]

### Internal Dependencies
- `@/components/ui` - Shared UI components (Button, Input, etc.)
- `@/lib/api` - API client utilities
- `@/hooks/useAuth` - Authentication hook
- `@/types` - Shared type definitions

### Dependency Graph
```
FeatureMain (main.tsx)
  ├→ react-hook-form, zod (external)
  ├→ @/components/ui (internal)
  ├→ ./types (internal)
  ├→ ./utils (internal)
  └→ ./api (internal)
```

---

## Architecture Pattern

**Style**: Container/Presentational Pattern
- Containers: Handle logic, state, API calls
- Presentational: Pure UI components

**State Management**:
- Server State: React Query (@tanstack/react-query)
- Form State: react-hook-form
- UI State: useState in components
- Global State: [None/Context/Zustand]

**Data Flow**:
```
User Action → Component Event Handler → 
API Call → React Query → State Update → Re-render
```

Pattern: Unidirectional data flow

---

## Folder Structure

```
src/features/[feature]/
├── components/
│   ├── FeatureHeader.tsx
│   ├── FeatureContent.tsx
│   └── ItemCard.tsx
├── hooks/
│   ├── useFeature.ts
│   └── useFeatureQuery.ts
├── api/
│   └── featureApi.ts
├── types/
│   └── feature.types.ts
├── utils/
│   └── helpers.ts
└── index.tsx (main entry)
```

**Convention**: Feature-based organization with type-based subfolders

- Files by name: X files
- Files by content: Y files
- Total unique files: Z files
- Lines of code: ~N lines

---

PHASE 1 COMPLETE ✅
```
```

**Return**: Phase 1 report to main agent

---

**Main Agent Action: Document Phase 1**

**Immediately write documentation**: `$OUTPUT_DIR/phase-1-discovery-structure.md`

```markdown
# Phase 1: Discovery & Structure - $ARGUMENTS

**Date**: [Current date/time]
**Search Query**: "$ARGUMENTS"
{{ if --source provided }}**Source**: $SOURCE_PATH{{ else }}**Source**: Auto-detected{{ endif }}

---

## Files Found

### Main Implementation
- [`main.tsx`](file:///path/to/main.tsx) (250 lines)
  - Main component implementation
  
- [`feature.ts`](file:///path/to/feature.ts) (120 lines)
  - Core business logic

### Components
- [`ComponentA.tsx`](file:///path/to/ComponentA.tsx) (80 lines)
- [`ComponentB.tsx`](file:///path/to/ComponentB.tsx) (60 lines)

### Utilities
- [`utils.ts`](file:///path/to/utils.ts) (100 lines)
- [`helpers.ts`](file:///path/to/helpers.ts) (50 lines)

### Types & Interfaces
- [`types.ts`](file:///path/to/types.ts) (40 lines)

### Tests
- [`feature.test.ts`](file:///path/to/feature.test.ts) (150 lines)
  - Coverage: ~X%

### API & Routes
- [`api/route.ts`](file:///path/to/api/route.ts) (70 lines)

### Configuration
- [`config.ts`](file:///path/to/config.ts) (30 lines)

---

## Entry Points

1. **Main Entry**: [`index.tsx`](file:///path/to/index.tsx)
   - Feature initialization and setup

2. **User Route**: `/feature-route`
   - User-facing access point

3. **Public API**: Exported from `src/features/index.ts`

---

## Statistics

- **Total Files**: X files
- **Total Lines**: ~N lines
- **Test Coverage**: X%

---

## Component Hierarchy

```
PageComponent (app/route/page.tsx)
└── FeatureContainer (src/features/[feature]/index.tsx)
    ├── FeatureHeader (components/FeatureHeader.tsx)
    ├── FeatureContent (components/FeatureContent.tsx)
    │   ├── ItemList (components/ItemList.tsx)
    │   │   └── ItemCard (components/ItemCard.tsx)
    │   └── FormPanel (components/FormPanel.tsx)
    └── FeatureFooter (components/FeatureFooter.tsx)
```

---

## Dependencies

### External Libraries

- **react** (^18.x) - Core UI library
- **react-hook-form** (^7.x) - Form state management
- **zod** (^3.x) - Schema validation
- **@tanstack/react-query** (^4.x) - Server state management

### Internal Dependencies

- [`@/components/ui`](file:///path/to/components/ui) - Shared UI components
  - Button, Input, Modal, Card
  
- [`@/lib/api`](file:///path/to/lib/api) - API client utilities
  
- [`@/hooks/useAuth`](file:///path/to/hooks/useAuth) - Authentication hook

- [`@/types`](file:///path/to/types) - Shared TypeScript types

### Dependency Graph

```mermaid
graph TD
    A[FeatureContainer] --> B[react-hook-form]
    A --> C[zod]
    A --> D[@/components/ui]
    A --> E[./types]
    A --> F[./utils]
    A --> G[./api]
    G --> H[@tanstack/react-query]
```

---

## Architecture Pattern

### Design Pattern
**Container/Presentational Pattern**

- **Containers**: 
  - `FeatureContainer` - Manages logic, state, and API calls
  - Contains business logic and data fetching
  
- **Presentational Components**:
  - `FeatureHeader`, `ItemCard`, etc.
  - Pure UI components with props only
  - No direct API calls or complex logic

### State Management Strategy

| State Type | Solution | Used For |
|------------|----------|----------|
| Server State | React Query | API data, caching, refetching |
| Form State | react-hook-form | Form inputs, validation |
| UI State | useState | Modals, toggles, local UI state |
| Global State | [Context/Zustand/None] | [If applicable] |

### Data Flow

```
1. User interacts with UI component
   ↓
2. Event handler in container triggered
   ↓
3. Form validation (react-hook-form + zod)
   ↓
4. API Call via featureApi
   ↓
5. React Query manages request
   ↓
6. Server responds
   ↓
7. React Query updates cache
   ↓
8. Components re-render with new data
```

**Pattern**: Unidirectional data flow (props down, events up)

---

## Folder Structure

```
src/features/[feature-name]/
├── components/           # Feature-specific components
│   ├── FeatureHeader.tsx
│   ├── FeatureContent.tsx
│   ├── ItemList.tsx
│   ├── ItemCard.tsx
│   └── FormPanel.tsx
├── hooks/               # Custom hooks
│   ├── useFeature.ts
│   ├── useFeatureQuery.ts
│   └── useFeatureForm.ts
├── api/                 # API client functions
│   └── featureApi.ts
├── types/               # TypeScript types
│   └── feature.types.ts
├── utils/               # Utility functions
│   └── helpers.ts
└── index.tsx            # Main entry point
```

**Organizational Convention**: 
- Feature-based top level
- Type-based subfolders (components, hooks, api, etc.)
- Co-location of related files

---

## Integration Points

### API Endpoints
- `POST /api/feature` - Create
- `GET /api/feature` - Read
- `PUT /api/feature/:id` - Update
- `DELETE /api/feature/:id` - Delete

### Shared Components Used
- `@/components/ui/Button`
- `@/components/ui/Input`
- `@/components/ui/Modal`
- `@/components/ui/Card`

### Routes
- `/feature` - Main feature page
- `/feature/:id` - Detail view

---

## Next Phase

→ **Phase 2** will deep-dive into the code implementation, business logic, and patterns used.

*[phase-2-analysis.md](./phase-2-analysis.md) (not yet created)*
```

**File created**: `$OUTPUT_DIR/phase-1-discovery-structure.md` ✅

---

{{ if not --skip-review }}
**Review Gate 1**: Verify Discovery & Structure Completeness

```markdown
Review Phase 1 Results:
✅ All relevant files found?
✅ Entry points identified?
✅ File categories complete?
✅ Component hierarchy clear?
✅ Dependencies mapped?
✅ Architecture pattern identified?
✅ Folder structure understood?

{{ if incomplete }}
  → Re-run Scout + Analyzer SubAgent with refined parameters
{{ else }}
  → Proceed to Phase 2 ✅
{{ endif }}
```
{{ else }}
**Review skipped** - Proceeding to Phase 2
{{ endif }}

---

### Phase 2: Code Analysis 💻

**SubAgent**: Code-Reviewer (`.claude/agents/code-reviewer.md`)

**Goal**: Deep-dive into implementation details, business logic, and patterns

**Duration**: 30-45 minutes

---

**Dispatch Code-Reviewer SubAgent**:

```markdown
Code-Reviewer SubAgent - Deep Analysis

GOAL: Analyze implementation details for "$ARGUMENTS"

INPUT:
- Phase 1 report (file list + structure)
- Files to review: [main files from Phase 1]

---

SUBTASK 2.1: Extract Business Logic (15-20 min)

**Steps**:
1. Read main implementation files
2. Identify critical business logic:
   - Calculation functions
   - Algorithms
   - Business rules
   - Validation logic
   - Complex operations

3. Document with line numbers:
   ```
   Function: calculateTotalPrice()
   Location: utils/pricing.ts (lines 45-89)
   Purpose: Calculates final price with discounts and tax
   Algorithm:
     1. Apply item discounts
     2. Sum items
     3. Apply coupon if valid
     4. Calculate tax
     5. Add shipping
   Critical: Tax rate is region-specific (MUST PRESERVE)
   ```

4. Note constants and configuration:
   ```
   TAX_RATES = {
     CA: 0.0725,
     NY: 0.08875,
     ...
   }
   ```

**Expected**: Complete business logic documentation with line references

---

SUBTASK 2.2: Analyze Data Models (10 min)

**Steps**:
1. Read type definitions
2. Document interfaces/types:
   ```typescript
   interface Product {
     id: string;
     name: string;
     price: number;
     category: Category;
     metadata?: Record<string, any>;
   }
   ```

3. Note validation schemas:
   ```typescript
   const productSchema = z.object({
     name: z.string().min(1).max(100),
     price: z.number().positive(),
     ...
   });
   ```

4. Identify data transformations

**Expected**: Complete data model documentation

---

SUBTASK 2.3: Identify Patterns & Practices (10 min)

**Steps**:
1. Error handling patterns:
   - try/catch usage
   - Error boundaries
   - User feedback

2. Testing approach:
   - Testing library used
   - Test coverage
   - Test patterns

3. Performance optimizations:
   - Memoization
   - Lazy loading
   - Code splitting

4. Security measures:
   - Input sanitization
   - Authentication checks
   - Authorization

**Expected**: Patterns and practices documentation

---

SUBTASK 2.4: Note Key Insights (5-10 min)

**Steps**:
1. Interesting implementations
2. Potential issues or tech debt
3. TODO/FIXME comments
4. Clever solutions
5. Areas for improvement

**Expected**: Key insights and observations

---

OUTPUT: Return analysis to main agent:

```markdown
📋 PHASE 2: CODE ANALYSIS REPORT

Topic: "$ARGUMENTS"
Based on: phase-1-discovery-structure.md

---

## Business Logic

### 1. Price Calculation ⚠️ CRITICAL
**Location**: `src/utils/pricing.ts` (lines 45-89)

**Purpose**: Calculate final price with discounts, tax, and shipping

**Algorithm**:
1. Apply item-level discounts
2. Sum all items
3. Apply coupon code (if valid)
4. Calculate region-specific tax
5. Add shipping cost

**Constants**:
```typescript
const TAX_RATES = {
  CA: 0.0725,
  NY: 0.08875,
  TX: 0.0625,
  // ... more
};

const SHIPPING_THRESHOLDS = {
  FREE_SHIPPING: 50,
  FLAT_RATE: 5.99,
};
```

**MUST PRESERVE**: Tax rates are legal requirements, cannot change arbitrarily

---

### 2. Coupon Validation
**Location**: `src/utils/coupons.ts` (lines 20-55)

**Logic**:
- Check expiry date
- Verify usage limits
- Validate minimum purchase amount
- Apply discount (percentage or fixed)

---

### 3. Inventory Check
**Location**: `src/utils/inventory.ts` (lines 10-30)

**Logic**: Prevent overselling by checking stock before purchase

---

## Data Models

### Product Interface
**Location**: `src/types/product.ts` (lines 5-20)

```typescript
interface Product {
  id: string;
  name: string;
  description: string;
  price: number;          // in cents
  currency: 'USD';
  category: Category;
  inStock: boolean;
  quantity: number;
  imageUrl?: string;
  metadata?: Record<string, any>;
}
```

### Order Interface
**Location**: `src/types/order.ts` (lines 8-25)

```typescript
interface Order {
  id: string;
  userId: string;
  items: OrderItem[];
  subtotal: number;
  tax: number;
  shipping: number;
  total: number;
  status: OrderStatus;
  createdAt: Date;
  updatedAt: Date;
}
```

### Validation Schemas
**Location**: `src/schemas/product.schema.ts`

Uses Zod for validation:
- Name: 1-100 characters
- Price: Positive number
- Quantity: Integer >= 0

---

## Patterns & Practices

### Error Handling
✅ **Pattern**: Try/catch with user-friendly messages

```typescript
try {
  await createOrder(orderData);
  toast.success('Order placed successfully!');
} catch (error) {
  if (error instanceof ValidationError) {
    toast.error(error.message);
  } else {
    toast.error('Something went wrong. Please try again.');
  }
}
```

✅ **Error Boundary**: Used at feature level
⚠️ **API Errors**: Some endpoints missing proper error handling

---

### Testing

**Framework**: Jest + React Testing Library

**Coverage**: ~65%

**What's Tested**:
- ✅ Component rendering
- ✅ User interactions
- ✅ Form validation
- ✅ Price calculation logic

**What's NOT Tested**:
- ❌ Error boundaries
- ❌ Edge cases in coupon validation
- ❌ Race conditions in inventory check

---

### Performance

✅ **Memoization**: Used in expensive calculations
```typescript
const totalPrice = useMemo(
  () => calculateTotalPrice(items, tax, shipping),
  [items, tax, shipping]
);
```

✅ **Lazy Loading**: Components loaded on-demand
```typescript
const CheckoutModal = lazy(() => import('./CheckoutModal'));
```

⚠️ **Opportunity**: Could add virtual scrolling for long product lists

---

### Security

✅ **Input Sanitization**: User inputs validated with Zod
✅ **Auth Check**: API routes protected with middleware
⚠️ **XSS Prevention**: React escapes by default (no dangerouslySetInnerHTML used)
❌ **Missing**: CSRF protection on mutations

---

## Key Insights

### Good Practices
- ✅ Type-safe with TypeScript
- ✅ Comprehensive validation schemas
- ✅ Clear separation of concerns
- ✅ Reusable utility functions

### Technical Debt
- ⚠️ Some files > 200 lines (could be split)
- ⚠️ TODO comments about refactoring
- ⚠️ Test coverage could be higher (target: 80%+)

### Interesting Implementations
- 💡 Price stored in cents (avoids floating-point issues)
- 💡 Optimistic UI updates for better UX
- 💡 Coupon system with flexible discount types

### Areas for Improvement
- Add E2E tests for critical flows
- Implement CSRF protection
- Increase test coverage
- Extract large components
- Add API rate limiting

---

## TODO/FIXME Comments Found

1. `pricing.ts:67` - "TODO: Add support for bundle discounts"
2. `inventory.ts:25` - "FIXME: Handle race condition for concurrent purchases"
3. `order.ts:120` - "TODO: Add order history pagination"

---

PHASE 2 COMPLETE ✅
```
```

**Return**: Phase 2 report to main agent

---

**Main Agent Action: Document Phase 2**

**Immediately write documentation**: `$OUTPUT_DIR/phase-2-analysis.md`

```markdown
# Phase 2: Code Analysis - $ARGUMENTS

**Date**: [Current date/time]
**Based on**: [phase-1-discovery-structure.md](./phase-1-discovery-structure.md)

---

## Business Logic

### 1. Price Calculation ⚠️ CRITICAL

**Location**: [`src/utils/pricing.ts`](file:///path/to/pricing.ts) (lines 45-89)

**Purpose**: Calculate final price with all discounts, taxes, and fees

**Algorithm**:
1. Apply item-level discounts
2. Sum all items to get subtotal
3. Apply coupon code discount (if valid)
4. Calculate region-specific sales tax
5. Add shipping cost based on rules

**Code Reference**:
```typescript
function calculateTotalPrice(items, region, coupon) {
  const itemsTotal = items.reduce((sum, item) => 
    sum + (item.price * item.quantity * (1 - item.discount)), 0
  );
  
  const afterCoupon = applyCoupon(itemsTotal, coupon);
  const tax = afterCoupon * TAX_RATES[region];
  const shipping = calculateShipping(afterCoupon);
  
  return afterCoupon + tax + shipping;
}
```

**Constants** (MUST PRESERVE):
```typescript
const TAX_RATES = {
  CA: 0.0725,   // California sales tax
  NY: 0.08875,  // New York sales tax
  TX: 0.0625,   // Texas sales tax
  // ... (region-specific, legal requirement)
};
```

> [!CAUTION]
> **CRITICAL**: Tax rates are legal requirements. Do not modify without legal review.

---

### 2. Coupon Validation

**Location**: [`src/utils/coupons.ts`](file:///path/to/coupons.ts) (lines 20-55)

**Validation Rules**:
- ✅ Check expiry date
- ✅ Verify usage limits (per user, globally)
- ✅ Validate minimum purchase amount
- ✅ Ensure coupon is active
- ✅ Apply correct discount type (percentage or fixed)

**Edge Cases Handled**:
- Expired coupons → Error message
- Exceeded usage → "Coupon no longer available"
- Below minimum → "Minimum purchase $X required"

---

### 3. Inventory Management

**Location**: [`src/utils/inventory.ts`](file:///path/to/inventory.ts) (lines 10-30)

**Purpose**: Prevent overselling

**Logic**:
```typescript
async function checkInventory(productId, requestedQty) {
  const product = await getProduct(productId);
  
  if (!product.inStock) {
    throw new OutOfStockError();
  }
  
  if (product.quantity < requestedQty) {
    throw new InsufficientStockError(product.quantity);
  }
  
  return true;
}
```

> [!WARNING]
> **Issue Found**: `FIXME` comment at line 25 mentions race condition for concurrent purchases. This should be addressed with database-level locking or optimistic concurrency control.

---

## Data Models

### Product Type

**Location**: [`src/types/product.ts`](file:///path/to/types/product.ts)

```typescript
interface Product {
  id: string;              // UUID
  name: string;            // 1-100 chars
  description: string;     // Markdown supported
  price: number;           // Stored in cents (e.g., 1099 = $10.99)
  currency: 'USD';         // Currently only USD
  category: ProductCategory;
  inStock: boolean;
  quantity: number;        // Available stock
  imageUrl?: string;       // Optional product image
  metadata?: Record<string, any>;  // Extensible data
}

type ProductCategory = 'electronics' | 'clothing' | 'books' | 'home' | 'other';
```

> [!TIP]
> **Smart Design**: Prices stored in cents to avoid floating-point arithmetic issues. Always multiply displayed price by 100 for storage.

---

### Order Type

**Location**: [`src/types/order.ts`](file:///path/to/types/order.ts)

```typescript
interface Order {
  id: string;
  userId: string;
  items: OrderItem[];
  subtotal: number;        // Before tax/shipping
  tax: number;
  shipping: number;
  total: number;           // Final amount
  status: OrderStatus;
  createdAt: Date;
  updatedAt: Date;
}

type OrderStatus = 
  | 'pending' 
  | 'processing' 
  | 'shipped' 
  | 'delivered' 
  | 'cancelled';

interface OrderItem {
  productId: string;
  quantity: number;
  priceAtPurchase: number; // Preserve price even if product price changes
}
```

---

### Validation Schemas

**Location**: [`src/schemas/product.schema.ts`](file:///path/to/schemas/product.schema.ts)

Uses **Zod** for runtime validation:

```typescript
const productSchema = z.object({
  name: z.string().min(1).max(100),
  description: z.string().max(5000),
  price: z.number().positive().int(), // Must be positive integer (cents)
  category: z.enum(['electronics', 'clothing', 'books', 'home', 'other']),
  quantity: z.number().int().min(0),
  imageUrl: z.string().url().optional(),
});
```

**Client**: react-hook-form uses this schema for form validation  
**Server**: API routes use same schema (shared validation)

---

## Patterns & Practices

### Error Handling Strategy

**Pattern**: Try/catch with contextual error messages

```typescript
try {
  const order = await createOrder(orderData);
  toast.success('Order placed successfully!');
  router.push(`/orders/${order.id}`);
} catch (error) {
  if (error instanceof ValidationError) {
    toast.error(`Validation failed: ${error.message}`);
  } else if (error instanceof OutOfStockError) {
    toast.error('Item out of stock. Please adjust quantity.');
  } else {
    toast.error('Something went wrong. Please try again.');
    logger.error('Order creation failed', error);
  }
}
```

**Error Boundaries**: Used at feature level to catch React errors

**Issues Found**:
- ⚠️ Some API endpoints don't have proper error handling
- ⚠️ Network errors not always handled gracefully

---

### Testing Approach

**Framework**: Jest + React Testing Library

**Current Coverage**: ~65%

**Test Structure**:
```
__tests__/
├── components/
│   ├── ProductCard.test.tsx
│   └── CheckoutForm.test.tsx
├── utils/
│   ├── pricing.test.ts
│   └── coupons.test.ts
└── integration/
    └── checkout-flow.test.tsx
```

**What's Tested**:
- ✅ Component rendering
- ✅ User interactions (click, type, submit)
- ✅ Form validation
- ✅ Price calculation logic
- ✅ Coupon validation rules

**What's NOT Tested**:
- ❌ Error boundaries behavior
- ❌ Edge cases in coupon stacking
- ❌ Race conditions in inventory
- ❌ E2E checkout flow

**Recommendation**: Increase coverage to 80%+, add E2E tests with Playwright

---

### Performance Optimizations

**Memoization**:
```typescript
const totalPrice = useMemo(
  () => calculateTotalPrice(items, region, coupon),
  [items, region, coupon]
);
```

**Lazy Loading**:
```typescript
const CheckoutModal = lazy(() => import('./CheckoutModal'));
const ProductDetailPage = lazy(() => import('./ProductDetailPage'));
```

**Code Splitting**: Route-based splitting with Next.js dynamic imports

**Opportunities**:
- 💡 Add virtual scrolling for product lists (100+ items)
- 💡 Implement debouncing on search input
- 💡 Prefetch product details on card hover

---

### Security Measures

✅ **Input Sanitization**: All user inputs validated with Zod schemas  
✅ **Authentication**: API routes protected with middleware  
✅ **XSS Prevention**: React escapes output by default (no `dangerouslySetInnerHTML` used)  
⚠️ **CSRF**: Missing CSRF token validation on mutations  
⚠️ **Rate Limiting**: Not implemented on API routes

**Recommendation**: Add CSRF protection and API rate limiting

---

## Key Insights

### Good Practices Found

- ✅ **Type-safe**: Full TypeScript with strict mode
- ✅ **Validated data**: Unified Zod schemas for client + server
- ✅ **Clean architecture**: Clear separation of concerns
- ✅ **Reusable utilities**: DRY principle followed
- ✅ **Smart data modeling**: Prices in cents, preserve historical data

### Technical Debt

- ⚠️ [`CheckoutForm.tsx`](file:///path/to/CheckoutForm.tsx) is 280 lines (should split)
- ⚠️ Several TODO comments for missing features
- ⚠️ Test coverage at 65% (target 80%+)
- ⚠️ No E2E tests

### Interesting Implementations

- 💡 **Optimistic UI**: Order creation shows immediate feedback, reverts on error
- 💡 **Price history**: `priceAtPurchase` preserves price for orders even if product price changes
- 💡 **Flexible coupons**: Supports both percentage and fixed amount discounts

### Areas for Improvement

1. **Add E2E tests** for critical user flows (browse → add to cart → checkout)
2. **Implement CSRF protection** on mutations
3. **Increase test coverage** to 80%+
4. **Split large components** (CheckoutForm, ProductList)
5. **Add API rate limiting** to prevent abuse
6. **Fix race condition** in inventory check (use database locks)

---

## TODO/FIXME Comments

Found 3 items requiring attention:

1. **`pricing.ts` line 67**  
   `// TODO: Add support for bundle discounts (buy 2 get 1 free)`

2. **`inventory.ts` line 25**  
   `// FIXME: Handle race condition for concurrent purchases of last item`

3. **`order.ts` line 120**  
   `// TODO: Add pagination for order history (currently loads all)`

---

**File created**: `$OUTPUT_DIR/phase-2-analysis.md` ✅

---

{{ if not --skip-review }}
**Review Gate 2**: Verify Code Analysis

```markdown
Review Phase 2 Results:
✅ Business logic documented with line numbers?
✅ Data models completely described?
✅ Patterns and practices identified?
✅ Key insights captured?

{{ if incomplete }}
  → Re-run Code-Reviewer with specific focus areas
{{ else }}
  → Analysis Complete ✅
{{ endif }}
```
{{ else }}
**Review skipped** - Analysis Complete
{{ endif }}

---

## Completion

```markdown
✅ EXPLORATION COMPLETE

Topic: "$ARGUMENTS"
Output Folder: $OUTPUT_DIR/

Documentation Created:
{{ if --recent or --commit provided }}
- recent-changes-analysis.md (Git change analysis and impact assessment)
{{ endif }}
- phase-1-discovery-structure.md (File inventory, architecture, dependencies)
- phase-2-analysis.md (Code deep-dive, business logic)
- README.md (Quick access to overview)

---

Quick Access:
📂 Open folder: $OUTPUT_DIR/
📄 Read overview: $OUTPUT_DIR/README.md
{{ if --recent or --commit provided }}
📄 View changes: $OUTPUT_DIR/recent-changes-analysis.md
{{ endif }}

Next Steps:
- Review documentation for complete understanding
- Use findings to plan modifications
- Reference phase docs when making changes
{{ if --recent and --plan provided }}
- Compare actual changes with planned changes
- Address any deviations from the plan
{{ endif }}
```

---

**Create README**: `$OUTPUT_DIR/README.md`

```markdown
# $ARGUMENTS - Codebase Exploration

**Explored**: [Current date/time]

## Quick Links

- [Phase 1: Discovery & Structure](./phase-1-discovery-structure.md) - Files, architecture, dependencies
- [Phase 2: Code Analysis](./phase-2-analysis.md) - Business logic, patterns, insights

## Overview

[Brief 2-3 sentence summary of what was explored]

## Key Findings

### Architecture
- [Key architecture pattern identified]

### Technologies
- [Main technologies/libraries used]

### Critical Business Logic
- [Most important business logic identified]

### Recommendations
- [Top 2-3 recommendations from analysis]

## Next Steps

After reviewing this exploration, you can:

1. **Apply patterns** - Use `/apply "$ARGUMENTS" --to="new-feature"` to replicate architecture
2. **Extend feature** - Use `/extend "$ARGUMENTS" --with="new-capability"` to add functionality
3. **Generate tests** - Use `/test-from "$ARGUMENTS"` to create comprehensive tests
4. **Refactor code** - Use `/refactor-from "$ARGUMENTS"` to improve code quality

---

*Generated by `/how` command*
```

**File created**: `$OUTPUT_DIR/README.md` ✅

---

## Tips for Best Results

1. **Be specific**: "user authentication flow" > "auth"
2. **Use --source** when you know where code lives
3. **Run all phases**: Each builds on previous ones
4. **Review phase docs**: Each has focused, detailed info
5. **Use comprehensive mode**: For complex features needing extra detail

---

## What to Do After Exploration

After `/how` completes and generates comprehensive documentation, you can:

### 1. Apply Patterns to New Features

Use `/apply` to clone the architecture to a new implementation:

```bash
# Apply authentication patterns to user profile
/apply "authentication" --to="user-profile"

# Apply specific validation pattern
/apply "calculator" --to="converter" --pattern="validation"

# Preview changes first
/apply "payment" --to="subscription" --preview
```

**When to use**:
- Building similar feature with same architecture
- Replicating proven patterns across codebase
- Ensuring consistency in implementation

### 2. Extend Existing Features

Use `/extend` to add new capabilities:

```bash
# Add 2FA to authentication
/extend "authentication" --with="2FA support"

# Add refund workflow to payments
/extend "payment" --feature="refund workflow"

# Preview plan first
/extend "calculator" --with="currency conversion" --preview
```

### 3. Generate Comprehensive Tests

Use `/test-from` to create tests from business logic:

```bash
# Generate tests for explored code
/test-from "calculator" --coverage=90

# Focus on specific areas
/test-from "auth" --type="integration"

# Preview test plan first
/test-from "payment" --preview
```

### 4. Refactor Based on Insights

Use `/refactor-from` to improve code quality:

```bash
# Extract business logic to utils
/refactor-from "calculator" --goal="Extract to utils"

# Improve error handling
/refactor-from "auth" --improve="error-handling"

# Preview refactoring plan
/refactor-from "payment" --preview
```

---

## Complete Workflow Example

```bash
# Step 1: Explore existing code
/how "authentication"
→ docs/authentication/ created with phase-1-discovery-structure.md and phase-2-analysis.md

# Step 2: Apply to new feature
/apply "authentication" --to="user-management"
→ src/features/user-management/ created with same patterns

# Step 3: Extend with new capability
/extend "user-management" --with="role permissions"
→ Adds role-based access control

# Step 4: Generate comprehensive tests
/test-from "user-management" --coverage=85
→ Full test suite created

# Step 5: Deploy
/ship "New user management feature with auth patterns"
```

---

## Related Commands

```bash
/auto-plan [feature]         # Auto-explore + plan migration
/plan-feature [feature]      # Plan new implementation
/plan-refactor [feature]     # Plan refactoring
```

---

## Examples

### Example 1: Explore Authentication

```bash
/how "authentication" --source=src/features/auth

# Output:
# docs/authentication/
# ├── phase-1-discovery-structure.md (12 files found, JWT-based architecture)
# ├── phase-2-analysis.md (Token generation logic, session mgmt)
# └── README.md (Quick overview)
```

### Example 2: Step-by-Step Exploration

```bash
# Run only discovery & structure first
/how "checkout flow" --phases=1

# Review results, then continue with code analysis
/how "checkout flow" --phases=2
```

### Example 3: Comprehensive Deep Dive

```bash
/how "salary calculator" --comprehensive

# Generates extra-detailed reports in each phase
# Useful for complex business logic
```

---

**Pro Tip**: Each phase writes documentation immediately, so you can review partial results without waiting for the entire exploration to complete!

