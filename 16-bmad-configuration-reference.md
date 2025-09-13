# Deep Dive: Configuration in BMad - Central Settings Registry for Tasks and Agents

## Overview

The `core-config.yaml` file serves as BMad's central settings registry - a single source of truth for document locations, processing options, and project structure. Tasks and agents consult this configuration to determine where to read input files and write outputs. Templates themselves use hardcoded default paths that are designed to align with the configuration defaults, creating a convention-based system where components work together seamlessly.

### Core Principles

- **Registry Pattern**: Tasks and agents read configuration to determine file locations and processing behavior
- **Convention Alignment**: Templates use hardcoded paths that match configuration defaults by design
- **Task-Driven Flexibility**: Tasks handle the dynamic aspects by reading config and managing file operations
- **Separation of Concerns**: Templates define structure, configuration defines locations, tasks bridge the gap

### What Makes Configuration Unique

BMad's configuration doesn't dynamically inject paths into templates. Instead, it provides a consistent reference that tasks consult when processing documents. Templates use standard default paths (like `docs/prd.md` or `docs/stories/...`) that align with the configuration defaults. When tasks execute, they read the configuration to know where to find or create files, providing flexibility without template complexity.

### Configuration Structure at a Glance

- markdownExploder - External tool usage for document sharding
- qa - Quality assurance artifact locations
- prd - Product requirements document settings and sharding
- architecture - Architecture document settings and sharding
- devLoadAlwaysFiles - Context files for development agents
- devDebugLog - Debug logging location
- devStoryLocation - Story file output directory
- slashPrefix - Command prefix for BMad commands

## Configuration Anatomy

### markdownExploder

Controls whether BMad uses an external npm tool for document sharding or falls back to manual sharding method.

**Purpose**: Determines the sharding method for large documents, affecting performance and reliability of document splitting operations.

**Requirement**: Optional (defaults to true)
- When to include: Always include to be explicit about sharding strategy
- When NOT to include: Only omit if you want default behavior (true)

**Format**:
```yaml
markdownExploder: true  # or false
```

**Examples from actual tasks**:

From `bmad-core/tasks/shard-doc.md:67-75`:
```markdown
Check the core-config.yaml to determine sharding method:
- If markdownExploder: true - Use @kayvan/markdown-tree-parser tool
- If markdownExploder: false or tool unavailable - Use Manual Method
```

**Best Practices**:
- Set to `true` when the npm package `@kayvan/markdown-tree-parser` is available
- Set to `false` for environments without npm or when manual sharding is preferred
- The tool provides more consistent sharding but requires external dependency

**How Tasks Use This**:
- The `shard-doc` task reads this setting to choose between `md-tree explode` command or manual procedural steps
- Templates are not affected by this setting directly
- The manual method follows detailed procedural steps, not automatic AI sharding

### qa

Configures the root directory for all quality assurance artifacts including gates, assessments, and test designs.

**Purpose**: Centralizes QA artifact storage, ensuring consistent location for test-related documentation across all agents and tasks.

**Requirement**: Required
- When to include: Always - QA tasks depend on this path
- When NOT to include: Never - will cause QA task failures

**Format**:
```yaml
qa:
  qaLocation: docs/qa
```

**Examples from actual tasks**:

From `bmad-core/tasks/qa-gate.md:19`:
```markdown
**ALWAYS** check the `bmad-core/core-config.yaml` for the `qa.qaLocation/gates`
```

From `bmad-core/tasks/nfr-assess.md:31`:
```markdown
Location: `{qa.qaLocation}/assessments/{story-id}-nfr-{timestamp}.md`
```

From `bmad-core/tasks/test-design.md:28`:
```markdown
Save to: `{qa.qaLocation}/assessments/{story-id}-test-design-{timestamp}.md`
```

**Best Practices**:
- Keep under project `docs/` directory for consistency
- Use descriptive subdirectory name like `qa` or `quality`
- Ensure directory is included in version control

**How This Works in Practice**:
- The qa-gate template has `qa.qaLocation` in its filename field, but this is NOT standard template variable substitution
- Tasks like `qa-gate.md` read the configuration and create the actual directories
- Tasks handle writing files to the configured location, not template interpolation
- Templates should use standard paths that align with expected config defaults

### prd

Comprehensive configuration for Product Requirements Document management including versioning, sharding, and epic patterns.

#### prd.prdFile

**Purpose**: Defines the primary PRD document location that serves as the source for all product requirements.

**Requirement**: Required
- When to include: Always - core document for product planning
- When NOT to include: Never - breaks PM agent functionality

**Format**:
```yaml
prd:
  prdFile: docs/prd.md
```

**Examples from actual usage**:

Tasks reference this as the source document for requirements extraction and story generation. The PM agent writes to this location when creating the initial PRD.

