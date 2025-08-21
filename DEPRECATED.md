# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is the **bmad-documentation** repository - an auto-generated documentation collection that syncs with the main BMAD method framework. This repository contains comprehensive documentation about the BMAD (Breakthrough Method of Agile AI-Driven Development) methodology and is regenerated every time the main BMAD method is updated.

## Repository Relationship

- **This Repository**: Documentation-only, auto-generated from main BMAD method
- **Main BMAD Repository**: Contains the actual BMAD framework implementation
- **Update Process**: Documentation is regenerated when main BMAD method changes
- **Usage**: Reference and learning material for BMAD methodology

## Documentation Structure

Documents follow a structured numbering system for systematic learning:

- **01-09**: Foundations and overview
- **10-19**: Core component specifications (Agents, Tasks, Templates, etc.)
- **20-29**: System interaction patterns
- **30-39**: Implementation guides
- **90-99**: Reference materials (Glossary, Index)

### Key Documents for Understanding BMAD

1. **Entry Point**: [01-bmad-overview.md](01-bmad-overview.md) - Start here for system overview
2. **Core Concepts**: [02-bmad-philosophy-architecture.md](02-bmad-philosophy-architecture.md) - Philosophy and architecture
3. **Practical Usage**: [30-bmad-usage-guide.md](30-bmad-usage-guide.md) - End-to-end user journeys
4. **Complete Reference**: [99-bmad-index.md](99-bmad-index.md) - Full documentation index

## Markdown Standards

Follow these markdown linting conventions when working with documentation:

- **Blank lines around headings**: Always leave a blank line before and after headings
- **Blank lines around lists**: Always leave a blank line before and after lists
- **Blank lines around code fences**: Always leave a blank line before and after fenced code blocks
- **Fenced code block languages**: All fenced code blocks must specify a language (use `text` for plain text)
- **Single trailing newline**: Files should end with exactly one newline character
- **No trailing spaces**: Remove any trailing spaces at the end of lines

## Content Guidelines

### Terminology Consistency

- **BMad** or **BMAD**: Breakthrough Method of Agile AI-Driven Development
- **Agent**: Specialized AI personas (Mary the Analyst, Winston the PM, etc.)
- **Template**: YAML-based document generation structures
- **Workflow**: Step-by-step process definitions
- **Team**: Collections of agents working together

### Writing Style

- Direct, professional tone
- Focus on practical implementation
- Use structured examples and code blocks
- Include mermaid diagrams for complex relationships
- Cross-reference related documents using relative links

## Prompt Engineering Context

This documentation is designed for prompt engineers and AI practitioners working with the BMAD methodology:

### Key BMAD Concepts for Prompt Engineering

1. **Agent Specialization**: Each AI agent has a specific role with deep domain expertise
2. **Context-Engineered Development**: Eliminates context loss through embedded implementation details
3. **Lazy Resource Loading**: Components load only what's needed when commanded
4. **Template-Driven Output**: YAML-based templates ensure consistent document generation
5. **Advisory Quality**: Test Architect provides advisory rather than blocking QA

### Agent Architecture Understanding

- Agents are complete, self-contained systems with identities and capabilities
- Each agent has persona definitions, command structures, and dependency management
- The system uses dependency resolution to keep context windows lean
- Templates are YAML-based with structured sections and LLM instructions

## Documentation Maintenance

### When Documentation Updates

Documentation in this repository is automatically regenerated when:

- Main BMAD method framework is updated
- New agents, templates, or workflows are added
- Core architectural changes are made
- Version releases occur (stable/beta)

### Manual Maintenance Tasks

- Ensure cross-references remain valid after updates
- Verify markdown formatting follows linting rules
- Check that new concepts are properly indexed in [99-bmad-index.md](99-bmad-index.md)
- Update README.md if document organization changes

## Important Notes

- **No Development Commands**: This is documentation-only - no build, test, or deployment commands
- **Auto-Generated Content**: Manual edits may be overwritten when documentation regenerates
- **Learning Path**: Follow the numbered sequence for systematic understanding
- **Cross-References**: Documents heavily reference each other - maintain link integrity