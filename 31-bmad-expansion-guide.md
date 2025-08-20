# Complete Guide: Creating BMad Expansion Packs

## Overview

Expansion packs extend BMad's capabilities to new domains beyond traditional software development. They provide specialized agents, workflows, templates, and resources tailored to specific industries or project types. This guide consolidates all knowledge needed to create, test, and distribute professional expansion packs.

> **Note**: This guide describes the current expansion pack architecture as implemented in BMad v4.39.1. Some advanced features like automated build systems and NPM distribution are aspirational and may be added in future versions.

## What is an Expansion Pack?

### Definition

An expansion pack is a self-contained ecosystem that adapts BMad's orchestration system to a specific domain. It includes:

- **Domain-specific agents** with specialized expertise
- **Custom workflows** for domain processes
- **Tailored templates** for domain documents
- **Specialized tasks** for domain activities
- **Domain knowledge** and best practices
- **Validation checklists** for quality control

### Real-World Examples

```
bmad-2d-unity-game-dev/     # Unity game development
bmad-2d-phaser-game-dev/    # Phaser.js game development
bmad-infrastructure-devops/  # Infrastructure as code
```

## Expansion Pack Architecture

### Standard Directory Structure

```
expansion-pack-name/
├── config.yaml              # Pack metadata and configuration
├── agent-teams/            # Team bundle definitions
│   └── domain-team.yaml
├── agents/                 # Domain-specific AI agents
│   ├── domain-expert.md
│   ├── domain-architect.md
│   └── domain-developer.md
├── workflows/              # Domain workflows
│   ├── domain-greenfield.yaml
│   └── domain-process.yaml
├── templates/              # Document templates
│   ├── domain-brief-tmpl.yaml
│   └── domain-spec-tmpl.yaml
├── tasks/                  # Domain-specific tasks
│   ├── domain-analysis.md
│   └── domain-validation.md
├── checklists/            # Quality checklists
│   └── domain-checklist.md
├── data/                  # Domain knowledge
│   ├── bmad-kb.md        # Domain knowledge base
│   └── best-practices.md
└── README.md             # Pack documentation
```

## Step-by-Step Creation Guide

### Step 1: Define Your Domain

#### 1.1 Identify Domain Characteristics

```yaml
domain_analysis:
  name: "Game Development"
  characteristics:
    - Visual and interactive output
    - Asset pipeline management
    - Player experience focus
    - Iterative playtesting
  unique_roles:
    - Game Designer (instead of Analyst)
    - Level Designer (new role)
    - Game Artist (if applicable)
  specific_artifacts:
    - Game Design Document
    - Level Design specs
    - Asset requirements
```

#### 1.2 Map to BMad Concepts

| BMad Concept | Domain Equivalent | Notes |
|--------------|-------------------|-------|
| Product Requirements | Game Design Doc | Focus on mechanics |
| User Stories | Game Features | Player-centric |
| Architecture | Game Architecture | Engine, systems |
| Implementation | Game Development | Code + assets |

### Step 2: Create Configuration

#### 2.1 Create config.yaml

```yaml
name: bmad-game-dev
version: 1.0.0
short-title: Game Dev Pack
description: Game Development expansion pack for BMad
author: Your Name
slashPrefix: game

# Optional: extend core config
extends: core-config
gameDesign:
  designFile: docs/game-design.md
  designSharded: true
  designShardedLocation: docs/game-design
  
# Additional context files
devLoadAlwaysFiles:
  - docs/game-design/mechanics.md
  - docs/game-design/assets.md
```

### Step 3: Design Domain Agents

#### 3.1 Identify Required Agents

```yaml
# Agent mapping strategy
core_agents:
  analyst: game-designer      # Maps to game design
  architect: game-architect   # Technical game systems
  pm: game-producer          # Project management
  dev: game-developer        # Implementation
  
new_agents:
  level-designer: Creates level layouts
  asset-manager: Manages art pipeline
  playtester: Testing coordinator
```

#### 3.2 Create Agent Definition

```markdown
# game-designer

ACTIVATION-NOTICE: You are now the Game Designer agent...

```yaml
agent:
  name: Gideon
  id: game-designer
  title: Game Designer
  icon: 🎮
  whenToUse: Use for game concept, mechanics, player experience

persona:
  role: Creative Game Designer & Systems Architect
  style: Creative, player-focused, iterative, analytical
  identity: Expert in game mechanics, player psychology, fun factor
  focus: Game loops, progression, balance, engagement
  core_principles:
    - Player Experience First
    - Fun is Fundamental
    - Mechanics Support Theme
    - Balance Through Iteration
    - Accessibility Matters
    - Emergent Gameplay
    - Clear Feedback Loops
    - Progressive Complexity

