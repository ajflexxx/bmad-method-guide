# BMad Glossary - Terms and Definitions

## Core Components

### Agent
**Definition**: An AI persona with a specific professional role in the software development lifecycle.  
**Location**: `/workspace/bmad-core/agents/`  
**Example**: The PM agent (Mary) acts as a Product Manager creating PRDs.  
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
**Example**: `template: "{root}/templates/prd-template.yaml"`

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
**Examples**: Game development, DevOps, creative writing.

### Bundle
**Definition**: Configuration file grouping agents and workflows for distribution.  
**Location**: Agent team YAML files.  
**Contains**: Agent lists, workflow references, descriptions.

### Context Engineering
**Definition**: Practice of embedding full implementation context directly in story files.  
**Purpose**: Eliminate context loss between planning and development.  
**Result**: Dev agents have complete understanding without external references.