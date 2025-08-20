# Deep Dive: Templates and Their Connections to Tasks and Agents

## Template Architecture Overview

Templates in BMad are YAML-based document schemas that define structured, repeatable document formats. They act as the "forms" that guide document creation, ensuring consistency and completeness across all project artifacts.

## Core Template Structure

### 1. Template Metadata
Every template starts with metadata that defines its identity and output:

```yaml
template:
  id: unique-template-id          # Unique identifier
  name: Human Readable Name       # Display name
  version: 2.0                     # Version tracking (most templates are v2.0, qa-gate is v1.0)
  output:
    format: markdown               # Output format (markdown or yaml for qa-gate)
    filename: docs/output.md       # Default output location (can use variables like {{epic_num}})
    title: "{{project_name}} Doc"  # Document title with variables
```

### 2. Workflow Configuration
Templates define how they should be processed:

```yaml
workflow:
  mode: interactive               # interactive | non-interactive
  elicitation: advanced-elicitation  # Reference to elicitation task
  custom_elicitation:            # Optional custom options
    title: "Custom Actions"
    options:
      - "Custom option 1"
      - "Custom option 2"
```

**Note on `elicitation: advanced-elicitation`:**
This refers to the `advanced-elicitation.md` task which provides a system-wide elicitation framework. When specified, the create-doc task will use this advanced elicitation system that:
- Intelligently selects 9 relevant elicitation methods from the `elicitation-methods.md` data file
- Presents them as numbered options (0-8) plus "Proceed" (9)
- Methods include techniques like "Critique and Refine", "Tree of Thoughts", "Red Team vs Blue Team", etc.
- Allows iterative refinement until user chooses to proceed

### 3. Section Types and Behaviors

Templates support various section types, each with specific behaviors:

#### **Basic Content Types**
```yaml
- id: section-id
  title: Section Title
  type: text                    # Single line/paragraph (or template-text, choice, paragraphs)
  content: "Static content"      # Pre-filled content
  template: "{{variable}}"      # Variable substitution (or multi-line with |)
  instruction: "Guidance text"  # Instructions for filling (often multi-line with |)
```

#### **List Types**
```yaml
- type: bullet-list             # Unordered list
- type: numbered-list           # Ordered list (can have prefix: like "FR" or "NFR")
- type: checklist              # Checkbox list
```

**Note:** Lists can include additional properties:
- `prefix:` for numbered lists (e.g., "FR" for functional requirements)
- `examples:` to provide sample entries
- `template:` for item formatting

#### **Structured Types**
```yaml
- type: table
  columns: [Col1, Col2, Col3]  # Table structure
  
- type: mermaid
  mermaid_type: graph          # Diagram type
```

#### **Interactive Types**
```yaml
- elicit: true                 # Requires user interaction
- condition: expression        # Conditional inclusion
- repeatable: true            # Can be repeated multiple times
```

## Template-Task Integration

### 1. Task References Template
Tasks reference templates primarily through command parameters:

#### **Via Command Parameter (Primary Method)**
```yaml
commands:
  - create-prd: run task create-doc.md with template prd-tmpl.yaml
```

**Note:** Frontmatter is rarely used for template references. Most tasks use runtime parameter passing or configuration-driven discovery.

#### **Via Configuration**
Some tasks (like qa-gate) get template locations from core-config.yaml:
```yaml
qa:
  qaLocation: "docs/qa"  # Template uses this for output paths
```

### 2. The create-doc Task Pattern
The `create-doc.md` task is the primary template processor:

```
Task Flow:
1. Load template YAML
2. Parse sections
3. For each section:
   - Check conditions
   - Draft content using instructions
   - If elicit: true → Present options 1-9
   - Wait for user input
   - Apply changes
4. Save to output file
```

### 3. Template Variables
Templates use double-brace syntax for variables:
- `{{project_name}}` - Replaced during processing
- `{{user_input}}` - Filled from user responses
- `{{section_content}}` - Dynamic content

## Template-Agent Relationships

### 1. Agent Template Dependencies
Agents declare which templates they can use:

```yaml
dependencies:
  templates:
    - prd-tmpl.yaml
    - architecture-tmpl.yaml
```

### 2. Agent-Specific Templates
Different agents own different document types:

| Agent | Primary Templates |
|-------|------------------|
| analyst | project-brief, market-research, competitor-analysis |
| pm | prd, brownfield-prd |
| architect | architecture, brownfield-architecture, front-end-architecture, fullstack-architecture |
| ux-expert | front-end-spec |
| sm/po | story-tmpl |
| qa (Test Architect) | qa-gate |

