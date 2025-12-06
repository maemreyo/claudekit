# ⚛️ React Plan: Migration of Tools from Old Project

**Stack**: React 18 / Next.js 14 | **Estimate**: 8-12 hours | **TDD**: Yes

## Executive Summary

This plan outlines the migration of all financial calculator tools from the old project (`cong-cu` section) to the new Next.js 14 structure. The migration will preserve all functionality while adapting to the new design system, architecture patterns, and tech stack.

## Component Architecture

### Current vs Target Structure

**Old Structure** (`docs/migration/old-project/code/app/cong-cu/`):
```
cong-cu/
├── tinh-lai-tien-gui/         # Savings Interest Calculator
├── tinh-luong-gross-net/      # Gross to Net Salary Calculator
├── tinh-luong-net-gross/      # Net to Gross Salary Calculator
└── tinh-toan-khoan-vay/       # Loan Calculator
```

**Target Structure** (`src/app/[locale]/tools/`):
```
tools/
├── page.tsx                   # Tools index (✅ exists)
├── savings-calculator/page.tsx    # Tính lãi tiền gửi (✅ exists)
├── salary-converter/page.tsx      # Combined Gross-Net & Net-Gross (✅ exists)
├── loan-calculator/page.tsx       # Tính toán khoản vay (✅ exists)
└── components/               # Shared tool components
    ├── ToolLayout.tsx        # Common layout wrapper
    ├── ToolBanner.tsx        # Header/banner component
    ├── CalculationResults.tsx  # Results display (✅ exists)
    └── FinancialComparison.tsx  # Comparison tables (✅ exists)
```

### Component Breakdown

| Component | Type | Responsibility | Source | Target |
|-----------|------|----------------|---------|---------|
| ToolsPage | Page | Tools listing and navigation | Old: `/cong-cu/` | ✅ `src/app/[locale]/tools/page.tsx` |
| SavingTool | Feature | Savings calculator with bank comparison | Old: `SavingTool` | ✅ `SavingsCalculator` |
| SalaryConversion | Feature | Gross-Net salary conversions | Old: `SalaryConversion` | ✅ `SalaryConverter` |
| InterestRateTool | Feature | Loan calculator with amortization | Old: `InterestRateTool` | ✅ `LoanCalculator` |
| ToolLayout | Layout | Common wrapper for all tools | New pattern | To create |
| TextInputGroup | UI | Reusable input component | Old: custom | Use shadcn/ui `Input` |
| SelectGroup | UI | Reusable dropdown | Old: `react-select` | Use shadcn/ui `Select` |

---

## Phase 1: Foundation & Setup [45min] 🏗️

### Task 1.1: Create ToolLayout Component (15 min)

**File**: `src/components/tools/ToolLayout.tsx`

**Acceptance Criteria**:
- [ ] Layout wrapper with common header structure
- [ ] Breadcrumb navigation support
- [ ] Responsive design with Tailwind CSS
- [ ] Support for tool-specific icons and descriptions
- [ ] Consistent spacing and typography

**Implementation**:
```typescript
import React from 'react';
import { Button } from '@/components/ui/button';
import { ArrowLeft } from 'lucide-react';
import Link from 'next/link';

interface ToolLayoutProps {
  children: React.ReactNode;
  title: string;
  description: string;
  icon?: React.ReactNode;
  backHref?: string;
}

export function ToolLayout({
  children,
  title,
  description,
  icon,
  backHref = '/vi/tools'
}: ToolLayoutProps) {
  return (
    <div className="min-h-screen bg-gray-50">
      {/* Header */}
      <div className="bg-white border-b">
        <div className="container mx-auto px-4 py-6">
          <div className="flex items-center gap-4">
            <Link href={backHref}>
              <Button variant="outline" size="sm">
                <ArrowLeft className="w-4 h-4 mr-2" />
                Quay lại công cụ
              </Button>
            </Link>
            <div className="flex items-center gap-3">
              {icon}
              <div>
                <h1 className="text-3xl font-bold text-gray-900">{title}</h1>
                <p className="text-gray-600 mt-1">{description}</p>
              </div>
            </div>
          </div>
        </div>
      </div>

      {/* Main Content */}
      <div className="container mx-auto px-4 py-8">
        <div className="max-w-6xl mx-auto">
          {children}
        </div>
      </div>
    </div>
  );
}
```

---

### Task 1.2: Create ToolBanner Component (15 min)

**File**: `src/components/tools/ToolBanner.tsx`

**Acceptance Criteria**:
- [ ] Dynamic content support (author info, last updated)
- [ ] Call-to-action buttons for related tools
- [ ] SEO-friendly semantic HTML
- [ ] Mobile responsive design

