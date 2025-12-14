# Troubleshooting Guide cho Claude Skills

## 1. Issues Khai báo và Cấu trúc

### 1.1 Skill không được nhận diện

**Symptoms:**
- Skill không activate khi hỏi relevant questions
- Claude không nhận biết skill tồn tại
- Error "Skill not found"

**Solutions:**

1. **Kiểm tra file location**
   ```bash
   # Personal skills
   ls ~/.claude/skills/your-skill/SKILL.md

   # Project skills
   ls .claude/skills/your-skill/SKILL.md
   ```

2. **Verify directory name matches frontmatter**
   ```bash
   # Tên thư mục phải khớp với name trong YAML
   # Nếu name: "pdf-processor" thì thư mục là "pdf-processor"
   ```

3. **Restart Claude Code**
   ```bash
   # Restart để load lại skills
   claude --restart
   ```

4. **Check YAML syntax**
   ```bash
   # Validate YAML
   python3 -c "import yaml; print(yaml.safe_load(open('SKILL.md')))"
   ```

### 1.2 YAML Frontmatter Errors

**Common errors:**

```yaml
# ❌ Sai - Thụt lề không đúng
---
 name: my-skill
  description: Wrong indentation
---

# ❌ Sai - Thiếu closing ---
---
name: my-skill
description: Missing closing frontmatter

# ✅ Đúng
---
name: my-skill
description: Correct YAML format
---
```

**Debug steps:**

1. **Check for tabs** (YAML không accept tabs):
   ```bash
   cat -A SKILL.md | grep '\t'
   # Nếu có ^I characters, replace tabs với spaces
   sed -i '' 's/\t/  /g' SKILL.md
   ```

2. **Validate with online tool**
   - https://yaml-online-parser.appspot.com/
   - Paste content vào để validate

3. **Check special characters**
   ```yaml
   # ❌ Sai - Special characters
   name: "skill-with-quotes"

   # ✅ Đúng
   name: skill-without-quotes
   ```

### 1.3 Description quá chung chung

**Problem:** Skill không trigger vì description không có trigger words

**Examples:**

```yaml
# ❌ Quá chung chung
description: Helps with documents
description: A tool for developers

# ✅ Specific với trigger words
description: Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when user mentions "PDF", "forms", or "document extraction".
```

**Fix formula:**
```
[What it does] + [When to use it] + [File types] + [Operations] + [Trigger phrases]
```

## 2. Runtime Issues

### 2.1 Tool Permission Errors

**Error:**
```
Tool X is not allowed for skill Y
```

**Solutions:**

1. **Add allowed-tools to frontmatter**
   ```yaml
   ---
   name: code-reviewer
   description: Analyze code quality
   allowed-tools: Read, Grep, Glob
   ---
   ```

2. **Check tool names**
   ```yaml
   # Valid tools:
   allowed-tools: Read, Write, Edit, Grep, Glob, Bash

   # Invalid tools:
   allowed-tools: read, write, cat, ls  # Wrong case/names
   ```

3. **Remove allowed-tools if full access needed**
   ```yaml
   # Full access - no allowed-tools field
   ---
   name: full-access-skill
   description: Needs full tool access
   ---
   ```

### 2.2 File Path Issues

**Common path errors:**

```markdown
<!-- ❌ Sai - Absolute paths -->
See /Users/name/project/resources/reference.md
Run C:\scripts\helper.py

<!-- ❌ Sai - Backslashes on Windows -->
See resources\reference.md

<!-- ❌ Sai - Relative too deep -->
See ../../docs/reference.md

<!-- ✅ Đúng - Relative forward slashes -->
See resources/reference.md
Run scripts/helper.py
```

**Debug script:**

```bash
# Test relative paths from skill directory
cd .claude/skills/your-skill
ls resources/reference.md
ls scripts/helper.py
```

### 2.3 Dependencies Not Found

**Error:**
```
ModuleNotFoundError: No module named 'xxx'
```

**Solutions:**

1. **List requirements clearly**
   ```markdown
   ## Requirements

   ```bash
   pip install pandas numpy
   npm install -D vitest
   ```
   ```

2. **Check Python version**
   ```markdown
   ## Requirements

   - Python >= 3.8
   - Node.js >= 16
   ```

3. **Provide installation commands**
   ```markdown
   ## Setup

   ```bash
   # Install Python dependencies
   pip install -r requirements.txt

   # Install Node dependencies
   npm install
   ```
   ```

## 3. Performance Issues

### 3.1 Skill Response Slow

**Diagnosis:**

```javascript
// Add timing logs to skill
console.time('skill-execution');
// ... skill logic ...
console.timeEnd('skill-execution');
```

