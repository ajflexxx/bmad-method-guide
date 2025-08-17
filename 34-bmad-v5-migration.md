# BMad v5.0 Migration Guide - From v4.x to v5.0

## Overview

This guide provides a comprehensive roadmap for migrating from BMad v4.x to v5.0. Version 5.0 represents a significant evolution in the BMad Method, introducing fundamental changes to quality philosophy, new testing capabilities, enhanced build systems, and cloud deployment options.

## Breaking Changes

### 1. QA Agent Transformation

#### What Changed
The QA agent has been completely reimagined as the Test Architect.

#### v4.x Structure
```yaml
agent:
  name: QA
  title: Senior Developer & QA Architect
  role: Quality gatekeeper with blocking authority
```

#### v5.0 Structure
```yaml
agent:
  name: Quinn
  id: qa  # ID remains the same for compatibility
  title: Test Architect & Quality Advisor
  role: Quality advisor providing recommendations
```

#### Migration Steps
1. Update agent references in workflows
2. Retrain teams on advisory approach
3. Update any custom QA agent extensions
4. Modify quality gate expectations

### 2. Quality Philosophy Shift

#### From Blocking to Advisory
- **v4.x**: Quality gates block progress until passed
- **v5.0**: Quality gates provide advisory recommendations

#### Impact on Workflows
```yaml
# v4.x Workflow
review:
  - qa: review-story
  - blocking: true
  - must_pass: true

# v5.0 Workflow
review:
  - test-architect: review-story
  - advisory: true
  - team_decides: true
```

### 3. New File Organization

#### Quality Artifacts Location
```
# v4.x
docs/
├── reviews/
│   └── story-reviews.md

# v5.0
docs/
├── qa/
│   ├── gates/
│   │   └── {epic}.{story}-{slug}.yml
│   ├── risks/
│   │   └── {epic}-risks.yml
│   ├── test-design/
│   │   └── {epic}-tests.yml
│   └── coverage/
│       └── {epic}-coverage.yml
```

## New Features in v5.0

### 1. Testing Task Suite

Five new tasks for comprehensive quality management:

| Task | Purpose | Output |
|------|---------|--------|
| `qa-gate` | Create advisory quality gates | YAML gate file |
| `risk-profile` | Assess project risks | Risk matrix |
| `test-design` | Design test architecture | Test strategy |
| `trace-requirements` | Map requirements to tests | Traceability matrix |
| `nfr-assess` | Validate non-functional requirements | NFR assessment |

### 2. Risk-Based Testing

#### Risk Scoring System
```yaml
risk_calculation:
  formula: "Probability × Impact"
  scale: 1-16
  categories:
    - TECH  # Technical risks
    - SEC   # Security risks
    - PERF  # Performance risks
    - DATA  # Data risks
    - BUS   # Business risks
    - OPS   # Operational risks
```

### 3. Dual Publishing Strategy

#### Package Distribution
```bash
# Stable release (production-ready)
npx bmad-method@latest install

# Beta release (bleeding edge)
npx bmad-method@beta install
```

### 4. CLAUDE.md Configuration

New AI assistant configuration file for development guidelines:

```markdown
# CLAUDE.md
- Markdown linting rules
- Development conventions
- Essential commands
- Project-specific guidelines
```

### 5. Google Cloud Integration

Complete expansion pack for GCP deployment:
- Vertex AI integration
- Cloud Run deployment
- Google Agent Development Kit (ADK)
- Production-ready templates

## Migration Checklist

### Phase 1: Preparation (Week 1)

- [ ] Review all breaking changes
- [ ] Backup current BMad installation
- [ ] Document custom modifications
- [ ] Train team on new philosophy
- [ ] Plan migration timeline

### Phase 2: Core Updates (Week 2)

- [ ] Update to BMad v5.0
  ```bash
  npm install bmad-method@latest
  ```
- [ ] Update agent references
- [ ] Migrate QA workflows to Test Architect
- [ ] Create new directory structure for quality artifacts
- [ ] Update .gitignore patterns

### Phase 3: Adopt New Features (Week 3)

- [ ] Implement risk-based testing
- [ ] Start using new testing tasks
- [ ] Create first advisory quality gates
- [ ] Set up requirements traceability
- [ ] Configure CLAUDE.md

### Phase 4: Workflow Integration (Week 4)

- [ ] Update existing workflows
- [ ] Add optional risk assessment to planning
- [ ] Integrate quality gates in review phase
- [ ] Train teams on waiver process
- [ ] Document team quality bar decisions

### Phase 5: Validation (Week 5)

- [ ] Run test workflows
- [ ] Verify agent transformations work
- [ ] Check file generation in new locations
- [ ] Validate quality gate creation
- [ ] Confirm team understanding

## Command Changes

### Deprecated Commands

```yaml
# v4.x commands that changed
- qa-review: Use 'review-story' with Test Architect
- block-release: No longer exists (advisory only)
- force-quality-gate: Replaced with advisory gates
```

### New Commands

```yaml
# v5.0 new commands
- create-qa-gate: Generate advisory quality gate
- assess-risks: Run risk profile analysis
- design-tests: Create test architecture
- trace-coverage: Map requirements to tests
- validate-nfrs: Assess non-functional requirements
```

## Workflow Migration Patterns

### Pattern 1: Simple Quality Check

#### v4.x
```yaml
workflow:
  develop:
    - implement: story
  review:
    - qa: review
    - gate: MUST_PASS
  deploy:
    - if: qa_passed
```

#### v5.0
```yaml
workflow:
  develop:
    - implement: story
  review:
    - test-architect: review-story
    - create: qa-gate
    - decision: team
  deploy:
    - regardless: gate_status
    - document: decision_rationale
```

### Pattern 2: Risk-Based Approach