**Implementation**:
```typescript
import React from 'react';
import { Card, CardContent } from '@/components/ui/card';
import { Badge } from '@/components/ui/badge';
import { Button } from '@/components/ui/button';
import { User, Calendar, TrendingUp } from 'lucide-react';

interface ToolBannerProps {
  title: string;
  description: string;
  author?: string;
  lastUpdated?: string;
  relatedTools?: Array<{
    name: string;
    href: string;
  }>;
}

export function ToolBanner({
  title,
  description,
  author,
  lastUpdated,
  relatedTools = []
}: ToolBannerProps) {
  return (
    <Card className="mb-6">
      <CardContent className="p-6">
        <div className="flex flex-col md:flex-row md:items-center md:justify-between gap-4">
          <div className="space-y-2">
            <div className="flex items-center gap-2">
              <TrendingUp className="w-5 h-5 text-blue-600" />
              <span className="text-sm text-gray-600">Công cụ tài chính</span>
            </div>
            <h2 className="text-2xl font-bold">{title}</h2>
            <p className="text-gray-600">{description}</p>
            <div className="flex items-center gap-4 text-sm text-gray-500">
              {author && (
                <div className="flex items-center gap-1">
                  <User className="w-4 h-4" />
                  <span>{author}</span>
                </div>
              )}
              {lastUpdated && (
                <div className="flex items-center gap-1">
                  <Calendar className="w-4 h-4" />
                  <span>Cập nhật: {lastUpdated}</span>
                </div>
              )}
            </div>
          </div>

          {relatedTools.length > 0 && (
            <div className="space-y-2">
              <span className="text-sm font-medium text-gray-700">Công cụ liên quan</span>
              <div className="flex flex-wrap gap-2">
                {relatedTools.map((tool, index) => (
                  <Button key={index} variant="outline" size="sm" asChild>
                    <Link href={tool.href}>{tool.name}</Link>
                  </Button>
                ))}
              </div>
            </div>
          )}
        </div>
      </CardContent>
    </Card>
  );
}
```

---

### Task 1.3: Create Vietnamese Number Utility (10 min)

**File**: `src/lib/utils/number-formatting.ts`

**Acceptance Criteria**:
- [ ] Format numbers as VND currency
- [ ] Parse formatted strings back to numbers
- [ ] Support for large numbers (up to billions)
- [ ] Vietnamese number formatting with periods

**Implementation**:
```typescript
/**
 * Vietnamese number formatting utilities
 */

export function formatVND(amount: number): string {
  return new Intl.NumberFormat('vi-VN', {
    style: 'currency',
    currency: 'VND',
    minimumFractionDigits: 0,
    maximumFractionDigits: 0,
  }).format(amount);
}

export function parseFormattedNumber(formatted: string): number {
  // Remove all non-digit characters
  return parseInt(formatted.replace(/[^\d]/g, '')) || 0;
}

export function formatNumberWithCommas(amount: number): string {
  return new Intl.NumberFormat('vi-VN').format(amount);
}

export function formatCompactNumber(amount: number): string {
  if (amount >= 1e9) {
    return `${(amount / 1e9).toFixed(1)} tỷ`;
  } else if (amount >= 1e6) {
    return `${(amount / 1e6).toFixed(1)} triệu`;
  } else if (amount >= 1e3) {
    return `${(amount / 1e3).toFixed(1)} nghìn`;
  }
  return amount.toString();
}
```

---

### Task 1.4: Create Calculation Utilities (5 min)

**File**: `src/lib/calculations/savings-calculations.ts`

**Acceptance Criteria**:
- [ ] Compound interest calculation
- [ ] Simple interest calculation
- [ ] Loan amortization calculation
- [ ] Tax calculation helpers

**Implementation**:
```typescript
/**
 * Financial calculation utilities
 */

export interface CalculationResult {
  finalAmount: number;
  totalInterest: number;
  monthlyPayment?: number;
}

export function calculateCompoundInterest(
  principal: number,
  annualRate: number,
  months: number,
  compoundingFrequency: number = 12
): CalculationResult {
  const r = annualRate / 100 / compoundingFrequency;
  const n = months;

  const finalAmount = principal * Math.pow(1 + r, n);
  const totalInterest = finalAmount - principal;

  return { finalAmount, totalInterest };
}

export function calculateLoanAmortization(
  principal: number,
  annualRate: number,
  months: number,
  startDate: Date
) {
  const monthlyRate = annualRate / 100 / 12;
  const monthlyPayment = principal *
    (monthlyRate * Math.pow(1 + monthlyRate, months)) /
    (Math.pow(1 + monthlyRate, months) - 1);

  const schedule = [];
  let balance = principal;

  for (let i = 1; i <= months; i++) {
    const interestPayment = balance * monthlyRate;
    const principalPayment = monthlyPayment - interestPayment;
    balance -= principalPayment;

    const paymentDate = new Date(startDate);
    paymentDate.setMonth(paymentDate.getMonth() + i);

    schedule.push({
      period: i,
      paymentDate: paymentDate.toISOString().split('T')[0],
      payment: monthlyPayment,
      principal: principalPayment,
      interest: interestPayment,
      balance: Math.max(0, balance)
    });
  }

  return { monthlyPayment, schedule };
}
```

---

## Phase 2: Update Savings Calculator [60min] 🏦

### Task 2.1: Update Savings Calculator with ToolLayout (15 min)

**File**: `src/app/[locale]/tools/savings-calculator/page.tsx`

**Acceptance Criteria**:
- [ ] Use new ToolLayout component
- [ ] Migrate all features from old SavingTool
- [ ] Preserve Vietnamese language support
- [ ] Responsive design with Tailwind

