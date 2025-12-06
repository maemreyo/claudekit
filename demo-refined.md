# ⚛️ React Plan: Tools Migration from Old Project (REFINED)

**Generated with**: `/plan-react --tdd --detailed --output=demo-refined.md`  
**Stack**: React 19 / Next.js 15.5.4 / TypeScript  
**Estimate**: 32-40 hours | **TDD**: Yes | **Tasks**: 89 micro-tasks

---

## Overview

Migration plan for moving all financial calculators from the old project's `/cong-cu` section to the new project's `/src/app/[locale]/tools/` structure while preserving all functionality and following the new project's patterns.

**This refined plan includes**:
- ✅ TDD micro-tasks (2-5 min cycles)
- ✅ Explicit file paths
- ✅ Acceptance criteria per task
- ✅ Ready for `/execute-plan` automation

---

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
│   └── page.tsx                 # Enhanced with amortization
├── salary-converter/
│   └── page.tsx                 # Already complete (Gross to Net)
├── savings-calculator/
│   └── page.tsx                 # Enhanced with bank comparison
├── savings-interest-comparison/ # NEW: Dedicated bank comparison
│   └── page.tsx
├── net-to-gross-calculator/     # NEW: Reverse salary calculation
│   └── page.tsx
└── loan-amortization/           # UPDATED: Detailed amortization table
    └── page.tsx
```

### Component Breakdown

| Tool | Old Component | New Location | Status | Priority |
|------|---------------|--------------|---------|----------|
| Bank Interest Comparison | Part of SavingTool | /savings-interest-comparison | ➕ NEW | 🔴 High |
| Net to Gross Salary | SalaryConversion (NET_TO_GROSS) | /net-to-gross-calculator | ➕ NEW | 🟡 Medium |
| Loan Amortization Table | Part of InterestRateTool | /loan-amortization | 🔄 UPDATE | 🟡 Medium |
| Savings Calculator | SavingTool | /savings-calculator | 🔄 UPDATE | 🟢 Low |
| Gross to Net Salary | SalaryConversion (GROSS_TO_NET) | /salary-converter | ✅ EXISTS | 🟢 Low |

---

## State Management Strategy

### Store Structure
```typescript
// src/stores/financial-tools-store.ts
interface FinancialToolsStore {
  // Existing
  loanCalculator: LoanCalculatorState;
  salaryConverter: SalaryConverterState;
  
  // New additions
  savingsComparison: {
    filters: {
      amount: number;
      period: number;
      type: 'online' | 'offline' | 'all';
    };
    sortBy: 'rate' | 'bank';
    sortOrder: 'asc' | 'desc';
  };
  
  netToGross: {
    netSalary: number;
    dependents: number;
    region: 1 | 2 | 3 | 4;
    calculated: {
      grossSalary: number;
      employerCost: number;
      taxBreakdown: TaxBracket[];
    } | null;
  };
}
```

---

## File Structure for Migration

### New Files to Create
```
src/
├── app/[locale]/tools/
│   ├── savings-interest-comparison/
│   │   └── page.tsx                                # NEW
│   ├── net-to-gross-calculator/
│   │   └── page.tsx                                # NEW
│   └── loan-amortization/
│       └── page.tsx                                # UPDATE
│
├── components/financial-tools/
│   ├── BankInterestComparison.tsx                  # NEW
│   ├── NetToGrossCalculator.tsx                    # NEW
│   ├── LoanAmortizationTable.tsx                   # NEW
│   └── common/
│       ├── CalculatorLayout.tsx                    # NEW
│       ├── ResultsCard.tsx                         # NEW
│       ├── ComparisonTable.tsx                     # NEW
│       └── TaxBreakdown.tsx                        # NEW
│
├── lib/financial/
│   ├── loan-calculations.ts                        # UPDATE
│   ├── salary-calculations.ts                      # UPDATE
│   └── savings-calculations.ts                     # NEW
│
└── lib/financial-data/
    ├── bank-interest-rates.ts                      # NEW
    ├── tax-brackets.ts                             # NEW
    ├── insurance-rates.ts                          # NEW
    └── regional-minimum-wage.ts                    # NEW
