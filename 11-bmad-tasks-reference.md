# Deep Dive: Tasks in BMad - The Executable Procedures

## Overview

Tasks in BMad are executable markdown procedures that agents use to perform specific actions. They are not reference documentation or simple templates - they are complete, self-contained instruction sets that guide AI behavior through complex operations. Tasks bridge the gap between agent commands and actual implementation, providing the "how" to the agent's "what."

### Core Principles

**Tasks Are Instructions, Not Documentation**

- Tasks contain imperative directives that OVERRIDE agent defaults
- They enforce specific behaviors and interaction patterns
- They are loaded on-demand, never pre-cached
- They can halt execution to require user interaction

**Task Authority Hierarchy**

1. During execution, task instructions override conflicting base agent constraints and behavioral defaults
2. Tasks that specify interactive steps (e.g., "wait for user response", "ask the user") enforce mandatory user interaction
3. Task execution rules supersede all optimization attempts
4. Interactive requirements defined in task instructions are mandatory and cannot be bypassed

### What Makes Tasks Unique

Unlike other BMad components:

- **Agents** define personas and capabilities → **Tasks** provide the implementation
- **Templates** structure documents → **Tasks** process those templates
- **Checklists** define criteria → **Tasks** execute validation against criteria
- **Data** provides reference information → **Tasks** consume and apply that data

Tasks can:

- Force user interaction that cannot be bypassed
- Override agent efficiency optimizations
- Define exact step-by-step execution flows
- Embed hidden LLM processing instructions via `[[LLM: ...]]` blocks

### Task Structure at a Glance

**Task File Structure** (`task-name.md`)

- **HTML Comment**: `<!-- Powered by BMAD™ Core -->` (always)
- **Task Name** (H1 heading) (always)
- **Brief Description** (optional - provides quick context)
- **Purpose Section** (H2) (almost always - explains what task accomplishes)
- **Common Core Sections**
  - **Inputs** (optional - when task needs parameters)
  - **Prerequisites** (optional - when specific conditions must exist)
  - **Instructions/Process** (always in some form - main execution flow)
  - **Outputs** (optional - when task generates artifacts)
- **Frequent Optional Sections**
  - **When to Use This Task** (optional - helps with task selection)
  - **Dependencies/Integration Points** (optional - references to config, templates, or other tasks)
  - **Blocking Conditions** (optional - defines when to halt)
  - **Completion Checklist** (optional - verification criteria)
  - **Success Criteria** (optional - defines done state)
  - **Key Principles** (optional - guiding rules and philosophy)
  - **Important Notes** (optional - critical reminders)
- **LLM Directives** (optional - hidden processing instructions)
  - `[[LLM: specific instructions]]` blocks

**Naming Variations:**

- Execution flow appears as: **Process**, **Instructions**, **Processing Flow**, or **Task Instructions**
- Output sections may be: **Outputs**, **Output Requirements**, or **Deliverables**
- Validation sections vary: **Validation**, **Review Process**, **Assessment**

## Task Anatomy

This section provides detailed documentation for each component of a task file, with examples and templates for prompt engineers creating expansion packs.

### HTML Comment

**Requirement**: Always present at the beginning of the file

**Exact Format**:

```html
<!-- Powered by BMAD™ Core -->
```

**Purpose**:

- Identifies the file as part of the BMAD framework
- Must be the first line of the file
- Maintains consistency across all BMAD components

**Implementation Notes**:

- Same format used across agents, tasks, templates, and checklists

### Task Name (H1 Heading)

**Requirement**: Always present, immediately after HTML comment

**Format**:

```markdown
# Task Name
```

**Examples from actual tasks**:

- `# Create Document from Template (YAML Driven)` - Descriptive title with qualifier
- `# qa-gate` - Simple identifier matching filename
- `# Advanced Elicitation Task` - Function-based name
- `# nfr-assess` - Abbreviated technical name

**Best Practices**:

- Can match filename (without .md) or be more descriptive
- Use clear, action-oriented names when possible
- Include key qualifiers in parentheses if it adds clarity
- Hyphenated names are common for compound concepts

**Relationship to Filename**:

- No strict requirement to match filename exactly
- Often the filename is kebab-case while H1 is more readable
- Example: `create-doc.md` has `# Create Document from Template (YAML Driven)`

### Brief Description

**Purpose**: Provides the LLM with immediate context about what the task does, appearing right after the task name to establish the task's core function before detailed instructions.

**Requirement**: Optional

- When to include: When the task name alone doesn't fully convey the task's purpose or scope to the LLM
- When NOT to include: When the task name is self-explanatory and the Purpose section immediately follows

**Format**:

```markdown
# Task Name

One-line or short paragraph describing the task's core function.
```

**Examples from actual tasks**:

From `qa-gate.md`:

```markdown
# qa-gate

Create or update a quality gate decision file for a story based on review findings.
```

From `nfr-assess.md`:

```markdown
# nfr-assess

Quick NFR validation focused on the core four: security, performance, reliability, maintainability.
```

**Best Practices**:

- Keep to one or two sentences maximum - the LLM needs quick context, not detailed explanation
- Focus on the "what" (task function) not the "how" (implementation details)
- Use present tense, action-oriented language that helps the LLM understand its role
- Mention key constraints or scope boundaries that shape how the LLM should approach the task
- Include the target agent when the task is agent-specific (e.g., "This task is for the Dev agent")

**Other important information**:

The brief description is processed by the LLM as the first piece of context after seeing the task name. It helps the LLM quickly orient itself to what it will be doing before reading the detailed Purpose and Instructions sections. This is particularly valuable in complex task libraries where the LLM needs to understand task boundaries and relationships.

### Purpose Section

**Purpose**: The Purpose section provides the LLM with essential context about what a task accomplishes, when it should be executed, and what value it delivers. This section acts as the task's mission statement, enabling the LLM to understand not just the "how" but the "why" behind task execution, ensuring appropriate task selection and proper execution context.

**Requirement**: Recommended

- When to include: In most tasks, especially those that could be confused with similar tasks, require specific context for proper execution, or integrate with other BMad components
- When NOT to include: Only when the task is extremely simple and self-explanatory from its filename and instructions alone (rare in practice - only 2 out of 21 core tasks omit it)

**Format**:

```markdown
## Purpose

[Clear statement of what the task accomplishes, when to use it, and what value it provides]
```

The content can be formatted as either:

- A single paragraph for focused, straightforward tasks
- Bullet points for tasks with multiple objectives or capabilities

**Examples from actual tasks**:

From `advanced-elicitation.md`:

```markdown
## Purpose

- Provide optional reflective and brainstorming actions to enhance content quality
- Enable deeper exploration of ideas through structured elicitation techniques
- Support iterative refinement through multiple analytical perspectives
- Usable during template-driven document creation or any chat conversation
```

From `qa-gate.md`:

```markdown
## Purpose

Generate a standalone quality gate file that provides a clear pass/fail decision with actionable feedback. This gate serves as an advisory checkpoint for teams to understand quality status.
```

From `create-next-story.md`:

```markdown
## Purpose

To identify the next logical story based on project progress and epic definitions, and then to prepare a comprehensive, self-contained, and actionable story file using the `Story Template`. This task ensures the story is enriched with all necessary technical context, requirements, and acceptance criteria, making it ready for efficient implementation by a Developer Agent with minimal need for additional research or finding its own context.
```

From `shard-doc.md`:

