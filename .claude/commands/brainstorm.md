---
description: Structured brainstorming using multiple techniques with AI facilitation. Use when user needs to generate ideas, solve problems creatively, or organize thoughts for innovation.
argument-hint: [topic] [technique?] [duration?]
allowed-tools: Read, Write, Edit, Bash, Grep, Glob
---

# Brainstorming Command

## Instructions
Use the brainstorm-facilitator skill to conduct structured brainstorming session based on the provided topic.

### Parse Arguments
- **Topic** ($1): The main subject to brainstorm about
- **Technique** ($2): Optional - mindmap, scamper, reverse, or auto-select
- **Duration** ($3): Optional - session duration in minutes

### Execution Steps

1. **Validate Input**
   - If no topic provided, request user to specify a topic
   - Show available techniques if invalid technique specified

2. **Auto-Select Technique** (if not specified)
   - Use mind-mapping for: visual, organize, structure topics
   - Use SCAMPER for: innovate, create, improve topics
   - Use reverse brainstorming for: problem, issue, challenge topics
   - Default to mind-mapping for general topics

3. **Execute Brainstorming**
   ```
   Use brainstorm-facilitator skill with:
   - Topic: "$1"
   - Technique: $2 or auto-selected
   - Duration: $3 minutes (default: 10)
   ```

4. **Generate Output**
   - Create structured brainstorming results
   - Save to file if requested
   - Offer follow-up actions

## Usage Examples

- `/brainstorm "new product features"` - Auto-select technique for 10 mins
- `/brainstorm "improve team workflow" mindmap 15` - Mind mapping for 15 mins
- `/brainstorm "reduce customer churn" scamper 20` - SCAMPER for 20 mins
- `/brainstorm "solve login issues" reverse` - Reverse brainstorming

## Available Techniques

- **mindmap**: Visual idea organization with hierarchical structure
- **scamper**: 7-perspective creative thinking (Substitute, Combine, Adapt, Modify, Put to another use, Eliminate, Reverse)
- **reverse**: Identify problems instead of solutions to find root causes
- **auto**: AI selects best technique based on topic context

## Error Handling

If arguments are invalid:
- Show usage examples
- Suggest valid techniques: mindmap, scamper, reverse
- Provide help message

If skill fails to load:
- Fallback to basic Q&A brainstorming
- Continue with reduced functionality