```

---

## Tasks (TDD Micro-Tasks)

### Phase 1: Foundation & Data Setup [4h] 🏗️

#### Task 1.1: Create Tax Brackets Data (15 min)

**File**: `src/lib/financial-data/tax-brackets.ts`

**Acceptance Criteria**:
- [ ] Export `TAX_BRACKETS_2024` constant
- [ ] Each bracket has `upTo`, `rate`, `previousTotal`
- [ ] Covers all 7 Vietnamese tax brackets
- [ ] TypeScript types exported

**Implementation**:
```typescript
export interface TaxBracket {
  upTo: number;
  rate: number;
  previousTotal: number;
}

export const TAX_BRACKETS_2024: TaxBracket[] = [
  { upTo: 5_000_000, rate: 0.05, previousTotal: 0 },
  { upTo: 10_000_000, rate: 0.10, previousTotal: 250_000 },
  // ... all brackets
];
```

---

#### Task 1.2: Write Test for Tax Calculation (3 min)

**File**: `src/lib/financial/__tests__/tax-calculations.test.ts`

**Test**:
```typescript
import { calculatePersonalIncomeTax } from '../tax-calculations';

describe('calculatePersonalIncomeTax', () => {
  it('should calculate tax for income in first bracket', () => {
    const result = calculatePersonalIncomeTax(15_000_000, 0);
    
    expect(result.taxableIncome).toBe(4_000_000); // 15M - 11M deduction
    expect(result.totalTax).toBe(200_000); // 4M * 5%
  });
});
```

**Expected**: ❌ Function not found

---

#### Task 1.3: Implement Tax Calculation (20 min)

**File**: `src/lib/financial/tax-calculations.ts`

**Acceptance Criteria**:
- [ ] Correctly calculates Vietnamese personal income tax
- [ ] Applies 11M personal deduction
- [ ] Applies 4.4M per dependent deduction
- [ ] Returns breakdown by tax bracket
- [ ] Handles edge cases (zero, negative)

**Implementation**:
```typescript
export interface TaxCalculationResult {
  grossIncome: number;
  deductions: number;
  taxableIncome: number;
  totalTax: number;
  netIncome: number;
  breakdown: Array<{
    bracket: number;
    taxableAmount: number;
    taxAmount: number;
    rate: number;
  }>;
}

export function calculatePersonalIncomeTax(
  grossIncome: number,
  dependents: number
): TaxCalculationResult {
  const PERSONAL_DEDUCTION = 11_000_000;
  const DEPENDENT_DEDUCTION = 4_400_000;
  
  const deductions = PERSONAL_DEDUCTION + (dependents * DEPENDENT_DEDUCTION);
  const taxableIncome = Math.max(0, grossIncome - deductions);
  
  // Calculate tax using brackets
  // ...
}
```

**Expected**: ✅ Test passes

---

#### Task 1.4: Commit Foundation (1 min)

```bash
git add src/lib/financial-data/ src/lib/financial/
git commit -m "feat(financial): add Vietnamese tax calculation foundation"
```

---

#### Task 1.5: Create Insurance Rates Data (10 min)

**File**: `src/lib/financial-data/insurance-rates.ts`

**Acceptance Criteria**:
- [ ] Export 2024 Vietnamese social insurance rates
- [ ] Include employee and employer portions
- [ ] Cover: SI, HI, UI, Union fees
- [ ] Include max salary cap

**Implementation**:
```typescript
export const INSURANCE_RATES_2024 = {
  employee: {
    socialInsurance: 0.08,
    healthInsurance: 0.015,
    unemploymentInsurance: 0.01,
    unionFee: 0.01,
  },
  employer: {
    socialInsurance: 0.175,
    healthInsurance: 0.03,
    unemploymentInsurance: 0.01,
  },
  maxSalaryCap: 36_000_000, // 20x minimum wage
};
```

---

#### Task 1.6: Create Regional Wage Data (10 min)

**File**: `src/lib/financial-data/regional-minimum-wage.ts`

**Acceptance Criteria**:
- [ ] Export 2024 Vietnamese minimum wages by region
- [ ] Include all 4 regions
- [ ] Export helper to get wage by region

**Implementation**:
```typescript
export const REGIONAL_MINIMUM_WAGE_2024 = {
  1: 4_680_000, // Region 1
  2: 4_160_000, // Region 2
  3: 3_640_000, // Region 3
  4: 3_250_000, // Region 4
} as const;

