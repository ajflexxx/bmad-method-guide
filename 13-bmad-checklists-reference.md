# Deep Dive: Checklists in BMad - Intelligent Validation Frameworks

## Overview

Checklists in BMad are sophisticated validation frameworks that act as quality gates and interactive guides throughout the development lifecycle. Unlike simple to-do lists, BMad checklists contain embedded AI instructions, conditional logic, and comprehensive validation criteria that adapt to project context.

### Core Principles

- **Embedded Intelligence**: Each checklist contains `[[LLM: instructions]]` blocks that guide AI agents through complex validation processes
- **Self-Executing**: Checklists are directly processed by agents without requiring intermediate tasks
- **Conditional Adaptation**: Sections dynamically adapt based on project type (GREENFIELD/BROWNFIELD/FRONTEND)
- **Evidence-Based Validation**: Require concrete citations and examples, not just checkbox completion
- **Interactive Processing**: Support both comprehensive batch mode and section-by-section interactive review

### What Makes Checklists Unique

Unlike templates or tasks, checklists:
- Provide validation criteria rather than generation instructions
- Can block workflow progression (blocking vs advisory)
- Contain multiple validation modes and execution patterns
- Include deep analysis instructions for AI agents
- Adapt their content based on project characteristics

### Checklist Structure at a Glance

- **Header Comment**: Powered by BMAD™ Core marker
- **Title**: Clear identification of checklist purpose
- **Purpose Statement**: Brief description of validation scope
- **LLM Initialization Instructions**: Complex AI guidance block
- **Validation Sections**: Organized categories of checks
- **Section-Specific LLM Instructions**: Contextual guidance
- **Validation Items**: Checkbox items with criteria
- **Conditional Sections**: Project-type specific validations
- **Final Confirmation**: Summary and decision guidance

## Checklist Anatomy

### Header Comment

**Purpose**: Identifies the file as part of the BMad framework

**Requirement**: Recommended

**Format**:
```markdown
<!-- Powered by BMAD™ Core -->
```

**Examples from actual checklists**:

From `story-dod-checklist.md:1`:
```markdown
<!-- Powered by BMAD™ Core -->
```

**Best Practices**:
- Include for consistency with other BMad components
- Helps identify BMad-compatible checklists in mixed repositories

### Title and Purpose

**Purpose**: Clearly identifies the checklist's validation scope and target audience

**Requirement**: Required

**Format**:
```markdown
# [Role/Domain] [Validation Type] Checklist

[Brief description of what this checklist validates and when to use it]
```

**Examples from actual checklists**:

From `story-dod-checklist.md:3-5`:
```markdown
# Story Definition of Done (DoD) Checklist

## Instructions for Developer Agent

Before marking a story as 'Review', please go through each item in this checklist.
```

From `architect-checklist.md:3-5`:
```markdown
# Architect Solution Validation Checklist

This checklist serves as a comprehensive framework for the Architect to validate the technical design and architecture before development execution.
```

**Best Practices**:
- Use role-specific titles for clarity
- Include brief usage instructions
- Specify when the checklist should be executed

### LLM Initialization Instructions

**Purpose**: Provides comprehensive guidance to AI agents on how to process the checklist

**Requirement**: Required

**Format**:
```markdown
[[LLM: INITIALIZATION INSTRUCTIONS - [CONTEXT]

[Detailed instructions for AI agent processing]

REQUIRED CONTEXT:
- [List of required documents or artifacts]

EXECUTION APPROACH:
1. [Step-by-step processing instructions]

VALIDATION APPROACH:
- [How to evaluate each item]
]]
```

**Examples from actual checklists**:

From `story-dod-checklist.md:9-23`:
```markdown
[[LLM: INITIALIZATION INSTRUCTIONS - STORY DOD VALIDATION

This checklist is for DEVELOPER AGENTS to self-validate their work before marking a story complete.

IMPORTANT: This is a self-assessment. Be honest about what's actually done vs what should be done.

EXECUTION APPROACH:
1. Go through each section systematically
2. Mark items as [x] Done, [ ] Not Done, or [N/A] Not Applicable
3. Add brief comments explaining any [ ] or [N/A] items

The goal is quality delivery, not just checking boxes.]]
```