**Implementation**:
```typescript
"use client";

import { TrendingUp } from "lucide-react";
import { ToolLayout } from "@/components/tools/ToolLayout";
import { ToolBanner } from "@/components/tools/ToolBanner";
import SavingsCalculator from "@/components/financial-tools/SavingsCalculator";

export default function SavingsCalculatorPage() {
  return (
    <ToolLayout
      title="Công cụ tiết kiệm"
      description="Tính toán lãi suất tiết kiệm và so sánh gói gửi tiền"
      icon={<TrendingUp className="w-8 h-8 text-green-600" />}
    >
      <ToolBanner
        title="Máy tính tiết kiệm thông minh"
        description="So sánh lãi suất từ nhiều ngân hàng, tính toán lãi kép và lập kế hoạch tiết kiệm hiệu quả"
        author="Team Finance"
        lastUpdated="Tháng 12, 2024"
        relatedTools={[
          { name: "Công cụ vay vốn", href: "/vi/tools/loan-calculator" },
          { name: "Chuyển đổi lương", href: "/vi/tools/salary-converter" }
        ]}
      />

      <SavingsCalculator />
    </ToolLayout>
  );
}
```

---

### Task 2.2: Update SavingsCalculator Component (45 min)

**File**: `src/components/financial-tools/SavingsCalculator.tsx` (already exists, needs enhancement)

**Acceptance Criteria**:
- [ ] Add missing features from old project
- [ ] Real bank rate integration
- [ ] Filter by amount, period, and type (online/offline)
- [ ] Sort functionality (interest rate, bank name)
- [ ] Pagination for large result sets
- [ ] Vietnamese number formatting throughout

**Key Enhancements**:
```typescript
// Add to existing component

// Bank filtering state
const [filterAmount, setFilterAmount] = useState(100000000);
const [filterPeriod, setFilterPeriod] = useState(12);
const [filterType, setFilterType] = useState<'all' | 'online' | 'offline'>('all');
const [sortBy, setSortBy] = useState<'rate' | 'bank'>('rate');

// Enhanced bank data with filtering
const filteredBanks = banks
  .filter(bank => {
    const rate = bank.savingsRates[filterPeriod];
    if (!rate) return false;
    return rate.minimumAmount <= filterAmount;
  })
  .filter(bank => filterType === 'all' || bank.savingsType === filterType)
  .sort((a, b) => {
    if (sortBy === 'rate') {
      return (b.savingsRates[filterPeriod]?.rate || 0) - (a.savingsRates[filterPeriod]?.rate || 0);
    }
    return a.name.localeCompare(b.name);
  });

// Add pagination for results
const [currentPage, setCurrentPage] = useState(1);
const itemsPerPage = 10;
const paginatedResults = filteredBanks.slice(
  (currentPage - 1) * itemsPerPage,
  currentPage * itemsPerPage
);
```

---

## Phase 3: Update Salary Converter [60min] 💰

### Task 3.1: Update Salary Converter Page (15 min)

**File**: `src/app/[locale]/tools/salary-converter/page.tsx`

**Acceptance Criteria**:
- [ ] Support both Gross-to-Net and Net-to-Gross modes
- [ ] Use ToolLayout component
- [ ] Tab-based interface for two modes
- [ ] Vietnamese regional data integration

**Implementation**:
```typescript
"use client";

import { DollarSign, ArrowRightLeft } from "lucide-react";
import { ToolLayout } from "@/components/tools/ToolLayout";
import { ToolBanner } from "@/components/tools/ToolBanner";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { GrossToNetCalculator } from "@/components/financial-tools/GrossToNetCalculator";
import { NetToGrossCalculator } from "@/components/financial-tools/NetToGrossCalculator";

export default function SalaryConverterPage() {
  return (
    <ToolLayout
      title="Chuyển đổi lương Gross - Net"
      description="Tính toán lương thực nhận sau thuế và bảo hiểm"
      icon={<DollarSign className="w-8 h-8 text-purple-600" />}
    >
      <ToolBanner
        title="Công cụ tính lương chuyên nghiệp"
        description="Chuyển đổi giữa lương gross và net theo quy định thuế Việt Nam"
        author="Team Finance"
        lastUpdated="Tháng 12, 2024"
        relatedTools={[
          { name: "Công cụ tiết kiệm", href: "/vi/tools/savings-calculator" },
          { name: "Công cụ vay vốn", href: "/vi/tools/loan-calculator" }
        ]}
      />

      <Tabs defaultValue="gross-to-net" className="w-full">
        <TabsList className="grid w-full grid-cols-2">
          <TabsTrigger value="gross-to-net" className="flex items-center gap-2">
            Gross → Net
            <ArrowRightLeft className="w-4 h-4" />
          </TabsTrigger>
          <TabsTrigger value="net-to-gross" className="flex items-center gap-2">
            Net → Gross
            <ArrowRightLeft className="w-4 h-4" />
          </TabsTrigger>
        </TabsList>

        <TabsContent value="gross-to-net">
          <GrossToNetCalculator />
        </TabsContent>

        <TabsContent value="net-to-gross">
          <NetToGrossCalculator />
        </TabsContent>
      </Tabs>
    </ToolLayout>
  );
}
```

---

