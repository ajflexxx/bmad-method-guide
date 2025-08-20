# Deep Dive: Configuration in BMad - The Control Center

## Overview

The `core-config.yaml` file is BMad's central configuration hub that controls document management, file locations, sharding behavior, and system-wide settings. It acts as a registry that all components reference to maintain consistency across the entire development lifecycle. Understanding this configuration is essential for customizing BMad to specific project structures and workflows.

## Configuration Anatomy

### Complete Structure

```yaml
markdownExploder: true
qa:
  qaLocation: docs/qa
prd:
  prdFile: docs/prd.md
  prdVersion: v4
  prdSharded: true
  prdShardedLocation: docs/prd
  epicFilePattern: epic-{n}*.md
architecture:
  architectureFile: docs/architecture.md
  architectureVersion: v4
  architectureSharded: true
  architectureShardedLocation: docs/architecture
customTechnicalDocuments: null
devLoadAlwaysFiles:
  - docs/architecture/coding-standards.md
  - docs/architecture/tech-stack.md
  - docs/architecture/source-tree.md
devDebugLog: .ai/debug-log.md
devStoryLocation: docs/stories
slashPrefix: BMad
```

### Core Configuration Innovation

**New in V4**: The `core-config.yaml` file is a critical innovation that enables BMad to work seamlessly with any project structure, providing maximum flexibility and backwards compatibility.

**What it Enables**:
- **Version Flexibility**: Work with V3, V4, or custom document structures
- **Custom Locations**: Define where documents and shards live
- **Developer Context**: Specify which files the dev agent should always load
- **Debug Support**: Built-in logging for troubleshooting
- **Intelligent Adaptation**: Agents automatically adapt to your configuration

**Why It Matters**:
1. **No Forced Migrations**: Keep your existing document structure
2. **Gradual Adoption**: Start with V3 and migrate to V4 at your pace  
3. **Custom Workflows**: Configure BMad to match your team's process
4. **Registry Pattern**: Acts as the "registry" that all components reference

## Configuration Parameters Deep Dive

### 1. **Markdown Processing**

```yaml
markdownExploder: true
```

**Purpose**: Controls automatic document sharding method
**Values**: 
- `true`: Use external `md-tree` tool for sharding
- `false`: Use manual AI-based sharding

**Effects**:
- When `true`, the `shard-doc` task attempts to use `@kayvan/markdown-tree-parser`
- Provides faster, more reliable sharding
- Requires npm package installation
- Falls back to manual if tool unavailable

**Referenced By**:
- `tasks/shard-doc.md`: Checks this setting first
- `agents/po.md`: Uses for document sharding commands

### 2. **QA Configuration**

```yaml
qa:
  qaLocation: docs/qa
```

**Purpose**: Controls Test Architect quality assurance file locations
**Values**: Directory path for QA artifacts

**Effects**:
- QA gate files saved to `{qaLocation}/gates/`
- Risk assessments saved to `{qaLocation}/assessments/`
- NFR assessments saved to `{qaLocation}/assessments/`
- Test design documents saved to `{qaLocation}/assessments/`

**Structure Created**:
```
docs/qa/
  ├── gates/
  │   ├── 1.1-user-login.yml
  │   ├── 1.2-password-reset.yml
  │   └── 2.1-dashboard-view.yml
  └── assessments/
      ├── 1.1-risk-20250117.md
      ├── 1.1-nfr-20250117.md
      └── 1.1-test-design-20250117.md
```

**Referenced By**:
- `tasks/qa-gate.md`: Creates gate files in `{qaLocation}/gates/`
- `tasks/nfr-assess.md`: Saves NFR assessments to `{qaLocation}/assessments/`
- `tasks/test-design.md`: Saves test designs to `{qaLocation}/assessments/`
- `agents/qa.md`: Test Architect uses for all QA artifacts

### 3. **PRD Configuration**

```yaml
prd:
  prdFile: docs/prd.md
  prdVersion: v4
  prdSharded: true
  prdShardedLocation: docs/prd
  epicFilePattern: epic-{n}*.md
```

**Version Flexibility Examples**:

**Legacy V3 Project**:
```yaml
prdVersion: v3
prdSharded: false
architectureVersion: v3 
architectureSharded: false
```

**V4 Optimized Project**:
```yaml
prdVersion: v4
prdSharded: true
prdShardedLocation: docs/prd
architectureVersion: v4
architectureSharded: true
architectureShardedLocation: docs/architecture
```

**Parameters**:

#### `prdFile`
- **Purpose**: Primary PRD document location
- **Default**: `docs/prd.md`
- **Used By**: PM agent, workflows, validation tasks
- **Pattern**: Always relative to project root