export function getMinimumWage(region: 1 | 2 | 3 | 4): number {
  return REGIONAL_MINIMUM_WAGE_2024[region];
}
```

---

#### Task 1.7: Create Bank Rates Data Structure (15 min)

**File**: `src/lib/financial-data/bank-interest-rates.ts`

**Acceptance Criteria**:
- [ ] Export bank rate interface
- [ ] Include sample data for major Vietnamese banks
- [ ] Support filtering by amount, period, type
- [ ] Export query functions

**Implementation**:
```typescript
export interface BankInterestRate {
  bank: string;
  logo?: string;
  amount: {
    min: number;
    max: number;
  };
  period: number; // months
  rate: number; // annual rate
  type: 'online' | 'offline';
  lastUpdated: Date;
}

export const BANK_RATES: BankInterestRate[] = [
  {
    bank: 'Vietcombank',
    amount: { min: 10_000_000, max: 100_000_000 },
    period: 12,
    rate: 5.5,
    type: 'online',
    lastUpdated: new Date('2024-01-01'),
  },
  // ... more banks
];

export function getBankRates(filters?: {
  amount?: number;
  period?: number;
  type?: 'online' | 'offline' | 'all';
}): BankInterestRate[] {
  // Filter logic
}
```

---

#### Task 1.8: Extend Zustand Store (20 min)

**File**: `src/stores/financial-tools-store.ts`

**Acceptance Criteria**:
- [ ] Add savings comparison state
- [ ] Add net-to-gross state
- [ ] Add actions for both
- [ ] Maintain type safety

**Test**:
```typescript
it('should update savings comparison filters', () => {
  const { updateSavingsFilters } = useFinancialToolsStore.getState();
  
  updateSavingsFilters({ amount: 100_000_000, period: 12 });
  
  const state = useFinancialToolsStore.getState();
  expect(state.savingsComparison.filters.amount).toBe(100_000_000);
});
```

---

#### Task 1.9: Create Savings Calculation Utils (25 min)

**File**: `src/lib/financial/savings-calculations.ts`

**Acceptance Criteria**:
- [ ] Calculate interest for compound and simple
- [ ] Support Vietnamese banks' calculation methods
- [ ] Compare multiple bank offers
- [ ] Return sorted results

**Test**:
```typescript
it('should calculate compound interest correctly', () => {
  const result = calculateSavingsInterest({
    principal: 100_000_000,
    rate: 0.06,
    periods: 12,
    compoundFrequency: 'monthly'
  });
  
  expect(result.totalInterest).toBeCloseTo(6_167_781, 0);
});
```

---

### Phase 2: Shared Components [6h] 🎨

#### Task 2.1: Test - CalculatorLayout Component (3 min)

**File**: `src/components/financial-tools/common/__tests__/CalculatorLayout.test.tsx`

**Test**:
```typescript
it('should render with title and description', () => {
  render(
    <CalculatorLayout 
      title="Test Calculator"
      description="Test description"
    >
      <div>Content</div>
    </CalculatorLayout>
  );
  
  expect(screen.getByText('Test Calculator')).toBeInTheDocument();
  expect(screen.getByText('Test description')).toBeInTheDocument();
  expect(screen.getByText('Content')).toBeInTheDocument();
});
```

**Expected**: ❌ Component not found

---

#### Task 2.2: Implement - CalculatorLayout (15 min)

**File**: `src/components/financial-tools/common/CalculatorLayout.tsx`

**Acceptance Criteria**:
- [ ] Renders title, description, children
- [ ] Responsive layout
- [ ] Breadcrumb navigation
- [ ] Share button (optional)
- [ ] TypeScript props interface

**Implementation**:
```typescript
interface CalculatorLayoutProps {
  title: string;
  description?: string;
  children: React.ReactNode;
  breadcrumbs?: Array<{ label: string; href?: string }>;
}

