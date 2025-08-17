# Deep Dive: Build System in BMad - Distribution and Packaging

## Overview

BMad's build system is responsible for creating deployable bundles from the modular component architecture. It handles dependency resolution, resource packaging, and dual-environment distribution. Understanding the build system is essential for creating expansion packs and customizing BMad distributions.

## v5.0 Dual Publishing Strategy

BMad v5.0 introduces a sophisticated dual publishing strategy to balance stability with innovation:

### Publishing Channels

#### Stable Release (`@latest`)
- **Target**: Production environments
- **Version**: Semantic versioning (e.g., 5.0.0)
- **Installation**: `npx bmad-method@latest install`
- **Features**: Thoroughly tested, production-ready
- **Release Cycle**: Monthly or as needed

#### Beta Release (`@beta`)
- **Target**: Early adopters and testing
- **Version**: Pre-release tags (e.g., 5.1.0-beta.1)
- **Installation**: `npx bmad-method@beta install`
- **Features**: Cutting-edge, experimental features
- **Release Cycle**: Continuous as features develop

### Automated Promotion Workflow

```yaml
# .github/workflows/promote-to-stable.yml
promotion:
  trigger: Manual or scheduled
  process:
    - Run comprehensive test suite
    - Validate all expansion packs
    - Update version numbers
    - Create stable release
    - Publish to npm @latest tag
    - Update documentation
```

### Version Management

```json
// package.json
{
  "version": "5.0.0",  // Stable version
  "publishConfig": {
    "tag": "latest"    // or "beta" for pre-releases
  }
}
```

## Build System Architecture

### Core Components

```mermaid
graph TD
    subgraph "Source Components"
        A[Agents] --> B[Build System]
        T[Tasks] --> B
        TM[Templates] --> B
        C[Checklists] --> B
        D[Data] --> B
        W[Workflows] --> B
        AT[Agent Teams] --> B
    end
    
    subgraph "Build Process"
        B --> DR[Dependency Resolution]
        DR --> RC[Resource Concatenation]
        RC --> FG[Format Generation]
    end
    
    subgraph "Output Bundles"
        FG --> IDE[IDE Distribution]
        FG --> WEB[Web UI Bundles]
        FG --> EP[Expansion Packs]
    end
```

### Build Tool: web-builder.js

The `web-builder.js` tool is the core build engine that:

1. **Reads agent or team definition files**
2. **Recursively resolves all dependencies**
3. **Concatenates content into single text files with clear separators**
4. **Outputs ready-to-upload bundles for web AI interfaces**

**Location**: `/bmad-core/utils/web-builder.js` (referenced in bmad-kb.md)

**Key Functionality**:
- Dependency graph traversal
- Resource deduplication
- Format optimization for web interfaces
- Error handling for missing dependencies

## Dependency Resolution System

### Resolution Algorithm

```yaml
# Pseudo-algorithm for dependency resolution
function resolveDependencies(component):
  visited = new Set()
  queue = [component]
  resources = []
  
  while queue is not empty:
    current = queue.pop()
    if current in visited:
      continue
      
    visited.add(current)
    resources.add(current)
    
    # Load component definition
    definition = loadYAML(current)
    
    # Add dependencies to queue
    for dep in definition.dependencies:
      for category in [tasks, templates, checklists, data]:
        if dep in category:
          queue.push(dep)
  
  return resources
```

### Resolution Patterns

#### 1. **Agent-Based Resolution**
```yaml
# Starting from agent file
agent: pm.md
dependencies:
  tasks:
    - create-doc.md
  templates: 
    - prd-tmpl.yaml
  checklists:
    - prd-validation.md
```

**Resolution Process**:
1. Load `pm.md` agent definition
2. Find `create-doc.md` task
3. Task references `prd-tmpl.yaml` template
4. Template may reference additional resources
5. Continue until all dependencies resolved

#### 2. **Team-Based Resolution** 
```yaml
# Starting from team file
team: team-fullstack.yaml
agents:
  - pm.md
  - architect.md
  - dev.md
  - qa.md
workflows:
  - greenfield-fullstack.yaml
```