#### `prdVersion`
- **Purpose**: PRD template version tracking
- **Current**: `v4`
- **Evolution**: Tracks template improvements
- **Compatibility**: Ensures agents use correct parsing

#### `prdSharded`
- **Purpose**: Indicates if PRD should be sharded
- **Values**: `true` | `false`
- **Effects**: 
  - `true`: PRD split into multiple files
  - `false`: Keep as single document
- **Performance**: Sharding improves AI processing

#### `prdShardedLocation`
- **Purpose**: Directory for sharded PRD files
- **Default**: `docs/prd`
- **Structure**: Each section becomes separate file
- **Naming**: `section-name.md` format

#### `epicFilePattern`
- **Purpose**: Pattern for epic file naming
- **Format**: `epic-{n}*.md` where {n} is number
- **Example**: `epic-1-user-auth.md`
- **Used By**: SM agent for story creation

### 4. **Architecture Configuration**

```yaml
architecture:
  architectureFile: docs/architecture.md
  architectureVersion: v4
  architectureSharded: true
  architectureShardedLocation: docs/architecture
```

**Parameters**:

#### `architectureFile`
- **Purpose**: Primary architecture document location
- **Default**: `docs/architecture.md`
- **Used By**: Architect agent, workflows
- **Contents**: Technical design, stack, patterns

#### `architectureVersion`
- **Purpose**: Architecture template version
- **Sync**: Usually matches PRD version
- **Migration**: Guides version upgrades

#### `architectureSharded`
- **Purpose**: Controls architecture sharding
- **Rationale**: Large architecture docs benefit from splitting
- **Sections**: Each technical area separate

#### `architectureShardedLocation`
- **Purpose**: Directory for sharded architecture
- **Structure**: Mirrors PRD sharding pattern
- **Files**: `tech-stack.md`, `api-design.md`, etc.

### 5. **Custom Documents**

```yaml
customTechnicalDocuments: null
```

**Purpose**: Extension point for additional documents
**Future Use**: Could define custom document types
**Format** (when used):
```yaml
customTechnicalDocuments:
  - type: api-spec
    file: docs/api-spec.md
    sharded: true
    location: docs/api
```

### 6. **Developer Context Files**

```yaml
devLoadAlwaysFiles:
  - docs/architecture/coding-standards.md
  - docs/architecture/tech-stack.md
  - docs/architecture/source-tree.md
```

**Purpose**: Files automatically loaded for dev agent
**Context**: Provides consistent context across stories
**Contents**:
- **coding-standards.md**: Style guides, conventions
- **tech-stack.md**: Technologies, libraries
- **source-tree.md**: Project structure

**Effects**:
- Dev agent always has these in context
- Ensures consistent implementation
- Reduces context switching

**Best Practices**:
- Keep files concise (token efficiency)
- Update as project evolves
- Include critical patterns

### 7. **Debug Configuration**

```yaml
devDebugLog: .ai/debug-log.md
```

**Purpose**: Location for development debug logs
**Contents**: 
- Agent activation logs
- Task execution traces
- Error messages
- Performance metrics

**Usage**:
- Hidden folder (`.ai/`)
- Markdown format for readability
- Append-only logging
- Useful for troubleshooting

### 8. **Story Management**

```yaml
devStoryLocation: docs/stories
```

**Purpose**: Directory for story files
**Structure**:
```
docs/stories/
  ├── epic-1-user-auth/
  │   ├── story-1-login.md
  │   ├── story-2-signup.md
  │   └── story-3-oauth.md
  └── epic-2-payments/
      ├── story-1-stripe.md
      └── story-2-refunds.md
```

**Naming Convention**:
- Epic folders match `epicFilePattern`
- Stories numbered sequentially
- Descriptive suffixes

### 9. **Command Prefix**

```yaml
slashPrefix: BMad
```

**Purpose**: Prefix for special commands
**Usage**: `/BMad command-name`
**Examples**:
- `/BMad help`
- `/BMad status`
- `/BMad validate`

**Customization**:
- Can change to project name
- Avoid conflicts with other tools
- Keep short for convenience

## Configuration Usage Patterns

### 1. **Document Location Resolution**

Components use config to find documents:

```javascript
// Pseudo-code from tasks
const prdPath = config.prd.prdFile;  // "docs/prd.md"
const prdExists = checkFile(prdPath);

if (config.prd.prdSharded) {
  const shardedPath = config.prd.prdShardedLocation;  // "docs/prd"
  loadShardedDocs(shardedPath);
}
```

### 2. **Sharding Decision Flow**