```markdown
## Purpose

- Split a large document into multiple smaller documents based on level 2 sections
- Create a folder structure to organize the sharded documents
- Maintain all content integrity including code blocks, diagrams, and markdown formatting
```

**Best Practices**:

- Start with action verbs that clearly indicate what the task does (Generate, Create, Validate, Transform, Split, Provide)
- Be specific about the task's scope and boundaries to prevent mission creep during execution
- Explicitly state when this task should be triggered or used
- Describe the tangible output or value the task provides
- Distinguish this task from similar tasks if confusion is likely
- Mention key integrations with templates, workflows, or other tasks when relevant
- Keep focused on the "why" and "what" rather than implementation details (save those for Task Instructions)
- Use bullet points when the task has multiple distinct purposes or capabilities
- Use paragraph format when the task has a single, cohesive purpose

**Other important information**:

The Purpose section is processed by the LLM at task initialization, helping it understand the broader context before diving into specific instructions. This section is particularly critical for tasks that can be invoked in multiple contexts, generate deliverables for other agents, or form part of larger workflows. When creating expansion pack tasks, ensure the Purpose section clearly differentiates domain-specific tasks from core BMad tasks.

**Key Distinction from Brief Description**:

- **Brief Description**: One-line quick orientation immediately after H1, tells the LLM _what_ the task does in simplest terms
- **Purpose Section**: Full H2 section with detailed context about _why_ the task exists, _when_ to use it, and _what value_ it provides
- **Example comparison** from `qa-gate.md`:
  - Brief: "Create or update a quality gate decision file for a story based on review findings."
  - Purpose: "Generate a standalone quality gate file that provides a clear pass/fail decision with actionable feedback. This gate serves as an advisory checkpoint for teams to understand quality status."
- The Brief Description is like a task's tagline; the Purpose Section is its mission statement
- Tasks often have both, with the Brief Description providing instant context and the Purpose Section offering comprehensive understanding

### Inputs Section

**Purpose**: The Inputs section defines parameters that the LLM receives when executing a task, configuring how the task operates and what data it processes. These inputs act as the task's API, allowing the same task to be reused across different contexts with different parameters. Inputs transform generic task templates into specific, actionable operations by providing context-specific values like story IDs, file paths, or configuration references.

**Requirement**: Optional

- When to include: When the task needs external parameters to function (story IDs, file paths, configuration values, user-provided data)
- When NOT to include: When the task is self-contained and doesn't require external parameters (knowledge base interactions, static processes, template listings)

**Format**:

````markdown
## Inputs

```yaml
required:
  - parameter_name: description and format
  - another_param: '{variable}' format explanation

optional:
  - optional_param: description of optional input
  - another_optional: default behavior if not provided
```
````

**Examples from actual tasks**:

From `nfr-assess.md`:

````markdown
## Inputs

```yaml
required:
  - story_id: '{epic}.{story}' # e.g., "1.3"
  - story_path: `bmad-core/core-config.yaml` for the `devStoryLocation`

optional:
  - architecture_refs: `bmad-core/core-config.yaml` for the `architecture.architectureFile`
  - technical_preferences: `bmad-core/core-config.yaml` for the `technicalPreferences`
  - acceptance_criteria: From story file
```
````

From `test-design.md`:

````markdown
## Inputs

```yaml
required:
  - story_id: "{epic}.{story}" # e.g., "1.3"
  - story_path: "{devStoryLocation}/{epic}.{story}.*.md" # Path from core-config.yaml
  - story_title: "{title}" # If missing, derive from story file H1
  - story_slug: "{slug}" # If missing, derive from title (lowercase, hyphenated)
```
````

From `apply-qa-fixes.md`:

````markdown
## Inputs

```yaml
required:
  - story_id: "{epic}.{story}" # e.g., "2.2"
  - qa_root: from `bmad-core/core-config.yaml` key `qa.qaLocation` (e.g., `docs/project/qa`)
  - story_root: from `bmad-core/core-config.yaml` key `devStoryLocation` (e.g., `docs/project/stories`)

optional:
  - story_title: "{title}" # derive from story H1 if missing
  - story_slug: "{slug}" # derive from title (lowercase, hyphenated) if missing
```
````

From `generate-ai-frontend-prompt.md`:

```markdown
## Inputs

- Completed UI/UX Specification (`front-end-spec.md`)
- Completed Frontend Architecture Document (`front-end-architecture`) or a full stack combined architecture such as `architecture.md`
- Main System Architecture Document (`architecture` - for API contracts and tech stack to give further context)
```

**Best Practices**:

- **Separate required vs optional inputs clearly**: Always use the `required:` and `optional:` YAML structure to make dependencies explicit
- **Include format examples for complex inputs**: Use inline comments (e.g., `# e.g., "1.3"`) to show expected formats
- **Reference configuration sources**: When inputs come from `core-config.yaml`, specify the exact key path (e.g., `qa.qaLocation`)
- **Document fallback behaviors**: For optional inputs, explain what happens if not provided (e.g., "derive from story H1 if missing")
- **Use consistent naming conventions**: Follow BMad patterns like `story_id`, `story_path`, `story_title` for common parameters
- **Support both structured and unstructured formats**: Some tasks use YAML structure, others use bullet lists for document inputs
- **Provide input validation hints**: Include format patterns like `'{epic}.{story}'` to guide input validation

**Other important information**:

**Input sources**: Inputs typically come from three sources:

1. **Configuration files**: Values from `bmad-core/core-config.yaml` (paths, settings)
2. **User prompts**: Direct values provided when invoking the task
3. **Workflow context**: Values passed from previous workflow steps or agent state

**Relationship to Prerequisites**: While Prerequisites define what must exist before task execution (files, states), Inputs define what parameters the task receives during execution. A task may have prerequisites without inputs (self-contained) or inputs without prerequisites (creates new artifacts).

**Variable substitution**: The `{variable}` syntax indicates placeholders that will be replaced with actual values during task execution. These follow BMad's template variable conventions.

**Input absence in self-contained tasks**: Tasks like `kb-mode-interaction.md` and `index-docs.md` don't have Inputs sections because they operate without external parameters, using only their internal instructions and available context.

### Prerequisites Section

**Purpose**: Defines environmental and system conditions that must exist before the LLM can execute the task. Prerequisites ensure the execution environment is ready and properly configured, focusing on states that cannot be validated or created by the task itself during execution.

**Requirement**: Optional

- When to include: When the task requires specific system states, completed prior work, or environmental conditions that must exist before execution begins
- When NOT to include: When all requirements can be validated during task execution, when the task is self-contained, or when checks are better handled in Process or Blocking Conditions sections

**Format**:

```markdown
## Prerequisites

- [State or condition that must exist before execution]
- [Prior task or workflow that must be completed]
- [Environmental requirement or system configuration]
- [Resource availability that cannot be checked programmatically]
```

**Examples from actual tasks**:

From `qa-gate.md`:

```markdown
## Prerequisites

- Story has been reviewed (manually or via review-story task)
- Review findings are available
- Understanding of story requirements and implementation
```

From `review-story.md`:

```markdown
## Prerequisites

- Story status must be "Review"
- Developer has completed all tasks and updated the File List
- All automated tests are passing
```

From `trace-requirements.md`:

```markdown
## Prerequisites

- Story file with clear acceptance criteria
- Access to test files or test specifications
- Understanding of the implementation
```