commands:
  - help: Show available commands
  - brainstorm {concept}: Explore game concepts
  - create-game-design: Create GDD using template
  - design-mechanic {mechanic}: Detail specific mechanic
  - balance-analysis: Analyze game balance
  - exit: Leave game designer mode

dependencies:
  tasks:
    - create-doc.md
    - game-design-brainstorming.md
  templates:
    - game-design-doc-tmpl.yaml
    - mechanic-spec-tmpl.yaml
  checklists:
    - game-design-checklist.md
```
```

### Step 4: Create Domain Workflows

#### 4.1 Adapt Core Workflows

```yaml
workflow:
  id: game-dev-greenfield
  name: Greenfield Game Development
  description: Complete game development from concept to prototype
  type: greenfield
  project_types:
    - 2d-game
    - 3d-game
    - mobile-game
    - web-game
    
  sequence:
    - agent: game-designer
      creates: game-brief.md
      optional_steps:
        - brainstorming_session
        - competitor_analysis
      notes: "Define core concept and target audience"
      
    - agent: game-designer
      creates: game-design-doc.md
      requires: game-brief.md
      notes: "Detailed mechanics, progression, monetization"
      
    - agent: game-architect
      creates: technical-architecture.md
      requires: game-design-doc.md
      notes: "Engine choice, systems design, performance targets"
      
    - agent: level-designer
      creates: level-designs/
      requires: game-design-doc.md
      notes: "Create level layouts and progression"
      
    # Continue with validation, sharding, implementation...
```

#### 4.2 Create Domain-Specific Workflows

```yaml
workflow:
  id: game-prototype
  name: Rapid Game Prototype
  description: Quick prototype for game jam or proof of concept
  
  sequence:
    - agent: game-designer
      action: quick_concept
      creates: prototype-pitch.md
      time_limit: 1_hour
      
    - agent: game-developer
      action: implement_core_loop
      creates: prototype_build
      time_limit: 8_hours
      
    - agent: playtester
      action: quick_feedback
      creates: playtest-results.md
      time_limit: 2_hours
```

### Step 5: Design Domain Templates

#### 5.1 Create Domain Document Templates

```yaml
template:
  id: game-design-doc
  name: Game Design Document
  version: 1.0
  output:
    format: markdown
    filename: docs/game-design.md
    title: "{{game_title}} - Game Design Document"
    
sections:
  - id: executive-summary
    title: Executive Summary
    instruction: |
      Create a compelling 1-page overview covering:
      - Core concept in one sentence
      - Target audience and platform
      - Unique selling points
      - Monetization strategy
    elicit: true
    
  - id: core-mechanics
    title: Core Mechanics
    sections:
      - id: primary-loop
        title: Primary Game Loop
        instruction: |
          Describe the 30-second to 2-minute core loop.
          What does the player do repeatedly?
        type: paragraphs
        elicit: true
        
      - id: progression
        title: Progression Systems
        instruction: |
          How does the player advance?
          - Leveling systems
          - Unlockables
          - Skill trees
        type: bullet-list
        
  - id: technical-requirements
    title: Technical Requirements
    owner: game-architect
    sections:
      - id: performance
        title: Performance Targets
        template: |
          - Platform: {{platform}}
          - Target FPS: {{fps}}
          - Max RAM: {{ram}}
          - Storage: {{storage}}
```

### Step 6: Create Domain Tasks

#### 6.1 Specialized Task Creation

```markdown
# game-design-brainstorming.md

## Purpose
Facilitate creative brainstorming for game concepts using game-specific techniques.

## Techniques
1. **Mechanic Mixing**: Combine mechanics from different genres
2. **Theme Reversal**: Take known theme and reverse it
3. **Constraint Design**: Design within specific limitations
4. **Player Story**: Start with player emotion/experience

## Process
1. Present initial concept
2. Apply selected technique
3. Generate 5 variations
4. Evaluate for:
   - Fun factor
   - Technical feasibility
   - Market fit
   - Uniqueness
5. Refine top concepts

## Output
- 3 refined game concepts
- Core mechanics for each
- Target audience analysis
- Risk assessment
```

### Step 7: Create Domain Checklists

#### 7.1 Quality Validation Checklists

