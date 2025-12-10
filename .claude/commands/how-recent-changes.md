# /how-recent-changes - Understand Recent Git Changes

## Purpose

Phân tích và giải thích các thay đổi hiện tại trong thư mục làm việc, bao gồm cả staged changes (sẵn sàng commit) và unstaged changes (đang trong quá trình làm việc). Command này giúp bạn hiểu trạng thái công việc hiện tại, xác nhận ý định của bạn và phát hiện các vấn đề tiềm ẩn trước khi commit.

## Aliases

```bash
/recent-changes
/status-explained
/diff-explained
```

## Usage

```bash
# Basic usage (analyzes current git status and saves by default)
/how-recent-changes

# Compare with plan file
/how-recent-changes --plan=plans/feature-x.md

# With specific analysis depth
/how-recent-changes --deep

# Focus on specific files
/how-recent-changes --files=src/components,src/utils

# Skip saving to file
/how-recent-changes --no-save
```

---

## Workflow

### Phase 1: Gather Git Status & Diffs 🔍

**Agent**: [`git-manager`](.claude/agents/git-manager.md)

**Goal**: Retrieve the raw data about what has changed.

**Steps**:

1.  **Check Git Status**
    ```bash
    git status
    ```

2.  **Get Unstaged Changes**
    ```bash
    git diff
    ```

3.  **Get Staged Changes**
    ```bash
    git diff --staged
    ```

4.  **Identify Modified Files**
    - List of files with staged changes
    - List of files with unstaged changes
    - List of untracked files

---

### Phase 2: Analyze Context & Intent 🧠

**Agent**: [`researcher`](.claude/agents/researcher.md)

**Skills**: 
- [`pattern-analysis`](.claude/skills/methodology/pattern-analysis/SKILL.md) - To identify code patterns
- [`sequential-thinking`](.claude/skills/methodology/sequential-thinking/SKILL.md) - For logical analysis

**Goal**: Interpret the changes to understand the high-level goal and implementation details.

**Analysis Tasks**:

1.  **Plan Comparison (if --plan flag provided)**:
    - Read the plan file specified
    - Compare actual changes with planned tasks
    - Identify missing implementations or extra work
    - Report completion percentage

2.  **Infer Intent**:
    - Look at the combination of changes.
    - Is this a refactor? A feature addition? A bug fix?
    - consistency check: Do the changes match the inferred intent?
    - Use [`pattern-analysis`](.claude/skills/methodology/pattern-analysis/SKILL.md) to identify recurring patterns

3.  **Analyze Staged vs. Unstaged**:
    - **Staged**: likely a coherent set of changes ready for a commit. Analyze them as a unit
    - **Unstaged**: likely work in progress or experimental changes.
    - Check for overlap: Are there files with both staged and unstaged changes? This can be confusing and risky
    - Apply [`sequential-thinking`](.claude/skills/methodology/sequential-thinking/SKILL.md) to understand workflow

4.  **Detailed File Analysis**:
    - For each modified file, determine *what* changed logically (not just line-by-line).
    - "Added validation to `UserForm`" instead of "Added lines 40-45".
    - Use [`pattern-analysis`](.claude/skills/methodology/pattern-analysis/SKILL.md) to understand architectural patterns

5.  **Risk Assessment**:
    - Are there breaking changes?
    - Are there `console.log` or debug code left?
    - Are there missing tests for new logic?
    - Use [`sequential-thinking`](.claude/skills/methodology/sequential-thinking/SKILL.md) to evaluate potential issues

---

### Phase 3: Synthesize Report 📝

**Agent**: [`docs-manager`](.claude/agents/docs-manager.md)

**Goal**: Present the analysis in a structured, actionable format.
*Only runs when --save flag is enabled or when explicitly requested*

**Output Template**:

```markdown
# 🕵️ Phân Tích Thay Đổi Gần Đây

## 🎯 Tóm Tắt
[Tóm tắt cấp cao về mục tiêu của các thay đổi này, ví dụ: "Refactor luồng Authentication và sửa lỗi chính tả trong Dashboard."]

---

## 📋 So Sánh Với Plan (nếu có)
*(Khi sử dụng --plan flag)*

### **Tiến Độ Hoàn Thành**
- **Tasks đã hoàn thành**: [số lượng]/[tổng số]
- **Completion rate**: [X]%
- **Tasks còn lại**: [danh sách]

### **Phân Tích Deviation**
- **Thừa**: [các thay đổi không có trong plan]
- **Thiếu**: [các tasks trong plan chưa implement]
- **Khác biệt**: [phân tích sự khác biệt]

---

## 🟢 Thay Đổi Đã Staged (Sẵn Sàng Commit)
*(Các thay đổi đã được thêm vào index)*

### **[Tên File]**
- **Thay đổi**: [Mô tả ngắn gọn về thay đổi]
- **Tác động**: [Tại sao thay đổi này quan trọng]

*(Lặp lại cho các file khác)*

---

## 🟡 Thay Đổi Chưa Staged (Đang Phát Triển)
*(Các thay đổi trong thư mục làm việc chưa được staged)*

### **[Tên File]**
- **Thay đổi**: [Mô tả ngắn gọn]
- **Trạng thái**: [ví dụ: Đang debug, Chưa hoàn thành, Đàn chỉnh]

---

## 🔍 Phân Tích Sâu
*(Nếu liên quan, khám phá các thay đổi phức tạp cụ thể)*

- **Thay đổi Logic trong `[Component]`**:
  - Giải thích sự thay đổi logic
  - Hiển thị code trước/sau nếu hữu ích

- **Phân Tích Pattern**:
  - Các pattern được áp dụng: [từ pattern-analysis]
  - Tính nhất quán với codebase: [đánh giá]

---

## ⚠️ Quan Sát & Gợi Ý
- **[Quan sát]**: ví dụ: "Bạn có cả staged và unstaged changes trong `api.ts`. Điều này có thể dẫn đến commit không hoàn chỉnh."
- **[Gợi ý]**: ví dụ: "Cân nhắc chạy tests cho validator mới."
- **[Dọn dẹp]**: "Tìm thấy `console.log` trong `utils.ts`."

---

## 📊 Thống Kê
- **Total files changed**: [số lượng]
- **Lines added**: [số lượng]
- **Lines removed**: [số lượng]
- **Complexity score**: [low/medium/high]

---

## 🔄 Hành Động Đề Xuất
1. [Hành động cụ thể 1]
2. [Hành động cụ thể 2]
3. [Hành động cụ thể 3]
```

---

## Integration with Subagents & Skills

### Agent Collaboration

1. **git-manager**:
   - Thực hiện các git commands
   - Cung cấp raw diff data
   - Đánh giá clean history practices

2. **researcher**:
   - Phân tích patterns trong code
   - Tìm các best practices liên quan
   - Đánh giá architectural consistency

3. **docs-manager**:
   - Tạo báo cáo có cấu trúc
   - Đảm bảo clarity và actionability
   - Lưu artifacts nếu cần

### Skill Application

1. **pattern-analysis**:
   - Nhận diện structural patterns
   - So sánh với existing codebase
   - Đề xuất improvements

2. **sequential-thinking**:
   - Phân tích logical flow
   - Đánh giá risk factors
   - Document reasoning chain

---

## Output Integration

This command provides an immediate report in the chat and saves by default to `.claude/artifacts/recent-changes-[timestamp].md`.

### Flags

| Flag | Description | Example |
|------|-------------|---------|
| `--plan=[path]` | Compare changes with plan file | `/how-recent-changes --plan=plans/feature-x.md` |
| `--deep` | Deep analysis with pattern recognition | `/how-recent-changes --deep` |
| `--files=[paths]` | Focus on specific files/directories | `/how-recent-changes --files=src/components,src/utils` |
| `--no-save` | Skip saving to file | `/how-recent-changes --no-save` |
| `--format=[json|markdown]` | Output format | `/how-recent-changes --format=json` |

---

## Best Practices

1. **Run before commits**: Always check before committing to catch issues early
2. **Use with --deep**: For complex changes or before PRs
3. **Save important analyses**: For documentation or team sharing
4. **Address flagged issues**: Pay attention to risk assessments and cleanup suggestions

---

## Related Commands

- [`/status`](.claude/commands/status.md) - Quick project status
- [`/commit`](.claude/commands/commit.md) - Create commits with analysis
- [`/review`](.claude/commands/review.md) - Code review with subagents
