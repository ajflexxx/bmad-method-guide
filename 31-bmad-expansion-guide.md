# Complete Guide: Creating BMad Expansion Packs

## Overview

Expansion packs extend BMad's capabilities to new domains beyond traditional software development. They provide specialized agents, workflows, templates, and resources tailored to specific industries or project types. This guide consolidates all knowledge needed to create, test, and distribute professional expansion packs.

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

### Step 10: Testing Your Pack

#### 10.1 Integration Testing

```bash
# Test pack loading
bmad init --pack ./expansion-packs/my-game-pack

# Verify agents load
/agent-list
# Should show game-specific agents

# Test workflow
/workflows
# Should show game workflows

# Run through complete workflow
/workflow-start game-dev-greenfield
```

#### 10.2 Create Test Project

```markdown
# Test checklist
- [ ] All agents activate correctly
- [ ] Templates generate valid documents
- [ ] Workflows complete successfully
- [ ] Tasks execute without errors
- [ ] Checklists validate properly
- [ ] Knowledge base accessible
```

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

### Package Structure

```yaml
# package.json for npm distribution
{
  "name": "@bmad/game-dev-pack",
  "version": "1.0.0",
  "description": "Game Development BMad Pack",
  "files": [
    "config.yaml",
    "agents/",
    "workflows/",
    "templates/",
    "tasks/",
    "checklists/",
    "data/",
    "agent-teams/"
  ],
  "keywords": ["bmad", "game-dev", "ai-agents"]
}
```

### Installation Methods

```bash
# Method 1: NPM Package
npm install @bmad/game-dev-pack

# Method 2: Git Repository
git clone https://github.com/user/bmad-game-pack
bmad install ./bmad-game-pack

# Method 3: Direct Directory
cp -r game-pack ~/.bmad/packs/
```

### Version Management

```yaml
# Semantic versioning
version: MAJOR.MINOR.PATCH
# MAJOR: Breaking changes
# MINOR: New features
# PATCH: Bug fixes

# Compatibility
compatible_with:
  bmad_core: ">=4.0.0"
  node: ">=18.0.0"
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

## Advanced Topics

### Cross-Pack Dependencies

```yaml
# Pack can depend on other packs
dependencies:
  - "@bmad/core": "^4.0.0"
  - "@bmad/graphics-pack": "^1.0.0"
  
# Import from other packs
imports:
  agents:
    - "@bmad/graphics-pack/agents/artist.md"
```

### Dynamic Configuration

```yaml
# Environment-specific settings
config:
  development:
    debug: true
    validation: strict
  production:
    debug: false
    validation: normal
```

### Custom Commands

```yaml
# Add pack-specific commands
commands:
  /game-balance: Run balance analysis
  /playtest: Initialize playtest session
  /asset-check: Validate asset pipeline
```

## Pack Certification

### Quality Standards

To be certified as an official BMad pack:

1. **Complete Documentation**: All components documented
2. **Test Coverage**: Automated tests for workflows
3. **Example Project**: Working sample included
4. **Version Compatibility**: Works with latest BMad
5. **Best Practices**: Follows BMad patterns
6. **Community Review**: Peer reviewed

### Submission Process

```bash
# 1. Prepare pack
bmad pack validate ./my-pack

# 2. Run tests
bmad pack test ./my-pack

# 3. Submit for review
bmad pack submit ./my-pack

# 4. Address feedback
# 5. Get certified
```

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