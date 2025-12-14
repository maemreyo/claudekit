# Ví dụ thực tế về Claude Skills

## 1. Skills từ cơ bản đến nâng cao

### 1.1 Skill đơn giản - Git Commit Helper

**File: `.claude/skills/commit-helper/SKILL.md`**

```yaml
---
name: commit-helper
description: Help write git commit messages following conventional commits. Use when preparing to commit code or when user mentions "commit", "git commit", or needs help with commit message format.
---

# Git Commit Helper

## Quick Start

1. Check staged changes
2. Suggest commit message in format: `type(scope): description`
3. Include breaking changes if applicable

## Instructions

1. Always ask: "Do you want me to write a commit message for these changes?"
2. Run `git status` and `git diff --cached` to analyze changes
3. Determine commit type based on changes:
   - `feat`: New features
   - `fix`: Bug fixes
   - `docs`: Documentation changes
   - `style`: Code formatting
   - `refactor`: Code refactoring
   - `test`: Adding tests
   - `chore`: Maintenance tasks
4. Write message in format: `type(scope): description`
5. Add body if explanation needed
6. Add footer for breaking changes or issues

## Examples

### Feature addition
```
feat(auth): add OAuth2 login support

- Implement Google OAuth2 provider
- Add JWT token management
- Update user model with OAuth fields

Closes #123
```

### Bug fix
```
fix(api): handle null response in user endpoint

Prevents crash when API returns null data
```

## Best Practices

- Keep description under 50 characters
- Use imperative mood ("add" not "added")
- Reference issues with `Closes #number`
```

### 1.2 Skill với Tool Permissions - Code Reviewer

**File: `.claude/skills/code-reviewer/SKILL.md`**

```yaml
---
name: code-reviewer
description: Analyze code for quality, security issues, and best practices. Use when asked to review, audit, or analyze code for potential improvements.
allowed-tools: Read, Grep, Glob
---

# Code Reviewer

## Quick Start

1. Read the provided code files
2. Check for common issues and patterns
3. Provide specific, actionable feedback

## Instructions

1. Always confirm before starting: "Would you like me to review this code?"
2. Read all provided files to understand context
3. Check for the following categories:

### Security Issues
- SQL injection vulnerabilities
- XSS possibilities
- Authentication/authorization gaps
- Hardcoded secrets or keys
- Input validation missing

### Performance Issues
- Inefficient loops or algorithms
- Memory leaks potential
- Unnecessary API calls
- Missing caching opportunities

### Code Quality
- Code style consistency
- Proper error handling
- Type safety
- Documentation completeness
- Test coverage gaps

4. Provide feedback in format:
   - **Issue**: Clear description
   - **Location**: File:line
   - **Severity**: Critical/High/Medium/Low
   - **Suggestion**: Specific fix recommendation

## Examples

### Security Issue
**Issue**: Potential SQL injection in user query
**Location**: `src/userService.js:45`
**Severity**: Critical
**Suggestion**: Use parameterized queries instead of string concatenation

```javascript
// Current (vulnerable)
const query = `SELECT * FROM users WHERE id = ${userId}`;

// Fixed
const query = 'SELECT * FROM users WHERE id = ?';
db.query(query, [userId]);
```

### Performance Issue
**Issue**: O(n²) loop for finding duplicates
**Location**: `src/utils.js:123`
**Severity**: Medium
**Suggestion**: Use Set for O(n) lookup

## Best Practices

- Focus on actionable feedback
- Explain why changes are needed
- Provide code examples for fixes
- Prioritize issues by severity
- Suggest specific refactoring approaches
```

### 1.3 Skill Multi-file - API Designer

**File: `.claude/skills/api-designer/SKILL.md`**

```yaml
---
name: api-designer
description: Design REST APIs following industry best practices. Use when creating new APIs, designing endpoints, or planning API architecture.
---

# API Designer

Quick examples: [See examples](resources/examples.md)
Design patterns: [See reference](resources/reference.md)

## Quick Start

1. Understand requirements and data models
2. Design resource endpoints following REST conventions
3. Define request/response schemas
4. Document with OpenAPI specification

## Instructions

1. **Gather Requirements**
   - Ask about data models and relationships
   - Identify CRUD operations needed
   - Understand authentication requirements
   - Check for special business logic