**Common causes:**

1. **Large file processing**
   ```typescript
   // ❌ Loading entire file
   const content = fs.readFileSync('large-file.txt');

   // ✅ Streaming
   const stream = fs.createReadStream('large-file.txt');
   for await (const chunk of stream) {
     await processChunk(chunk);
   }
   ```

2. **Multiple API calls**
   ```typescript
   // ❌ Sequential calls
   const result1 = await api.call1();
   const result2 = await api.call2();

   // ✅ Parallel calls
   const [result1, result2] = await Promise.all([
     api.call1(),
     api.call2()
   ]);
   ```

3. **Inefficient loops**
   ```typescript
   // ❌ O(n²) search
   for (const item1 of array1) {
     for (const item2 of array2) {
       if (item1.id === item2.id) { /* ... */ }
     }
   }

   // ✅ O(n) with Map
   const map2 = new Map(array2.map(item => [item.id, item]));
   for (const item1 of array1) {
     const match = map2.get(item1.id);
     if (match) { /* ... */ }
   }
   ```

### 3.2 Memory Usage High

**Symptoms:**
- System slows down
- Out of memory errors
- Claude responses timeout

**Solutions:**

1. **Process in chunks**
   ```typescript
   // Process large arrays in chunks
   const chunkSize = 1000;
   for (let i = 0; i < largeArray.length; i += chunkSize) {
     const chunk = largeArray.slice(i, i + chunkSize);
     await processChunk(chunk);

     // Force garbage collection if available
     if (global.gc) global.gc();
   }
   ```

2. **Stream processing**
   ```typescript
   // Don't load entire file
   // Use streams for large files
   ```

3. **Clear caches**
   ```typescript
   // Clear unused data
   cache.clear();
   bigArray = null; // Allow GC
   ```

## 4. Content Issues

### 4.1 Instructions Not Followed

**Problem:** Claude không theo instructions

**Solutions:**

1. **Make instructions more explicit**
   ```markdown
   # ❌ Vague
   Check the code and fix issues

   # ✅ Specific
   1. Read all provided files
   2. Look for security vulnerabilities
   3. Check for performance issues
   4. Provide specific line-by-line feedback
   ```

2. **Add confirmation step**
   ```markdown
   ## Instructions

   1. **Before starting, always ask**: "Do you want me to [action]?"
   2. Wait for explicit confirmation ("yes" or similar)
   3. Then proceed with the task
   ```

3. **Use numbered steps**
   ```markdown
   1. First specific action
   2. Second specific action
   3. Third specific action
   ```

### 4.2 Examples Not Working

**Common issues:**

1. **Missing imports**
   ```typescript
   // ❌ Missing import
   const user = new User();

   // ✅ Complete example
   import { User } from './models/User';
   const user = new User();
   ```

2. **Outdated syntax**
   ```typescript
   // ❌ Old syntax
   const promise = new Promise((resolve, reject) => {
     // ...
   });

   // ✅ Modern syntax
   const promise = Promise.resolve();
   ```

3. **Configuration missing**
   ```typescript
   // ❌ Missing config
   const db = Database.connect();

   // ✅ With config
   const config = {
     host: process.env.DB_HOST,
     port: process.env.DB_PORT
   };
   const db = Database.connect(config);
   ```

## 5. Debugging Tools

### 5.1 Enable Debug Mode

```bash
# Run Claude with debug
claude --debug

# Check skill loading
claude --debug --list-skills

# Test specific skill
claude --debug --test-skill pdf-processor
```

### 5.2 Local Testing Script

**File: `scripts/test-skill.js`**

```javascript
#!/usr/bin/env node

const { spawn } = require('child_process');
const fs = require('fs');

async function testSkill(skillName, testQuery) {
  console.log(`Testing skill: ${skillName}`);
  console.log(`Query: ${testQuery}`);

  // Test with Claude Code CLI
  const claude = spawn('claude', ['--debug'], {
    env: { ...process.env, CLAUDE_TEST_MODE: '1' }
  });

  claude.stdin.write(testQuery);
  claude.stdin.end();

  claude.stdout.on('data', (data) => {
    console.log('Output:', data.toString());
  });

  claude.stderr.on('data', (data) => {
    console.error('Error:', data.toString());
  });
}

// Usage
testSkill('pdf-processor', 'Extract text from this PDF file');
```

### 5.3 Skill Health Check

**File: `scripts/health-check.js`**