### Task 3.2: Create GrossToNetCalculator Component (20 min)

**File**: `src/components/financial-tools/GrossToNetCalculator.tsx`

**Acceptance Criteria**:
- [ ] Calculate tax deductions based on Vietnamese tax brackets
- [ ] Include social, health, and unemployment insurance
- [ ] Support different regions (I, II, III, IV)
- [ ] Show tax bracket breakdown
- [ ] Display employer costs

**Implementation**:
```typescript
import React, { useState, useEffect } from 'react';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from '@/components/ui/select';
import { Button } from '@/components/ui/button';
import { Separator } from '@/components/ui/separator';
import { calculateVietnameseTax, calculateSocialInsurance } from '@/lib/calculations/tax-calculations';

export function GrossToNetCalculator() {
  const [grossSalary, setGrossSalary] = useState(20000000);
  const [dependents, setDependents] = useState(0);
  const [region, setRegion] = useState('1');
  const [results, setResults] = useState<any>(null);

  useEffect(() => {
    calculateNetSalary();
  }, [grossSalary, dependents, region]);

  const calculateNetSalary = () => {
    // Calculate insurance contributions
    const socialInsurance = calculateSocialInsurance(grossSalary, 'social');
    const healthInsurance = calculateSocialInsurance(grossSalary, 'health');
    const unemploymentInsurance = calculateSocialInsurance(grossSalary, 'unemployment', region);

    // Calculate taxable income
    const taxableIncome = grossSalary - socialInsurance - healthInsurance - unemploymentInsurance - 11000000 - (dependents * 4400000);

    // Calculate income tax
    const incomeTax = calculateVietnameseTax(taxableIncome);

    // Calculate net salary
    const netSalary = grossSalary - socialInsurance - healthInsurance - unemploymentInsurance - incomeTax;

    // Calculate employer cost
    const employerSocialInsurance = grossSalary * 0.175;
    const employerHealthInsurance = grossSalary * 0.03;
    const employerUnemploymentInsurance = grossSalary * (region === '1' ? 0.01 : 0.008);
    const employerCost = grossSalary + employerSocialInsurance + employerHealthInsurance + employerUnemploymentInsurance;

    setResults({
      grossSalary,
      socialInsurance,
      healthInsurance,
      unemploymentInsurance,
      totalInsurance: socialInsurance + healthInsurance + unemploymentInsurance,
      taxableIncome,
      incomeTax,
      netSalary,
      employerCost,
      employerSocialInsurance,
      employerHealthInsurance,
      employerUnemploymentInsurance
    });
  };

  if (!results) return null;

  return (
    <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
      {/* Input Form */}
      <Card>
        <CardHeader>
          <CardTitle>Thông tin lương Gross</CardTitle>
        </CardHeader>
        <CardContent className="space-y-4">
          <div>
            <Label htmlFor="gross-salary">Lương Gross (VND/tháng)</Label>
            <Input
              id="gross-salary"
              type="text"
              value={formatVND(results.grossSalary)}
              onChange={(e) => setGrossSalary(parseFormattedNumber(e.target.value))}
            />
          </div>

          <div>
            <Label htmlFor="dependents">Số người phụ thuộc</Label>
            <Select value={dependents.toString()} onValueChange={(v) => setDependents(parseInt(v))}>
              <SelectTrigger>
                <SelectValue />
              </SelectTrigger>
              <SelectContent>
                {[0,1,2,3,4,5].map(n => (
                  <SelectItem key={n} value={n.toString()}>{n}</SelectItem>
                ))}
              </SelectContent>
            </Select>
          </div>

          <div>
            <Label htmlFor="region">Khu vực</Label>
            <Select value={region} onValueChange={setRegion}>
              <SelectTrigger>
                <SelectValue />
              </SelectTrigger>
              <SelectContent>
                <SelectItem value="1">Khu vực I</SelectItem>
                <SelectItem value="2">Khu vực II</SelectItem>
                <SelectItem value="3">Khu vực III</SelectItem>
                <SelectItem value="4">Khu vực IV</SelectItem>
              </SelectContent>
            </Select>
          </div>
        </CardContent>
      </Card>

      {/* Results */}
      <div className="space-y-4">
        <Card>
          <CardHeader>
            <CardTitle className="text-green-600">Lương Net nhận được</CardTitle>
          </CardHeader>
          <CardContent>
            <div className="text-3xl font-bold text-green-600">
              {formatVND(results.netSalary)}
            </div>
            <p className="text-sm text-gray-600 mt-1">
              {((results.netSalary / results.grossSalary) * 100).toFixed(1)}% của lương Gross
            </p>
          </CardContent>
        </Card>

        <Card>
          <CardHeader>
            <CardTitle>Chi tiết khấu trừ</CardTitle>
          </CardHeader>
          <CardContent className="space-y-3">
            <div className="flex justify-between">
              <span>BHXH (8%):</span>
              <span>{formatVND(results.socialInsurance)}</span>
            </div>
            <div className="flex justify-between">
              <span>BHYT (1.5%):</span>
              <span>{formatVND(results.healthInsurance)}</span>
            </div>
            <div className="flex justify-between">
              <span>BHTN (1%):</span>
              <span>{formatVND(results.unemploymentInsurance)}</span>
            </div>
            <Separator />
            <div className="flex justify-between font-medium">
              <span>Tổng bảo hiểm:</span>
              <span>{formatVND(results.totalInsurance)}</span>
            </div>
            <div className="flex justify-between">
              <span>Thuế TNCN:</span>
              <span>{formatVND(results.incomeTax)}</span>
            </div>
            <Separator />
            <div className="flex justify-between font-bold">
              <span>Tổng khấu trừ:</span>
              <span>{formatVND(results.grossSalary - results.netSalary)}</span>
            </div>
          </CardContent>
        </Card>

        <Card>
          <CardHeader>
            <CardTitle>Chi phí employer</CardTitle>
          </CardHeader>
          <CardContent>
            <div className="text-2xl font-bold text-blue-600">
              {formatVND(results.employerCost)}
            </div>
            <p className="text-sm text-gray-600 mt-1">
              Bao gồm: Lương {formatVND(results.grossSalary)} +
              BHXH (17.5%) + BHYT (3%) + BHTN ({region === '1' ? '1%' : '0.8%'})
            </p>
          </CardContent>
        </Card>
      </div>
    </div>
  );
}
```

