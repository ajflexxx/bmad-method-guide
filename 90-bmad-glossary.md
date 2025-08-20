# BMad Glossary - Terms and Definitions

## Core Components

### Agent
**Definition**: An AI persona with a specific professional role in the software development lifecycle.  
**Location**: `/workspace/bmad-core/agents/`  
**Example**: The PM agent (John) acts as a Product Manager creating PRDs.  
**Key Features**: Has identity, commands, dependencies, and behavioral patterns.

### Task
**Definition**: An executable procedure written in markdown that agents run to perform specific actions.  
**Location**: `/workspace/bmad-core/tasks/` and `/workspace/common/tasks/`  
**Example**: `create-prd.md` task guides PRD document creation.  
**Key Features**: Can be interactive, reference templates, specify output locations.

### Template
**Definition**: A YAML-based document schema that defines structured, repeatable document formats.  
**Location**: `/workspace/bmad-core/templates/`  
**Example**: `prd-template.yaml` defines sections for a Product Requirements Document.  
**Key Features**: Sections with types, instructions, elicitation flags.

### Checklist
**Definition**: Validation criteria in markdown format used to ensure quality and completeness.  
**Location**: `/workspace/bmad-core/checklists/`  
**Example**: `po-master-checklist.md` validates PRD and architecture alignment.  
**Key Features**: LLM instructions, conditional sections, checkbox items.

### Workflow
**Definition**: A multi-agent orchestration sequence that coordinates agents to complete complex processes.  
**Location**: `/workspace/bmad-core/workflows/`  
**Example**: `greenfield-fullstack.yaml` orchestrates the full development lifecycle.  
**Key Features**: Agent sequences, input/output specifications, conditional logic.

### Agent Team
**Definition**: A bundled configuration grouping agents and workflows for specific project types.  
**Location**: `/workspace/bmad-core/agent-teams/`  
**Example**: `team-fullstack.yaml` includes all agents for full-stack development.  
**Key Features**: Agent lists, workflow references, capability descriptions.

### Data Resource
**Definition**: Knowledge files containing reference information, techniques, and best practices.  
**Location**: `/workspace/bmad-core/data/`  
**Example**: `elicitation-methods.md` contains various requirement gathering techniques.  
**Key Features**: Domain knowledge, methodologies, preferences.

## Agent Names (Personification)

**Mary** - Business Analyst (analyst.md)  
**John** - Product Manager (pm.md)  
**Winston** - Architect (architect.md)  
**Sarah** - Product Owner (po.md)  
**Bob** - Scrum Master (sm.md)  
**James** - Full Stack Developer (dev.md)  
**Quinn** - Test Architect & Quality Advisor (qa.md)  
**Sally** - UX Expert (ux-expert.md)

## Special Agents

### Orchestrator
**Definition**: Master coordinator agent that can transform into any specialized agent.  
**Types**: BMad-Orchestrator (transforms) and BMad-Master (direct execution).  
**Purpose**: Central control point for agent activation and task coordination.

### BMad-Master
**Definition**: Direct task executor that runs commands without persona transformation.  
**Difference from Orchestrator**: Executes tasks directly rather than becoming other agents.

## Configuration

### Core-config
**Definition**: Central configuration file defining project paths, settings, and behaviors.  
**Location**: `/workspace/bmad-core/core-config.yaml`  
**Purpose**: Registry that all components reference for file locations and settings.

### Sharding
**Definition**: Process of splitting large documents into smaller, manageable pieces.  
**Usage**: PRDs and Architecture docs are sharded into epics and components.  
**Example**: `prdSharded: true` in core-config enables PRD sharding.

## Patterns and Mechanisms

### IDE-FILE-RESOLUTION
**Definition**: File loading pattern used by agents to locate resources.  
**Format**: `{root}/{type}/{name}` where type is the folder (tasks, templates, etc.).  
**Example**: `{root}/tasks/create-prd.md`

### Commands
**Definition**: Actions available to agents, always prefixed with asterisk (*).  
**Example**: `*create-prd` command triggers PRD creation task.  
**Usage**: Users invoke commands to make agents perform tasks.

