# Compositional Archetypes

## Overview

A **compositional archetype** is formed when multiple archetypes compose at different levels or layers to create a more complex architectural pattern. This is distinct from choosing a single archetype - it's about how archetypes **combine and interact** to solve complex problems.

## Why Compositional Archetypes?

Most real-world systems are not simple enough to fit a single archetype. They require:
- **Multiple layers** (organizational, technical, integration)
- **Nested instances** (one archetype per instance of another)
- **Cross-cutting concerns** (governance, security spanning layers)

## Composition Patterns

### Pattern 1: Nesting
One archetype contains multiple instances of another archetype.

**Example**: Digital Twin containing Marketplace instances
```yaml
top_level: Digital Twin (business domains)
nested_instances:
  - each_domain: Marketplace (data products)
```

**Use when**:
- Parent archetype defines boundaries (domains, tenants, regions)
- Each boundary runs independent instance of child archetype
- Conway's Law: team boundaries = architectural boundaries

### Pattern 2: Layering
Different archetypes at different abstraction levels.

**Example**: Organizational vs. Technical layers
```yaml
layer_1_organizational: Digital Twin (business structure)
layer_2_technical: Marketplace (data products)
layer_3_integration: Integration Platform (interoperability)
```

**Use when**:
- System has multiple concerns at different levels
- Organizational structure drives technical architecture
- Need clear separation between concerns

### Pattern 3: Integration
Cross-cutting archetype connects composed layers.

**Example**: Integration Platform connecting domain marketplaces
```yaml
domains:
  - domain_a: Marketplace (products A)
  - domain_b: Marketplace (products B)
cross_cutting:
  - Integration Platform (connect A ↔ B)
```

**Use when**:
- Composed archetypes must interoperate
- Need federated governance across instances
- Data/control flows between composed layers

## Compositional Rules

### Rule 1: Conway's Law Alignment
Team boundaries should align with composition boundaries.

**Good**:
```yaml
Sales Team → owns → Sales Domain → operates → Sales Marketplace
Marketing Team → owns → Marketing Domain → operates → Marketing Marketplace
```

**Bad**:
```yaml
Central Platform Team → owns → All Domains → operates → All Marketplaces
(Defeats purpose of composition - recreates central bottleneck)
```

### Rule 2: Clear Ownership
Each composed layer must have clear ownership.

```yaml
layer_1_ownership: Domain teams own business structure (Digital Twin)
layer_2_ownership: Domain teams own data products (Marketplace)
layer_3_ownership: Platform team owns integration infrastructure
```

### Rule 3: Explicit Contracts
Layers communicate via explicit contracts, not internal knowledge.

**Good**: Schema registry, API contracts, data contracts
**Bad**: Shared database, in-memory state, tight coupling

### Rule 4: Independent Evolution
Composed layers should evolve independently (loose coupling).

**Example**:
- Domain A can change internal data products without affecting Domain B
- Platform team can upgrade integration infrastructure without breaking domains
- Business can restructure domains without breaking data products

## Common Compositions

### Composition 1: Data Mesh

**Problem**: Scale data architecture to 100s of domains without central bottleneck

**Solution**: Three-level composition
```yaml
level_1:
  archetype: Digital Twin
  mirrors: Business domains (organizational structure)
  components:
    - DomainRegistry (track all domains)
    - DomainOwnership_Manager (sync org changes)
    - Domain_Analytics (performance tracking)

level_2:
  archetype: Marketplace/Portal
  scope: Per domain (each domain = one marketplace)
  traded_items: Data products
  components:
    - Catalog_Manager (product search/discovery)
    - Subscription_Manager (access control, usage tracking)
    - Quality_Rating_System (SLO compliance)

level_3:
  archetype: Integration Platform
  scope: Cross-domain
  integrates: Data products between domains
  components:
    - Federated_Governance_Engine (global policies)
    - Cross_Domain_Lineage (track dependencies)
    - Federated_Catalog (unified search)
```

**Key Insight**: Domains are Digital Twin of business structure. Each domain runs a marketplace for data products.

**See**: `../examples/data-mesh/`

### Composition 2: Multi-Tenant SaaS with Analytics

**Problem**: Provide analytics to 1000s of tenants with tenant isolation