---

### Task 3.3: Create NetToGrossCalculator Component (15 min)

**File**: `src/components/financial-tools/NetToGrossCalculator.tsx`

**Acceptance Criteria**:
- [ ] Reverse calculation from net to gross
- [ ] Iterative calculation for accurate results
- [ ] Show detailed breakdown
- [ ] Support same regions and dependents

**Implementation**:
```typescript
import React, { useState, useEffect } from 'react';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from '@/components/ui/select';
import { formatVND, parseFormattedNumber } from '@/lib/utils/number-formatting';

export function NetToGrossCalculator() {
  const [netSalary, setNetSalary] = useState(17000000);
  const [dependents, setDependents] = useState(0);
  const [region, setRegion] = useState('1');
  const [results, setResults] = useState<any>(null);

  useEffect(() => {
    calculateGrossFromNet();
  }, [netSalary, dependents, region]);

  const calculateGrossFromNet = () => {
    // Binary search to find gross salary
    let low = netSalary;
    let high = netSalary * 2;
    let gross = 0;
    let tolerance = 100; // VND tolerance

    while (low <= high) {
      const mid = Math.floor((low + high) / 2);

      // Calculate deductions for mid value
      const socialInsurance = Math.min(mid * 0.08, 2384000); // Cap at 23.84 triệu
      const healthInsurance = Math.min(mid * 0.015, 447000); // Cap at 4.47 triệu
      const unemploymentInsurance = mid * (region === '1' ? 0.01 : 0.008);

      const taxableIncome = mid - socialInsurance - healthInsurance - unemploymentInsurance - 11000000 - (dependents * 4400000);
      const incomeTax = calculateVietnameseTax(Math.max(0, taxableIncome));

      const calculatedNet = mid - socialInsurance - healthInsurance - unemploymentInsurance - incomeTax;

      if (Math.abs(calculatedNet - netSalary) <= tolerance) {
        gross = mid;
        break;
      } else if (calculatedNet < netSalary) {
        low = mid + 1;
      } else {
        high = mid - 1;
      }
    }

    // Calculate final breakdown
    const socialInsurance = Math.min(gross * 0.08, 2384000);
    const healthInsurance = Math.min(gross * 0.015, 447000);
    const unemploymentInsurance = gross * (region === '1' ? 0.01 : 0.008);
    const taxableIncome = gross - socialInsurance - healthInsurance - unemploymentInsurance - 11000000 - (dependents * 4400000);
    const incomeTax = calculateVietnameseTax(Math.max(0, taxableIncome));

    setResults({
      gross,
      socialInsurance,
      healthInsurance,
      unemploymentInsurance,
      incomeTax,
      net: gross - socialInsurance - healthInsurance - unemploymentInsurance - incomeTax
    });
  };

  // ... similar UI structure to GrossToNetCalculator but with reverse calculation
}
```

---

### Task 3.4: Create Tax Calculation Utilities (10 min)

**File**: `src/lib/calculations/tax-calculations.ts`

**Acceptance Criteria**:
- [ ] Vietnamese tax bracket calculations
- [ ] Social insurance calculations with caps
- [ ] Regional unemployment insurance rates
- [ ] Accurate rounding and validation