From `apply-qa-fixes.md`:

```markdown
## Prerequisites

- Repository builds and tests run locally (Deno 2)
- Lint and test commands available:
  - `deno lint`
  - `deno test -A`
```

**Note**: QA artifacts and story/config presence are validated in Process/Blocking Conditions, not listed as prerequisites.

**Best Practices**:

- Focus on system state and completion status rather than file existence (files can be checked in Process)
- Describe human-completed work or manual processes that the task depends on
- Include workflow dependencies where one task must complete before another can begin
- Keep prerequisites conceptual rather than programmatic (e.g., "tests are passing" not "test-results.json exists")
- Use prerequisites for conditions that require human judgment or external system states
- Avoid duplicating checks that will be performed in the Inputs validation or Process sections

**Other important information**:

**Key Distinction from Blocking Conditions**: Prerequisites are checked before task execution begins and focus on environmental readiness. Blocking Conditions are discovered during execution and trigger task termination with specific error messages. Prerequisites also differ from Inputs, which define required parameters and data structures rather than system states.

**When tasks omit Prerequisites**: Many BMad tasks operate without Prerequisites sections because they are self-contained or handle all validation internally. Tasks like `create-next-story.md`, `index-docs.md`, and `kb-mode-interaction.md` omit Prerequisites because they can execute in any valid BMad environment without special preconditions.

**Content Types commonly seen in Prerequisites**:

- **Prior workflow completion**: Other tasks that must finish first
- **System state requirements**: Build status, test results, deployment state
- **Human-completed work**: Manual reviews, approvals, or configurations
- **Knowledge requirements**: Understanding or context the LLM needs
- **Environmental conditions**: Tool availability, service readiness

**Important Distinction**: Prerequisites focus on environment/system readiness that can't be validated by the task. If a task can detect and halt on a missing requirement, it belongs in Blocking Conditions, not Prerequisites. Prerequisites are about "Can this task even attempt to run?" not "Will this task succeed?"

### Instructions/Process Section

**Requirement**: Always present in some form - this is the main execution flow

**Common Names**:

- `## Process`
- `## Instructions`
- `## Processing Flow`
- `## Task Instructions`
- `## Task Execution Instructions`
- `## SEQUENTIAL Task Execution`

**Format**:

```markdown
## Process

1. **Step Name**: Description
   - Sub-step detail
   - Validation check
2. **Next Step**: Description
   - Implementation detail
   - Error handling
3. **Final Step**: Description
   - Output generation
   - Completion criteria
```

**Examples from actual tasks**:

From `test-design.md` (simple numbered list):

```markdown
## Process

### 1. Analyze Story Requirements

Break down each acceptance criterion into testable scenarios.

### 2. Apply Test Level Framework

Quick rules:

- **Unit**: Pure logic, algorithms, calculations
- **Integration**: Component interactions, DB operations
- **E2E**: Critical user journeys, compliance

### 3. Assign Priorities

- **P0**: Revenue-critical, security, compliance
- **P1**: Core user journeys, frequently used

### 4. Design Test Scenarios

For each identified test need, create test scenario YAML

### 5. Validate Coverage

Ensure all acceptance criteria have corresponding test scenarios
```

From `apply-qa-fixes.md` (detailed with sub-sections):

```markdown
## Process (Do not skip steps)

### 0) Load Core Config & Locate Story

- Read `bmad-core/core-config.yaml` and resolve `qa_root` and `story_root`
- Locate story file in `{story_root}/{epic}.{story}.*.md`
  - HALT if missing and ask for correct story id/path

### 1) Collect QA Findings

- Read gate YAML from `{qa_root}/gates/{epic}.{story}-*.yml`
- Parse `top_issues` array for actionable items
- Read assessment markdowns if they exist
```

From `create-next-story.md` (sequential with warnings):

```markdown
## SEQUENTIAL Task Execution (Do not proceed until current Task is complete)

### 0. Load Core Configuration and Check Workflow

### 1. Identify Next Story for Preparation

#### 1.1 Locate Epic Files and Review Existing Stories

### 2. Gather Story Requirements and Previous Story Context

### 3. Gather Architecture Context

#### 3.1 Determine Architecture Reading Strategy
```

**Structural Patterns**:

- **Simple Steps**: Basic numbered list for straightforward tasks
- **Hierarchical Steps**: Nested sections for complex workflows
- **Sequential Enforcement**: Explicit "do not skip" or "SEQUENTIAL" warnings
- **Conditional Logic**: If/then branches within steps
- **Validation Points**: HALT conditions and checkpoints

**Conditional Logic Examples**:

From `validate-next-story.md` (file existence check):

```markdown
### 0. Load Core Configuration and Inputs

- Load `.bmad-core/core-config.yaml`
- If the file does not exist, HALT and inform the user: "core-config.yaml not found. This file is required for story validation."
```

From `create-next-story.md` (story type branching):

```markdown
#### 3.2 Read Architecture Documents Based on Story Type

**For Backend/API Stories, additionally:**
data-models.md, database-schema.md, backend-architecture.md

**For Frontend/UI Stories, additionally:**
frontend-architecture.md, components.md, core-workflows.md

**For Full-Stack Stories:**
Read both Backend and Frontend sections above
```

From `shard-doc.md` (tool availability check):

```markdown
[[LLM: First, check if markdownExploder is set to true in {root}/core-config.yaml.
If it is, attempt to run the command: `md-tree explode {input file} {output path}`.

If the command succeeds, inform the user that the document has been sharded
successfully and STOP - do not proceed further.

If the command fails, inform the user: "The markdownExploder setting is enabled
but the md-tree command is not available."
]]
```

**Validation Point Examples**:

From `apply-qa-fixes.md` (multiple HALT conditions):

```markdown
### 0) Load Core Config & Locate Story

- Read `bmad-core/core-config.yaml` and resolve paths
- Locate story file in `{story_root}/{epic}.{story}.*.md`
  - HALT if missing and ask for correct story id/path

## Blocking Conditions

- No QA artifacts found (neither gate nor assessments)
  - HALT and request QA to generate at least a gate file
```

From `create-next-story.md` (content validation):

```markdown
### 5. Populate Story Template with Full Context

- **`Dev Notes` section (CRITICAL):**
  - MUST contain ONLY information extracted from architecture
  - NEVER invent or assume technical details
  - Every detail MUST include source: `[Source: architecture/{file}]`
  - If not found, explicitly state: "No guidance found in docs"
```

From `validate-next-story.md` (completeness check):

```markdown
### 1. Template Completeness Validation

- Check all required sections are present
- Verify no placeholder text remains
- Ensure all `{{variables}}` are replaced
- Flag any "TODO" or "TBD" markers
```

**Best Practices**:

- Number primary steps for clear sequence
- Use bold for step names to improve scanning
- Include validation and error handling inline
- Specify when steps cannot be skipped or parallelized
- Add HALT points where task must stop for missing requirements
- Use consistent indentation for sub-steps

**Common Elements**:

- **Load/Initialize**: Often starts with loading config or context
- **Gather/Collect**: Assembling required information
- **Process/Transform**: Main logic execution
- **Validate/Verify**: Checking results
- **Output/Save**: Generating deliverables
- **Report/Complete**: Final status or summary

### Outputs Section