**Best Practices**:
- Place in `docs/` directory at project root
- Use consistent naming across projects
- Keep as single source of truth for requirements

#### prd.prdVersion

**Purpose**: Indicates the PRD document structure model (v3 = monolithic, v4 = sharded), NOT the template version.

**Requirement**: Recommended
- When to include: Always specify to clarify document structure expectations
- When NOT to include: Only if using default version (v4)

**Format**:
```yaml
prd:
  prdVersion: v4  # Document structure version, not template version
```

**Best Practices**:
- This is about document structure (v3 = single file, v4 = shardable)
- NOT related to `template.version` in prd-tmpl.yaml (which is 2.0)
- Keep synchronized with architecture version for consistency

#### prd.prdSharded

**Purpose**: Controls whether the PRD should be split into multiple files for better AI processing of large documents.

**Requirement**: Recommended
- When to include: For any non-trivial project
- When NOT to include: Only for very small projects

**Format**:
```yaml
prd:
  prdSharded: true  # or false
```

**Examples from actual tasks**:

From `bmad-core/tasks/create-next-story.md:45-52`:
```markdown
If prdSharded is true:
  Read from prdShardedLocation directory
Else:
  Read from single prdFile
```

**Best Practices**:
- Enable for PRDs larger than 50KB
- Disable for simple projects with few requirements
- Consider AI token limits when deciding

#### prd.prdShardedLocation

**Purpose**: Specifies the directory where sharded PRD sections are stored when sharding is enabled.

**Requirement**: Required when `prdSharded: true`
- When to include: Always when sharding is enabled
- When NOT to include: When `prdSharded: false`

**Format**:
```yaml
prd:
  prdShardedLocation: docs/prd
```

**Best Practices**:
- Convention: use document name without extension as directory
- Keep adjacent to main PRD file
- Include in version control

#### prd.epicFilePattern

**Purpose**: Defines the naming pattern for epic files, enabling consistent epic organization and discovery.

**Requirement**: Recommended when `prdSharded: true`
- When to include: When using sharded PRD structure with epics
- When NOT to include: For simple projects without epic organization

**Format**:
```yaml
prd:
  epicFilePattern: epic-{n}*.md
```

**Examples from actual usage**:

The `{n}` placeholder is replaced with epic numbers, allowing patterns like:
- `epic-1-user-authentication.md`
- `epic-2-payment-processing.md`

**Best Practices**:
- Always include `{n}` placeholder for epic numbering
- Use descriptive suffixes after the number
- Maintain consistent pattern across project

### architecture

Parallel configuration structure for architecture documentation with same sharding capabilities as PRD.

#### architecture.architectureFile

**Purpose**: Defines the primary architecture document location containing technical design decisions.

**Requirement**: Required
- When to include: Always - core technical reference
- When NOT to include: Never - breaks Architect agent

**Format**:
```yaml
architecture:
  architectureFile: docs/architecture.md
```

**Best Practices**:
- Mirror PRD configuration for consistency
- Place in same directory as PRD
- Maintain as technical counterpart to PRD

#### architecture.architectureVersion

**Purpose**: Indicates the architecture document structure model (v3 = monolithic, v4 = sharded), NOT the template version.

**Requirement**: Recommended
- When to include: Always specify to clarify document structure expectations
- When NOT to include: Only if using default (v4)

**Format**:
```yaml
architecture:
  architectureVersion: v4  # Document structure version, not template version
```

**Best Practices**:
- Keep synchronized with PRD version for consistency
- Update both together during migrations
- NOT related to `template.version` in architecture-tmpl.yaml

#### architecture.architectureSharded

**Purpose**: Controls architecture document sharding for large technical specifications.

**Requirement**: Recommended
- When to include: For complex architectures
- When NOT to include: For simple systems

**Format**:
```yaml
architecture:
  architectureSharded: true
```

**Best Practices**:
- Enable for multi-component systems
- Match PRD sharding decision
- Consider developer context needs

#### architecture.architectureShardedLocation

**Purpose**: Directory for sharded architecture sections.

**Requirement**: Required when `architectureSharded: true`
- When to include: When sharding is enabled
- When NOT to include: When using single document

**Format**:
```yaml
architecture:
  architectureShardedLocation: docs/architecture
```

**Best Practices**:
- Follow same convention as PRD sharding
- Keep technical sections well-organized
- Include diagrams in sharded structure

### devLoadAlwaysFiles

Array of files automatically loaded as context for the development agent during story implementation.

**Purpose**: Provides consistent technical context across all development stories, ensuring adherence to project standards without manual inclusion.

**Requirement**: Recommended
- When to include: For any project with coding standards
- When NOT to include: Only for proof-of-concept projects

**Format**:
```yaml
devLoadAlwaysFiles:
  - docs/architecture/coding-standards.md
  - docs/architecture/tech-stack.md
  - docs/architecture/source-tree.md
```

