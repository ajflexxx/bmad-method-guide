# Deep Dive: Agent Teams in BMad - The Bundle System

## Overview

Agent Teams in BMad are curated bundles that package together specific agents and workflows for different development scenarios. They act as "deployment profiles" that determine which AI personas and processes are available in a given environment. Teams are the mechanism for tailoring BMad's capabilities to specific project needs while managing system resources efficiently.

## Team Bundle Anatomy

### Core Structure

Every agent team is defined in a YAML file with this standardized structure:

```yaml
bundle:
  name: Human-readable team name
  icon: Visual emoji representation
  description: Brief explanation of team purpose
agents:
  - list of agent IDs or patterns
workflows:
  - list of workflow files
```

### Bundle Components

#### 1. **Bundle Metadata**

```yaml
bundle:
  name: Team Fullstack
  icon: 🚀
  description: Team capable of full stack, front end only, or service development.
```

**Key Elements:**
- **name**: Display name for the team
- **icon**: Visual identifier (emoji)
- **description**: Clear explanation of team capabilities

#### 2. **Agent Manifest**

```yaml
agents:
  - bmad-orchestrator  # Specific agent ID
  - analyst           # Individual agent
  - '*'              # Wildcard - includes all agents
```

**Agent Patterns:**
- **Explicit listing**: Named agents only (`analyst`, `pm`, `architect`)
- **Wildcard inclusion**: `'*'` includes all available agents
- **Orchestrator presence**: Most teams include `bmad-orchestrator` for coordination

#### 3. **Workflow Registry**

```yaml
workflows:
  - brownfield-fullstack.yaml
  - greenfield-service.yaml
  - null  # No workflows included
```

**Workflow Inclusion:**
- **Specific workflows**: Listed by filename
- **Multiple workflows**: Teams can include many workflows
- **No workflows**: `null` for execution-only teams

## Core Teams Analysis

### 1. **Team All** (`team-all.yaml`)

```yaml
bundle:
  name: Team All
  icon: 👥
  description: Includes every core system agent.
agents:
  - bmad-orchestrator
  - '*'
workflows:
  - [all 6 core workflows]
```

**Purpose**: Complete BMad capabilities
**Use Cases**:
- Development environments with unlimited resources
- Exploration and experimentation
- Training and learning BMad
- Projects with uncertain requirements

**Characteristics**:
- Maximum flexibility
- Highest resource consumption
- All personas available
- All workflows accessible

### 2. **Team Fullstack** (`team-fullstack.yaml`)

```yaml
bundle:
  name: Team Fullstack
  icon: 🚀
  description: Team capable of full stack, front end only, or service development.
agents:
  - bmad-orchestrator
  - analyst
  - pm
  - ux-expert
  - architect
  - po
workflows:
  - [all 6 core workflows]
```

**Purpose**: Balanced full-stack development
**Use Cases**:
- Standard web applications
- SaaS products
- MVP development
- Most common projects

**Characteristics**:
- Planning and design agents only
- No dev/qa agents (handled separately in IDE)
- All workflows available
- Optimized for planning phase

**Missing Agents**:
- `sm`: Scrum Master (added in IDE phase)
- `dev`: Developer (IDE phase)
- `qa`: Quality Assurance (IDE phase)

### 3. **Team IDE Minimal** (`team-ide-minimal.yaml`)

```yaml
bundle:
  name: Team IDE Minimal
  icon: ⚡
  description: Only the bare minimum for the IDE PO SM dev qa cycle.
agents:
  - po
  - sm
  - dev
  - qa
workflows: null
```

**Purpose**: Lightweight IDE development
**Use Cases**:
- IDE-based development phase
- Story implementation cycle
- Resource-constrained environments
- CI/CD pipelines

**Characteristics**:
- Execution-focused agents only
- No planning agents
- No workflows (already planned)
- Minimum memory footprint

**Agent Roles in IDE**:
- `po`: Validates and shards documents
- `sm`: Creates and manages stories
- `dev`: Implements stories
- `qa`: Reviews implementations

### 4. **Team No UI** (`team-no-ui.yaml`)

```yaml
bundle:
  name: Team No UI
  icon: 🔧
  description: Team with no UX or UI Planning.
agents:
  - bmad-orchestrator
  - analyst
  - pm
  - architect
  - po
workflows:
  - greenfield-service.yaml
  - brownfield-service.yaml
```

