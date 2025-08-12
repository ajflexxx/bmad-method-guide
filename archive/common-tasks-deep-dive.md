# Deep Dive: Common Tasks in BMad - The Universal Processors

## Overview

Common tasks in BMad are foundational, reusable task definitions that serve as universal processors for all agents and workflows. Unlike domain-specific tasks in the `bmad-core/tasks/` directory, common tasks provide generic capabilities that work across all contexts, teams, and expansion packs. They are the "primitives" that enable consistent document creation and validation throughout the system.

## Common Tasks Architecture

### Location and Purpose

```
/workspace/common/
├── tasks/
│   ├── create-doc.md       # Universal document generator
│   └── execute-checklist.md # Universal checklist processor
└── utils/
    ├── workflow-management.md   # Workflow orchestration utilities
    └── bmad-doc-template.md    # Template specification guide
```

**Key Distinction**: 
- **Common tasks**: Generic, reusable across all contexts
- **BMad-core tasks**: Domain-specific, project-focused
- **Utils**: Supporting documentation and specifications

## Task #1: create-doc.md - The Document Factory

### Purpose and Philosophy

`create-doc.md` is BMad's universal document generator that transforms YAML templates into interactive, AI-generated documents. It enforces a strict interactive workflow that ensures human oversight and quality control throughout the document creation process.

### Core Architecture

```markdown
# Critical Execution Model
1. DISABLE ALL EFFICIENCY OPTIMIZATIONS
2. MANDATORY STEP-BY-STEP EXECUTION  
3. ELICITATION IS REQUIRED when elicit: true
4. NO SHORTCUTS ALLOWED
```

**Design Philosophy**:
- **Human-in-the-loop**: Every section requires human review
- **Interactive by default**: Forces engagement, not automation
- **Quality over speed**: Thoroughness beats efficiency
- **Violation detection**: Clear indicators when process violated

### The Elicitation Pattern

The most sophisticated aspect of create-doc:

```markdown
When elicit: true:
1. Present section content
2. Provide detailed rationale
3. STOP and present numbered options 1-9:
   - Option 1: "Proceed to next section"
   - Options 2-9: Select from elicitation-methods
4. WAIT FOR USER RESPONSE
```

**Why This Pattern**:
- **Consistent interface**: Always 1-9 options
- **Method reuse**: Pulls from elicitation-methods data
- **User control**: Can proceed or dig deeper
- **No ambiguity**: Clear numbered choices

### Processing Flow

```yaml
# Execution sequence
1. Parse YAML template
2. Set preferences (mode, output file)
3. For each section:
   a. Check conditions
   b. Verify agent permissions
   c. Draft content
   d. Present with rationale
   e. IF elicit: Apply 1-9 options
   f. Save to file
4. Continue until complete
```

### Agent Permission System

create-doc respects agent ownership:

```yaml
section:
  owner: architect      # Who creates initially
  editors: [architect]  # Who can modify
  readonly: false      # Can be changed later?
```

**Permission Enforcement**:
- Notes restricted sections in output
- Adds ownership comments to document
- Prevents unauthorized modifications
- Maintains accountability chain

### YOLO Mode

Alternative execution path:
- User types `#yolo`
- Processes all sections at once
- Still respects conditions and permissions
- Trades interaction for speed

### Why Common?

create-doc is common because:
1. **Template agnostic**: Works with any YAML template
2. **Agent neutral**: Any agent can use it
3. **Domain independent**: No project-specific logic
4. **Universally needed**: All documents need creation

## Task #2: execute-checklist.md - The Validator

### Purpose and Philosophy

`execute-checklist.md` is the universal validation engine that ensures document quality and completeness. It processes any checklist against any document, providing consistent validation across all BMad components.

### Validation Architecture

```markdown
# Two execution modes
1. Interactive Mode (section by section)
2. YOLO Mode (all at once)
```

**Design Choices**:
- **Flexible validation**: Works with any checklist format
- **Progressive disclosure**: Can validate incrementally
- **Clear reporting**: Highlights warnings, errors, N/A items
- **Action-oriented**: Suggests corrections