**Resolution Process**:
1. Resolve each agent's dependencies
2. Resolve workflow dependencies
3. Merge all resources
4. Deduplicate common dependencies
5. Create unified bundle

### Bundle Optimization

#### Deduplication Strategy
```yaml
# Multiple agents may reference same resources
agent-pm:
  dependencies:
    tasks: [create-doc.md]
    
agent-architect:
  dependencies:
    tasks: [create-doc.md]  # Same task
```

**Optimization**:
- Include `create-doc.md` only once
- Reference by ID throughout bundle
- Maintain lookup table for references

#### Resource Ordering
```yaml
# Optimal bundle order for web UI
1. Core data resources (bmad-kb.md, etc.)
2. Templates (structural definitions)
3. Tasks (executable procedures)  
4. Checklists (validation criteria)
5. Agents (orchestrators)
6. Workflows (sequences)
```

**Benefits**:
- Foundation concepts loaded first
- Dependencies available before dependents
- Logical flow for AI processing

## Distribution Formats

### IDE Environment Distribution

**Structure**: Direct file access to source components
```
bmad-core/
├── agents/          # Individual agent files
├── tasks/           # Individual task files  
├── templates/       # Individual template files
├── checklists/      # Individual checklist files
├── data/            # Individual data files
└── workflows/       # Individual workflow files
```

**Access Pattern**: `{root}/{type}/{name}` resolution at runtime
**Benefits**:
- Live editing and development
- Granular component access
- Dynamic loading capabilities
- Version control friendly

### Web UI Bundle Distribution

**Structure**: Pre-built single files in `dist/` directory
```
dist/
├── agents/          # Single-agent bundles
│   ├── pm-bundle.txt
│   ├── dev-bundle.txt
│   └── architect-bundle.txt
└── teams/           # Multi-agent team bundles
    ├── team-fullstack.txt
    ├── team-all.txt
    └── team-no-ui.txt
```

**Bundle Format**:
```markdown
# BMad Agent Bundle: Team Fullstack
# Generated: 2024-01-15T10:30:00Z
# Dependencies: 47 resources resolved

## SECTION: DATA RESOURCES
### FILE: bmad-kb.md
[content...]

---SEPARATOR---

### FILE: elicitation-methods.md
[content...]

---SEPARATOR---

## SECTION: TEMPLATES  
### FILE: prd-tmpl.yaml
[content...]

---SEPARATOR---

[continues with all resources...]
```

**Benefits**:
- Single upload to web interfaces
- Complete context in one package
- No dependency resolution needed at runtime
- Optimized for large context windows

## Build Process Stages

### Stage 1: Source Analysis

```yaml
# Build system analyzes source structure
sourceAnalysis:
  - validateAgentFiles()
  - checkDependencyIntegrity()
  - identifyCircularDependencies()  
  - catalogAvailableResources()
```

**Validation Checks**:
- All declared dependencies exist
- No circular references
- Valid YAML/Markdown syntax
- Required metadata present

### Stage 2: Dependency Graph Construction

```yaml
# Create dependency graph
graphConstruction:
  - buildDependencyTree()
  - topologicalSort()
  - identifySharedResources()
  - optimizeLoadOrder()
```

**Graph Properties**:
- Directed acyclic graph (DAG)
- Topologically sorted
- Shared resources identified
- Load order optimized

### Stage 3: Resource Processing

```yaml
# Process each resource
resourceProcessing:
  - loadSourceContent()
  - applyFormatTransforms()
  - injectMetadata() 
  - validateContent()
```

**Processing Steps**:
- Load raw content from filesystem
- Apply any format transformations
- Inject build metadata (timestamps, versions)
- Validate processed content

### Stage 4: Bundle Generation

```yaml
# Generate final bundles
bundleGeneration:
  - concatenateResources()
  - insertSeparators()
  - addBundleHeaders()
  - writeOutputFiles()
```

