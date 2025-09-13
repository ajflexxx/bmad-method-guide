# Deep Dive: Agent Teams in BMad - Creating Web Bundles for Expansion Packs

## Overview

Agent Teams are YAML configuration files that define which agents and workflows should be packaged together into web bundles. For expansion pack developers, teams are the mechanism for creating distributable `.txt` bundles that can be uploaded to web-based AI chat interfaces (like ChatGPT, Claude, or Gemini). Teams are NOT used by IDEs - IDE environments interact with individual agent markdown files directly.

### Core Principles

1. **Web Bundle Generation**: Teams exist solely to generate `.txt` bundles for web UIs
2. **Modularity**: Teams reference agents and workflows without duplicating them
3. **Composability**: Mix core BMad agents with expansion pack agents seamlessly
4. **Dependency Resolution**: Web-builder.js automatically resolves all agent dependencies
5. **IDE Independence**: IDEs use individual agents directly, never team bundles

### What Makes Teams Unique

Unlike individual agents which define personas and capabilities, teams:
- **Bundle Multiple Agents**: Create complete working groups for web environments
- **Include Workflows**: Package prescribed processes with agent collections
- **Generate Web Bundles**: Produce single `.txt` files for uploading to web UIs
- **Enable Distribution**: Single bundle file contains entire capability set
- **Optimize for Web**: Create appropriately-sized bundles for token-limited platforms

### Team Structure at a Glance

- Bundle Metadata
- Agent Manifest 
- Workflow Registry
- Dependency Resolution
- Bundle Generation

## Team Anatomy

Each team is a YAML configuration file that defines a deployable bundle. Teams live in `agent-teams/` directories within both bmad-core and expansion packs.

### Bundle Metadata

**Purpose**: Identifies and describes the team for both humans and build tools. The metadata provides essential information for team selection and documentation.

**Requirement**: Recommended
- When to include: Always recommended for clarity and documentation
- When NOT to include: Technically optional (builder will still process) but poor practice

**Format**:
```yaml
bundle:
  name: [Human-readable team name]
  icon: [Single emoji character]
  description: [One-line explanation of team's purpose]
```

**Examples from actual agent-teams**:

From `/workspace/bmad-method/bmad-core/agent-teams/team-fullstack.yaml:2-5`:
```yaml
bundle:
  name: Team Fullstack
  icon: 🚀
  description: Team capable of full stack, front end only, or service development.
```

From `/workspace/bmad-method/expansion-packs/bmad-2d-phaser-game-dev/agent-teams/phaser-2d-nodejs-game-team.yaml:2-5`:
```yaml
bundle:
  name: Phaser 2D NodeJS Game Team
  icon: 🎮
  description: Game Development team specialized in 2D games using Phaser 3 and TypeScript.
```

**Best Practices**:
- Use descriptive names that clearly indicate the team's domain and purpose
- Choose icons that visually represent the team's focus area
- Keep descriptions concise but informative (under 100 characters)
- Include key technologies or methodologies in the description when relevant

**Other important information**:
- The name appears in the generated bundle filename
- Icons and descriptions help document the team's purpose
- Bundle metadata is included in the generated `.txt` file for reference

### Agent Manifest

**Purpose**: Defines which agents are included in the team bundle. The manifest controls which AI personas will be available when the team is loaded.

**Requirement**: Required
- When to include: Always - a team without agents has no functionality
- When NOT to include: Never omit - empty agent list makes team unusable

**Format**:
```yaml
agents:
  - [agent-id]      # Specific agent by ID
  - [agent-id]      # Another specific agent
  - '*'             # Wildcard to include all agents (optional)
```

**Examples from actual agent-teams**:

From `/workspace/bmad-method/bmad-core/agent-teams/team-all.yaml:6-8`:
```yaml
agents:
  - bmad-orchestrator
  - "*"
```

From `/workspace/bmad-method/bmad-core/agent-teams/team-ide-minimal.yaml:6-10`:
```yaml
agents:
  - po
  - sm
  - dev
  - qa
```

**Best Practices**:
- Always include `bmad-orchestrator` for teams with workflows
- List agents in order of importance or workflow sequence
- Wildcard (`'*'`) ONLY works in core teams (not expansion packs)
- Ensure all workflow-required agents are included
- List each agent explicitly in expansion pack teams

