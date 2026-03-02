# Compositional Archetype Framework

**Version**: 2.0 (March 2026)

**Major Enhancement**: Framework now supports compositional architectures - systems that require multiple archetypes working together at different levels.

---

## Framework Evolution

### v1.0: Single Archetype Selection

**Original approach** (still valid for simple systems):
```
Requirements → Identify ONE archetype → Apply pattern → Select technologies
```

**Works for**:
- Single concern (workflow automation, master data, analytics)
- Single team
- Bounded problem domain

**Examples**:
- Broker verification → Workflow Orchestrator
- Product catalog → System of Record
- Sales analytics → Analytics Platform

### v2.0: Compositional Archetypes (NEW)

**Enhanced approach** (for complex systems):
```
Requirements → Identify LEVELS/LAYERS
            ↓
            Apply archetype PER level
            ↓
            Define integration contracts between levels
            ↓
            Validate composition rules
            ↓
            Select technologies per level
```

**Works for**:
- Multiple concerns (organizational + technical + integration)
- Multiple teams (team boundaries = architectural boundaries)
- Repeating patterns (many domains/tenants/regions)
- Need for federated governance

**Examples**:
- Data mesh → Digital Twin (L1) + Marketplace (L2) + Integration Platform + Supply Chain (L3)
- Multi-tenant SaaS → Marketplace (L1) + System of Record (L2) + Analytics (L3)
- Manufacturing → Digital Twin (L1) + System of Record (L2) + Marketplace (L3)

---

## Core Concept: Archetypes Compose Like Reality

**Key Insight**: Real-world systems naturally compose multiple patterns.

**Pre-digital example** (Medieval guild system, 1400s):

```
City Registry (tracks all guilds)
    ↓ contains
Guild Marketplaces (each guild sells goods/apprenticeships)
    ↓ governed by
City Council (enforces quality standards across guilds)
```

**This is THREE different patterns**:
- **Level 1**: Registry/Digital Twin (city tracks guilds like physical assets)
- **Level 2**: Marketplace (each guild operates as seller)
- **Level 3**: Governance/Integration (city council enforces standards)

**This compositional structure existed 600 years before software.**

Software simply implements the same compositional patterns digitally.

---

## When to Use Compositional Archetypes

### Decision Tree

**Start here**: Does your system have ONE of these characteristics?

1. **Multiple distinct concerns at different levels**
   - Organizational structure + Technical operations + Integration
   - Example: Data mesh (business domains + data products + cross-domain governance)

2. **Repeating instances with same pattern**
   - Many domains, each operates similarly
   - Many tenants, each needs isolated resources
   - Example: 50 business domains, each publishes data products (50 marketplace instances)

3. **Conway's Law applies**
   - Team boundaries should map to architectural boundaries
   - Each team owns a portion of the system
   - Example: Sales team owns Sales domain owns Sales marketplace

4. **Independent evolution required**
   - Different parts of system evolve at different speeds
   - Changes in one part shouldn't break others
   - Example: Domain A changes products without affecting Domain B

**If YES to any of above** → Use compositional archetypes

**If NO to all** → Single archetype sufficient

### Red Flags (DON'T Force Composition)

❌ **Single team** - If only one team, composition adds overhead
❌ **Simple problem** - If single archetype fits cleanly, don't over-engineer
❌ **No clear boundaries** - If can't define layer/instance boundaries, composition won't help
❌ **Tight coupling required** - If layers must share state heavily, wrong pattern

---

## The Compositional Framework (5 Steps)

### Step 1: Identify Levels/Layers

**Ask**: What are the natural boundaries in this system?

**Common patterns**:

**Pattern A: Nesting** (one contains many instances of another)
```
Organization
  ↓ contains
Domains (Sales, Marketing, Finance)
  ↓ each domain has
Products/Services
```

**Pattern B: Layering** (different concerns at different levels)
```
Organizational Layer (business structure)
  ↓
Technical Layer (operations, products)
  ↓
Integration Layer (cross-cutting coordination)
```