**Bundle Structure**:
- Header with metadata
- Sections organized by type
- Clear separators between resources
- Footer with summary information

## Advanced Build Features

### Conditional Bundling

```yaml
# Bundle only specific components
build-config:
  includeComponents:
    - agents: [pm, architect]
    - tasks: [create-doc, shard-doc]
    - templates: [prd-tmpl, architecture-tmpl]
  excludeComponents:
    - checklists: [experimental-*]
```

**Use Cases**:
- Lightweight bundles for specific domains
- Beta feature exclusion
- Custom expansion pack builds

### Environment-Specific Builds

```yaml
# Different builds for different environments
environments:
  development:
    includeDebugInfo: true
    verboseLogging: true
    
  production:  
    includeDebugInfo: false
    minifyContent: true
    
  web-ui:
    format: single-file
    separators: true
    
  ide:
    format: individual-files
    preserveStructure: true
```

### Build Caching

```yaml
# Cache build artifacts for performance
caching:
  enabled: true
  strategy: dependency-hash
  invalidation: file-modification
  location: .bmad-build-cache/
```

**Benefits**:
- Faster incremental builds
- Skip unchanged components
- Reduced build times during development

## Expansion Pack Build Integration

### Pack-Specific Builds

```yaml
# Expansion pack build configuration
pack-build:
  name: game-dev-pack
  extends: core-build
  additionalComponents:
    agents: [game-designer, level-designer]
    templates: [gdd-tmpl, level-spec-tmpl]
    tasks: [create-level, validate-gameplay]
```

**Integration Process**:
1. Inherit core build configuration
2. Add expansion-specific components
3. Resolve combined dependencies
4. Generate integrated bundles

### Distribution Packaging

```yaml
# Package expansion for distribution
distribution:
  format: npm-package
  registry: public
  includes:
    - source: expansion-pack/
    - builds: dist/
    - docs: README.md
    - config: package.json
```

## Build Tool Usage

### Command-Line Interface

```bash
# Build specific bundles
node web-builder.js --agent pm
node web-builder.js --team fullstack
node web-builder.js --all

# Build with options
node web-builder.js --team fullstack --minify --exclude-experimental

# Expansion pack builds
node web-builder.js --expansion game-dev --output dist/expansion/
```

### Programmatic API

```javascript
// Use build system programmatically
const Builder = require('./web-builder');

const builder = new Builder({
  sourceDir: 'bmad-core',
  outputDir: 'dist',
  format: 'web-bundle'
});

// Build specific agent
const pmBundle = await builder.buildAgent('pm');

// Build team
const teamBundle = await builder.buildTeam('fullstack');

// Build with custom configuration
const customBundle = await builder.build({
  agents: ['pm', 'dev'],
  tasks: ['create-doc'],
  templates: ['prd-tmpl']
});
```

## Build System Extension

### Custom Build Plugins

```javascript
// Example build plugin
class CustomFormatPlugin {
  constructor(options) {
    this.options = options;
  }
  
  process(resources) {
    // Custom processing logic
    return transformResources(resources, this.options);
  }
  
  generate(processedResources) {
    // Custom output format
    return generateCustomFormat(processedResources);
  }
}

// Register plugin
builder.registerPlugin('custom-format', CustomFormatPlugin);
```

### Build Hooks

```javascript
// Build lifecycle hooks
builder.on('pre-build', (config) => {
  console.log('Starting build with config:', config);
});

builder.on('post-dependency-resolution', (graph) => {
  console.log('Dependencies resolved:', graph.nodes.length);
});

builder.on('pre-bundle', (resources) => {
  // Last chance to modify resources
  return preprocessResources(resources);
});

builder.on('post-build', (bundles) => {
  console.log('Build complete. Generated bundles:', bundles.length);
});
```

## Performance Optimization

### Build Performance Metrics

```yaml
# Typical build performance
metrics:
  full-build-time: 2.3s
  incremental-build-time: 0.8s
  dependency-resolution-time: 0.3s
  resource-processing-time: 1.2s  
  bundle-generation-time: 0.8s
```

