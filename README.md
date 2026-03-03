# Solutioning Framework

A systematic, archetype-based approach to solution architecture that **discovers patterns from reality** and shows how to implement them in software.

## What Are Archetypes?

**Archetypes are patterns that exist in reality, before software exists.**

This framework catalogs patterns from the physical and business world:
- **Marketplaces**: Grand Bazaar (1455), Stock Exchanges (1602), Medieval Markets
- **Physical Twins**: Temple miniatures (Hampi), Delta Works models (Rotterdam), Scale models
- **Ledgers/Records**: Land registries, accounting books, birth certificates
- **Approval Processes**: Royal courts, guild certifications, quality inspections

**Software implements these patterns digitally** - it doesn't invent them.

### Archetypes ≠ Software Architecture Patterns

This framework is NOT:
- Competing with POSA (Pattern-Oriented Software Architecture)
- Defining how to organize code (layers, microservices, pipes-filters)
- Software design patterns (Gang of Four)

This framework IS:
- Catalog of **business/domain patterns** that software implements
- Independent of implementation style (use layers OR microservices OR monolith)
- Patterns proven over centuries/millennia (not invented by framework)

```
Reality/Business World (marketplaces, twins, workflows exist)
    ↓
ARCHETYPES (our framework catalogs these patterns)
    ↓
Software Implementation (use any architectural style: layered, microservices, etc.)
    ↓
Technology Stack (AWS, Azure, open-source)
```

## Overview

Most architecture frameworks jump straight from requirements to technology ("We need microservices on Kubernetes"). This framework introduces a critical missing layer: **Solution Archetypes** - patterns from reality that apply across industries and are independent of both software architecture styles and specific technologies.

**The Framework Flow:**
```
Requirements → Solution Archetype(s) → Solution Components → Technology Stack
     ↓              ↓                      ↓                      ↓
  Business      Canonical Patterns     Instantiated         Budget/Skills
   Problems    (Single or Composed)    Components           Constrained
                                                           Technologies
```

**For complex systems**, archetypes can **compose** at multiple levels:
```
Requirements → Identify Levels → Apply Archetype Per Level → Define Integration
     ↓              ↓                    ↓                         ↓
  Multiple    Organizational       L1: Digital Twin         Explicit
  Concerns    + Technical +        L2: Marketplace          Contracts
              Integration          L3: Integration          Between Levels
```

## Core Principles

1. **Patterns Over Technologies**: Choose archetype first, technology second
2. **Data Lives Longer Than Code**: Open formats, 7+ year retention
3. **Minimize Data Stores**: 3 stores, not 6+ (reduce operational burden)
4. **Separate Evolution Speeds**: UI ≠ API ≠ Workflow ≠ Data
5. **Fitness Functions**: NFRs as automated tests
6. **Archetype Completeness Predicts Adoption**: Incomplete pattern → Poor adoption

## Predicting Adoption Success

**Key Framework Capability**: Measure how completely an implementation matches its archetype to predict adoption rates.

**Why this matters**:
- Infrastructure alone ≠ Pattern (roads without shops = no commerce)
- Incomplete marketplace (no discovery, no quality signals) → Buyers can't find sellers → Market dies
- Partial implementation raises expectations, then disappoints → Trust lost

**Archetype Completeness → Adoption Correlation**:
```
100% Completeness → 80-90% Adoption (full pattern)
70% Completeness  → 50-60% Adoption (functional with gaps)
40% Completeness  → 20-30% Adoption (critical gaps)
15% Completeness  → 10-20% Adoption (infrastructure only)
```

**Real Example**: AWS Lake Formation Data Mesh
- **Completeness**: 30% (implements access control only, missing discovery, quality signals, usage tracking)
- **Predicted Adoption**: 15-30%
- **Why**: Critical marketplace components missing → Consumers can't find products → Revert to emailing producers
- **See**: `examples/data-mesh/aws-gap-analysis.md` for complete analysis

**Methodology**: `docs/archetype-completeness-methodology.md` provides step-by-step process to:
1. Identify target archetype from requirements
2. Map all reality pattern components (pre-digital)
3. Evaluate implementation completeness (score each component)
4. Predict adoption rate (based on score)
5. Identify gaps and remediation (prioritize critical components)

**Use this to**:
- Evaluate vendor solutions before purchasing
- Predict if your project will get adopted
- Prioritize roadmap (fix critical gaps first)
- Justify budget (show ROI of closing gaps)

## Repository Structure