```javascript
const skills = [
  'pdf-processor',
  'code-reviewer',
  'commit-helper'
];

async function healthCheck() {
  for (const skill of skills) {
    try {
      const skillPath = `.claude/skills/${skill}/SKILL.md`;
      const content = fs.readFileSync(skillPath, 'utf8');

      // Parse YAML
      const frontmatter = content.match(/^---\n(.*?)\n---/s);
      if (!frontmatter) {
        throw new Error('Invalid YAML');
      }

      console.log(`✅ ${skill}: OK`);
    } catch (error) {
      console.error(`❌ ${skill}: ${error.message}`);
    }
  }
}

healthCheck();
```

## 6. Advanced Troubleshooting

### 6.1 Skill Activation Matrix

Tạo matrix để test activation:

| Query | Expected Skill | Actual | Pass/Fail |
|-------|---------------|---------|-----------|
| "Extract text from PDF" | pdf-processor | pdf-processor | ✅ |
| "Review my code" | code-reviewer | none | ❌ |
| "Help with git commit" | commit-helper | commit-helper | ✅ |

**Automation script:**

```javascript
// scripts/test-activation-matrix.js
const testCases = [
  { query: 'Extract text from PDF', expected: 'pdf-processor' },
  { query: 'Review this code', expected: 'code-reviewer' },
  { query: 'Help me commit', expected: 'commit-helper' }
];

async function testActivationMatrix() {
  for (const test of testCases) {
    const actual = await getActivatedSkill(test.query);
    const pass = actual === test.expected;

    console.log(`${pass ? '✅' : '❌'} "${test.query}"`);
    console.log(`  Expected: ${test.expected}`);
    console.log(`  Actual: ${actual || 'none'}`);
  }
}
```

### 6.2 Performance Profiling

```javascript
// scripts/profile-skill.js
const { performance } = require('perf_hooks');

async function profileSkill(skillName, input) {
  const metrics = {
    memoryBefore: process.memoryUsage(),
    startTime: performance.now()
  };

  await executeSkill(skillName, input);

  const memoryAfter = process.memoryUsage();
  metrics.endTime = performance.now();
  metrics.duration = metrics.endTime - metrics.startTime;
  metrics.memoryDelta = memoryAfter.heapUsed - metrics.memoryBefore.heapUsed;

  console.log(`Skill: ${skillName}`);
  console.log(`Duration: ${metrics.duration}ms`);
  console.log(`Memory delta: ${metrics.memoryDelta / 1024 / 1024}MB`);
}
```

## 7. Common Gotchas

### 7.1 Edge Cases to Watch

1. **Windows vs Unix paths**
   ```bash
   # Cross-platform path handling
   const path = require('path');
   const configPath = path.join('config', 'settings.json');
   ```

2. **Environment variables**
   ```markdown
   ## Requirements

   Environment variables:
   - API_KEY (required)
   - API_URL (optional, defaults to https://api.example.com)
   ```

3. **Network timeouts**
   ```typescript
   // Always include timeouts
   const response = await fetch(url, {
     signal: AbortSignal.timeout(10000) // 10 second timeout
   });
   ```

### 7.2 Prevention Checklist

Before creating a skill, verify:

- [ ] YAML validates
- [ ] Name follows conventions
- [ ] Description is specific
- [ ] Instructions are clear
- [ ] Examples are complete
- [ ] Dependencies are listed
- [ ] Error handling included
- [ ] File paths are correct

## 8. Getting Help

### 8.1 Debug Information to Collect

Khi báo lỗi, thu thập:

1. **System info**
   ```bash
   claude --version
   node --version
   npm --version
   ```

2. **Skill content**
   ```bash
   cat .claude/skills/your-skill/SKILL.md
   ```

3. **Debug output**
   ```bash
   claude --debug 2>&1 | tee debug.log
   ```

4. **Error messages**
   - Full stack trace
   - Steps to reproduce
   - Expected vs actual behavior

### 8.2 Community Resources

- **GitHub Issues**: https://github.com/anthropics/claude-code/issues
- **Documentation**: https://code.claude.com/docs/en/skills
- **Discord Community**: Claude Code Discord server
- **Stack Overflow**: Tag with `claude-code` and `claude-skills`

### 8.3 Template for Bug Reports

```markdown
## Bug Report - [Skill Name]

### Description
Brief description of the issue

### Steps to Reproduce
1. Step 1
2. Step 2
3. Step 3

### Expected Behavior
What should happen

### Actual Behavior
What actually happens

### Environment
- OS: [e.g., macOS 14.0]
- Claude Code Version: [e.g., 1.0.0]
- Node.js Version: [e.g., 18.17.0]

### Skill Content
```yaml
[Your SKILL.md content]
```

### Additional Context
Any other relevant information
```