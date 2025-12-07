# Command: /how

## Quick Summary

**"How does this work?"** - Multi-phase codebase exploration with organized documentation.

## What It Does

Explores your codebase in 4 distinct phases, writing documentation after EACH phase to prevent context loss:

1. **Discovery** 🔍 - Find all relevant files
2. **Structure** 🏗️ - Map architecture and dependencies  
3. **Analysis** 💻 - Deep-dive into code and business logic
4. **Synthesis** 📋 - Comprehensive final report

## Usage

```bash
# Basic
/how "authentication"

# With source path
/how "payment flow" --source=old-app/payments

# Specific phases only
/how "checkout" --phases=1,2

# Comprehensive mode
/how "calculator" --comprehensive
```

## Output

Creates organized documentation in `docs/<topic>/`:

```
docs/authentication/
├── phase-1-discovery.md      # File inventory
├── phase-2-structure.md      # Architecture map
├── phase-3-analysis.md       # Code deep-dive
├── phase-4-synthesis.md      # Complete overview
└── README.md                 # Quick access
```

## Why Use This Instead of /explore-codebase?

| Feature | /explore-codebase | /how |
|---------|-------------------|------|
| **Structure** | Single workflow | 4 distinct phases |
| **Documentation** | One file at end | 4 files (per phase) |
| **Context Loss** | High risk | Prevented |
| **SubAgents** | Not used | 4 specialized agents |
| **Flexibility** | All or nothing | Run specific phases |
| **Organization** | Single file | Organized folder |

## When to Use

- ✅ Understanding complex features
- ✅ Planning migrations or refactors
- ✅ Onboarding new team members
- ✅ Documenting architecture
- ✅ Analyzing business logic

## Comparison with Related Commands

- **`/how`** - Understand existing code in YOUR project
- **`/research`** - Learn about NEW technologies
- **`/auto-plan`** - Explore + analyze + create migration plan
- **`/explore-codebase`** - Original single-phase exploration (still available)

## Full Documentation

See [how.md](./how.md) for complete details.
