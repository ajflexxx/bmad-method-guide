# BMad Philosophy and System Architecture

## Part 1: BMad Philosophy & Principles

### Core Philosophy

BMad (Breakthrough Method of Agile AI-Driven Development) represents a fundamental shift in how AI agents collaborate to deliver software. Rather than treating AI as a single monolithic assistant, BMad orchestrates specialized AI personas that mirror real-world development teams.

### Key Principles

#### 1. **Specialization Over Generalization**
Each agent embodies deep expertise in their domain. A Product Manager agent thinks and acts like a PM, not a generic AI trying to play multiple roles.

#### 2. **Context Engineering**
BMad eliminates context loss - the biggest problem in AI-assisted development. Stories contain complete implementation details, architectural guidance, and full context embedded directly in the files.

#### 3. **Human-in-the-Loop Refinement**
While agents are autonomous, humans remain in control at key decision points. The system respects human expertise while amplifying productivity.

#### 4. **Structured Creativity**
Templates and checklists provide structure without stifling innovation. They ensure consistency while allowing flexibility in implementation.

#### 5. **Lazy Resource Loading**
Components never pre-load resources. They load only what's needed when commanded, keeping the system efficient and focused.

### The Two-Phase Innovation

**Phase 1 - Agentic Planning:** Dedicated agents (Analyst, PM, Architect) collaborate to create detailed, consistent PRDs and Architecture documents through advanced prompt engineering and human refinement.

**Phase 2 - Context-Engineered Development:** The Scrum Master transforms plans into hyper-detailed development stories that contain everything the Dev agent needs - eliminating both planning inconsistency and context loss.

## Part 2: System Architecture Overview

### Component Architecture & Interactions

The BMad system is built on a layered architecture where each component has a specific role but all work together through the orchestration layer.

### Core Components

#### 1. AGENTS - The Personas
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

#### 2. TASKS - The Executable Procedures
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

#### 3. TEMPLATES - The Document Structures
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

#### 4. CHECKLISTS - The Quality Gates
Checklists are markdown files with:
- LLM instructions in `[[LLM: ]]` blocks
- Conditional sections (`[[GREENFIELD ONLY]]`, `[[BROWNFIELD ONLY]]`)
- Validation criteria as checkbox items
- Execution modes (interactive vs comprehensive)
- Skip logic based on project context

#### 5. DATA - The Knowledge Resources
Data files contain reference information:
- Techniques and methodologies
- Best practices and preferences
- Domain knowledge
- Can be loaded by tasks/agents when needed

#### 6. WORKFLOWS - The Orchestration Scripts
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

### System Architecture Details

#### Core Configuration Hub (`core-config.yaml`)
- Central configuration that defines project paths and settings
- Controls document locations (PRD, architecture, stories)
- Specifies sharding behavior and file patterns
- Acts as the "registry" that all components reference

#### Agent System (`agents/`)
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

#### Workflow Engine (`workflows/`)
Workflows define end-to-end processes by orchestrating agents in sequence:
- Each workflow specifies agent order, required inputs, and expected outputs
- Greenfield workflows: For new projects (fullstack, service, UI)
- Brownfield workflows: For existing codebases
- Workflows reference agents, tasks, and templates to create a complete pipeline

#### Task Library (`tasks/`)
Reusable task definitions that agents execute:
- Tasks contain step-by-step instructions
- Can be interactive (requiring user input) or automated
- Reference templates and checklists
- Examples: `create-next-story`, `facilitate-brainstorming-session`, `review-story`

#### Template System (`templates/`)
Structured document templates ensure consistency:
- Define document formats (YAML-based)
- Include sections, prompts, and validation rules
- Used by agents to create standardized outputs (PRDs, architecture docs, stories)

#### Agent Teams (`agent-teams/`)
Bundle configurations grouping agents and workflows:
- Define which agents work together
- Specify available workflows for different project types
- Example: `team-fullstack` includes all agents needed for full-stack development

#### Checklists (`checklists/`)
Quality control and validation steps:
- Used by agents (especially PO) to validate deliverables
- Ensure completeness and consistency across documents

#### Data Resources (`data/`)
Knowledge base and reference materials:
- `bmad-kb.md`: Core methodology knowledge
- Brainstorming techniques, elicitation methods
- Technical preferences and best practices

## Connection Flow Patterns

### Command Execution Flow:
User issues command to agent → Agent loads task → Task loads template → Template guides document creation

### Validation Flow:
Agent creates document → PO agent loads checklist → Checklist validates against requirements → Issues tracked for resolution

### Data Reference Flow:
Task needs domain knowledge → Loads data file → Applies techniques/preferences → Incorporates into output

### Workflow Orchestration:
Workflow activated → Agents execute in sequence → Each creates/modifies artifacts → Next agent uses previous outputs

## Complete Integration Flow

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

## Design Decisions & Rationale

### Why Multiple Specialized Agents?
Rather than one generalist AI, specialized agents bring domain expertise. A PM agent understands product thinking, while a Dev agent focuses on implementation details. This mirrors successful human teams.

### Why YAML Configuration?
YAML provides human-readable configuration that's easy to modify and extend. It allows non-programmers to create and customize agents while maintaining structure.

### Why Markdown for Tasks?
Markdown is universally readable, version-controllable, and allows rich formatting. Tasks can include code blocks, lists, and structured content while remaining accessible.

### Why Templates and Checklists?
They ensure consistency across projects while allowing flexibility. Templates guide document creation, while checklists validate quality - together they maintain high standards without rigidity.

### Why Lazy Loading?
Loading resources only when needed keeps the system fast and focused. Agents don't carry unnecessary baggage, making them more efficient and easier to debug.

## Key Integration Points

- **File Resolution**: Always use `{root}/{type}/{name}` pattern
- **Command Prefix**: All commands use `*` prefix
- **Lazy Loading**: Never pre-load resources
- **Task Priority**: Task instructions override agent defaults
- **Elicitation Respect**: Never skip interactive sections
- **Output Consistency**: Use templates for standardized outputs

## Best Practices

1. **Modularity**: Each component should be self-contained but composable
2. **Clear Dependencies**: Explicitly declare all required files
3. **Documentation**: Include clear instructions in each component
4. **Validation**: Create checklists for quality assurance
5. **Flexibility**: Support both interactive and automated modes
6. **Error Handling**: Include validation and skip conditions

## See Also

- [10-bmad-agents-reference.md](10-bmad-agents-reference.md) - Deep dive into agent system
- [14-bmad-workflows-reference.md](14-bmad-workflows-reference.md) - Workflow orchestration details
- [31-bmad-expansion-guide.md](31-bmad-expansion-guide.md) - Creating expansion packs
- [01-bmad-overview.md](01-bmad-overview.md) - System overview and navigation