export function CalculatorLayout({
  title,
  description,
  children,
  breadcrumbs
}: CalculatorLayoutProps) {
  return (
    <div className="container mx-auto py-8 px-4">
      {breadcrumbs && <Breadcrumbs items={breadcrumbs} />}
      
      <div className="max-w-4xl mx-auto">
        <h1 className="text-3xl font-bold mb-2">{title}</h1>
        {description && (
          <p className="text-gray-600 mb-6">{description}</p>
        )}
        
        <div className="bg-white rounded-lg shadow-md p-6">
          {children}
        </div>
      </div>
    </div>
  );
}
```

**Expected**: ✅ Test passes

---

#### Task 2.3: Test - ResultsCard Component (3 min)

**File**: `src/components/financial-tools/common/__tests__/ResultsCard.test.tsx`

**Test**:
```typescript
it('should display result with label and value', () => {
  render(
    <ResultsCard
      label="Total Interest"
      value={6_000_000}
      format="currency"
    />
  );
  
  expect(screen.getByText('Total Interest')).toBeInTheDocument();
  expect(screen.getByText('6.000.000 ₫')).toBeInTheDocument();
});
```

**Expected**: ❌ Component not found

---

#### Task 2.4: Implement - ResultsCard (20 min)

**File**: `src/components/financial-tools/common/ResultsCard.tsx`

**Acceptance Criteria**:
- [ ] Displays label and value
- [ ] Supports currency, percentage, number formats
- [ ] Supports highlighting (success, warning, error)
- [ ] Optional icon and description
- [ ] Responsive design

**Implementation**:
```typescript
interface ResultsCardProps {
  label: string;
  value: number;
  format: 'currency' | 'percentage' | 'number';
  variant?: 'default' | 'success' | 'warning' | 'error';
  icon?: React.ReactNode;
  description?: string;
}

export function ResultsCard({
  label,
  value,
  format,
  variant = 'default',
  icon,
  description
}: ResultsCardProps) {
  const formattedValue = formatValue(value, format);
  
  return (
    <div className={cn('p-4 rounded border', variantStyles[variant])}>
      <div className="flex items-center gap-2">
        {icon}
        <span className="text-sm text-gray-600">{label}</span>
      </div>
      <div className="text-2xl font-bold mt-1">{formattedValue}</div>
      {description && (
        <p className="text-sm text-gray-500 mt-1">{description}</p>
      )}
    </div>
  );
}
```

**Expected**: ✅ Test passes

---

#### Task 2.5: Test - ComparisonTable Component (4 min)

**Test**:
```typescript
it('should render comparison table with sortable columns', () => {
  const data = [
    { bank: 'Vietcombank', rate: 5.5, period: 12 },
    { bank: 'Techcombank', rate: 6.0, period: 12 },
  ];
  
  render(<ComparisonTable data={data} sortBy="rate" />);
  
  expect(screen.getByText('Vietcombank')).toBeInTheDocument();
  expect(screen.getByText('6.0%')).toBeInTheDocument();
});
```

---

#### Task 2.6: Implement - ComparisonTable (30 min)

**File**: `src/components/financial-tools/common/ComparisonTable.tsx`

**Acceptance Criteria**:
- [ ] Renders table with data
- [ ] Sortable columns
- [ ] Filterable rows
- [ ] Pagination support
- [ ] Responsive (mobile: cards, desktop: table)
- [ ] TypeScript generic for data type

**Implementation**:
```typescript
interface ComparisonTableProps<T> {
  data: T[];
  columns: Array<{
    key: keyof T;
    label: string;
    format?: 'currency' | 'percentage' | 'number';
    sortable?: boolean;
  }>;
  sortBy?: keyof T;
  sortOrder?: 'asc' | 'desc';
  onSort?: (key: keyof T) => void;
  pageSize?: number;
}