### 3. Template Ownership Pattern
Templates can specify which agents can edit sections:

```yaml
# In agent_config section (story-tmpl.yaml):
agent_config:
  editable_sections:
    - Status
    - Story
    - Acceptance Criteria
    # ... etc

# In individual sections:
sections:
  - id: dev-notes
    owner: scrum-master        # Who creates it
    editors: [scrum-master]    # Who can modify it
```

**Note:** The story template uniquely includes an `agent_config` section that defines which sections agents can edit.

## Advanced Template Patterns

### 1. Nested Sections
Templates support hierarchical section organization:

```yaml
sections:
  - id: parent-section
    title: Parent Section
    sections:
      - id: child-1
        title: Child Section 1
      - id: child-2
        title: Child Section 2
        sections:
          - id: grandchild
            title: Nested Deeper
```

### 2. Conditional Sections
Sections can be conditionally included:

```yaml
- id: ui-section
  condition: PRD has UX/UI requirements
  title: User Interface Goals
```

### 3. Repeatable Sections
For dynamic content that varies in quantity:

```yaml
- id: technique-sessions
  title: Technique Sessions
  repeatable: true            # Can have multiple instances
  template: "{{technique_content}}"
```

### 4. Mixed Content Sections
Sections can combine multiple content types:

```yaml
sections:
  - id: complex-section
    content: "Static intro text"
    template: "Dynamic: {{variable}}"
    sections:
      - type: bullet-list
      - type: table
        columns: [A, B, C]
```

## Template Workflow Modes

### 1. Interactive Mode
- Default for most templates
- Requires user interaction via elicitation
- Presents numbered options (1-9)
- Cannot be skipped or automated
- Ensures user control and input

### 2. Non-Interactive Mode
- Used for output templates (e.g., brainstorming-output)
- Processes all sections automatically
- No user prompts or elicitation
- Suitable for data transformation

### 3. YOLO Mode
- User-triggered via `#yolo` command
- Processes all sections at once
- Still respects elicit: true for critical sections
- Speeds up document creation

## Template Processing Rules

### 1. Elicitation Protocol
When `elicit: true`:
```
1. Present section content
2. Provide detailed rationale
3. Show options 1-9:
   - Option 1: "Proceed to next section"
   - Options 2-9: From elicitation-methods data file
4. WAIT for user response (mandatory)
```

**The Elicitation System:**
BMad has a comprehensive elicitation system with 30+ methods defined in `data/elicitation-methods.md`, including:
- **Core Reflective**: Expand/Contract, Explain Reasoning, Critique and Refine
- **Structural Analysis**: Logical Flow, Goal Alignment
- **Risk and Challenge**: Risk Identification, Critical Perspective
- **Creative Exploration**: Tree of Thoughts, Hindsight Reflection
- **Multi-Persona**: Agile Team Perspectives, Stakeholder Roundtable
- **Advanced Techniques**: Self-Consistency, ReWOO, Meta-Prompting
- **Game-Based**: Red Team vs Blue Team, Innovation Tournament

The `advanced-elicitation.md` task intelligently selects which 9 methods to offer based on content type and context.

### 2. Section Processing Order
- Sections processed sequentially
- Parent sections before children
- Conditions evaluated before processing
- Repeatable sections handled dynamically

### 3. Variable Resolution
- Variables resolved at runtime
- User inputs captured and stored
- Cross-section references supported
- Default values can be specified

## Template Types by Purpose

### 1. Planning Templates
- **project-brief**: Initial project definition
- **prd/brownfield-prd**: Requirements documentation
- **architecture**: Technical design

### 2. Design Templates
- **front-end-spec**: UI/UX specifications
- **fullstack-architecture**: Combined architecture

### 3. Analysis Templates
- **market-research**: Market analysis
- **competitor-analysis**: Competitive landscape

### 4. Execution Templates
- **story-tmpl**: User story format
- **brainstorming-output**: Session results

### 5. Quality Templates
- **qa-gate-tmpl**: Quality gate decisions (v1.0, outputs YAML not markdown)

## v5.0 Quality Gate Template

The qa-gate template is a critical v5.0 addition that enables advisory quality decisions:

### qa-gate-tmpl.yaml Structure

