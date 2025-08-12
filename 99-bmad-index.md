# BMad Documentation Index - Complete Table of Contents

## Document Organization Overview

Documents are organized in numbered sections for optimal learning progression:
- **01-09**: Foundations and overview
- **10-19**: Core component specifications
- **20-29**: System interaction patterns
- **30-39**: Implementation guides
- **90-99**: Reference materials

---

## Section 1: Foundations (01-09)

### 01-bmad-overview.md
**Purpose:** Entry point and navigation guide for all BMad documentation  
**Topics:**
- System overview and purpose
- Quick architecture diagram
- Navigation paths for different audiences
- Quick start guides
**Related:** All documents (serves as entry point)  
**Keywords:** overview, navigation, quick start, entry point, introduction

### 02-bmad-philosophy-architecture.md
**Purpose:** Core principles, methodology, and system architecture  
**Topics:**
- BMad philosophy and principles
- System architecture overview
- Component relationships
- Design decisions and rationale
**Related:** 01-bmad-overview.md, all component references (10-17)  
**Keywords:** philosophy, architecture, principles, design, methodology, core concepts

---

## Section 2: Core Components (10-19)

### 10-bmad-agents-reference.md
**Purpose:** Complete specification of the BMad agent system  
**Topics:**
- Agent anatomy and structure
- Persona system and behavioral definition
- Command architecture
- Dependencies and resource loading
- Agent activation patterns
**Related:** 11-bmad-tasks-reference.md, 20-bmad-workflow-patterns.md, 15-bmad-teams-reference.md  
**Keywords:** agent, persona, commands, dependencies, activation, identity, orchestrator

### 11-bmad-tasks-reference.md
**Purpose:** Comprehensive guide to BMad tasks (bmad-core and common)  
**Topics:**
- Task structure and frontmatter
- BMad-core tasks specification
- Common tasks specification
- Interactive vs automated tasks
- Task execution patterns
**Related:** 10-bmad-agents-reference.md, 12-bmad-templates-reference.md, 23-bmad-distinction-patterns.md  
**Keywords:** task, execution, procedures, frontmatter, interactive, automated, create-doc

### 12-bmad-templates-reference.md
**Purpose:** Template system for structured document creation  
**Topics:**
- Template YAML structure
- Section types and behaviors
- Elicitation configuration
- Template-task integration
- Output formatting
**Related:** 11-bmad-tasks-reference.md, 21-bmad-document-patterns.md  
**Keywords:** template, YAML, sections, elicitation, document schema, structure

### 13-bmad-checklists-reference.md
**Purpose:** Checklist structure and validation system  
**Topics:**
- Checklist anatomy
- LLM instruction blocks
- Conditional sections
- Validation criteria
- Skip logic patterns
**Related:** 22-bmad-execution-patterns.md, 10-bmad-agents-reference.md  
**Keywords:** checklist, validation, quality gates, LLM instructions, conditional, criteria

### 14-bmad-workflows-reference.md
**Purpose:** Workflow orchestration and multi-agent coordination  
**Topics:**
- Workflow YAML structure
- Agent sequencing
- Conditional execution
- Input/output specifications
- Greenfield vs Brownfield patterns
**Related:** 20-bmad-workflow-patterns.md, 10-bmad-agents-reference.md, 15-bmad-teams-reference.md  
**Keywords:** workflow, orchestration, sequence, multi-agent, coordination, pipeline

### 15-bmad-teams-reference.md
**Purpose:** Agent team bundles and configurations  
**Topics:**
- Team YAML structure
- Team composition patterns
- Capability matching
- Bundle distribution
- Team selection criteria
**Related:** 10-bmad-agents-reference.md, 14-bmad-workflows-reference.md  
**Keywords:** team, bundle, agents, composition, capability, distribution

### 16-bmad-configuration-reference.md
**Purpose:** Core configuration parameters and settings  
**Topics:**
- Configuration parameter reference
- Document location management
- Sharding configuration
- Environment settings
- File pattern configurations
**Related:** All components reference configurations  
**Keywords:** configuration, core-config, settings, parameters, sharding, paths

### 17-bmad-data-reference.md
**Purpose:** Data resources and knowledge base  
**Topics:**
- Data resource types
- Knowledge organization
- Elicitation methods
- Best practices storage
- Domain knowledge structure
**Related:** 11-bmad-tasks-reference.md, 23-bmad-distinction-patterns.md  
**Keywords:** data, resources, knowledge, elicitation methods, best practices, domain

---

## Section 3: System Interactions (20-29)

### 20-bmad-workflow-patterns.md
**Purpose:** How workflows interact with system components  
**Topics:**
- Workflow-agent interactions
- Workflow-configuration integration
- Document handoff patterns
- State management
- Composition and chaining
**Related:** 14-bmad-workflows-reference.md, 21-bmad-document-patterns.md  
**Keywords:** workflow patterns, interactions, state, handoff, composition