From `po-master-checklist.md:7-60`:
```markdown
[[LLM: INITIALIZATION INSTRUCTIONS - PO MASTER CHECKLIST

PROJECT TYPE DETECTION:
First, determine the project type by checking:
1. Is this a GREENFIELD project (new from scratch)?
2. Is this a BROWNFIELD project (enhancing existing system)?

DOCUMENT REQUIREMENTS:
Based on project type, ensure you have access to:
- prd.md - The Product Requirements Document
- architecture.md - The system architecture

EXECUTION MODE:
Ask the user if they want to work through the checklist:
- Section by section (interactive mode)
- All at once (comprehensive mode)]]
```

**Best Practices**:
- Provide clear context about the checklist's purpose
- List all required documents upfront
- Define execution modes for flexibility
- Include project type detection logic if applicable
- Guide critical thinking, not just checkbox completion

### Validation Sections

**Purpose**: Organize related validation items into logical categories

**Requirement**: Required

**Format**:
```markdown
## [Number]. [SECTION NAME]

[[LLM: [Section-specific guidance for deeper analysis]]]

### [Number].[Subsection] [Subsection Name]

- [ ] Validation item with clear criteria
- [ ] Another validation item
  - [ ] Sub-item if needed for detail
```

**Examples from actual checklists**:

From `story-dod-checklist.md:27-32`:
```markdown
## Checklist Items

1. **Requirements Met:**

   [[LLM: Be specific - list each requirement and whether it's complete]]
   - [ ] All functional requirements specified in the story are implemented.
   - [ ] All acceptance criteria defined in the story are met.
```

From `architect-checklist.md:47-58`:
```markdown
## 1. REQUIREMENTS ALIGNMENT

[[LLM: Before evaluating this section, take a moment to fully understand the product's purpose]]

### 1.1 Functional Requirements Coverage

- [ ] Architecture supports all functional requirements in the PRD
- [ ] Technical approaches for all epics and stories are addressed
- [ ] Edge cases and performance scenarios are considered
```

**Best Practices**:
- Use numbered sections for systematic processing
- Include LLM guidance for complex sections
- Group related items logically
- Use sub-items sparingly for essential detail
- Bold important categories for visual hierarchy

### Validation Items

**Purpose**: Individual criteria that must be evaluated

**Requirement**: Required

**Format**:
```markdown
- [ ] Clear, objective validation criterion
- [x] Completed item example
- [N/A] Not applicable item with explanation
```

**Examples from actual checklists**:

From `story-dod-checklist.md:36-42`:
```markdown
- [ ] All new/modified code strictly adheres to `Operational Guidelines`.
- [ ] All new/modified code aligns with `Project Structure`.
- [ ] Adherence to `Tech Stack` for technologies/versions used.
- [ ] Basic security best practices applied for new/modified code.
- [ ] No new linter errors or warnings introduced.
```

**Best Practices**:
- Write objectively assessable criteria
- Reference specific documents or standards
- Keep items atomic (one criterion per checkbox)
- Use consistent marking conventions: `[x]`, `[ ]`, `[N/A]`

### Conditional Sections

**Purpose**: Adapt checklist content based on project characteristics

**Requirement**: Optional (use when checklists serve multiple contexts)

**Format**:
```markdown
[[CONDITION_TYPE ONLY]]
- [ ] Items specific to this condition
- [ ] Skip these for other project types

Common conditions:
- [[GREENFIELD ONLY]]
- [[BROWNFIELD ONLY]]
- [[FRONTEND ONLY]]
- [[BACKEND ONLY]]
- [[UI/UX ONLY]]
```

**Examples from actual checklists**:

From `po-master-checklist.md:66-73`:
```markdown
### 1.1 Project Scaffolding [[GREENFIELD ONLY]]

- [ ] Epic 1 includes explicit steps for project creation
- [ ] If using a starter template, steps for cloning/setup are included

### 1.2 Existing System Integration [[BROWNFIELD ONLY]]

- [ ] Existing project analysis has been completed
- [ ] Integration points with current system are identified
```