**Purpose**: Defines what a task produces upon completion - any artifacts, reports, data, or modifications that result from the task's execution. Tasks can generate multiple outputs in different formats, each serving different purposes within your expansion pack's domain. In BMad core, these are often QA-related (gate files, assessments), but expansion packs can define outputs appropriate to their domain (API responses, database schemas, configuration files, analysis results, etc.).

**Requirement**: Optional

- When to include: When the task generates any form of output - files, reports, YAML blocks, or updates to existing documents
- When NOT to include: When the task is purely analytical without producing artifacts, OR when the task delegates output specification to a template (e.g., tasks used with "run task X with template Y" pattern in agent commands)

**Common Names**:

- `## Outputs`
- `## Output Requirements`
- `## Output 1:`, `## Output 2:` (direct numbered outputs)
- `## Output Deliverables` (rare, only in correct-course.md)

**Format**:

Tasks can have **one or more outputs**, numbered sequentially when multiple artifacts are generated. Each output can use a different format based on its purpose:

```markdown
## Output Requirements # Single unified output section

## Outputs # Multiple outputs with subsections

## Output 1: # Direct numbered outputs
```

Each output within a task can have its own structure:

```markdown
### Output 1: [Artifact Name]

[Description and specifications]

### Output 2: [Different Artifact]

[Different format and requirements]
```

**Examples from actual tasks**:

**Single Section with Multiple Instructions** (from `qa-gate.md`):

```markdown
## Output Requirements

1. **ALWAYS** create gate file at: `qa.qaLocation/gates` from `bmad-core/core-config.yaml`
2. **ALWAYS** append this exact format to story's QA Results section:

   Gate: {STATUS} → qa.qaLocation/gates/{epic}.{story}-{slug}.yml

3. Keep status_reason to 1-2 sentences maximum
4. Use severity values exactly: `low`, `medium`, or `high`
```

**Multiple Outputs with Different Formats** (from `nfr-assess.md`):

```markdown
## Output 1: Gate YAML Block

Generate ONLY for NFRs actually assessed (no placeholders):

# Gate YAML (copy/paste):

nfr_validation:
\_assessed: [security, performance]
security:
status: CONCERNS
notes: 'No rate limiting on auth endpoints'
```

```markdown
## Output 2: Brief Assessment Report

**ALWAYS save to:** `qa.qaLocation/assessments/{epic}.{story}-nfr-{YYYYMMDD}.md`

# NFR Assessment: {epic}.{story}

Date: {date}
Reviewer: Quinn

## Summary

- Security: CONCERNS - Missing rate limiting
- Performance: PASS - Meets <200ms requirement
```

```markdown
## Output 3: Story Update Line

**End with this line for the review task to quote:**

NFR assessment: qa.qaLocation/assessments/{epic}.{story}-nfr-{YYYYMMDD}.md
```

```markdown
## Output 4: Gate Integration Line

**Always print at the end:**

Gate NFR block ready → paste into qa.qaLocation/gates/{epic}.{story}-{slug}.yml under nfr_validation
```

**Subsectioned Outputs** (from `risk-profile.md`):

````markdown
## Outputs

### Output 1: Gate YAML Block

Generate for pasting into gate file under `risk_summary`:

**Output rules:**

- Only include assessed risks; do not emit placeholders
- Sort risks by score (desc) when emitting highest and any tabular lists
- If no risks: totals all zeros, omit highest, keep recommendations arrays empty

```yaml
# risk_summary (paste into gate file):
risk_summary:
  totals:
    critical: X # score 9
    high: Y # score 6
    medium: Z # score 4
    low: W # score 2-3
  highest:
    id: SEC-001
    score: 9
    title: "XSS on profile form"
  recommendations:
    must_fix:
      - "Add input sanitization & CSP"
    monitor:
      - "Add security alerts for auth endpoints"
```
````

```markdown
### Output 2: Risk Assessment Report

**Save to:** `qa.qaLocation/assessments/{epic}.{story}-risk-{YYYYMMDD}.md`

# Risk Assessment: {epic}.{story}

## Executive Summary

## Critical Risks Requiring Immediate Attention

### 1. [ID]: Risk Title

- **Score**: {score}
- **Mitigation**: {strategy}
```

**Output Types**:

- **YAML blocks**: For machine processing and gate files
- **Markdown documents**: For human-readable reports saved to disk
- **Text snippets**: Single-line references for other tasks to consume
- **Update instructions**: Specific changes to existing files
- **Console output**: Information to display but not save

**Location Patterns**:

- **Config-key based**: `qa.qaLocation`, `devStoryLocation` (direct from core-config.yaml)
- **Variable-based**: `{qa_root}`, `{story_root}` (when task sets variables)
- Include identifiers: `{epic}.{story}`
- Add date stamps: `{YYYYMMDD}` format for assessment files
- Use slugs for readability: `{slug}`

**Best Practices**:

- Tasks can generate 1-4+ different outputs, each serving distinct purposes
- Number outputs sequentially (Output 1, Output 2...) when generating multiple artifacts
- Each output can have a completely different format appropriate to its purpose
- Specify exact file paths using BMad configuration variables (e.g., `qa.qaLocation`)
- Include clear instructions for where each output should be saved, pasted, or applied
- Use descriptive names that indicate each artifact's purpose and consumer
- Define schema/format for structured outputs
- Reference core-config.yaml for base paths when using BMad core patterns

**Other important information**:

The variety in output formats is intentional - each task chooses the structure that best communicates its specific deliverables. Common patterns in BMad core include:

- **Gate files**: YAML blocks to paste into `qa.qaLocation/gates/`
- **Assessment reports**: Markdown documents saved to `qa.qaLocation/assessments/`
- **Story updates**: Specific sections to modify in existing story files
- **Task references**: Single-line outputs that other tasks can quote or reference
- **Integration instructions**: Human-readable directions for manual steps

Tasks generate documentation artifacts rather than executable code - they produce assessment reports, gate files, YAML configuration blocks, and updates to existing files. The numbered output pattern (Output 1, Output 2, etc.) helps organize complex deliverables and ensures expansion pack developers understand all artifacts their custom tasks should produce. Expansion packs should define output patterns appropriate to their domain, using BMad core's patterns as examples rather than requirements.

**Template-Based Outputs**:

When tasks use templates (particularly the `create-doc` task from `common/tasks/`), the output format is defined in the template's YAML header rather than in the task itself. This pattern separates output specification from task logic, allowing reusable tasks to work with any template.

Example from `prd-tmpl.yaml`:

```yaml
template:
  output:
    format: markdown
    filename: docs/prd.md
    title: "{{project_name}} Product Requirements Document (PRD)"
```

Example from `qa-gate-tmpl.yaml`:

```yaml
template:
  output:
    format: yaml
    filename: qa.qaLocation/gates/{{epic_num}}.{{story_num}}-{{story_slug}}.yml
    title: "Quality Gate: {{epic_num}}.{{story_num}}"
```

This approach enables:

- **Reusable tasks**: The `create-doc` task works with any template
- **Template-specific destinations**: Each template defines its own output path
- **Variable substitution**: Filenames and titles can include `{{variables}}`
- **Format flexibility**: Templates can specify markdown, yaml, or other formats

**Key distinction**:

- **Task-defined outputs**: The task itself specifies what/where/how to output (e.g., `qa-gate.md`, `risk-profile.md`)
- **Template-defined outputs**: The template YAML header specifies output, task just processes it (e.g., when agents use `create-doc with prd-tmpl.yaml`)

