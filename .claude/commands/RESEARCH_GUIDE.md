# Research & Exploration Commands - Quick Reference

## 🤔 Which Command Should I Use?

### Quick Decision Tree

```
What do you want to do?
│
├─ Learn about a TECHNOLOGY/LIBRARY? (React, Vitest, Prisma, etc.)
│  │
│  ├─ Need comprehensive research → `/research [technology]`
│  └─ Quick comparison of 2-3 options → `/compare [A] vs [B]`
│
└─ Understand YOUR CODEBASE? (how a feature is implemented)
   │
   └─ Explore how something works → `/explore-codebase [feature]`
```

---

## 📚 Commands Overview

| Command | Purpose | Input | Output |
|---------|---------|-------|--------|
| **`/research`** | Research technologies | "React form libraries" | Tech comparison with recommendations |
| **`/compare`** | Quick tech comparison | "Vitest vs Jest" | Side-by-side table |
| **`/explore-codebase`** | Analyze your code | "authentication feature" | Code flow, architecture, dependencies |

---

## 🔍 `/research` - Technology Research

**Use when**: You want to learn about a technology, library, or approach

### Examples

```bash
# Research a topic
/research "React form validation libraries"

# Context-aware research
/research --for="next.js 14" "state management"

# Deep dive
/research --deep "Prisma ORM"

# Quick overview
/research --quick "Bun vs Node.js"
```

### What You Get

- 📊 **Real metrics**: npm downloads, GitHub stars, bundle size
- 🆚 **Comparison table**: if multiple options exist
- ✅ **Pros & Cons**: with specific examples
- 🎯 **Clear recommendation**: tailored to your context
- 📚 **Resources**: docs, tutorials, benchmarks
- 🚀 **Next steps**: actionable implementation guide
- 🔄 **Migration path**: if replacing something

### Output Example

```markdown
## 🔍 Research: React Form Libraries

📊 Metrics:
- React Hook Form: 1.2M/week, 12KB
- Formik: 800K/week, 45KB

🏆 Winner: React Hook Form
- 5x smaller bundle
- Better TypeScript support
- Modern API design

Next Steps:
1. Install: npm i react-hook-form zod
2. Try quick start example
3. Migrate existing forms
```

---

## ⚖️ `/compare` - Quick Technology Comparison

**Use when**: You have 2-3 specific options and need a quick decision

### Examples

```bash
# Compare two options
/compare "Vitest vs Jest"

# Three-way comparison
/compare "Redux vs Zustand vs Jotai"

# Context-specific
/compare --for="next.js" "NextAuth vs Clerk"
```

### What You Get

- 📊 **Side-by-side comparison** table
- ⚡ **Performance** metrics
- 😊 **DX ratings**
- 🏆 **Clear winner** with reasoning
- 🔄 **Migration difficulty**

### Output Example

```markdown
## ⚖️ Vitest vs Jest

| Aspect | Vitest | Jest |
|--------|--------|------|
| Speed | ⚡⚡ 5-10x faster | 🐌 Baseline |
| DX | 😍 Modern | 😊 Familiar |
| Config | ✅ Zero | ⚠️ Complex |

Winner: Vitest for new projects
Use Jest: If migration cost is high
```

---

## 🔍 `/explore-codebase` - Analyze Your Code

**Use when**: You want to understand how a feature is implemented in YOUR project

**Aliases**: `/explore`, `/analyze`

### Examples

```bash
# Explore a feature
/explore-codebase "authentication"
/explore "user profile"

# Find a component
/explore --component="Button"

# Trace API
/explore --api="/api/users"

# Follow flow
/explore --trace="checkout process"
```

### What You Get

- 📁 **File locations**: where the code lives
- 🏗️ **Architecture diagram**: component hierarchy
- 🔄 **Data flow**: step-by-step execution
- 💻 **Code snippets**: key implementation details
- 🔗 **Dependencies**: external and internal
- 🎨 **Patterns used**: design patterns identified
- 🧪 **Test coverage**: what's tested
- 🐛 **Issues/TODOs**: from code comments
- 🚀 **How to modify**: guide for changes

### Output Example

```markdown
## 🔍 Explore: Authentication

📁 Files:
- src/features/auth/LoginForm.tsx
- app/api/auth/[...nextauth]/route.ts
- src/hooks/useAuth.ts

🔄 Flow:
User → LoginForm → useAuth → NextAuth → Session

💻 Key Code:
// LoginForm.tsx
const { login } = useAuth();
```

---

## 🎯 Use Case Examples

### Scenario 1: "I need form validation for my React app"

**Step 1**: Research options
```bash
/research "React form validation"
→ Shows: React Hook Form vs Formik vs Yup
→ Recommends: React Hook Form + Zod
```

**Step 2**: Quick comparison if unsure
```bash
/compare "React Hook Form vs Formik"
→ Clear winner with reasons
```