2. **Design Resources**
   - Use nouns for resource names (plural)
   - Follow REST verb conventions
   - Implement proper HTTP status codes
   - Design consistent URL patterns

3. **Define Schemas**
   - Create request/response schemas
   - Include validation rules
   - Document all fields and types
   - Handle error responses

4. **Document API**
   - Create OpenAPI/Swagger spec
   - Include examples for each endpoint
   - Document authentication method
   - Add rate limiting information

## Resources

- **Complete examples**: [examples.md](resources/examples.md)
- **Design patterns**: [reference.md](resources/reference.md)
- **OpenAPI templates**: [templates/openapi.yaml](templates/openapi.yaml)
```

**File: `.claude/skills/api-designer/resources/examples.md`**

```markdown
# API Design Examples

## Blog API Example

### Endpoints
```
GET    /posts              # List all posts
GET    /posts/:id          # Get single post
POST   /posts              # Create new post
PUT    /posts/:id          # Update post
DELETE /posts/:id          # Delete post

GET    /posts/:id/comments # Get post comments
POST   /posts/:id/comments # Add comment to post
```

### Response Schema
```json
{
  "id": "string",
  "title": "string",
  "content": "string",
  "author": {
    "id": "string",
    "name": "string"
  },
  "createdAt": "datetime",
  "updatedAt": "datetime"
}
```

## E-commerce API Example

### Product Endpoints
```
GET    /products              # List products with filters
GET    /products/:id          # Product details
GET    /products/search       # Search products
POST   /products              # Create product (admin)

GET    /products/:id/reviews  # Product reviews
POST   /products/:id/reviews  # Add review
```

### Filtering Example
```
GET /products?category=electronics&sort=price&limit=10&offset=0
```
```

### 1.4 Skill với Scripts - Database Migrator

**File: `.claude/skills/db-migrator/SKILL.md`**

```yaml
---
name: db-migrator
description: Create and manage database migrations with automatic rollback support. Use when setting up new databases, updating schemas, or managing data versioning.
allowed-tools: Read, Write, Bash
---

# Database Migrator

## Quick Start

1. Create migration: `npm run migration:create add_users_table`
2. Run migrations: `npm run migration:run`
3. Check status: `npm run migration:status`

## Instructions

1. **Before writing code, always ask**: "Do you want me to create a database migration for this?"

2. **Migration Naming**
   - Use descriptive names: `add_users_table`, `remove_legacy_fields`
   - Follow convention: `verb_noun_table`

3. **Create Migration Files**
   - Use [migration template](scripts/create-migration.js)
   - Include up() and down() methods
   - Add proper error handling

4. **Test Migrations**
   - Run on test database first
   - Verify rollback works
   - Check data integrity

## Usage Examples

### Create users table migration
```bash
node scripts/create-migration.js create_users_table
```

### Run specific migration
```bash
npm run migration:run -- --migration=20240101000000_create_users_table
```

### Rollback migration
```bash
npm run migration:rollback
```

## Scripts

- **Create migration**: [scripts/create-migration.js](scripts/create-migration.js)
- **Migration template**: [templates/migration.js](templates/migration.js)
- **Database config**: [config/database.js](config/database.js)

## Requirements

- Node.js >= 16
- PostgreSQL driver: `npm install pg`
- Migration runner: `npm install db-migrate`
```

## 2. Skills theo Categories

### 2.1 Development Workflow Skills

#### Code Generator
```yaml
---
name: code-generator
description: Generate boilerplate code for common patterns. Use when creating new components, services, or API endpoints.
---

Trước khi viết code, hãy luôn hỏi: "Bạn có muốn tôi tạo code boilerplate cho việc này không?"

# Examples
## React Component
```typescript
// src/components/Button.tsx
import React from 'react';

interface ButtonProps {
  children: React.ReactNode;
  onClick: () => void;
  variant?: 'primary' | 'secondary';
}

export const Button: React.FC<ButtonProps> = ({
  children,
  onClick,
  variant = 'primary'
}) => {
  return (
    <button
      className={`btn btn-${variant}`}
      onClick={onClick}
    >
      {children}
    </button>
  );
};
```
```

#### Test Creator
```yaml
---
name: test-creator
description: Create comprehensive test suites for existing code. Use when user asks to write tests, add test coverage, or implement TDD.
---

