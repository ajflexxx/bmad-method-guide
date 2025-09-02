# Deep Dive: Agents in BMad - The AI Personas

## Overview

Agents in BMad are sophisticated AI personas that embody specific professional roles in software development. They are not just simple prompt templates - they are complete, self-contained systems with their own identities, capabilities, workflows, and resource dependencies.

## Agent Anatomy

### Core Structure

Every agent follows a standardized structure defined in a single markdown file:

````markdown
# agent-id

ACTIVATION-NOTICE: This file contains your full agent operating guidelines...

## COMPLETE AGENT DEFINITION FOLLOWS - NO EXTERNAL FILES NEEDED

```yaml
IDE-FILE-RESOLUTION: [file loading rules]
REQUEST-RESOLUTION: [fuzzy matching instructions]
activation-instructions: [step-by-step activation process]
agent: [identity metadata]
persona: [behavioral definition]
commands: [available actions]
dependencies: [resource references]
```
````

````

### 1. **Agent Identity Block**

```yaml
agent:
  name: Mary                    # Human name for the persona
  id: analyst                   # Unique identifier
  title: Business Analyst       # Professional title
  icon: 📊                     # Visual representation
  whenToUse: Use for market research, brainstorming...
  customization: null          # Optional customizations
````

**Key Patterns:**

- **Human Names**: Each agent has a distinct personality (Mary, Winston, Alex)
- **Professional Identity**: Clear role definition
- **Usage Context**: When to select this agent
- **Visual Identity**: Emoji icons for quick recognition

### 2. **Persona System**

```yaml
persona:
  role: Insightful Analyst & Strategic Ideation Partner
  style: Analytical, inquisitive, creative, facilitative
  identity: Strategic analyst specializing in brainstorming
  focus: Research planning, ideation facilitation
  core_principles:
    - Curiosity-Driven Inquiry
    - Objective & Evidence-Based Analysis
    - Strategic Contextualization
    - [8-12 principles total]
```

**Persona Components:**

- **Role**: Primary professional function
- **Style**: Behavioral characteristics and approach
- **Identity**: Self-description and specializations
- **Focus**: Key areas of expertise
- **Core Principles**: 8-12 guiding principles that shape behavior

### 3. **Command System**

```yaml
commands:
  - help: Show numbered list of commands
  - create-project-brief: use task create-doc with project-brief-tmpl.yaml
  - brainstorm {topic}: Facilitate session (run task facilitate-brainstorming-session.md)
  - exit: Say goodbye and abandon persona
```

**Command Types:**

- **Meta Commands**: help, exit, yolo
- **Document Creation**: Combine task + template
- **Process Execution**: Run specific tasks
- **Mode Toggles**: Change agent behavior
- **Parameterized**: Accept user input

### 4. **Dependency Management**

```yaml
dependencies:
  tasks:
    - create-doc.md
    - facilitate-brainstorming-session.md
    - advanced-elicitation.md
  templates:
    - project-brief-tmpl.yaml
    - market-research-tmpl.yaml
  checklists:
    - story-dod-checklist.md
  data:
    - bmad-kb.md
    - brainstorming-techniques.md
