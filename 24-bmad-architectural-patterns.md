# BMad Architectural Patterns - Deep Dive for Expansion Pack Developers

## Overview

This document synthesizes the architectural patterns discovered through deep analysis of BMad's implementation. These patterns reveal not just what BMad does, but WHY it's designed this way - crucial knowledge for building effective expansion packs.

## Core Design Philosophy

BMad's architecture follows these fundamental principles:

1. **Configuration Over Code**: Behavior changes through configuration, not code modification
2. **Progressive Complexity**: Simple defaults with deep customization available
3. **Human-Friendly Abstractions**: Fuzzy matching, personification, pragmatic shortcuts
4. **Context Minimalism**: Every decision optimizes for smaller context windows
5. **Indirect Knowledge Transfer**: Knowledge flows through tasks, not direct loading
6. **Runtime Flexibility**: Bindings happen at execution, not definition
7. **Role-Based Specialization**: Each component knows its lane and stays in it

## Resource Loading Patterns

### Data as Behavioral Modifiers

Data files don't just store information - they modify how agents and tasks behave:

```yaml
# Data creates different interaction modes
bmad-kb.md → Conversational knowledge mode
brainstorming-techniques.md → Interactive turn-taking dialogue
test-levels-framework.md → Criterial decision making
technical-preferences.md → Global configuration overlay
```

**Expansion Pack Implication**: Design data files that define behavior, not just content.

### Task-Exclusive Resource Loading

Some resources bypass agent dependencies entirely:

```
Pattern: Agent → Command → Task → Data
NOT: Agent → Data
```

Example: Test frameworks are only loaded by test-design task, not by Test Architect agent.

**Expansion Pack Implication**: Keep specialized knowledge in tasks, not agents, for better modularity.

### Granular Section Loading

Tasks can load specific sections of data files:

```markdown
# In test-design.md
**Reference:** Load `test-levels-framework.md` for detailed criteria
**Reference:** Load `test-priorities-matrix.md` for classification
```

**Expansion Pack Implication**: Structure data files with clear sections for selective loading.

### Lazy Loading with Strategic Exceptions

```yaml
# Default: Nothing pre-loaded
# Exception 1: core-config.yaml always loaded
# Exception 2: Dev agent loads devLoadAlwaysFiles
```

**Expansion Pack Implication**: Only pre-load what's absolutely essential for the role.

## Interaction Design Patterns

### Interactive vs. Batch Processing

Brainstorming and elicitation use conversational turn-taking:

```markdown
# From brainstorming-techniques.md
"Ask one provocative question, get their response, then ask another"
```

Not menu selection but dialogue construction.

**Expansion Pack Implication**: Create interactive experiences, not just command menus.

### Runtime Binding Pattern

Tasks use command definitions and runtime discovery:

```yaml
# Not in frontmatter
# But discovered at runtime through command execution
```

**Expansion Pack Implication**: Enable dynamic adaptation rather than static configuration.

### Parameter Injection Pattern

Commands support dynamic parameters:

```yaml
commands:
  - review {story}: Review story with ID
  - gate {epic}.{story}: QA gate for story
```

**Expansion Pack Implication**: Design flexible command structures with clear parameter patterns.

### Fuzzy Command Resolution

User-friendly approximate matching:

```yaml
# All work:
*checklist
*execute-check
*run-checklist
```

**Expansion Pack Implication**: Support variations of commands for better UX.

## Configuration Patterns

### Configuration-First Architecture

Critical paths are configuration-driven:

```yaml
qa:
  qaLocation: docs/qa  # Determines all QA artifact paths

# Results in dynamic paths:
${config.qa.qaLocation}/gates/${storyId}.yaml
${config.qa.qaLocation}/assessments/${type}/${storyId}.md
```

**Expansion Pack Implication**: Make paths and behaviors configurable, not hardcoded.

### Config Namespace Traversal

Dot notation enables deep config access:

```yaml
# In templates and tasks
qa.qaLocation
prd.prdShardedLocation
architecture.architectureVersion
```

