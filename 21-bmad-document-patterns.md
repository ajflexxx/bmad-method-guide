# Deep Dive: Document Lifecycle in BMad - From Creation to Implementation

## Overview

The document lifecycle in BMad represents the complete journey of project artifacts from initial creation through sharding, validation, implementation, and maintenance. Documents are not just static files but living entities that evolve through well-defined states, undergo transformations, and maintain relationships with other artifacts. Understanding this lifecycle is crucial for managing complex projects and ensuring document integrity throughout development.

## Document Lifecycle Stages

### The Complete Journey

```mermaid
graph LR
    A[Conception] --> B[Creation]
    B --> C[Validation]
    C --> D[Sharding]
    D --> E[Distribution]
    E --> F[Implementation]
    F --> G[Review]
    G --> H[Archive]
    
    C -->|Failed| B
    G -->|Updates| F
    
    style A fill:#E6F3FF
    style B fill:#FFE6E6
    style C fill:#FFFACD
    style D fill:#E6FFE6
    style E fill:#FFE6F3
    style F fill:#F0E6FF
    style G fill:#FFE6E6
    style H fill:#E6E6E6
```

## Stage 1: Conception

### Document Planning

Before creation, documents are conceived through:

```yaml
# Workflow defines what documents needed
workflow:
  sequence:
    - agent: analyst
      creates: project-brief.md  # Planned output
    - agent: pm
      creates: prd.md           # Planned output
```

**Conception Elements**:
- **Purpose definition**: Why document needed
- **Template selection**: Which template to use
- **Owner assignment**: Who creates it
- **Location planning**: Where it will live

### Document Types in BMad

| Document Type | Purpose | Template | Owner |
|--------------|---------|----------|-------|
| **project-brief.md** | Initial requirements | project-brief-tmpl | analyst |
| **prd.md** | Product requirements | prd-tmpl | pm |
| **architecture.md** | Technical design | architecture-tmpl | architect |
| **front-end-spec.md** | UI/UX specification | front-end-spec-tmpl | ux-expert |
| **story-*.md** | Implementation tasks | story-tmpl | sm |

## Stage 2: Creation

### Creation Patterns

Documents are created through three primary patterns:

#### 1. Template-Driven Creation

```yaml
# Agent uses create-doc task with template
agent: pm
command: create-doc
template: prd-tmpl.yaml
output: docs/prd.md
```

**Process**:
1. Load YAML template
2. Process each section
3. Apply elicitation
4. Generate content
5. Save to file

#### 2. Task-Driven Creation

```yaml
# Agent uses specific task
agent: sm
task: create-next-story
inputs: sharded PRD
output: docs/stories/epic-1/story-1.md
```

**Process**:
1. Execute task logic
2. Gather inputs
3. Apply business rules
4. Generate document
5. Save to location

#### 3. Direct Creation

```yaml
# Agent creates directly
agent: architect
action: write architecture
method: custom logic
output: docs/architecture.md
```

### Creation Metadata

Every document includes metadata:

```markdown
---
title: Product Requirements Document
version: 1.0
created_by: pm
created_at: 2024-01-15
status: draft
dependencies:
  - project-brief.md
---
```

## Stage 3: Validation

### Validation Layers

Documents undergo multiple validation layers:

#### 1. Structural Validation

```yaml
# Checklist validates structure
- Has executive summary
- Contains all required sections
- Includes success metrics
- Defines scope clearly
```

#### 2. Content Validation

```yaml
# PO validates content quality
- Requirements are clear
- No contradictions
- Technically feasible
- Business value defined
```

#### 3. Dependency Validation

```yaml
# Validate dependencies met
dependencies:
  - project-brief.md exists
  - version compatibility
  - no circular dependencies
```

### Validation Gates

```mermaid
sequenceDiagram
    participant D as Document
    participant PO as PO Agent
    participant C as Checklist
    participant W as Workflow
    
    D->>PO: Request validation
    PO->>C: Load checklist
    C->>PO: Validation rules
    PO->>D: Apply rules
    PO->>W: Report results
    
    alt Validation Passed
        W->>D: Mark validated
        W->>W: Continue workflow
    else Validation Failed
        W->>D: Return for fixes
        W->>W: Loop back
    end
```

### Validation Status Tracking

```yaml
validation:
  status: passed
  checked_by: po
  checklist: po-master-checklist
  issues_found: []
  timestamp: 2024-01-15T10:30:00Z
```