**Pattern C: Both** (nesting + layering combined)
```
Organization (Layer 1: org structure)
  ↓ contains
Domains (Layer 2: each domain operates independently)
  ↓ coordinated by
Platform (Layer 3: cross-domain integration)
```

**Output**: Level/layer structure
```yaml
composition:
  level_1: [concern, scope, boundaries]
  level_2: [concern, scope, boundaries]
  level_3: [concern, scope, boundaries]
```

### Step 2: Apply Archetype Per Level

**For each level, identify archetype independently**:

**Method**: Use keywords from requirements + pre-digital analog

**Example** (Data mesh):

**Level 1 keywords**: "business domains", "organizational structure", "mirror business"
- **Pre-digital analog**: Org charts, city registry of guilds
- **Archetype match**: Digital Twin (business organization is "physical asset" being mirrored)
- **Ontology**: BusinessDomain, DomainCapability, DomainTeam
- **Components**: Domain_Registry, Domain_Ownership_Manager, Domain_Analytics

**Level 2 keywords**: "data products", "tradeable", "discovery", "subscription", "quality ratings"
- **Pre-digital analog**: Grand Bazaar (1455), medieval markets
- **Archetype match**: Marketplace/Portal (data products are tradeable commodities)
- **Ontology**: DataProduct, Subscription, QualityRating, OutputPort
- **Components**: Catalog_Manager, Subscription_Manager, Quality_Rating_System, Provider_Portal

**Level 3 keywords**:
- "federated governance", "schema registry", "unified catalog" → **Integration Platform**
- "lineage", "provenance", "impact analysis", "quality propagation" → **Supply Chain Network**
- **Pre-digital analogs**:
  - Integration: Postal systems, trade treaties, standard weights & measures
  - Supply Chain: Silk Road, manufacturing chains, provenance tracking
- **Archetype match**: BOTH (Integration Platform + Supply Chain Network)
- **Ontology**:
  - Integration: GlobalPolicy, SchemaRegistry, FederatedCatalog
  - Supply Chain: DataLineage, QualityPropagation, ImpactAnalysis
- **Components**:
  - Integration: Federated_Governance_Engine, Schema_Registry, Federated_Catalog_Aggregator
  - Supply Chain: Cross_Domain_Lineage_Tracker, Impact_Analyzer, Provenance_Verifier

**Output**: Archetype per level with ontology + components

### Step 3: Define Integration Contracts

**Ask**: How do levels communicate?

**Contract types**:
1. **API contracts** (REST, GraphQL) - Synchronous queries
2. **Event contracts** (Avro, Protobuf) - Async notifications
3. **Database contracts** (SQL DDL, views) - Shared data
4. **Webhook contracts** (JSON Schema) - Automation triggers

**Example** (Data mesh):

**L1 → L2**:
- **Contract**: Domain Registry API (REST/OpenAPI)
- **Purpose**: Catalog_Manager (L2) queries Domain_Registry (L1) for domain list
- **Format**: `GET /api/v1/domains → BusinessDomain[]`
- **Enforcement**: OpenAPI validation, API gateway

**L2 → L3**:
- **Contract 1**: Data Product Registration API (REST/OpenAPI)
- **Purpose**: Provider_Portal (L2) registers products with Federated_Catalog (L3)
- **Format**: `POST /api/v1/catalog/products → {product_id, schema, slos, dependencies}`
- **Enforcement**: Governance validation (reject if violates PII policy)

- **Contract 2**: Lineage Events (OpenLineage standard)
- **Purpose**: Data pipelines (L2) emit lineage to Cross_Domain_Lineage_Tracker (L3)
- **Format**: OpenLineage JSON events on Kafka topic
- **Enforcement**: Schema validation, OpenLineage spec compliance

