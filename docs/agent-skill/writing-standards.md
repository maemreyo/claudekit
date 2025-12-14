# Tiêu chuẩn và Quy tắc viết Claude Skills

## 1. Quy tắc chung cho Frontmatter

### 1.1 Quy tắc đặt tên (name)

```yaml
# ✅ Tốt
name: pdf-processor
name: git-commit-helper
name: api-designer

# ❌ Sai
name: PDF_Processor        # Chữ hoa và underscore
name: git commit helper    # Spaces
name: api.designer         # Dấu chấm
name: 123-skill            # Bắt đầu bằng số
```

**Quy tắc:**
- Chỉ chứa lowercase letters (a-z), numbers (0-9), và hyphens (-)
- Tối đa 64 ký tự
- Không bắt đầu hoặc kết thúc bằng hyphen
- Không có hyphen liên tiếp
- Phải khớp chính xác với tên thư mục

### 1.2 Quy tắc viết description

```yaml
# ✅ Tốt - Cụ thể và có trigger words
description: Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when user mentions PDFs, forms, or document extraction.

description: Analyze Excel spreadsheets, create pivot tables, and generate charts. Use when working with Excel files, spreadsheets, or analyzing tabular data in .xlsx format.

# ❌ Sai - Quá chung chung
description: Helps with documents
description: For data analysis
description: A useful tool
```

**Công thức viết description hiệu quả:**

```
[What it does] + [When to use it] + [Key triggers]
```

**Các thành phần cần có:**
- **What it does**: Chức năng chính (ví dụ: "Extract text and tables")
- **When to use it**: "Use when..." + ngữ cảnh
- **Key triggers**: Các từ khóa mà người dùng thường nói
- **File types**: Các định dạng file hỗ trợ (.pdf, .xlsx, .json)
- **Operations**: Các hành động cụ thể (extract, analyze, generate)

## 2. Tiêu chuẩn viết nội dung Skill

### 2.1 Cấu trúc Markdown

Sử dụng cấu trúc nhất quán:

```markdown
# Skill Name

Brief one-sentence overview.

## Quick Start

Provide immediate value - show how to use in 2-3 steps.

## Instructions

Detailed step-by-step guidance for Claude.

## Examples

Concrete, copy-pasteable examples.

## Best Practices

Key conventions and common pitfalls.

## Requirements

Dependencies and prerequisites if any.
```

### 2.2 Quy tắc viết Instructions

**DO:**
- Sử dụng numbered lists cho từng bước
- Mỗi bước chỉ có một hành động chính
- Bắt đầu mỗi bước với verb mạnh
- Cụ thể về expected outcomes

```markdown
## Instructions

1. **Analyze the requirements**: Read the user's request and identify key parameters.
2. **Create the structure**: Set up the directory structure with required files.
3. **Implement core logic**: Write the main functionality following best practices.
4. **Add error handling**: Implement try-catch blocks and validation.
5. **Test the implementation**: Verify functionality with test cases.
```

**DON'T:**
- Viết các bước quá phức tạp
- Sử dụng passive voice
- Để mơ hồ về next steps
- Trộn lẫn nhiều hành động trong một bước

### 2.3 Quy tắc viết Examples

Tất cả examples phải:
- **Realistic**: Thực tế và applicable
- **Complete**: Đầy đủ để copy-paste
- **Contextual**: Có context rõ ràng
- **Formatted**: Đúng syntax highlighting

```markdown
## Examples

### Basic Usage
```typescript
// Create a simple REST API endpoint
import { FastifyInstance } from 'fastify';

async function routes(fastify: FastifyInstance) {
  fastify.get('/users', async (request, reply) => {
    return { users: [] };
  });
}
```

### Advanced Usage
```typescript
// With authentication and validation
import { FastifyInstance } from 'fastify';
import { createUserSchema } from './schemas';

async function routes(fastify: FastifyInstance) {
  fastify.post('/users', {
    schema: createUserSchema,
    preHandler: [fastify.authenticate]
  }, async (request, reply) => {
    const user = await userService.create(request.body);
    return reply.code(201).send(user);
  });
}
```

## 3. Coding Standards trong Skills

### 3.1 Code Examples

**Language-specific conventions:**

#### Python
```python
# ✅ Tốt - PEP 8 compliant, type hints
def process_data(data: List[Dict[str, Any]]) -> Dict[str, Any]:
    """Process input data and return results.

    Args:
        data: List of dictionaries containing input data

    Returns:
        Dictionary containing processed results
    """
    try:
        results = {}
        for item in data:
            key = item.get('key')
            value = item.get('value')
            results[key] = value
        return results
    except Exception as e:
        logger.error(f"Error processing data: {e}")
        raise