From `architect-checklist.md:124-131`:
```markdown
### 3.2 Frontend Architecture [[FRONTEND ONLY]]

[[LLM: Skip this entire section if this is a backend-only project]]

- [ ] UI framework and libraries are specifically selected
- [ ] State management approach is defined
```

**Best Practices**:
- Place condition markers clearly at section headers
- Include skip instructions in LLM blocks
- Use consistent condition naming across checklists
- Document all conditions in the initialization block

### Interactive Elements

**Purpose**: Enable different execution modes for flexibility

**Requirement**: Recommended for complex checklists

**Format**:
```markdown
EXECUTION MODE:
Ask the user if they want to work through the checklist:
- Section by section (interactive mode - time consuming)
- All at once (YOLO mode - recommended, with summary at end)
```

**Examples from actual checklists**:

From `change-checklist.md:30-31`:
```markdown
APPROACH:
This is an interactive process with the user. Work through each section together, discussing implications and options.
```

**Best Practices**:
- Offer both interactive and batch modes
- Set expectations about time requirements
- Default to comprehensive mode for efficiency
- Maintain user control over pacing

### Final Confirmation

**Purpose**: Summarize findings and guide decision-making

**Requirement**: Required

**Format**:
```markdown
## Final Confirmation

[[LLM: FINAL SUMMARY
After completing the checklist:
1. Summarize what was validated
2. List any items marked as [ ] Not Done
3. Identify risks or follow-up work
4. Make pass/fail recommendation
]]

- [ ] I, the [Role] Agent, confirm that all applicable items have been addressed.
```

**Examples from actual checklists**:

From `story-dod-checklist.md:84-96`:
```markdown
## Final Confirmation

[[LLM: FINAL DOD SUMMARY
After completing the checklist:
1. Summarize what was accomplished in this story
2. List any items marked as [ ] Not Done with explanations
3. Identify any technical debt or follow-up work needed
4. Confirm whether the story is truly ready for review
]]

- [ ] I, the Developer Agent, confirm that all applicable items above have been addressed.
```

**Best Practices**:
- Include clear summary instructions
- Require explanation of any failures
- Guide pass/fail decision criteria
- Include formal confirmation checkbox

## Checklist Patterns

### Self-Validation Pattern

**Purpose**: Agents validate their own work before marking complete

**When to Use**: After task completion, before status changes

**Structure**:
```markdown
# [Role] Definition of Done Checklist

[[LLM: Self-assessment instructions emphasizing honesty]]

1. **Requirements Met:**
   - [ ] Functional requirements implemented
   - [ ] Acceptance criteria satisfied

2. **Quality Standards:**
   - [ ] Coding standards followed
   - [ ] Tests written and passing
```

**Example Implementation**:

From `story-dod-checklist.md`:
- Used by: Developer agents
- Trigger: Before marking story as "Ready for Review"
- Focus: Implementation completeness and quality

**Best Practices**:
- Emphasize honest self-assessment
- Include both functional and quality criteria
- Require evidence for completion claims

### Architecture Validation Pattern

**Purpose**: Comprehensive technical design validation

**When to Use**: After architecture documentation, before development

**Structure**:
```markdown
# Architecture Validation Checklist

[[LLM: Load architecture documents and perform deep analysis]]

## 1. REQUIREMENTS ALIGNMENT
[[LLM: Verify every requirement has technical solution]]

## 2. TECHNICAL DECISIONS
[[LLM: Validate technology choices and alternatives]]
```

**Example Implementation**:

From `architect-checklist.md`:
- 380+ validation items across multiple categories
- Deep analysis instructions for each section
- Evidence-based validation requirements

**Best Practices**:
- Organize into logical technical categories
- Require specific document citations
- Include risk assessment for decisions

### Change Management Pattern

**Purpose**: Navigate significant project changes interactively