**Examples from actual configuration**:

From `bmad-core/core-config.yaml:17-20`:
```yaml
devLoadAlwaysFiles:
  - docs/architecture/coding-standards.md
  - docs/architecture/tech-stack.md
  - docs/architecture/source-tree.md
```

**Best Practices**:
- Keep files concise (under 1000 lines each) for token efficiency
- Include only essential, frequently-referenced information
- Update files as project standards evolve
- Order by importance (most critical first)

**Template Developer Considerations**:
- Story templates can assume these files are in agent context
- Reference these files in story dev notes sections
- Consider token limits when designing templates that use this context

### devDebugLog

Specifies the location for development agent debug logging.

**Purpose**: Captures detailed execution traces for troubleshooting agent behavior and task execution problems.

**Requirement**: Optional
- When to include: When debugging is needed
- When NOT to include: In production or when logs aren't needed

**Format**:
```yaml
devDebugLog: .ai/debug-log.md
```

**Best Practices**:
- Use hidden directory (`.ai/`) to avoid clutter
- Use `.md` extension for readable formatting
- Rotate or clear logs periodically
- Exclude from version control (add to `.gitignore`)

**Template Developer Considerations**:
- Templates don't write directly to debug log
- Useful for troubleshooting template processing issues
- Can reference in template debugging instructions

### devStoryLocation

Defines the root directory for all story documents created during development.

**Purpose**: Centralizes story file storage, enabling consistent organization and discovery of development tasks.

**Requirement**: Required
- When to include: Always - core to development workflow
- When NOT to include: Never - breaks story creation

**Format**:
```yaml
devStoryLocation: docs/stories
```

**Examples from actual templates**:

From `bmad-core/templates/story-tmpl.yaml:8`:
```yaml
output:
  filename: docs/stories/{{epic_num}}.{{story_num}}.{{story_title_short}}.md
```

**Best Practices**:
- Use `docs/stories` for standard projects
- Keep template default aligned with config default
- Include in version control for history
- Keep separate from other documentation

**Important Note**:
- The story template hardcodes `docs/stories/...` path
- Changing `devStoryLocation` in config doesn't automatically change template output
- Tasks that create stories would need to handle custom locations explicitly

### slashPrefix

Defines the prefix for BMad-specific commands in chat interfaces.

**Purpose**: Provides namespace for BMad commands to avoid conflicts with other tools or frameworks.

**Requirement**: Optional (defaults to "BMad")
- When to include: When customizing command prefix
- When NOT to include: When default is acceptable

**Format**:
```yaml
slashPrefix: BMad  # Results in commands like /BMad status
```

**Best Practices**:
- Keep short (3-5 characters) for convenience
- Use project or company prefix for branding
- Avoid common prefixes that might conflict
- Document custom prefix for team

**Template Developer Considerations**:
- Templates don't typically use slash commands
- May reference in help documentation templates
- Consider when creating command-driven templates

## Configuration Patterns for Templates

### The Actual Pattern: Convention Alignment

Templates in BMad use hardcoded default paths that are designed to align with configuration defaults:

```yaml
# From story-tmpl.yaml - hardcoded path
output:
  filename: docs/stories/{{epic_num}}.{{story_num}}.{{story_title_short}}.md

# From prd-tmpl.yaml - hardcoded path
output:
  filename: docs/prd.md

# From architecture-tmpl.yaml - hardcoded path
output:
  filename: docs/architecture.md
```

These paths match the configuration defaults by convention, NOT through dynamic resolution.

### How Tasks Bridge Configuration and Templates

Tasks read configuration to determine where to process files:

```markdown
# From qa-gate.md task
**ALWAYS** check the `bmad-core/core-config.yaml` for the `qa.qaLocation/gates`

# From create-next-story.md task
If config.prd.prdSharded is true:
  Read from config.prd.prdShardedLocation directory
Else:
  Read from config.prd.prdFile
```

Tasks handle the dynamic aspects - they read config and manage file I/O accordingly.

### Template Variable Substitution vs Configuration

Templates support variable substitution using `{{variable}}` syntax, but these are template variables, NOT configuration paths:

```yaml
# CORRECT - template variables
filename: docs/stories/{{epic_num}}.{{story_num}}.{{story_title_short}}.md

# INCORRECT ASSUMPTION - config paths don't work in standard templates
filename: {{devStoryLocation}}/{{epic_num}}.{{story_num}}.md  # This doesn't work
```

The qa-gate template uses `qa.qaLocation` in its filename, but this is a special case that relies on task processing, not standard template interpolation.

### The Division of Responsibilities

1. **Templates**: Define document structure with hardcoded default paths
2. **Configuration**: Provides centralized settings that match template defaults
3. **Tasks**: Read configuration and handle actual file operations
4. **Agents**: Use tasks and templates together, consulting config as needed

