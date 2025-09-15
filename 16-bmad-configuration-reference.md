# Deep Dive: Configuration in BMad - Central Settings Registry

## Overview

The `core-config.yaml` file serves as BMad's central settings registry with four key principles:

- **Tasks and agents read configuration** to determine file locations and processing behavior
- **Templates use hardcoded paths** that match configuration defaults by convention
- **Tasks bridge the gap** between static templates and dynamic configuration needs
- **Separation of concerns** keeps templates simple while enabling flexibility through task logic

## Schema at a Glance

```yaml
# Typical core-config.yaml structure
markdownExploder: true                      # Use npm tool for sharding
qa:
  qaLocation: docs/qa                       # QA artifacts directory
prd:
  prdFile: docs/prd.md                     # Main PRD document
  prdVersion: v4                            # Document model (v3/v4)
  prdSharded: true                          # Enable sharding
  prdShardedLocation: docs/prd             # Sharded sections directory
  epicFilePattern: epic-{n}*.md            # Epic naming pattern
architecture:
  architectureFile: docs/architecture.md    # Main architecture document
  architectureVersion: v4                   # Document model (v3/v4)
  architectureSharded: true                 # Enable sharding
  architectureShardedLocation: docs/architecture  # Sharded sections directory
customTechnicalDocuments: null              # Optional domain docs hook (unused by core)
devLoadAlwaysFiles:                         # Context files for dev agent
  - docs/architecture/coding-standards.md
  - docs/architecture/tech-stack.md
  - docs/architecture/source-tree.md
devDebugLog: .ai/debug-log.md              # Debug log location
devStoryLocation: docs/stories             # Story files directory
slashPrefix: BMad                           # Command prefix
```

## Version Model (v3 vs v4)

BMad supports two document structure models:

- **v3 (Monolithic)**: Single file containing entire document
- **v4 (Shardable)**: Document can be split into multiple files for better AI processing

Both `prdVersion` and `architectureVersion` refer to this document structure model, NOT the template version (which is separate, e.g., `template.version: 2.0` in YAML templates).

## Core Configuration Keys

### markdownExploder
**Purpose**: Choose between npm tool or manual sharding method
**Required**: No (defaults to true)
**Note**: When true, uses `@kayvan/markdown-tree-parser`; when false, uses manual procedural steps

### qa.qaLocation
**Purpose**: Root directory for QA artifacts (gates, assessments, test designs)
**Required**: Yes
**Example**: `docs/qa`
**Note**: Tasks create subdirectories `/gates` and `/assessments` automatically

### PRD Configuration (prd.*)
**Purpose**: Control PRD document location and structure
**Required**: See per-field requirements below

| Field | Purpose | Default | Required? |
|-------|---------|---------|-----------|
| `prdFile` | Main PRD document | `docs/prd.md` | Yes |
| `prdVersion` | Document model (v3/v4) | `v4` | Recommended |
| `prdSharded` | Enable sharding | `true` | Recommended |
| `prdShardedLocation` | Sharded sections | `docs/prd` | When `prdSharded: true` |
| `epicFilePattern` | Epic naming | `epic-{n}*.md` | Recommended when sharded |

### Architecture Configuration (architecture.*)
**Purpose**: Mirror PRD configuration for technical documentation
**Required**: See per-field requirements below

| Field | Purpose | Default | Required? |
|-------|---------|---------|-----------|
| `architectureFile` | Main architecture document | `docs/architecture.md` | Yes |
| `architectureVersion` | Document model (v3/v4) | `v4` | Recommended |
| `architectureSharded` | Enable sharding | `true` | Recommended |
| `architectureShardedLocation` | Sharded sections | `docs/architecture` | When `architectureSharded: true` |

### Developer Context Files

#### devLoadAlwaysFiles
**Purpose**: Files automatically loaded as context for dev agent
**Required**: Recommended
**Note**: Keep files concise (<1000 lines each) for token efficiency

#### devDebugLog
**Purpose**: Debug log location
**Required**: No
**Example**: `.ai/debug-log.md`

#### devStoryLocation
**Purpose**: Story files output directory
**Required**: Yes
**Note**: Templates hardcode `docs/stories/...` - changing this requires task support

### slashPrefix
**Purpose**: Command prefix for chat interfaces
**Required**: No (defaults to "BMad")

## Consumer Map

| Config Key | Primary Consumers | How It's Used |
|------------|------------------|---------------|
| `markdownExploder` | `shard-doc` task | Determines sharding method |
| `qa.qaLocation` | `qa-gate`, `nfr-assess`, `test-design` tasks | Creates directories, writes artifacts |
| `prd.prdFile` | PM agent, PO tasks | Document location for reading/writing |
| `prd.prdSharded*` | `create-next-story`, `shard-doc` tasks | Determines source for requirements |
| `architecture.*` | Architect agent, PO tasks | Mirrors PRD pattern |
| `devLoadAlwaysFiles` | Dev agent | Loads context at story start |
| `devStoryLocation` | SM agent, `create-next-story` task | Story creation (template hardcodes path) |
| `slashPrefix` | Chat interfaces | Command recognition |

## Configuration Patterns

Templates and configuration work together through **convention alignment**:

```yaml
# Template hardcodes (story-tmpl.yaml):
output:
  filename: docs/stories/{{epic_num}}.{{story_num}}.{{story_title_short}}.md

# Config defines matching default:
devStoryLocation: docs/stories

# Tasks read config to process files at the right location
```

Key points:
- Templates don't interpolate config values (use `{{template_vars}}` only)
- Tasks read config to know where to read/write files
- Convention ensures templates and config align by default

## Gotchas

- **No template interpolation**: Templates do not read config values. The qa-gate filename includes `qa.qaLocation`, but resolution is performed by tasks, not templates.
- **Version confusion**: `prdVersion`/`architectureVersion` = document model (v3/v4), not template version
- **Path changes**: Changing `devStoryLocation` doesn't auto-update template outputs - tasks must handle it
- **qa-gate exception**: Uses `qa.qaLocation` in filename but relies on task processing, not standard interpolation
- **Hardcoded by design**: Templates hardcoding paths is correct - it's convention, not a limitation

## Extension Points for Expansion Packs

- Current expansion packs use **metadata configuration only** (name, version, slashPrefix)
- Any custom configuration keys must have corresponding task/agent support
- No automatic inheritance or config merging exists - follow core conventions

## Best Practices

- Align template default paths with configuration defaults
- Let tasks handle any dynamic path resolution
- Keep `devLoadAlwaysFiles` concise for token efficiency
- Use v4 (shardable) for documents >50KB
- Test templates with both sharded and non-sharded workflows
- Don't attempt config value interpolation in templates
- Follow the established `docs/...` convention

## Links

- Template Specification: `bmad-method/common/utils/bmad-doc-template.md`
- Key Tasks:
  - `bmad-core/tasks/shard-doc.md` - Document sharding
  - `bmad-core/tasks/create-next-story.md` - Story generation
  - `bmad-core/tasks/qa-gate.md` - QA gate creation
- Expansion Packs: `bmad-method/docs/expansion-packs.md`

## Summary

BMad configuration provides a central registry that tasks and agents consult for file operations. Templates use hardcoded paths that align with config defaults through convention. This separation keeps templates simple while tasks handle flexibility by reading configuration at runtime.
