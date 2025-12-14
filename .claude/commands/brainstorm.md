---
description: Generate creative solutions using structured brainstorming methods
argument-hint: problem or challenge to brainstorm
---

# /brainstorm - Creative Problem Solving

## Purpose

Generate creative solutions and explore alternatives using structured thinking methods. Invokes the Brainstormer Agent with automatic mode detection (Quick vs Deep mode).

**"Ideas built on structure, not chaos"** - Smart brainstorming through proven frameworks.

## Aliases

```bash
/brainstorm [description]
/bs [description]
```

## Usage

```bash
# Auto-mode detection (default)
/brainstorm "improve error handling"

# Explicit Quick Mode (3-5 ideas, <10 seconds)
/brainstorm --quick "improve error handling"
/brainstorm "quick ideas for caching"

# Explicit Deep Mode (comprehensive analysis)
/brainstorm --deep "design authentication system"

# With specific framework
/brainstorm --framework=scamper "optimize checkout flow"

# Compare multiple approaches
/brainstorm --compare "state management options"

# Save output to file
/brainstorm "API caching" --save=docs/brainstorm-caching.md

# Show help
/brainstorm --help
```

## Arguments

- `$ARGUMENTS`: Problem or challenge to brainstorm

## Flags

| Flag | Description | Default |
|------|-------------|---------|
| `--quick` | Force Quick Mode (3-5 bullet points) | Auto-detect |
| `--deep` | Force Deep Mode (full framework analysis) | Auto-detect |
| `--framework=NAME` | Use specific framework: `six-hats`, `scamper`, `first-principles` | Auto-select |
| `--compare` | Auto-compare multiple approaches | Off |
| `--save=PATH` | Save output to markdown file | Display only |
| `--timeout=N` | Time limit in seconds | 300 (quick) / 600 (deep) |
| `--help` | Show detailed usage and examples | - |

---

## Workflow

### Phase 0: Input Validation \u0026 Help

```markdown
{{ if --help }}
DISPLAY comprehensive help documentation
EXIT
{{ endif }}

{{ if $ARGUMENTS is empty }}
ERROR: "Please provide a problem to brainstorm"
SHOW: /brainstorm --help
EXIT
{{ endif }}
```

---

### Phase 1: Pre-flight Checks

```markdown
**Verify agent files exist:**

REQUIRED_FILES:
- `.claude/agents/brainstormer.md` (main agent)
- `.claude/agents/brainstormer/frameworks.md`
- `.claude/agents/brainstormer/examples.md`
- `.claude/agents/brainstormer/templates.md`

FOR EACH file:
  if NOT exists:
    {{ if main agent }}
      ERROR: "Brainstormer agent not found at .claude/agents/brainstormer.md"
      NOTIFY: "Please ensure agent is properly installed"
      FALLBACK: Use basic brainstorming (no frameworks)
    {{ else }}
      WARN: "$file not found - functionality will be limited"
      LOG: Missing file for debugging
    {{ endif }}
```

---

### Phase 2: Mode Detection

```markdown
**Determine brainstorming mode:**

{{ if --quick flag }}
  MODE: Quick (forced by user)
{{ else if --deep flag }}
  MODE: Deep (forced by user)
{{ else }}
  **Auto-detect mode based on problem:**
  
  QUICK_MODE_TRIGGERS:
  - Keywords in $ARGUMENTS: "quick", "briefly", "fast", "simple", "urgent", "rapid"
  - Problem mentions < 3 files
  - Time indicators: "now", "today", "asap"
  
  {{ if any QUICK_MODE_TRIGGERS matched }}
    MODE: Quick (auto-detected)
  {{ else }}
    MODE: Deep (default for complex problems)
  {{ endif }}
{{ endif }}

DETECTED_MODE: {{ MODE }}
TIMEOUT: {{ if Quick }}300 seconds{{ else }}600 seconds{{ endif }}

{{ if --timeout flag }}
  TIMEOUT: {{ --timeout value }} seconds (user override)
{{ endif }}
```

---

### Phase 3: Framework Selection

```markdown
{{ if --framework flag }}
  FRAMEWORK: {{ --framework value }}
  
  VALIDATE framework:
  {{ if NOT in [six-hats, scamper, first-principles] }}
    ERROR: "Unknown framework: {{ --framework value }}"
    SHOW: Available frameworks:
      - six-hats: Multi-perspective analysis
      - scamper: Systematic variation
      - first-principles: Fundamental breakdown
    EXIT
  {{ endif }}
{{ else }}
  FRAMEWORK: auto-select (agent decides based on problem)
{{ endif }}
```

---

### Phase 4: Agent Dispatch

**Dispatch Brainstormer Agent** (`.claude/agents/brainstormer.md`)