**Solution**: Three-level composition
```yaml
level_1:
  archetype: Marketplace/Portal
  role: Tenant management
  components:
    - Tenant_Registry (all customers)
    - Subscription_Manager (billing, usage)
    - Provider_Portal (tenant onboarding)

level_2:
  archetype: System of Record
  scope: Per tenant (tenant master data)
  components:
    - Entity_Repository (customer data, products)
    - Data_Quality_Engine (validation)
    - API_Gateway (expose to tenant)

level_3:
  archetype: Analytics Platform
  scope: Cross-tenant (aggregated insights)
  components:
    - Data_Ingestion (from all tenants)
    - Semantic_Model (cross-tenant metrics)
    - Visualization_Layer (admin dashboards)
```

**Key Insight**: Marketplace manages tenants. Each tenant gets System of Record. Platform provides cross-tenant analytics.

### Composition 3: Manufacturing with Marketplace

**Problem**: Track production line AND sell manufactured goods

**Solution**: Three-level composition
```yaml
level_1:
  archetype: Digital Twin
  mirrors: Physical production line
  components:
    - Asset_Registry (machines, equipment)
    - State_Synchronizer (real-time telemetry)
    - Process_Orchestrator (production workflows)

level_2:
  archetype: System of Record
  role: Product catalog (goods manufactured)
  components:
    - Entity_Repository (product master data)
    - Data_Quality_Engine (product specs)
    - Change_Tracker (product evolution)

level_3:
  archetype: Marketplace/Portal
  role: Sell manufactured goods
  components:
    - Catalog_Manager (browse products)
    - Transaction_Processor (orders, payments)
    - Review_System (customer feedback)
```

**Key Insight**: Digital Twin tracks manufacturing. System of Record catalogs products. Marketplace sells them.

### Composition 4: Healthcare Platform

**Problem**: Manage patient care across hospitals, labs, pharmacies

**Solution**: Three-level composition
```yaml
level_1:
  archetype: Digital Twin
  mirrors: Patient health state
  components:
    - Asset_Registry (patient records)
    - State_Synchronizer (vitals, medications)
    - Performance_Analyzer (health trends)

level_2:
  archetype: Workflow Orchestrator
  role: Care delivery processes
  components:
    - Workflow_Engine (treatment plans)
    - Approval_Manager (physician approvals)
    - Task_Manager (care team assignments)

level_3:
  archetype: Integration Platform
  role: Connect healthcare providers
  components:
    - Connector_Registry (hospitals, labs, pharmacies)
    - ETL_Engine (sync records across systems)
    - Event_Router (notifications, alerts)
```

**Key Insight**: Digital Twin is patient state. Workflows are care delivery. Integration connects providers.

## How to Identify Compositional Needs

### Ask These Questions

1. **Multiple Concerns?**
   - "Do we have organizational AND technical concerns?"
   - "Do we need domain isolation AND cross-domain collaboration?"
   - → Consider layering

2. **Repeating Instances?**
   - "Will we have many domains/tenants/regions with similar patterns?"
   - "Does each instance need isolation?"
   - → Consider nesting

3. **Cross-Cutting Integration?**
   - "Do instances need to interoperate?"
   - "Do we need federated governance?"
   - → Consider integration layer

4. **Conway's Law?**
   - "Do team boundaries map to architectural boundaries?"
   - "Will teams evolve independently?"
   - → Align composition with teams

### Red Flags (Don't Force Composition)

- ❌ **Single team**: If only one team, composition adds overhead
- ❌ **Simple problem**: If single archetype fits, don't over-engineer
- ❌ **No clear boundaries**: If can't define layer/instance boundaries, composition won't help
- ❌ **Tight coupling required**: If layers must share state, composition wrong approach

## Applying Compositional Archetypes

### Step 1: Identify Layers/Instances

**Questions**:
- What are the natural boundaries? (domains, tenants, regions, teams)
- What are the different concerns? (organizational, technical, integration)
- How do they relate? (nested, layered, cross-cutting)

**Output**: Composition structure
```yaml
composition:
  level_1: [archetype, scope, boundaries]
  level_2: [archetype, scope, boundaries]
  level_3: [archetype, scope, boundaries]
```