### Processing Pattern

```yaml
# Validation flow
1. Initial Assessment
   - Fuzzy match checklist name
   - Load appropriate checklist
   - Confirm execution mode
   
2. Document Gathering
   - Each checklist specifies requirements
   - Gather specified artifacts
   - Resolve file locations
   
3. Checklist Processing
   - Work through each section
   - Check items against documents
   - Report findings
   - Get user confirmation
   
4. Final Report
   - Summary of all findings
   - Action items
   - Compliance status
```

### Validation Approaches

For each checklist item:

```markdown
1. Read requirement
2. Locate relevant content
3. Assess compliance:
   - ✅ Meets requirement
   - ⚠️ Partially meets
   - ❌ Does not meet
   - ➖ Not applicable
4. Document rationale
5. Suggest improvements
```

### Why Common?

execute-checklist is common because:
1. **Checklist agnostic**: Works with any validation list
2. **Document neutral**: Validates any content type
3. **Process independent**: No workflow assumptions
4. **Universal need**: All documents need validation

## Utils: Supporting Infrastructure

### workflow-management.md - The Orchestrator Guide

**Purpose**: Documents how BMad orchestrator manages workflows

**Key Capabilities**:
```markdown
/workflows - List available workflows
/workflow-start {id} - Begin workflow
/workflow-status - Check progress
/workflow-resume - Continue from checkpoint
/workflow-next - Show next step
```

**Design Patterns**:
- **Dynamic loading**: Workflows loaded from team config
- **State tracking**: Maintains workflow position
- **Context passing**: Preserves artifacts between stages
- **Interruption handling**: Can resume from any point

**Why a Util?**:
- Documentation, not executable
- Guides orchestrator behavior
- Reference for workflow commands
- Not directly invoked

### bmad-doc-template.md - The Template Specification

**Purpose**: Defines YAML template structure for documents

**Core Structure**:
```yaml
template:
  id: identifier
  name: Human name
  version: 1.0
  output:
    format: markdown
    filename: path/to/file.md
    
sections:
  - id: section-id
    title: Section Title
    instruction: LLM guidance
    elicit: true/false
    condition: when to include
```

**Key Features**:
- **Variable substitution**: `{{variable}}`
- **Conditional sections**: Based on project needs
- **Mermaid diagrams**: Visual representations
- **Agent permissions**: Ownership control
- **Repeatable sections**: For lists, items

**Why a Util?**:
- Specification document
- Template creation guide
- Not executable task
- Reference material

## Common vs Core Tasks Distinction

### Common Tasks Characteristics

| Aspect | Common Tasks | Core Tasks |
|--------|-------------|------------|
| **Scope** | Universal | Domain-specific |
| **Dependencies** | Minimal | May require others |
| **Templates** | Any YAML | Specific templates |
| **Agents** | All can use | May be agent-specific |
| **Logic** | Generic processing | Business logic |
| **Reusability** | 100% reusable | Project-focused |

### Examples Comparison

**Common Task** (create-doc):
```markdown
# Works with ANY template
- Parse any YAML structure
- Generate any document type
- Apply any elicitation
- Save to any location
```

**Core Task** (create-next-story):
```markdown
# Specific to story creation
- Requires sharded PRD
- Uses story template only
- Applies story logic
- Saves to story location
```

## Design Patterns in Common Tasks

### 1. **Strict Enforcement Pattern**

```markdown
⚠️ CRITICAL EXECUTION NOTICE ⚠️
DISABLE ALL EFFICIENCY OPTIMIZATIONS
MANDATORY STEP-BY-STEP EXECUTION
```

**Purpose**: Prevent AI from taking shortcuts
**Implementation**: Clear violation indicators
**Result**: Consistent user experience

### 2. **Interactive Elicitation Pattern**

```markdown
1. Present content
2. Explain rationale
3. Offer numbered choices (1-9)
4. Wait for response
```

