# Validation và Testing cho Claude Skills

## 1. Validation Checklist

### 1.1 YAML Frontmatter Validation

Sử dụng checklist này trước khi publish Skill:

```markdown
## YAML Validation Checklist

- [ ] Opening `---` on line 1 (no spaces before)
- [ ] Closing `---` on separate line before content
- [ ] Valid YAML syntax (no tabs, 2 spaces for indentation)
- [ ] `name` field exists and follows naming rules
- [ ] `description` field exists and is < 1024 characters
- [ ] `allowed-tools` (if present) contains valid tool names
- [ ] No extra fields unless required
- [ ] Directory name matches `name` exactly
```

**Tool để validate YAML:**

```bash
# Using Python
python -c "import yaml; yaml.safe_load(open('SKILL.md'))"

# Using yamllint
yamllint SKILL.md

# Using online validator
# https://yaml-online-parser.appspot.com/
```

### 1.2 Content Validation

```markdown
## Content Quality Checklist

### Structure
- [ ] Has `# Skill Name` heading
- [ ] Sections follow standard order
- [ ] Quick Start section present
- [ ] Instructions section present
- [ ] Examples section present

### Instructions
- [ ] Start with confirmation step
- [ ] Steps are numbered
- [ ] Each step has clear action
- [ ] Include error handling
- [ ] Provide alternatives

### Links and References
- [ ] All internal links use forward slashes
- [ ] Relative paths are correct
- [ ] No broken links
- [ ] External links have proper format

### Code Examples
- [ ] All examples are complete
- [ ] Syntax highlighting applied
- [ ] Examples are tested
- [ ] Include imports/dependencies
```

## 2. Testing Methods

### 2.1 Manual Testing Steps

1. **Restart Claude Code**
   ```bash
   # Ensure skill is loaded
   claude --version
   ```

2. **Test Activation**
   - Ask questions matching description
   - Verify skill triggers automatically
   - Check for false positives/negatives

3. **Test Behavior**
   - Follow skill instructions exactly
   - Verify Claude follows steps
   - Check output quality

4. **Test Edge Cases**
   - Invalid inputs
   - Missing dependencies
   - Error scenarios

### 2.2 Automated Validation Script

Tạo script để validate skills:

**File: `scripts/validate-skill.js`**

```javascript
#!/usr/bin/env node

const fs = require('fs');
const yaml = require('js-yaml');
const path = require('path');

function validateSkill(skillPath) {
  const skillFile = path.join(skillPath, 'SKILL.md');

  if (!fs.existsSync(skillFile)) {
    console.error(`❌ SKILL.md not found in ${skillPath}`);
    return false;
  }

  const content = fs.readFileSync(skillFile, 'utf8');

  // Extract frontmatter
  const frontmatterMatch = content.match(/^---\n(.*?)\n---\n(.*)$/s);
  if (!frontmatterMatch) {
    console.error('❌ Invalid YAML frontmatter');
    return false;
  }

  const [, yamlContent, markdownContent] = frontmatterMatch;

  // Parse YAML
  let frontmatter;
  try {
    frontmatter = yaml.load(yamlContent);
  } catch (e) {
    console.error(`❌ YAML parse error: ${e.message}`);
    return false;
  }

  // Validate required fields
  const required = ['name', 'description'];
  const missing = required.filter(field => !frontmatter[field]);
  if (missing.length > 0) {
    console.error(`❌ Missing required fields: ${missing.join(', ')}`);
    return false;
  }

  // Validate name format
  if (!/^[a-z0-9-]+$/.test(frontmatter.name)) {
    console.error('❌ Invalid name format');
    return false;
  }

  // Validate description length
  if (frontmatter.description.length > 1024) {
    console.error('❌ Description too long (>1024 chars)');
    return false;
  }

  // Check markdown content
  if (!markdownContent.includes('#')) {
    console.error('❌ No markdown headings found');
    return false;
  }

  console.log(`✅ Skill ${frontmatter.name} validated successfully`);
  return true;
}

// Usage
const skillPath = process.argv[2] || '.';
validateSkill(skillPath);
```

### 2.3 Lint Configuration

**File: `.eslintrc.js`**

```javascript
module.exports = {
  extends: ['@anthropic/skill-linter'],
  rules: {
    'skill-description-length': ['error', 1024],
    'skill-name-format': ['error', '^[a-z0-9-]+$'],
    'skill-require-instructions': 'error',
    'skill-require-examples': 'error'
  }
};
```

## 3. Testing Scenarios

### 3.1 Activation Testing

Tạo test cases cho skill activation:

**File: `tests/activation.test.js`**

```javascript
const { test, expect } = require('@jest/globals');