export function ComparisonTable<T extends Record<string, any>>({
  data,
  columns,
  sortBy,
  sortOrder = 'asc',
  onSort,
  pageSize = 10
}: ComparisonTableProps<T>) {
  // Implementation with sorting, pagination
}
```

**Expected**: ✅ Tests pass

---

#### Task 2.7: Test - TaxBreakdown Component (3 min)

**Test**:
```typescript
it('should display tax breakdown by brackets', () => {
  const breakdown = [
    { bracket: 1, taxableAmount: 5_000_000, taxAmount: 250_000, rate: 0.05 },
  ];
  
  render(<TaxBreakdown breakdown={breakdown} totalTax={250_000} />);
  
  expect(screen.getByText('250.000 ₫')).toBeInTheDocument();
  expect(screen.getByText('5%')).toBeInTheDocument();
});
```

---

#### Task 2.8: Implement - TaxBreakdown Component (25 min)

**File**: `src/components/financial-tools/common/TaxBreakdown.tsx`

**Acceptance Criteria**:
- [ ] Shows tax by each bracket
- [ ] Displays total tax
- [ ] Shows effective tax rate
- [ ] Progress bars for visual representation
- [ ] Tooltips for explanations

**Implementation**:
```typescript
interface TaxBreakdownProps {
  breakdown: Array<{
    bracket: number;
    taxableAmount: number;
    taxAmount: number;
    rate: number;
  }>;
  totalTax: number;
  grossIncome: number;
}

export function TaxBreakdown({
  breakdown,
  totalTax,
  grossIncome
}: TaxBreakdownProps) {
  const effectiveRate = (totalTax / grossIncome) * 100;
  
  return (
    <div className="space-y-4">
      <h3>Chi tiết thuế thu nhập cá nhân</h3>
      
      {breakdown.map((item, index) => (
        <div key={index} className="border-b pb-2">
          <div className="flex justify-between">
            <span>Bậc {item.bracket} ({item.rate * 100}%)</span>
            <span className="font-medium">
              {formatVND(item.taxAmount)}
            </span>
          </div>
          <div className="text-sm text-gray-600">
            Tính trên: {formatVND(item.taxableAmount)}
          </div>
        </div>
      ))}
      
      <div className="font-bold flex justify-between">
        <span>Tổng thuế</span>
        <span>{formatVND(totalTax)}</span>
      </div>
      
      <div className="text-sm text-gray-600">
        Thuế suất hiệu lực: {effectiveRate.toFixed(2)}%
      </div>
    </div>
  );
}
```

---

#### Task 2.9: Test - CurrencyInput Component (3 min)

**Test**:
```typescript
it('should format input as Vietnamese currency', async () => {
  const user = userEvent.setup();
  const onChange = vi.fn();
  
  render(<CurrencyInput value={0} onChange={onChange} />);
  
  const input = screen.getByRole('textbox');
  await user.type(input, '1000000');
  
  expect(input).toHaveValue('1.000.000');
  expect(onChange).toHaveBeenCalledWith(1_000_000);
});
```

---

#### Task 2.10: Implement - CurrencyInput (20 min)

**File**: `src/components/financial-tools/common/CurrencyInput.tsx`

**Acceptance Criteria**:
- [ ] Formats as Vietnamese number (1.000.000)
- [ ] Strips formatting on change callback
- [ ] Validates numeric input only
- [ ] Supports min/max values
- [ ] Accessible with label

**Implementation**:
```typescript
interface CurrencyInputProps {
  label: string;
  value: number;
  onChange: (value: number) => void;
  min?: number;
  max?: number;
  required?: boolean;
}