Luôn hỏi trước khi viết: "Bạn có muốn tôi viết tests cho code này không?"

## Test Generation Process
1. Analyze the code structure
2. Identify test cases:
   - Happy path
   - Edge cases
   - Error conditions
3. Create test files
4. Add test utilities if needed
```

### 2.2 Language-Specific Skills

#### Python FastAPI
```yaml
---
name: fastapi-boilerplate
description: Create FastAPI applications with best practices including auth, database, and testing. Use when starting new FastAPI projects or adding features.
---

## Quick Start
1. Ask: "Do you want me to create a FastAPI application?"
2. Choose features: Auth, Database, Tests
3. Generate project structure
4. Install dependencies

## Project Structure
```
app/
├── main.py
├── routers/
├── models/
├── schemas/
├── services/
└── tests/
```
```

#### TypeScript React
```yaml
---
name: react-component-creator
description: Create TypeScript React components with proper typing and testing. Use when building UI components or React applications.
---

## Component Template
```typescript
import React from 'react';

interface ComponentNameProps {
  // Define props here
}

export const ComponentName: React.FC<ComponentNameProps> = ({
  // Destructure props
}) => {
  return (
    <div>
      {/* Component JSX */}
    </div>
  );
};

export default ComponentName;
```
```

### 2.3 DevOps Skills

#### Docker Setup
```yaml
---
name: docker-setup
description: Create Docker configurations for applications. Use when containerizing apps, setting up development environments, or deploying with Docker.
---

## Process
1. Always ask: "Do you want me to create Docker configuration?"
2. Analyze application stack
3. Create Dockerfile
4. Create docker-compose.yml
5. Add .dockerignore
6. Provide usage instructions

## Multi-stage Example
```dockerfile
# Build stage
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

# Production stage
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/node_modules ./node_modules
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```
```

#### CI/CD Pipeline
```yaml
---
name: cicd-pipeline
description: Create GitHub Actions workflows for CI/CD. Use when setting up automated testing, deployment, or release processes.
---

## Workflow Template
```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
      - name: Install dependencies
        run: npm ci
      - name: Run tests
        run: npm test
      - name: Upload coverage
        uses: codecov/codecov-action@v3
```
```

## 3. Advanced Patterns

### 3.1 Conditional Skills

Skills thay đổi behavior dựa trên context:

```yaml
---
name: smart-logger
description: Intelligent logging solution that adapts to environment. Use in development for detailed logs, production for structured logging, or testing for mock logging.
---

## Environment Detection
- Development: Verbose logging with colors
- Production: JSON format, reduced verbosity
- Testing: Silent or mock logging
```

### 3.2 Interactive Skills

Skills yêu cầu user input:

```yaml
---
name: interactive-wizard
description: Guide users through complex setup processes with step-by-step prompts. Use for project initialization, configuration, or complex installations.
---

## Wizard Flow
1. Ask clarifying questions about requirements
2. Present options with numbers/letters
3. Wait for user selection
4. Continue based on choice
5. Show summary before execution
```

### 3.3 Integration Skills

Kết nối multiple tools/services:

```yaml
---
name: slack-notifier
description: Send notifications to Slack based on events. Use when integrating Slack with CI/CD, monitoring, or automated processes.
---

## Integration Steps
1. Ask for Slack webhook URL
2. Configure message format
3. Set up triggers
4. Test integration
5. Provide documentation
```

## 4. Tips từ thực tế

### 4.1 Common Mistakes

1. **Forgot to ask for confirmation**
   - Luôn hỏi trước khi thực hiện changes
   - Ví dụ: "Do you want me to create X for you?"

2. **Too generic description**
   - Cần specific về functionality
   - Include trigger words

3. **Missing error handling**
   - Luôn include error scenarios
   - Provide troubleshooting steps

### 4.2 Success Factors

1. **Clear entry point**
   - Quick start section
   - Simple 2-3 steps

2. **Progressive disclosure**
   - Basic in SKILL.md
   - Advanced in separate files

3. **Real examples**
   - Complete, runnable code
   - Include both success and error cases

4. **Testing guidance**
   - Commands to run
   - Expected outputs

### 4.3 Maintenance Tips

1. **Version your skills**
2. **Keep examples up to date**
3. **Add FAQ for common issues**
4. **Monitor for breaking changes** in dependencies