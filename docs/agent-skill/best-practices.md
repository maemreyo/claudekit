# Best Practices cho Claude Skills

## 1. Design Principles

### 1.1 Single Responsibility Principle

Mỗi Skill chỉ nên làm MỘT việc tốt:

```yaml
# ✅ Tốt - Skill cụ thể
name: pdf-form-filler
description: Fill out forms in PDF documents automatically. Use when users need to complete PDF forms or templates.

name: excel-pivot-creator
description: Create pivot tables from Excel data. Use when analyzing data or creating reports in Excel.

# ❌ Sai - Skill quá rộng
name: document-processor
description: Process all types of documents with various operations.
```

### 1.2 Clear Entry Points

Skill nên có cách sử dụng rõ ràng ngay từ đầu:

```markdown
## Quick Start

Trước khi viết code, hãy luôn hỏi: "Bạn có muốn tôi viết code cho việc này không?"

# ✅ Tốt - Đi thẳng vào vấn đề
1. Ask the user: "Do you want me to write code for [task]?"
2. Wait for confirmation
3. Implement based on requirements

# ❌ Sai - Làm mất thời gian
Let me analyze your request and prepare a comprehensive plan...
```

## 2. Content Organization

### 2.1 Progressive Disclosure Pattern

Tách nội dung theo mức độ chi tiết:

```
skill-name/
├── SKILL.md              # Quick start + basic instructions
├── resources/
│   ├── examples.md       # Extended examples
│   └── reference.md      # Complete documentation
└── scripts/
    └── helpers/          # Utility scripts
```

**SKILL.md structure:**
- Quick start (2-3 steps)
- Basic instructions
- Link to detailed resources

**resources/reference.md:**
- Complete API documentation
- Advanced configuration
- Edge cases and troubleshooting

### 2.2 Information Hierarchy

Sắp xếp nội dung theo mức độ quan trọng:

1. **Must know** - Để bắt đầu ngay
2. **Should know** - Để sử dụng hiệu quả
3. **Nice to know** - Để sử dụng nâng cao

```markdown
# ✅ Tốt - Theo thứ tự ưu tiên
## Quick Start (Must know)
## Instructions (Must know)
## Examples (Should know)
## Advanced Usage (Nice to know)
## Reference (Nice to know)
```

## 3. Writing Effective Instructions

### 3.1 Action-Oriented Language

Sử dụng active voice với clear verbs:

```markdown
# ✅ Tốt - Direct actions
1. Read the file to understand its structure
2. Identify the main components
3. Create a test file with examples
4. Run the test to verify functionality

# ❌ Sai - Passive language
1. The file should be read in order to understand its structure
2. The main components need to be identified
3. A test file should be created with examples
```

### 3.2 Specific and Concrete

Tránh abstract statements:

```markdown
# ✅ Tốt - Specific actions
1. Create a new file called `user.ts` in the `src/models/` directory
2. Add the following interface: `interface User { id: number; name: string; }`
3. Export the interface at the bottom of the file

# ❌ Sai - Generic instructions
1. Set up the necessary models
2. Define the appropriate interfaces
3. Make sure everything is properly exported
```

## 4. Code Examples Standards

### 4.1 Complete and Runnable

Mọi code example phải:

- **Complete**: Đầy đủ để chạy
- **Contextual**: Có đủ context để hiểu
- **Formatted**: Đúng syntax highlighting
- **Tested**: Thực tế hoạt động

```typescript
// ✅ Tốt - Complete example
// src/services/userService.ts
import { User } from '../models/User';
import { Database } from '../utils/database';

export class UserService {
  private db: Database;

  constructor(db: Database) {
    this.db = db;
  }

  async createUser(userData: Omit<User, 'id'>): Promise<User> {
    const user = await this.db.insert('users', userData);
    return user;
  }
}

// Usage example:
const userService = new UserService(database);
const user = await userService.createUser({
  name: 'John Doe',
  email: 'john@example.com'
});
```

### 4.2 Error Handling Examples

Luôn show cả success và error cases:

```typescript
// ✅ T好 - Includes error handling
async function fetchUser(id: number): Promise<User> {
  try {
    const response = await api.get(`/users/${id}`);
    if (!response.data) {
      throw new Error(`User with id ${id} not found`);
    }
    return response.data;
  } catch (error) {
    if (error instanceof AxiosError) {
      throw new Error(`API error: ${error.message}`);
    }
    throw error;
  }
}
```

## 5. Common Patterns

### 5.1 Template Pattern

Sử dụng templates cho các tasks lặp lại:

```markdown
## Usage Template

1. **Understand requirements**
   - Ask clarifying questions about [specific aspects]
   - Identify key constraints and dependencies

2. **Set up structure**
   - Create necessary directories
   - Initialize configuration files

3. **Implement core functionality**
   - [Step-by-step implementation]

4. **Add tests**
   - Write unit tests for main functions
   - Add integration tests if needed

5. **Document usage**
   - Create README with examples
   - Document API endpoints if applicable
```

