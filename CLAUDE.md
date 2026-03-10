# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is an **architecture framework** that provides a systematic, archetype-based approach to solution architecture. It discovers patterns from reality (marketplaces, physical twins, ledgers, workflows) and shows how to implement them in software.

**Key insight**: Archetypes are patterns that existed in reality before software (Grand Bazaar 1455, temple miniatures, land registries). Software implements these patterns digitally - it doesn't invent them.

## Core Framework Flow

```
Requirements → Solution Archetype(s) → Solution Components → Technology Stack
     ↓              ↓                      ↓                      ↓
  Business      Canonical Patterns     Instantiated         Budget/Skills
   Problems    (Single or Composed)    Components           Constrained
                                                           Technologies
```

## Repository Structure

```
/
├── skills/                         # Core SKILLS (primary framework tools)
│   ├── decode-the-meeting.md       # Extract requirements from meetings/emails
│   ├── solution-mapper.md          # Map requirements → archetype → components
│   └── tech-stack-mapper.md        # Select technologies based on constraints
│
├── examples/                       # Complete worked examples
│   ├── broker-verification/        # Workflow Orchestrator (single archetype)
│   ├── data-mesh/                  # Compositional (Digital Twin + Marketplace + Integration)
│   └── mcdonalds-nso/             # Retail store opening
│
├── docs/                          # Framework documentation
│   ├── archetype-completeness-methodology.md  # Predict adoption success
│   ├── archetype-success-kpis.md              # Known failure patterns per archetype
│   ├── compositional-archetypes.md            # How archetypes compose
│   └── [other methodology docs]
│
├── archetypes/                    # Detailed archetype documentation
├── tech-stack-mapper/            # Tech selection framework
│   ├── frameworks/               # Discovery & recommendation engines
│   └── examples/                 # Tech stack examples
├── templates/                    # Reusable YAML templates
└── tools/                        # Supporting utilities
```

## The Three Core SKILLs

This framework is built around three sequential skills that transform unstructured meetings into production architecture:

### 1. `/decode-the-meeting` (skills/decode-the-meeting.md)
**Purpose**: Unstructured content → Structured requirements

**Input**: Meeting transcripts, emails, interviews, stakeholder conversations
**Output**: `requirements.md` with structured requirements

**Key concepts**:
- Extracts explicit requirements AND hidden subtext
- 5 layers: Surface problem → Blame → Comparison → Fear → Want
- Outputs keywords for solution-mapper archetype matching

### 2. `/solution-mapper` (skills/solution-mapper.md)
**Purpose**: Requirements → Archetype → Components (technology-agnostic)

**Input**: `requirements.md`, business problems, keywords
**Output**: `solution-architecture.yaml`

**Key concepts**:
- 8 solution archetypes from reality (Digital Twin, Workflow Orchestrator, Marketplace, etc.)
- Maps domain entities to canonical ontology
- Instantiates solution components from archetype patterns
- Defines required capabilities (what tech must provide, not which tech)
- MUST consult `docs/archetype-success-kpis.md` for failure patterns

### 3. `/tech-stack-mapper` (skills/tech-stack-mapper.md)
**Purpose**: Components → Technologies (budget/skills constrained)

**Input**: `solution-architecture.yaml`, NFRs, budget, team skills, platform
**Output**: `technology-stack.yaml`

**Key concepts**:
- Selects specific technologies based on constraints
- Applies architectural principles (data longevity, minimal stores, 12-factor)
- Estimates costs (POC vs production)
- Defines fitness functions (NFRs as automated tests)
- Plans evolution strategy

## Solution Archetypes Catalog

The framework catalogs 8 patterns discovered from reality:

1. **Digital Twin**: Temple miniatures (Hampi), Delta Works models → Retail store tracking, IoT monitoring
2. **Workflow Orchestrator**: Royal court approvals, guild certifications → Broker verification, purchase orders
3. **System of Record**: Land registries, birth certificates → Customer MDM, product catalog
4. **Analytics Platform**: Census records, trade statistics → Sales analytics, ML forecasting
5. **Integration Platform**: Trade routes, postal systems → ERP ↔ CRM sync, legacy modernization
6. **Collaboration Hub**: Town squares, guild halls → Team chat, project collaboration
7. **Marketplace/Portal**: Grand Bazaar (1455), Stock exchanges (1602) → E-commerce, data product catalog
8. **Supply Chain Network**: Silk Road, Medieval wool trade → Data mesh lineage, ETL pipelines

## Compositional Archetypes

For complex systems, archetypes can compose at multiple levels:

**Example (Data Mesh)**:
- Level 1: Digital Twin (business domains mirror organizational structure)
- Level 2: Marketplace (each domain publishes data products)
- Level 3: Integration Platform (federated governance)

See `docs/compositional-archetypes.md` and `examples/data-mesh/`