```markdown
# game-design-checklist.md

## Game Design Document Validation

### Core Concept
- [ ] Concept explained in one sentence
- [ ] Target audience clearly defined
- [ ] Platform requirements specified
- [ ] Unique selling points identified

### Mechanics
- [ ] Core loop documented
- [ ] All mechanics have purpose
- [ ] Progression systems defined
- [ ] Difficulty curve planned
- [ ] Tutorial approach outlined

### Technical Feasibility
- [ ] Performance targets realistic
- [ ] Asset pipeline defined
- [ ] Technical risks identified
- [ ] Prototype scope defined

### Monetization
- [ ] Revenue model selected
- [ ] Fits target audience
- [ ] Doesn't break gameplay
- [ ] Competitive analysis done
```

### Step 8: Add Domain Knowledge

#### 8.1 Create Domain Knowledge Base

```markdown
# data/bmad-kb.md

## Game Development with BMad

### Core Principles
1. **Fun First**: Every decision should enhance fun
2. **Iterate Rapidly**: Playtest early and often
3. **Player-Centric**: Design for your audience
4. **Scope Management**: Start small, expand carefully

### Development Phases
1. **Concept Phase**: Core idea and validation
2. **Pre-Production**: Prototypes and proof of concept
3. **Production**: Full development
4. **Polish**: Bug fixes and optimization
5. **Launch**: Release and marketing
6. **Post-Launch**: Updates and content

### Best Practices
- Prototype the core loop first
- Get feedback early
- Document all decisions
- Version control everything
- Plan for platform requirements
```

### Step 9: Create Agent Teams

#### 9.1 Bundle Agents into Teams

```yaml
# agent-teams/game-dev-team.yaml
bundle:
  name: Game Dev Team
  icon: 🎮
  description: Complete game development team
agents:
  - bmad-orchestrator
  - game-designer
  - game-architect
  - game-developer
  - level-designer
  - game-qa
workflows:
  - game-dev-greenfield.yaml
  - game-prototype.yaml
  - game-iteration.yaml
```

### Step 10: Configuration and Integration

#### 10.1 Expansion Pack Configuration

Expansion packs use a simple configuration file:

```yaml
# config.yaml in expansion pack
name: bmad-game-dev-pack
version: 1.0.0
short-title: Game Dev Pack
description: Game Development expansion pack for BMad Method
author: Your Name
slashPrefix: game  # Prefix for expansion-specific commands
```

#### 10.2 Integration with Core BMad

**Current Integration Model**:
- Expansion packs are self-contained ecosystems
- They reference core components through agent dependencies
- No automatic configuration merging (each pack stands alone)
- Agents can reference core tasks/templates through dependencies

**Agent Dependency Resolution**:
```yaml
# In expansion pack agent definition
dependencies:
  tasks:
    - create-doc.md        # Can reference core BMad tasks
    - game-specific.md     # Or expansion-specific tasks
  templates:
    - game-design-doc.yaml # Expansion templates
```

#### 10.3 Context Management

**Manual Context Loading**:
- Expansion packs define their own knowledge base in `data/bmad-kb.md`
- Agents load context through task dependencies
- No automatic context merging with core BMad
- Each agent explicitly defines what it needs

**Benefits of Current Approach**:
- Clear separation of concerns
- No configuration conflicts
- Predictable behavior
- Easy to understand and debug

### Step 11: Testing Your Pack

#### 11.1 Testing Approach

```bash
# Test individual components
# 1. Test agent activation
/agent game-designer
*help  # Verify commands work

# 2. Test template generation
*create  # List available templates
# Select and test template generation

# 3. Test workflow execution
/workflow-start game-dev-greenfield
# Follow workflow steps

# 4. Verify task execution
*brainstorm game-mechanics
# Test task functionality
```

#### 11.2 Testing Checklist

```markdown
# Expansion Pack Testing
- [ ] All agents activate without errors
- [ ] Agent commands execute properly
- [ ] Templates generate valid documents
- [ ] Workflows reference correct agents
- [ ] Tasks load and execute
- [ ] Checklists validate correctly
- [ ] Knowledge base is accessible
- [ ] Dependencies resolve correctly
```

## Example Domain Expansion Packs

### Currently Available Expansion Packs

BMad includes several domain-specific expansion packs:

#### 1. **Game Development Packs**
```
bmad-2d-phaser-game-dev/    # Phaser 3 + TypeScript 2D games
bmad-2d-unity-game-dev/      # Unity 2D game development
```

**Key Features**:
- Game Designer agent (replaces Analyst)
- Game-specific templates (GDD, level design)
- Game development workflows
- Domain knowledge base for game dev