**Implementation**:
```typescript
/**
 * Vietnamese tax and insurance calculation utilities
 */

export function calculateVietnameseTax(taxableIncome: number): number {
  if (taxableIncome <= 0) return 0;

  // Vietnamese progressive tax brackets
  const brackets = [
    { max: 5000000, rate: 0.05 },
    { max: 10000000, rate: 0.10 },
    { max: 18000000, rate: 0.15 },
    { max: 32000000, rate: 0.20 },
    { max: 52000000, rate: 0.25 },
    { max: 80000000, rate: 0.30 },
    { max: Infinity, rate: 0.35 }
  ];

  let tax = 0;
  let remaining = taxableIncome;
  let previousMax = 0;

  for (const bracket of brackets) {
    if (remaining <= 0) break;

    const taxableInBracket = Math.min(remaining, bracket.max - previousMax);
    tax += taxableInBracket * bracket.rate;
    remaining -= taxableInBracket;
    previousMax = bracket.max;
  }

  return Math.round(tax);
}

export function calculateSocialInsurance(
  grossSalary: number,
  type: 'social' | 'health' | 'unemployment',
  region?: string
): number {
  const rates = {
    social: 0.08,      // 8%
    health: 0.015,     // 1.5%
    unemployment: region === '1' ? 0.01 : 0.008  // 1% or 0.8%
  };

  const caps = {
    social: 23840000,  // 298 triệu * 8%
    health: 4470000    // 298 triệu * 1.5%
  };

  const rate = rates[type];
  const cap = caps[type];

  const amount = grossSalary * rate;
  return cap ? Math.min(amount, cap) : amount;
}

export const REGIONAL_MINIMUM_WAGE = {
  '1': 4680000,   // Region I
  '2': 4160000,   // Region II
  '3': 3640000,   // Region III
  '4': 3250000    // Region IV
};
```

---

## Phase 4: Update Loan Calculator [60min] 🏠

### Task 4.1: Update Loan Calculator Page (15 min)

**File**: `src/app/[locale]/tools/loan-calculator/page.tsx`

**Acceptance Criteria**:
- [ ] Use ToolLayout component
- [ ] Support both interest calculation methods
- [ ] Amortization schedule display
- [ ] Vietnamese currency formatting

**Implementation**:
```typescript
"use client";

import { Calculator } from "lucide-react";
import { ToolLayout } from "@/components/tools/ToolLayout";
import { ToolBanner } from "@/components/tools/ToolBanner";
import LoanCalculator from "@/components/financial-tools/LoanCalculator";

export default function LoanCalculatorPage() {
  return (
    <ToolLayout
      title="Tính toán khoản vay"
      description="Tính toán khoản vay, lãi suất và lịch trả góp chi tiết"
      icon={<Calculator className="w-8 h-8 text-blue-600" />}
    >
      <ToolBanner
        title="Công cụ tính vay vốn chuyên nghiệp"
        description="Hỗ trợ hai phương pháp tính lãi: trên dư nợ ban đầu và trên dư nợ giảm dần"
        author="Team Finance"
        lastUpdated="Tháng 12, 2024"
        relatedTools={[
          { name: "Công cụ tiết kiệm", href: "/vi/tools/savings-calculator" },
          { name: "Chuyển đổi lương", href: "/vi/tools/salary-converter" }
        ]}
      />

      <LoanCalculator />
    </ToolLayout>
  );
}
```

---

### Task 4.2: Update LoanCalculator Component (45 min)

**File**: `src/components/financial-tools/LoanCalculator.tsx` (already exists, needs enhancement)

**Acceptance Criteria**:
- [ ] Add Vietnamese loan types (consumer, mortgage, business)
- [ ] Support both interest calculation methods
- [ ] Detailed amortization schedule with dates
- [ ] Export functionality for schedule
- [ ] Insurance and fee calculations
- [ ] Early payment calculations

**Key Enhancements**:
```typescript
// Add to existing component

// Interest calculation methods
const [interestMethod, setInterestMethod] = useState<'reducing' | 'flat'>('reducing');

// Loan type state
const [loanType, setLoanType] = useState<'consumer' | 'mortgage' | 'business'>('consumer');

// Additional fees
const [insuranceFee, setInsuranceFee] = useState(0);
const [processingFee, setProcessingFee] = useState(0);

// Enhanced calculation
const calculateLoan = useCallback(() => {
  let monthlyPayment: number;
  let schedule: any[] = [];

  if (interestMethod === 'flat') {
    // Flat rate calculation (lãi trên dư nợ ban đầu)
    const totalInterest = (loanAmount * annualRate / 100 / 12) * loanTerm;
    const totalPayment = loanAmount + totalInterest;
    monthlyPayment = totalPayment / loanTerm;

    // Create schedule
    let balance = loanAmount;
    for (let i = 1; i <= loanTerm; i++) {
      const interestPayment = (loanAmount * annualRate / 100 / 12);
      const principalPayment = monthlyPayment - interestPayment;
      balance -= principalPayment;

      schedule.push({
        period: i,
        payment: monthlyPayment,
        principal: principalPayment,
        interest: interestPayment,
        balance: Math.max(0, balance)
      });
    }
  } else {
    // Reducing balance calculation (lãi trên dư nợ giảm dần)
    const monthlyRate = annualRate / 100 / 12;
    monthlyPayment = loanAmount *
      (monthlyRate * Math.pow(1 + monthlyRate, loanTerm)) /
      (Math.pow(1 + monthlyRate, loanTerm) - 1);

    // Create schedule
    let balance = loanAmount;
    for (let i = 1; i <= loanTerm; i++) {
      const interestPayment = balance * monthlyRate;
      const principalPayment = monthlyPayment - interestPayment;
      balance -= principalPayment;

      schedule.push({
        period: i,
        payment: monthlyPayment,
        principal: principalPayment,
        interest: interestPayment,
        balance: Math.max(0, balance)
      });
    }
  }

  setResults({
    monthlyPayment,
    totalPayment: monthlyPayment * loanTerm,
    totalInterest: (monthlyPayment * loanTerm) - loanAmount,
    schedule,
    effectiveRate: interestMethod === 'flat' ? annualRate * 1.8 : annualRate
  });
}, [loanAmount, annualRate, loanTerm, interestMethod]);
```

