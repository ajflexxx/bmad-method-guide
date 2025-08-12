# BMad-Core System Architecture

The bmad-core folder contains an AI-powered software development orchestration system. Here's how the files are interconnected:

## Core Configuration Hub (`core-config.yaml`)
- Central configuration that defines project paths and settings
- Controls document locations (PRD, architecture, stories)
- Specifies sharding behavior and file patterns
- Acts as the "registry" that all components reference

## Agent System (agents/)
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

## Workflow Engine (workflows/)
Workflows define end-to-end processes by orchestrating agents in sequence:
- Each workflow specifies agent order, required inputs, and expected outputs
- Greenfield workflows: For new projects (fullstack, service, UI)
- Brownfield workflows: For existing codebases
- Workflows reference agents, tasks, and templates to create a complete pipeline

## Task Library (tasks/)
Reusable task definitions that agents execute:
- Tasks contain step-by-step instructions
- Can be interactive (requiring user input) or automated
- Reference templates and checklists
- Examples: `create-next-story`, `facilitate-brainstorming-session`, `review-story`

## Template System (templates/)
Structured document templates ensure consistency:
- Define document formats (YAML-based)
- Include sections, prompts, and validation rules
- Used by agents to create standardized outputs (PRDs, architecture docs, stories)

## Agent Teams (agent-teams/)
Bundle configurations grouping agents and workflows:
- Define which agents work together
- Specify available workflows for different project types
- Example: `team-fullstack` includes all agents needed for full-stack development

## Checklists (checklists/)
Quality control and validation steps:
- Used by agents (especially PO) to validate deliverables
- Ensure completeness and consistency across documents

## Data Resources (data/)
Knowledge base and reference materials:
- `bmad-kb.md`: Core methodology knowledge
- Brainstorming techniques, elicitation methods
- Technical preferences and best practices

## Connection Flow
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