**When to Use**: When pivots, blockers, or major issues arise

**Structure**:
```markdown
# Change Navigation Checklist

[[LLM: Interactive problem-solving with user]]

## 1. Understand the Trigger
[[LLM: Ask probing questions about root cause]]

## 2. Impact Assessment
[[LLM: Analyze ripple effects across project]]
```

**Example Implementation**:

From `change-checklist.md`:
- Interactive user collaboration
- Structured problem analysis
- Impact assessment across artifacts
- Path forward planning

**Best Practices**:
- Make it conversational and interactive
- Focus on understanding before solving
- Document all decisions and rationale

### Quality Gate Pattern

**Purpose**: Control workflow progression based on validation

**When to Use**: At critical workflow transitions

**Structure**:
```markdown
# [Role] Master Validation Checklist

[[LLM: This is a BLOCKING checklist - failures prevent progress]]

## Critical Requirements
- [ ] Must-have criteria that block if failed

## Advisory Recommendations
- [ ] Should-have criteria that warn but don't block
```

**Example Implementation**:

From `po-master-checklist.md`:
- Blocking checklist for project validation
- 200+ items across all project aspects
- Can prevent workflow continuation

**Best Practices**:
- Clearly distinguish blocking vs advisory
- Provide clear remediation paths
- Document why items are critical

### Project Type Adaptation Pattern

**Purpose**: Single checklist serving multiple project contexts

**When to Use**: When similar validations apply to different scenarios

**Structure**:
```markdown
[[LLM: PROJECT TYPE DETECTION
1. Check for GREENFIELD indicators
2. Check for BROWNFIELD indicators
3. Determine UI/BACKEND focus]]

### Common Validations
- [ ] Apply to all project types

### Greenfield Specific [[GREENFIELD ONLY]]
- [ ] New project validations

### Brownfield Specific [[BROWNFIELD ONLY]]
- [ ] Existing system validations
```

**Example Implementation**:

From `po-master-checklist.md`:
- Auto-detects project type
- Skips irrelevant sections
- Documents what was skipped

**Best Practices**:
- Clear detection logic upfront
- Consistent condition markers
- Report skipped sections in summary

## Integration Architecture

### Agent Dependencies

**Purpose**: Agents declare which checklists they can execute

**Requirement**: Required for checklist access

**Format**:
```yaml
dependencies:
  checklists:
    - story-dod-checklist.md
    - other-checklist.md
```

**Examples from actual agents**:

From `dev.md:74-76`:
```yaml
dependencies:
  checklists:
    - story-dod-checklist.md
```

**Best Practices**:
- Only include checklists the agent actually uses
- Match checklist to agent role and responsibilities
- Keep dependencies minimal for context efficiency

**Agent-Checklist Mapping in BMad**:
- **dev.md**: story-dod-checklist.md (self-validation)
- **architect.md**: architect-checklist.md (design validation)
- **po.md**: po-master-checklist.md, change-checklist.md (project validation)
- **pm.md**: pm-checklist.md, change-checklist.md (requirements validation)
- **sm.md**: story-draft-checklist.md (story quality)

### Direct Processing

**Purpose**: Agents process checklists directly without intermediate tasks

**Requirement**: Understanding this is critical for expansion packs

**How It Works**:
1. Agent declares checklist in dependencies
2. Agent commands reference checklist execution
3. Agent loads checklist file directly from dependencies
4. Processes according to embedded LLM instructions
5. No separate execute-checklist.md task exists

**Examples from actual agents**:

From `dev.md:68`:
```yaml
completion: "run the task execute-checklist for the checklist story-dod-checklist"
```

**Important Notes**:
- The "execute-checklist" reference is a command pattern, not a task file
- Agents interpret this as "load and process the specified checklist"
- This is why checklists must be self-contained with full instructions

### Workflow Integration

**Purpose**: Checklists act as quality gates in workflows

**Requirement**: Optional but recommended for quality control

**Format in Workflows**:
```yaml
- step: validation
  agent: po
  action: Run po-master-checklist
  blocking: true
  on_failure: Return to previous step for fixes
```