### Step 2: Select Archetype Per Layer

For each layer, apply solution-mapper:
- Identify archetype based on requirements at that layer
- Define ontology for that layer
- Instantiate components for that layer

**Output**: Archetype per layer with ontology + components

### Step 3: Define Integration Points

**Questions**:
- How do layers communicate? (APIs, events, shared data)
- What contracts exist? (schemas, SLOs, access policies)
- Who owns integration? (platform team, domain teams)

**Output**: Integration contracts
```yaml
layer_1_to_layer_2:
  - contract: Domain boundaries (which domain owns which data)
  - owned_by: Platform team (domain registry)

layer_2_to_layer_3:
  - contract: Data contracts (schemas, SLOs)
  - owned_by: Domain teams (publish) + Governance team (validate)
```

### Step 4: Validate Composition

**Checklist**:
- [ ] Conway's Law: Team boundaries align with composition?
- [ ] Clear ownership: Each layer has owner?
- [ ] Explicit contracts: Layers communicate via contracts?
- [ ] Independent evolution: Layers can change without breaking others?
- [ ] No circular dependencies: Layer N doesn't depend on Layer N+1?

### Step 5: Apply Tech-Stack-Mapper

For each layer, select technologies based on:
- NFRs (performance, cost, scale)
- Team skills
- Platform constraints

**Output**: Technology stack per layer

## Example Walkthrough: Data Mesh

### Step 1: Identify Layers

```yaml
level_1:
  concern: Organizational structure (business domains)
  boundaries: Domain boundaries (Sales, Marketing, Supply Chain)
  pattern: Digital Twin of business

level_2:
  concern: Data product publishing (per domain)
  boundaries: Each domain is isolated marketplace
  pattern: Marketplace per domain

level_3:
  concern: Cross-domain interoperability
  boundaries: Organization-wide governance
  pattern: Integration platform
```

### Step 2: Select Archetypes

```yaml
level_1: Digital Twin
  why: Business domains are digital representation of org structure
  ontology: BusinessDomain, DomainCapability, DomainTeam

level_2: Marketplace/Portal
  why: Data products are tradeable commodities
  ontology: DataProduct, Subscription, QualityRating

level_3: Integration Platform
  why: Need federated governance, cross-domain lineage
  ontology: GlobalPolicy, DataLineage, SchemaRegistry
```

### Step 3: Define Integration

```yaml
L1_to_L2:
  - Digital Twin defines domain boundaries
  - Each domain (from L1) operates one marketplace (L2)
  - Domain registry (L1) feeds catalog registry (L2)

L2_to_L3:
  - Marketplaces publish to federated catalog (L3)
  - Governance engine (L3) validates data contracts (L2)
  - Lineage tracker (L3) connects products across domains (L2)
```

### Step 4: Validate

- [x] Conway's Law: Sales Team owns Sales Domain owns Sales Marketplace ✅
- [x] Clear ownership: Domain teams own L1+L2, Platform team owns L3 ✅
- [x] Explicit contracts: Data contracts, schemas, SLOs ✅
- [x] Independent evolution: Domains change products without affecting others ✅
- [x] No circular deps: L1 → L2 → L3 (no cycles) ✅

### Step 5: Tech Stack (see `examples/data-mesh/technology-stack.yaml`)

## Summary

**Compositional archetypes enable**:
- ✅ Scale beyond single-archetype limits
- ✅ Conway's Law alignment (teams = boundaries)
- ✅ Independent evolution (loose coupling)
- ✅ Separation of concerns (organizational vs. technical)

**When to use**:
- Multiple layers of concern (organizational, technical, integration)
- Repeating instances (many domains, tenants, regions)
- Need for federated governance
- Team boundaries drive architecture

**How to apply**:
1. Identify layers/instances
2. Select archetype per layer
3. Define integration contracts
4. Validate composition
5. Select technologies per layer

**Examples**:
- Data Mesh (Digital Twin + Marketplace + Integration)
- Multi-tenant SaaS (Marketplace + System of Record + Analytics)
- Manufacturing (Digital Twin + System of Record + Marketplace)
- Healthcare (Digital Twin + Workflow + Integration)

---

**Next**: See complete example in `../examples/data-mesh/`
