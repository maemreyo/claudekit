# Salary Calculation Flow Documentation

## Overview

This document outlines the salary calculation logic and gross/net conversion implementation based on the analysis of the old project code (`docs/migration/old-project/code`).

## Architecture

### Directory Structure
```
modules/Tools/SalaryConversion/
├── index.tsx                  # Main component entry point
├── SalaryBanner/             # Banner component
│   └── index.tsx
├── SalaryContent/            # Main calculation UI and logic
│   └── index.tsx
└── style.module.scss         # Styling
```

### API Integration
- **Endpoint**: `https://api-dev.finzone.vn/tools/salary`
- **Method**: POST
- **State Management**: Zustand store (`useSalaryConversionStore`)

## Input Parameters

| Parameter | Type | Range | Description |
|-----------|------|-------|-------------|
| `salary` | number | 1,000,000 - 1,000,000,000 VND | Salary amount |
| `dependants` | number | 0-10 | Number of dependents |
| `zone` | number | 1-4 | Geographic region |
| `type` | number | 0 or 1 | 0=Gross→Net, 1=Net→Gross |
| `insurance_level` | number (optional) | 1M - 1B VND | Insurance salary base |

## Gross to Net Calculation Flow

```
Gross Salary
  ↓ (-10.5% insurance)
Pre-Tax Income
  ↓ (- allowances)
Taxable Income
  ↓ (- progressive tax)
Net Salary
```

### Step 1: Insurance Deductions (Employee)
- **Social Insurance (BHXH)**: 8% of insurance salary
- **Health Insurance (BHYT)**: 1.5% of insurance salary
- **Unemployment Insurance (BHTN)**: 1% of insurance salary
- **Total Insurance Deduction**: 10.5% of insurance salary

### Step 2: Tax Allowances
- **Self Allowance**: 11,000,000 VND/month
- **Dependent Allowance**: 4,400,000 VND/month per dependent
- **Total Allowances**: 11,000,000 + (dependents × 4,400,000)

### Step 3: Taxable Income Calculation
```
Taxable Income = Pre-Tax Income - Total Allowances
```

### Step 4: Personal Income Tax (Progressive Rates)

| Bracket | Income Range (VND) | Rate | Tax Amount |
|---------|-------------------|-------|------------|
| 1 | 0 - 5,000,000 | 5% | 5% of amount |
| 2 | 5,000,001 - 10,000,000 | 10% | 250,000 + 10% of amount above 5M |
| 3 | 10,000,001 - 18,000,000 | 15% | 750,000 + 15% of amount above 10M |
| 4 | 18,000,001 - 32,000,000 | 20% | 1,950,000 + 20% of amount above 18M |
| 5 | 32,000,001 - 52,000,000 | 25% | 4,750,000 + 25% of amount above 32M |
| 6 | 52,000,001 - 80,000,000 | 30% | 9,750,000 + 30% of amount above 52M |
| 7 | Above 80,000,000 | 35% | 18,150,000 + 35% of amount above 80M |

### Step 5: Net Salary
```
Net Salary = Pre-Tax Income - Total Personal Income Tax
```

## Net to Gross Calculation

This is a reverse calculation that:
1. Iteratively determines the gross salary
2. Calculates forward to find resulting net
3. Adjusts until net matches target amount
4. Returns the gross salary that produces the desired net

## Geographic Regions

| Region | Minimum Wage (VND/month) | Coverage Areas |
|--------|-------------------------|----------------|
| I | 4,680,000 | Hanoi, HCMC, and other special urban areas |
| II | 4,160,000 | Urban areas in other provinces |
| III | 3,640,000 | Rural areas in other provinces |
| IV | 3,250,000 | Other regions |

## Data Model

### ISalary Interface
```typescript
interface ISalary {
  gross: number;
  net: number;
  social_insurance: number;
  health_insurance: number;
  unemployment_insurance: number;
  total_insurance: number;
  family_allowances: number;
  dependent_family_allowances: number;
  taxable_income: number;
  income: number;
  personal_income_tax: number[]; // Array of 7 tax bracket amounts
  total_personal_income_tax: number;
  // Employer-side costs
  org_social_insurance: number;
  org_health_insurance: number;
  org_unemployment_insurance: number;
  total_org_payment: number;
}
```

## Employer Contributions

| Type | Rate | Base |
|------|------|------|
| Social Insurance | 17.5% | of insurance salary |
| Health Insurance | 3% | of insurance salary |
| Unemployment Insurance | 1% | of insurance salary |
| **Total Employer Cost** | **21.5%** | of insurance salary |

## Implementation Notes

1. **Backend Calculation**: All complex calculations are handled server-side
2. **Frontend Responsibilities**:
   - Collect user inputs
   - Display calculation results
   - Handle user interactions
   - Show detailed breakdown

3. **Validation Rules**:
   - Salary must be between 1M and 1B VND
   - Maximum 10 dependents
   - Insurance salary cannot be negative

4. **Features**:
   - Real-time calculation
   - Detailed tax bracket breakdown
   - Employer cost estimation
   - Regional adjustment support

## Example Calculations

### Example 1: Gross to Net
- **Input**: Gross salary of 20,000,000 VND, 0 dependents, Region I
- **Insurance Salary**: 20,000,000 VND (same as gross)
- **Insurance Deductions**: 2,100,000 VND (10.5%)
- **Pre-Tax Income**: 17,900,000 VND
- **Allowances**: 11,000,000 VND
- **Taxable Income**: 6,900,000 VND
- **Tax**:
  - 5% on first 5M = 250,000
  - 10% on 1.9M = 190,000
  - **Total Tax**: 440,000 VND
- **Net Salary**: 17,460,000 VND

### Example 2: Net to Gross
- **Target Net**: 15,000,000 VND
- **Iterations**: System calculates backward
- **Result**: Approximately 17,500,000 VND gross needed

## References

- Old Project Code: `docs/migration/old-project/code/modules/Tools/SalaryConversion/`
- API Endpoint: `https://api-dev.finzone.vn/tools/salary`
- Vietnamese Personal Income Tax Law
- Vietnamese Social Insurance Regulations