## Stage 4: Sharding

### Why Sharding?

Large documents are sharded for:
- **AI token limits**: Keep under context windows
- **Parallel processing**: Multiple agents work simultaneously
- **Focused context**: Relevant sections only
- **Performance**: Faster processing

### Sharding Process

#### Automatic Sharding (md-tree)

```bash
# When markdownExploder: true
md-tree explode docs/prd.md docs/prd/

# Results in:
docs/prd/
├── executive-summary.md
├── project-overview.md
├── epic-1-user-auth.md
├── epic-2-payments.md
└── non-functional.md
```

#### Manual Sharding

```yaml
# When markdownExploder: false
1. Parse document structure
2. Identify H2 sections
3. Create folder structure
4. Split into files
5. Maintain references
```

### Sharding Rules

```yaml
sharding_rules:
  split_level: 2  # Split at H2 headers
  min_size: 100   # Minimum lines for shard
  max_size: 1000  # Maximum lines per shard
  preserve:
    - code_blocks: true
    - tables: true
    - diagrams: true
```

### Shard Naming Convention

```
Original: docs/prd.md
Sharded:  docs/prd/
          ├── 01-executive-summary.md
          ├── 02-project-overview.md
          ├── 03-epic-1-user-authentication.md
          ├── 04-epic-2-payment-processing.md
          └── 05-non-functional-requirements.md
```

## Stage 5: Distribution

### v5.0 Quality Documentation Pattern

BMad v5.0 introduces a new pattern for quality documentation that maintains traceability and accountability:

#### Quality Document Organization

```
docs/
├── qa/                          # NEW in v5.0
│   ├── gates/                  # Quality gate decisions
│   │   ├── 1.1-user-auth.yml
│   │   ├── 1.2-dashboard.yml
│   │   └── 1.3-payments.yml
│   ├── risks/                  # Risk assessments
│   │   ├── epic-1-risks.yml
│   │   └── epic-2-risks.yml
│   ├── test-design/            # Test architectures
│   │   ├── epic-1-tests.yml
│   │   └── story-1.1-tests.yml
│   ├── coverage/               # Requirements traceability
│   │   ├── epic-1-coverage.yml
│   │   └── story-1.1-coverage.yml
│   └── waivers/                # Waiver documentation
│       └── 2024-Q1-waivers.yml
├── stories/                     # Existing story structure
├── prd/                        # Existing PRD shards
└── architecture/               # Existing architecture
```

#### Quality Gate File Pattern

```yaml
# docs/qa/gates/1.1-user-auth.yml
schema: 1
story: "1.1"
title: "User Authentication"
gate: CONCERNS
status_reason: "Test coverage at 72%, below 80% target"
reviewer: "Quinn"
updated: "2025-08-16T10:00:00Z"

quality_score:
  test_coverage: 72
  code_quality: 85
  documentation: 90
  overall: 82

top_issues:
  - priority: MEDIUM
    category: TEST_COVERAGE
    issue: "Missing edge case tests for session timeout"
    recommendation: "Add timeout scenario tests"
    risk_if_unaddressed: "Users may experience unexpected logouts"

waiver:
  active: true
  reason: "Low-risk feature, deadline critical"
  approved_by: "Product Owner"
  risk_accepted: true
  follow_up: "Address in next sprint - ticket #123"
```

#### Waiver Documentation Pattern

```yaml
# docs/qa/waivers/2024-Q1-waivers.yml
waivers:
  - story: "1.1"
    date: "2024-01-15"
    gate_status: CONCERNS
    waived_issues:
      - "Test coverage below target"
      - "Performance metrics not validated"
    business_justification: "Critical customer demo"
    risk_assessment: "Low - monitoring in place"
    follow_up_status: "Resolved in 1.1.1"
    
  - story: "2.3"
    date: "2024-02-20"
    gate_status: FAIL
    waived_issues:
      - "Security scan findings"
    business_justification: "False positives confirmed"
    risk_assessment: "None - verified safe"
    follow_up_status: "No action needed"
```

### Distribution Patterns

Sharded documents are distributed to agents:

```yaml
# SM agent receives specific epic
sm:
  receives: docs/prd/03-epic-1-user-authentication.md
  creates: docs/stories/epic-1/

# Dev agent receives story + context
dev:
  receives:
    - docs/stories/epic-1/story-1.md
    - docs/architecture/tech-stack.md
    - docs/architecture/coding-standards.md
```