#### v4.x
```yaml
workflow:
  plan:
    - create: stories
  develop:
    - implement: all
  test:
    - run: all_tests
```

#### v5.0
```yaml
workflow:
  plan:
    - create: stories
    - assess: risk-profile  # NEW
  develop:
    - implement: by_priority
  test:
    - depth: based_on_risk  # NEW
    - high_risk: comprehensive
    - low_risk: basic
```

### Pattern 3: Continuous Quality

#### v4.x
```yaml
workflow:
  sprint:
    - develop: features
    - test: end_of_sprint
    - review: final_day
```

#### v5.0
```yaml
workflow:
  sprint:
    - develop: features
    - test: continuously
    - gates: per_story     # NEW
    - review: ongoing      # NEW
    - decisions: documented # NEW
```

## Agent Team Updates

### Affected Teams

Teams that include the QA agent need updating:

1. **team-fullstack**: Update QA to Test Architect
2. **team-ide-minimal**: Update QA references
3. **team-all**: Comprehensive update needed
4. **Custom teams**: Manual migration required

### Migration Example

```yaml
# v4.x team
agents:
  - pm
  - architect
  - dev
  - qa  # Old QA agent

# v5.0 team
agents:
  - pm
  - architect
  - dev
  - qa  # Now Test Architect (same ID, new behavior)
```

## Expansion Pack Compatibility

### v4.x Expansion Packs

Most v4.x expansion packs remain compatible, but consider:

1. **Game Development Packs**: Update QA references
2. **Infrastructure Packs**: Adopt new quality gates
3. **Custom Packs**: Review for QA dependencies

### New v5.0 Expansion Packs

1. **Google Cloud AI Agent System**
   - Complete GCP deployment solution
   - Vertex AI integration
   - Production-ready templates

## Configuration Updates

### 1. Package.json

```json
{
  "dependencies": {
    "bmad-method": "^5.0.0"  // Update from ^4.x.x
  }
}
```

### 2. .gitignore

Add new patterns:
```
# v5.0 additions
flattened-codebase.xml
bmad-documentation/
.claude/
CLAUDE.md
```

### 3. Build Scripts

```json
{
  "scripts": {
    "build": "node tools/cli.js build",
    "build:agents": "node tools/cli.js build --agents-only",
    "build:teams": "node tools/cli.js build --teams-only",
    "build:expansions": "node tools/cli.js build --expansions-only"  // NEW
  }
}
```

## Common Migration Issues

### Issue 1: QA Commands Not Found

**Problem**: Workflows fail with "QA command not found"

**Solution**: Update to Test Architect commands
```yaml
# Replace
qa: validate-story

# With
test-architect: review-story
```

### Issue 2: Blocking Gates Expected

**Problem**: CI/CD expects blocking quality gates

**Solution**: Update pipeline to handle advisory gates
```yaml
# Add decision logic
if (gate.status === 'CONCERNS') {
  // Check if waived
  if (gate.waiver.active) {
    // Proceed with documented risk
  }
}
```

### Issue 3: File Location Errors

**Problem**: Quality files not found in expected location

**Solution**: Update paths to new structure
```yaml
# Old path
docs/reviews/story-1.1.md

# New path
docs/qa/gates/1.1-story-name.yml
```

## Rollback Plan

If migration issues arise:

1. **Keep v4.x backup**: Maintain previous installation
2. **Document issues**: Track specific problems
3. **Gradual migration**: Migrate team by team
4. **Parallel run**: Run v4.x and v5.0 in parallel initially

### Rollback Commands

```bash
# Rollback to v4.x
npm install bmad-method@4.36.2

# Restore v4.x workflows
git checkout v4-stable -- workflows/

# Revert agent configurations
git checkout v4-stable -- agents/
```

## Support and Resources

### Documentation

- [BMad v5.0 Testing Guide](./33-bmad-testing-guide.md)
- [Cloud Deployment Guide](./35-bmad-cloud-deployment.md)
- [Expansion Pack Guide](./31-bmad-expansion-guide.md)

### Community

- GitHub Issues: Report migration problems
- Discussions: Share migration experiences
- Wiki: Community migration tips

### Training Materials

1. **Quality Philosophy Workshop**: 2-hour session on advisory approach
2. **Test Architect Training**: Role and responsibilities
3. **Risk-Based Testing**: Practical workshop
4. **Team Autonomy**: Decision-making framework

## Migration Timeline

### Recommended Schedule

| Week | Focus | Deliverables |
|------|-------|--------------|
| 1 | Preparation | Training complete, backup created |
| 2 | Core Updates | v5.0 installed, agents updated |
| 3 | New Features | Testing tasks implemented |
| 4 | Integration | Workflows updated |
| 5 | Validation | Migration complete |

### Accelerated Migration (3 weeks)

For teams needing faster migration:
1. Combine preparation with core updates
2. Implement only critical new features
3. Defer advanced features to post-migration

## Success Criteria

Migration is complete when:

- [ ] All agents updated to v5.0
- [ ] Quality gates are advisory
- [ ] New testing tasks in use
- [ ] Teams understand waiver process
- [ ] Risk-based testing implemented
- [ ] Quality artifacts in new structure
- [ ] No v4.x dependencies remain

## Summary

BMad v5.0 migration represents a philosophical shift from enforcement to enablement. The key to successful migration is understanding that quality becomes a shared responsibility with teams making informed decisions based on Test Architect recommendations.

Focus on:
1. Training teams on the new philosophy
2. Gradual adoption of new features
3. Clear documentation of decisions
4. Continuous improvement based on outcomes

The migration effort is justified by the benefits:
- Increased team autonomy
- Faster delivery without sacrificing quality
- Better risk management
- Improved collaboration
- Data-driven quality decisions