**Other important information**:
- Agent IDs must match exact filenames (without .md extension) in agents/ directory
- Wildcard (`'*'`) only works in core teams - expands to all bmad-core agents except bmad-master
- Wildcard in expansion pack teams will log warnings and be ignored
- Core teams: Missing agents cause build to fail
- Expansion teams: Missing agents log warnings but build continues

### Workflow Registry

**Purpose**: Specifies which workflows are available when the team is active. Workflows provide guided processes for common development scenarios.

**Requirement**: Optional
- When to include: For planning/orchestration teams that guide users through processes
- When NOT to include: For execution-only teams (like IDE minimal) that just implement

**Format**:
```yaml
workflows:
  - [workflow-filename.yaml]   # Specific workflow file
  - [another-workflow.yaml]     # Additional workflow
# OR
workflows: null                 # Explicitly no workflows
```

**Examples from actual agent-teams**:

From `/workspace/bmad-method/bmad-core/agent-teams/team-fullstack.yaml:13-19`:
```yaml
workflows:
  - brownfield-fullstack.yaml
  - brownfield-service.yaml
  - brownfield-ui.yaml
  - greenfield-fullstack.yaml
  - greenfield-service.yaml
  - greenfield-ui.yaml
```

From `/workspace/bmad-method/bmad-core/agent-teams/team-ide-minimal.yaml:11`:
```yaml
workflows: null
```

**Best Practices**:
- Include only workflows compatible with the team's agents
- Order workflows by likelihood of use or complexity
- Use `null` explicitly for execution teams to clarify intent
- For expansion packs, ensure workflow files exist in workflows/ directory
- Consider workflow prerequisites when selecting which to include

**Other important information**:
- Workflow files must exist in workflows/ directory at bundle time
- For core teams: Listed workflows are included in the bundle
- For expansion packs: ALL pack workflows are included regardless of team list
- The bmad-orchestrator discovers workflows from bundle content at runtime

## Agent Resolution and Dependency Loading

### Wildcard Expansion (Core Teams Only)

**Purpose**: The wildcard (`'*'`) in core team manifests includes all bmad-core agents except bmad-master.