### 5.2 Decision Tree Pattern

Để hướng dẫn Claude đưa ra quyết định:

```markdown
## Decision Guide

### Is this a new project or existing?
- **New**: Create fresh structure with [template]
- **Existing**: Analyze current structure and [adapt]

### Does it need authentication?
- **Yes**: Implement [auth pattern]
- **No**: Skip auth setup

### Database required?
- **SQL**: Use [SQL pattern]
- **NoSQL**: Use [NoSQL pattern]
- **None**: Use in-memory storage
```

## 6. Security Best Practices

### 6.1 Never Hardcode Secrets

```yaml
# ❌ Sai - Hardcoded secrets
---
name: api-integration
description: Connect to external API with API key abc123xyz
---

# ✅ Tốt - Environment variables
---
name: api-integration
description: Connect to external API. Requires API_KEY environment variable.
---

## Setup

1. Set required environment variable:
```bash
export API_KEY="your-api-key-here"
```
```

### 6.2 Input Validation Template

```markdown
## Input Validation

Always validate inputs before processing:

```python
def validate_input(data: dict) -> bool:
    required_fields = ['email', 'name']
    for field in required_fields:
        if not data.get(field):
            raise ValueError(f"Missing required field: {field}")

    if '@' not in data['email']:
        raise ValueError("Invalid email format")

    return True
```

## 7. Testing Strategy

### 7.1 Include Test Commands

Luôn provide commands để test:

```markdown
## Testing

### Run all tests
```bash
npm test
```

### Run with coverage
```bash
npm run test:coverage
```

### Run specific test file
```bash
npm test -- userService.test.ts
```
```

### 7.2 Provide Test Data

```markdown
## Test Data

### Example Input
```json
{
  "name": "Test User",
  "email": "test@example.com",
  "age": 30
}
```

### Expected Output
```json
{
  "id": 1,
  "name": "Test User",
  "email": "test@example.com",
  "age": 30,
  "createdAt": "2024-01-01T00:00:00Z"
}
```

## 8. Documentation Patterns

### 8.1 FAQ Section

```markdown
## Frequently Asked Questions

### Q: How do I handle large files?
A: Use streaming instead of loading entire file into memory.

### Q: What if the API is down?
A: Implement retry logic with exponential backoff.

### Q: Can I use this with TypeScript?
A: Yes, types are included in the DefinitelyTyped package.
```

### 8.2 Troubleshooting Guide

```markdown
## Common Issues

### Issue: Permission Denied
**Symptoms**: Error when accessing files
**Solution**: Check file permissions with `ls -la`
**Prevention**: Set proper permissions during setup

### Issue: Module Not Found
**Symptoms**: Import errors
**Solution**: Install with `npm install module-name`
**Prevention**: List all dependencies in README
```

## 9. Performance Optimization

### 9.1 Mention Performance Considerations

```markdown
## Performance Tips

- Use `Array.map()` instead of `for` loops for transformations
- Implement caching for expensive operations
- Consider lazy loading for large datasets
- Use connection pooling for database operations
```

### 9.2 Provide Benchmarks

```markdown
## Performance Benchmarks

Based on tests with 10,000 records:
- Processing time: ~2.5 seconds
- Memory usage: ~50MB
- Concurrent requests: 100/second

To optimize further:
1. Implement streaming for large datasets
2. Add Redis caching layer
3. Use database indexes
```

## 10. Maintenance Guidelines

### 10.1 Versioning

```markdown
## Version History

### v2.0.0 (2024-01-15)
- Added support for TypeScript
- Improved error handling
- Breaking: Changed API signature

### v1.1.0 (2023-12-01)
- Added new features
- Bug fixes

### v1.0.0 (2023-11-01)
- Initial release
```

### 10.2 Update Instructions

```markdown
## Updating from v1 to v2

1. Backup your current configuration
2. Install new version: `npm update skill-name@latest`
3. Run migration script: `npm run migrate`
4. Update your code to use new API (see examples)
5. Test thoroughly
```

## 11. Community Guidelines

### 11.1 Contribution Template

```markdown
## Contributing

To contribute to this skill:

1. Fork the repository
2. Create feature branch: `git checkout -b feature-name`
3. Make your changes
4. Add tests for new functionality
5. Ensure all tests pass: `npm test`
6. Submit pull request

### Code Style
- Follow existing patterns
- Use TypeScript for new code
- Add JSDoc comments
- Include examples in documentation
```

### 11.2 Issue Reporting

```markdown
## Reporting Issues

When reporting an issue, please include:

1. **Environment**: OS, Node.js version, skill version
2. **Steps to reproduce**: Clear, numbered steps
3. **Expected behavior**: What should happen
4. **Actual behavior**: What actually happened
5. **Error messages**: Full stack traces if available
6. **Code example**: Minimal reproduction case
```