export function CurrencyInput({
  label,
  value,
  onChange,
  min = 0,
  max = Number.MAX_SAFE_INTEGER,
  required = false
}: CurrencyInputProps) {
  const [displayValue, setDisplayValue] = useState(formatVNDInput(value));
  
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    // Remove non-numeric characters except dot
    const rawValue = e.target.value.replace(/[^0-9]/g, '');
    const numericValue = parseInt(rawValue || '0', 10);
    
    // Validate range
    const clampedValue = Math.min(Math.max(numericValue, min), max);
    
    setDisplayValue(formatVNDInput(clampedValue));
    onChange(clampedValue);
  };
  
  return (
    <div>
      <label className="block text-sm font-medium mb-1">
        {label} {required && <span className="text-red-500">*</span>}
      </label>
      <input
        type="text"
        value={displayValue}
        onChange={handleChange}
        className="w-full border rounded px-3 py-2"
        aria-label={label}
      />
    </div>
  );
}
```

---

#### Task 2.11: Commit Shared Components (1 min)

```bash
git add src/components/financial-tools/common/
git commit -m "feat(financial): add shared calculator components"
```

---

### Phase 3: Bank Interest Comparison Tool [5h] 🏦

#### Task 3.1: Test - Page Route (2 min)

**File**: `src/app/[locale]/tools/savings-interest-comparison/__tests__/page.test.tsx`

**Test**:
```typescript
it('should render bank comparison page', () => {
  render(<SavingsInterestComparisonPage />);
  
  expect(screen.getByText(/so sánh lãi suất/i)).toBeInTheDocument();
});
```

**Expected**: ❌ Page not found

---

#### Task 3.2: Create Page Route (8 min)

**File**: `src/app/[locale]/tools/savings-interest-comparison/page.tsx`

**Acceptance Criteria**:
- [ ] Page renders in App Router
- [ ] Metadata for SEO
- [ ] Server component structure
- [ ] Imports BankInterestComparison component

**Implementation**:
```typescript
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

**Expected**: ❌ BankInterestComparison not found

---

#### Task 3.3: Test - BankInterestComparison Rendering (3 min)

**File**: `src/components/financial-tools/__tests__/BankInterestComparison.test.tsx`

**Test**:
```typescript
it('should render filter inputs', () => {
  render(<BankInterestComparison />);
  
  expect(screen.getByLabelText(/số tiền gửi/i)).toBeInTheDocument();
  expect(screen.getByLabelText(/kỳ hạn/i)).toBeInTheDocument();
  expect(screen.getByLabelText(/loại hình/i)).toBeInTheDocument();
});
```

---

#### Task 3.4: Create Component Structure (12 min)

**File**: `src/components/financial-tools/BankInterestComparison.tsx`

**Acceptance Criteria**:
- [ ] Renders in CalculatorLayout
- [ ] Has filter form
- [ ] Has results placeholder
- [ ] Responsive layout

**Implementation**:
```typescript
export function BankInterestComparison() {
  return (
    <CalculatorLayout
      title="So sánh lãi suất tiền gửi"
      description="So sánh lãi suất tiết kiệm giữa các ngân hàng Việt Nam"
    >
      <div className="space-y-6">
        {/* Filters */}
        <div className="grid md:grid-cols-3 gap-4">
          <CurrencyInput
            label="Số tiền gửi (VND)"
            value={0}
            onChange={() => {}}
          />
          {/* More filters */}
        </div>
        
        {/* Results */}
        <div>
          <p>Kết quả sẽ hiển thị ở đây</p>
        </div>
      </div>
    </CalculatorLayout>
  );
}
```

**Expected**: ✅ Tests pass

---

#### Task 3.5: Test - Filter Functionality (4 min)