```

#### TypeScript
```typescript
// ✅ Tốt - Strict types, clear interfaces
interface UserData {
  id: number;
  name: string;
  email: string;
}

class UserService {
  async createUser(userData: Omit<UserData, 'id'>): Promise<UserData> {
    try {
      const user = await this.repository.create(userData);
      return user;
    } catch (error) {
      throw new Error(`Failed to create user: ${error.message}`);
    }
  }
}
```

### 3.2 Shell Commands

```bash
# ✅ Tốt - Clear, with comments
# Install dependencies
npm install --save-dev vitest @testing-library/react

# Create test directory structure
mkdir -p src/__tests__

# Run tests in watch mode
npm run test:watch

# ❌ Sai - No context, unclear
npm i
mkdir test
npm test
```

## 4. Documentation Standards

### 4.1 File References

Sử dụng relative paths với forward slashes:

```markdown
✅ Đúng:
- See [reference.md](resources/reference.md) for details
- Run [helper.py](scripts/helper.py) to process data
- Use [template.txt](templates/template.txt) as boilerplate

❌ Sai:
- See C:\project\resources\reference.md
- Use /Users/name/project/scripts/helper.py
- Reference ../docs/reference.md
```

### 4.2 Links và References

- Internal links: Sử dụng relative paths
- External links: Định dạng Markdown standard với title
- Code references: Sử dụng backticks cho inline code

```markdown
# Internal links
See [API Reference](resources/reference.md#api-endpoints)

# External links
Check [Vitest Documentation](https://vitest.dev/) for more details.

# Code references
Use `describe()` for test suites and `it()` for individual tests.
```

## 5. Security Standards

### 5.1 Input Validation

Luôn include input validation trong examples:

```python
# ✅ Tốt - With validation
def process_user_input(user_input: str) -> str:
    """Process user input safely."""
    # Validate input
    if not user_input or not isinstance(user_input, str):
        raise ValueError("Invalid input: must be non-empty string")

    # Sanitize input
    sanitized = user_input.strip().lower()
    return sanitized

# ❌ Sai - No validation
def process_user_input(user_input):
    return eval(user_input)  # Dangerous!
```

### 5.2 Tool Permissions

Sử dụng `allowed-tools` khi cần:

```yaml
# Read-only skill
---
name: code-analyzer
description: Analyze code without making changes. Use for code review and documentation.
allowed-tools: Read, Grep, Glob
---

# Skill with specific tools
---
name: file-processor
description: Process specific file types with read and write access.
allowed-tools: Read, Write, Bash
---
```

## 6. Error Handling Standards

### 6.1 Include Error Scenarios

Mỗi skill nên mention các error cases phổ biến:

```markdown
## Common Errors and Solutions

### Error: Permission denied
```bash
# Fix: Change file permissions
chmod +x scripts/deploy.sh
```

### Error: Module not found
```bash
# Fix: Install missing dependencies
npm install missing-module
```
```

### 6.2 Graceful Degradation

Xử lý khi dependencies không available:

```markdown
## Requirements

### Required
- Node.js >= 16
- npm >= 8

### Optional
- Docker (for containerized deployment)
- Redis (for caching, falls back to memory cache)
```

## 7. Testing Standards

### 7.1 Include Test Examples

Mỗi skill nên có examples về testing:

```markdown
## Testing

### Unit Tests
```typescript
// Example test
import { describe, it, expect } from 'vitest';
import { processData } from './processor';

describe('Data Processor', () => {
  it('should process valid data', () => {
    const input = { key: 'value' };
    const result = processData(input);
    expect(result).toBeDefined();
  });
});
```

### Integration Tests
```bash
# Run integration tests
npm run test:integration
```

## 8. Performance Guidelines

### 8.1 Mention Performance Considerations

```markdown
## Performance Tips

- Use `async/await` for async operations
- Implement caching for expensive operations
- Consider streaming for large files
```

### 8.2 Provide Optimization Examples

```typescript
// ✅ Good: Efficient implementation
async function processLargeFile(filePath: string) {
  const stream = fs.createReadStream(filePath);
  for await (const chunk of stream) {
    await processChunk(chunk);
  }
}

// ❌ Bad: Inefficient for large files
async function processLargeFile(filePath: string) {
  const content = fs.readFileSync(filePath);
  // Process entire file at once
}
```

## 9. Validation Checklist

Trước khi publish skill, kiểm tra:

- [ ] Frontmatter YAML hợp lệ
- [ ] Description < 1024 characters
- [ ] Name follows naming conventions
- [ ] All links work correctly
- [ ] Code examples are tested
- [ ] Dependencies are clearly listed
- [ ] Security considerations addressed
- [ ] Error cases are covered
- [ ] File paths use forward slashes
- [ ] No hardcoded secrets or API keys