Both patterns coexist in BMad and are complementary, not mutually exclusive. Tasks focused on assessments and QA typically define their own outputs directly, while document generation tasks delegate output specification to templates. When agents specify commands like "create-prd: run task create-doc.md with template prd-tmpl.yaml", the output is determined by the template, not the task.

## Frequent Optional Sections

These sections appear in many tasks but are not required. They provide additional context, constraints, and guidance.

### When to Use This Task

**Purpose**: Helps LLMs understand when to select and apply a particular task by defining specific criteria, boundaries, and alternatives. This section acts as a decision guide, ensuring the LLM chooses the right task for the right situation and understands relationships between similar tasks.

**Requirement**: Optional

- When to include: When multiple similar tasks exist in the expansion pack, when task selection criteria aren't obvious from the name alone, or when improper task selection could lead to significant inefficiency or failure
- When NOT to include: When the task is unique with no alternatives, when the task name and purpose clearly define its use cases, or when the task is always executed as part of a fixed workflow

**Format**:

```markdown
## When to Use This Task

**Use this task when:**

- [Specific condition that makes this task appropriate]
- [Another qualifying scenario]
- [Measurable criteria (e.g., "2-3 stories" not "a few")]

**Use [alternative-task] when:**

- [Condition that makes the alternative more appropriate]
- [Different scenario requiring different approach]

**Use [another-alternative] when:**

- [Yet another scenario with different requirements]

**Do NOT use when:**

- [Anti-pattern or inappropriate scenario]
- [Condition that would cause task failure]
```

**Examples from actual tasks**:

From `brownfield-create-story.md`:

```markdown
## When to Use This Task

**Use this task when:**

- The enhancement can be completed in a single story
- No new architecture or significant design is required
- The change follows existing patterns exactly
- Integration is straightforward with minimal risk
- Change is isolated with clear boundaries

**Use brownfield-create-epic when:**

- The enhancement requires 2-3 coordinated stories
- Some design work is needed
- Multiple integration points are involved

**Use the full brownfield PRD/Architecture process when:**

- The enhancement requires multiple coordinated stories
- Architectural planning is needed
- Significant integration work is required
```

From `create-brownfield-story.md`:

```markdown
## When to Use This Task

**Use this task when:**

- Working on brownfield projects with non-standard documentation
- Stories need to be created from document-project output
- Working from brownfield epics without full PRD/architecture
- Existing project documentation doesn't follow BMad v4+ structure
- Need to gather additional context from user during story creation

**Use create-next-story when:**

- Working with properly sharded PRD and v4 architecture documents
- Following standard greenfield or well-documented brownfield workflow
- All technical context is available in structured format
```

**Best Practices**:

- **Use measurable criteria**: Specify "1-3 stories" rather than "small enhancements" to remove ambiguity
- **Reference alternatives explicitly**: Name the specific tasks or processes that should be used instead
- **Order by likelihood**: List the most common scenarios first
- **Focus on differentiators**: Emphasize what makes this task unique compared to similar ones
- **Include anti-patterns**: Explicitly state when NOT to use the task to prevent misuse
- **Consider task relationships**: Explain how this task fits into larger workflows or task chains
- **Use consistent terminology**: Match the vocabulary used in the Purpose and Instructions sections

**Other important information**:

The "When to Use This Task" section is processed by the LLM during task selection, before the task is loaded for execution. This makes it crucial for proper task routing in expansion packs with multiple similar tasks. The section acts as metadata that helps the LLM's task selection algorithm.

Only 3 out of 21 core BMad tasks include this section, indicating it's primarily needed when task confusion is likely. Tasks like `qa-gate.md`, `review-story.md`, and `apply-qa-fixes.md` omit this section because their purpose and scope are unambiguous from their names and Purpose sections alone.

When creating expansion pack tasks, include this section when you have:

- Multiple tasks that could handle similar scenarios (e.g., different story creation approaches)
- Tasks with overlapping but distinct scopes (e.g., single story vs. epic creation)
- Complex decision trees for task selection (e.g., brownfield vs. greenfield workflows)
- Tasks that users might incorrectly choose based on name alone

### Dependencies/Integration Points

**Purpose**: Identifies which BMad components a task requires to function properly, including configuration files, templates, data files, and outputs from other tasks.
**Requirement**: Optional

- When to include: Formal dependency sections are extremely rare in BMad (only 1 out of 21 core tasks uses one). Most tasks reference dependencies informally within their process steps
- When NOT to include: For the vast majority of tasks - BMad favors inline references over formal dependency declarations

**Format**:

BMad tasks use various informal (or sometimes formal) patterns for declaring dependencies, with no standardized format:

1. **Formal YAML Dependencies Section** (extremely rare - only 1 task):

````markdown
## Dependencies

```yaml
data:
  - data-file-1.md # Description of what this provides
  - data-file-2.md # Another data dependency
```
````

2. **Frontmatter Variables** (rare - 1 task):

```markdown
docOutputLocation: docs/output.md
template: '{root}/templates/template-name.yaml'
```

3. **Inline Process References** (most common - 8+ tasks):

```markdown
- Load `{root}/core-config.yaml` from the project root
- Read the test design assessment from `{qa_root}/assessments/`
- Use template at `{root}/templates/document-tmpl.yaml`
```

**Examples from actual tasks**:

**The only task with formal Dependencies section** - From `test-design.md`:

````markdown
## Dependencies

```yaml
data:
  - test-levels-framework.md # Unit/Integration/E2E decision criteria
  - test-priorities-matrix.md # P0/P1/P2/P3 classification system
```
````

**Inline references within process steps** - From `apply-qa-fixes.md`:

```markdown
## QA Sources to Read

- Gate (YAML): `{qa_root}/gates/{epic}.{story}-*.yml`
  - If multiple, use the most recent by modified time
- Assessments (Markdown):
  - Test Design: `{qa_root}/assessments/{epic}.{story}-test-design-*.md`
  - Traceability: `{qa_root}/assessments/{epic}.{story}-trace-*.md`
  - Risk Profile: `{qa_root}/assessments/{epic}.{story}-risk-*.md`
  - NFR Assessment: `{qa_root}/assessments/{epic}.{story}-nfr-*.md`
```

**Frontmatter declaration** - From `facilitate-brainstorming-session.md`:

```markdown
docOutputLocation: docs/brainstorming-session-results.md
template: '{root}/templates/brainstorming-output-tmpl.yaml'
```

**Config loading in process** - From `create-next-story.md`:

```markdown
- Load `{root}/core-config.yaml` from the project root
- If the file does not exist, HALT and inform the user: "core-config.yaml not found. This file is required for story creation..."
- Extract key configurations: `devStoryLocation`, `prd.*`, `architecture.*`, `workflow.*`
```

**Best Practices**:

- Prefer inline references within process steps (following BMad's actual pattern)
- Only create formal Dependencies sections if you have a specific reason
- Use path variables like `{root}` and `{qa_root}` for portability
- Document what each dependency provides when referencing it
- Check for file existence when dependencies are critical

**Other important information**:

The flexible, informal nature of BMad dependencies means tasks can reference resources as needed without rigid structure. This design philosophy allows tasks to be more readable and self-contained, with dependencies documented where they're actually used rather than in a separate section. When creating expansion pack tasks, follow this informal pattern unless you have a compelling reason for formal dependency declaration.

- List data files in the order they're typically accessed during task execution
- Clearly indicate if a dependency is conditional (e.g., "if risk profile exists")
- Group dependencies by type (data, templates, config) for clarity
- Use YAML format for structured dependencies, inline format for simple lists
- Include error handling instructions when dependencies might be missing

**Other important information**:

Dependencies serve multiple critical functions in the BMad ecosystem:

1. **Build System Integration**: The web-builder uses declared dependencies to bundle all required files into expansion packs
2. **Path Resolution**: Variables like `{root}`, `{qa_root}`, and `{devStoryLocation}` are resolved from `core-config.yaml`
3. **Frontmatter vs Body**: Dependencies can appear in YAML frontmatter (rare) or in the task body (common)
4. **Dynamic Dependencies**: Some tasks compute dependencies at runtime based on config values
5. **Validation Requirements**: Tasks should validate that required dependencies exist before proceeding

**Note**: Most core tasks don't use frontmatter; this is an uncommon pattern.

**Integration Types**:

- **Configuration Dependencies**: Core-config.yaml values needed
- **Template Dependencies**: Templates to process
- **Task Chain Dependencies**: Prior tasks that must run first
- **Data Dependencies**: Reference files, frameworks, preferences
- **Output Dependencies**: Where this task's outputs are consumed

**Best Practices**:

- Specify exact config keys needed (e.g., `qa.qaLocation`)
- Use glob patterns for finding files (e.g., `*-risk-*.md`)
- Note if dependencies are optional vs required
- Explain fallback behavior if dependencies missing
- Document which outputs feed into other tasks

### Blocking Conditions

**Requirement**: Optional, defines when to halt execution

**Format**:

```markdown
## Blocking Conditions

- Condition that stops execution
  - HALT and specific action/message
- Another blocking condition
  - HALT and what to do
```

**Examples from actual tasks**:

From `apply-qa-fixes.md`:

```markdown
## Blocking Conditions

- No QA artifacts found (neither gate nor assessments)
  - HALT and request QA to generate at least a gate file (or proceed only with clear developer-provided fix list)
```

From `validate-next-story.md` (embedded in process):

```markdown
### 0. Load Core Configuration and Inputs

- Load `.bmad-core/core-config.yaml`
- If the file does not exist, HALT and inform the user: "core-config.yaml not found. This file is required for story validation."
```

**Note**: `core-config.yaml` path varies by task (`.bmad-core/core-config.yaml`, `bmad-core/core-config.yaml`, or `{root}/core-config.yaml`). Examples retain the exact text from each task.

From `review-story.md` (dedicated section with non-HALT phrasing):

```markdown
## Blocking Conditions

Stop the review and request clarification if:

- Story file is incomplete or missing critical sections
- File List is empty or clearly incomplete
- No tests exist when they were required
- Code changes don't align with story requirements
- Critical architectural issues that require discussion
```

From `create-next-story.md` (embedded in process):

```markdown
### 0. Load Core Configuration and Check Workflow

- Load `{root}/core-config.yaml`
- If the file does not exist, HALT and inform the user: "core-config.yaml not found. This file is required for story creation. You can either: 1) Copy it from GITHUB bmad-core/core-config.yaml and configure it for your project OR 2) Run the BMad installer against your project to upgrade and add the file automatically. Please add and configure core-config.yaml before proceeding."

### 1. Identify Next Story for Preparation

- If highest existing story is not 'Done', ALERT user: "ALERT: Found incomplete story! File: {lastEpicNum}.{lastStoryNum}.story.md Status: [current status] You should fix this story first, but would you like to accept risk & override to create the next story in draft?"
- If epic is complete, prompt user to choose next action (never auto-advance; require explicit instruction)
```

**Common Blocking Patterns**:

- **Missing Required Files**: Config, story, architecture docs
- **No Work Available**: May prompt for next action (e.g., create-next-story) or HALT (e.g., missing QA in apply-qa-fixes)
- **Ambiguous State**: Multiple options, unclear next step
- **Invalid Input**: Wrong story ID, malformed data
- **Environment Not Ready**: Often handled via prerequisites/validation loops rather than HALTs; if blocking, specify owner and next command/task

**HALT Message Best Practices**:

- Be specific about what's missing
- Provide clear next steps or alternatives
- Include exact file paths when relevant
- Suggest commands or tasks to run
- Offer workarounds when possible

**Integration with Process**:

- Blocking conditions can be in dedicated section
- Or embedded within process steps as HALT points
- Prefer "HALT" for searchability, but tasks also use "ALERT" and "Stop the review"; consider standardizing on "HALT" going forward
- Place early checks at beginning of process

### Completion Checklist

**Requirement**: Optional, verification criteria

**Format**:

```markdown
## Completion Checklist

- [ ] Item to verify before considering task complete
- [ ] Another verification point
- [ ] Final check
```

**Note**: Completion-style checks may appear as "Completion Checklist", "Validation Checklist", or be implemented via a separate checklist executed with `{root}/tasks/execute-checklist` (e.g., `{root}/checklists/story-draft-checklist.md`).

**Examples from actual tasks**:

From `apply-qa-fixes.md`:

```markdown
## Completion Checklist

- deno lint: 0 problems
- deno test -A: all tests pass
- All high severity `top_issues` addressed
- NFR FAIL → resolved; CONCERNS minimized or documented
- Coverage gaps closed or explicitly documented with rationale
- Story updated (allowed sections only) including File List and Change Log
- Status set according to Status Rule
```

From `create-next-story.md` (embedded in process with checklist execution):

```markdown
### 6. Story Draft Completion and Review

- Review all sections for completeness and accuracy
- Verify all source references are included for technical details
- Ensure tasks align with both epic requirements and architecture constraints
- Update status to "Draft" and save the story file
- Execute `{root}/tasks/execute-checklist` `{root}/checklists/story-draft-checklist`
```

**Note**: The checklist content lives in `.bmad-core/checklists/story-draft-checklist.md`.

From `qa-gate.md` (uses Output Requirements instead of checklist):

**Note**: Rather than a Completion Checklist, qa-gate.md enforces verification through mandatory Output Requirements that serve as completion criteria:

```markdown
## Output Requirements

1. ALWAYS create gate file at: `qa.qaLocation/gates` (from bmad-core/core-config.yaml)
2. ALWAYS append this exact format to story's QA Results section:
   Gate: {STATUS} → qa.qaLocation/gates/{epic}.{story}-{slug}.yml
3. Keep status_reason to 1–2 sentences maximum
4. Use severity values exactly: `low`, `medium`, or `high`
```

**Purpose of Checklists**:

- Ensure completeness before task finalization
- Provide verification points for quality
- Remind of critical steps that might be missed
- Serve as a contract of what "done" means

**Best Practices**:

- Use checkbox format `- [ ]` for visual scanning
- Order items by execution sequence or importance
- Keep items specific and verifiable
- Include both technical and process checks
- Reference specific files or locations to check
- Prefer checkboxes for dedicated "Completion Checklist" sections. When completion is enforced via outputs or embedded steps (e.g., qa-gate, create-next-story), bullets without checkboxes are acceptable

### Success Criteria

**Requirement**: Optional, defines done state

**Format**:

```markdown
## Success Criteria

Task is successful when:

1. Specific outcome achieved
2. Quality threshold met
3. Deliverable completed
```

**Note**: Both numbered lists and bullet points are used in actual tasks.

**Note**: Success Criteria sections are relatively rare in core tasks (found in 5 of 21 tasks). Many tasks embed success definitions in their Outputs, Completion Checklists, or process steps instead.

**Examples from actual tasks**:

From `brownfield-create-story.md`:

```markdown
## Success Criteria

The story creation is successful when:

1. Enhancement is clearly defined and appropriately scoped for single session
2. Integration approach is straightforward and low-risk
3. Existing system patterns are identified and will be followed
4. Rollback plan is simple and feasible
5. Acceptance criteria include existing functionality verification
```

From `document-project.md`:

```markdown
## Success Criteria

- Single comprehensive brownfield architecture document created
- Document reflects REALITY including technical debt and workarounds
- Key files and modules are referenced with actual paths
- Models/APIs reference source files rather than duplicating content
- If PRD provided: Clear impact analysis showing what needs to change
- Document enables AI agents to navigate and understand the actual codebase
- Technical constraints and "gotchas" are clearly documented
```

**Alternative Patterns**:

Some tasks embed success definitions in other sections rather than a dedicated Success Criteria heading:

- **In Outputs**: Tasks like `risk-profile.md` define success through required outputs (risk summary YAML, markdown report)
- **In Completion Checklists**: Tasks like `apply-qa-fixes.md` define success through verification items
- **In Process Steps**: Tasks like `qa-gate.md` embed success in mandatory output requirements

**Relationship to Completion Checklist**:

- Success Criteria define the "what" - outcomes to achieve
- Completion Checklists define the "how" - steps to verify
- Success Criteria are outcome-focused
- Checklists are process-focused

**Best Practices**:

- State criteria as measurable outcomes
- Focus on deliverables and quality standards
- Use either numbered lists or bullets consistently within a task
- Keep criteria specific to the task's purpose
- Consider embedding criteria in Outputs or Checklists if no dedicated section

### Key Principles

**Purpose**: Define behavioral modifiers that shape how the LLM executes your task. These principles override default agent behaviors and establish the execution philosophy.

**When to Include Key Principles in Your Task**:

- When establishing a domain expertise perspective (e.g., "You are a Security Auditor")
- When you need to override default LLM behaviors (verbosity, caution, speed)
- When setting quality/speed trade-offs for execution
- When defining priority order for competing concerns
- When enforcing specific output constraints

**How Key Principles Affect LLM Behavior**:

- They persist throughout task execution as behavioral modifiers
- They override base agent tendencies and defaults
- They establish authority level for modifications
- They resolve conflicts when multiple approaches exist
- They set the "voice" and expertise level

**Template for Creating Your Key Principles**:

```markdown
## Key Principles

# Pick patterns that apply to your task:

# Domain expertise (if needed):

- You are a [Role] providing [specific value]
- You have the authority to [action] when [condition]

# Speed/quality balance:

- [Fast/Thorough] - [specific guidance on trade-offs]
- Focus on [primary goal] over [secondary consideration]

# Output constraints:

- Keep it [minimal/comprehensive] and [quality attribute]
- Always [required action] to [location/format]
- [Constraint]: [specific limitation or requirement]

# Priority ordering (order matters):

- [Most important principle]
- [Second priority]
- [Lower priority]
```

**Pattern Library from Core Tasks** (Diverse Task Families):

**Facilitation & Interaction Pattern** (from `facilitate-brainstorming-session.md`, partial):

```markdown
## Key Principles

- **YOU ARE A FACILITATOR**: Guide the user to brainstorm, don't brainstorm for them (unless they request it persistently)
- **INTERACTIVE DIALOGUE**: Ask questions, wait for responses, build on their ideas
- **ONE TECHNIQUE AT A TIME**: Don't mix multiple techniques in one response
- **CONTINUOUS ENGAGEMENT**: Stay with one technique until user wants to switch
- **DRAW IDEAS OUT**: Use prompts and examples to help them generate their own ideas
- **REAL-TIME ADAPTATION**: Monitor engagement and adjust approach as needed
- Maintain energy and momentum
- Defer judgment during generation

# [Additional principles omitted: quantity goals, collaborative building, documentation]
```

_Use when_: Task requires user interaction and cannot be automated

**Quick Assessment Pattern** (from `nfr-assess.md`):

```markdown
## Key Principles

- Focus on the core four NFRs by default
- Quick assessment, not deep analysis
- Gate-ready output format
- Brief, actionable findings
- Skip what doesn't apply
- Deterministic status rules for consistency
- Unknown targets → CONCERNS, not guesses
```

_Use when_: Task needs speed over completeness, with clear fallback rules

**Risk Management Pattern** (from `risk-profile.md`):

```markdown
## Key Principles

- Identify risks early and systematically
- Use consistent probability × impact scoring
- Provide actionable mitigation strategies
- Link risks to specific test requirements
- Track residual risk after mitigation
- Update risk profile as story evolves
```

_Use when_: Task involves assessment, scoring, and tracking over time

**Authority & Constraints Pattern** (from `apply-qa-fixes.md`):

```markdown
## Key Principles

- Deterministic, risk-first prioritization
- Minimal, maintainable changes
- Tests validate behavior and close gaps
- Strict adherence to allowed story update areas
- Gate ownership remains with QA; Dev signals readiness via Status
```

_Use when_: Task modifies existing content with specific boundaries

**Minimalist Execution Pattern** (from `qa-gate.md`):

```markdown
## Key Principles

- Keep it minimal and predictable
- Fixed severity scale (low/medium/high)
- Always write to standard path
- Always update story with gate reference
- Clear, actionable findings
```

_Use when_: Task needs consistent, predictable outputs

**Decision Guide for Including Key Principles**:

1. **Does your task need domain expertise?**
   → Add role definition principle

2. **Are there quality/speed trade-offs?**
   → Add balance principle (fast vs thorough)

3. **Does task modify existing content?**
   → Add authority and constraint principles

4. **Are there competing priorities?**
   → Order principles by importance

5. **Does output need specific format/location?**
   → Add output constraint principles

### LLM Directives

**Purpose**: Provide hidden processing instructions that shape LLM behavior during task execution without exposing implementation details to users. These directives enable sophisticated conditional logic, tool integration, dynamic content generation, and execution flow control while maintaining a clean user experience.

**Requirement**: Optional

- When to include: When tasks need complex processing logic, conditional branching based on environment checks, tool availability verification, or extensive content generation that shouldn't clutter the visible task instructions
- When NOT to include: When simple, visible instructions suffice; when transparency about processing logic benefits users; when the task is straightforward without conditional paths

**Format**:

```markdown
[[LLM: Specific instructions for the AI to follow internally.
Can span multiple lines and include:

- Conditional logic (if/then statements)
- Tool availability checks
- Complex generation instructions
- Error handling procedures
- Multi-step internal workflows
  ]]
```

**Examples from actual tasks**:

From `shard-doc.md`:

```markdown
[[LLM: First, check if markdownExploder is set to true in {root}/core-config.yaml. If it is, attempt to run the command: `md-tree explode {input file} {output path}`.

If the command succeeds, inform the user that the document has been sharded successfully and STOP - do not proceed further.

If the command fails (especially with an error indicating the command is not found or not available), inform the user: "The markdownExploder setting is enabled but the md-tree command is not available. Please either:

1. Install @kayvan/markdown-tree-parser globally with: `npm install -g @kayvan/markdown-tree-parser`
2. Or set markdownExploder to false in {root}/core-config.yaml

**IMPORTANT: STOP HERE - do not proceed with manual sharding until one of the above actions is taken.**"
]]
```

From `document-project.md`:

```markdown
[[LLM: Generate a comprehensive BROWNFIELD architecture document that reflects the ACTUAL state of the codebase.

**CRITICAL**: This is NOT an aspirational architecture document. Document what EXISTS, including:

- Technical debt and workarounds
- Inconsistent patterns between different parts
- Legacy code that can't be changed
- Integration constraints
- Performance bottlenecks

**Document Structure**:

# [Project Name] Brownfield Architecture Document

## Introduction

This document captures the CURRENT STATE of the [Project Name] codebase...
]]
```

**Best Practices**:

- Use LLM directives for complex conditional logic that would confuse users if visible
- Include tool availability checks and graceful fallback instructions
- Embed large document generation templates within directives to keep tasks concise
- Provide clear STOP/HALT conditions within directives for execution control
- Structure multi-step internal workflows with clear decision points
- Always inform users of outcomes, even when processing happens invisibly
- Use CRITICAL markers for instructions that must not be overlooked
- Keep error messages user-friendly while handling complexity internally

**Other important information**:

LLM directives are **rarely needed** in BMad tasks - only about 10% of core tasks use them (2 out of 21 tasks). Most tasks accomplish their goals effectively with visible instructions alone. LLM directives in tasks differ from those in templates or checklists and should be used sparingly.

In tasks, LLM directives primarily handle:

- **Environment detection**: Checking configuration settings, tool availability
- **Conditional execution**: Different paths based on project state or user responses
- **Content generation**: Large document structures or complex outputs
- **Error recovery**: Graceful handling when expected tools/files aren't available
- **Hidden complexity**: Processing logic that users don't need to see but agents must follow

The `[[LLM: ...]]` syntax ensures these instructions are processed by the AI but never shown to users. While this maintains a clean interface, **consider first whether visible instructions would be clearer** - transparency often benefits users more than hidden logic.

When creating expansion pack tasks, you MAY use LLM directives if truly necessary to:

- Check for domain-specific tools or dependencies
- Generate complex domain artifacts (reports, configurations, analyses)
- Implement branching logic based on your domain's requirements
- Handle edge cases without cluttering visible instructions

**Note on Quoting**: When documenting examples from BMad source files, preserve typos and formatting exactly as they appear (e.g., "AEGNT" instead of "AGENT" if that's what the source contains)

### Important Notes

**Purpose**: Add critical warnings and reminders that prevent task failure or misuse. These notes act as guardrails during execution.

**When to Include Important Notes in Your Task**:

- When external tools or dependencies might not be available
- When specific steps MUST NOT be skipped
- When there are scope restrictions (what NOT to modify)
- When timing matters (must run before/during/after something)
- When common mistakes could break the task

**Placement Strategy Decision Tree**:

```
Number of warnings needed?
├─ 1-2 warnings → Use inline placement
│   └─ Place at the exact step where it matters
├─ 3+ warnings → Create dedicated ## Important Notes section
└─ Warning should be hidden from user → Use [[LLM: note]] blocks
```

**Warning Escalation Levels**:

- **Note**: General reminder (can be overlooked without breaking task)
- **Important**: Could cause issues if ignored (degraded results)
- **CRITICAL**: Will break task if ignored (execution failure)
- **HALT/ALERT/STOP**: Must stop execution immediately if condition is met
  - **HALT**: Most common, used for blocking conditions
  - **ALERT**: Used for user warnings requiring decision (e.g., create-next-story)
  - **STOP**: Used for immediate termination (e.g., shard-doc)
  - _Note: Consider standardizing on HALT for new tasks_

**Templates for Creating Important Notes**:

```markdown
## Important Notes # Use when you have 3+ warnings

- External dependency: [Tool/file] may not be available in all environments
- Timing critical: Run this DURING [phase], not [other phase]
- Scope restriction: Only modify [allowed sections], never touch [restricted]
- Common failure: Do NOT [action] as it will [consequence]

# Or inline placement:

**Important**: [Specific warning exactly where it applies in the process]

**CRITICAL**: [Warning about something that will break if ignored]

# Or hidden from user:

[[LLM: Important: [Internal note about execution that user shouldn't see]]]
```

**Verified Examples from Core Tasks**:

**Inline CRITICAL Warning** (from `create-next-story.md`):

```markdown
- **CRITICAL**: NEVER automatically skip to another epic. User MUST explicitly instruct which story to create.
```

_Context_: Placed in the process step about epic selection

**Inline Dev Notes CRITICAL** (from `create-next-story.md`):

```markdown
- **`Dev Notes` section (CRITICAL):**
  - CRITICAL: This section MUST contain ONLY information extracted from architecture documents. NEVER invent or assume technical details.
```

_Context_: Enforces data source constraints for technical details

**Process Header Warning** (from `apply-qa-fixes.md`):

```markdown
## Process (Do not skip steps)
```

_Context_: Warning in section header to prevent skipping

**Scope Restriction** (from `apply-qa-fixes.md`):

```markdown
### 5) Update Story (Allowed Sections ONLY)

CRITICAL: Dev agent is ONLY authorized to update these sections of the story file. Do not modify any other sections (e.g., QA Results, Story, Acceptance Criteria, Dev Notes, Testing):

- Tasks / Subtasks Checkboxes (mark any fix subtask you added as done)
- Dev Agent Record →
  - Agent Model Used (if changed)
  - Debug Log References (commands/results, e.g., lint/tests)
  - Completion Notes List (what changed, why, how)
  - File List (all added/modified/deleted files)
- Change Log (new dated entry describing applied fixes)
```

_Context_: Clear boundaries on what can be modified

**Hidden LLM Note** (from `shard-doc.md`):

```markdown
[[LLM: First, check if markdownExploder is set to true in {root}/core-config.yaml. If it is, attempt to run the command: `md-tree explode {input file} {output path}`.

If the command succeeds, inform the user that the document has been sharded successfully and STOP - do not proceed further.

If the command fails (especially with an error indicating the command is not found or not available), inform the user: "The markdownExploder setting is enabled but the md-tree command is not available. Please either:
...]]
```

_Context_: Tool availability check with STOP directive hidden from user

**Common Patterns for Important Notes**:

1. **External Dependencies Pattern**:

   - Note about optional tools
   - Fallback behavior if unavailable
   - Example: shard-doc's markdownExploder check

2. **Scope Restriction Pattern**:

   - List allowed modifications explicitly
   - State what must NOT be touched
   - Example: apply-qa-fixes story update limits

3. **Execution Order Pattern**:

   - CRITICAL for non-skippable sequences
   - Warning about dependencies between steps
   - Example: "Do not skip steps" in apply-qa-fixes

4. **Authority Boundary Pattern**:
   - Define what the task can/cannot do
   - Ownership and permission constraints
   - Example: Dev vs QA section ownership

**Best Practices for Expansion Pack Tasks**:

1. **Be Specific**: "Only modify X, Y, Z sections" not "be careful with modifications"
2. **Explain Consequences**: "Will cause [specific failure]" not just "don't do this"
3. **Place Strategically**: Put warning exactly where it's needed, not buried elsewhere
4. **Use Correct Escalation**: CRITICAL for breaking issues, Important for degradation
5. **Don't Over-Warn**: Too many warnings dilute impact - be selective

[PLACEHOLDER: Additional component sections to be added]