### Context Assembly

```yaml
# Configuration defines always-loaded files
devLoadAlwaysFiles:
  - docs/architecture/coding-standards.md
  - docs/architecture/tech-stack.md
  - docs/architecture/source-tree.md

# Plus specific story context
story_context:
  - current_story.md
  - related_epic.md
  - relevant_architecture.md
```

### Distribution Tracking

```yaml
distribution_log:
  - document: epic-1.md
    distributed_to: [sm, dev]
    timestamp: 2024-01-15T11:00:00Z
  - document: story-1.md
    distributed_to: [dev, qa]
    timestamp: 2024-01-15T14:00:00Z
```

## Stage 6: Implementation

### Story Implementation Flow

```mermaid
stateDiagram-v2
    [*] --> Draft: SM creates
    Draft --> Approved: PM/Analyst review
    Approved --> InProgress: Dev starts
    InProgress --> Implemented: Dev completes
    Implemented --> Review: QA reviews
    Review --> Done: QA approves
    
    Review --> InProgress: Issues found
    Approved --> Draft: Changes needed
```

### Document Updates During Implementation

```yaml
# Story document evolves
story.md:
  v1: Initial draft from SM
  v2: Approved by PM
  v3: Dev adds implementation notes
  v4: Dev updates with file list
  v5: QA adds review comments
  v6: Final version marked Done
```

### File List Tracking

Stories track implementation changes:

```markdown
## File List
- Created: src/auth/login.component.ts
- Modified: src/app/app.module.ts
- Created: src/auth/auth.service.ts
- Modified: src/api/endpoints.ts
- Created: tests/auth/login.spec.ts
```

## Stage 7: Review

### Review Layers

#### QA Review

```yaml
qa_review:
  story: story-1.md
  checks:
    - code_quality
    - test_coverage
    - documentation
    - security
  results:
    - passed: [code_quality, documentation]
    - failed: [test_coverage]
    - actions: Add unit tests for auth service
```

#### Architectural Review

```yaml
architect_review:
  implementation: epic-1-implementation
  checks:
    - follows_patterns
    - uses_approved_libraries
    - maintains_consistency
  feedback: Consider using repository pattern
```

### Review Status Tracking

```markdown
## Review Status
- [ ] Code review passed
- [x] Tests written
- [x] Documentation updated
- [ ] Security review complete
- [ ] Performance validated
```

## Stage 8: Archive

### Document Persistence

BMAD relies on git for document versioning and history:

```bash
project/
├── docs/           # All project documents
│   ├── stories/    # Implementation stories
│   ├── prd/        # Sharded PRD documents
│   ├── architecture/ # Sharded architecture
│   └── qa/         # Quality gate files
```

### Version Tracking

Version tracking is limited to:
- Template versions (e.g., v2.0 for most templates)
- Story status field updates
- Manual version notes in document metadata

**Note**: No automated archive system exists. Document history is maintained through git commits and manual status updates.

## Document Relationships

### Dependency Graph

```mermaid
graph TD
    PB[project-brief.md]
    PRD[prd.md]
    ARCH[architecture.md]
    FE[front-end-spec.md]
    
    E1[epic-1.md]
    E2[epic-2.md]
    
    S1[story-1.md]
    S2[story-2.md]
    S3[story-3.md]
    
    PB --> PRD
    PRD --> ARCH
    PRD --> FE
    PRD --> E1
    PRD --> E2
    
    E1 --> S1
    E1 --> S2
    E2 --> S3
    
    ARCH --> S1
    ARCH --> S2
    ARCH --> S3
    
    style PB fill:#FFE4B5
    style PRD fill:#FFE4B5
    style ARCH fill:#E4E4FF
    style FE fill:#FFE4E4
```

### Cross-References

Documents maintain references:

```markdown
<!-- In story-1.md -->
## References
- Epic: [User Authentication](../prd/epic-1.md)
- Architecture: [Auth Design](../architecture/auth-design.md)
- API Spec: [Auth Endpoints](../api/auth-endpoints.md)
```

## Document State Management

### Story State Transitions

Document states are tracked in story templates but managed manually:

```yaml
# From story template - actual states available
status:
  type: choice
  choices: [Draft, Approved, InProgress, Review, Done]
  description: "Current status of the story"
```