**L1 → L3**:
- **Contract**: Platform Provisioning Webhook (JSON Schema)
- **Purpose**: Domain_Registry (L1) triggers Platform (L3) to provision infrastructure
- **Format**: `POST /api/v1/provision → {domain_id, domain_name, owner_team}`
- **Enforcement**: Webhook signature verification, Terraform validation

**Output**:
```yaml
layer_X_to_layer_Y:
  contracts:
    - name: "Contract Name"
      format: "API/Event/Database/Webhook"
      schema: "OpenAPI/Avro/DDL/JSON Schema"
      enforcement: "How contract is validated"
```

**See**: `examples/data-mesh/integration-contracts.md` for 8 concrete contract examples

### Step 4: Validate Composition

**Checklist** (must pass ALL):

- [ ] **Conway's Law alignment**: Team boundaries = composition boundaries?
  - Example: Sales Team owns Sales Domain (L1) owns Sales Marketplace (L2) ✅
  - Counter-example: Central Platform Team owns all domains ❌

- [ ] **Clear ownership**: Each level has explicit owners?
  - L1: Domain teams own business structure content
  - L2: Domain teams own data products
  - L3: Platform team owns integration infrastructure
  - ✅ Each level clearly owned

- [ ] **Explicit contracts**: Levels communicate via contracts, not internal knowledge?
  - ✅ API schemas, event schemas, database DDL all specified
  - ❌ "Components just know how to talk" (no contracts) would fail

- [ ] **Independent evolution**: Layers can change without breaking others?
  - Example: Domain A changes products → Domain B unaffected ✅
  - Example: Platform upgrades Kafka → domains unaffected (contract unchanged) ✅
  - Counter-example: Platform changes API → all domains break ❌

- [ ] **No circular dependencies**: Layer dependencies unidirectional?
  - L1 → L2 → L3 (forward only) ✅
  - L3 → L2 → L1 (circular) ❌

- [ ] **Archetype completeness**: Each level implements archetype pattern sufficiently?
  - Use archetype completeness methodology
  - L2 Marketplace: Must have Discovery, Quality, Transactions (critical components)
  - L3 Supply Chain: Must have Lineage_Tracker, Impact_Analyzer (critical components)

**Output**: Validated composition ready for technology selection

### Step 5: Select Technologies Per Level

**For each level, apply tech-stack-mapper independently**:

**Example** (Data mesh):

**Level 1 (Digital Twin)** technologies:
- Domain_Registry: PostgreSQL (relational, domain entities)
- Domain_Ownership_Manager: Okta integration (sync with HR)
- Domain_Analytics: Prometheus + Grafana (metrics)

**Level 2 (Marketplace)** technologies:
- Catalog_Manager: Elasticsearch (search), React UI
- Subscription_Manager: AWS IAM (access control), custom usage metering
- Quality_Rating_System: Great Expectations (SLO monitoring), Datadog

**Level 3 (Integration Platform + Supply Chain)** technologies:
- Integration Platform:
  - Schema_Registry: Confluent Schema Registry
  - Federated_Governance_Engine: OPA (Open Policy Agent)
  - Federated_Catalog: OpenMetadata

- Supply Chain Network:
  - Cross_Domain_Lineage_Tracker: OpenLineage + Neo4j
  - Impact_Analyzer: Custom graph algorithms on Neo4j
  - Provenance_Verifier: Audit trail in PostgreSQL

**Output**: Technology stack per level with cost estimates

---

## Compositional Patterns Library

### Pattern 1: Data Mesh

**Problem**: Scale data architecture to 100+ domains without central bottleneck

**Composition**:
```yaml
level_1: Digital Twin of Business Structure
  archetype: Digital Twin
  scope: Organization-wide
  mirrors: Business domains (Sales, Marketing, Finance)
  optional: true  # Many skip this, use existing org tools

level_2: Marketplace Per Domain
  archetype: Marketplace/Portal
  scope: Per domain (50 instances if 50 domains)
  traded_items: Data products
  instance_pattern: Each domain = one marketplace

level_3: Cross-Domain Coordination
  archetypes:
    - Integration Platform (interoperability)
    - Supply Chain Network (provenance, trust)
  scope: Organization-wide
  connects: All domain marketplaces
```

