# BMad Philosophy and System Architecture

## Part 1: BMad Philosophy & Principles

### Core Philosophy

BMad (Breakthrough Method of Agile AI-Driven Development) represents a fundamental shift in how AI agents collaborate to deliver software. Rather than treating AI as a single monolithic assistant, BMad orchestrates specialized AI personas that mirror real-world development teams.

### Key Principles

#### 1. **Specialization Over Generalization** (Observed Pattern)
Each agent embodies deep expertise in their domain. A Product Manager agent thinks and acts like a PM, not a generic AI trying to play multiple roles. This specialization pattern is evident throughout the agent implementations.

#### 2. **Context-Engineered Development**
BMad eliminates context loss - the biggest problem in AI-assisted development. Stories contain complete implementation details, architectural guidance, and full context embedded directly in the files.

#### 3. **Human-in-the-Loop Refinement**
While agents are autonomous, humans remain in control at key decision points. The system respects human expertise while amplifying productivity.

#### 4. **Structured Creativity**
Templates and checklists provide structure without stifling innovation. They ensure consistency while allowing flexibility in implementation.

#### 5. **Advisory Quality**
Quality assurance has evolved from blocking gates to advisory recommendations. The Test Architect provides comprehensive quality insights, but teams decide their quality bar. This preserves team autonomy while ensuring informed decision-making.

#### 6. **Lazy Resource Loading**
Components never pre-load resources. They load only what's needed when commanded, keeping the system efficient and focused.

**Critical Implementation Detail**: This principle is especially important for development agents, which must be kept lean to maximize context available for coding tasks. Dev agents should have minimal dependencies and load resources dynamically only when executing specific tasks.

**Resource Loading Pattern**:
```
Agent Activation → Command Issued → Task Loaded → Resources Loaded → Execution
```

**Important Exceptions**:
- All agents automatically load `bmad-core/core-config.yaml` before greeting to understand project structure
- The dev agent additionally loads files specified in `devLoadAlwaysFiles` from core-config (coding standards, tech stack, source tree)

This ensures agents don't carry unnecessary baggage that would consume valuable context space needed for code analysis and generation, while still having essential configuration and standards available.

#### 7. **Progressive Disclosure**
Components start minimal with deep customization available when needed. Templates have minimal defaults with extensive optional fields documented in examples. This pattern reduces cognitive load while maintaining flexibility.

#### 8. **Runtime Binding**
Decisions and bindings happen at execution time, not definition time. Tasks use command definitions and runtime discovery instead of static frontmatter references. This enables dynamic adaptation to context.

#### 9. **Configuration-First Architecture**
Critical system behaviors are configuration-driven, not hardcoded. Paths, modes, and behaviors can be customized through configuration files rather than code changes. This makes the system adaptable without modification.

#### 10. **Context Minimalism**
Every design decision optimizes for smaller context windows. Tasks can load specific sections of data files, not entire documents. This granular loading keeps agents focused and efficient.

#### 11. **Human-Friendly Abstractions**
The system uses abstractions that are intuitive for humans:
- **Personification**: Agents have names (James, John, Sarah) not just roles, making them memorable
- **Fuzzy Matching**: Commands support approximate input, reducing cognitive burden
- **Pragmatic Shortcuts**: YOLO mode for experienced users who want to skip interactions

#### 12. **Embedded Intelligence**
Components contain sophisticated embedded instructions, not just data. Checklists aren't simple lists but complex LLM instruction sets. Templates guide generation through detailed instructions. This distributes intelligence throughout the system.

### Design Philosophy Summary

These principles work together to create a system that is:
- **Efficient**: Minimal context usage through lazy loading and granular access
- **Flexible**: Configuration-driven with runtime adaptation
- **Approachable**: Human-friendly with progressive complexity
- **Intelligent**: Embedded instructions throughout components
- **Extensible**: Patterns that naturally support expansion packs

### BMad Method's Two Key Innovations

**1. Agentic Planning:** Dedicated agents (Analyst, PM, Architect) collaborate to create detailed, consistent PRDs and Architecture documents through advanced prompt engineering and human-in-the-loop refinement.

**2. Context-Engineered Development:** The Scrum Master agent transforms these detailed plans into hyper-detailed development stories that contain everything the Dev agent needs - full context, implementation details, and architectural guidance embedded directly in story files.

This two-phase approach eliminates both planning inconsistency and context loss - the biggest problems in AI-assisted development.

## Part 2: System Architecture Overview

### BMAD Directory Structure

