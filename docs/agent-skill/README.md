# Claude Agents Skills Documentation

## Tổng quan về Claude Agents Skills

Claude Agents Skills là một tính năng mới được ra mắt vào tháng 10 năm 2025 bởi Anthropic, cho phép người dùng đóng gói kiến thức chuyên môn, kịch bản và tài nguyên thành các "thư viện kỹ năng" (skill folders) module hóa. Điều này giúp Claude xử lý các tác vụ cụ thể hiệu quả hơn, bao gồm xử lý hình ảnh, Excel, Word và PDF files tại local.

### Nguồn tài liệu chính

- **[Agent Skills - Claude Code Docs](https://code.claude.com/docs/en/skills)** - Tài liệu chính thức từ Anthropic
- **[Using Agent Skills with the API - Claude Docs](https://platform.claude.com/docs/en/build-with-claude/skills-guide)** - Hướng dẫn sử dụng Skills với API
- **[Anthropic's Skills Repository](https://github.com/anthropics/skills)** - Ví dụ mẫu từ Anthropic

### Các bài viết phân tích chuyên sâu

- **[Inside Claude Code Skills: Structure, prompts, invocation](https://mikhail.io/2025/10/claude-code-skills/)** - Phân tích chi tiết cấu trúc Skills
- **[SKILL.md, resources, and how Claude loads them](https://skywork.ai/blog/ai-agent/claude-skills-skill-md-resources-runtime-loading/)** - Cách Claude tải và xử lý Skills
- **[Claude Agent Skills: A First-Principles Deep Dive](https://medium.com/aimonks/claude-agent-skills-a-first-principles-deep-dive-into-prompt-based-meta-tools-022de66fc721)** - Phân tích kỹ thuật sâu
- **[Claude Skills vs System Prompts (2025)](https://skywork.ai/blog/ai-agent/claude-skills-vs-system-prompts-2025-comparison/)** - So sánh Skills với System Prompts truyền thống

## Tài liệu trong bộ này

1. **[skill-structure.md](./skill-structure.md)** - Cấu trúc chi tiết của một Skill
2. **[writing-standards.md](./writing-standards.md)** - Tiêu chuẩn và quy tắc viết Skill
3. **[best-practices.md](./best-practices.md)** - Best practices và các mẫu phổ biến
4. **[examples.md](./examples.md)** - Ví dụ thực tế và các case study
5. **[validation.md](./validation.md)** - Hướng dẫn validate và test Skill
6. **[troubleshooting.md](./troubleshooting.md)** - Các vấn đề thường gặp và cách giải quyết

## Bắt đầu nhanh

Để tạo một Skill mới:

```bash
# Tạo thư mục cho Skill
mkdir -p .claude/skills/my-skill

# Tạo file SKILL.md với cấu trúc chuẩn
touch .claude/skills/my-skill/SKILL.md
```

Xem [skill-structure.md](./skill-structure.md) để biết cấu trúc chi tiết.

## Các khái niệm chính

- **SKILL.md**: File chính chứa định nghĩa và hướng dẫn cho Skill
- **YAML Frontmatter**: Metadata để Claude nhận biết khi nào sử dụng Skill
- **Progressive Disclosure**: Kỹ thuật tách biệt nội dung cơ bản và nâng cao
- **Tool Permissions**: Kiểm soát quyền truy cập tools của Skill

## Hệ sinh thái Skills

Skills được tổ chức theo các cấp độ:

1. **Managed Skills** - Skills được quản lý bởi hệ thống
2. **Plugin Skills** - Skills từ các plugin (api-scaffolding, backend-development, etc.)
3. **Project Skills** - Skills dành cho project cụ thể
4. **Personal Skills** - Skills cá nhân trong `~/.claude/skills/`

Mỗi loại có ưu và nhược điểm riêng tùy theo mục đích sử dụng.