#### 2. **Creative Writing Pack**
```
bmad-creative-writing/       # Fiction and narrative design
```

**Key Features**:
- 10 specialized writing agents
- 8 workflows from ideation to publication
- 27 quality checklists
- KDP publishing integration

#### 3. **Infrastructure DevOps Pack**
```
bmad-infrastructure-devops/  # Infrastructure as code
```

**Key Features**:
- DevOps-specific agents
- Infrastructure workflows
- Deployment templates
- Cloud provider integrations

### Expansion Pack Reality Check

**What Expansion Packs Actually Are**:
- Self-contained domain adaptations
- Reference core BMad components through dependencies
- Simple configuration (name, version, author)
- Focus on domain-specific content

**What They Don't Currently Have**:
- Complex configuration merging systems
- Automated build/distribution pipelines
- NPM package distribution
- Dynamic configuration extensions

The power of expansion packs lies in their domain-specific agents, workflows, and templates that adapt BMad's orchestration to new fields while maintaining the core philosophy of human-in-the-loop, document-centric development.

## Best Practices for Pack Development

### 1. Maintain BMad Philosophy

```yaml
maintain:
  - Human-in-the-loop interaction
  - Document-centric workflow
  - Quality gates and validation
  - Clear agent responsibilities
  - Structured workflows
```

### 2. Domain-Specific Adaptations

```yaml
adapt:
  - Terminology to domain language
  - Processes to domain workflows
  - Validation to domain standards
  - Artifacts to domain outputs
  
preserve:
  - Core interaction patterns
  - Validation mechanisms
  - Document flow
  - Agent coordination
```

### 3. Reuse Core Components

```yaml
reuse:
  - Common tasks (create-doc, execute-checklist)
  - Base templates structure
  - Validation patterns
  - Workflow patterns
  
extend:
  - With domain-specific logic
  - Custom validation rules
  - Specialized processing
```

### 4. Documentation Standards

```markdown
# Every pack should include:
1. README.md - Overview and quick start
2. AGENTS.md - Agent descriptions and usage
3. WORKFLOWS.md - Workflow documentation
4. TEMPLATES.md - Template guide
5. EXAMPLES.md - Sample projects
```

## Distribution and Installation

### Current Distribution Model

Expansion packs are currently distributed as:

#### 1. **Git Repositories**
```bash
# Clone expansion pack repository
git clone https://github.com/user/bmad-game-pack
cp -r bmad-game-pack/. ./expansion-packs/my-game-pack/
```

#### 2. **Directory Structure**
```
expansion-packs/
├── bmad-2d-phaser-game-dev/
├── bmad-2d-unity-game-dev/
├── bmad-creative-writing/
└── bmad-infrastructure-devops/
```

#### 3. **Manual Integration**
- Copy expansion pack to BMad's expansion-packs directory
- Reference agents and workflows directly
- No automated installation process

### Using Expansion Packs

```bash
# 1. Copy expansion pack to BMad installation
cp -r my-expansion-pack ~/.bmad/expansion-packs/

# 2. Use expansion agents directly
/agent game-designer

# 3. Run expansion workflows
/workflow-start game-dev-greenfield
```

### Team Bundles

Expansion packs define teams in `agent-teams/` directory:

```yaml
# phaser-2d-nodejs-game-team.yaml
bundle:
  name: Phaser 2D NodeJS Game Team
  icon: 🎮
  description: Game Development team
agents:
  - analyst           # Core BMad agent
  - bmad-orchestrator # Core BMad agent
  - game-designer     # Expansion agent
  - game-developer    # Expansion agent
  - game-sm          # Expansion agent
workflows:
  - game-dev-greenfield.md
  - game-prototype.md
```

### Installation and Usage

#### **Current Installation Method**
```bash
# Manual installation
# 1. Navigate to BMad installation
cd /path/to/bmad-method

# 2. Expansion packs are in expansion-packs directory
ls expansion-packs/
# bmad-2d-phaser-game-dev/
# bmad-2d-unity-game-dev/
# bmad-creative-writing/
# bmad-infrastructure-devops/

# 3. Use expansion pack agents and workflows
# Agents and workflows from expansion packs
# are available alongside core BMad components
```

#### **Using Expansion Pack Components**
```bash
# Activate expansion pack agent
/agent game-designer

# Use expansion workflows
/workflow-start game-dev-greenfield

# Access expansion templates
# (through agent commands)
*create game-design-doc
```