```yaml
# Required fields (keep these first)
schema: 1
story: "{{epic_num}}.{{story_num}}"
story_title: "{{story_title}}"
gate: "{{gate_status}}" # PASS|CONCERNS|FAIL|WAIVED
status_reason: "{{status_reason}}" # 1-2 sentence summary
reviewer: "Quinn (Test Architect)"
updated: "{{iso_timestamp}}"

# Always present but only active when WAIVED
waiver: { active: false }

# Issues (if any) - Use fixed severity: low | medium | high
top_issues: []
  # Example:
  # - id: "SEC-001"
  #   severity: high  # ONLY: low|medium|high
  #   finding: "No rate limiting on login endpoint"
  #   suggested_action: "Add rate limiting middleware before production"

# Risk summary (from risk-profile task if run)
risk_summary:
  totals: { critical: 0, high: 0, medium: 0, low: 0 }
  recommendations:
    must_fix: []
    monitor: []
```

**Note:** The qa-gate template includes extensive examples of optional fields in its `examples:` and `optional_fields_examples:` sections, including:
- Quality scores and expiry dates
- Evidence and traceability
- NFR validation status
- History tracking
- Detailed recommendations with code references

The template is designed to be minimal by default with the ability to add more detail as needed.

### Gate Status Definitions

- **PASS**: All quality criteria met, no concerns
- **CONCERNS**: Issues identified but not blocking, team decides
- **FAIL**: Critical issues that should be addressed
- **WAIVED**: Concerns explicitly waived by team decision

### Usage Pattern

```yaml
# Template output configuration:
output:
  format: yaml  # Note: YAML format, not markdown
  filename: qa.qaLocation/gates/{{epic_num}}.{{story_num}}-{{story_slug}}.yml
  
# The qa.qaLocation is resolved from core-config.yaml
```

## Template Creation Guidelines

### 1. Structure Guidelines
```yaml
template:
  id: kebab-case-id
  name: Human Readable
  version: Start at 1.0 or 2.0
  output:
    format: markdown (typically)
    filename: docs/category/name.md
    title: Use {{variables}} for dynamic parts
```

### 2. Section Design Principles
- **Hierarchical**: Organize related content
- **Progressive**: Build from general to specific
- **Complete**: Include all necessary information
- **Flexible**: Support variations via conditions
- **Interactive**: Use elicitation for critical decisions

### 3. Instruction Writing
- Be specific about what's needed
- Provide examples where helpful
- Reference source documents
- Include validation criteria
- Guide decision-making

### 4. Variable Naming
- Use snake_case for most variables
- Be descriptive: `{{story_title}}`, `{{epic_num}}`, `{{story_num}}`
- Some use special prefixes: `{{iso_timestamp}}` for dates
- Avoid ambiguity
- Document expected values
- Can use dot notation in output paths: `qa.qaLocation` references config values

## Template-Workflow Integration

Templates integrate with workflows at multiple points:

```yaml
workflow:
  sequence:
    - agent: pm
      creates: prd.md          # Uses prd-tmpl
      requires: project-brief.md
    - agent: architect
      creates: architecture.md  # Uses architecture-tmpl
      requires: prd.md
```

## Best Practices for Template Design

### 1. Consistency
- Use consistent section structures
- Maintain naming conventions
- Standardize instruction formats

### 2. Modularity
- Create reusable section patterns
- Avoid duplication
- Support composition

### 3. User Experience
- Clear instructions
- Logical flow
- Appropriate elicitation points
- Helpful examples

### 4. Flexibility
- Support multiple use cases
- Conditional sections for variations
- Extensible structure

### 5. Documentation
- Include inline help
- Provide examples
- Document dependencies
- Explain conditions

## Template Evolution

Templates evolve through versions:
- **v1.0**: Basic structure (e.g., qa-gate-tmpl.yaml)
- **v2.0**: Enhanced with elicitation (most templates)
- **Future**: AI-assisted completion

Version changes should be tracked in the template's changelog section. Most templates are at v2.0 with advanced elicitation support, while newer templates like qa-gate start at v1.0.

## Template Instructions vs Task Instructions: When to Use Which

### The Division of Responsibility Pattern

There's a clear pattern in BMad for when instructions belong in templates versus tasks:

#### **Template Instructions - The "What"**
Templates contain instructions for **content creation** - what should go in each section:

```yaml
# Template instruction example
- id: technical-summary
  instruction: |
    Provide a brief paragraph (3-5 sentences) overview of:
    - The system's overall architecture style
    - Key components and their relationships
    - Primary technology choices
```

**Use template instructions when:**
- Defining what content belongs in a section
- Providing examples of expected output
- Explaining the purpose of a section
- Giving formatting guidelines
- Specifying validation criteria for content
- Setting elicitation requirements

#### **Task Instructions - The "How"**
Tasks contain instructions for **process and orchestration** - how to gather information and populate templates:

```markdown
# Task instruction example
### 3. Gather Architecture Context
#### 3.1 Determine Architecture Reading Strategy
- If `architectureVersion: >= v4` and `architectureSharded: true`: Read `{architectureShardedLocation}/index.md`
- Extract ONLY information directly relevant to implementing the current story
- Every technical detail MUST include its source reference
```

**Use task instructions when:**
- Defining multi-step processes
- Specifying data gathering procedures
- Orchestrating between multiple files/sources
- Setting up conditional logic flows
- Managing state between operations
- Coordinating validation steps

### Common Patterns

#### **Pattern 1: Simple Document Creation**
```
Template: Contains all section instructions
Task: Generic create-doc.md just processes the template
Example: project-brief-tmpl.yaml
```

#### **Pattern 2: Complex Document Assembly**
```
Template: Basic section structure and output format
Task: Complex logic for gathering and assembling content
Example: create-next-story.md task with story-tmpl.yaml
```

#### **Pattern 3: Transform and Output**
```
Template: Output structure only (non-interactive)
Task: All transformation logic and data processing
Example: facilitate-brainstorming-session.md with brainstorming-output-tmpl.yaml
```

#### **Pattern 4: Guided Creation**
```
Template: Detailed instructions with elicitation points
Task: Minimal - just invokes create-doc
Example: prd-tmpl.yaml with extensive instructions
```

### Decision Framework

Choose **template instructions** when:
1. Instructions are about content requirements
2. User needs guidance on what to write
3. Examples would help clarify expectations
4. The instruction applies to a specific section
5. Elicitation is needed for that section

Choose **task instructions** when:
1. Instructions involve multiple steps
2. External data needs to be gathered
3. Files need to be analyzed or processed
4. Complex conditional logic is required
5. Multiple templates might be involved
6. State needs to be maintained across operations

### Real-World Examples

#### **Story Creation (Complex Task + Simple Template)**
- **Task** (`create-next-story.md`): 
  - Identifies next story number
  - Gathers from multiple architecture docs
  - Extracts relevant technical details
  - Manages source references
  - Runs validation checklist
- **Template** (`story-tmpl.yaml`):
  - Defines story structure
  - Sets section ownership
  - Provides basic field instructions

#### **PRD Creation (Simple Task + Complex Template)**
- **Task** (`create-doc.md`):
  - Generic template processor
  - Handles elicitation flow
- **Template** (`prd-tmpl.yaml`):
  - Extensive instructions per section
  - Examples for each field
  - Conditional sections
  - Detailed elicitation requirements

#### **Brainstorming (Process Task + Output Template)**
- **Task** (`facilitate-brainstorming-session.md`):
  - Interactive facilitation process
  - Technique selection and execution
  - Data capture during session
- **Template** (`brainstorming-output-tmpl.yaml`):
  - Output structure only
  - No instructions (non-interactive)
  - Just formatting for results

### Best Practices

1. **Keep templates focused on content**: Templates should describe WHAT goes in each section, not HOW to get it
2. **Keep tasks focused on process**: Tasks should describe HOW to gather/process information, not WHAT the final content should look like
3. **Use elicitation in templates**: When user input is needed for content decisions
4. **Use tasks for orchestration**: When multiple sources or complex logic is involved
5. **Avoid duplication**: Don't repeat instructions between task and template
6. **Consider reusability**: Generic tasks (create-doc) can work with many templates
7. **Document the division**: Make it clear why instructions are where they are

### The Key Principle

**Templates define the destination (what the document should contain), while tasks define the journey (how to create it).**

This separation enables:
- Template reuse across different tasks
- Task reuse across different templates  
- Clear separation of concerns
- Easier maintenance and updates
- Flexible composition of capabilities

## Key Insights

1. **Templates are contracts**: They define the structure all documents must follow
2. **Tasks are processors**: They execute template-based document creation
3. **Agents are users**: They invoke tasks with specific templates
4. **Elicitation is sacred**: User interaction points cannot be bypassed
5. **Sections are flexible**: Support various types and behaviors
6. **Workflow modes matter**: Interactive vs non-interactive changes behavior
7. **Variables enable reuse**: Same template, different contexts (including config references)
8. **Hierarchy provides organization**: Nested sections create logical structure
9. **Conditions enable adaptability**: Templates adjust to project needs
10. **Integration is seamless**: Templates work with entire BMad ecosystem
11. **Output formats vary**: Most use markdown, but qa-gate uses YAML
12. **Agent configuration**: Story template includes agent_config for section editability
11. **Instructions have homes**: Template instructions for "what", task instructions for "how"