```yaml
# In shard-doc task
if (config.markdownExploder) {
  // Try md-tree tool
  runCommand(`md-tree explode ${source} ${dest}`);
} else {
  // Manual AI sharding
  performManualSharding(document);
}
```

### 3. **Story Creation Context**

```yaml
# Dev agent loads context
contextFiles = config.devLoadAlwaysFiles;
foreach (file in contextFiles) {
  loadIntoContext(file);
}
// Now has coding standards, tech stack, etc.
```

## Configuration Effects on Components

### Effects on Agents

| Agent | Configuration Usage |
|-------|-------------------|
| **PM** | Writes to `prdFile` location |
| **Architect** | Writes to `architectureFile` |
| **PO** | Reads sharding settings, validates locations |
| **SM** | Creates stories in `devStoryLocation` |
| **Dev** | Loads `devLoadAlwaysFiles` for context |
| **QA (Test Architect)** | Uses `qaLocation` for gates and assessments |

### Effects on Tasks

| Task | Configuration Dependency |
|------|------------------------|
| **create-next-story** | Reads from `prdShardedLocation` |
| **shard-doc** | Uses `markdownExploder` setting |
| **validate-next-story** | Checks `devStoryLocation` |
| **document-project** | May write to custom locations |
| **qa-gate** | Writes to `qaLocation/gates/` |
| **nfr-assess** | Writes to `qaLocation/assessments/` |
| **test-design** | Writes to `qaLocation/assessments/` |

### Effects on Workflows

Workflows reference config indirectly through agents:
1. Workflow triggers agent
2. Agent reads config
3. Agent performs action at configured location
4. Next agent finds document at expected location

## Configuration Patterns

### 1. **Version Management**

```yaml
prdVersion: v4
architectureVersion: v4
```

**Purpose**: Track template evolution
**Migration**: Guides upgrades between versions
**Compatibility**: Ensures correct parsing

### 2. **Sharding Pattern**

```yaml
# Document and its sharded location
prdFile: docs/prd.md
prdShardedLocation: docs/prd

architectureFile: docs/architecture.md  
architectureShardedLocation: docs/architecture
```

**Convention**: Sharded location = file without extension
**Benefits**: Predictable structure
**Organization**: Keeps related content together

### 3. **Context Loading Pattern**

```yaml
devLoadAlwaysFiles:
  - docs/architecture/coding-standards.md
  - docs/architecture/tech-stack.md
  - docs/architecture/source-tree.md
```

**Strategy**: Load once, use everywhere
**Efficiency**: Reduces token usage
**Consistency**: Same context across stories

## Customizing Configuration

### For Different Project Structures

```yaml
# Alternative structure for monorepo
prd:
  prdFile: packages/docs/prd.md
  prdShardedLocation: packages/docs/prd
  
devStoryLocation: packages/docs/stories

# For microservices
architecture:
  architectureFile: services/docs/architecture.md
  architectureShardedLocation: services/docs/architecture
```

### For Different Workflows

```yaml
# Disable sharding for small projects
prd:
  prdSharded: false
  
architecture:
  architectureSharded: false

# Skip markdown exploder
markdownExploder: false
```

### For Custom Documents

```yaml
# Future extension possibility
customTechnicalDocuments:
  - apiSpec:
      file: docs/api-spec.md
      sharded: true
      location: docs/api
  - dataModel:
      file: docs/data-model.md
      sharded: false
```

## Advanced Configuration Patterns

### Configuration Namespace Traversal

**Pattern**: Dot notation enables deep configuration value access

```yaml
# In templates and tasks
qa.qaLocation  # Accesses nested config value
prd.prdShardedLocation  # Deep path resolution
```

**Benefits**:
- Clean configuration references
- Type-safe path resolution
- Prevents hardcoded paths

### Pluggable Artifact Storage

**Pattern**: Configuration determines where artifacts are stored

```yaml
qa:
  qaLocation: docs/qa  # All QA artifacts under this path
  
# Results in:
# docs/qa/gates/{story-id}.yaml
# docs/qa/assessments/{type}/{story-id}.md
```

**Flexibility**:
- Teams can organize artifacts by their conventions
- Easy to redirect outputs for different environments
- Supports both local and remote storage patterns

### Agent-Specific Customization Points

**Pattern**: Templates expose agent_config sections for role-specific behavior

```yaml
# In story template
agent_config:
  editable_sections:
    - implementation_details
    - technical_notes
```

**Usage**:
- Agents can modify only their designated sections
- Preserves separation of concerns
- Enables collaborative document creation

### Configuration-Driven Behavior

**Pattern**: System behaviors change based on configuration, not code