**Test**:
```typescript
it('should filter banks by amount', async () => {
  const user = userEvent.setup();
  render(<BankInterestComparison />);
  
  const amountInput = screen.getByLabelText(/số tiền gửi/i);
  await user.clear(amountInput);
  await user.type(amountInput, '100000000');
  
  // Should filter banks that accept 100M
  await waitFor(() => {
    expect(screen.getByText('Vietcombank')).toBeInTheDocument();
  });
});
```

---

#### Task 3.6: Implement Filtering Logic (25 min)

**Acceptance Criteria**:
- [ ] Filter by amount range
- [ ] Filter by period
- [ ] Filter by type (online/offline)
- [ ] Real-time filtering
- [ ] Updates results immediately

**Implementation**:
```typescript
export function BankInterestComparison() {
  const [filters, setFilters] = useState({
    amount: 10_000_000,
    period: 12,
    type: 'all' as 'online' | 'offline' | 'all'
  });
  
  const filteredRates = useMemo(() => {
    return getBankRates(filters);
  }, [filters]);
  
  return (
    // ... render with filteredRates
  );
}
```

---

#### Task 3.7: Test - Sorting Functionality (3 min)

**Test**:
```typescript
it('should sort banks by interest rate', async () => {
  const user = userEvent.setup();
  render(<BankInterestComparison />);
  
  const sortButton = screen.getByRole('button', { name: /lãi suất/i });
  await user.click(sortButton);
  
  const banks = screen.getAllByTestId('bank-item');
  // First bank should have highest rate
  expect(banks[0]).toHaveTextContent('6.0%');
});
```

---

#### Task 3.8: Implement Sorting (20 min)

**Acceptance Criteria**:
- [ ] Sort by rate (ascending/descending)
- [ ] Sort by bank name
- [ ] Toggle sort direction
- [ ] Visual indicator of sort state

**Implementation**:
```typescript
const [sortConfig, setSortConfig] = useState({
  key: 'rate' as 'rate' | 'bank',
  direction: 'desc' as 'asc' | 'desc'
});

const sortedRates = useMemo(() => {
  const sorted = [...filteredRates].sort((a, b) => {
    if (sortConfig.key === 'rate') {
      return sortConfig.direction === 'asc' 
        ? a.rate - b.rate 
        : b.rate - a.rate;
    }
    return a.bank.localeCompare(b.bank);
  });
  return sorted;
}, [filteredRates, sortConfig]);
```

---

#### Task 3.9: Test - Pagination (3 min)

**Test**:
```typescript
it('should paginate results', async () => {
  const user = userEvent.setup();
  render(<BankInterestComparison />);
  
  // Should show 10 items per page
  const banks = screen.getAllByTestId('bank-item');
  expect(banks).toHaveLength(10);
  
  // Click next page
  await user.click(screen.getByRole('button', { name: /next/i }));
  
  // Should show next 10
  expect(screen.getAllByTestId('bank-item')).toHaveLength(10);
});
```

---

#### Task 3.10: Implement Pagination (18 min)

**Acceptance Criteria**:
- [ ] 10 items per page
- [ ] Previous/Next buttons
- [ ] Page number display
- [ ] Disabled state on boundaries
- [ ] Maintains filters when paginating

---

#### Task 3.11: Test - Interest Calculation Display (3 min)

**Test**:
```typescript
it('should show calculated interest for each bank', () => {
  render(<BankInterestComparison />);
  
  const bankCard = screen.getByTestId('bank-vietcombank');
  
  expect(bankCard).toHaveTextContent('Lãi suất: 5.5%/năm');
  expect(bankCard).toHaveTextContent('Lãi nhận được: 550.000 ₫');
});
```

---

#### Task 3.12: Display Interest Calculations (20 min)

**Acceptance Criteria**:
- [ ] Calculate interest for input amount
- [ ] Show monthly and total interest
- [ ] Format as Vietnamese currency
- [ ] Update when filters change
- [ ] Show calculation method

---