**Purpose**: Backend service development
**Use Cases**:
- APIs and microservices
- Backend-only systems
- Data processing services
- Command-line tools

**Characteristics**:
- No `ux-expert` agent
- Service-oriented workflows only
- Backend architecture focus
- Reduced complexity for non-UI projects

## Team Selection Patterns

### Decision Matrix

| Project Type | Recommended Team | Rationale |
|-------------|-----------------|-----------|
| **Full Web App** | Team Fullstack | Includes UX for frontend |
| **API Only** | Team No UI | No UX overhead |
| **Exploration** | Team All | Maximum flexibility |
| **IDE Development** | Team IDE Minimal | Execution only |
| **Microservice** | Team No UI | Backend focused |
| **Mobile App** | Team Fullstack | Needs UX design |

### Resource Considerations

Teams have different resource footprints:

1. **Team All**: ~100% resource usage
   - All agents loaded
   - All workflows available
   - Maximum flexibility
   - Highest token consumption

2. **Team Fullstack**: ~70% resource usage
   - Planning agents only
   - All workflows
   - Balanced approach
   - Moderate tokens

3. **Team No UI**: ~50% resource usage
   - No UX components
   - Limited workflows
   - Efficient for backend
   - Lower tokens

4. **Team IDE Minimal**: ~30% resource usage
   - Execution only
   - No workflows
   - Minimum footprint
   - Lowest tokens

## Bundle Loading Mechanism

### How Teams Are Loaded

1. **Bundle Selection**:
   - User specifies team during BMad initialization
   - System loads team YAML file
   - Parses agent and workflow lists

2. **Agent Resolution**:
   ```yaml
   agents:
     - bmad-orchestrator  # Load specific file
     - '*'               # Load all agents in agents/ folder
   ```
   - Wildcard expands to all agent files
   - Individual agents loaded by ID
   - Missing agents cause load failure

3. **Workflow Registration**:
   ```yaml
   workflows:
     - greenfield-fullstack.yaml  # Register specific workflow
     - null                       # No workflows registered
   ```
   - Workflows registered for user selection
   - Only registered workflows appear in menus
   - Null means no workflow options

### Bundle Distribution

Teams are distributed as single YAML files that reference:
- Agent markdown files in `agents/` directory
- Workflow YAML files in `workflows/` directory
- Shared resources in `data/`, `templates/`, `checklists/`

This separation enables:
- Lightweight team definitions
- Shared agent definitions across teams
- Easy team customization
- Modular distribution

## Team Composition Patterns

### 1. **Planning Teams**

Include agents for requirements and design:
```yaml
agents:
  - analyst     # Requirements gathering
  - pm         # Product management
  - architect  # Technical design
  - ux-expert  # User experience
```

Used in early project phases for:
- Requirements analysis
- Architecture design
- User experience planning
- Documentation creation

### 2. **Execution Teams**

Include agents for implementation:
```yaml
agents:
  - sm   # Story creation
  - dev  # Implementation
  - qa   # Quality review
```

Used in development phase for:
- Story management
- Code implementation
- Quality assurance
- Bug fixing

### 3. **Validation Teams**

Include agents for quality control:
```yaml
agents:
  - po  # Product validation
  - qa  # Technical validation
```

Used for:
- Document validation
- Implementation review
- Acceptance testing
- Quality gates

## Workflow-Team Relationships

### Workflow Requirements

Workflows require specific agents to function:

```yaml
# greenfield-fullstack.yaml requires:
- analyst
- pm
- ux-expert
- architect
- po

# Team must include these agents or workflow fails
```

### Team-Workflow Compatibility

| Team | Compatible Workflows | Missing Agents |
|------|---------------------|----------------|
| **Team All** | All workflows | None |
| **Team Fullstack** | All planning workflows | sm, dev, qa (IDE phase) |
| **Team No UI** | Service workflows only | ux-expert |
| **Team IDE Minimal** | None (execution only) | All planning agents |

### Workflow Inclusion Strategy

Teams include workflows based on:
1. **Agent availability**: All required agents present
2. **Use case alignment**: Workflows match team purpose
3. **Resource optimization**: Only needed workflows included

## Creating Custom Teams

### Team Definition Template