**Key characteristics**:
- Nesting: Organization contains domains, each domain operates marketplace
- Dual archetypes at L3: Integration (standards) + Supply Chain (trust)
- Conway's Law: Domain team owns domain (L1) + marketplace (L2)

**See**: `examples/data-mesh/compositional-architecture.yaml`

### Pattern 2: Multi-Tenant SaaS

**Problem**: Serve 1000s of tenants with isolation + cross-tenant analytics

**Composition**:
```yaml
level_1: Tenant Management
  archetype: Marketplace/Portal
  scope: Platform-wide
  role: Tenant onboarding, subscription management
  components: Tenant_Registry, Subscription_Manager, Billing

level_2: Per-Tenant Master Data
  archetype: System of Record
  scope: Per tenant (1000 instances if 1000 tenants)
  role: Tenant-specific master data (customers, products)
  instance_pattern: Each tenant = one database

level_3: Cross-Tenant Analytics
  archetype: Analytics Platform
  scope: Platform-wide
  role: Aggregated insights for platform operator
  components: Data_Warehouse, Semantic_Model, BI_Dashboards
```

**Key characteristics**:
- Nesting: Platform contains tenants, each tenant has System of Record
- L3 aggregates data from all L2 instances
- Isolation: L2 instances can't see each other's data

### Pattern 3: Manufacturing + Sales

**Problem**: Track production AND sell manufactured goods

**Composition**:
```yaml
level_1: Production Line Tracking
  archetype: Digital Twin
  mirrors: Physical manufacturing equipment
  components: Asset_Registry, State_Synchronizer, Process_Orchestrator

level_2: Product Catalog
  archetype: System of Record
  role: Master data for manufactured goods
  components: Entity_Repository, Data_Quality_Engine

level_3: Sales Marketplace
  archetype: Marketplace/Portal
  role: Sell manufactured goods to customers
  components: Catalog_Manager (browse products), Transaction_Processor, Review_System
```

**Key characteristics**:
- Layering: Different concerns at each level (production, catalog, sales)
- L1 → L2 flow: Production complete → product added to catalog
- L2 → L3 flow: Cataloged products → displayed in marketplace

---

## Comparison: Single vs Compositional

### Example: E-commerce System

**Single Archetype Approach**:
```yaml
archetype: Marketplace/Portal
entities: Product, Buyer, Seller, Transaction
components: Catalog_Manager, Transaction_Processor, Review_System
team: Single e-commerce team
```

**Works for**: Simple marketplace (one seller = platform, or peer-to-peer)

**Breaks down when**:
- Need to track inventory in warehouses (Digital Twin needed)
- Need supplier onboarding process (Workflow Orchestrator needed)
- Need to track product manufacturing (Supply Chain Network needed)

**Compositional Approach** (if system complex):
```yaml
level_1: Warehouse Operations
  archetype: Digital Twin
  mirrors: Physical warehouses, inventory

level_2: Product Catalog
  archetype: System of Record
  role: Master product data

level_3: Sales Marketplace
  archetype: Marketplace/Portal
  role: Customer-facing sales

level_4: Supplier Onboarding
  archetype: Workflow Orchestrator
  role: Vet and onboard new suppliers
```

**Use when**: System outgrows single archetype, multiple concerns emerge

---

## Framework Artifacts

### For Single Archetype (v1.0)

**Deliverables**:
1. `requirements.md` - Business problem, NFRs
2. `solution-architecture.yaml` - Single archetype, ontology, components
3. `technology-stack.yaml` - Technology selections, costs

**Examples**:
- `examples/broker-verification/` (Workflow Orchestrator)
- `examples/retail-store-opening/` (Digital Twin)