```markdown
Brainstormer Agent - Creative Problem Solving

GOAL: Generate creative solutions for: "$ARGUMENTS"

INPUT CONTEXT:
- Problem: "$ARGUMENTS"
- Mode: {{ DETECTED_MODE }}
- Framework: {{ if --framework }}{{ framework value }}{{ else }}auto-select{{ endif }}
- Timeout: {{ TIMEOUT }} seconds
- Compare mode: {{ if --compare }}enabled{{ else }}disabled{{ endif }}

---

AGENT INSTRUCTIONS:

{{ if MODE == Quick }}
**QUICK MODE** (3-5 ideas, <10 seconds):
1. Skip Step 0 (context gathering) unless critical
2. Generate 3-5 distinct approaches immediately
3. Use Quick Mode template from `.claude/agents/brainstormer/templates.md`:
   - Read template with: Read .claude/agents/brainstormer/templates.md
   - Look for "Quick Mode Template" section
4. Keep each idea to 2-3 lines max
5. Basic trade-offs only (pros/cons/effort)
6. Target response time: <10 seconds

OUTPUT FORMAT:
```markdown
## Quick Ideas: {{ PROBLEM }}

### Option 1: [Name]
- **What**: [One sentence]
- **Pros**: [Brief]
- **Cons**: [Brief]
- **Effort**: [Time estimate]

### Option 2: [Name]
[Same format]

### Option 3: [Name]
[Same format]

**Recommend**: [Option X] because [one sentence rationale]
```
{{ endif }}

{{ if MODE == Deep }}
**DEEP MODE** (Comprehensive analysis):

Follow your complete workflow:

**Step 0: Context Gathering**
- Use `Glob` to understand project structure
- Use `Read` to inspect relevant files
- Use `WebSearch` for current best practices (if needed)

**Step 1-3: Full Brainstorming Process**
- Understand the Problem
- Generate Options (5+ approaches)
- Evaluate \u0026 Recommend

**Framework Usage:**
{{ if --framework specified }}
- Read `.claude/agents/brainstormer/frameworks.md`
- Use ONLY {{ --framework }} framework
{{ else }}
- Read `.claude/agents/brainstormer/frameworks.md`
- Select most appropriate framework for this problem
{{ endif }}

**Examples Reference:**
- Read `.claude/agents/brainstormer/examples.md`
- Look for similar patterns to inform your analysis

**Output Template:**
- Read `.claude/agents/brainstormer/templates.md`
- Use "Brainstorm Session" template
- Include comparison matrix
- Provide validation artifact (pseudo-code/diagram)

**Quality Standards:**
- 5+ distinct approaches (or 3-5 if problem is simpler)
- At least 1 "wild card" idea
- Detailed trade-off analysis for top 2-3
- Validation artifact for top recommendation
{{ endif }}

{{ if --compare }}
**COMPARE MODE ENABLED:**
- Generate approaches as normal
- Add side-by-side comparison table
- Highlight key differentiators
- Provide decision guide (when to use each)
{{ endif }}

---

TIMEOUT: {{ TIMEOUT }} seconds
{{ if timeout exceeded }}
  NOTIFY: "Brainstorming taking longer than expected..."
  OPTIONS:
    1. Continue (auto-extend timeout)
    2. Return partial results
    3. Switch to Quick Mode
{{ endif }}
```

---

### Phase 5: Output Formatting

```markdown
**Format and present results:**

{{ if --save flag }}
  **Save to file**: {{ --save path }}
  
  VALIDATE path:
  {{ if path is invalid or not writable }}
    ERROR: "Invalid save path: {{ --save path }}"
    SUGGEST: "Use relative or absolute path, e.g., docs/brainstorm.md"
    EXIT
  {{ endif }}
  
  **File content:**
  ```markdown
  # Brainstorm Session: {{ PROBLEM }}
  
  **Date**: {{ current date }}
  **Mode**: {{ DETECTED_MODE }}
  **Framework**: {{ if used }}{{ framework name }}{{ else }}Auto-selected{{ endif }}
  
  ---
  
  {{ agent output }}
  
  ---
  
  **Generated by**: /brainstorm command
  **Agent**: Brainstormer (`.claude/agents/brainstormer.md`)
  ```
  
  SAVE to file
  NOTIFY: "Brainstorming output saved to {{ --save path }}"
  
{{ else }}
  **Display in chat:**
  
  ```markdown
  {{ agent output }}
  ```
  
  **Next Steps Suggestion:**
  ```
  💡 Want to implement an idea?
  → /plan "{{ chosen approach }}" to create implementation plan
  
  💡 Need more research?
  → /research {{ technology mentioned }} for detailed analysis
  
  💡 Compare options deeper?
  → /compare "{{ option A }} vs {{ option B }}"
  ```
{{ endif }}
```

---

## Help Documentation

When user runs `/brainstorm --help`, display:

```markdown
# /brainstorm - Creative Problem Solving

## Description
Generate creative solutions using structured brainstorming methods.
Invokes the Brainstormer Agent with automatic mode detection.

## Quick Start

**Auto-mode (recommended)**:
```bash
/brainstorm "your problem or challenge"
```
Automatically detects Quick vs Deep mode based on problem complexity.

## Modes

### Quick Mode (3-5 Ideas, <10 seconds)
**Triggers**: Keywords like "quick", "briefly", "fast", "simple", "urgent"
**Best for**: Rapid idea generation, brainstorming sessions, simple problems

```bash
/brainstorm --quick "improve error handling"
/brainstorm "quick ideas for caching"
```

### Deep Mode (Comprehensive Analysis)
**Triggers**: Default for complex problems
**Best for**: Architecture decisions, feature design, complex challenges

```bash
/brainstorm --deep "design authentication system"
/brainstorm "microservices architecture"
```

## Frameworks

Use specific brainstorming frameworks:

```bash
# Six Thinking Hats - Multi-perspective analysis
/brainstorm --framework=six-hats "team collaboration issue"

# SCAMPER - Systematic variation
/brainstorm --framework=scamper "improve checkout flow"

# First Principles - Fundamental breakdown
/brainstorm --framework=first-principles "reduce infrastructure costs"
```

**Available frameworks**:
- `six-hats`: Facts, feelings, risks, benefits, creativity, process
- `scamper`: Substitute, Combine, Adapt, Modify, Put to other uses, Eliminate, Rearrange
- `first-principles`: Break down to fundamentals and rebuild

## Advanced Features

### Compare Approaches
```bash
/brainstorm --compare "state management options"
```
Generates side-by-side comparison with decision guide.

### Save Output
```bash
/brainstorm "API caching" --save=docs/brainstorm-caching.md
/brainstorm "auth system" --save=temp/brainstorm-$(date +%Y%m%d).md
```

### Timeout Control
```bash
/brainstorm "complex system design" --timeout=900  # 15 minutes
```

## Examples

### Example 1: Quick Ideas
```bash
/brainstorm --quick "improve API error handling"
```
**Output**: 3-5 approaches with pros/cons/effort in <10 seconds

### Example 2: Architecture Decision
```bash
/brainstorm "should we use microservices?"
```
**Output**: 5+ approaches with comparison matrix and validation artifacts

### Example 3: Framework-Specific
```bash
/brainstorm --framework=scamper "optimize checkout flow"
```
**Output**: SCAMPER analysis with concrete suggestions

## Tips for Best Results

- **Be specific**: "improve error handling in REST API" > "errors"
- **Mention constraints**: "design auth with <50ms latency"
- **Add context**: "caching for e-commerce site with 10K users/day"
- **Use Quick Mode** for brainstorming sessions
- **Use Deep Mode** for documentation and decision records
- **Save outputs** for team review and decision tracking

## Related Commands

After brainstorming:
- `/plan $TOPIC` - Create implementation plan from chosen approach  
- `/research $TECH` - Research specific technologies mentioned
- `/compare $A vs $B` - Deep comparison of alternatives

## Troubleshooting

**Problem**: "Brainstormer agent not found"  
**Solution**: Ensure `.claude/agents/brainstormer.md` exists

**Problem**: Timeout errors  
**Solution**: Use `--timeout=N` to extend or switch to `--quick`

**Problem**: Too generic output  
**Solution**: Provide more context and constraints in problem description

**Problem**: Wrong mode detected  
**Solution**: Use `--quick` or `--deep` flag to override auto-detection

## All Flags

| Flag | Description | Default |
|------|-------------|---------|
| `--quick` | Force Quick Mode | Auto-detect |
| `--deep` | Force Deep Mode | Auto-detect |
| `--framework=NAME` | Use specific framework | Auto-select |
| `--compare` | Compare multiple approaches | Off |
| `--save=PATH` | Save to markdown file | Display only |
| `--timeout=N` | Time limit (seconds) | 300/600 |
| `--help` | Show this help | - |
```

---

## Error Handling Summary

**Missing agent file:**
- Show error message
- Fallback to basic brainstorming
- Notify user to check installation

**Invalid framework:**
- Show error with available options
- Exit gracefully

**Empty input:**
- Show error
- Display help

**Invalid save path:**
- Show error with suggestion
- Exit gracefully

**Timeout exceeded:**
- Notify user
- Offer options (continue/partial/quick mode)

---

## Related Commands

```bash
/plan [approach]       # Plan implementation after brainstorming
/research [topic]      # Research technologies mentioned
/compare [A] vs [B]    # Deep comparison
/how [feature]         # Understand existing codebase
```

---

**Remember**: Brainstorming should lead to **action**. After brainstorming, use `/plan` to create an implementation strategy!