### Dependencies
**Definition**: Resource references declared by agents for tasks, templates, checklists, and data.  
**Purpose**: Explicitly declare all files an agent needs to function.  
**Location**: In agent YAML under `dependencies:` section.

### Elicitation
**Definition**: Process of gathering user input through structured questions.  
**Types**: Basic (simple prompts) or Advanced (sophisticated techniques).  
**Example**: `elicit: true` in templates triggers user input gathering.

### Advanced Elicitation
**Definition**: Sophisticated elicitation system using multiple techniques from data resources.  
**Reference**: `advanced-elicitation.md` task.  
**Features**: Presents 9 relevant methods like "Critique and Refine", "Tree of Thoughts".

## Document Types

### PRD (Product Requirements Document)
**Definition**: Comprehensive document defining product features, requirements, and specifications.  
**Created By**: PM agent using `prd-template.yaml`.  
**Contains**: Functional requirements, non-functional requirements, epics, stories.

### Architecture Document
**Definition**: Technical specification defining system design, components, and implementation.  
**Created By**: Architect agent using `architecture-template.yaml`.  
**Contains**: System design, technology stack, component specifications.

### Story
**Definition**: Detailed development task with context, requirements, and implementation details.  
**Created By**: Scrum Master agent from sharded epics.  
**Purpose**: Provides complete context to Dev agent for implementation.

### Epic
**Definition**: Large feature or capability broken down into multiple stories.  
**Source**: Defined in PRD, sharded for processing.  
**Usage**: Scrum Master converts epics into implementable stories.

## Execution Concepts

### Execute-checklist Task
**Definition**: Central task that processes all checklist executions.  
**Location**: `/workspace/common/tasks/execute-checklist.md`  
**Purpose**: Ensures consistent checklist processing and validation.

### Greenfield
**Definition**: New project development starting from scratch.  
**Workflows**: `greenfield-fullstack.yaml`, `greenfield-service.yaml`.  
**Characteristics**: No existing codebase, full planning phase.

### Brownfield
**Definition**: Development within an existing codebase.  
**Workflows**: `brownfield-fullstack.yaml`, `brownfield-service.yaml`.  
**Characteristics**: Existing code, architecture constraints, incremental changes.

## Workflow States

### Planning Phase
**Definition**: Initial phase where requirements and architecture are defined.  
**Agents**: Analyst, PM, Architect, UX Expert, PO.  
**Output**: PRD and Architecture documents.

### Development Cycle
**Definition**: Iterative phase where stories are implemented.  
**Agents**: Scrum Master, Developer, QA.  
**Process**: SM creates story → Dev implements → QA reviews.

### Document Alignment
**Definition**: State where PRD and Architecture documents are consistent.  
**Verified By**: PO agent using master checklist.  
**Required For**: Proceeding to development phase.

## File Patterns

### Frontmatter
**Definition**: YAML metadata at the beginning of markdown files.  
**Usage**: Tasks use frontmatter to specify templates and output locations.  
**Example**: `template: '{root}/templates/brainstorming-output-tmpl.yaml'`

### LLM Instructions
**Definition**: Special blocks in checklists providing context to AI.  
**Format**: `[[LLM: instructions here]]`  
**Purpose**: Guide AI understanding of checklist intent.

### Conditional Sections
**Definition**: Checklist sections that apply only in specific contexts.  
**Examples**: `[[GREENFIELD ONLY]]`, `[[BROWNFIELD ONLY]]`  
**Purpose**: Adapt validation to project type.

## Integration Terms

### Expansion Pack
**Definition**: Domain-specific bundle of agents, tasks, templates, and workflows.  
**Purpose**: Extend BMad to new domains beyond software development.  
**Examples**: Game development, DevOps, creative writing, Google Cloud AI systems.

### Bundle
**Definition**: Configuration file grouping agents and workflows for distribution.  
**Location**: Agent team YAML files.  
**Contains**: Agent lists, workflow references, descriptions.

