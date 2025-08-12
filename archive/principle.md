# How BMad Components Work Together - Expansion Pack Guide

## Component Architecture & Interactions

### 1. AGENTS - The Personas
Agents are YAML-configured prompt personas that:
- Define role, style, identity, and core principles
- List available commands (prefixed with *)
- Declare dependencies (tasks, templates, checklists, data)
- Use `IDE-FILE-RESOLUTION` pattern to locate files: `{root}/{type}/{name}`
- Never pre-load resources - only load when commanded

**Key Structure:**
```yaml
agent:
  name: [Agent Name]
  id: [unique-id]
  title: [Professional Title]
  icon: [emoji]
  whenToUse: [usage context]
persona:
  role: [primary role]
  style: [behavior style]
  identity: [self-description]
  core_principles: [list of principles]
commands:
  - [command-name]: [description/action]
dependencies:
  tasks: [list of task files]
  templates: [list of template files]
  checklists: [list of checklist files]
  data: [list of data files]
```

### 2. TASKS - The Executable Procedures
Tasks are markdown files with step-by-step instructions that:
- Can reference templates via frontmatter (`template: "{root}/templates/xxx.yaml"`)
- Define output locations (`docOutputLocation: docs/xxx.md`)
- Can be interactive (`elicit: true`) or automated
- Follow sequential execution patterns
- Can load and use other resources dynamically

**Task Patterns:**
- **Interactive Tasks**: Gather user input through structured questions
- **Processing Tasks**: Transform data or create documents
- **Validation Tasks**: Check quality and completeness
- **Integration Tasks**: Connect multiple components

### 3. TEMPLATES - The Document Structures
Templates are YAML files defining document schemas with:
- Metadata (id, name, version, output format)
- Workflow configuration (interactive/automated)
- Section definitions with types:
  - `text`, `paragraphs`, `bullet-list`, `numbered-list`
  - `table`, `choice`, `template-text`
- Instructions for each section
- Elicitation flags for user input
- Owner/editor permissions

**Template Structure:**
```yaml
template:
  id: [unique-id]
  name: [template name]
  output:
    format: markdown
    filename: [output path]
sections:
  - id: [section-id]
    title: [section title]
    type: [section type]
    instruction: [guidance for filling]
    elicit: [true/false]
```

### 4. CHECKLISTS - The Quality Gates
Checklists are markdown files with:
- LLM instructions in `[[LLM: ]]` blocks
- Conditional sections (`[[GREENFIELD ONLY]]`, `[[BROWNFIELD ONLY]]`)
- Validation criteria as checkbox items
- Execution modes (interactive vs comprehensive)
- Skip logic based on project context

### 5. DATA - The Knowledge Resources
Data files contain reference information:
- Techniques and methodologies
- Best practices and preferences
- Domain knowledge
- Can be loaded by tasks/agents when needed

### 6. WORKFLOWS - The Orchestration Scripts
Workflows YAML files define multi-agent sequences:
```yaml
workflow:
  id: [workflow-id]
  sequence:
    - agent: [agent-id]
      creates: [output file]
      requires: [input files]
      condition: [optional condition]
      notes: [execution notes]
```

## Connection Flow Patterns

### Command Execution Flow:
User issues command to agent → Agent loads task → Task loads template → Template guides document creation

### Validation Flow:
Agent creates document → PO agent loads checklist → Checklist validates against requirements → Issues tracked for resolution

### Data Reference Flow:
Task needs domain knowledge → Loads data file → Applies techniques/preferences → Incorporates into output

### Workflow Orchestration:
Workflow activated → Agents execute in sequence → Each creates/modifies artifacts → Next agent uses previous outputs

## Creating an Expansion Pack

### Essential Components:

#### 1. Define Your Domain
- Identify the specialized area (e.g., DevOps, Security, Data Science)
- Map required roles and responsibilities

#### 2. Create Agents (`agents/`)
- One orchestrator agent for coordination
- Specialist agents for domain roles
- Follow the YAML structure with proper dependencies

#### 3. Design Tasks (`tasks/`)
- Break down complex procedures into steps
- Reference templates for structured outputs
- Include elicitation for user input where needed
- Use frontmatter for template/output configuration

#### 4. Build Templates (`templates/`)
- Create YAML schemas for domain documents
- Define sections with appropriate types
- Include instructions and examples
- Set elicitation flags for interactive sections

#### 5. Write Checklists (`checklists/`)
- Quality gates for your domain
- Include LLM instructions for context
- Use conditional sections for variations
- Reference specific validation criteria

#### 6. Compile Data Resources (`data/`)
- Domain-specific techniques
- Best practices and standards
- Reference materials agents can load

#### 7. Orchestrate Workflows (`workflows/`)
- Define end-to-end processes
- Sequence agents appropriately
- Specify inputs/outputs between steps
- Include conditional branches

#### 8. Bundle Configuration (`agent-teams/`)
```yaml
bundle:
  name: [Pack Name]
  icon: [emoji]
  description: [what it does]
agents: [list of agent files]
workflows: [list of workflow files]
```

## Key Integration Points:

- **File Resolution**: Always use `{root}/{type}/{name}` pattern
- **Command Prefix**: All commands use `*` prefix
- **Lazy Loading**: Never pre-load resources
- **Task Priority**: Task instructions override agent defaults
- **Elicitation Respect**: Never skip interactive sections
- **Output Consistency**: Use templates for standardized outputs

## Best Practices:

1. **Modularity**: Each component should be self-contained but composable
2. **Clear Dependencies**: Explicitly declare all required files
3. **Documentation**: Include clear instructions in each component
4. **Validation**: Create checklists for quality assurance
5. **Flexibility**: Support both interactive and automated modes
6. **Error Handling**: Include validation and skip conditions

## System Architecture Summary

The bmad-core folder contains an AI-powered software development orchestration system with these key components:

### Core Configuration Hub (`core-config.yaml`)
- Central configuration that defines project paths and settings
- Controls document locations (PRD, architecture, stories)
- Specifies sharding behavior and file patterns
- Acts as the "registry" that all components reference

### Agent System (`agents/`)
Two primary orchestrator agents control the system:
- **BMad-Orchestrator**: Master coordinator that can transform into any specialized agent
- **BMad-Master**: Direct task executor without persona transformation

Specialized agents represent different roles:
- **analyst**: Requirements gathering and project analysis
- **pm**: Product management and PRD creation
- **architect**: Technical architecture design
- **ux-expert**: UI/UX specifications
- **po**: Product owner validation
- **dev**: Development implementation
- **qa**: Quality assurance
- **sm**: Scrum master

### Complete Integration Flow

1. **User** activates an orchestrator (BMad-Master or BMad-Orchestrator)
2. **Orchestrator** reads core-config.yaml to understand project structure
3. **User** selects a workflow or specific agent/task
4. **Workflow** coordinates multiple agents in sequence
5. **Agents** execute tasks using templates and checklists
6. **Tasks** reference core-config for file locations and patterns
7. **Templates** provide structure for document creation
8. **Checklists** validate outputs at each stage
9. **Data** resources provide domain knowledge when needed

This creates a sophisticated prompt-based agent system where each component has a specific role but all work together through the orchestration layer to manage complex software development processes.

Your expansion pack should follow this architecture to integrate seamlessly with the BMad system while adding specialized capabilities for your domain.