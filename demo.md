# ⚛️ React Plan: Tools Migration from Old Project

**Stack**: React 19 / Next.js 15.5.4 | **Estimate**: 32-40 hours | **TDD**: Yes

## Overview

Migration plan for moving all financial calculators from the old project's `/cong-cu` section to the new project's `/src/app/[locale]/tools/` structure while preserving all functionality and following the new project's patterns.

## Component Architecture Analysis

### Existing New Project Structure
```
src/app/[locale]/tools/
├── page.tsx                     # Tools listing page
├── layout.tsx                   # Tools layout
├── loan-calculator/
│   └── page.tsx                 # Already exists
├── salary-converter/
│   └── page.tsx                 # Already exists
└── savings-calculator/
    └── page.tsx                 # Already exists
```

### Target Structure After Migration
```
src/app/[locale]/tools/
├── page.tsx                     # Updated with all tools
├── loan-calculator/
│   └── page.tsx                 # Enhanced
├── salary-converter/
│   └── page.tsx                 # Enhanced
├── savings-calculator/
│   └── page.tsx                 # Enhanced
├── savings-interest-comparison/  # NEW: Tính Lãi Tiền Gửi
│   └── page.tsx
├── gross-to-net-calculator/     # UPDATED: Tính Lương Gross-Net
│   └── page.tsx
├── net-to-gross-calculator/     # NEW: Tính Lương Net-Gross
│   └── page.tsx
└── loan-amortization/           # UPDATED: Tính Toán Khoản Vay
    └── page.tsx
```

### Component Breakdown

| Tool | Old Component | New Location | Status | Notes |
|------|---------------|--------------|---------|-------|
| Savings Calculator | SavingTool | /savings-calculator | ✅ Exists | Needs enhancement for bank comparison |
| Gross to Net Salary | SalaryConversion (GROSS_TO_NET) | /gross-to-net-calculator | 🔄 Update | Already exists as salary-converter |
| Net to Gross Salary | SalaryConversion (NET_TO_GROSS) | /net-to-gross-calculator | ➕ New | Separate tool needed |
| Loan Calculator | InterestRateTool | /loan-amortization | 🔄 Update | Already exists, needs amortization table |
| Bank Interest Rates | Part of SavingTool | /savings-interest-comparison | ➕ New | Extract as separate tool |

---

## State Management Strategy

### Current New Project Patterns
```typescript
// Zustand store example
interface FinancialToolsStore {
  // Tool-specific states
  loanCalculator: LoanCalculatorState;
  salaryConverter: SalaryConverterState;

  // Actions
  updateLoanParams: (params: LoanParams) => void;
  calculateLoan: () => void;
}

// React Query for API calls
const { data: bankRates, isLoading } = useQuery({
  queryKey: ['bank-rates'],
  queryFn: fetchBankRates
});
```

### Migration State Strategy
```typescript
// Extend existing store for new tools
interface ToolsStore extends FinancialToolsStore {
  // New tool states
  savingsComparison: SavingsComparisonState;
  netToGross: NetToGrossState;
  loanAmortization: LoanAmortizationState;
}

// Local state for form data
const [formData, setFormData] = useState<SavingsFormData>({
  amount: 0,
  period: 12,
  type: 'online'
});
```

---

## File Structure for Migration

### Components to Create/Update
```
src/components/financial-tools/
├── SavingsCalculator.tsx         # UPDATE: Add bank comparison
├── SalaryConverter.tsx          # UPDATE: Already exists, verify features
├── LoanCalculator.tsx           # UPDATE: Add amortization table
├── BankInterestComparison.tsx   # NEW: Bank rates comparison
├── NetToGrossCalculator.tsx     # NEW: Reverse salary calculation
├── LoanAmortizationTable.tsx    # NEW: Detailed payment schedule
└── common/                      # Shared components
    ├── CalculatorLayout.tsx     # NEW: Common layout wrapper
    ├── ResultsCard.tsx          # NEW: Standardized results display
    ├── ComparisonTable.tsx      # NEW: Bank/product comparison
    └── TaxBreakdown.tsx         # NEW: Vietnamese tax calculation display
```

### Business Logic
```
src/lib/financial/
├── loan-calculations.ts         # UPDATE: Add amortization functions
├── salary-calculations.ts       # UPDATE: Ensure Vietnamese tax laws
├── savings-calculations.ts      # NEW: Bank comparison logic
└── tax-calculations.ts          # NEW: Detailed Vietnamese tax system
```

### Data Layer
```
src/lib/financial-data/
├── bank-interest-rates.ts       # NEW: Vietnamese bank rates
├── tax-brackets.ts              # NEW: 2024 Vietnamese tax brackets
├── insurance-rates.ts           # NEW: Social insurance rates
└── regional-minimum-wage.ts     # NEW: Vietnamese regional wages
```