```

**Dependency Categories:**

- **Tasks**: Executable workflows the agent can run
- **Templates**: Document structures the agent can use
- **Checklists**: Validation frameworks the agent can execute
- **Data**: Knowledge resources the agent can access
- **Utils**: Utility files and helpers

## Agent Types and Specializations

### 1. **Core Development Agents**

#### **Developer Agent (Dev)**

- **Name**: James
- **Focus**: Story implementation, coding, testing
- **Key Commands**: develop-story, run-tests, explain
- **Unique Features**: Complex workflow command with completion gates
- **v5.0 Integration**: Works closely with Test Architect for quality advisory

#### **Architect Agent (Winston)**

- **Name**: Winston
- **Focus**: System design, technology selection, infrastructure
- **Key Commands**: create-full-stack-architecture, create-backend-architecture
- **Unique Features**: Multiple architecture templates for different scenarios

#### **Product Manager (PM)**

- **Name**: John
- **Focus**: Requirements, PRDs, product strategy
- **Key Commands**: create-prd, create-brownfield-prd
- **Unique Features**: Brownfield-specific capabilities

#### **Product Owner (PO)**

- **Name**: Sarah
- **Focus**: Validation, acceptance, quality gates
- **Key Commands**: execute-checklist-po, validate-next-story
- **Unique Features**: Master validation checklist

#### **Scrum Master (SM)**

- **Name**: Bob
- **Focus**: Story management, process facilitation
- **Key Commands**: draft (create story), story-checklist
- **Unique Features**: Story lifecycle management

#### **Test Architect Agent (Quinn)**

- **Name**: Quinn
- **File**: qa.md (not test-architect.md)
- **Focus**: Test architecture, quality advisory, risk assessment
- **Key Commands**: review, gate, risk-profile, test-design, trace
- **Unique Features**: Advisory quality gates, risk-based testing approach, requirements traceability
- **Philosophy Change (v5.0)**: Transformed from blocking QA to advisory Test Architect - teams choose their quality bar

### 2. **Discovery and Analysis Agents**

#### **Business Analyst (Mary)**

- **Name**: Mary
- **Focus**: Market research, brainstorming, competitive analysis
- **Key Commands**: brainstorm, perform-market-research, create-competitor-analysis
- **Unique Features**: Advanced brainstorming facilitation

#### **UX Expert (Sally)**

- **Name**: Sally
- **Focus**: User experience design, frontend specifications
- **Key Commands**: create-front-end-spec, generate-ai-frontend-prompt
- **Unique Features**: AI UI generation capabilities

### 3. **Orchestration Agents**

#### **BMad Orchestrator**

- **Focus**: Agent coordination, workflow guidance, role switching
- **Key Commands**: agent, workflow, workflow-guidance, kb-mode
- **Unique Features**: Can transform into any other agent, workflow discovery

#### **BMad Master**

- **Focus**: Universal task execution, direct access to all capabilities
- **Key Commands**: All tasks, all templates, all checklists
- **Unique Features**: Master access to entire BMad ecosystem

### 4. **Domain-Specific Agents (Expansion Packs)**

#### **Game Designer (Alex)**

- **Domain**: Game development
- **Focus**: Game design documents, mechanics, player experience
- **Key Commands**: create-game-design-doc, game-design-brainstorming
- **Unique Features**: Game-specific templates and validation

## Agent Interaction Patterns

### 1. **Direct User Interaction**

```
User → Agent Command → Task Execution → Output
```

### 2. **Agent Transformation**

```
User in Orchestrator → *agent architect → Transform to Architect → Work as Architect
```

### 3. **Workflow Orchestration**

```
Workflow → Agent A → Create Document → Agent B → Validate Document → Agent C → Implement
```

### 4. **Cross-Agent Dependencies**

```
PM creates PRD → Architect references PRD → Developer implements from both
```

### 5. **Quality Advisory Flow (v5.0)**

```
Developer implements → Test Architect reviews → Advisory gate created → Team decides quality bar
```

## Agent Lifecycle

### 1. **Activation Phase**

```yaml
activation-instructions:
  - STEP 1: Read THIS ENTIRE FILE
  - STEP 2: Adopt the persona defined below
  - STEP 3: Greet user with name/role and mention *help
  - DO NOT: Load any other files during activation
  - ONLY load dependency files when user requests execution
  - STAY IN CHARACTER!
```

### 2. **Operational Phase**

- Respond to user commands
- Execute tasks using dependencies
- Maintain persona throughout interaction
- Follow task instructions exactly (task authority principle)

### 3. **Exit Phase**

- Explicit exit command required
- Graceful goodbye maintaining character
- Return to orchestrator or user choice

## Agent Behavioral Rules

### 1. **Core Operational Rules**

#### **Lazy Loading Principle**

- Never pre-load any dependencies during activation
- Only load files when specific commands are executed
- This ensures fast startup and memory efficiency

#### **Task Authority Principle**

- When executing tasks, follow task instructions exactly
- Task instructions override agent default behavior
- Interactive workflows cannot be bypassed for efficiency

#### **Persona Consistency**

- Maintain character throughout entire session
- Use the defined style, approach, and principles
- Respond as the specific professional role

### 2. **Interaction Rules**

#### **Numbered Lists Protocol**

- Always present options as numbered lists
- Allow user selection by number
- Makes interaction consistent across all agents

#### **Command Prefix Requirement**

- All commands require `*` prefix
- Prevents accidental command execution
- Clear distinction between conversation and commands

#### **User Control Principle**

- Agent halts after greeting to await user direction
- No automatic execution without user request
- User always drives the interaction

### 3. **Resource Access Rules**

#### **Dependency-Only Access**

- Can only access files declared in dependencies
- No arbitrary file system access
- Controlled, predictable resource usage

#### **File Resolution Pattern**

- All files resolved using `{root}/{type}/{name}` pattern
- Consistent file location across all agents
- Enables portable agent definitions

## Agent Customization and Extension

### 1. **Customization Field**

```yaml
agent:
  customization: null # Can be overridden for specific needs
