---
description: Launch structured brainstorming session using the brainstormer agent. Use when you need facilitated ideation, creative problem-solving, or innovation workshops.
argument-hint: [topic] [technique?] [duration?] [participants?]
allowed-tools: Read, Write, Edit, Bash, Grep, Glob
---

# Brainstorming Command

## Instructions
Launch the brainstormer agent to facilitate a structured ideation session. The agent will handle technique selection, session orchestration, and output generation.

### Parse Arguments
- **Topic** ($1): The main subject or challenge to brainstorm about
- **Technique** ($2): Optional - mindmap, scamper, reverse, six-hats, or auto
- **Duration** ($3): Optional - session duration in minutes (default: 15)
- **Participants** ($4): Optional - number of participants or "team" for group sessions

### Execution Steps

1. **Validate Input**
   - Topic is required - if missing, show usage and ask for topic
   - Validate technique against available options
   - Set reasonable defaults for duration and participants

2. **Prepare Agent Context**
   - Compose session brief with all parameters
   - Include any relevant context from current project
   - Set up expected output format based on technique

3. **Launch Brainstormer Agent**
   ```
   Use the brainstormer agent to facilitate a brainstorming session with:
   - Topic: "$1"
   - Preferred technique: $2 or auto-select based on context
   - Session duration: $3 minutes
   - Participants: $4
   ```

4. **Support Agent Execution**
   - The agent will orchestrate the brainstorm-facilitator skill
   - Ensure all workflow phases are completed
   - Collect and present agent outputs

## Usage Examples

- `/brainstorm "new product features"` - Agent auto-selects technique for 15 mins
- `/brainstorm "improve team workflow" mindmap 30` - Agent facilitates mind mapping for 30 mins
- `/brainstorm "reduce customer churn" scamper 45 team` - Agent runs SCAMPER for team of any size
- `/brainstorm "solve login issues" reverse 20` - Agent facilitates reverse brainstorming
- `/brainstorm "innovate our pricing" six-hats 60` - Agent guides Six Thinking Hats exercise

## Available Techniques

The agent will select and facilitate from these techniques:

- **mindmap**: Visual idea organization with hierarchical structure
- **scamper**: 7-perspective creative thinking (Substitute, Combine, Adapt, Modify, Put to other uses, Eliminate, Reverse)
- **reverse**: Identify problems instead of solutions to find root causes
- **six-hats**: Perspective exploration (White facts, Red emotions, Black cautions, Yellow benefits, Green creativity, Blue process)
- **auto**: Agent selects optimal technique based on topic and context

## What the Agent Provides

The brainstormer agent will:
1. **Assess your needs** and select the best technique
2. **Orchestrate the session** using the brainstorm-facilitator skill
3. **Maintain psychological safety** for open idea sharing
4. **Document all ideas** without judgment
5. **Organize and prioritize** outputs using evaluation matrices
6. **Create action plans** with clear next steps

## Error Handling

If arguments are invalid:
- Show usage examples with all options
- Suggest appropriate technique for your topic
- Guide you to rephrase with clear topic

If agent is unavailable:
- Provide direct brainstorm-facilitator skill invocation
- Offer simplified facilitation
- Suggest manual brainstorming approaches