## Archetype Completeness → Adoption Prediction

**Critical capability**: Measure how completely an implementation matches its archetype to predict adoption rates.

```
100% Completeness → 80-90% Adoption
70% Completeness  → 50-60% Adoption
40% Completeness  → 20-30% Adoption
15% Completeness  → 10-20% Adoption (infrastructure only, no pattern)
```

**Methodology**: `docs/archetype-completeness-methodology.md`
**Real example**: `examples/data-mesh/aws-gap-analysis.md` (AWS Lake Formation at 30% completeness)

## Key Architectural Principles

1. **Patterns Over Technologies**: Choose archetype first, technology second
2. **Data Lives Longer Than Code**: Open formats (SQL, Parquet), 7+ year retention
3. **Minimize Data Stores**: 3 stores, not 6+ (reduce operational burden)
4. **Separate Evolution Speeds**: UI ≠ API ≠ Workflow ≠ Data
5. **Fitness Functions**: NFRs as automated tests
6. **Archetype Completeness Predicts Adoption**: Incomplete pattern → Poor adoption

## File Formats and Conventions

### Requirements Files
- **Format**: Markdown
- **Naming**: `requirements.md`
- **Location**: `examples/{project-name}/requirements.md`
- **Structure**: Project context, business problem, current state, goals, NFRs, constraints

### Solution Architecture Files
- **Format**: YAML
- **Naming**: `solution-architecture.yaml` or `compositional-architecture.yaml` (for multi-level)
- **Location**: `examples/{project-name}/`
- **Structure**: archetype, ontology (entities), components, capabilities, team structure

### Technology Stack Files
- **Format**: YAML
- **Naming**: `technology-stack.yaml`
- **Location**: `examples/{project-name}/`
- **Structure**: NFRs, principles, data architecture, component mappings, costs, fitness functions, evolution

## Worked Examples

### Broker Verification (`examples/broker-verification/`)
- **Archetype**: Workflow Orchestrator
- **Domain**: Financial services, mortgage broker verification
- **Problem**: Manual 10-step process, 1 day per broker
- **Technologies**: AWS Step Functions, Lambda, RDS PostgreSQL, Bedrock
- **Cost**: $774-$5,274/month POC
- **Complete with**: requirements.md, solution-architecture.yaml, technology-stack.yaml, README.md

### Data Mesh (`examples/data-mesh/`)
- **Archetypes**: Compositional (Digital Twin + Marketplace + Integration)
- **Domain**: Enterprise data architecture
- **Problem**: Scale to 100+ domains without central bottleneck
- **Key file**: `aws-gap-analysis.md` (shows archetype completeness methodology in action)
- **Complete with**: requirements.md, compositional-architecture.yaml, integration-contracts.md

## Working with This Repository

### When adding new examples:
1. Create `examples/{project-name}/` directory
2. Add `requirements.md` (use decode-the-meeting skill output as template)
3. Add `solution-architecture.yaml` (use solution-mapper skill output as template)
4. Add `technology-stack.yaml` (use tech-stack-mapper skill output as template)
5. Add `README.md` explaining the example and key decisions

### When extending archetypes:
1. Add archetype documentation to `archetypes/{archetype-name}.md`
2. Include: pre-digital origins, canonical ontology, canonical components, real examples
3. Update archetype catalog in `README.md` and `skills/solution-mapper.md`

### When adding methodology documentation:
1. Add to `docs/{topic}.md`
2. Follow existing doc structure: Purpose, Core Principle, Methodology Steps, Examples
3. Link from relevant SKILLs and README.md

## Important Framework Distinctions

### This Framework vs. Software Architecture Patterns
- **This framework**: Business/reality patterns (Marketplace, Twin, Workflow)
- **POSA/etc**: How to organize code (Layers, Microservices, Pipes-Filters)
- **Gang of Four**: Component structure (Observer, Factory, Strategy)

**All are complementary, not competing**.

### This Framework vs. Enterprise Architecture Tools
- **TOGAF/Archimate**: Heavy process, formal diagrams, top-down
- **This framework**: Lightweight YAML, directly maps to code, bottom-up

## No Build/Test Commands

This is a **documentation and methodology framework**, not a software project. There are no build commands, test suites, or package dependencies.

All work involves:
- Reading/writing Markdown documentation
- Creating/editing YAML architecture files
- Applying the three SKILLs to new projects
- Documenting examples and patterns

## Framework Philosophy

From README.md introduction and user context:
- **Reject complexity for complexity's sake** (influenced by DHH, Rich Hickey, Casey Muratori)
- **Use proven patterns from reality**, not invented abstractions
- **Make cost constraints explicit**, not afterthoughts
- **Architecture should map directly to implementation** (ontology → database schema)
- **Documentation should be practical**, not theoretical

## Version

v1.0.0 (February 2026)
