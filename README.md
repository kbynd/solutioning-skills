# Architecture Framework

A systematic, archetype-based approach to solution architecture that separates **architectural patterns** from **technology choices**.

## Overview

Most architecture frameworks jump straight from requirements to technology ("We need a microservices architecture with Kubernetes"). This framework introduces a critical missing layer: **Solution Archetypes** - proven patterns that apply across industries and are independent of specific technologies.

**The Framework Flow:**
```
Requirements → Solution Archetype → Solution Components → Technology Stack
     ↓              ↓                    ↓                      ↓
  Business      Canonical            Instantiated         Budget/Skills
   Problems      Patterns            Components           Constrained
                                                          Technologies
```

## Core Principles

1. **Patterns Over Technologies**: Choose archetype first, technology second
2. **Data Lives Longer Than Code**: Open formats, 7+ year retention
3. **Minimize Data Stores**: 3 stores, not 6+ (reduce operational burden)
4. **Separate Evolution Speeds**: UI ≠ API ≠ Workflow ≠ Data
5. **Fitness Functions**: NFRs as automated tests

## Repository Structure

```
architecture-framework/
├── skills/                    # The core SKILLS (what you use)
│   ├── solution-mapper.md     # Archetype-based solution design
│   └── tech-stack-mapper.md   # Technology selection framework
│
├── examples/                  # Complete worked examples
│   ├── broker-verification/   # Workflow Orchestrator archetype
│   └── retail-store-opening/         # Digital Twin archetype
│
├── archetypes/                # Detailed archetype documentation
├── templates/                 # Reusable YAML templates
├── principles/                # Architectural principles library
└── docs/                      # Framework documentation
```

## Quick Start

### 1. Read a Complete Example

Start with **`examples/broker-verification/`** to see the full flow:
- `requirements.md`: Extracted from project email
- `solution-architecture.yaml`: Output from solution-mapper
- `technology-stack.yaml`: Output from tech-stack-mapper
- `README.md`: How it was created step-by-step

### 2. Use the SKILLs

#### `/solution-mapper`: Map Requirements → Archetype → Components

**Input**: Project requirements, business problems, keywords
**Output**: Solution archetype, domain ontology, solution components

**Example**:
```yaml
Requirements: "Verify brokers, multi-step process, HQ approval, LexisNexis checks"
            ↓
Archetype: Workflow Orchestrator (not Digital Twin)
            ↓
Components: Broker_Verification_Engine, HQ_Review_Manager, Risk_Analyzer, ...
```

See `skills/solution-mapper.md` for full documentation.

#### `/tech-stack-mapper`: Select Technologies Based on Constraints

**Input**: Solution architecture, NFRs, budget, team skills, platform
**Output**: Technology selections, costs, fitness functions, evolution strategy

**Example**:
```yaml
Constraints: AWS platform, <$5K/month POC budget, Python team
            ↓
Technologies: Step Functions, Lambda, RDS PostgreSQL, Bedrock
            ↓
Cost: $774-$5,274/month ✅ Within budget
```

See `skills/tech-stack-mapper.md` for full documentation.

### 3. Apply to Your Project

1. **Extract requirements** → Document in `requirements.md`
2. **Apply solution-mapper** → Identify archetype, map ontology
3. **Apply tech-stack-mapper** → Select technologies, estimate costs
4. **Generate deliverables** → `solution-architecture.yaml`, `technology-stack.yaml`

## Solution Archetypes

The framework provides 7 reusable archetypes:

| Archetype | When to Use | Example Domains |
|-----------|-------------|-----------------|
| **Digital Twin** | Physical process with digital tracking, real-time state sync | Retail Chain store construction, manufacturing lines, supply chain |
| **Workflow Orchestrator** | Multi-step approval process, human-in-the-loop | Broker verification, purchase order approval, employee onboarding |
| **System of Record** | Single source of truth, master data management | Customer MDM, product catalog, vendor registry |
| **Analytics Platform** | Historical insights, dashboards, predictive models | Sales analytics, operations reporting, ML forecasting |
| **Integration Platform** | Connect disparate systems, data synchronization | ERP ↔ CRM sync, legacy modernization, API orchestration |
| **Collaboration Hub** | Team communication, content sharing, real-time collaboration | Team chat, project collaboration, document collaboration |
| **Marketplace/Portal** | Multi-tenant, catalog, transactions, reviews | E-commerce marketplace, SaaS app store, service marketplace |

Each archetype defines:
- **Canonical ontology**: Entity types, properties, relationships
- **Canonical components**: Asset_Registry, Workflow_Engine, etc.
- **Required capabilities**: What technology must provide (not which technology)

## Architectural Principles

The framework is built on proven principles:

### 12-Factor App
- **Factor III**: Config in environment (no hardcoded DB names)
- **Factor IV**: Backing services as attached resources (swap PostgreSQL ↔ MySQL via config)
- **Factor VI**: Stateless processes (state in data tier, not in-memory)
- **Factor XI**: Logs as event streams (queryable, analyzable)

### 12-Factor Agents (AI Systems)
- **Unify state**: Business state IS audit trail (no separate audit DB)
- **Stateless reducers**: Components are pure functions (input → output)
- **Small, focused agents**: Each component has narrow responsibility

### Evolutionary Architecture
- **Fitness functions**: NFRs as automated tests
- **Incremental change**: Evolve without breaking production
- **Appropriate coupling**: Allow technology swaps (PostgreSQL → Aurora)

### Domain-Driven Design
- **Bounded contexts**: Component boundaries = team boundaries (Conway's Law)
- **Ubiquitous language**: Ontology defines shared vocabulary
- **Aggregate roots**: Each component owns specific entity types

## Key Differentiators

### vs. Traditional Architecture Frameworks

| Traditional | This Framework |
|-------------|----------------|
| Requirements → Technology | Requirements → Archetype → Technology |
| "Build microservices on Kubernetes" | "Use Workflow Orchestrator pattern" (tech agnostic) |
| Cost is afterthought | Cost constraints drive tech selection |
| NFRs documented, not tested | NFRs are fitness functions (automated tests) |
| Data architecture = App architecture | Data lives longer than code (separate concerns) |

### vs. Enterprise Architecture Tools

| Enterprise Tools (Archimate, TOGAF) | This Framework |
|--------------------------------------|----------------|
| Heavy process, formal diagrams | Lightweight, YAML-based |
| Architecture ≠ Implementation | Directly maps to code (ontology → database schema) |
| Technology-agnostic (too abstract) | Technology-specific but archetype-first |
| Top-down governance | Bottom-up, team-aligned (Conway's Law) |

## Complete Examples

### Broker Verification (Workflow Orchestrator)
**Domain**: Financial services, mortgage broker verification
**Problem**: Manual 10-step process, 1 day per broker
**Archetype**: Workflow Orchestrator (multi-step approval, human-in-the-loop)
**Technologies**: AWS Step Functions, Lambda, RDS PostgreSQL, Bedrock
**Cost**: $774-$5,274/month POC, $600-$5,500/month production
**See**: `examples/broker-verification/`

### Retail Store Opening (Digital Twin)
**Domain**: Restaurant construction and tech installation
**Problem**: Track 195 stores/year, 95% on-time gate completion
**Archetype**: Digital Twin with Ontology-Based Operations
**Technologies**: Microsoft Fabric IQ Ontology, Lakehouse, Event Hubs
**See**: Documented in `skills/solution-mapper.md` (lines 855-1499)

## Framework Benefits

### For Architects
- ✅ **Reusable patterns**: Don't reinvent the wheel, use proven archetypes
- ✅ **Clear rationale**: Document why you chose technologies (not just what)
- ✅ **Cost-aware**: Budget constraints drive decisions (not technology hype)
- ✅ **Testable NFRs**: Fitness functions validate architecture continuously

### For Developers
- ✅ **Clear ontology**: Domain entities mapped to database schema
- ✅ **Component boundaries**: Know what each team owns (Conway's Law)
- ✅ **Technology rationale**: Understand why PostgreSQL, not DynamoDB

### For Business
- ✅ **Cost transparency**: Know POC vs. production costs upfront
- ✅ **Evolution strategy**: Understand how system will evolve over time
- ✅ **ROI clarity**: See cost savings (e.g., $30K/year from automation)

### For Compliance/Governance
- ✅ **Data retention**: 7-year compliance built into data architecture
- ✅ **Audit trail**: Business state IS audit trail (no separate DB)
- ✅ **Open formats**: Data portable (SQL, Parquet, CSV for archival)

## Getting Started

1. **Read**: `examples/broker-verification/README.md` (complete walkthrough)
2. **Learn**: `skills/solution-mapper.md` (archetype catalog)
3. **Apply**: `skills/tech-stack-mapper.md` (technology selection)
4. **Practice**: Use templates in `templates/` for your project

## Contributing

This framework is a work in progress. Contributions welcome:
- New archetypes (e.g., Event Processing Platform)
- New examples (different domains, different platforms)
- Improved fitness functions
- Technology mapping templates (AWS, Azure, GCP, open-source)

## License

[To be determined - Open source or Sonata proprietary]

## Authors

- Kalyan Vennelakanti (Sonata Software)
- Built with Claude (Anthropic)

## Version

v1.0.0 (February 2026)