---

## Phase 5: Add Missing Features & Polish [30min] ✨

### Task 5.1: Add Vietnamese Bank Data (10 min)

**File**: `src/lib/data/vietnamese-banks.ts`

**Acceptance Criteria**:
- [ ] Real bank names and rates
- [ ] Online/offline rate differentiation
- [ ] Minimum amounts and special conditions
- [ ] Regular rate updates mechanism

**Implementation**:
```typescript
export const VIETNAMESE_BANKS = [
  {
    id: 'vcb',
    name: 'Vietcombank',
    nameVn: 'Ngân hàng TMCP Ngoại thương Việt Nam',
    logo: '/banks/vcb.png',
    savingsRates: {
      online: {
        1: { rate: 0.5, minimumAmount: 100000 },
        3: { rate: 1.5, minimumAmount: 100000 },
        6: { rate: 2.8, minimumAmount: 100000 },
        12: { rate: 4.7, minimumAmount: 100000 },
        24: { rate: 5.2, minimumAmount: 100000 },
      },
      offline: {
        1: { rate: 0.4, minimumAmount: 100000 },
        3: { rate: 1.4, minimumAmount: 100000 },
        6: { rate: 2.7, minimumAmount: 100000 },
        12: { rate: 4.6, minimumAmount: 100000 },
        24: { rate: 5.1, minimumAmount: 100000 },
      }
    }
  },
  // ... more banks
];
```

---

### Task 5.2: Add Export Functionality (10 min)

**File**: `src/lib/utils/export-utils.ts`

**Acceptance Criteria**:
- [ ] Export amortization schedule to Excel
- [ ] Export to PDF
- [ ] Share results via email/link
- [ ] Print-friendly format

**Implementation**:
```typescript
import { formatVND } from './number-formatting';

export function exportToExcel(data: any[], filename: string) {
  const worksheet = XLSX.utils.json_to_sheet(data.map(row => ({
    'Kỳ': row.period,
    'Ngày thanh toán': row.paymentDate,
    'Gốc': formatVND(row.principal),
    'Lãi': formatVND(row.interest),
    'Tổng thanh toán': formatVND(row.payment),
    'Dư nợ': formatVND(row.balance)
  })));

  const workbook = XLSX.utils.book_new();
  XLSX.utils.book_append_sheet(workbook, worksheet, 'Lịch trả góp');
  XLSX.writeFile(workbook, `${filename}.xlsx`);
}

export function exportToPDF(element: HTMLElement, filename: string) {
  // Use html2canvas + jsPDF implementation
}

export function generateShareLink(calculationData: any): string {
  const encoded = btoa(JSON.stringify(calculationData));
  return `${window.location.origin}/share?data=${encoded}`;
}
```

---

### Task 5.3: Add Analytics & Tracking (10 min)

**File**: `src/lib/analytics/tool-tracking.ts`

**Acceptance Criteria**:
- [ ] Track tool usage
- [ ] Track calculation parameters
- [ ] Track conversion events
- [ ] Privacy-compliant tracking

**Implementation**:
```typescript
export function trackToolUsage(toolName: string, parameters: any) {
  // Track with privacy in mind
  window.gtag?.('event', 'tool_usage', {
    tool_name: toolName,
    // Only track non-sensitive parameters
    has_dependents: parameters.dependents > 0,
    region: parameters.region,
    calculation_type: parameters.calculationType
  });
}

export function trackCalculation(toolName: string, result: any) {
  window.gtag?.('event', 'calculation_complete', {
    tool_name: toolName,
    result_category: getResultCategory(result)
  });
}

function getResultCategory(result: any): string {
  // Categorize results for analytics without storing exact values
  if (result.finalAmount > 1000000000) return 'high_value';
  if (result.finalAmount > 100000000) return 'medium_value';
  return 'low_value';
}
```

---

## Phase 6: Testing & Quality Assurance [45min] 🧪

### Task 6.1: Unit Tests for Calculations (15 min)

**File**: `src/lib/calculations/__tests__/savings-calculations.test.ts`

**Test Cases**:
```typescript
import { calculateCompoundInterest, calculateLoanAmortization } from '../savings-calculations';

describe('Savings Calculations', () => {
  it('should calculate compound interest correctly', () => {
    const result = calculateCompoundInterest(100000000, 6, 12, 12);
    expect(result.finalAmount).toBeCloseTo(10616778, 0);
    expect(result.totalInterest).toBeCloseTo(616778, 0);
  });

  it('should calculate loan amortization correctly', () => {
    const result = calculateLoanAmortization(100000000, 10, 12, new Date());
    expect(result.monthlyPayment).toBeCloseTo(8791588, 0);
    expect(result.schedule).toHaveLength(12);
  });
});
```

### Task 6.2: Component Tests (15 min)

**File**: `src/components/financial-tools/__tests__/SavingsCalculator.test.tsx`