### 21-bmad-document-patterns.md
**Purpose:** Document lifecycle and flow through system  
**Topics:**
- Document creation flow
- Sharding and reconstruction
- Version control patterns
- Document validation checkpoints
- Document movement through workflows
**Related:** 12-bmad-templates-reference.md, 20-bmad-workflow-patterns.md  
**Keywords:** document lifecycle, sharding, flow, version control, validation

### 22-bmad-execution-patterns.md
**Purpose:** Checklist execution patterns and enforcement  
**Topics:**
- Execution trigger patterns
- Enforcement mechanisms
- Task-mediated access
- Validation workflows
- Interactive vs comprehensive modes
**Related:** 13-bmad-checklists-reference.md, 11-bmad-tasks-reference.md  
**Keywords:** execution, checklist execution, enforcement, triggers, validation

### 23-bmad-distinction-patterns.md
**Purpose:** Critical distinction between tasks and data  
**Topics:**
- Task vs data characteristics
- When to use tasks vs data
- Resource loading patterns
- Decision criteria
- Common mistakes to avoid
**Related:** 11-bmad-tasks-reference.md, 17-bmad-data-reference.md  
**Keywords:** distinction, tasks vs data, patterns, decision criteria, resource types

---

## Section 4: Implementation Guides (30-39)

### 30-bmad-usage-guide.md
**Purpose:** Complete end-to-end user journey  
**Topics:**
- Planning workflow (Web UI)
- Development cycle (IDE)
- User decision points
- Cost optimization strategies
- Common paths through system
**Related:** 32-bmad-troubleshooting-guide.md, 01-bmad-overview.md  
**Keywords:** usage, user journey, workflow, planning, development, implementation

### 31-bmad-expansion-guide.md
**Purpose:** Creating domain-specific expansion packs  
**Topics:**
- Folder structure requirements
- Creating domain agents
- Building custom tasks
- Designing templates
- Testing and distribution
**Related:** All component references (10-17), 30-bmad-usage-guide.md  
**Keywords:** expansion pack, creation, domain-specific, custom agents, distribution

### 32-bmad-troubleshooting-guide.md
**Purpose:** Problem solving and debugging reference  
**Topics:**
- Common error patterns
- Agent activation issues
- Dependency conflicts
- Workflow execution problems
- Configuration troubleshooting
**Related:** 30-bmad-usage-guide.md, 16-bmad-configuration-reference.md  
**Keywords:** troubleshooting, debugging, errors, problems, solutions, fixes

---

## Section 5: Reference (90-99)

### 90-bmad-glossary.md
**Purpose:** Complete glossary of BMad-specific terms  
**Topics:**
- Core component definitions
- Pattern explanations
- Execution concepts
- File formats
- Integration terms
**Related:** All documents (defines terminology used throughout)  
**Keywords:** glossary, definitions, terms, terminology, vocabulary

### 99-bmad-index.md (This Document)
**Purpose:** Master table of contents with descriptions and cross-references  
**Topics:**
- Complete document listing
- Purpose and topic summaries
- Cross-reference matrix
- Keyword index
**Related:** All documents (provides navigation)  
**Keywords:** index, table of contents, navigation, cross-reference, documentation map

---

## Cross-Reference Matrix

### Planning Phase Documents
- 02-bmad-philosophy-architecture.md → 10-bmad-agents-reference.md
- 10-bmad-agents-reference.md → 11-bmad-tasks-reference.md → 12-bmad-templates-reference.md
- 30-bmad-usage-guide.md → 20-bmad-workflow-patterns.md

### Development Phase Documents
- 14-bmad-workflows-reference.md → 20-bmad-workflow-patterns.md
- 21-bmad-document-patterns.md → 22-bmad-execution-patterns.md
- 13-bmad-checklists-reference.md → 22-bmad-execution-patterns.md

### Expansion Pack Creation
- 31-bmad-expansion-guide.md references all component specs (10-17)
- 31-bmad-expansion-guide.md → 23-bmad-distinction-patterns.md
- 31-bmad-expansion-guide.md → 32-bmad-troubleshooting-guide.md

### Configuration Dependencies
- 16-bmad-configuration-reference.md ← All components
- 16-bmad-configuration-reference.md → 21-bmad-document-patterns.md
- 16-bmad-configuration-reference.md → 32-bmad-troubleshooting-guide.md

---

## Quick Reference by Goal

### "I want to use BMad"
Start: 01-bmad-overview.md → 30-bmad-usage-guide.md

### "I want to create an expansion pack"
Start: 01-bmad-overview.md → 31-bmad-expansion-guide.md → Component references (10-17)

### "I want to understand how BMad works"
Start: 01-bmad-overview.md → 02-bmad-philosophy-architecture.md → Component references (10-17) → Patterns (20-23)

### "I'm debugging an issue"
Start: 32-bmad-troubleshooting-guide.md → 90-bmad-glossary.md → Relevant component reference

### "I'm an AI agent processing BMad"
Start: 99-bmad-index.md → 90-bmad-glossary.md → Relevant documents by keyword