---

## Tasks (Component-Driven Development)

### Phase 1: Foundation & Data Setup [4h] 🏗️
| # | Task | Size | Est | Test |
|---|------|------|-----|------|
| 1 | Create Vietnamese financial data modules | M | 60m | Mock |
| 2 | Extend Zustand store for new tools | M | 45m | ✅ |
| 3 | Create shared calculation utilities | M | 45m | ✅ |
| 4 | Set up API endpoints for bank rates | M | 60m | Mock |

### Phase 2: Shared Components [6h] 🎨
| # | Task | Size | Est | Test |
|---|------|------|-----|------|
| 5 | Build CalculatorLayout wrapper | M | 45m | ✅ |
| 6 | Build ResultsCard component | M | 45m | ✅ |
| 7 | Build ComparisonTable component | M | 60m | ✅ |
| 8 | Build TaxBreakdown component | M | 60m | ✅ |
| 9 | Build InputField with Vietnamese formatting | M | 45m | ✅ |
| 10 | Build CurrencyDisplay component | S | 30m | ✅ |

**Testing Focus**: Component rendering, props handling, Vietnamese number formatting

### Phase 3: Bank Interest Comparison Tool [5h] 🏦
| # | Task | Size | Est | Test |
|---|------|------|-----|------|
| 11 | Create /savings-interest-comparison/page | S | 20m | ✅ |
| 12 | Build BankInterestComparison main component | L | 120m | ✅ |
| 13 | Implement filtering and sorting logic | M | 60m | ✅ |
| 14 | Add pagination for bank list | M | 45m | ✅ |
| 15 | Integrate with real bank rate API | M | 45m | Mock |

**Features to Implement**:
- Filter by amount (10-1000 triệu VND)
- Filter by period (1, 3, 6, 9, 12, 18, 24 tháng)
- Filter by type (Tại quầy/Gửi trực tuyến)
- Sort by interest rate or bank name
- Paginated results with 10 items per page

### Phase 4: Net to Gross Calculator [4h] 💱
| # | Task | Size | Est | Test |
|---|------|------|-----|------|
| 16 | Create /net-to-gross-calculator/page | S | 20m | ✅ |
| 17 | Build NetToGrossCalculator component | L | 120m | ✅ |
| 18 | Implement reverse tax calculation | M | 60m | ✅ |
| 19 | Add employer cost breakdown | M | 30m | ✅ |
| 20 | Add region-based calculations | S | 30m | ✅ |

**Features to Implement**:
- Input net salary, calculate gross
- Show required gross for desired net
- Display employer's total cost
- Regional minimum wage validation
- Detailed tax and insurance breakdown

### Phase 5: Enhanced Loan Calculator [5h] 🏠
| # | Task | Size | Est | Test |
|---|------|------|-----|------|
| 21 | Update /loan-calculator with new features | M | 60m | ✅ |
| 22 | Build LoanAmortizationTable component | L | 90m | ✅ |
| 23 | Add interest type selection (fixed/reducing) | M | 45m | ✅ |
| 24 | Implement payment schedule generation | M | 60m | ✅ |
| 25 | Add export to PDF/Excel functionality | M | 45m | ✅ |

**Features to Implement**:
- Two interest calculation methods
- Detailed monthly payment schedule
- Total interest paid breakdown
- Early payment impact calculation
- Export functionality

### Phase 6: Update Existing Tools [4h] 🔄
| # | Task | Size | Est | Test |
|---|------|------|-----|------|
| 26 | Update SavingsCalculator with bank data | M | 60m | ✅ |
| 27 | Verify SalaryConverter has all features | M | 45m | ✅ |
| 28 | Add Vietnamese localization where missing | M | 45m | ✅ |
| 29 | Ensure consistent UI across all tools | M | 60m | ✅ |

### Phase 7: Integration & Polish [4h] ✨
| # | Task | Size | Est | Test |
|---|------|------|-----|------|
| 30 | Update tools listing page | S | 30m | ✅ |
| 31 | Add breadcrumb navigation | S | 20m | ✅ |
| 32 | Implement analytics tracking | M | 45m | - |
| 33 | Add loading and error states | M | 45m | ✅ |
| 34 | Performance optimization | M | 40m | - |
| 35 | Final testing and bug fixes | L | 60m | ✅ |

---

## Testing Strategy (React Testing Library)