describe('Skill Activation Tests', () => {
  const skillName = 'pdf-processor';

  test('should activate on PDF-related queries', async () => {
    const queries = [
      'Can you extract text from this PDF?',
      'I need to merge multiple PDF files',
      'Help me fill out this PDF form'
    ];

    for (const query of queries) {
      const result = await checkSkillActivation(query);
      expect(result.activatedSkill).toBe(skillName);
    }
  });

  test('should not activate on unrelated queries', async () => {
    const queries = [
      'Write a Python function',
      'Create a React component',
      'Set up a database'
    ];

    for (const query of queries) {
      const result = await checkSkillActivation(query);
      expect(result.activatedSkill).not.toBe(skillName);
    }
  });
});
```

### 3.2 Integration Testing

**File: `tests/integration.test.js`**

```javascript
const { test, expect } = require('@jest/globals');
const fs = require('fs').promises;
const path = require('path');

describe('Skill Integration Tests', () => {
  const testDir = path.join(__dirname, 'fixtures');

  beforeEach(async () => {
    // Setup test environment
    await fs.mkdir(testDir, { recursive: true });
  });

  test('should process files correctly', async () => {
    // Create test file
    const testFile = path.join(testDir, 'test.pdf');
    await fs.writeFile(testFile, 'mock pdf content');

    // Execute skill
    const result = await executeSkill('pdf-processor', {
      files: [testFile],
      action: 'extract-text'
    });

    expect(result.success).toBe(true);
    expect(result.text).toBeDefined();
  });

  test('should handle errors gracefully', async () => {
    // Test with invalid file
    const result = await executeSkill('pdf-processor', {
      files: ['/nonexistent.pdf'],
      action: 'extract-text'
    });

    expect(result.success).toBe(false);
    expect(result.error).toContain('File not found');
  });
});
```

## 4. Performance Testing

### 4.1 Response Time Testing

```javascript
// tests/performance.test.js
const { performance } = require('perf_hooks');

async function measureResponseTime(skillName, input) {
  const start = performance.now();
  const result = await executeSkill(skillName, input);
  const end = performance.now();

  return {
    responseTime: end - start,
    result
  };
}

test('skill responds within acceptable time', async () => {
  const { responseTime, result } = await measureResponseTime(
    'pdf-processor',
    { files: ['test.pdf'], action: 'extract-text' }
  );

  // Should respond within 5 seconds for simple tasks
  expect(responseTime).toBeLessThan(5000);
  expect(result.success).toBe(true);
});
```

### 4.2 Memory Usage Testing

```javascript
// tests/memory.test.js
const process = require('process');

async function measureMemoryUsage(skillName, input) {
  const before = process.memoryUsage();

  await executeSkill(skillName, input);

  // Force garbage collection if available
  if (global.gc) {
    global.gc();
  }

  const after = process.memoryUsage();

  return {
    heapUsed: after.heapUsed - before.heapUsed,
    heapTotal: after.heapTotal - before.heapTotal
  };
}

test('skill memory usage is reasonable', async () => {
  const memory = await measureMemoryUsage('pdf-processor', {
    files: ['large-file.pdf'],
    action: 'extract-text'
  });

  // Should not use more than 100MB additional memory
  expect(memory.heapUsed).toBeLessThan(100 * 1024 * 1024);
});
```

## 5. Continuous Integration

### 5.1 GitHub Actions Workflow

**File: `.github/workflows/skill-validation.yml`**

```yaml
name: Skill Validation

on:
  push:
    paths:
      - '.claude/skills/**/SKILL.md'
  pull_request:
    paths:
      - '.claude/skills/**/SKILL.md'