```yaml
# Behavior changes
markdownExploder: true   # Uses external tool
markdownExploder: false  # Uses AI sharding

prdSharded: true   # Reads from sharded location
prdSharded: false  # Reads from single file
```

**Advantages**:
- No code changes needed for different workflows
- Environment-specific optimizations
- Easy A/B testing of approaches

### Dynamic Path Resolution

**Pattern**: Paths are resolved at runtime from configuration

```javascript
// Not hardcoded
const storyPath = `${config.devStoryLocation}/${storyId}.md`;

// Not static
const qaGate = `${config.qa.qaLocation}/gates/${storyId}.yaml`;
```

**Benefits**:
- Portable across projects
- Supports different organizational structures
- Enables configuration-based routing

## Environment-Specific Configuration

### Development Environment

```yaml
# Development settings
devDebugLog: .ai/debug-log-verbose.md
markdownExploder: true  # Use tool for speed
```

### CI/CD Environment

```yaml
# CI/CD optimized
devDebugLog: /dev/null  # No logging
markdownExploder: false  # No external dependencies
```

### Production Environment

```yaml
# Production settings
devLoadAlwaysFiles:
  - docs/architecture/production-standards.md
  - docs/architecture/security-guidelines.md
```

## Configuration Validation

### Required Fields

Must be present for BMad to function:
- `qa.qaLocation`
- `prd.prdFile`
- `architecture.architectureFile`
- `devStoryLocation`

### Optional Fields

Can be null or omitted:
- `customTechnicalDocuments`
- `devDebugLog`
- `slashPrefix`

### Validation Rules

1. **Paths must be relative** (not absolute)
2. **Directories auto-created** if missing
3. **Version strings** should match templates
4. **File patterns** must include `{n}` placeholder

## Troubleshooting Configuration Issues

### Common Problems and Solutions

| Problem | Cause | Solution |
|---------|-------|----------|
| Documents not found | Wrong path in config | Verify paths relative to project root |
| Sharding fails | markdownExploder true but tool missing | Install md-tree or set to false |
| Stories in wrong place | devStoryLocation incorrect | Update path in config |
| Context not loaded | devLoadAlwaysFiles paths wrong | Check file existence |
| Epic pattern mismatch | epicFilePattern incorrect | Ensure {n} placeholder present |

### Configuration Debugging

1. **Check file existence**:
```bash
ls docs/prd.md
ls docs/architecture.md
```

2. **Verify sharding**:
```bash
ls -la docs/prd/
ls -la docs/architecture/
```

3. **Test markdown exploder**:
```bash
which md-tree
md-tree --version
```

4. **Review debug log**:
```bash
cat .ai/debug-log.md
```

## Migration and Upgrades

### Version Migration

When upgrading BMad versions:

1. **Check version changes**:
```yaml
# Old version
prdVersion: v3

# New version  
prdVersion: v4
```

2. **Run migration scripts** if provided
3. **Update templates** to match versions
4. **Test with sample documents**

### Configuration Evolution

Config may expand over time:
- New document types
- Additional context files
- Enhanced sharding options
- Custom workflow paths

## Best Practices

### 1. **Consistent Structure**
- Keep all docs in `docs/` folder
- Use predictable naming
- Mirror sharding patterns

### 2. **Optimize Context Files**
- Keep under 1000 lines each
- Focus on essential information
- Update regularly

### 3. **Version Alignment**
- Keep PRD and architecture versions in sync
- Update both during migrations
- Document version changes

### 4. **Path Management**
- Use relative paths only
- Avoid deeply nested structures
- Keep paths short

### 5. **Sharding Strategy**
- Enable for documents > 50KB
- Disable for simple projects
- Use md-tree tool when possible

## Configuration in Expansion Packs

Expansion packs extend configuration:

```yaml
# Expansion pack config.yaml
extends: core-config
gameDesign:
  designFile: docs/game-design.md
  designSharded: true
  designShardedLocation: docs/game-design
  
devLoadAlwaysFiles:
  - docs/game-design/mechanics.md
  - docs/game-design/assets.md
```

This allows:
- Domain-specific documents
- Additional context files
- Custom sharding rules
- Extended patterns

## Summary

The `core-config.yaml` file is the nerve center of BMad that:

- **Centralizes** all path and location settings
- **Controls** document management and sharding
- **Provides** consistent context across agents
- **Enables** customization for different environments
- **Supports** extension through expansion packs

Understanding configuration is essential for:
- Customizing BMad to your project structure
- Troubleshooting document location issues
- Optimizing performance through sharding
- Creating expansion packs
- Managing multi-environment deployments

The configuration system demonstrates BMad's philosophy of "convention with configuration" - providing sensible defaults while allowing complete customization when needed.