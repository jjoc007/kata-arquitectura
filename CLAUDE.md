# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repository contains C4 model architectural documentation for the "Road Warrior" trip management dashboard system. The project is part of a master's degree coursework in software architecture.

**Authors:** Pilar Mass, Juan Orjuela

## Repository Structure

The repository uses Markdown files with embedded Mermaid diagrams to document software architecture:

- `kata.md` - Contains the complete problem definition, functional requirements, quality attributes analysis, and architectural priorities for the Road Warrior system
- `diagrama.md` - Contains Mermaid diagram examples with interactive links
- `.pre-commit-config.yaml` - Placeholder pre-commit configuration (currently a no-op)

## Working with this Repository

### Viewing Diagrams

Mermaid diagrams are embedded in Markdown using code blocks with the `mermaid` language tag. These diagrams can include:
- Interactive clickable elements using the `click` syntax
- Standard Mermaid graph notation (TD for top-down, LR for left-right, etc.)

### Architecture Documentation Pattern

When working with architectural documentation in this repository, follow the structure established in `kata.md`:

1. **Problem Definition** - Clear statement of the business problem
2. **Functional Requirements** - Specific capabilities the system must provide
3. **Quality Attributes** - Both explicit (from requirements) and implicit (from domain knowledge)
4. **Attribute Prioritization** - Ranked by business and technical importance
5. **Detailed Analysis** - Deep dive into each priority attribute with justification

### Key Quality Attributes for Road Warrior System

The architecture prioritizes these attributes in order:
1. **Interoperability** - Integration with airlines, hotels, and car rental systems
2. **Availability** - 24/7 access for global users
3. **Security** - Protection of personal and financial data
4. **Scalability** - Support for traffic spikes during peak travel seasons

### Git Workflow

The repository uses a simple `master` branch workflow. When making commits, ensure changes align with the academic nature of the project and maintain clarity in architectural documentation.

### Language and Formatting

Documentation is primarily in Spanish. When editing or creating new content:
- Maintain consistent use of Spanish for technical terms and descriptions
- Use emojis in headers to improve visual organization (as established in `kata.md`)
- Use tables for structured comparisons and attribute listings
- Use Mermaid for visual diagrams rather than external diagram tools