**Requirement**: Optional
- When to include: Only in core teams that need all core capabilities
- When NOT to include: In expansion pack teams (wildcards don't work there)

**Format (Core Teams Only)**:
```yaml
agents:
  - bmad-orchestrator   # Explicit agent first
  - '*'                 # Expands to all core agents except bmad-master
```

**How it works in core teams**:
1. DependencyResolver scans bmad-core/agents/ directory
2. Collects all .md files except bmad-master.md
3. Expands '*' to the full list at build time
4. Deduplicates if explicit agents also listed

**CRITICAL: Wildcards in Expansion Packs**:
- Wildcards DO NOT work in expansion pack teams
- Using '*' in expansion teams will log "Agent not found" warning
- You must list each agent explicitly in expansion pack teams

**Best Practices**:
- Only use wildcards in core bmad-core teams
- Always list agents explicitly in expansion pack teams
- Consider bundle size when using wildcards (adds ~500KB)

### Dependency Resolution Process

**Purpose**: When an agent is included in a team, all its direct dependencies (tasks, templates, checklists, data files) are bundled.

**Resolution algorithm** (from `/workspace/bmad-method/tools/lib/dependency-resolver.js`):
1. Read agent's YAML header for dependency lists
2. For each dependency type (tasks, templates, etc.):
   - Locate file in corresponding directory
   - Add to bundle if not already included
   - NO recursive resolution of sub-dependencies
3. Handle missing dependencies:
   - Core teams: Throw error on missing dependency
   - Expansion teams: Log warning and continue
4. Deduplicate shared resources by path

**Example dependency loading**:
```
team-fullstack.yaml
  → pm agent
    → create-doc task (loaded)
    → prd template (loaded)
    → technical-preferences.md (loaded)
    (NO further sub-dependencies resolved)
```

### Path Resolution for Expansion Packs

**Purpose**: Expansion pack teams can reference both core BMad agents and custom expansion agents.

**Resolution order**:
1. Check expansion pack's agents/ directory first
2. Fall back to bmad-core/agents/ for core agents
3. Log warning and continue if agent not found (build doesn't fail)

**Format for mixed teams**:
```yaml
agents:
  - bmad-orchestrator      # Core agent from bmad-core
  - game-designer          # Custom agent from expansion pack
  - game-developer         # Custom agent from expansion pack
  - qa                     # Core agent from bmad-core
```

## Workflow-Team Compatibility

### Workflow Discovery and Usage

**Purpose**: Workflows define step sequences with specific agents. The orchestrator discovers available workflows from bundle content.

**How workflows work**:
1. Workflows specify `agent:` fields in their step sequences
2. Workflows do NOT declare `required_agents` lists
3. No validation checks team agents against workflow needs
4. Orchestrator presents whatever workflows are in the bundle
5. If a workflow step needs a missing agent, that step may fail

**Example workflow structure**:

From greenfield-fullstack workflow:
```yaml
steps:
  - name: "Requirements Analysis"
    agent: analyst    # This step uses the analyst agent
  - name: "Create PRD"
    agent: pm        # This step uses the pm agent
```

### Agent-Workflow Relationships

**Purpose**: Understanding which agents are typically used by different workflows.

| Workflow Type | Commonly Used Agents | Notes |
|--------------|---------------------|--------|
| greenfield-fullstack | analyst, pm, ux-expert, architect, po | Full planning team |
| greenfield-service | analyst, pm, architect, po | No UX needed |
| brownfield-* | Varies based on existing system | Context-dependent |

**Important**: These are typical usage patterns, not enforced requirements.

### Handling Missing Agents

**Build-time behavior**:
- Core teams: Missing agents cause build failure
- Expansion teams: Missing agents log warnings but build continues
- No workflow validation occurs during build

**Runtime behavior**:
- Orchestrator presents all workflows found in bundle
- If workflow step references missing agent, that step may fail
- No automatic validation or "graying out" of workflows

## Creating Custom Teams for Expansion Packs

### Team Definition Process

**Step 1: Identify Required Agents**
- List all agents needed for your domain
- Include bmad-orchestrator if using workflows
- Consider mixing core and custom agents

**Step 2: Create Team YAML**

Location: `[expansion-pack]/agent-teams/[team-name].yaml`

```yaml
# <!-- Powered by BMAD™ Core -->
bundle:
  name: [Domain] Team
  icon: [Appropriate emoji]
  description: [Clear purpose statement]
agents:
  - bmad-orchestrator    # If using workflows
  - [domain-agent-1]     # Your custom agents
  - [domain-agent-2]
  - [core-agent]         # Any needed core agents
workflows:
  - [domain-workflow-1].yaml
  - [domain-workflow-2].yaml
  # OR
  # workflows: null for execution-only teams
```

**Step 3: Validate Team**
- Ensure all agent files exist
- Verify workflow compatibility
- Test bundle generation
- Check dependency resolution

### Example: Creating a Game Development Team

From `/workspace/bmad-method/expansion-packs/bmad-2d-phaser-game-dev/agent-teams/phaser-2d-nodejs-game-team.yaml`:

```yaml
# <!-- Powered by BMAD™ Core -->
bundle:
  name: Phaser 2D NodeJS Game Team
  icon: 🎮
  description: Game Development team specialized in 2D games using Phaser 3 and TypeScript.
agents:
  - analyst              # Core agent for requirements
  - bmad-orchestrator    # Core agent for orchestration
  - game-designer        # Custom: game-specific design
  - game-developer       # Custom: game implementation
  - game-sm             # Custom: game-specific stories
workflows:
  - game-dev-greenfield.yaml  # Should be .yaml not .md
  - game-prototype.yaml       # Should be .yaml not .md
```

**Important**: The actual workflow files use .yaml extension. Always verify file extensions against actual files.

**Key decisions made**:
1. Mixed core (analyst, bmad-orchestrator) with custom agents
2. Custom agents handle game-specific concerns
3. Workflows tailored to game development process
4. Clear naming indicates technology stack (Phaser, NodeJS)

## Team Selection Patterns for Expansion Packs

### Planning vs Execution Teams

**Planning Teams** (Web UI bundles):
- Include orchestrator and design agents
- Heavy on templates and documentation tasks
- Include all relevant workflows
- Generate larger web bundles

Example pattern:
```yaml
agents:
  - bmad-orchestrator
  - [domain]-designer
  - [domain]-architect
  - [domain]-analyst
workflows: 
  - [workflow1].yaml
  - [workflow2].yaml
```

**Execution Teams** (Often used for minimal web bundles):
- Minimal agent set for implementation
- Focus on development and validation agents
- No workflows needed
- Smaller bundle size for web upload

Example pattern:
```yaml
agents:
  - [domain]-developer
  - qa
workflows: null
```

**Note**: While called "execution teams", these still generate web bundles. IDEs use individual agent files directly, not teams.

### Domain-Specific Team Patterns

| Domain | Common Agent Pattern | Workflow Focus |
|--------|---------------------|----------------|
| Game Development | designer, developer, artist, tester | Prototype → Production |
| Content Creation | writer, editor, researcher, publisher | Ideation → Publication |
| Data Science | analyst, engineer, scientist, visualizer | Exploration → Deployment |
| Business | strategist, analyst, planner, validator | Planning → Execution |

## Bundle Generation Process

Bundle size expectations (measured):
- Minimal core team (team-ide-minimal): ~196 KB
- Standard core team (team-fullstack): ~427 KB
- Full/wildcard core team (team-all): ~507 KB

**Best practices for size management**:
- Use focused teams; avoid wildcards in production
- Split large teams into planning/execution variants
- Document size implications in team description
- Consider token limits of the target LLM platform

### How Bundle Generation Works

**Purpose**: The build process transforms team definitions into `.txt` bundles for web upload.

**Process overview**:

1. **Invocation**: Use `npm run build` from bmad-method directory
   - This runs tools/cli.js build command
   - NOT directly calling web-builder.js

2. **Team Processing**:
   - Reads team YAML configuration
   - Resolves agent list (wildcards only for core)
   - Loads each agent's markdown file

3. **Dependency Loading**:
   - Reads agent YAML headers for dependencies
   - Loads direct dependencies only (no recursion)
   - Expansion packs check pack first, then core

4. **Bundle Assembly**:
   - Adds "Web Agent Bundle Instructions" header
   - Concatenates all content with START/END markers
   - Preserves full file paths in markers

5. **Output**:
   - Writes to `dist/teams/[team-name].txt`
   - Ready for upload to web AI platforms

### Bundle File Structure

**Generated bundle format**:
```
Web Agent Bundle Instructions

===== START /bmad-core/agents/bmad-orchestrator.md =====
[agent content]
===== END /bmad-core/agents/bmad-orchestrator.md =====

===== START /bmad-core/tasks/create-doc.md =====
[task content]
===== END /bmad-core/tasks/create-doc.md =====

===== START /bmad-core/templates/prd.md =====
[template content]
===== END /bmad-core/templates/prd.md =====
```

**Key characteristics**:
- Plain text format for LLM consumption
- Clear START/END markers with full source file paths
- Preserves markdown formatting
- Includes direct agent dependencies only (no recursive chaining)
- Self-contained and portable

### Dependency Resolution Algorithm

**Detailed resolution steps**:

1. **Parse agent header**:
   ```yaml
   dependencies:
     tasks:
       - create-doc
       - advanced-elicitation
     templates:
       - prd
       - architecture
   ```

2. **Locate each dependency**:
   - Check `[pack]/[type]/[name].md` first
   - Fall back to `bmad-core/[type]/[name].md`
   - Error if not found in either location

3. **No recursive resolution**:
   - Only direct dependencies listed in the agent’s YAML header are bundled
   - Tasks/templates may internally link to other content, but these are not auto-included
   - No sub-dependency chaining beyond the agent’s declared lists

4. **Deduplication**:
   - Track included files by path
   - Skip if already in bundle
   - Maintain single source of truth

### Bundle Size Reality

**Actual bundle sizes from dist/teams/**:

| Team Type | Actual Size | Agent Count | Notes |
|-----------|-------------|-------------|--------|
| team-ide-minimal | ~196 KB | 4 agents | Smallest core team |
| team-no-ui | ~350 KB | 5 agents | No UX components |
| team-fullstack | ~427 KB | 6 agents | Standard web team |
| team-all | ~507 KB | All agents | Uses wildcard |

**Why bundles are larger than expected**:

1. **No minification**: Content preserved exactly as written
2. **Complete dependencies**: All tasks, templates, checklists included  
3. **Instructions overhead**: Web bundle instructions added
4. **Markdown formatting**: Full formatting preserved

**Size management strategies**:
- Create focused teams instead of comprehensive ones
- Avoid wildcards in production teams
- Consider platform token limits (bundles can use 10-20K tokens)
- Split into multiple specialized teams if needed

## Real-World Team Examples

### Core BMad Teams

**Team All** (`/workspace/bmad-method/bmad-core/agent-teams/team-all.yaml`):
```yaml
# Complete capabilities for exploration
bundle:
  name: Team All
  icon: 👥
  description: Includes every core system agent.
agents:
  - bmad-orchestrator
  - "*"  # Includes all agents
workflows:
  - brownfield-fullstack.yaml
  - brownfield-service.yaml
  - brownfield-ui.yaml
  - greenfield-fullstack.yaml
  - greenfield-service.yaml
  - greenfield-ui.yaml
```

**Team IDE Minimal** (`/workspace/bmad-method/bmad-core/agent-teams/team-ide-minimal.yaml`):
```yaml
# Execution-only for IDE environments
bundle:
  name: Team IDE Minimal
  icon: ⚡
  description: Only the bare minimum for the IDE PO SM dev qa cycle.
agents:
  - po
  - sm
  - dev
  - qa
workflows: null  # No workflows needed for execution
```

### Expansion Pack Teams

**Game Development Team** (`/workspace/bmad-method/expansion-packs/bmad-2d-phaser-game-dev/agent-teams/phaser-2d-nodejs-game-team.yaml`):
```yaml
# Domain-specific game development
bundle:
  name: Phaser 2D NodeJS Game Team
  icon: 🎮
  description: Game Development team specialized in 2D games using Phaser 3 and TypeScript.
agents:
  - analyst                # Core agent
  - bmad-orchestrator      # Core agent
  - game-designer          # Custom agent
  - game-developer         # Custom agent
  - game-sm               # Custom agent
workflows:
  - game-dev-greenfield.yaml  # Custom workflow (correct extension)
  - game-prototype.yaml       # Custom workflow (correct extension)
```

**Note**: The actual team file incorrectly references .md extensions, but the workflow files are .yaml. This is a bug in the source that should be fixed.

**Key observations**:
1. Core teams can use wildcards; expansion teams cannot
2. Expansion teams must list each agent explicitly
3. Workflow file extensions must match actual files (.yaml not .md)
4. Bundle generation continues despite warnings in expansion packs
5. All expansion pack workflows included regardless of team list

## Technical Reference

### Complete Team YAML Schema

```yaml
# Required header comment
# <!-- Powered by BMAD™ Core -->

# Required: Bundle metadata
bundle:
  name: string           # Required: Human-readable name
  icon: string          # Required: Single emoji character
  description: string   # Required: Purpose description (< 100 chars)

# Required: Agent list
agents:
  - string              # Agent ID (filename without .md)
  - "*"                 # Optional: Wildcard for all agents

# Required: Workflow list or null
workflows:
  - string              # Workflow filename (with .yaml extension)
  # OR
workflows: null         # Explicit no workflows
```

### Agent ID Validation Rules

1. **Format**: `[a-z0-9-]+` (lowercase, numbers, hyphens)
2. **Length**: 3-50 characters
3. **Uniqueness**: No duplicates within same team
4. **Existence**: Must correspond to .md file in agents/
5. **Wildcard**: Only `"*"` as quoted string is valid

### Workflow File Resolution

1. **Search order**:
   - Core teams: `bmad-core/workflows/[filename]`
   - Expansion teams: ALL files in `[pack]/workflows/` included

2. **Extension handling**:
   - Modern workflows use `.yaml` extension
   - Some legacy examples incorrectly show `.md`
   - Always verify actual file extension

3. **No validation**:
   - File must exist at bundle time
   - YAML must be valid
   - NO checking of agent requirements

## Best Practices for Team Creation

### Naming Conventions

**Team names**:
- Format: `[Domain] [Type] Team`
- Examples: "Game Development Team", "Content Creation Team"
- Avoid: Abbreviations, version numbers, personal names

**File naming**:
- Format: `[domain]-[purpose]-team.yaml`
- Examples: `game-dev-team.yaml`, `content-minimal-team.yaml`
- Keep under 30 characters for compatibility

### Icon Selection Guidelines

| Domain | Recommended Icons |
|--------|-------------------|
| Gaming | 🎮 🎲 🕹️ |
| Content | ✍️ 📝 📖 |
| Data | 📊 📊 📈 |
| Business | 💼 📈 💰 |
| Development | 🚀 ⚡ 🔧 |

### Description Writing Patterns

**Good descriptions**:
- "Game Development team specialized in 2D games using Phaser 3"
- "Content creation team for technical documentation and guides"
- "Minimal execution team for story implementation"

**Poor descriptions**:
- "My custom team" (not descriptive)
- "Team for doing stuff" (too vague)
- "The ultimate super team for everything" (unclear scope)

### Version Management

**Approach 1: Separate files**:
```
teams/
  game-team-v1.yaml
  game-team-v2.yaml
  game-team-latest.yaml -> game-team-v2.yaml
```

**Approach 2: Git versioning**:
- Single file tracked in version control
- Tags for major versions
- Branches for experimental variants

### Testing Team Configurations

**Validation checklist**:
- [ ] All agent files exist
- [ ] Workflows have required agents
- [ ] Bundle builds without errors
- [ ] Size is reasonable for target platform
- [ ] Description accurately reflects capabilities
- [ ] Icon is appropriate and renders correctly

## Common Pitfalls and Solutions

### Pitfall: Missing Agent Dependencies

**Problem**: Workflow requires agents not in team
**Symptom**: Bundle generation fails or workflow won't start
**Solution**: 
```yaml
# Check workflow requirements first
agents:
  - bmad-orchestrator
  - analyst        # Required by greenfield workflow
  - pm            # Required by greenfield workflow
  - architect     # Required by greenfield workflow
```

### Pitfall: Wildcard Overuse

**Problem**: Using `"*"` creates massive bundles
**Symptom**: Slow loading, token limit errors
**Solution**: List specific agents instead
```yaml
agents:
  - bmad-orchestrator
  - game-designer    # Specific agents only
  - game-developer
  # Avoid: - "*"
```

### Pitfall: Incorrect Path Resolution

**Problem**: Agents not found during bundle generation
**Symptom**: "Agent not found" errors
**Solution**: Ensure correct directory structure
```
bmad-expansion/
  agent-teams/
    my-team.yaml      # Team file here
  agents/
    my-agent.md       # Agents here
  workflows/
    my-workflow.yaml  # Workflows here
```

### Pitfall: Workflow Extension Mismatch

**Problem**: Wrong extension in workflow reference
**Symptom**: Workflow not found or not included in bundle
**Solution**: Use correct extension (.yaml for most workflows)
```yaml
workflows:
  - my-workflow.yaml  # Correct: .yaml extension
  # Not: my-workflow.md  # Wrong for modern workflows
  # Not: my-workflow     # Wrong: missing extension
```

**Note**: Some legacy examples incorrectly show .md extensions

## Expansion Pack Integration

### Mixing Core and Custom Agents

**Purpose**: Expansion packs often need both core BMad agents and domain-specific custom agents.

**Integration pattern**:
```yaml
agents:
  # Core agents from bmad-core/
  - bmad-orchestrator
  - analyst
  
  # Custom agents from expansion-pack/agents/
  - game-designer
  - game-developer
  # Note: No wildcards in expansion teams!
```

**Resolution order**:
1. Check expansion pack's agents/ first
2. Fall back to bmad-core/agents/
3. If agent has same name as core agent, pack version takes precedence
4. Missing agents log warning but build continues

### Custom Workflow Integration

**Workflow inclusion in expansion packs**:
```yaml
workflows:
  # Custom workflows from expansion
  - game-design.yaml
  - level-creation.yaml
  
  # Note: Core workflows NOT included unless copied to pack
```

**Important for expansion packs**:
- ALL workflows in pack's workflows/ directory are included automatically
- Team workflow list is essentially ignored for expansion bundles
- Core workflows are NOT pulled in by listing them
- To use core workflows, copy them to your expansion pack

### Distribution Strategies

**Option 1: Single comprehensive team**:
```yaml
# All-in-one team for the domain
bundle:
  name: Complete Game Dev Team
agents: [all domain agents]
workflows: [all domain workflows]
```

**Option 2: Multiple focused teams**:
```yaml
# game-planning-team.yaml
bundle:
  name: Game Planning Team
agents: [design and planning agents]

# game-dev-team.yaml  
bundle:
  name: Game Development Team
agents: [implementation agents]
```

## Testing and Validation

### Testing Team Bundles

**Manual testing**:
```bash
# Generate bundle using npm scripts
cd bmad-method
npm run build

# Or for specific expansion pack
npm run build:expansion [pack-name]

# Check output
ls -la dist/teams/
cat dist/teams/[team-name].txt | head -100
```

**Validation checks**:
1. Bundle generates without errors (core) or with only warnings (expansion)
2. All agents included in output
3. Dependencies resolved correctly
4. File size reasonable (typically 200-500KB)
5. Workflows included in bundle

### Using Built-in Validation

**Use the provided validation command**:
```bash
# Validate all teams and dependencies
cd bmad-method
npm run validate

# This runs tools/cli.js validate command
# which checks team structures and dependencies
```

**Note**: Prefer using the built-in validation over custom scripts to maintain consistency with the framework.

### Performance Considerations

**Typical bundle metrics**:
- Generation time: 1-5 seconds
- Bundle file sizes:
  - Minimal teams: ~196KB (team-ide-minimal)
  - Standard teams: ~427KB (team-fullstack)
  - Complete teams: ~507KB (team-all)
- Token usage varies by platform

**Important**: Bundle sizes are larger than you might expect because:
- All agent dependencies are included
- No minification occurs (content preserved as-is)
- Templates and tasks add significant content

## Summary

Agent Teams are YAML configurations that generate web bundles for uploading to AI chat interfaces. For expansion pack developers, teams provide:

### Key Capabilities

1. **Web Bundle Generation**: Create `.txt` files for web-based AI platforms
2. **Agent Orchestration**: Combine core and custom agents (no wildcards in expansion packs)
3. **Workflow Packaging**: Bundle workflows with agents (all pack workflows included automatically)
4. **Dependency Resolution**: Automatic loading of direct agent dependencies
5. **Distribution**: Single bundle file contains complete capability set

### Critical Knowledge for Expansion Pack Developers

**Team Creation Process**:
1. Define bundle metadata (name, icon, description)
2. List required agents explicitly (NO wildcards in expansion packs)
3. Add workflows with .yaml extension (all pack workflows included anyway)
4. Build with `npm run build` from bmad-method directory
5. Check generated bundle in dist/teams/

**Technical Reality**:
- Wildcards ('*') ONLY work in core teams, not expansion packs
- Missing agents log warnings but don't fail expansion builds
- All pack workflows included regardless of team list
- No recursive dependency resolution - only direct agent deps
- Bundles are larger than expected (200-500KB typical)

**Best Practices**:
- List every agent explicitly (no wildcards in expansion packs)
- Use .yaml extension for workflows, not .md
- Test with `npm run build` and `npm run validate`
- Expect bundles of 200-500KB (no minification occurs)
- Remember: Teams are for web only, IDEs use agents directly

### Next Steps

With this accurate understanding, expansion pack developers can:
- Create teams that generate proper web bundles
- Combine core and custom agents correctly (without wildcards)
- Build and validate teams using npm scripts
- Understand actual bundle sizes and limitations
- Avoid common pitfalls like wrong extensions or wildcard usage

Remember: Teams generate web bundles only. IDEs interact with individual agent files directly, never with team bundles. This distinction is critical for understanding BMad's architecture.