```

### 2. **Persona Adaptation**

Agents can be adapted for different contexts:

- **Core Principles**: Adjusted for domain-specific needs
- **Commands**: Modified for specialized workflows
- **Dependencies**: Tailored resource sets

### 3. **Expansion Pack Pattern**

```yaml
# Domain-specific agent in expansion pack
agent:
  name: Alex
  id: game-designer
  title: Game Design Specialist
  whenToUse: Use for game concept development, GDD creation
```

## Creating New Agents

### 1. **Agent Definition Template**

```yaml
agent:
  name: [Human Name]
  id: [kebab-case-id]
  title: [Professional Title]
  icon: [emoji]
  whenToUse: [Usage context]
  customization: null

persona:
  role: [Primary Role & Specialization]
  style: [Behavioral characteristics]
  identity: [Self-description]
  focus: [Key areas of expertise]
  core_principles:
    - [Principle 1]
    - [Principle 2]
    # 8-12 total principles

commands:
  - help: Show numbered list of commands
  - [domain-specific-commands]
  - exit: Abandon persona

dependencies:
  tasks: [list of task files]
  templates: [list of template files]
  checklists: [list of checklist files]
  data: [list of data files]
```

### 2. **Design Guidelines**

#### **Persona Development**

- Create distinct, memorable personality
- Define clear expertise boundaries
- Establish consistent behavioral patterns
- Include 8-12 core principles for guidance

#### **Command Design**

- Focus on domain-specific capabilities
- Combine tasks and templates logically
- Include standard meta-commands (help, exit)
- Support both simple and parameterized commands

#### **Dependency Management**

- Declare only necessary resources
- Follow the lazy loading principle
- Group related resources logically
- Ensure all dependencies exist

#### **Integration Considerations**

- Design for workflow integration
- Enable cross-agent collaboration
- Support agent team bundling
- Consider orchestrator compatibility

## Agent Bundling and Teams

### 1. **Agent Teams Structure**

```yaml
bundle:
  name: Team Fullstack
  icon: 🚀
  description: Team capable of full stack development
agents:
  - bmad-orchestrator
  - analyst
  - pm
  - architect
  - ux-expert
  - po
workflows:
  - greenfield-fullstack.yaml
  - brownfield-fullstack.yaml
```

### 2. **Bundle Benefits**

- **Cohesive Capabilities**: Related agents grouped together
- **Workflow Integration**: Matching workflows included
- **Easy Distribution**: Single package for domain
- **Reduced Complexity**: Users select teams, not individual agents

## v5.0 Agent Enhancements

### Test Architect Evolution

The QA agent has been completely reimagined as a Test Architect:

#### **Old Model (v4.x)**

- **Role**: Senior Developer & QA Architect
- **Approach**: Blocking quality gates
- **Focus**: Code review and validation
- **Authority**: Pass/fail decisions

#### **New Model (v5.0)**

- **Role**: Test Architect & Quality Advisor
- **Approach**: Advisory recommendations
- **Focus**: Risk-based testing, requirements traceability
- **Authority**: Teams decide their quality bar
- **New Tasks**:
  - `qa-gate`: Create advisory quality gates
  - `risk-profile`: Generate risk assessment matrices
  - `test-design`: Design comprehensive test strategies
  - `trace-requirements`: Map requirements to tests
  - `nfr-assess`: Validate non-functional requirements

#### **Philosophy Shift**

- **From**: "Gatekeeper" mentality
- **To**: "Quality Advisory" approach
- **Benefit**: Teams maintain autonomy while receiving expert guidance
- **Result**: More collaborative, less blocking workflow

## Advanced Agent Patterns

### 1. **Multi-Modal Agents**

Some agents support multiple interaction modes:

- **Normal Mode**: Standard command execution
- **KB Mode**: Knowledge base consultation
- **Chat Mode**: Conversational with elicitation
- **YOLO Mode**: Skip confirmations for speed

### 2. **Agent Transformation**

Orchestrator agents can transform into specialist agents:

```
*agent architect → Becomes Architect agent
*agent pm → Becomes PM agent
```

### 3. **Workflow-Integrated Agents**

Agents designed for workflow orchestration:

```yaml
workflow:
  sequence:
    - agent: pm
      creates: prd.md
    - agent: architect
      creates: architecture.md
      requires: prd.md