### Component Testing Template
```typescript
// BankInterestComparison.test.tsx
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import { BankInterestComparison } from './BankInterestComparison';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

describe('BankInterestComparison', () => {
  let queryClient: QueryClient;

  beforeEach(() => {
    queryClient = new QueryClient({
      defaultOptions: { queries: { retry: false } }
    });
  });

  const renderWithQuery = (component: React.ReactElement) => {
    return render(
      <QueryClientProvider client={queryClient}>
        {component}
      </QueryClientProvider>
    );
  };

  it('should display filtered bank rates', async () => {
    renderWithQuery(<BankInterestComparison />);

    // Wait for data to load
    await waitFor(() => {
      expect(screen.getByText('Vietcombank')).toBeInTheDocument();
    });

    // Test filtering
    const amountInput = screen.getByLabelText('Số tiền gửi');
    fireEvent.change(amountInput, { target: { value: '100' } });

    // Verify filtered results
    expect(screen.getByText('Lãi suất: 5.5%/năm')).toBeInTheDocument();
  });

  it('should sort banks by interest rate', async () => {
    renderWithQuery(<BankInterestComparison />);

    const sortSelect = screen.getByLabelText('Sắp xếp');
    fireEvent.change(sortSelect, { target: { value: 'rate' } });

    await waitFor(() => {
      const banks = screen.getAllByTestId('bank-item');
      expect(banks[0]).toHaveTextContent('6.0%'); // Highest rate first
    });
  });
});
```

### Integration Testing with MSW
```typescript
// tools.integration.test.tsx
import { rest } from 'msw';
import { setupServer } from 'msw/node';
import { renderWithRouter } from '@/test/utils';

const server = setupServer(
  rest.get('/api/bank-rates', (req, res, ctx) => {
    return res(ctx.json([
      { bank: 'Vietcombank', rate: 5.5, type: 'online' },
      { bank: 'Techcombank', rate: 6.0, type: 'online' }
    ]));
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

describe('Tools Integration', () => {
  it('should navigate between tools', async () => {
    const { user } = renderWithRouter(<ToolsPage />);

    // Click on savings comparison tool
    await user.click(screen.getByText('So sánh lãi suất'));

    expect(screen.getByText('So sánh lãi suất tiền gửi')).toBeInTheDocument();
  });
});
```

---

## TDD Workflow for Each Tool

### Example: Bank Interest Comparison

```markdown
## Task 11.1: Write Failing Test for Page Structure (3 min)

**Test**:
```typescript
it('should render bank comparison page', () => {
  render(<SavingsInterestComparisonPage />);
  expect(screen.getByText('So sánh lãi suất tiền gửi')).toBeInTheDocument();
  expect(screen.getByLabelText('Số tiền gửi')).toBeInTheDocument();
});
```

**Expected**: ❌ Page not found

## Task 11.2: Create Page Component (5 min)

**Implementation**:
```typescript
// app/[locale]/tools/savings-interest-comparison/page.tsx
import { BankInterestComparison } from '@/components/financial-tools/BankInterestComparison';

export default function SavingsInterestComparisonPage() {
  return (
    <div className="container mx-auto py-8">
      <h1>So sánh lãi suất tiền gửi</h1>
      <BankInterestComparison />
    </div>
  );
}
```

**Expected**: ✅ Test passes

## Task 11.3: Add Layout Wrapper (4 min)

**Implementation**:
```typescript
import { CalculatorLayout } from '@/components/financial-tools/common/CalculatorLayout';

export default function SavingsInterestComparisonPage() {
  return (
    <CalculatorLayout
      title="So sánh lãi suất tiền gửi"
      description="So sánh lãi suất tiết kiệm giữa các ngân hàng Việt Nam"
    >
      <BankInterestComparison />
    </CalculatorLayout>
  );
}
```

**Expected**: ✅ Test passes with proper layout
```

---

## Next.js Specific Implementation

### App Router Pages
```typescript
// app/[locale]/tools/savings-interest-comparison/page.tsx
import { BankInterestComparison } from '@/components/financial-tools/BankInterestComparison';
import { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'So sánh lãi suất tiền gửi - Công cụ tài chính',
  description: 'So sánh lãi suất tiết kiệm giữa các ngân hàng hàng đầu Việt Nam',
};

export default function SavingsInterestComparisonPage() {
  return <BankInterestComparison />;
}
```

### API Routes for Bank Data
```typescript
// app/api/bank-rates/route.ts
import { NextResponse } from 'next/server';
import { getBankInterestRates } from '@/lib/financial-data/bank-interest-rates';

export async function GET(request: Request) {
  const { searchParams } = new URL(request.url);
  const amount = searchParams.get('amount');
  const period = searchParams.get('period');
  const type = searchParams.get('type');

  const rates = await getBankInterestRates({
    amount: amount ? parseInt(amount) : undefined,
    period: period ? parseInt(period) : undefined,
    type: type as 'online' | 'offline' | undefined
  });

  return NextResponse.json(rates);
}
```