### For Compositional Archetypes (v2.0)

**Deliverables**:
1. `requirements.md` - Business problem, NFRs (same as v1.0)
2. `compositional-architecture.yaml` - Multi-level composition:
   - Level 1: archetype, ontology, components
   - Level 2: archetype, ontology, components
   - Level 3: archetype(s), ontology, components
   - Integration points: contracts between levels
   - Team structure: Conway's Law alignment
3. `integration-contracts.md` - Concrete contract examples (NEW)
   - API schemas (OpenAPI)
   - Event schemas (Avro, OpenLineage)
   - Database contracts (SQL DDL)
   - Webhook contracts (JSON Schema)
4. `technology-stack.yaml` - Technologies per level

**Examples**:
- `examples/data-mesh/` (Digital Twin + Marketplace + Integration + Supply Chain)

---

## Key Innovations in v2.0

### 1. Multiple Archetypes Per Level (NEW)

**Discovery**: Some levels need TWO archetypes simultaneously

**Example**: Data mesh Level 3
- **Integration Platform** (connect independent domains)
- **Supply Chain Network** (track multi-stage flows, provenance)
- Different purposes, both needed, complementary

**See**: `docs/integration-vs-supply-chain.md`

### 2. Prosumer Pattern (NEW)

**Discovery**: DataProduct is not just marketplace offering

**Realization**: DataProduct is **node in supply chain**
- Consumes from upstream products
- Produces output
- Both marketplace offering (L2) AND supply chain node (tracked at L3)

**Pre-digital analog**: Medieval merchant
- Buys wool from shepherd (consumer)
- Weaves fabric (transformation)
- Sells fabric to tailor (producer)

**This is prosumer pattern** (Alvin Toffler, "The Third Wave")

**See**: `archetypes/supply-chain-network.md`

### 3. Supply Chain Network as 8th Archetype (NEW)

**Discovery**: Supply Chain is distinct from both Marketplace and Integration Platform

**Pre-digital evidence**:
- Silk Road (200 BC - 1400s AD)
- Medieval manufacturing chains
- Industrial supply chains

**Characteristics**:
- Multi-stage transformation (raw → processed → finished)
- Provenance tracking (where did this come from?)
- Quality propagation (defect at source → impacts downstream)
- Impact analysis (change here → who breaks?)

**Modern examples**:
- Data mesh lineage (Payments → Sales → Marketing)
- ETL pipelines (Bronze → Silver → Gold)
- Manufacturing execution systems
- Food traceability (farm-to-fork)

**See**: `archetypes/supply-chain-network.md`

### 4. Integration Contracts as First-Class Artifacts (NEW)

**Problem**: Framework said "levels communicate via contracts" but didn't show formats

**Solution**: Concrete contract examples in standard formats

**8 contracts documented**:
- REST APIs (OpenAPI 3.0)
- Event streams (Avro, OpenLineage)
- Database schemas (SQL DDL)
- Webhooks (JSON Schema)

**All contracts**:
- Versioned (evolution strategy)
- Enforced (validation, not just docs)
- Testable (fitness functions)

**See**: `examples/data-mesh/integration-contracts.md`

### 5. Archetype Completeness Methodology (NEW)

**Innovation**: Predict adoption success by measuring archetype completeness

**Method**:
1. Map reality pattern components (pre-digital)
2. Score implementation (0.0 to 1.0 per component)
3. Calculate completeness percentage
4. Predict adoption rate

**Correlation**:
```
100% completeness → 80-90% adoption
70% completeness  → 50-60% adoption
40% completeness  → 20-30% adoption
15% completeness  → 10-20% adoption
```

**Example**: AWS Lake Formation = 30% completeness → predicts 15-30% adoption

**Why it works**: Incomplete archetype = pattern doesn't work (like marketplace without discovery)

**See**: `docs/archetype-completeness-methodology.md`

---

## How to Use This Framework