**Expansion Pack Implication**: Use hierarchical configuration with dot notation access.

### Pluggable Artifact Storage

Configuration determines artifact organization:

```yaml
# Team A structure
qaLocation: docs/quality

# Team B structure  
qaLocation: testing/reports
```

**Expansion Pack Implication**: Let teams organize outputs their way.

### Agent-Specific Customization Points

Templates expose agent_config sections:

```yaml
agent_config:
  editable_sections:
    - implementation_details
    - technical_notes
```

**Expansion Pack Implication**: Create clear customization boundaries for different roles.

## Design Philosophy Patterns

### Progressive Disclosure

Templates minimal by default with extensive examples:

```yaml
# Minimal template
defaults:
  brief: true

# But extensive examples section
examples:
  - comprehensive: [30+ fields]
  - detailed: [20+ fields]
```

**Expansion Pack Implication**: Start simple, reveal complexity gradually.

### Strategy Pattern Without Classes

BMad achieves polymorphism through configuration:

```yaml
# Different sharding strategies
markdownExploder: true   # External tool strategy
markdownExploder: false  # AI sharding strategy

# Different workflow routing
brownfield-fullstack: complexity_classification
brownfield-service: direct_planning
```

**Expansion Pack Implication**: Use configuration to enable different behavioral strategies.

### Personification as Memory Aid

Agents have names, not just roles:

```yaml
Dev Agent: James
PM Agent: John
PO Agent: Sarah
SM Agent: Bob
```

**Expansion Pack Implication**: Give agents memorable identities.

### Pragmatic Shortcuts

YOLO mode for experienced users:

```yaml
execute-checklist:
  modes:
    - interactive: Step-by-step confirmation
    - YOLO: Execute all without prompts
```

**Expansion Pack Implication**: Provide expert modes alongside guided experiences.

### Embedded Intelligence

Components contain sophisticated instructions:

```yaml
# Checklists aren't just lists
checklist:
  llm_instructions: |
    Complex multi-paragraph guidance
    for intelligent evaluation
```

**Expansion Pack Implication**: Embed intelligence in components, not just agents.

### Workflow Specialization by Complexity

Only complex workflows have routing:

```yaml
brownfield-fullstack: # Has classification routing
  - single story
  - small feature  
  - major enhancement

brownfield-service: # Direct to planning
  - comprehensive only
```

**Expansion Pack Implication**: Add complexity only where needed.

## System Integration Patterns

### Command-Task-Data Pipeline

Knowledge flows through specific pathways:

```
User Command → Agent interprets → Task executes → Data loads → Result
```

**Expansion Pack Implication**: Respect the pipeline architecture.

### Task Authority Pattern

Tasks override agent behavioral constraints:

```yaml
# Agent says: "I need more context"
# Task says: "Execute with this specific data"
# Result: Task authority wins - ensures consistent execution
```

**Expansion Pack Implication**: Use tasks to enforce consistent behavior regardless of agent personality.

### Interactive Validation Requirements

System prevents progress without required information through interactive prompts:

```yaml
# Cannot proceed without:
- Project requirements
- Technical context  
- User preferences

# Interactive validation ensures quality
```

**Expansion Pack Implication**: Build validation gates that enforce critical information gathering through user interaction.

### Template Minimalism with Examples

Core templates are minimal, examples are comprehensive:

```yaml
template:
  required: [2-3 fields]
  optional: [documented in examples]
```

**Expansion Pack Implication**: Keep requirements minimal, document possibilities.

### Multi-Modal Agent Design

Agents support different interaction modes:

```yaml
modes:
  - normal: Standard commands
  - kb: Knowledge consultation
  - chat: Conversational
  - yolo: Skip confirmations
```

**Expansion Pack Implication**: Design agents with multiple interaction styles.

### Advisory vs Blocking QA Pattern

v5.0 QA is guidance, not gates:

```yaml
# Traditional: QA blocks progress
# BMad: QA provides structured feedback in separate chat

qa_approach:
  type: advisory
  format: structured_artifacts
  timing: optional_parallel
```