## Extension Points for Expansion Packs

### Actual Expansion Pack Configuration

Expansion packs currently contain minimal configuration - primarily metadata and slash prefix:

```yaml
# From actual expansion-packs/bmad-creative-writing/config.yaml
name: bmad-creative-writing
version: 1.1.1
short-title: Creative Writing Studio
description: >-
  Comprehensive AI-powered creative writing framework...
author: Wes
slashPrefix: bmad-cw

# From actual expansion-packs/bmad-infrastructure-devops/config.yaml
name: bmad-infrastructure-devops
version: 1.12.0
short-title: Infrastructure DevOps Pack
description: >-
  This expansion pack extends BMad Method...
author: Brian (BMad)
slashPrefix: bmadInfraDevOps
```

### What Expansion Packs Actually Do

Rather than extending configuration, expansion packs:
- Provide domain-specific agents with their own dependencies
- Include specialized templates with appropriate default paths
- Define tasks that handle domain-specific operations
- May include their own conventions that align with core patterns

### Future Configuration Extensions

While not currently implemented, expansion packs could potentially:
- Define domain-specific configuration keys (would require task support)
- Override defaults (would need tooling to merge configs)
- Extend arrays like devLoadAlwaysFiles (not currently supported)

**Important**: Any configuration extensions must be consumed by tasks/agents that know how to use them. There is no automatic configuration inheritance or merging system.

## Configuration Reference by Component

### Agent Configuration Usage

| Agent | Configuration Sections Used | How It's Used |
|-------|----------------------------|---------------|
| **PM** | `prd.prdFile` | Template outputs to hardcoded `docs/prd.md` that matches config default |
| **Architect** | `architecture.architectureFile` | Template outputs to hardcoded `docs/architecture.md` that matches config |
| **PO** | `prd.*`, `architecture.*` | Tasks read config to validate and shard documents |
| **SM** | `devStoryLocation`, `prd.*` | Tasks read config to locate PRD; template uses hardcoded story path |
| **Dev** | `devLoadAlwaysFiles`, `devDebugLog` | Agent loads context files; tasks may write debug logs |
| **QA** | `qa.qaLocation` | Tasks read config to create directories and write test artifacts |

### Task Configuration Dependencies

| Task | Configuration Path | Usage |
|------|-------------------|-------|
| `create-next-story` | `prd.prdShardedLocation` | Reads sharded requirements |
| `shard-doc` | `markdownExploder` | Determines sharding method |
| `qa-gate` | `qa.qaLocation/gates/` | Writes gate files |
| `nfr-assess` | `qa.qaLocation/assessments/` | Stores NFR assessments |
| `test-design` | `qa.qaLocation/assessments/` | Saves test designs |
| `validate-next-story` | `devStoryLocation` | Finds story files |

### Template Output Configuration

| Template | Hardcoded Output Path | Aligns With Config |
|----------|----------------------|--------------------|
| `story-tmpl` | `docs/stories/{{vars}}.md` | `devStoryLocation: docs/stories` |
| `prd-tmpl` | `docs/prd.md` | `prd.prdFile: docs/prd.md` |
| `architecture-tmpl` | `docs/architecture.md` | `architecture.architectureFile: docs/architecture.md` |
| `qa-gate-tmpl` | `qa.qaLocation/gates/{{vars}}.yml` | Special case - relies on task processing |

## Best Practices for Template Developers

### 1. Path Resolution
- Use hardcoded default paths that align with configuration defaults
- Keep template paths consistent with core conventions (`docs/...`)
- Let tasks handle any dynamic path resolution needs

### 2. Sharding Awareness
- Templates don't handle sharding directly - tasks do
- Design document structure to be sharding-friendly
- Test with both sharded and non-sharded workflows

### 3. Configuration Extension
- Expansion packs currently use metadata config only
- Any custom configuration must have corresponding task support
- Follow core conventions rather than trying to override them

### 4. Context Management
- Keep context files referenced in `devLoadAlwaysFiles` concise
- Update context files as templates evolve
- Consider cumulative token usage

### 5. Debugging Support
- Use debug log configuration during template development
- Include clear error messages referencing configuration
- Document configuration requirements in template comments

## Summary

The BMad configuration system provides a central registry that:

- **Centralizes** settings that tasks and agents consult for file operations
- **Aligns** with template defaults through convention, not dynamic binding
- **Enables** tasks to handle flexible document processing and location
- **Supports** both simple and complex project structures through sharding options
- **Maintains** clear separation: templates define structure, config defines settings, tasks bridge the gap

For expansion pack developers, understanding this pattern is essential: create templates with standard default paths that match configuration conventions, then let tasks handle any dynamic aspects by reading the configuration. This separation keeps templates simple while enabling flexibility through task logic.