```yaml
bundle:
  name: Team Custom Domain
  icon: 🎯
  description: Specialized team for [domain]
agents:
  - bmad-orchestrator  # Usually included
  - domain-expert      # Custom agent
  - domain-developer   # Specialized developer
  - [standard agents as needed]
workflows:
  - domain-workflow.yaml
  - [standard workflows if compatible]
```

### Best Practices for Custom Teams

1. **Include Orchestrator**:
   - Almost always include `bmad-orchestrator`
   - Provides coordination and transformation

2. **Match Agents to Workflows**:
   - Ensure all workflow agents included
   - Test workflow execution paths

3. **Consider Phases**:
   - Planning phase agents
   - Execution phase agents
   - May need separate teams

4. **Optimize for Resources**:
   - Include only necessary agents
   - Limit workflows to relevant ones
   - Consider token consumption

### Example: Game Development Team

```yaml
bundle:
  name: Team Game Dev
  icon: 🎮
  description: Game development specialists
agents:
  - bmad-orchestrator
  - game-designer      # Instead of analyst
  - game-architect     # Instead of architect
  - game-developer     # Specialized dev
  - qa                # Standard QA
workflows:
  - game-design-workflow.yaml
  - game-prototype-workflow.yaml
```

## Team Capability Matching

### Capability Matrix

Teams provide different capabilities:

| Capability | Team All | Team Fullstack | Team No UI | Team IDE Minimal |
|-----------|----------|----------------|------------|------------------|
| **Requirements** | ✅ | ✅ | ✅ | ❌ |
| **Architecture** | ✅ | ✅ | ✅ | ❌ |
| **UX Design** | ✅ | ✅ | ❌ | ❌ |
| **Validation** | ✅ | ✅ | ✅ | ✅ |
| **Story Creation** | ✅ | ❌ | ❌ | ✅ |
| **Development** | ✅ | ❌ | ❌ | ✅ |
| **QA Review** | ✅ | ❌ | ❌ | ✅ |

### Matching Teams to Projects

1. **Assess Project Needs**:
   - Frontend requirements?
   - Backend complexity?
   - Team size?
   - Resource constraints?

2. **Select Appropriate Team**:
   - Full-stack → Team Fullstack
   - API only → Team No UI
   - Exploration → Team All
   - Implementation → Team IDE Minimal

3. **Consider Phasing**:
   - Planning phase: Feature-rich team
   - Execution phase: Minimal team
   - May switch teams between phases

## Team Loading and Activation

### Loading Process

1. **Team Selection**:
   ```bash
   bmad init --team team-fullstack
   ```

2. **Bundle Loading**:
   - Read team YAML file
   - Parse agent list
   - Parse workflow list

3. **Agent Activation**:
   - Load each agent markdown file
   - Parse agent configuration
   - Load dependencies (tasks, templates)

4. **Workflow Registration**:
   - Register available workflows
   - Validate agent requirements
   - Enable workflow selection

### Troubleshooting Team Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| Agent not found | Missing from team | Add to team or switch teams |
| Workflow unavailable | Not in team bundle | Use team with workflow |
| Activation fails | Missing dependencies | Check agent requirements |
| Resource exceeded | Team too large | Use smaller team |

## Bundle System Benefits

### 1. **Resource Optimization**
- Load only needed agents
- Reduce memory footprint
- Lower token consumption
- Faster activation

### 2. **Clear Boundaries**
- Explicit capability sets
- Predictable behavior
- Easier troubleshooting
- Better documentation

### 3. **Flexible Deployment**
- Different teams for different phases
- Custom teams for domains
- Minimal teams for CI/CD
- Full teams for exploration

### 4. **Modular Architecture**
- Agents shared across teams
- Workflows reusable
- Easy to extend
- Simple to maintain

## Summary

Agent Teams are the deployment mechanism that makes BMad practical for real-world use. They provide:

- **Curated bundles** of agents and workflows for specific scenarios
- **Resource optimization** through selective loading
- **Clear capability boundaries** for different project phases
- **Flexible deployment** options from minimal to complete
- **Extensibility** through custom team definitions

Understanding teams is essential for:
- Selecting the right capabilities for your project
- Optimizing resource usage
- Creating domain-specific bundles
- Managing development phases
- Troubleshooting activation issues

The team system embodies BMad's philosophy of "right tool for the job" - providing exactly the capabilities needed for each scenario without unnecessary overhead.