### Optimization Strategies

1. **Parallel Processing**: Process independent resources in parallel
2. **Incremental Builds**: Only rebuild changed components
3. **Dependency Caching**: Cache resolved dependency graphs
4. **Resource Compression**: Minimize bundle sizes
5. **Smart Invalidation**: Precise cache invalidation

### Memory Management

```yaml
# Build system memory optimization
memoryManagement:
  streamProcessing: true        # Process large resources in streams
  garbageCollection: aggressive # Aggressive cleanup
  memoryLimit: 512MB           # Maximum memory usage
  chunkSize: 1MB               # Process resources in chunks
```

## Troubleshooting Build Issues

### Common Build Problems

| Problem | Cause | Solution |
|---------|--------|----------|
| **Missing Dependencies** | Broken references | Run dependency validation |
| **Circular Dependencies** | Components reference each other | Refactor architecture |
| **Build Timeout** | Large resource processing | Increase timeout limits |
| **Memory Issues** | Insufficient memory | Enable streaming mode |
| **Invalid Bundles** | Syntax errors | Run content validation |

### Build Debugging

```bash
# Enable debug mode
node web-builder.js --debug --verbose --agent pm

# Validate dependencies
node web-builder.js --validate-deps --agent pm

# Analyze build performance
node web-builder.js --profile --team fullstack

# Generate dependency graph visualization  
node web-builder.js --graph --output deps.dot --team fullstack
```

### Build System Logs

```yaml
# Build logging configuration
logging:
  level: info
  file: .ai/build-log.txt
  format: structured
  includes:
    - dependency-resolution
    - resource-processing
    - bundle-generation
    - performance-metrics
```

## Integration with Core-Config

### Build Configuration in core-config.yaml

```yaml
# Build system configuration
build:
  enabled: true
  outputDir: dist
  formats: [web-bundle, ide-bundle]
  compression: true
  
  # Environment-specific builds
  environments:
    web:
      format: web-bundle
      separators: "---SEPARATOR---"
    ide:  
      format: individual-files
      preserveStructure: true
```

### Core-Config Build Integration

The build system reads core-config.yaml to:
- Understand project structure
- Locate source components
- Apply environment-specific settings
- Generate compatible bundles

**Integration Points**:
- Document locations from config
- Sharding preferences
- Custom component definitions
- Output preferences

## Best Practices

### Build System Design

1. **Modular Architecture**: Keep build components loosely coupled
2. **Clear Separation**: Separate source analysis, processing, and generation
3. **Error Handling**: Graceful handling of missing or invalid components
4. **Performance Focus**: Optimize for typical use cases
5. **Extension Points**: Provide hooks for customization

### Bundle Design

1. **Logical Organization**: Group related resources together
2. **Clear Separators**: Use consistent separators between resources
3. **Metadata Headers**: Include build information and timestamps
4. **Size Optimization**: Balance completeness with bundle size
5. **Format Consistency**: Maintain consistent formatting across bundles

### Maintenance Practices

1. **Regular Validation**: Run dependency checks frequently
2. **Performance Monitoring**: Track build performance over time
3. **Cache Management**: Regular cache cleanup and validation
4. **Documentation**: Keep build documentation updated
5. **Testing**: Automated testing of build outputs

## Summary

The BMad build system is a sophisticated distribution and packaging engine that:

- **Resolves Dependencies**: Automatically discovers and resolves component dependencies
- **Creates Bundles**: Generates optimized bundles for different environments
- **Supports Extension**: Enables custom expansion pack builds
- **Optimizes Performance**: Provides caching, incremental builds, and parallel processing
- **Ensures Quality**: Validates components and dependencies throughout the build process

Understanding the build system is essential for:
- Creating expansion packs with custom distributions
- Optimizing bundle sizes and performance
- Troubleshooting build and dependency issues
- Customizing BMad for specific deployment environments
- Contributing to BMad's core infrastructure

The build system exemplifies BMad's philosophy of "intelligent automation" - providing powerful capabilities while maintaining simplicity for end users.