#### **Slash Prefix for Commands**
```yaml
# In config.yaml
slashPrefix: game  # or bmad2dp for Phaser pack

# This enables expansion-specific commands
/game help
/bmad2dp status
```

### Version Compatibility

```yaml
# In config.yaml
version: 1.0.0  # Your expansion pack version

# Document BMad compatibility in README
# "Compatible with BMad v4.0.0 and above"
# "Requires Node.js v20+"
```

## Common Patterns for Different Domains

### Creative Domains (Games, Film, Music)

```yaml
focus:
  - Creative ideation
  - Iterative refinement
  - Audience experience
  - Asset management
  
agents:
  - Creative Director
  - Technical Director
  - Producer
  - Artist/Designer
```

### Engineering Domains (IoT, Embedded, Hardware)

```yaml
focus:
  - Specifications
  - Compliance/Standards
  - Testing protocols
  - Manufacturing
  
agents:
  - Systems Engineer
  - Hardware Designer
  - Test Engineer
  - Compliance Officer
```

### Business Domains (Marketing, Sales, Operations)

```yaml
focus:
  - Strategy documents
  - Process optimization
  - Metrics/KPIs
  - Stakeholder management
  
agents:
  - Strategist
  - Analyst
  - Process Designer
  - Project Manager
```

## Troubleshooting Pack Development

### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| Agents don't load | Missing dependencies | Check task/template references |
| Workflow fails | Agent not in team | Add to agent-teams bundle |
| Template errors | Invalid YAML | Validate YAML syntax |
| Tasks not found | Wrong path | Use relative paths from pack root |
| Knowledge not loaded | Missing from data/ | Add to data/bmad-kb.md |

### Debugging Workflow

```bash
# 1. Enable debug logging
export BMAD_DEBUG=true

# 2. Test individual components
bmad test-agent game-designer
bmad test-workflow game-dev-greenfield
bmad test-template game-design-doc

# 3. Check logs
cat .ai/debug-log.md
```

## Advanced Patterns

### Referencing Core Components

```yaml
# In expansion pack agents
dependencies:
  tasks:
    # Reference core BMad tasks
    - create-doc.md          # From bmad-core/tasks/
    - execute-checklist.md   # From common/tasks/
    # Add expansion-specific tasks
    - game-analysis.md       # From expansion pack
  templates:
    # Mix core and expansion templates
    - prd-tmpl.yaml         # Core template
    - game-design-doc.yaml  # Expansion template
```

### Agent Naming Conventions

```yaml
# Give agents memorable names
agent:
  name: Alex        # Personal name
  id: game-designer # Functional ID
  title: Game Design Specialist
  icon: 🎮         # Visual identifier
```

### Workflow Patterns

```yaml
# Adapt core workflow patterns
workflow:
  # Similar to core greenfield but domain-specific
  type: greenfield
  # Reference both core and expansion agents
  sequence:
    - agent: game-designer    # Expansion
    - agent: architect        # Core (if needed)
    - agent: game-developer   # Expansion
```

## Sharing Expansion Packs

### Distribution Best Practices

1. **Complete Documentation**
   - README.md with overview and quick start
   - List all agents, workflows, and templates
   - Provide usage examples
   - Document dependencies on BMad version

2. **Repository Structure**
   ```
   my-expansion-pack/
   ├── README.md           # Overview and setup
   ├── AGENTS.md          # Agent documentation
   ├── WORKFLOWS.md       # Workflow guide
   ├── config.yaml        # Pack configuration
   ├── agents/            # Agent definitions
   ├── workflows/         # Workflow files
   ├── templates/         # Templates
   ├── tasks/             # Tasks
   ├── checklists/        # Checklists
   ├── data/              # Knowledge base
   └── agent-teams/       # Team definitions
   ```

3. **Version Management**
   - Use semantic versioning
   - Document BMad version compatibility
   - Maintain changelog

4. **Community Sharing**
   - Share on GitHub/GitLab
   - Submit to BMad community registry (when available)
   - Provide examples and tutorials

## Summary

Creating BMad expansion packs enables:

- **Domain adaptation** of BMad's powerful orchestration
- **Specialized workflows** for any industry
- **Reusable components** across projects
- **Consistent quality** through validation
- **Community sharing** of domain expertise

Key success factors:
- Understand your domain deeply
- Map domain concepts to BMad patterns
- Maintain BMad's quality philosophy
- Test thoroughly with real projects
- Document comprehensively
- Share with the community

Expansion packs demonstrate BMad's ultimate flexibility: the ability to bring AI-orchestrated development to any domain while maintaining quality, consistency, and human oversight throughout the process.