jobs:
  validate:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'

      - name: Install dependencies
        run: |
          npm install js-yaml
          npm install -g yamllint

      - name: Validate YAML syntax
        run: |
          for skill in .claude/skills/*/SKILL.md; do
            echo "Validating $skill"
            yamllint "$skill" || exit 1
          done

      - name: Validate skill structure
        run: node scripts/validate-all-skills.js

      - name: Run skill tests
        run: npm run test:skills

      - name: Check skill activation
        run: npm run test:activation
```

### 5.2 Pre-commit Hook

**File: `.husky/pre-commit`**

```bash
#!/bin/sh

# Validate modified skills
changed_skills=$(git diff --cached --name-only | grep SKILL.md)

if [ -n "$changed_skills" ]; then
  echo "Validating skills..."
  npm run validate:skills

  if [ $? -ne 0 ]; then
    echo "Skill validation failed!"
    exit 1
  fi
fi
```

**File: `package.json`**

```json
{
  "scripts": {
    "validate:skills": "node scripts/validate-all-skills.js",
    "validate:skill": "node scripts/validate-skill.js",
    "test:skills": "jest tests/skills",
    "test:activation": "jest tests/activation"
  },
  "lint-staged": {
    "**/SKILL.md": [
      "yamllint",
      "node scripts/validate-skill.js"
    ]
  }
}
```

## 6. Debugging Tools

### 6.1 Skill Activation Logger

**File: `scripts/debug-activation.js`**

```javascript
// Log skill activation for debugging
const debugLog = {
  query: "",
  matchedSkills: [],
  selectedSkill: "",
  timestamp: new Date().toISOString()
};

// Add to skill selection logic
function logSkillActivation(query, skills, selected) {
  console.log({
    query,
    availableSkills: skills.map(s => s.name),
    selected,
    timestamp: new Date().toISOString()
  });
}
```

### 6.2 Local Testing Environment

**File: `docker/test-env.dockerfile`**

```dockerfile
FROM node:18-alpine

WORKDIR /app

# Install Claude Code
RUN npm install -g @anthropic-ai/claude-code

# Copy validation scripts
COPY scripts/ /app/scripts/
COPY tests/ /app/tests/

# Set up test environment
ENV CLAUDE_TEST_MODE=true
ENV CLAUDE_DEBUG=true

CMD ["npm", "run", "test:all"]
```

## 7. Common Validation Issues

### 7.1 YAML Errors

| Issue | Cause | Fix |
|-------|-------|-----|
| `malformed yaml` | Missing closing `---` | Add closing frontmatter |
| `invalid character` | Tab instead of spaces | Use 2 spaces for indentation |
| `duplicate key` | Same field twice | Remove duplicate |

### 7.2 Activation Issues

| Symptom | Cause | Solution |
|---------|-------|----------|
| Skill not triggering | Description too vague | Add specific trigger words |
| Wrong skill activates | Overlapping descriptions | Make descriptions more distinct |
| Partial activation | Multiple skills match | Use more specific keywords |

### 7.3 Performance Issues

| Issue | Diagnosis | Fix |
|-------|-----------|-----|
| Slow response | Large file processing | Add streaming |
| High memory | Loading entire file | Use chunked processing |
| Timeouts | API calls too slow | Add timeouts and retries |

## 8. Validation Reports

### 8.1 Weekly Validation Report

Tạo automated report:

```javascript
// scripts/generate-validation-report.js
const fs = require('fs');
const path = require('path');

function generateReport() {
  const skillsDir = '.claude/skills';
  const skills = fs.readdirSync(skillsDir);

  const report = {
    date: new Date().toISOString(),
    totalSkills: skills.length,
    validSkills: 0,
    errors: []
  };

  for (const skill of skills) {
    const skillPath = path.join(skillsDir, skill);
    if (validateSkill(skillPath)) {
      report.validSkills++;
    } else {
      report.errors.push(`${skill}: Validation failed`);
    }
  }

  const reportPath = `reports/validation-${Date.now()}.json`;
  fs.writeFileSync(reportPath, JSON.stringify(report, null, 2));

  console.log(`Report generated: ${reportPath}`);
  return report;
}
```

### 8.2 Health Check Dashboard

```javascript
// Simple health check endpoint
app.get('/api/skills/health', (req, res) => {
  const health = {
    status: 'ok',
    timestamp: new Date().toISOString(),
    skills: validateAllSkills()
  };

  res.json(health);
});
```

## 9. Best Practices Summary

### DO:
- ✅ Always ask for confirmation before executing
- ✅ Include comprehensive error handling
- ✅ Test with real data and scenarios
- ✅ Validate YAML syntax separately
- ✅ Use progressive disclosure for complex content

### DON'T:
- ❌ Skip testing edge cases
- ❌ Hardcode sensitive information
- ❌ Use absolute paths
- ❌ Forget to document requirements
- ❌ Ignore performance implications