**Integration Points**:
- After document creation (validate completeness)
- Before development (validate readiness)
- After implementation (validate quality)
- At phase transitions (validate progression criteria)

**Best Practices**:
- Place checkpoints at natural boundaries
- Clear blocking vs advisory distinctions
- Document remediation paths

### Document Validation

**Purpose**: Checklists validate against project artifacts

**Requirement**: Required for meaningful validation

**Document Loading Pattern**:
```markdown
[[LLM: REQUIRED ARTIFACTS
Before proceeding, ensure you have access to:
1. architecture.md - Technical design document
2. prd.md - Product requirements
3. [Other required documents]

If any are missing, ask user for location.]]
```

**Examples from actual checklists**:

From `architect-checklist.md:7-18`:
```markdown
[[LLM: INITIALIZATION INSTRUCTIONS - REQUIRED ARTIFACTS

Before proceeding with this checklist, ensure you have access to:
1. architecture.md - The primary architecture document
2. prd.md - Product Requirements Document
3. frontend-architecture.md - If this is a UI project

IMPORTANT: If any required documents are missing, immediately ask the user]]
```

**Best Practices**:
- List all required documents upfront
- Include fallback instructions if documents are missing
- Reference specific sections when validating
- Maintain traceability between checks and sources

## Creating Expansion Pack Checklists

### Structure Template

**Purpose**: Complete template for creating domain-specific checklists

**Requirement**: Follow this structure for BMad compatibility

```markdown
<!-- Powered by BMAD™ Core -->

# [Domain] [Validation Type] Checklist

[Brief description of what this checklist validates and when it should be used]

[[LLM: INITIALIZATION INSTRUCTIONS - [DOMAIN] VALIDATION

Purpose: [What this checklist accomplishes]

REQUIRED CONTEXT:
Before proceeding, ensure you have access to:
1. [Required document 1]
2. [Required document 2]

PROJECT/DOMAIN TYPE DETECTION:
[If applicable, how to determine which variant applies]

VALIDATION APPROACH:
- [How to think about validation]
- [Level of scrutiny required]
- [Evidence requirements]

EXECUTION MODE:
Ask if user wants:
- Section by section (interactive, detailed)
- All at once (comprehensive report at end)

SKIP INSTRUCTIONS:
[Any conditional sections and when to skip them]
]]

## 1. [FIRST MAJOR CATEGORY]

[[LLM: [Specific guidance for validating this category]]]

### 1.1 [Subcategory Name]

- [ ] Specific validation criterion
- [ ] Another validation point
- [ ] Third validation item

### 1.2 [Another Subcategory] [[CONDITIONAL TAG ONLY]]

[[LLM: Skip this section if [condition]]]

- [ ] Conditional validation item
- [ ] Another conditional item

## 2. [SECOND MAJOR CATEGORY]

[[LLM: [Category-specific validation guidance]]]

### 2.1 [Subcategory Name]

- [ ] Validation items for this category
- [ ] Continue with specific criteria

## Final Confirmation

[[LLM: FINAL VALIDATION SUMMARY

After completing the checklist:
1. Summarize what was validated
2. List any failed items with impact
3. Identify risks or concerns
4. Provide clear pass/fail recommendation
5. Suggest remediation if needed
]]

- [ ] I confirm that this [domain] validation has been completed
- [ ] All critical items have been addressed
- [ ] Recommendations have been documented
```

### LLM Instruction Guidelines

**Purpose**: Write effective AI guidance blocks

**Requirement**: Critical for checklist intelligence

**Key Principles**:
1. **Be Specific**: Provide concrete validation approaches
2. **Guide Thinking**: Don't just list what to check, explain how
3. **Require Evidence**: Ask for citations and examples
4. **Encourage Depth**: Push beyond surface-level checking

**Instruction Types**:

**Initialization Instructions**:
```markdown
[[LLM: INITIALIZATION INSTRUCTIONS
- Set overall context and purpose
- List all prerequisites
- Define execution modes
- Establish validation standards]]
```