**Step 3**: Implement
```bash
/plan-react "add form validation with React Hook Form"
→ Implementation plan
```

---

### Scenario 2: "How does authentication work in our app?"

**Step 1**: Explore existing code
```bash
/explore-codebase "authentication"
→ Shows how it's currently implemented
→ Files, flow, patterns used
```

**Step 2**: Plan improvements (if needed)
```bash
/plan-refactor "authentication feature"
→ Refactoring plan based on exploration
```

---

### Scenario 3: "Should we use Prisma or Drizzle?"

**Quick decision**:
```bash
/compare --for="next.js edge" "Prisma vs Drizzle"
→ Side-by-side comparison
→ Recommendation: Drizzle for edge
```

**Deep research** (if needed):
```bash
/research --deep "Drizzle ORM"
→ Comprehensive analysis
→ Migration guide
→ Best practices
```

---

## 📊 Comparison Table

| Aspect | `/research` | `/compare` | `/explore-codebase` |
|--------|-------------|------------|---------------------|
| **Purpose** | Learn NEW tech | Decide between options | Understand EXISTING code |
| **Input** | Technology name | "A vs B" | Feature in your code |
| **Output** | Full research report | Comparison table | Code analysis |
| **Time** | 2-3 min | 30 sec | 1-2 min |
| **Depth** | Comprehensive | Quick overview | Deep |
| **Web search** | ✅ Yes | ✅ Yes | ❌ No (local only) |
| **Code analysis** | ❌ No | ❌ No | ✅ Yes |

---

## 🎓 Best Practices

### For Technology Research

1. **Start with context**: Use `--for="your-stack"`
   ```bash
   /research --for="next.js 14 typescript" "state management"
   ```

2. **Compare when you have options**: Use `/compare` to narrow down
   ```bash
   /compare "Zustand vs Jotai vs Recoil"
   ```

3. **Deep dive when deciding**: Use `--deep` for critical decisions
   ```bash
   /research --deep "Prisma vs Drizzle"  # Major decision
   ```

### For Codebase Exploration

1. **Start broad**: Explore the main feature first
   ```bash
   /explore "authentication"
   ```

2. **Then go specific**: Drill into components
   ```bash
   /explore --component="LoginForm"
   ```

3. **Trace flows**: Understand execution
   ```bash
   /explore --trace="login process"
   ```

4. **Check tests**: See what's covered
   ```bash
   /explore --show-tests "authentication"
   ```

---

## 🔄 Typical Workflows

### Workflow 1: Adding New Feature

```bash
# 1. Research best approach
/research "authentication libraries for Next.js"

# 2. Compare top options
/compare "NextAuth vs Clerk"

# 3. Explore existing patterns in your code
/explore "existing auth implementation"  

# 4. Plan implementation
/plan-react "add OAuth with NextAuth"

# 5. Implement...

# 6. Add tests
/add-tests src/features/auth/LoginForm.tsx
```

### Workflow 2: Refactoring Existing Code

```bash
# 1. Explore current implementation
/explore "user profile feature"

# 2. Research modern approaches
/research "React state management 2024"

# 3. Plan refactoring
/plan-refactor "migrate to Zustand"

# 4. Implement...
```

### Workflow 3: Bug Fixing

```bash
# 1. Explore affected area
/explore "payment flow"

# 2. Trace execution
/explore --trace="checkout process"

# 3. Plan fix
/plan-bugfix "payment fails on Safari"

# 4. Fix...
```

---

## 💡 Pro Tips

### For Research

- ✅ Use `--for` to get tailored recommendations
- ✅ Save important research: `--save=docs/research-orm.md`
- ✅ Use `--quick` when time-sensitive
- ✅ Use `--deep` for architecture decisions

### For Comparison

- ✅ Best for 2-3 options (not 10)
- ✅ Follow up with `/research [winner]` for details
- ✅ Use when you already know the options

### For Exploration

- ✅ Start with high-level feature, then drill down
- ✅ Use `--component` to find specific components
- ✅ Use `--api` to understand endpoints
- ✅ Check `--show-tests` to see coverage gaps

---

## 🚀 Quick Reference

### I want to...

**Learn about a technology**:
```bash
/research [technology]
```

**Decide between options**:
```bash
/compare [A] vs [B]
```

**Understand my code**:
```bash
/explore-codebase [feature]
```

**Find a component**:
```bash
/explore --component="ComponentName"
```

**Trace a flow**:
```bash
/explore --trace="user registration"
```

**Quick tech overview**:
```bash
/research --quick [technology]
```

---

## 📝 Summary

| Command | Think of it as... |
|---------|-------------------|
| `/research` | "Tell me about this technology" |
| `/compare` | "Which one should I use?" |
| `/explore-codebase` | "How does this work in our app?" |

Use the right tool for the right job! 🎯
