# Deep Dive: Workflows in BMad - Universal Orchestration Blueprints

> **Important Note**: This document includes a [Critical Clarifications](#critical-clarifications-how-workflows-really-work) section at the bottom that corrects common misconceptions about workflow functionality. Please review it for an accurate understanding of how workflows actually work in BMad.

## Overview

Workflows in BMad are orchestration blueprints that coordinate multiple AI agents through complex, multi-step processes in any domain. While BMad's core workflows focus on software development, the workflow architecture is fundamentally domain-agnostic—capable of orchestrating educational content creation, creative writing processes, game development, business analysis, or any field requiring coordinated agent collaboration.

### Core Principles

- **Domain Independence**: Workflow patterns (planning → creation → review) apply universally across domains
- **Agent Orchestration**: Coordinates specialized agents
- **Conditional Logic**: Supports branching, routing, and decision trees
- **Artifact Flow**: Manages document/artifact creation and handoffs
- **Quality Gates**: Implements validation checkpoints

### What Makes Workflows Unique

Unlike individual agents (domain experts) or tasks (specific procedures), workflows are:

- **Process Definitions**: Complete end-to-end processes with multiple phases
- **Multi-Agent Coordinators**: Orchestrate teams of specialized agents
- **Orchestration Blueprints**: Guide progress, dependencies, and decision points
- **Reusable Patterns**: Templates that translate across different domains
- **Bundle Components**: Included in agent-teams for distribution

### Workflow Structure at a Glance

Every BMad workflow is a single YAML file with all sections nested under the `workflow` key:

- **workflow** - Top-level container for all workflow content
  - **id** - Unique identifier
  - **name** - Human-readable title
  - **description** - Purpose and use cases
  - **type** - Category (greenfield/brownfield)
  - **project_types** - Applicable contexts
  - **sequence** - Ordered execution steps (nested under workflow)
    - agent specifications
    - artifact definitions
    - conditional logic
    - routing decisions
  - **flow_diagram** - Mermaid visualization (nested under workflow)
  - **decision_guidance** - Selection criteria (nested under workflow)
  - **handoff_prompts** - Agent communication templates (nested under workflow)

## Workflow Anatomy

### Workflow Block

The top-level `workflow` block defines the workflow's identity and metadata.

**Purpose**: Establishes workflow identity, categorization, and applicability

**Requirement**: Required

- When to include: Always - every workflow must have this block
- When NOT to include: Never - this is mandatory

**Format**:

```yaml
workflow:
  id: unique-identifier
  name: Human-Readable Workflow Name
  description: >-
    Multi-line description explaining the workflow's purpose,
    use cases, and what it orchestrates
  type: greenfield | brownfield
  project_types:
    - type-1
    - type-2
    - type-3

  # All following sections are nested under workflow:
  sequence:
    - ...

  flow_diagram: |
    ...

  decision_guidance: ...

  handoff_prompts: ...
```

**Examples from actual workflows**:

From `greenfield-fullstack.yaml`:

```yaml
workflow:
  id: greenfield-fullstack
  name: Greenfield Full-Stack Application Development
  description: >-
    Agent workflow for building full-stack applications from concept to development.
    Supports both comprehensive planning for complex projects and rapid prototyping for simple ones.
  type: greenfield
  project_types:
    - web-app
    - saas
    - enterprise-app
    - prototype
    - mvp
```

**Best Practices**:

- Use descriptive, domain-appropriate ids (e.g., `course-creation`, `book-writing`)
- Write clear descriptions that explain the complete process
- Choose type based on starting point: greenfield (new) vs brownfield (existing)
- List all applicable contexts in project_types

**Domain Adaptation**:

- **Software**: greenfield = new app, brownfield = existing codebase
- **Education**: greenfield = new course, brownfield = course revision
- **Writing**: greenfield = new book, brownfield = manuscript editing
- **Game Dev**: greenfield = new game, brownfield = DLC/expansion

### Sequence Array

The `sequence` array defines the ordered execution of workflow steps.

**Purpose**: Orchestrates the complete process flow with agents, artifacts, and logic

**Requirement**: Required

- When to include: Always - this is the core of the workflow
- When NOT to include: Never - workflows without sequences cannot execute

**Format**:

```yaml
sequence:
  - agent: agent-name
    creates: artifact-name.md
    requires: previous-artifact.md  # Can be a single string
    # OR array for multiple dependencies:
    requires:
      - artifact-1.md
      - artifact-2.md
    optional_steps:
      - optional-action-1
      - optional-action-2
    optional: true  # Mark entire step as optional
    notes: "Human-readable guidance for this step"
    condition: when-to-execute
    repeats: loop-condition

  - step: special-step-name
    action: what-to-do
    condition: execution-condition
    routes:
      route_name:
        agent: agent-for-route
        uses: task-or-template
```

**Examples from actual workflows**:

From `greenfield-fullstack.yaml`:

```yaml
sequence:
  - agent: analyst
    creates: project-brief.md
    optional_steps: # These are descriptive labels for human guidance, not task file IDs
      - brainstorming_session # Relates to task: facilitate-brainstorming-session.md
      - market_research_prompt # Relates to task: create-deep-research-prompt.md
    notes: "Can do brainstorming first, then optional deep research before creating project brief. SAVE OUTPUT: Copy final project-brief.md to your project's docs/ folder."

  - agent: pm
    creates: prd.md
    requires: project-brief.md
    notes: "Creates PRD from project brief using prd-tmpl. SAVE OUTPUT: Copy final prd.md to your project's docs/ folder."
```

From `brownfield-fullstack.yaml` (routing example):

```yaml
- step: routing_decision
  condition: based_on_classification
  routes:
    single_story:
      agent: pm
      uses: brownfield-create-story
      notes: "Create single story for immediate implementation. Exit workflow after story creation."
    small_feature:
      agent: pm
      uses: brownfield-create-epic
      notes: "Create focused epic with 1-3 stories. Exit workflow after epic creation."
    major_enhancement:
      continue: to_next_step
      notes: "Continue with comprehensive planning workflow below."
```

**Best Practices**:

- Order steps logically from planning to execution
- Specify clear artifact names for creates/requires
- Use conditions for branching logic (as guidance, not executable code)
- Include helpful notes for human operators
- Group related steps together
- Remember optional_steps entries are descriptive labels, not direct file references

**Domain Adaptation**:

- Replace software artifacts with domain-appropriate outputs
- Adapt agent roles to domain experts
- Maintain the pattern of: gather → plan → create → validate → iterate

### Special Step Types

Workflows support special step types beyond basic agent execution.

**Purpose**: Enable conditional logic, routing decisions, and process control through guidance

**Requirement**: Optional

- When to include: When workflow needs branching, validation, or complex logic
- When NOT to include: Simple linear workflows don't need special steps

**Important**: Conditions and routing logic in workflows are guidance for the orchestrator and human operators, not executable code. There is no runtime engine that automatically evaluates YAML conditions. The orchestrator and agents interpret these patterns contextually during workflow execution.

**Format**:

```yaml
# Classification step
- step: classification_name
  agent: analyzing-agent
  action: classify or analyze
  notes: "Classification criteria"

# Routing decision
- step: routing_decision
  condition: based_on_classification
  routes:
    route_1:
      agent: agent-name
      uses: task-name
      notes: "Route description"
    route_2:
      continue: to_next_step
      notes: "Continue main flow"

# Validation gate
- agent: validator
  validates: artifacts
  uses: po-master-checklist # Core checklist example
  condition: validation_required

# Repeat cycle
- step: repeat_cycle_name
  action: continue_for_all_items
  notes: "Repeat criteria"
```

**Examples from actual workflows**:

From `brownfield-fullstack.yaml`:

```yaml
- step: enhancement_classification
  agent: analyst
  action: classify enhancement scope
  notes: |
    Determine enhancement complexity to route to appropriate path:
    - Single story (< 4 hours) → Use brownfield-create-story task
    - Small feature (1-3 stories) → Use brownfield-create-epic task  
    - Major enhancement (multiple epics) → Continue with full workflow

    Ask user: "Can you describe the enhancement scope? Is this a small fix, a feature addition, or a major enhancement requiring architectural changes?"

- step: routing_decision
  condition: based_on_classification
  routes:
    single_story:
      agent: pm
      uses: brownfield-create-story
      notes: "Create single story for immediate implementation. Exit workflow after story creation."
    small_feature:
      agent: pm
      uses: brownfield-create-epic
      notes: "Create focused epic with 1-3 stories. Exit workflow after epic creation."
    major_enhancement:
      continue: to_next_step
      notes: "Continue with comprehensive planning workflow below."
```

**Best Practices**:

- Use classification steps to assess scope/complexity
- Implement routing for efficiency (avoid over-processing)
- Add validation gates at critical points
- Use repeat cycles for iterative processes
- Write conditions as clear guidance for human/orchestrator interpretation

**Domain Adaptation**:

- **Education**: Route based on course complexity (micro-learning vs full curriculum)
- **Writing**: Route based on content type (blog post vs book chapter)
- **Game Dev**: Route based on feature size (bug fix vs new game mode)

### Flow Diagram

Mermaid diagram providing visual representation of the workflow.

**Purpose**: Visual aid for human comprehension of workflow structure and decision points

**Requirement**: Recommended

- When to include: Always, unless workflow is trivially simple
- When NOT to include: Only for very basic linear workflows

**Note**: Flow diagrams are descriptive documentation only. BMad does not parse or execute Mermaid diagrams. They serve as visual guides for humans to understand the workflow's intended flow and are not processed programmatically.

**Format**:

````yaml
flow_diagram: |
  ```mermaid
  graph TD
      A[Start] --> B[First Step]
      B --> C{Decision?}
      C -->|Yes| D[Path 1]
      C -->|No| E[Path 2]
      
      style A fill:#color
````

**Examples from actual workflows**:

From `greenfield-fullstack.yaml` (excerpt):

````yaml
flow_diagram: |
  ```mermaid
  graph TD
      A[Start: Greenfield Project] --> B[analyst: project-brief.md]
      B --> C[pm: prd.md]
      C --> D[ux-expert: front-end-spec.md]
      D --> D2{Generate v0 prompt?}
      D2 -->|Yes| D3[ux-expert: create v0 prompt]
      D2 -->|No| E[architect: fullstack-architecture.md]
````

**Best Practices**:

- Use clear, descriptive node labels
- Show decision points with diamond shapes
- Color-code by phase or agent type
- Keep diagram readable (avoid overcrowding)

### Decision Guidance

Criteria for when to use this workflow.

**Purpose**: Helps users select the appropriate workflow for their needs

**Requirement**: Highly Recommended (strong convention)

- When to include: Always include for consistency with core workflows
- When NOT to include: Only omit if workflow usage is completely obvious

**Note**: While not programmatically enforced, all core workflows include decision_guidance as a best practice. This helps the orchestrator provide better workflow selection guidance to users.

**Format**:

```yaml
decision_guidance:
  when_to_use:
    - Criterion 1
    - Criterion 2
    - Criterion 3
```

**Examples from actual workflows**:

From `greenfield-fullstack.yaml`:

```yaml
decision_guidance:
  when_to_use:
    - Building production-ready applications
    - Multiple team members will be involved
    - Complex feature requirements
    - Need comprehensive documentation
    - Long-term maintenance expected
    - Enterprise or customer-facing applications
```

**Best Practices**:

- List concrete, measurable criteria
- Order from most to least important
- Include scope/complexity indicators
- Mention team size considerations

**Domain Adaptation**:

- Translate criteria to domain-specific indicators
- Focus on project scope and complexity markers relevant to the domain

### Handoff Prompts

Templates for agent-to-agent communication and context passing.

**Purpose**: Ensures smooth context transfer between workflow phases

**Requirement**: Recommended

- When to include: When agents need specific context from previous steps
- When NOT to include: Simple workflows with obvious handoffs

**Format**:

```yaml
handoff_prompts:
  from_to: "Message template"
  from_to_conditional: |
    Multi-line template with
    {{variable}} substitution
    {{if condition}}: Conditional content
```

**Important Note**: The `{{variable}}` and `{{if condition}}` syntax shown in handoff prompts is descriptive guidance for humans and agents, not runtime-evaluated templates. There is no templating engine in BMad core. These patterns help communicate intent and structure to the orchestrator and agents who will interpret them contextually during workflow execution.

**Examples from actual workflows**:

From `greenfield-fullstack.yaml`:

```yaml
handoff_prompts:
  analyst_to_pm: "Project brief is complete. Save it as docs/project-brief.md in your project, then create the PRD."
  pm_to_ux: "PRD is ready. Save it as docs/prd.md in your project, then create the UI/UX specification."
  architect_to_pm: "Please update the PRD with the suggested story changes, then re-export the complete prd.md to docs/."
  complete: "All planning artifacts validated and saved in docs/ folder. Move to IDE environment to begin development."
```

From `brownfield-fullstack.yaml`:

```yaml
handoff_prompts:
  po_to_sm: |
    All artifacts validated. 
    Documentation type available: {{sharded_prd / brownfield_docs}}
    {{if sharded}}: Use standard create-next-story task.
    {{if brownfield}}: Use create-brownfield-story task to handle varied documentation formats.
```

**Best Practices**:

- Include artifact locations and naming conventions
- Preserve key decisions and findings
- Use conditional templates for branching workflows
- Keep messages concise but complete

**Domain Adaptation**:

- Replace development terms with domain equivalents
- Adjust artifact types (e.g., "lesson plan" instead of "PRD")
- Maintain clarity about what's being passed forward

## Workflow Types and Domain Translation

### Understanding Greenfield vs Brownfield

BMad's fundamental workflow categorization translates universally across domains:

**Greenfield** (Starting from scratch):

- Software: New application
- Education: New course or curriculum
- Writing: New book or publication
- Game Dev: New game or IP
- Business: New product or service

**Brownfield** (Enhancing existing work):

- Software: Adding features to existing code
- Education: Updating existing course materials
- Writing: Revising or expanding published work
- Game Dev: DLC, expansions, or patches
- Business: Improving existing offerings

### Core Workflow Reference

| Workflow ID            | Type       | Purpose                           | Key Pattern                                       |
| ---------------------- | ---------- | --------------------------------- | ------------------------------------------------- |
| `greenfield-fullstack` | Greenfield | Complete application from concept | Full planning → design → implementation           |
| `greenfield-service`   | Greenfield | Backend service/API creation      | Planning → architecture → implementation          |
| `greenfield-ui`        | Greenfield | Frontend application              | Planning → UX design → implementation             |
| `brownfield-fullstack` | Brownfield | Major feature additions           | Classification → scoped planning → implementation |
| `brownfield-service`   | Brownfield | Service enhancements              | Analysis → architecture → safe integration        |
| `brownfield-ui`        | Brownfield | UI/UX improvements                | Analysis → design alignment → implementation      |

### Domain Translation Examples

**Educational Content Workflow** (based on greenfield-fullstack):

- analyst → curriculum designer
- pm → course coordinator
- ux-expert → instructional designer
- architect → learning architect
- dev → content creator
- qa → educational reviewer

**Creative Writing Workflow** (based on greenfield-fullstack):

- analyst → market researcher
- pm → editor/publisher
- ux-expert → reader experience designer
- architect → story architect
- dev → writer
- qa → copy editor

**Game Development Workflow** (based on greenfield-fullstack):

- analyst → market analyst
- pm → game producer
- ux-expert → UX/UI designer
- architect → technical director
- dev → game developer
- qa → game tester

## Universal Sequence Patterns

These patterns appear across BMad workflows and translate to any domain:

### Classification Routing Pattern

Assess scope/complexity to determine appropriate process path.

**Purpose**: Avoid over-engineering simple tasks while ensuring complex ones get proper treatment

**Requirement**: Recommended for brownfield/enhancement workflows

- When to include: When scope can vary significantly
- When NOT to include: When all work follows same process

**Format**:

```yaml
- step: classification_step
  agent: analyzing-agent
  action: classify scope
  notes: "Classification criteria"

- step: routing_decision
  condition: based_on_classification
  routes:
    small: [lightweight process]
    medium: [standard process]
    large: [comprehensive process]
```

**Examples from actual workflows**:

From `brownfield-fullstack.yaml`:

```yaml
- step: enhancement_classification
  agent: analyst
  action: classify enhancement scope
  notes: |
    Determine enhancement complexity to route to appropriate path:
    - Single story (< 4 hours) → Use brownfield-create-story task
    - Small feature (1-3 stories) → Use brownfield-create-epic task  
    - Major enhancement (multiple epics) → Continue with full workflow
```

**Domain Adaptations**:

- **Education**: Micro-lesson vs module vs full course
- **Writing**: Blog post vs article vs book chapter
- **Game Dev**: Bug fix vs feature vs major update

### Validation Gate Pattern

Quality checkpoint ensuring all artifacts meet standards before proceeding.

**Purpose**: Prevent downstream issues by validating completeness and consistency

**Requirement**: Highly recommended

- When to include: After planning phases, before execution
- When NOT to include: Only in very simple, low-risk workflows

**Format**:

```yaml
- agent: validator
  validates: all_artifacts
  uses: po-master-checklist # Actual checklist from core

- agent: various
  updates: flagged_items
  condition: validation_issues
```

**Examples from actual workflows**:

From all core workflows:

```yaml
- agent: po
  validates: all_artifacts
  uses: po-master-checklist
  notes: "Validates all documents for consistency and completeness. May require updates to any document."

- agent: various
  updates: any_flagged_documents
  condition: po_checklist_issues
  notes: "If PO finds issues, return to relevant agent to fix and re-export updated documents to docs/ folder."
```

**Domain Adaptations**:

- **Education**: Curriculum review board
- **Writing**: Editorial review
- **Game Dev**: Design review committee

### Iterative Development Cycle

Repeating pattern of creation, implementation, and review.

**Purpose**: Break large efforts into manageable, reviewable chunks

**Requirement**: Required for multi-part deliverables

- When to include: When output has multiple components
- When NOT to include: Single, atomic deliverables

**Format**:

```yaml
- agent: planner
  creates: unit-plan
  repeats: for_each_component

- agent: creator
  action: implement_unit
  requires: unit-plan
  creates: implementation

- agent: reviewer
  validates: implementation
  optional: true

- step: repeat_cycle
  action: continue_for_all_units
```

**Examples from actual workflows**:

From all development workflows:

```yaml
- agent: sm
  action: create_story
  creates: story.md
  repeats: for_each_epic

- agent: dev
  action: implement_story
  creates: implementation_files

- agent: qa
  action: review_implementation
  optional: true
```

**Domain Adaptations**:

- **Education**: Lesson planning → content creation → peer review
- **Writing**: Chapter outline → draft → edit
- **Game Dev**: Feature spec → implementation → playtesting

### Document Sharding Pattern

Breaking large documents into focused sections for processing.

**Purpose**: Manage cognitive load and context limitations

**Requirement**: Recommended for large artifacts

- When to include: When artifacts exceed single-context processing
- When NOT to include: Small, focused documents

**Format**:

```yaml
- agent: organizer
  action: shard_documents
  creates: sharded_sections
  requires: complete_artifacts
```

**Domain Adaptations**:

- **Education**: Breaking curriculum into modules
- **Writing**: Splitting manuscript into chapters
- **Game Dev**: Dividing GDD into feature specs

## Pattern Library for Universal Workflows

These high-level patterns transcend specific domains:

### Planning → Creation → Review → Iteration

The fundamental creative process pattern.

**Conceptual Pattern** (not valid YAML structure):

```yaml
# This illustrates the pattern, not actual workflow syntax
sequence:
  - phase: planning
    agents: [researcher, strategist, designer]
    outputs: [requirements, specifications, designs]

  - phase: creation
    agents: [creators, builders, developers]
    outputs: [implementations, drafts, prototypes]

  - phase: review
    agents: [reviewers, testers, editors]
    outputs: [feedback, corrections, approvals]

  - phase: iteration
    condition: not_complete
    returns_to: creation
```

**Valid Workflow Implementation**:

```yaml
sequence:
  - agent: researcher
    creates: requirements.md

  - agent: creator
    creates: implementation/
    requires: requirements.md

  - agent: reviewer
    validates: implementation/
    uses: story-dod-checklist
```

**Domain Examples**:

- **Software**: Requirements → Code → Testing → Refactor
- **Education**: Learning objectives → Content → Assessment → Revision
- **Writing**: Outline → Draft → Edit → Rewrite
- **Game Dev**: Design → Prototype → Playtest → Iterate

### Research → Design → Implementation → Validation

The engineering/construction pattern.

**Conceptual Pattern** (not valid YAML structure):

```yaml
sequence:
  - phase: research
    activities: [analysis, investigation, discovery]

  - phase: design
    activities: [architecture, planning, specification]

  - phase: implementation
    activities: [building, creating, executing]

  - phase: validation
    activities: [testing, verification, acceptance]
```

**Universal Application**:

- Applies to any domain requiring systematic approach
- Ensures solid foundation before execution
- Validates results meet requirements

### Ideation → Prototyping → Refinement → Delivery

The innovation/creative pattern.

**Conceptual Pattern** (not valid YAML structure):

```yaml
sequence:
  - phase: ideation
    tools: [brainstorming, exploration, concepting]

  - phase: prototyping
    tools: [rapid-creation, proof-of-concept, mvp]

  - phase: refinement
    tools: [iteration, polish, optimization]

  - phase: delivery
    tools: [packaging, distribution, deployment]
```

Note: See the earlier “Valid Workflow Implementation” mapping example for how to translate conceptual patterns into valid workflow YAML using core keys (agent, creates, requires, validates, action).

**Cross-Domain Value**:

- Encourages experimentation
- Allows for pivots and adjustments
- Focuses on iterative improvement

## Creating Domain-Specific Workflows

### Step 1: Analyze Your Domain

Identify the key phases, roles, and artifacts in your domain:

**Questions to Answer**:

- What are the major phases of work in your domain?
- Who are the key roles/experts involved?
- What artifacts/documents are created?
- What are the quality checkpoints?
- What variations in scope exist?

**Example: Online Course Creation**

```yaml
# Domain Analysis
Phases: Research → Design → Development → Review → Launch
Roles: Instructor, Instructional Designer, Video Producer, Reviewer
Artifacts: Course outline, Lesson plans, Video scripts, Assessments
Checkpoints: Curriculum review, Content review, Technical review
Scope: Micro-learning (< 1hr) vs Full course (8+ hrs)
```

### Step 2: Map to Workflow Structure

Translate your domain analysis to BMad workflow format:

```yaml
workflow:
  id: course-creation
  name: Online Course Development
  description: >-
    Orchestrates the creation of online educational content
    from concept through launch readiness
  type: greenfield # New course
  project_types:
    - micro-learning
    - full-course
    - certification-prep
```

### Step 3: Define Your Sequence

Create the sequence with domain-appropriate agents and artifacts:

```yaml
sequence:
  - agent: education-analyst
    creates: learning-objectives.md
    optional_steps:
      - learner-research
      - competitive-analysis
    notes: "Define target audience and learning outcomes"

  - agent: instructional-designer
    creates: course-outline.md
    requires: learning-objectives.md
    notes: "Structure course with modules and lessons"

  - agent: content-creator
    creates: lesson-content/
    requires: course-outline.md
    repeats: for_each_module
    notes: "Develop content for each module"
```

### Step 4: Add Domain-Specific Patterns

Incorporate patterns that make sense for your domain:

```yaml
# Classification for course complexity
- step: complexity_assessment
  agent: instructional-designer
  action: assess course scope
  notes: |
    Determine course complexity:
    - Micro-learning (< 1 hour) → Simplified workflow
    - Standard course (1-8 hours) → Standard workflow
    - Comprehensive (8+ hours) → Full workflow with reviews

# Domain-specific validation
- agent: curriculum-reviewer
  validates: all_course_materials
  uses: educational-standards-checklist
  notes: "Ensure alignment with learning objectives"
```

### Step 5: Create Handoff Prompts

Design clear context passing for your domain:

```yaml
handoff_prompts:
  analyst_to_designer: |
    Learning objectives complete. Target audience: {{audience}}.
    Create course outline with {{module_count}} modules.

  designer_to_creator: |
    Course structure ready. Begin content development.
    Focus on {{learning_style}} approach.

  creator_to_reviewer: |
    Module {{module_number}} complete.
    Please review for accuracy and engagement.
```

### Step 6: Test and Iterate

**Validation Checklist**:

- ☑ Does the workflow cover all essential phases?
- ☑ Are handoffs clear and complete?
- ☑ Do conditional paths make sense?
- ☑ Are artifacts well-defined?
- ☑ Is the flow logical and efficient?

### Common Pitfalls to Avoid

1. **Over-complexity**: Start simple, add complexity as needed
2. **Missing handoffs**: Ensure context flows between agents
3. **Unclear artifacts**: Define what each step produces
4. **No validation**: Include quality checkpoints
5. **Rigid structure**: Allow for optional steps and variations

## Integration Points

### Agent-Team Integration

Workflows are distributed through agent-team bundles.

**Purpose**: Package workflows with their required agents for distribution

**Requirement**: Required for workflow distribution

- When to include: Always include workflows in team definitions
- When NOT to include: Never - workflows need teams for distribution

**Format**:

```yaml
# In agent-team YAML
bundle:
  name: Team Name
  icon: 🎯 # Optional emoji icon
  description: Team purpose
agents:
  - agent-1
  - agent-2
workflows:
  - workflow-1.yaml
  - workflow-2.yaml
```

**Examples from actual teams**:

From `team-all.yaml`:

```yaml
bundle:
  name: Team All
  icon: 👥
  description: Includes every core system agent.
agents:
  - bmad-orchestrator
  - "*"
workflows:
  - brownfield-fullstack.yaml
  - brownfield-service.yaml
  - brownfield-ui.yaml
  - greenfield-fullstack.yaml
  - greenfield-service.yaml
  - greenfield-ui.yaml
```

**Best Practices**:

- Include all workflows relevant to the team's purpose
- Ensure all required agents are in the bundle
- Use descriptive bundle names and descriptions

### Orchestrator Integration

The bmad-orchestrator discovers and manages workflows.

**Discovery Process**:

1. Orchestrator reads team bundle on activation
2. Loads workflow list from bundle definition
3. Provides workflow commands to user
4. Loads specific workflow when selected

**Orchestrator Commands** (User-facing with \* prefix):

```markdown
*workflow [name] .... Start specific workflow
*workflow-guidance .. Get help selecting workflow
*plan ............... Create detailed workflow plan
*plan-status ........ Show workflow progress
```

**Utility Commands** (Internal documentation with / prefix):

```yaml
# From workflow-management.md utility (for bundle builders):
/workflows - List workflows in current bundle
/workflow-start {workflow-id} - Start workflow
/workflow-status - Show current progress
/workflow-resume - Resume from last position
```

**Important**: When documenting workflows for users, use the `*` prefix commands. The `/` prefix commands are internal utility documentation included in bundles for the orchestrator's reference, not for direct user interaction.

### Runtime Loading

Workflows are loaded dynamically when needed.

**Loading Sequence**:

1. User requests workflow (via orchestrator or direct)
2. System reads workflow YAML from bundle
3. Parses structure and sequences
4. Begins execution from first step
5. Maintains state through execution

**State Management** (Conversational/Session-based):

- Current step tracking (within session)
- Completed artifacts list (during workflow execution)
- Decision history (for current workflow instance)
- Agent handoff context (preserved during session)

**Note**: State is maintained conversationally during workflow execution, not persisted across sessions in BMad core. The orchestrator and agents track progress through the workflow_state concept during active use.

**Domain Considerations**:

- Custom workflows load the same way as core
- Expansion packs can override or extend workflows within their bundle context only
- Overrides apply when using the expansion team bundle, not system-wide
- Multiple workflows can be available in one bundle

**Scope Note**: When an expansion pack includes a workflow with the same ID as a core workflow, the expansion version takes precedence ONLY within that expansion's team bundle. This allows domain-specific variations without affecting core BMad or other expansion packs.

## Workflow Examples: From Software to Other Domains

### Example 1: Educational Content Workflow

Based on greenfield-fullstack pattern, adapted for course creation:

```yaml
workflow:
  id: course-development
  name: Comprehensive Course Creation
  description: >-
    Orchestrates creation of online educational content
    from learning objectives through launch-ready materials
  type: greenfield
  project_types:
    - online-course
    - workshop-series
    - certification-program

  sequence:
    - agent: education-analyst
      creates: learner-analysis.md
      optional_steps:
        - market-research
        - competitor-analysis
      notes: "Analyze target audience and learning needs"

    - agent: curriculum-designer
      creates: course-blueprint.md
      requires: learner-analysis.md
      notes: "Design curriculum structure and learning paths"

    - agent: instructional-designer
      creates: lesson-plans/
      requires: course-blueprint.md
      notes: "Create detailed lesson plans with activities"

    # Validation gate pattern
    - agent: education-reviewer
      validates: all_plans
      uses: curriculum-standards-checklist

    # Iterative creation pattern
    - agent: content-creator
      creates: course-materials/
      repeats: for_each_module
      notes: "Develop videos, slides, exercises"
```

### Example 2: Game Development Workflow

Adapting brownfield patterns for game expansions:

```yaml
workflow:
  id: game-dlc-development
  name: Game DLC/Expansion Development
  description: >-
    Orchestrates development of downloadable content
    for existing games
  type: brownfield
  project_types:
    - dlc-content
    - expansion-pack
    - seasonal-update

  sequence:
    # Classification routing pattern
    - step: content_classification
      agent: game-analyst
      action: classify DLC scope
      notes: |
        Determine content scope:
        - Cosmetic pack → Fast track
        - New levels → Standard workflow
        - Major expansion → Full workflow

    - step: routing_decision
      condition: based_on_scope
      routes:
        cosmetic:
          agent: artist
          uses: cosmetic-pack-template
        levels:
          agent: level-designer
          uses: level-pack-workflow
        expansion:
          continue: full_workflow
```

### Example 3: Creative Writing Workflow

Translating software patterns to book writing:

```yaml
workflow:
  id: novel-writing
  name: Novel Development Workflow
  description: >-
    Orchestrates novel creation from concept to
    publication-ready manuscript
  type: greenfield
  project_types:
    - fiction
    - non-fiction
    - anthology

  sequence:
    - agent: market-researcher
      creates: market-analysis.md
      notes: "Research genre trends and audience"

    - agent: story-architect
      creates: story-bible.md
      requires: market-analysis.md
      notes: "Create world-building and character docs"

    - agent: outline-designer
      creates: chapter-outline.md
      requires: story-bible.md
      notes: "Structure plot and chapter breakdown"

    # Iterative writing pattern
    - agent: writer
      creates: chapters/
      repeats: for_each_chapter

    - agent: editor
      validates: chapters/
      optional: true

    # Validation pattern
    - agent: beta-reader-coordinator
      validates: complete_manuscript
      uses: reader-feedback-process
```

## Best Practices for Workflow Creation

### Design Principles

1. **Start Simple**: Begin with linear flow, add complexity gradually
2. **Clear Artifacts**: Define explicit outputs for each step
3. **Meaningful Gates**: Add validation where quality matters
4. **Flexible Routing**: Use classification for variable scope
5. **Complete Handoffs**: Ensure context transfers between agents

### Workflow Patterns to Reuse

**From Core Workflows**:

- Classification routing (brownfield-fullstack)
- Optional steps (all workflows)
- Validation gates (all workflows using `po-master-checklist`)
- Iterative cycles (all workflows)
- Document sharding (all workflows)

### Using Real Task and Template IDs

When referencing tasks and templates, use actual core IDs:

- Templates: `prd-tmpl`, `brownfield-prd-tmpl`, `fullstack-architecture-tmpl`
- Tasks: `brownfield-create-epic`, `brownfield-create-story`, `shard-doc`
- Checklists: `po-master-checklist` (not generic "validation-checklist")

### Testing Your Workflow

**Validation Questions**:

1. Can users understand when to use this workflow?
2. Are all required agents defined and available?
3. Do artifacts flow logically between steps?
4. Are decision points clear and actionable?
5. Can the workflow recover from errors?

### Troubleshooting Path Issues

If workflows reference incorrect paths, check these keys in `core-config.yaml`:

- `prd.prdFile`: Location of PRD document (default: `docs/prd.md`)
- `prd.prdShardedLocation`: Sharded PRD folder (default: `docs/prd`)
- `architecture.architectureFile`: Architecture doc (default: `docs/architecture.md`)
- `architecture.architectureShardedLocation`: Sharded architecture (default: `docs/architecture`)
- `devStoryLocation`: Story files location (default: `docs/stories`)

### Common Anti-Patterns to Avoid

| Anti-Pattern       | Why It's Bad                 | Better Approach            |
| ------------------ | ---------------------------- | -------------------------- |
| Linear everything  | No flexibility for scope     | Add classification routing |
| Missing validation | Quality issues downstream    | Add validation gates       |
| Unclear handoffs   | Context loss between agents  | Explicit handoff prompts   |
| Over-engineering   | Too complex for simple tasks | Optional steps and routes  |
| No iteration       | Can't refine or improve      | Build in review cycles     |

## Troubleshooting Common Issues

| Issue                    | Cause                  | Solution                               |
| ------------------------ | ---------------------- | -------------------------------------- |
| Workflow won't start     | Missing agent-team     | Ensure team bundle includes workflow   |
| Agent activation fails   | Missing dependencies   | Check agent's required tasks/templates |
| Document not found       | Wrong path             | Verify core-config.yaml paths          |
| Validation keeps failing | Inconsistent documents | Review handoff prompts for context     |
| Stories not created      | Sharding failed        | Manually shard with shard-doc task     |

## Critical Clarifications: How Workflows Really Work

*This section contains important clarifications about workflow functionality based on deep exploration of the BMad codebase. These findings correct several misconceptions that may arise from earlier sections.*

### 1. Workflows Are Guides, Not Programs

**The Reality**: Workflows in BMad are instructional blueprints for humans and LLM agents, not executable programs.

- **No execution engine exists** - BMad has no runtime that processes workflow YAML files
- **No automatic progression** - The system cannot move from one workflow step to the next automatically
- **Human-driven orchestration** - Users manually coordinate the workflow progression
- **Agents read workflows as guidance** - When given a workflow, agents interpret it as instructions, not execute it as code

**Evidence**: No workflow execution engine found in `/workspace/bmad-method/tools/` or agent code. Workflows are loaded as reference documents, not parsed for execution.

### 2. The Truth About Workflow Execution

**How workflows actually get executed**:

1. **Start with the Orchestrator** (optional)
   ```
   User: "@bmad-orchestrator"
   User: "*workflow-guidance"
   [Orchestrator helps choose appropriate workflow]
   ```

2. **Manual Agent Switching**
   ```
   User: [New chat] "@analyst"
   User: "Create project brief for [project]"
   [Analyst works, creates artifact]
   User: [Save artifact manually]
   
   User: [New chat] "@pm"  
   User: "Create PRD from docs/project-brief.md"
   [PM works, creates artifact]
   User: [Save artifact manually]
   ```

3. **Key Points**:
   - Each agent interaction typically happens in a **new chat session**
   - Users must **manually save artifacts** between steps
   - **Context doesn't transfer automatically** between agents
   - Handoff prompts are **instructions for you**, not system commands
   - The workflow document is your **checklist**, not an automation script

### 3. Understanding the Type Field

**The `type: greenfield` or `type: brownfield` field is descriptive metadata, not a control parameter.**

**What actually happens**:
- The type field provides **context to agents** about the nature of the project
- **No code checks the type field** - searched entire codebase, found no type checking logic
 - Agents infer type from context, checklists, and template selection (e.g., `brownfield-prd-tmpl`), not enforced filenames; PRD may still be saved as `docs/prd.md` in brownfield workflows
- The type influences **which templates agents choose** through naming conventions

**How it works in practice** (`/workspace/bmad-method/bmad-core/checklists/po-master-checklist.md:10-18`):
```markdown
First, determine the project type by checking:
1. Is this a GREENFIELD project (new from scratch)?
   - Look for: New project initialization, no existing codebase references
2. Is this a BROWNFIELD project (enhancing existing system)?  
   - Look for: References to existing codebase, enhancement/modification language
```

Agents determine type by **analyzing context**, not by reading a type variable.

### 4. Which Agents Actually Work with Workflows

**Only two agents have workflow access**:

1. **BMad Orchestrator** (`/workspace/bmad-method/bmad-core/agents/bmad-orchestrator.md`)
   - Has workflow commands: `*workflow`, `*workflow-guidance`, `*plan`
   - Can read and explain workflows
   - Guides users through workflow selection
   - **Does NOT execute workflows automatically**

2. **BMad Master** (`/workspace/bmad-method/bmad-core/agents/bmad-master.md:103-109`)
   - Has workflows in dependencies
   - Can read workflow files if needed
   - No workflow-specific commands
   - Functions as a universal task executor

**All other agents** (PM, Architect, Dev, QA, etc.):
- Are **participants IN workflows**, not managers OF workflows
- Don't have workflows in their dependencies
- Execute their specialized steps when manually activated
- The "CRITICAL WORKFLOW RULE" in their configs refers to following task instructions, not managing workflows

### 5. The State Management Reality

**Workflows are completely stateless** - there is no mechanism to track workflow progress.

**Missing functionality**:
- The `*plan-status` and `*plan-update` commands listed in the Orchestrator **have no implementation**
- No workflow state files are created or maintained
- No database or storage mechanism for workflow progress
- State is lost when switching agents or chat sessions

**How progress is actually tracked**:
- **By document artifacts**: Presence of `prd.md`, `architecture.md` indicates completion
- **By folder structure**: `docs/prd/` (sharded) indicates development phase
- **By story status fields**: Individual stories track their own state (Draft → Done)
- **Manually by users**: Creating your own `workflow-status.md` file

**Returning to Orchestrator mid-workflow**:
```
User: "I'm following greenfield-fullstack. I've completed PRD and architecture. What's next?"
[Orchestrator reads workflow and tells you next step based on what you report]
```

### 6. Orchestrator Capabilities and Limitations

**What the Orchestrator CAN do**:
- Help select appropriate workflows via `*workflow-guidance`
- Explain workflow steps and structure
- Read workflow files and describe sequences
- Suggest which agent to use next (if you tell it where you are)

**What the Orchestrator CANNOT do**:
- Automatically detect your current workflow position
- Execute workflow steps for you
- Track progress across sessions
- Coordinate agents automatically (except in experimental party-mode)
- Know what artifacts you've created unless you tell it

**Working effectively with the Orchestrator**:
- Always provide context: "I'm at step X of workflow Y"
- Use it for guidance, not automation
- Treat it as a knowledgeable advisor, not a workflow engine

### 7. Important Corrections to Earlier Sections

These clarifications correct potentially misleading implications in the document:

**Conditions and Routing**:
- `condition:` fields are **LLM interpretation guides**, not executable logic
- No runtime evaluates `{{if}}` statements - they're templates for human/agent understanding
- Routing decisions happen through human choice, not automatic branching

**Flow Diagrams**:
- Mermaid diagrams are **visual documentation only**
- BMad doesn't parse or execute these diagrams
- They help humans understand flow, nothing more

**Template Syntax**:
- `{{variable}}` and `{{if condition}}` in handoffs are **descriptive patterns**
- No template engine processes these - agents interpret them contextually
- These show intent and structure, not runtime substitution

**Optional Steps**:
- `optional_steps:` entries are **descriptive labels for humans**
- They don't directly reference task files
- They guide human decision-making, not system execution

**State Management Claims**:
- References to "maintains state through execution" are **session-based only**
- No persistence mechanism exists in BMad core
- State tracking is conversational within a single agent session

### 8. Practical Workflow Execution Guide

**Step-by-Step Example: Executing Greenfield-Fullstack Workflow**

1. **Understand the workflow**:
   ```
   @bmad-orchestrator
   *workflow-guidance
   [Choose greenfield-fullstack]
   ```

2. **Create tracking document** (optional but recommended):
   ```markdown
   # workflow-status.md
   Workflow: greenfield-fullstack
   - [ ] Project brief (analyst)
   - [ ] PRD (pm)  
   - [ ] Architecture (architect)
   ```

3. **Execute each step manually**:
   ```
   [New Chat]
   @analyst
   "Create project brief for [describe project]"
   [Save output to docs/project-brief.md]
   [Check off in workflow-status.md]
   
   [New Chat]
   @pm
   "Create PRD from docs/project-brief.md"
   [Save output to docs/prd.md]
   [Check off in workflow-status.md]
   ```

4. **Handle interruptions**:
   - Your workflow-status.md shows where you stopped
   - When returning, tell the next agent about existing artifacts
   - Provide context from previous steps as needed

5. **Best Practices**:
   - Keep workflow YAML open as reference
   - Save all artifacts immediately after creation
   - Use git commits as workflow checkpoints
   - Start fresh chats to avoid context confusion
   - Follow handoff prompts as your checklist

**The Reality**: You are the workflow engine. BMad provides the instructions (workflows), the tools (agents), and the guidance (orchestrator), but you drive the entire process.

## Summary

Workflows are BMad's universal orchestration blueprints that coordinate multiple AI agents through complex processes in any domain. While core workflows focus on software development, the architecture is fundamentally domain-agnostic.

**Key Capabilities**:

- **Domain Independence**: Patterns translate across all fields
- **Intelligent Routing**: Adapt process to scope and complexity
- **Quality Gates**: Ensure standards through validation
- **Flexible Execution**: Optional steps and conditional paths
- **State Management**: Track progress and maintain context

**For Expansion Pack Developers**:

Workflows are your primary tool for orchestrating domain-specific processes. By understanding the patterns in BMad's core workflows, you can:

1. **Translate Patterns**: Apply software patterns to your domain
2. **Create Orchestrations**: Design multi-agent collaborations
3. **Ensure Quality**: Implement appropriate validation
4. **Handle Complexity**: Route based on scope assessment
5. **Maintain State**: Track progress through complex processes

**Universal Principles**:

Regardless of domain, effective workflows share these characteristics:

- Clear phase definitions (planning → execution → validation)
- Explicit artifact creation and dependencies
- Smooth context transfer between agents
- Appropriate validation checkpoints
- Flexibility for different scales of work

The workflow system embodies BMad's philosophy: Complex processes become manageable when broken into well-orchestrated steps, with specialized agents handling their areas of expertise while maintaining overall coherence through structured coordination.