**Section Instructions**:
```markdown
[[LLM: For this section, consider:
- Specific aspects to examine
- Common issues to watch for
- How items relate to each other
- Evidence required for validation]]
```

**Decision Instructions**:
```markdown
[[LLM: DECISION CRITERIA
- How to weigh different factors
- What constitutes pass/fail
- When to escalate concerns
- How to document findings]]
```

### Validation Item Best Practices

**Purpose**: Create effective validation criteria

**Requirement**: Each item must be objectively assessable

**Guidelines**:

1. **Specificity Over Generality**:
   - ❌ "Code quality is good"
   - ✅ "All functions have JSDoc comments with parameter descriptions"

2. **Reference Standards**:
   - ❌ "Follows best practices"
   - ✅ "Adheres to project's OPERATIONAL-GUIDELINES.md coding standards"

3. **Measurable Criteria**:
   - ❌ "Performance is acceptable"
   - ✅ "API responses complete within 200ms for standard queries"

4. **Actionable Failures**:
   - ❌ "Security is implemented"
   - ✅ "Authentication middleware applied to all protected routes"

### Conditional Logic Implementation

**Purpose**: Create adaptive checklists for multiple contexts

**Requirement**: Use when single checklist serves varied scenarios

**Implementation Pattern**:

1. **Detection Logic**:
```markdown
[[LLM: PROJECT TYPE DETECTION
Check for indicators:
- File patterns (package.json, requirements.txt)
- Directory structures (/frontend, /backend)
- Document references (mentions of UI, API only)
Make determination and note in report]]
```

2. **Section Markers**:
```markdown
### Database Setup [[BACKEND ONLY]]
### Component Architecture [[FRONTEND ONLY]]
### Mobile Considerations [[MOBILE ONLY]]
```

3. **Inline Conditions**:
```markdown
- [ ] API authentication implemented
- [ ] [[FRONTEND ONLY]] Token refresh logic in place
- [ ] [[BACKEND ONLY]] Session management configured
```

**Best Practices**:
- Define all conditions in initialization
- Use consistent marker format
- Document what was skipped in final report
- Keep conditions simple and clear

### Testing Your Checklist

**Purpose**: Ensure checklist works as intended

**Requirement**: Test before deploying to expansion pack

**Testing Checklist**:

1. **Structural Validation**:
   - [ ] Follows BMad checklist format
   - [ ] All sections have LLM instructions
   - [ ] Validation items are specific and measurable
   - [ ] Conditional sections properly marked

2. **Content Validation**:
   - [ ] Instructions are clear and actionable
   - [ ] No ambiguous validation criteria
   - [ ] Evidence requirements specified
   - [ ] Pass/fail criteria defined

3. **Integration Testing**:
   - [ ] Can be loaded by agents with dependency
   - [ ] Document requirements are clear
   - [ ] Execution modes work correctly
   - [ ] Final summary generates properly

4. **Domain Coverage**:
   - [ ] Addresses all critical domain aspects
   - [ ] Appropriate for target audience
   - [ ] Balances thoroughness with efficiency
   - [ ] Provides value beyond simple checking

## Key Insights for Expansion Pack Developers

1. **Checklists Are Programs**: They contain executable logic through LLM instructions, not just static lists

2. **Self-Contained Design**: Each checklist must include all processing instructions since there's no intermediate task

3. **Validation vs Generation**: Unlike templates that create content, checklists validate existing artifacts

4. **Intelligence Through Instructions**: The power comes from sophisticated LLM guidance blocks that teach critical thinking

5. **Conditional Flexibility**: Single checklists can serve multiple contexts through conditional sections

6. **Direct Processing**: Agents load and execute checklists directly from dependencies without intermediary tasks

7. **Evidence-Based**: Always require concrete citations and examples, not just affirmations

8. **Interactive Options**: Support both batch and interactive modes for different use cases

9. **Quality Gates**: Can be blocking (prevent progress) or advisory (warn but allow continuation)

10. **Domain Adaptation**: Structure is consistent but content adapts to specific domain requirements