```

## Advanced Agent Patterns

### Personification Strategy

**Pattern**: Agents have human names alongside their roles

```yaml
# Not just roles, but personas
Dev Agent: James
PM Agent: John
PO Agent: Sarah
SM Agent: Bob
UX Agent: Sally
QA Agent: Taylor
```

**Benefits**:

- **Memory Aid**: Names make agents more memorable than roles alone
- **Personality**: Creates distinct interaction styles
- **Team Feel**: Simulates real team dynamics
- **User Connection**: Easier to relate to named personas

### Role-Based Context Expansion

**Pattern**: Different agents load different amounts of context based on their needs

```yaml
# Dev agent loads additional context
dev:
  dependencies:
    tasks: [extensive list]
  loadAlways:
    - coding-standards.md
    - tech-stack.md
    - source-tree.md

# PM agent stays lean
pm:
  dependencies:
    tasks: [minimal list]
```

**Rationale**:

- Dev agents need maximum context for code generation
- Planning agents need less technical detail
- Keeps each agent optimized for their role

### Fuzzy Command Resolution

**Pattern**: Commands support approximate matching

```yaml
# All resolve to execute-checklist
*checklist
*execute-check
*run-checklist
```

**Implementation**:

- Reduces cognitive load on users
- Prevents command memorization burden
- Enables natural interaction
- Graceful error handling

### Command Parameter Injection

**Pattern**: Dynamic parameters in command definitions

```yaml
commands:
  - review {story}: Review story implementation
  - test {component}: Design tests for component
  - gate {epic}.{story}: Execute QA gate
```

**Features**:

- Runtime parameter substitution
- Type hints in command structure
- Flexible argument passing
- Context-aware execution

### Agent Transformation Pattern

**Pattern**: Orchestrator agents can become specialist agents

```yaml
# BMad-Master transforms
*agent architect  # Becomes Winston (Architect)
*agent pm        # Becomes John (PM)
*agent dev       # Becomes James (Developer)
```

**Advantages**:

- Single entry point for multiple specialists
- Preserves conversation context
- Reduces chat proliferation
- Smooth role transitions

## Key Insights

1. **Agents are Complete Systems**: Not just prompts, but full persona + capability packages
2. **Persona-Driven Behavior**: Professional identity shapes all interactions
3. **Resource Independence**: Each agent is self-contained with declared dependencies
4. **Lazy Loading Architecture**: Fast startup, on-demand resource loading
5. **Task Authority**: Tasks override agent defaults when executing
6. **User-Controlled**: Agents await direction, never act automatically
7. **Modular Design**: Easy to create, customize, and bundle agents
8. **Workflow Integration**: Designed for orchestration and collaboration
9. **Domain Extensibility**: Expansion packs enable specialized agents
10. **Consistent Interface**: All agents follow same command and interaction patterns

## The Agent Advantage

BMad's agent system provides:

- **Professional Expertise**: Each agent embodies domain knowledge
- **Consistent Interface**: Same interaction patterns across all agents
- **Flexible Orchestration**: Can work individually or in coordinated workflows
- **Easy Extension**: Simple to add new domain specialists
- **User Familiarity**: Professional roles users already understand
- **Quality Focus**: Each agent maintains professional standards
- **Collaborative Design**: Agents designed to work together effectively
