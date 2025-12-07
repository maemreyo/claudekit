# Summary: Command Renamed to /how

## Changes Made

### 1. File Renamed ✅
- **Old**: `explore-codebase-v2.md`
- **New**: `how.md`

### 2. Command Updated Throughout File ✅

Updated all references in `how.md`:
- Command name: `/explore-codebase-v2` → `/how`
- Aliases: `/explore-v2` → `/how-it-works`
- All examples updated to use `/how`

### 3. CLAUDE.md Updated ✅

Added to Enhanced Commands section:
```markdown
| `/how [topic]` | Multi-phase codebase exploration with organized docs |
```

### 4. RESEARCH_GUIDE.md Updated ✅

Updated all 8 references:
- Command name
- Examples
- Comparison tables
- Decision tree

### 5. Documentation Created ✅

Created `HOW_README.md` with:
- Quick summary
- Usage examples
- Output structure
- Comparison with other commands
- When to use

## Files Modified

1. ✅ `.claude/commands/how.md` (renamed + updated)
2. ✅ `.claude/CLAUDE.md` (added to command list)
3. ✅ `.claude/commands/RESEARCH_GUIDE.md` (8 references updated)
4. ✅ `.claude/commands/HOW_README.md` (created)

## Files NOT Modified (Still Reference Old Command)

These files still reference `/explore-codebase` (the original command):
- `plan-migration.md` (2 references)
- `auto-plan.md` (2 references)
- `.claude/skills/methodology/deep-research/SKILL.md` (1 reference)

**Note**: These are intentional - they reference the OLD `/explore-codebase` command, not the new `/how` command. Both commands can coexist.

## Command Comparison

### /explore-codebase (Original)
- Single-phase workflow
- One output file
- Still available for quick explorations

### /how (New)
- Multi-phase workflow (4 phases)
- Organized documentation per phase
- Prevents context loss
- Uses SubAgents
- Better for complex analysis

## Usage

```bash
# New command
/how "authentication"
/how "payment flow" --source=old-app

# Old command (still works)
/explore-codebase "authentication"
```

## Why "how"?

- ✅ **Meaningful**: Answers "How does this work?"
- ✅ **Concise**: Short and memorable
- ✅ **Intuitive**: Clear purpose
- ✅ **Natural**: Reads like a question

Much better than `explore-v2` or `explore-codebase-v2`!

## Next Steps

Users can now:
1. Use `/how` for comprehensive multi-phase exploration
2. Use `/explore-codebase` for quick single-phase exploration
3. Choose based on complexity and documentation needs