### For Simple Systems (Single Archetype)

1. Read requirements
2. Identify ONE archetype (keywords + pre-digital analog)
3. Apply pattern (ontology + components)
4. Select technologies
5. Done

**Time**: 1-2 days for architecture design

**Deliverables**: `solution-architecture.yaml`, `technology-stack.yaml`

### For Complex Systems (Compositional)

1. **Analyze requirements** → Identify if composition needed
   - Multiple concerns? Repeating instances? Conway's Law?

2. **Identify levels** → What are natural boundaries?
   - Organizational, technical, integration layers?
   - Nesting (org contains domains)?

3. **Apply archetype per level** → For each level independently:
   - Keywords → Pre-digital analog → Archetype match
   - Define ontology
   - Instantiate components

4. **Define integration contracts** → How levels communicate:
   - API contracts (REST/OpenAPI)
   - Event contracts (Avro/Protobuf)
   - Database contracts (SQL DDL)
   - Webhook contracts (JSON Schema)

5. **Validate composition** → Check rules:
   - Conway's Law aligned?
   - Clear ownership?
   - Explicit contracts?
   - Independent evolution?
   - No circular dependencies?

6. **Select technologies per level** → Independent tech choices:
   - L1 technologies
   - L2 technologies
   - L3 technologies

7. **Validate archetype completeness** → For each level:
   - Map reality pattern components
   - Score implementation
   - Predict adoption

**Time**: 1-2 weeks for architecture design

**Deliverables**:
- `compositional-architecture.yaml`
- `integration-contracts.md`
- `technology-stack.yaml`
- Archetype completeness scorecard

---

## Summary: The Compositional Framework

**What it is**:
- Extension of archetype framework to handle complex systems
- Supports multiple archetypes at different levels
- Explicit integration contracts between levels
- Validated composition rules
- Archetype completeness scoring

**When to use**:
- Multiple concerns (organizational + technical)
- Repeating instances (many domains/tenants)
- Conway's Law (team boundaries = architecture boundaries)
- Independent evolution needed

**How it works**:
1. Identify levels/layers (boundaries)
2. Apply archetype per level (independently)
3. Define integration contracts (how levels communicate)
4. Validate composition (rules check)
5. Select technologies per level
6. Measure completeness (predict adoption)

**Proven patterns**:
- Data Mesh: Digital Twin + Marketplace + Integration + Supply Chain
- Multi-tenant SaaS: Marketplace + System of Record + Analytics
- Manufacturing: Digital Twin + System of Record + Marketplace

**Key innovation**:
- Archetypes compose like they do in reality (pre-digital proof)
- Framework provides systematic approach to composition
- Archetype completeness predicts adoption success

---

## Framework Status

**Current Version**: 2.0 (March 2026)

**Archetypes Cataloged**: 8
1. Digital Twin
2. Workflow Orchestrator
3. System of Record
4. Analytics Platform
5. Integration Platform
6. Collaboration Hub
7. Marketplace/Portal
8. Supply Chain Network (NEW in v2.0)

**Composition Patterns**: 3
1. Nesting (one contains many instances)
2. Layering (different concerns at levels)
3. Integration (cross-cutting coordination)

**Complete Examples**:
- Single archetype: Broker verification, Retail Store Opening
- Compositional: Data mesh (Digital Twin + Marketplace + Integration + Supply Chain)

**Documentation**:
- Framework: `README.md`, `compositional-archetypes.md`
- Archetypes: `archetypes/*.md` (8 archetypes documented)
- Methodology: `archetype-completeness-methodology.md`, `integration-vs-supply-chain.md`
- Examples: `examples/*/` (3 complete examples)

---

**Next Evolution** (Future):
- More compositional patterns (e-commerce, healthcare, logistics)
- Vendor scorecard database (AWS vs GCP vs Databricks completeness scores)
- Industry-specific composition templates
- Automated composition validation tools

**End of Compositional Framework Overview**