**Purpose**: Ensure human oversight
**Implementation**: Forced stop points
**Result**: Quality control

### 3. **Fuzzy Matching Pattern**

```markdown
If user provides "architecture checklist":
- Try fuzzy match → "architect-checklist"
- If multiple matches → ask for clarification
- Load appropriate resource
```

**Purpose**: User-friendly input
**Implementation**: Intelligent resolution
**Result**: Reduced friction

### 4. **Mode Toggle Pattern**

```markdown
Interactive Mode: Step-by-step
YOLO Mode: All at once (#yolo to toggle)
```

**Purpose**: Balance thoroughness and speed
**Implementation**: User-controlled switching
**Result**: Flexible execution

## Integration Points

### How Common Tasks Connect

1. **With Agents**:
   - Agents invoke create-doc with templates
   - Agents use execute-checklist for validation
   - Common tasks respect agent permissions

2. **With Templates**:
   - create-doc parses template YAML
   - Follows template instructions
   - Applies template conditions

3. **With Workflows**:
   - Workflows trigger agents
   - Agents use common tasks
   - Results flow back to workflow

4. **With Configuration**:
   - Tasks read core-config for paths
   - Save to configured locations
   - Respect sharding settings

## Extension Patterns

### Creating New Common Tasks

Criteria for common tasks:
1. **Universal applicability**: Works across domains
2. **No domain logic**: Pure processing
3. **Template/data driven**: Configurable behavior
4. **Reusable pattern**: Used by multiple agents

Template for new common task:
```markdown
# Task Name

## Purpose
Universal [function] for all agents

## Critical Execution Rules
- Rule 1: [Enforcement]
- Rule 2: [Requirement]

## Processing Flow
1. Step 1
2. Step 2
3. Step 3

## Integration
- Works with: [components]
- Requires: [dependencies]
- Outputs: [results]
```

## Best Practices

### For Using Common Tasks

1. **Respect the Process**:
   - Don't skip interactive steps
   - Follow elicitation requirements
   - Honor agent permissions

2. **Provide Clear Input**:
   - Specify template clearly
   - Name checklists accurately
   - Set preferences upfront

3. **Handle Outputs Properly**:
   - Save to correct locations
   - Maintain document structure
   - Preserve formatting

### For Extending Common Tasks

1. **Maintain Universality**:
   - No project-specific logic
   - No domain assumptions
   - No hardcoded paths

2. **Preserve Patterns**:
   - Keep enforcement mechanisms
   - Maintain interaction models
   - Follow existing structures

3. **Document Thoroughly**:
   - Clear execution rules
   - Complete flow documentation
   - Integration points defined

## Troubleshooting Common Tasks

### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| Document created without interaction | YOLO mode or violation | Check mode, enforce interaction |
| Checklist not found | Name mismatch | Use fuzzy matching |
| Template parse error | Invalid YAML | Validate template structure |
| Permission denied | Agent restrictions | Check owner/editors fields |
| Elicitation skipped | Missing elicit flag | Set elicit: true in template |

### Debugging Strategies

1. **Check Execution Mode**:
   - Interactive vs YOLO
   - Verify user didn't toggle

2. **Validate Inputs**:
   - Template structure
   - Checklist format
   - File paths

3. **Review Permissions**:
   - Agent ownership
   - Editor lists
   - Readonly flags

4. **Trace Execution**:
   - Follow processing flow
   - Check each step
   - Identify failure point

## Summary

Common tasks are the foundational processors that enable BMad's document-centric workflow. They provide:

- **Universal capabilities** that work across all domains
- **Consistent interfaces** for document creation and validation
- **Strict enforcement** of quality control processes
- **Flexible execution** modes for different scenarios
- **Clear integration** points with other components

Understanding common tasks is essential for:
- Using BMad effectively across projects
- Creating quality documents consistently
- Validating outputs thoroughly
- Extending BMad with new capabilities
- Troubleshooting document generation issues

The common task system embodies BMad's core principle: universal patterns with strict quality control enable consistent, high-quality output across diverse projects and domains.