#### Task 3.13: Add API Integration Placeholder (15 min)

**File**: `src/app/api/bank-rates/route.ts`

**Acceptance Criteria**:
- [ ] GET endpoint returns filtered rates
- [ ] Accepts query params (amount, period, type)
- [ ] Returns JSON response
- [ ] Ready for real API later

---

#### Task 3.14: Commit Bank Comparison Tool (1 min)

```bash
git add src/app/[locale]/tools/savings-interest-comparison/
git add src/components/financial-tools/BankInterestComparison.tsx
git commit -m "feat(tools): add bank interest comparison tool"
```

---

### Phase 4: Net to Gross Calculator [4h] 💱

*[Similar TDD micro-tasks for Net to Gross Calculator]*
*[Tasks 4.1 - 4.15 following same pattern]*

### Phase 5: Enhanced Loan Calculator [5h] 🏠

*[Similar TDD micro-tasks for Loan Amortization]*
*[Tasks 5.1 - 5.15 following same pattern]*

### Phase 6: Update Existing Tools [4h] 🔄

*[Update tasks for existing tools]*
*[Tasks 6.1 - 6.10 following same pattern]*

### Phase 7: Integration & Polish [4h] ✨

*[Final integration and polish tasks]*
*[Tasks 7.1 - 7.15 following same pattern]*

---

## Success Criteria

**Functional Requirements**:
- [ ] All 4 tools migrated with full functionality
- [ ] Real-time calculations work correctly
- [ ] Vietnamese localization complete
- [ ] API integration for bank rates functional
- [ ] Export functionality works (if applicable)

**Quality Requirements**:
- [ ] Test coverage ≥85% (measured with Vitest coverage)
- [ ] Lighthouse score ≥90 (Performance, Accessibility, Best Practices, SEO)
- [ ] No TypeScript errors (strict mode)
- [ ] No console warnings in production
- [ ] Passes all accessibility audits

**Performance Requirements**:
- [ ] First Contentful Paint < 1.5s
- [ ] Time to Interactive < 3.0s
- [ ] Smooth animations at 60fps
- [ ] API responses cached appropriately (React Query)
- [ ] No unnecessary re-renders (React DevTools Profiler)

---

## Execution Guide

### Using with /execute-plan

This refined plan is optimized for automated execution:

```bash
/execute-plan demo-refined.md
```

**The command will**:
1. Execute each task sequentially
2. Run TDD cycle (test → implement → verify)
3. Auto-commit after each completed task
4. Pause for review if tests fail
5. Report progress continuously

### Manual Execution

If executing manually, follow this pattern for each task:

```bash
# For each task X.Y:

# 1. Write failing test
npm run test TaskX.Y.test.ts

# 2. Implement minimal solution
# ... code ...

# 3. Verify test passes
npm run test TaskX.Y.test.ts

# 4. Refactor if needed
# ... improve code ...

# 5. Commit
git commit -m "feat: TaskX.Y description"

# 6. Move to next task
```

---

## Differences from demo.md

| Aspect | demo.md | demo-refined.md |
|--------|---------|-----------------|
| Tasks | 35 tasks | 89 micro-tasks |
| TDD | Mentioned but not detailed | Full TDD cycles |
| File paths | Generic | Explicit paths |
| Est per task | 20-120 min | 1-30 min (micro) |
| Tests | Strategy only | Test code included |
| Acceptance | Implicit | Explicit criteria |
| Execution | Manual | `/execute-plan` ready |

---

## Ready to Execute?

This refined plan is:
- ✅ **Detailed**: 89 micro-tasks with clear steps
- ✅ **Test-first**: Every component has tests before implementation
- ✅ **Executable**: Optimized for `/execute-plan` automation
- ✅ **Clear**: Explicit file paths and acceptance criteria
- ✅ **Traceable**: Each task can be committed individually

**Next steps**:
```bash
# Review this plan
cat demo-refined.md

# When ready to execute
/execute-plan demo-refined.md
```