```
bmad-method/
├── bmad-core/           # Core framework components
│   ├── agents/          # Agent persona definitions (.md files with YAML headers)
│   ├── tasks/           # Executable procedures (.md files)
│   ├── templates/       # Document templates (.yaml files)
│   ├── checklists/      # Quality validation (.md files)
│   ├── data/            # Knowledge resources (.md files)
│   ├── workflows/       # Orchestration sequences (.yaml files)
│   ├── agent-teams/     # Bundle configurations (.yaml files)
│   └── core-config.yaml # Central configuration registry
├── common/              # Shared utilities and helpers
│   ├── utils/           # Utility files (bmad-doc-template.md)
│   └── tasks/           # Common tasks (create-doc.md)
├── expansion-packs/     # Domain-specific extensions
├── dist/                # Pre-built bundles for web UI
│   └── teams/           # Team bundle .txt files
└── tools/               # Build and utility scripts
    └── builders/        # Bundle creation tools (web-builder.js)
```

This structure shows where each component type lives and their file formats, essential for expansion pack developers.

### Component Architecture & Interactions

The BMad system is built on a layered architecture where each component has a specific role but all work together through the orchestration layer.

### Dual Environment Architecture

BMad employs a sophisticated dual environment approach that enables operation in both web interfaces and IDE environments:

#### Web UI Environment
- **Purpose**: Cost-effective planning and documentation creation
- **Architecture**: Pre-built bundles from `dist/teams/` containing all agent dependencies
- **Bundle Creation**: Uses `web-builder.js` tool to recursively resolve dependencies and create single-file uploads
- **Context Advantage**: Large context windows (Gemini's 1M tokens) for comprehensive document creation
- **Resource Distribution**: All resources bundled into single text files with clear separators

#### IDE Environment  
- **Purpose**: Development implementation and file operations
- **Architecture**: Direct access to individual agent files with dynamic resource loading
- **Resource Access**: Agents load dependencies on-demand using `{root}/{type}/{name}` pattern
- **File Integration**: Real-time project file operations and context
- **Optimization**: Lean agents to maximize coding context

**Critical Design Decision**: The same agents work in both environments through different resource packaging methods, maintaining behavioral consistency while optimizing for each environment's strengths.

### Core Components

**Note**: All components are designed to work seamlessly across both web and IDE environments through the dual packaging system.

#### 1. AGENTS - The Personas
Agents are markdown files with embedded YAML headers that:
- Define role, style, identity, and core principles in YAML frontmatter
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

**Fuzzy Matching Pattern** (bmad-orchestrator): Agents use intelligent request resolution with 85% confidence threshold:
- User input is matched against available commands and tasks
- Example: "draft story" → matches "*create" → executes "create-next-story"  
- Shows numbered list of options when confidence is below 85%
- Reduces cognitive burden by accepting approximate commands

**ACTIVATION-NOTICE Pattern**: Agents begin with a structured activation sequence:
- **ACTIVATION-NOTICE**: Signals file contains complete agent configuration
- **CRITICAL**: Instructions to read and adopt the persona from YAML
- **COMPLETE AGENT DEFINITION FOLLOWS**: Confirms no external files needed
- **IDE-FILE-RESOLUTION**: 
  - Marked "FOR LATER USE ONLY - NOT FOR ACTIVATION"
  - Defines how to resolve dependencies when executing commands
  - Maps `{root}/{type}/{name}` pattern (e.g., `tasks/create-doc.md`)
  - Files loaded only when user requests specific command execution
- **REQUEST-RESOLUTION**: Defines fuzzy matching for user requests
- **activation-instructions**: Step-by-step persona adoption process

This pattern ensures agents operate correctly in both bundled web and IDE environments.

#### 2. TASKS - The Executable Procedures
Tasks are markdown files with step-by-step instructions that:
- Reference templates through command definitions in agent files or runtime template discovery
- Define output locations through configuration
- Can be interactive (with user elicitation) or automated
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

**Template Processing System**: BMad employs a sophisticated three-component template system:

1. **Template Format** (`common/utils/bmad-doc-template.md`): Defines markup language for YAML template processing
2. **Document Creation** (`common/tasks/create-doc.md`): Orchestrates template selection and user interaction
3. **Advanced Elicitation** (`bmad-core/tasks/advanced-elicitation.md`): Provides interactive refinement through structured brainstorming

This system enables transformation of structured YAML specifications into rich, contextual markdown documents through AI-guided processes.

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

#### 4. CHECKLISTS - The Quality Advisors
Checklists are markdown files with:
- LLM instructions in `[[LLM: ]]` blocks
- Conditional sections (`[[GREENFIELD ONLY]]`, `[[BROWNFIELD ONLY]]`)
- Validation criteria as checkbox items
- Execution modes (interactive vs comprehensive)
- Skip logic based on project context

**Quality Advisory Model**: All quality checks are advisory rather than blocking:
- Test Architect provides comprehensive quality analysis
- Teams decide whether to address or waive recommendations  
- Preserves team autonomy while ensuring informed decisions
- Quality gates serve as checkpoints, not barriers

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

**Workflow Conditions**: Control flow with conditional execution:
```yaml
- agent: [agent-id]
  condition: [condition-name]  # Optional - when to execute this step
  optional: true               # Often paired with conditions
  notes: "Action to take if condition is true"
```

**How Conditions Work**:
- Conditions are boolean flags evaluated at runtime
- If condition is present and false, the step is skipped
- If condition is true or absent, the step executes
- Conditions can trigger loops back to previous agents
- Common patterns:
  - Review cycles (e.g., `po_checklist_issues` → return to fix issues)
  - Optional enhancements (e.g., `user_wants_story_review` → optional review)
  - Architectural decisions (e.g., `architecture_changes_needed` → create architecture doc)
  - Completion events (e.g., `epic_complete` → run retrospective)
  - Routing decisions (e.g., `based_on_classification` → choose path)

**Creating Custom Conditions**:
- Name conditions descriptively (e.g., `needs_technical_review`)
- Document what triggers the condition in the `notes` field
- Consider pairing with `optional: true` for skippable steps
- Conditions enable dynamic, context-aware workflow execution

### System Architecture Details

#### Core Configuration Hub (`core-config.yaml`)
- Central configuration that defines project paths and settings
- Controls document locations (PRD, architecture, stories)
- Specifies sharding behavior and file patterns
- Acts as the "registry" that all components reference

#### Document Sharding System

BMad includes a sophisticated sharding system for managing large documents:

**Purpose**: Break large PRDs and Architecture documents into manageable chunks to optimize context usage

**Configuration** (`core-config.yaml`):
```yaml
markdownExploder: true              # Enable automatic sharding with md-tree
prd:
  prdSharded: true                  # Use sharded version
  prdShardedLocation: docs/prd      # Location of shards
  epicFilePattern: epic-{n}*.md    # Pattern for epic files
architecture:
  architectureSharded: true         # Use sharded version
  architectureShardedLocation: docs/architecture
```

**Method 1: Automatic Sharding with markdown-tree**

When `markdownExploder: true`, the system uses the `@kayvan/markdown-tree-parser` npm package:

1. **Installation**:
   ```bash
   npm install -g @kayvan/markdown-tree-parser
   ```

2. **Execution**:
   ```bash
   md-tree explode docs/prd.md docs/prd
   md-tree explode docs/architecture.md docs/architecture
   ```

3. **Process**:
   - Parses the markdown AST (Abstract Syntax Tree)
   - Identifies all level 2 headers (`##`) as split points
   - Creates separate files for each section
   - Generates an `index.md` with links to all shards
   - Preserves all formatting, code blocks, tables, and diagrams
   - Maintains heading hierarchy within each shard

**Method 2: Manual Sharding (Fallback)**

When `markdownExploder: false` or the tool is unavailable, agents use the `shard-doc` task:

1. **Parsing Phase**:
   - Read the entire document into memory
   - Identify section boundaries by regex pattern `/^##\s+/`
   - Track parent headers (level 1) for context
   - Preserve frontmatter and metadata

2. **Splitting Logic**:
   - Each level 2 header becomes a new file
   - File naming follows the pattern from config (e.g., `epic-{n}-{slug}.md`)
   - Slugs are generated from header text (lowercase, hyphenated)
   - Special handling for appendices and glossaries

3. **Content Preservation**:
   - Code blocks with triple backticks maintained intact
   - Mermaid diagrams preserved with proper fencing
   - Tables keep their alignment and formatting
   - Links updated to reference new shard locations
   - Images and attachments paths adjusted

4. **Index Generation**:
   - Creates a master `index.md` file
   - Builds a nested table of contents
   - Links to each shard with relative paths
   - Includes document metadata and overview

**Sharding Benefits**:
- Agents load only relevant sections (e.g., specific epic)
- Reduces context consumption by 70-90%
- Enables parallel work on different sections
- Maintains document integrity through index files
- Allows granular updates without touching entire document

**File Structure After Sharding**:
```
docs/prd/
├── index.md                    # Master table of contents
├── overview.md                 # Document introduction
├── epic-1-user-auth.md        # Epic 1: User Authentication
├── epic-2-payment.md          # Epic 2: Payment Processing
├── epic-3-reporting.md        # Epic 3: Reporting Dashboard
├── appendix-a-glossary.md     # Glossary appendix
└── appendix-b-references.md   # References appendix
```

**Agent Shard Loading**:
- SM agent loads specific epic when creating stories
- Dev agent loads only the current story's epic context
- Architect loads relevant technical sections
- Dynamic loading based on task requirements

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
- **qa**: Test Architect - Quality advisory and test architecture recommendations
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
Quality advisory and validation guidance:
- Used by agents (especially Test Architect) to assess deliverables
- Provide recommendations without blocking progress
- Teams decide whether to address concerns or waive them
- Ensure completeness and consistency across documents

#### Data Resources (`data/`)
Knowledge base and reference materials:
- `bmad-kb.md`: Core methodology knowledge
- Brainstorming techniques, elicitation methods
- `technical-preferences.md`: Persistent technical profile for team preferences (stack, patterns, services)

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
They ensure consistency across projects while allowing flexibility. Templates guide document creation, while checklists validate quality as advisory checkpoints - teams choose their quality bar.

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

## Expansion Pack Integration

Expansion packs extend BMad with domain-specific capabilities:

**Structure**: Expansion packs follow the same component architecture:
```
expansion-packs/[domain]/
├── agents/         # Domain-specific agent personas
├── tasks/          # Specialized task procedures
├── templates/      # Domain templates
├── workflows/      # Domain-specific orchestration sequences
├── checklists/     # Domain validation rules
├── data/           # Domain knowledge base
└── agent-teams/    # Domain team configurations
```

**Integration Pattern**:
1. Agents extend core agents by adding domain dependencies
2. Tasks reference domain-specific templates and data
3. Workflows create domain-specific sequences (standalone, not extending core)
4. Agent teams bundle domain components for specific use cases
5. Web bundles include expansion pack components automatically via dependency resolution

**Creating Expansion Packs**:
- Mirror the complete core directory structure
- Use consistent naming conventions (domain prefix)
- Declare dependencies explicitly in YAML headers
- Create self-contained workflows for domain processes
- Document domain-specific commands and patterns
- Test with both web and IDE environments

**Example Expansion Pack Workflow**:
```yaml
workflow:
  id: [domain]-[process]
  name: Domain-Specific Process
  type: greenfield  # or brownfield
  sequence:
    - agent: [domain]-analyst
      creates: domain-requirements.md
    - agent: architect  # Can use core agents
      uses: [domain]-architecture-tmpl
```

See [31-bmad-expansion-guide.md](31-bmad-expansion-guide.md) for detailed expansion pack development.

## Bundle Resolution Process

The web-builder.js tool creates single-file bundles through recursive dependency resolution:

**Resolution Algorithm**:
1. **Entry Point**: Start with agent or team definition
2. **Parse Dependencies**: Extract tasks, templates, checklists, data from YAML headers
3. **Recursive Scan**: For each dependency, parse its dependencies
4. **Deduplication**: Track processed files to avoid duplicates
5. **Concatenation**: Combine all content with clear separators

**Bundle Format**:
```
FILE: path/to/component.md
=====================================
[component content]
=====================================
FILE: path/to/dependency.yaml
=====================================
[dependency content]
```

**Resolution Rules**:
- `{root}` resolves to bmad-core/ or expansion-pack/[domain]/
- Wildcards (`*`) in team files include all agents in directory
- Missing dependencies logged but don't fail build
- Circular dependencies detected and prevented
- Comments and metadata preserved

**Build Command**:
```bash
node tools/builders/web-builder.js \
  --agent bmad-core/agents/[agent].md \
  --output dist/agents/[agent].txt
```

This process ensures all required components are available in web environments where file system access isn't possible.

## See Also

- [10-bmad-agents-reference.md](10-bmad-agents-reference.md) - Deep dive into agent system
- [14-bmad-workflows-reference.md](14-bmad-workflows-reference.md) - Workflow orchestration details
- [31-bmad-expansion-guide.md](31-bmad-expansion-guide.md) - Creating expansion packs
- [01-bmad-overview.md](01-bmad-overview.md) - System overview and navigation