### Server Component for Static Data
```typescript
// app/[locale]/tools/layout.tsx
import { getToolList } from '@/lib/tools';
import { ToolsNavigation } from '@/components/tools/ToolsNavigation';

export default async function ToolsLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  const tools = await getToolList();

  return (
    <div className="flex min-h-screen">
      <aside className="w-64">
        <ToolsNavigation tools={tools} />
      </aside>
      <main className="flex-1">{children}</main>
    </div>
  );
}
```

---

## Vietnamese Localization Considerations

### Number Formatting
```typescript
// utils/currency.ts
export const formatVND = (value: number): string => {
  return new Intl.NumberFormat('vi-VN', {
    style: 'currency',
    currency: 'VND',
    minimumFractionDigits: 0,
    maximumFractionDigits: 0,
  }).format(value);
};

// For input display (no currency symbol)
export const formatVNDInput = (value: number): string => {
  return new Intl.NumberFormat('vi-VN', {
    minimumFractionDigits: 0,
    maximumFractionDigits: 0,
  }).format(value);
};
```

### Tax Calculation (Vietnam 2024)
```typescript
// lib/financial/tax-calculations.ts
export const calculatePersonalIncomeTax = (
  taxableIncome: number,
  dependents: number
): TaxBreakdown => {
  const TAX_BRACKETS = [
    { upTo: 5_000_000, rate: 0.05 },
    { upTo: 10_000_000, rate: 0.10 },
    { upTo: 18_000_000, rate: 0.15 },
    { upTo: 32_000_000, rate: 0.20 },
    { upTo: 52_000_000, rate: 0.25 },
    { upTo: 80_000_000, rate: 0.30 },
    { above: 80_000_000, rate: 0.35 },
  ];

  const deductible = 11_000_000 + (dependents * 4_400_000);
  // ... calculation logic
};
```

---

## Performance Optimization

### React Query Configuration
```typescript
// lib/queries.ts
export const bankRatesQuery = (params?: BankRateParams) => ({
  queryKey: ['bank-rates', params],
  queryFn: () => fetchBankRates(params),
  staleTime: 5 * 60 * 1000, // 5 minutes
  cacheTime: 10 * 60 * 1000, // 10 minutes
});

// Usage in component
const { data: bankRates } = useQuery(bankRatesQuery({
  amount: formData.amount,
  period: formData.period
}));
```

### Memoization for Expensive Calculations
```typescript
const amortizationSchedule = useMemo(() => {
  return calculateAmortization(loanParams);
}, [loanParams.amount, loanParams.period, loanParams.rate]);
```

---

## Accessibility Checklist

- [ ] Semantic HTML for all calculator forms
- [ ] ARIA labels for complex inputs
- [ ] Keyboard navigation for all interactions
- [ ] Screen reader announcements for calculation results
- [ ] High contrast mode support
- [ ] Focus management in modals/drawers
- [ ] Form validation with clear error messages

---

## Success Criteria

### Functional Requirements
- [ ] All 4 tools migrated with full functionality
- [ ] Real-time calculations work correctly
- [ ] Vietnamese localization complete
- [ ] API integration for bank rates functional
- [ ] Export functionality works

### Quality Requirements
- [ ] Test coverage ≥85%
- [ ] Lighthouse score ≥90
- [ ] No TypeScript errors
- [ ] Consistent UI/UX across all tools

### Performance Requirements
- [ ] First Contentful Paint < 1.5s
- [ ] Smooth animations at 60fps
- [ ] Efficient re-renders with proper memoization
- [ ] API responses cached appropriately

---

## 🚀 Migration Timeline

**Week 1**: Foundation, shared components, Bank Interest Comparison
**Week 2**: Net to Gross Calculator, Enhanced Loan Calculator
**Week 3**: Updates to existing tools, integration testing
**Week 4**: Polish, optimization, deployment preparation

---

## Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Bank rate API reliability | High | Implement fallback static data |
| Complex tax calculations | Medium | Thorough testing with edge cases |
| Performance with large datasets | Medium | Implement pagination and virtualization |
| Vietnamese regulation changes | Low | Modular calculation logic for easy updates |

---

## Next Steps

1. **Review and approve** this migration plan
2. **Set up development branch** for migration work
3. **Begin Phase 1** with foundation setup
4. **Daily standups** to track progress
5. **Code reviews** after each phase completion

Ready to migrate the Vietnamese financial tools with modern React patterns and full functionality preservation! 🇻🇳