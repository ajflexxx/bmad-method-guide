# Continuation Plan for 11-bmad-tasks-reference.md

## Current Status

### Completed Sections ✅

1. Overview
2. Task Structure at a Glance
3. Task Anatomy:
   - HTML Comment
   - Task Name (H1 Heading)
   - Brief Description
   - Purpose Section
   - Inputs Section
   - Prerequisites Section
   - Instructions/Process Section (includes Conditional Logic and Validation Points)
   - Outputs Section
4. Frequent Optional Sections:
   - When to Use This Task
   - Dependencies/Integration Points
   - Blocking Conditions
   - Completion Checklist
   - Success Criteria

### Remaining Sections to Document 📝

#### From Task Structure at a Glance

1. **Key Principles** section
2. **Important Notes** section
3. **LLM Directives** section (needs dedicated section - partially covered in Process)

#### Additional Major Sections Needed

4. **Task Categories and Types**

   - Common Tasks - Universal Processors
   - BMad-Core Tasks - Domain Procedures
   - Expansion Pack Tasks

5. **Creating Tasks for Expansion Packs**

   - Step-by-step guide
   - Templates and patterns
   - Integration with agents
   - Testing and validation

6. **Task Resolution and Loading**

   - IDE-FILE-RESOLUTION pattern
   - Resolution order for expansion packs
   - Dependency declaration
   - Loading triggers

7. **Integration with Agents**

   - How agents reference tasks
   - Command-to-task mapping patterns
   - Task execution from agent commands

8. **Common Implementation Errors**

   - Skipping mandatory interaction
   - Pre-loading tasks
   - Ignoring task authority
   - Inconsistent structure

9. **Summary**
   - Key takeaways for prompt engineers
   - Task system's role in BMad

## Instructions for Next Session

### INSTRUCTIONS FOR AI AGENT

**Initial Context to Provide:**
"I need to continue working on the BMad tasks reference documentation. We completed sections through 'Success Criteria' in the previous session. The continuation plan is in `/workspace/bmad-documentation/11-tasks-continuation-plan.md`. Please read that file first, then continue with the 'Key Principles' section."

**Working Method:**

1. Read the continuation plan first
2. Review the existing document to understand the established pattern
3. Work section by section with user approval
4. Always verify examples against source files
5. Use the TodoWrite tool to track progress

### Starting Context

Begin with: "Continue documenting the BMad tasks reference. We completed sections through Success Criteria. Now working on remaining sections starting with Key Principles."

### Reference Files

- Main document: `/workspace/bmad-documentation/11-bmad-tasks-reference.md`
- Source tasks: `/workspace/bmad-method/bmad-core/tasks/`
- Common tasks: `/workspace/bmad-method/common/tasks/`
- Agents reference (for comparison): `/workspace/bmad-documentation/10-bmad-agents-reference.md`

### Quality Guidelines

1. **Accuracy**: Use actual examples from source files
2. **Verbatim quotes**: When showing code blocks from tasks, use exact text
3. **Source verification**: Check each example against actual task files
4. **Pattern consistency**: Follow the established pattern from completed sections

### Section Template for Remaining Components

````markdown
### [Section Name]

**Requirement**: [Optional/Required], [brief purpose]

**Format**:

```markdown
## [Section Name]

[Format example]
```
````

**Examples from actual tasks**:

From `[task-name].md`:

```markdown
[Actual verbatim example]
```

**Purpose/Usage**:

- [Key point about this section]
- [Another key point]

**Best Practices**:

- [Guidance for prompt engineers]
- [Another guideline]

```

### Key Principles Section Notes
Look for tasks with "## Key Principles" like:
- review-story.md
- qa-gate.md
- Apply pattern similar to other optional sections

### Important Notes Section Notes
Common in many tasks, often contains:
- Critical warnings
- Implementation caveats
- Version requirements
- Non-obvious constraints

### LLM Directives Section Notes
Focus on:
- The `[[LLM: ]]` syntax
- Common patterns (conditional execution, hidden logic, behavioral overrides)
- Examples from shard-doc.md, create-doc.md
- How these differ from visible instructions

### Task Categories Section Notes
Structure similar to agents:
- Location of each category
- Purpose and characteristics
- Example tasks
- When to use each type

### For Creating Tasks Section
Provide practical guide:
1. Determine task type
2. Create structure (use template)
3. Add behavioral controls
4. Implement interaction patterns
5. Define outputs
6. Test and validate

## Work Patterns Observed

### What Works Well
- Section-by-section review with user
- Checking actual source files for examples
- Showing multiple naming variations
- Explaining patterns vs prescribing single approach

### Common Issues to Avoid
- Don't paraphrase when showing "actual" examples
- Don't invent examples - always check source
- Don't mix process steps with outcome criteria
- Verify counts and statistics against actual files

## Estimated Work Remaining
- Approximately 8-10 sections to complete
- Each section: 15-30 minutes
- Total: 3-4 hours of focused work

## Critical Reminders
1. The document is for **prompt engineers creating expansion packs**, not end users
2. Focus on **architecture and patterns**, not usage instructions
3. Always verify against source files - no invented examples
4. Maintain consistency with the completed sections' style
5. When user provides feedback, be critical and check if changes are accurate before agreeing

## Quality Checklist for Each Section
- [ ] Examples are verbatim from source files
- [ ] File paths are correct
- [ ] Counts/statistics are verified
- [ ] Format matches established pattern
- [ ] Best practices are actionable
- [ ] No user-facing content included
- [ ] Cross-references to other sections are accurate
```