```
architecture-framework/
├── skills/                    # The core SKILLS (what you use)
│   ├── decode-the-meeting.md  # Extract requirements from unstructured content
│   ├── solution-mapper.md     # Archetype-based solution design
│   └── tech-stack-mapper.md   # Technology selection framework
│
├── examples/                  # Complete worked examples
│   ├── broker-verification/   # Workflow Orchestrator (single archetype)
│   ├── retail-store-opening/         # Digital Twin (single archetype)
│   └── data-mesh/             # Compositional archetypes (Digital Twin + Marketplace + Integration)
│
├── docs/                      # Framework documentation
│   ├── compositional-archetypes.md      # How archetypes compose
│   └── archetype-completeness-methodology.md  # Predict adoption success
│
├── archetypes/                # Detailed archetype documentation (coming soon)
├── templates/                 # Reusable YAML templates (coming soon)
└── principles/                # Architectural principles library (coming soon)
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

The framework catalogs 8 patterns discovered from reality:

| Archetype | Pre-Digital Origins | Modern Software Examples |
|-----------|---------------------|--------------------------|
| **Digital Twin** | Temple miniatures (Hampi), Delta Works models (Rotterdam), architectural scale models | Franchise store construction tracking, manufacturing digital twins, IoT monitoring |
| **Workflow Orchestrator** | Royal court approvals, guild certifications, multi-step inspections | Broker verification, purchase order approval, employee onboarding |
| **System of Record** | Land registries, birth certificates, accounting ledgers, guild membership rolls | Customer MDM, product catalog, vendor registry |
| **Analytics Platform** | Census records, trade statistics, astronomical observations | Sales analytics, operations reporting, ML forecasting |
| **Integration Platform** | Trade routes, postal systems, messenger networks | ERP ↔ CRM sync, legacy modernization, API orchestration |
| **Collaboration Hub** | Town squares, guild halls, royal courts | Team chat, project collaboration, document collaboration |
| **Marketplace/Portal** | Grand Bazaar (1455), Stock exchanges (1602), Medieval markets | E-commerce marketplace, SaaS app store, data product catalog |
| **Supply Chain Network** | Silk Road (200 BC - 1400s AD), Medieval wool trade (Shepherd → Weaver → Tailor), Industrial manufacturing chains | Data mesh lineage, ETL pipelines (Bronze → Silver → Gold), Manufacturing execution systems, Food traceability (farm-to-fork) |

**Key insight**: These patterns existed for centuries before software. Software simply implements them digitally.

Each archetype defines:
- **Canonical ontology**: Entity types, properties, relationships (discovered from reality)
- **Canonical components**: Asset_Registry, Workflow_Engine, etc. (how reality organizes these patterns)
- **Required capabilities**: What technology must provide (not which technology to use)

## Compositional Archetypes

For complex systems, archetypes can **compose** at multiple levels. Instead of choosing a single archetype, you identify different levels/layers and apply the appropriate archetype at each level.

### Composition Patterns

**Nesting**: One archetype contains multiple instances of another
- Example: Digital Twin (business domains) contains Marketplace instances (one per domain)

**Layering**: Different archetypes at different abstraction levels
- Example: Organizational (Digital Twin) + Technical (Marketplace) + Integration (Integration Platform)

**Integration**: Cross-cutting archetype connects composed layers
- Example: Integration Platform provides federated governance across domain marketplaces

### Example: Data Mesh

```yaml
Level 1: Digital Twin of Business Structure
  - Mirrors: Business domains (Sales, Marketing, Supply Chain)
  - Components: Domain_Registry, Domain_Ownership_Manager

Level 2: Marketplace Per Domain (nested)
  - Each domain operates as marketplace seller
  - Traded items: Data products
  - Components: Catalog_Manager, Subscription_Manager, Quality_Rating_System

Level 3: Integration Platform (cross-cutting)
  - Connects: Domain marketplaces
  - Components: Federated_Governance_Engine, Cross_Domain_Lineage_Tracker
```

**When to use composition**:
- ✅ Multiple concerns (organizational + technical)
- ✅ Repeating instances (many domains, tenants, regions)
- ✅ Conway's Law: team boundaries = architectural boundaries
- ✅ Need federated governance (global policies, local autonomy)

**See**: `docs/compositional-archetypes.md` and `examples/data-mesh/`

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

## Relationship to Software Architecture Patterns

**This framework complements (not competes with) software architecture patterns**:

```
┌─────────────────────────────────────────────┐
│ Business/Reality Patterns (This Framework)  │
│ - What patterns exist in the world?         │
│ - Marketplace, Twin, Ledger, Workflow       │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│ Software Architecture Patterns (POSA, etc.) │
│ - How to organize code?                     │
│ - Layers, Microservices, Pipes-Filters      │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│ Design Patterns (Gang of Four)              │
│ - How to structure components?              │
│ - Observer, Factory, Strategy               │
└─────────────────────────────────────────────┘
```

**Example**:
- **Reality pattern**: Marketplace (sellers, buyers, catalog, transactions)
- **This framework says**: "Your problem matches Marketplace archetype"
- **You choose architecture**: Microservices OR Monolith OR Layered
- **You choose design patterns**: Observer for notifications, Factory for product creation

**Both needed, different purposes, complementary.**

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

### Data Mesh (Compositional Archetypes)
**Domain**: Enterprise data architecture
**Problem**: Scale data architecture to 100+ domains without central bottleneck
**Composition**:
- Level 1: Digital Twin (business domains mirror organizational structure)
- Level 2: Marketplace (each domain publishes data products as tradeable commodities)
- Level 3: Integration Platform (federated governance, cross-domain lineage)
**Key Insight**: First example of compositional archetypes - domains are Digital Twin of business, each operates marketplace
**See**: `examples/data-mesh/`

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