**Expansion Pack Implication**: Design quality assurance as helpful guidance, not blocking gates.

### Manual State Management Philosophy

No automated state transitions:

```yaml
# User explicitly:
- Saves files manually (Web UI)
- Updates status fields
- Decides when to progress
- Controls handoffs
```

**Expansion Pack Implication**: Give users explicit control over workflow progression.

### Configuration-Driven Behavior Changes

System behavior changes via config:

```yaml
markdownExploder: true   # Uses external tool
markdownExploder: false  # Uses AI sharding
```

**Expansion Pack Implication**: Make behaviors switchable through configuration.

## Expansion Pack Design Guidelines

Based on these patterns, successful expansion packs should:

### 1. Respect the Architecture

- Use configuration for customization points
- Keep resources task-exclusive when possible
- Implement progressive disclosure in templates
- Create behavioral modifiers through data files

### 2. Optimize for Context

- Lazy load everything possible
- Use granular section loading
- Keep agents lean, tasks rich
- Load only what's needed, when needed

### 3. Design for Humans

- Use personification for memorable agents
- Provide both interactive and YOLO modes
- Support fuzzy command matching
- Create conversational experiences

### 4. Enable Flexibility

- Make paths configurable
- Use runtime binding
- Support parameter injection
- Allow behavioral switching via config

### 5. Distribute Intelligence

- Embed instructions in components
- Let tasks be knowledge brokers
- Use templates to guide generation
- Create smart checklists, not just lists

### 6. Follow the Patterns

- Configuration over code changes
- Progressive complexity revelation
- Human-friendly abstractions
- Context minimalism throughout
- Indirect knowledge transfer
- Runtime flexibility
- Role-based specialization

## Pattern Categories Summary

### Resource Management
- Data as behavioral modifiers
- Task-exclusive loading
- Granular section access
- Strategic lazy loading

### Interaction Design
- Interactive dialogues
- Runtime binding
- Parameter injection
- Fuzzy resolution
- Independent session requirements
- Interactive validation enforcement

### Configuration Architecture
- Config-first design
- Namespace traversal
- Pluggable storage
- Role customization
- Strategy pattern without classes

### Design Philosophy
- Progressive disclosure
- Personification strategy
- Pragmatic shortcuts
- Embedded intelligence
- Advisory vs blocking approach
- Manual state management

### Quality & Control
- Task authority pattern
- Permission-based editing
- Interactive validation requirements
- Artifact-driven handoffs

## Conclusion

BMad's architecture isn't just a collection of features - it's a coherent system of patterns that work together to create an extensible, efficient, and human-friendly framework. Understanding these patterns is essential for building expansion packs that feel native to the BMad ecosystem.

The key insight: **BMad achieves power through constraint**. By following consistent patterns and respecting architectural boundaries, the system remains predictable, extensible, and efficient. Expansion packs that embrace these patterns will integrate seamlessly and provide the best user experience.

## Quick Reference: Pattern Checklist for Expansion Packs

### Architecture & Integration
- [ ] Configuration-driven, not hardcoded
- [ ] Lazy loading with selective access
- [ ] Task-exclusive resources where appropriate
- [ ] Respect the command-task-data pipeline
- [ ] Design for independent agent sessions
- [ ] Use tasks for behavioral authority

### User Experience
- [ ] Progressive disclosure in interfaces
- [ ] Personified, memorable agents
- [ ] Interactive and batch modes
- [ ] Fuzzy command matching
- [ ] Support both novice and expert modes
- [ ] Manual state management (user control)

### Quality & Behavior
- [ ] Advisory guidance over blocking gates
- [ ] Elicitation enforcement at critical points
- [ ] Embedded intelligence in components
- [ ] Runtime binding over static config
- [ ] Permission-based section editing
- [ ] Optimize for minimal context usage

### Expansion Pack Specific
- [ ] Artifact-driven state transfer
- [ ] Interactive validation where needed
- [ ] Strategy pattern implementation
- [ ] Multi-modal interaction design