### Context-Engineered Development
**Definition**: BMAD's approach of embedding full implementation context directly in story files.  
**Source**: Core innovation described in README.md.  
**Purpose**: Eliminate context loss between planning and development.  
**Result**: Dev agents have complete understanding without external references.

## Quality & Testing Terms

### Test Architect
**Definition**: Evolution of QA role focused on advisory quality recommendations.  
**Name**: Quinn  
**File**: qa.md (agent file is named qa.md, not test-architect.md)  
**Philosophy**: Teams choose their quality bar based on Test Architect recommendations.  
**Authority**: Advisory only, not blocking.

### Quality Gate
**Definition**: Advisory checkpoint documenting quality status and recommendations.  
**Statuses**: PASS, CONCERNS, FAIL, WAIVED.  
**Location**: `docs/qa/gates/{epic}.{story}-{slug}.yml`  
**Purpose**: Inform teams without blocking progress.

### Risk Profile
**Definition**: Comprehensive assessment of project risks using probability × impact.  
**Categories**: TECH, SEC, PERF, DATA, BUS, OPS.  
**Scoring**: 1-16 scale determining testing depth.  
**Usage**: Guides test strategy and resource allocation.

### Requirements Traceability
**Definition**: Systematic mapping between requirements and test cases.  
**Format**: Given-When-Then documentation (not code).  
**Coverage Levels**: FULL, PARTIAL, NONE, NOT_TESTABLE.  
**Purpose**: Ensure all requirements have appropriate test coverage.


### Waiver
**Definition**: Documented decision to proceed despite quality concerns.  
**Contains**: Reason, approver, risk acceptance, follow-up plan.  
**Location**: Within quality gate YAML files.  
**Purpose**: Transparent risk acceptance and accountability.

### Risk-Based Testing
**Definition**: Testing strategy where depth is proportional to risk.  
**Formula**: Risk = Probability × Impact.  
**Application**: High risk = comprehensive testing, Low risk = basic testing.  
**Benefits**: Optimal resource allocation and focus.

### Dual Publishing
**Definition**: BMAD method dual tag strategy for stable and beta releases.  
**Stable**: `npx bmad-method@stable install` - production ready.  
**Default**: `npx bmad-method install` - latest stable (same as @stable).  
**Beta**: `npx bmad-method@beta install` - bleeding edge features.  
**Current Version**: v4.39.1 (as of verification)  
**Purpose**: Balance stability with innovation.

### CLAUDE.md
**Definition**: AI assistant configuration file for development guidelines.  
**Contains**: Markdown rules, conventions, essential commands.  
**Location**: Project root directory.  
**Status**: File is .gitignored in BMAD framework (not part of distribution).  
**Purpose**: Standardize AI assistant behavior across projects when manually created.

### Expansion Pack Examples
**Definition**: Domain-specific BMAD extensions currently available.  
**Available**: Game development (Phaser 2D, Unity 2D), creative writing, infrastructure/DevOps.  
**Structure**: Self-contained with agents, tasks, templates, workflows, and checklists.  
**Installation**: Manual directory copying from expansion-packs/ folder.

### Test Levels Framework
**Definition**: Decision matrix for selecting appropriate test types.  
**Levels**: Unit (70%), Integration (25%), E2E (5%).  
**Criteria**: Based on component type and risk assessment.  
**Documentation**: `test-levels-framework.md` in data resources.

### YOLO Mode
**Definition**: Fast-track execution mode that processes all sections at once.  
**Usage**: User types `#yolo` in certain tasks to skip interactive processing.  
**Available In**: execute-checklist, create-doc, correct-course tasks.  
**Purpose**: Speeds up execution for experienced users who don't need step-by-step guidance.  
**Source**: Found in `/workspace/bmad-method/common/tasks/execute-checklist.md`, `create-doc.md`, and `bmad-core/tasks/correct-course.md`.

### NFR Assessment
**Definition**: Validation of non-functional requirements.  
**Categories**: Performance, Security, Reliability, Usability, Scalability.  
**Task**: `nfr-assess.md`  
**Output**: Metrics, status, and recommendations for each category.