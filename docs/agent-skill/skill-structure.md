# Cấu trúc chi tiết của một Claude Skill

## Cấu trúc tổng quan

Một Claude Skill có cấu trúc chuẩn gồm 2 phần chính trong file `SKILL.md`:

```
skill-name/
├── SKILL.md              # Bắt buộc: File chính định nghĩa Skill
├── resources/            # Tùy chọn: Thư mục tài nguyên bổ sung
│   ├── reference.md      # Tài liệu tham khảo chi tiết
│   └── examples.md       # Ví dụ mở rộng
├── scripts/              # Tùy chọn: Kịch bản và tiện ích
│   └── helper.py         # Script hỗ trợ
└── templates/            # Tùy chọn: Mẫu và boilerplate
    └── template.txt      # File template
```

## Phân tích chi tiết file SKILL.md

### 1. YAML Frontmatter (Bắt buộc)

Phải nằm ở đầu file, giữa 3 dấu gạch ngang:

```yaml
---
name: ten-skill
description: Mô tả ngắn gọn về chức năng và khi nào sử dụng
allowed-tools: tool1,tool2  # Tùy chọn
---
```

#### Các trường bắt buộc:

- **`name`** (Bắt buộc)
  - Tối đa 64 ký tự
  - Chỉ chứa chữ thường, số và dấu gạch ngang
  - Phải khớp với tên thư mục
  - Ví dụ tốt: `pdf-processor`, `git-commit-helper`
  - Ví dụ sai: `PDF_Processor`, `Git Commits!`

- **`description`** (Bắt buộc)
  - Tối đa 1024 ký tự
  - Phải chứa CẢ chức năng VÀ khi nào sử dụng
  - Nên chứa các từ khóa trigger
  - Công thức: `[Chức năng] + [Khi dùng] + [Từ khóa trigger]`

#### Các trường tùy chọn:

- **`allowed-tools`**
  - Giới hạn quyền truy cập tools
  - Dành cho Skills read-only hoặc nhạy cảm về bảo mật
  - Ví dụ: `Read, Grep, Glob`

### 2. Markdown Content (Nội dung chính)

Nội dung phía sau frontmatter dùng để hướng dẫn Claude thực hiện Skill.

#### Cấu trúc đề xuất:

```markdown
# Skill Name

Tổng quan ngắn gọn về chức năng.

## Quick Start

Cung cấp ví dụ đơn giản để bắt đầu ngay.

## Instructions

Hướng dẫn chi tiết từng bước cho Claude:
1. Bước đầu tiên với hành động rõ ràng
2. Bước thứ hai với kết quả mong đợi
3. Xử lý các trường hợp ngoại lệ

## Examples

Hiển thị ví dụ thực tế với code hoặc commands.

## Best Practices

- Các quy ước quan trọng cần tuân thủ
- Các lỗi phổ biến cần tránh
- Khi nào dùng và khi không dùng

## Requirements

Liệt kê các dependencies hoặc prerequisites:

```bash
pip install package-name
npm install package-name
```

## Advanced Usage

Để biết thêm chi tiết, xem [reference.md](resources/reference.md).
```

## Quy tắc đặt tên và đường dẫn

### Tên Skill và thư mục:
- Sử dụng lowercase, numbers, và hyphens
- Không dùng spaces, underscores, hoặc ký tự đặc biệt
- Tên thư mục phải khớp với `name` trong frontmatter

### Đường dẫn file:
- Luôn dùng forward slashes (`/`) ngay cả trên Windows
- Sử dụng relative paths từ thư mục Skill
- Ví dụ: `scripts/helper.py`, `resources/examples.md`

## Ví dụ thực tế

### Skill đơn giản (single-file):

```yaml
---
name: commit-helper
description: Help write git commit messages following conventional commits. Use when preparing to commit code or when user mentions "commit", "git commit", or needs help with commit message format.
---

# Git Commit Helper

## Quick Start

I'll help you write a conventional commit message by analyzing your changes.

## Instructions

1. Check git status and staged changes
2. Analyze what changed
3. Suggest commit message in format: `type(scope): description`
4. Include breaking changes and footer if needed

## Examples

- `feat(auth): add OAuth2 login support`
- `fix(api): handle null response in user endpoint`
- `docs(readme): update installation instructions`
```

### Skill với tool permissions:

```yaml
---
name: code-reviewer
description: Analyze code for quality, security issues, and best practices. Use when asked to review, audit, or analyze code for potential improvements.
allowed-tools: Read, Grep, Glob
---

# Code Reviewer

## Instructions

1. Read the provided code files
2. Check for common issues:
   - Security vulnerabilities
   - Performance bottlenecks
   - Code style violations
   - Missing error handling
3. Provide specific, actionable feedback
4. Suggest improvements with examples
```

### Skill multi-file với progressive disclosure:

```yaml
---
name: api-designer
description: Design REST APIs following industry best practices. Use when creating new APIs, designing endpoints, or planning API architecture.
---

# API Designer

Quick examples: See [examples.md](resources/examples.md)
Detailed reference: See [reference.md](resources/reference.md)

## Instructions

1. Gather requirements for the API
2. Design resource endpoints (see examples.md)
3. Define request/response schemas
4. Document with OpenAPI specification
5. Review against best practices (see reference.md)
```

## Progressive Disclosure Pattern

Khi có nhiều nội dung, hãy tách thành nhiều file:

1. **SKILL.md** - Nội dung cơ bản, quick start
2. **resources/reference.md** - Tài liệu chi tiết, API docs
3. **resources/examples.md** - Ví dụ mở rộng, case studies
4. **scripts/** - Các utility scripts
5. **templates/** - File templates và boilerplate

### Liên kết giữa các file:

```markdown
Để biết thêm chi tiết, xem [reference.md](resources/reference.md).

Xem ví dụ thực tế trong [examples.md](resources/examples.md).

Chạy script helper:
```bash
python scripts/helper.py input.txt
```
```

## Validation checklist

Trước khi hoàn thành Skill, kiểm tra:

- [ ] Tên thư mục khớp với `name` trong frontmatter
- [ ] Frontmatter có opening và closing `---`
- [ ] YAML không có tabs, thụt lề đúng
- [ ] Description cụ thể và < 1024 ký tự
- [ ] Instructions rõ ràng, theo từng bước
- [ ] Ví dụ cụ thể và thực tế
- [ ] Dependencies được liệt kê
- [ ] Đường dẫn dùng forward slashes
- [ ] Không có lỗi syntax

## Common mistakes cần tránh

1. **Description quá chung chung**:
   - Sai: "Helps with documents"
   - Đúng: "Extract text and tables from PDF files, fill forms"

2. **Quên allowed-tools cho Skills read-only**:
   - Nên thêm cho code reviewer: `allowed-tools: Read, Grep, Glob`

3. **Dùng absolute paths**:
   - Sai: `/Users/name/project/scripts/helper.py`
   - Đúng: `scripts/helper.py`

4. **Không cung cấp examples**:
   - Luôn bao gồm code examples thực tế

5. **Instructions quá phức tạp**:
   - Chia thành các bước nhỏ, rõ ràng