```mermaid
stateDiagram-v2
    [*] --> Draft: SM creates story
    Draft --> Approved: PM/Analyst approve
    Approved --> InProgress: Dev begins work
    InProgress --> Review: Dev completes
    Review --> Done: QA approves
    
    Review --> InProgress: Issues found
    Approved --> Draft: Changes needed
```

**Note**: State transitions are managed manually by agents updating the status field. There is no automated state machine system.

## Document Transformation Pipeline

### Transformation Steps

```yaml
transformations:
  - step: create
    input: template + context
    output: draft document
    
  - step: validate
    input: draft document
    output: validated document
    
  - step: shard
    input: validated document
    output: document fragments
    
  - step: enrich
    input: fragments
    output: contextualized fragments
    
  - step: implement
    input: contextualized fragments
    output: implemented features
```

### Format Transformations

```yaml
# Document format evolution
markdown → validated_markdown → sharded_markdown → 
contextualized_markdown → code_files → reviewed_code
```

## Best Practices

### 1. **Maintain Document Integrity**
- Always validate before sharding
- Preserve formatting in shards
- Keep references intact
- Track all versions

### 2. **Optimize for AI Processing**
- Shard large documents
- Provide focused context
- Minimize token usage
- Cache frequently used

### 3. **Enable Traceability**
- Track creation metadata
- Log all transformations
- Maintain dependency links
- Document decisions

### 4. **Handle Failures Gracefully**
- Checkpoint at each stage
- Enable rollback
- Preserve working versions
- Clear error messages

## Troubleshooting Document Issues

### Common Problems

| Issue | Cause | Solution |
|-------|-------|----------|
| Document not found | Wrong path | Check core-config paths |
| Sharding fails | Invalid structure | Validate markdown format |
| Validation loops | Conflicting requirements | Review checklist logic |
| Lost updates | Concurrent editing | Implement locking |
| Broken references | File moved | Update reference paths |

### Debugging Document Flow

1. **Trace Creation**:
```bash
# Check if document created
ls -la docs/prd.md
# Check creation log
cat .ai/debug-log.md | grep "prd.md"
```

2. **Verify Sharding**:
```bash
# Check sharded output
ls -la docs/prd/
# Verify shard count
find docs/prd -name "*.md" | wc -l
```

3. **Validate References**:
```bash
# Find broken links
grep -r "](.*\.md)" docs/ | check_links
```

## Document Lifecycle Optimization

### Performance Strategies

1. **Lazy Sharding**: Only shard when needed
2. **Smart Caching**: Cache validated documents
3. **Incremental Updates**: Update only changed sections
4. **Parallel Processing**: Process shards concurrently

### Storage Optimization

```yaml
storage_strategy:
  active: docs/          # Current working
  sharded: docs/*/       # Processed fragments
  archive: .archive/     # Historical versions
  cache: .cache/        # Temporary processing
```

## Summary

The document lifecycle in BMad represents a practical system for managing project artifacts through their journey. Key aspects include:

- **Core stages** from creation through implementation
- **Validation through checklists** and PO agent review
- **Intelligent sharding** using md-tree for AI token management
- **Configuration-driven paths** via core-config.yaml
- **Manual state tracking** through status fields
- **Template-based creation** for consistency

Understanding the document lifecycle is essential for:
- Managing project documentation flow
- Debugging sharding and validation issues
- Optimizing AI context windows
- Maintaining document integrity
- Creating expansion pack documents

## Architectural Patterns Discovered

### 1. Configuration-First Document Management
All document paths, sharding behavior, and QA locations are controlled through `core-config.yaml`, enabling flexible customization without code changes.

### 2. Permission-Based Section Editing
Agents like Test Architect can only edit specific sections of documents (e.g., "QA Results" in stories), preventing unauthorized modifications.

### 3. Sharding as Token Optimization
Documents are automatically sharded when `markdownExploder: true` to keep within AI token limits, with fallback to manual sharding.

### 4. Template-Driven Consistency
All major documents (PRD, architecture, stories) follow YAML templates ensuring consistent structure and required sections.

### 5. Manual State Management Philosophy
Rather than complex automated state machines, BMAD uses simple status fields updated manually, keeping the system transparent and debuggable.

The document lifecycle embodies BMad's pragmatic approach: documents are structured artifacts that flow through well-defined but manually controlled processes, balancing automation with human oversight.