**Test Cases**:
```typescript
import { render, screen, fireEvent } from '@testing-library/react';
import SavingsCalculator from '../SavingsCalculator';

describe('SavingsCalculator', () => {
  it('should render calculator form', () => {
    render(<SavingsCalculator />);
    expect(screen.getByLabelText(/Số tiền gửi/)).toBeInTheDocument();
    expect(screen.getByLabelText(/Lãi suất/)).toBeInTheDocument();
    expect(screen.getByLabelText(/Kỳ hạn/)).toBeInTheDocument();
  });

  it('should calculate savings when inputs change', async () => {
    render(<SavingsCalculator />);

    const principalInput = screen.getByLabelText(/Số tiền gửi/);
    await fireEvent.change(principalInput, { target: { value: '200000000' } });

    // Check if results update
    await waitFor(() => {
      expect(screen.getByText(/Số tiền cuối kỳ/)).toBeInTheDocument();
    });
  });
});
```

### Task 6.3: Integration Tests (15 min)

**File**: `src/app/[locale]/tools/savings-calculator/__tests__/integration.test.tsx`

**Test Cases**:
```typescript
import { render, screen } from '@testing-library/react';
import SavingsCalculatorPage from '../../page';

// Mock bank data
jest.mock('@/lib/data/vietnamese-banks', () => ({
  VIETNAMESE_BANKS: [/* mock data */]
}));

describe('Savings Calculator Integration', () => {
  it('should load and display calculator', () => {
    render(<SavingsCalculatorPage />);
    expect(screen.getByText('Công cụ tiết kiệm')).toBeInTheDocument();
    expect(screen.getByText('Máy tính tiết kiệm thông minh')).toBeInTheDocument();
  });
});
```

---

## Migration Checklist

### ✅ Completed Features
- [x] Tools index page with all 3 calculators listed
- [x] Savings calculator with bank comparison
- [x] Salary converter (basic implementation)
- [x] Loan calculator with amortization
- [x] Common UI components from shadcn/ui

### 🔄 Features to Migrate
- [ ] **Savings Tool Enhancements**:
  - [ ] Filter by amount, period, type (online/offline)
  - [ ] Sort by interest rate/bank name
  - [ ] Pagination for results
  - [ ] Real bank rate integration

- [ ] **Salary Converter Enhancements**:
  - [ ] Net-to-Gross calculation mode
  - [ ] Tax bracket visualization
  - [ ] Regional minimum wage support
  - [ ] Employer cost breakdown

- [ ] **Loan Calculator Enhancements**:
  - [ ] Two interest methods (flat vs reducing)
  - [ ] Detailed amortization schedule
  - [ ] Early payment calculations
  - [ ] Loan type differentiation

- [ ] **Common Features**:
  - [ ] Vietnamese number formatting
  - [ ] Export to PDF/Excel
  - [ ] Share functionality
  - [ ] Print-friendly views
  - [ ] Mobile responsiveness
  - [ ] Error handling and validation
  - [ ] Loading states
  - [ ] Accessibility compliance

### 🔧 Technical Updates
- [ ] Replace Bulma CSS with Tailwind CSS
- [ ] Update from SCSS modules to Tailwind classes
- [ ] Migrate from react-select to shadcn/ui Select
- [ ] Replace custom components with shadcn/ui equivalents
- [ ] Add TypeScript strict mode compliance
- [ ] Implement proper error boundaries
- [ ] Add meta tags for SEO

### 📊 Data Migration
- [ ] Vietnamese bank rates with real-time updates
- [ ] Tax brackets and regulations
- [ ] Regional minimum wage data
- [ ] Insurance rate configurations
- [ ] Exchange rate integration (if needed)

---

## Success Criteria

**Functional Requirements**:
- [ ] All calculators produce accurate results matching Vietnamese regulations
- [ ] User can switch between calculation modes seamlessly
- [ ] Responsive design works on all device sizes
- [ ] All form validations work correctly
- [ ] Export and share functions work properly

**Quality Requirements**:
- [ ] Test coverage ≥ 80%
- [ ] Accessibility score ≥ 90 (Lighthouse)
- [ ] Performance score ≥ 90 (Lighthouse)
- [ ] No console errors or warnings
- [ ] SEO meta tags properly implemented

**User Experience**:
- [ ] Vietnamese language throughout
- [ ] Intuitive interface matching old functionality
- [ ] Fast loading times (< 3 seconds)
- [ ] Mobile-first responsive design
- [ ] Clear error messages and help text

---

## Post-Migration Tasks

1. **Performance Optimization**:
   - Lazy load heavy components
   - Optimize bundle size
   - Implement service worker for offline support

2. **SEO Enhancement**:
   - Add structured data for calculators
   - Implement Open Graph tags
   - Add calculator-specific sitemaps

3. **Analytics Implementation**:
   - Google Analytics 4 integration
   - Custom events for calculator usage
   - Conversion tracking

4. **Maintenance Plan**:
   - Automated tests for calculations
   - Bank rate update schedule
   - Tax regulation update monitoring

---

## Conclusion

This migration plan ensures all functionality from the old project is preserved while taking advantage of the new architecture, design system, and tech stack. The modular approach allows for incremental implementation and testing of each component.