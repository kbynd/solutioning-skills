# Data Mesh - Compositional Archetype Example

This folder contains a complete example of **data mesh as a compositional archetype** - the first example in the framework demonstrating how multiple archetypes compose at different levels.

## What Makes This Different?

**Traditional Examples** (Broker Verification, Retail Store Opening):
- Choose ONE archetype
- Apply pattern
- Done

**Compositional Example** (Data Mesh):
- Identify MULTIPLE levels
- Apply archetype PER level
- Define integration between levels
- Result: Composed system

---

## The Composition

### Three-Level Architecture

```
LEVEL 1: Digital Twin of Business Structure
    ↓ (each domain operates)
LEVEL 2: Marketplace for Data Products
    ↓ (connected via)
LEVEL 3: Integration Platform for Interoperability
```

### Why Three Levels?

**Level 1: Organizational**
- **Problem**: Need to mirror business structure digitally
- **Archetype**: Digital Twin (business domains = physical assets)
- **Pre-digital example**: Org charts on walls, phone directories, guild registries
- **Components**: Domain_Registry, Domain_Ownership_Manager, Domain_Analytics
- **Ownership**: Platform Team (infrastructure) + Domain Teams (content)

**Level 2: Technical (Per Domain)**
- **Problem**: Each domain needs to publish data products as tradeable commodities
- **Archetype**: Marketplace/Portal (data products = offerings, domain teams = sellers)
- **Pre-digital example**: Grand Bazaar (1455), Medieval markets, Stock exchanges (1602)
- **Components**: Catalog_Manager, Subscription_Manager, Quality_Rating_System, Provider_Portal
- **Ownership**: Domain Teams (each domain owns their marketplace)

**Level 3: Cross-Domain**
- **Problem**: Domains must interoperate while maintaining autonomy
- **Archetype**: Integration Platform (federated governance, cross-domain lineage)
- **Pre-digital example**: Trade routes, postal systems, market inspectors (weights & measures)
- **Components**: Federated_Governance_Engine, Cross_Domain_Lineage_Tracker, Schema_Registry
- **Ownership**: Platform Team (infrastructure) + Governance Team (policies)

---

## Pre-Digital Evidence (This Pattern Existed Before Software)

### Medieval Guild System (1400s) - Compositional Archetype

**Level 1: City Registry** (Digital Twin of Guild Structure)
- Physical: City hall maintained registry of all guilds
- Records: Which guilds exist, who leads them, what they produce
- Updates: When guilds merge, split, or dissolve

**Level 2: Guild Marketplaces** (Each Guild Operates Marketplace)
- Physical: Blacksmiths Guild sells tools, offers apprenticeships
- Catalog: Master craftsmen known by reputation
- Quality: Guild master certifications (like SLO compliance)
- Transactions: Purchase goods, contract apprenticeships

**Level 3: City Governance** (Integration Across Guilds)
- Physical: City council sets trade rules, quality standards
- Governance: Weights & measures inspectors (like schema registry)
- Interoperability: Standard currencies, contract formats
- Lineage: "Where did this material come from?" (supply chain tracking)

**This exact compositional pattern existed 600 years before software.**

---

## Key Insights

### Insight 1: Domains Are Digital Twin of Business

**Physical World** (Retail Store Opening):
- PhysicalAsset = Store
- Process = Construction
- WorkUnit = Task
- Actor = Contractor

**Business World** (Data Mesh):
- PhysicalAsset = **BusinessDomain**
- Process = **DomainCapability**
- WorkUnit = **DataProduct**
- Actor = **DomainTeam**

**The pattern is the same** - just mirroring different "physical" realities.

### Insight 2: Marketplace Economics Apply

Data products are **tradeable commodities**:
- Sellers: Domain teams
- Buyers: Data consumers
- Products: Data products
- Catalog: Data Product Registry
- Transactions: Subscriptions
- Ratings: SLO compliance scores
- Supply chain: Data lineage

### Insight 3: Integration Enables Federated Autonomy

Without Level 3, domains would be isolated silos. Integration Platform provides:
- Federated catalog (unified search across domains)
- Federated governance (global policies, local enforcement)
- Cross-domain lineage (understand dependencies)

### Insight 4: Conway's Law Drives Composition

```
Organization Structure (Level 1)
    → Business domains defined
    → Domain boundaries set

Team Boundaries (Conway's Law)
    → Each domain = one team
    → Team owns domain + marketplace

Architecture Boundaries (Level 2)
    → Each domain = one marketplace instance
    → Isolated resources, independent publishing
```

Team boundaries = domain boundaries = architectural boundaries ✅

---

## What's in This Example

### 1. `requirements.md`
**Source**: Data mesh principles (Zhamak Dehghani)

**Contents**:
- Business problem (central data team bottleneck)
- Pain points (poor quality, no discovery, no accountability)
- Proposed solution (domain ownership, data as product, self-service, federated governance)
- NFRs (performance, quality, scalability, governance)
- Success criteria (adoption, quality, time-to-insight, cost)

**Use this to see**: How compositional architectures emerge from complex requirements that span organizational and technical concerns.

---

### 2. `compositional-architecture.yaml`
**Source**: Output from solution-mapper (compositional mode)

**Contents**:
- **Level 1 (Digital Twin)**:
  - Ontology: BusinessDomain, DomainCapability, DomainTeam, DomainMetrics
  - Components: Domain_Registry, Domain_Ownership_Manager, Domain_Analytics

- **Level 2 (Marketplace)**:
  - Ontology: DataProduct, DataConsumer, Subscription, OutputPort, DataContract, QualityRating
  - Components: Catalog_Manager, Subscription_Manager, Quality_Rating_System, Provider_Portal

- **Level 3 (Integration)**:
  - Ontology: DataLineage, GlobalPolicy, SchemaRegistry, FederatedCatalog
  - Components: Federated_Governance_Engine, Cross_Domain_Lineage_Tracker, Schema_Registry

- **Integration Points**: How levels communicate (contracts, flows)
- **Team Structure**: Conway's Law alignment
- **KPI Mapping**: Business goals → components
- **Validation**: Composition rules checked

**Use this to see**: How compositional archetypes are documented - not a single archetype, but a composed system.

**Note on Level 1 (Optional)**:
- Many data mesh implementations (Netflix, Uber, LinkedIn) skip Level 1 entirely
- They use existing org tools (Okta for teams, CMDB for domain catalog)
- Level 1 is valuable when: complex matrix org, frequent restructures, need explicit domain registry
- **Recommendation**: Start with Level 2 (marketplace), add Level 1 only if needed

---

### 3. `technology-stack.yaml`
Will show technology selections for each level:
- Level 1: PostgreSQL (domain registry), Prometheus (metrics)
- Level 2: Elasticsearch (catalog search), Great Expectations (quality)
- Level 3: Apache Atlas (lineage), Confluent Schema Registry, Terraform (IaC)

---

## How This Example Was Created

### Step 1: Requirements Analysis
1. Read data mesh principles (Dehghani)
2. Extract business problems, pain points, goals
3. Identify NFRs (scale, quality, governance)
4. Document in `requirements.md`

### Step 2: Identify Composition Levels
1. **Question**: "Is this a single archetype or composition?"
   - **Answer**: Composition (organizational + technical + integration concerns)

2. **Question**: "What are the natural levels?"
   - **Answer**:
     - L1: Business structure (domains)
     - L2: Data products (per domain)
     - L3: Cross-domain integration

3. **Question**: "Do levels nest or layer?"
   - **Answer**: Both - L1 contains L2 instances (nesting), plus L3 cross-cuts (layering)

### Step 3: Apply Archetype Per Level

**Level 1**: Keywords: "business domains", "organizational structure", "mirror business"
- **Match**: Digital Twin (business = physical world being mirrored)
- **Ontology**: BusinessDomain, DomainCapability, DomainTeam
- **Components**: Domain_Registry, Domain_Ownership_Manager

**Level 2**: Keywords: "data products", "tradeable", "discovery", "subscription", "quality ratings"
- **Match**: Marketplace/Portal (data products = offerings, domains = sellers)
- **Ontology**: DataProduct, Subscription, QualityRating
- **Components**: Catalog_Manager, Subscription_Manager, Provider_Portal

**Level 3**: Keywords: "federated governance", "cross-domain", "interoperability", "lineage"
- **Match**: Integration Platform (connect domain marketplaces)
- **Ontology**: GlobalPolicy, DataLineage, SchemaRegistry
- **Components**: Federated_Governance_Engine, Cross_Domain_Lineage_Tracker

### Step 4: Define Integration Contracts

**L1 → L2**:
- Domain boundaries from Domain_Registry (L1) → organize Catalog_Manager (L2)
- Domain ownership from Domain_Ownership_Manager (L1) → control Provider_Portal (L2)

**L2 → L3**:
- Data contracts from DataContract (L2) → validated by Federated_Governance_Engine (L3)
- Product schemas from DataProduct (L2) → registered in Schema_Registry (L3)
- Lineage extracted from DataProduct (L2) → stored in Cross_Domain_Lineage_Tracker (L3)

**L1 → L3**:
- Domain list from Domain_Registry (L1) → provisions resources in Self_Serve_Data_Platform (L3)
- Domain costs from DomainMetrics (L1) → allocated via cost tracking (L3)

### Step 5: Validate Composition

- [x] Conway's Law: Team boundaries = domain boundaries = marketplace boundaries ✅
- [x] Clear ownership: Each level has owners (domains, platform, governance) ✅
- [x] Explicit contracts: Levels communicate via contracts, not shared state ✅
- [x] Independent evolution: Domains evolve independently, platform upgrades don't break domains ✅
- [x] No circular dependencies: L1 → L2 → L3 (unidirectional) ✅

### Step 6: Document

Created:
- `requirements.md` (business problem, goals, NFRs)
- `compositional-architecture.yaml` (three-level composition)
- `README.md` (this file)

---

## How to Use This Example

### For Learning

1. **Read requirements.md** → Understand why data mesh needs compositional architecture
2. **Read compositional-architecture.yaml** → See how three archetypes compose
3. **Study integration points** → Learn how composed levels communicate
4. **Compare to single-archetype examples** → Understand when composition is needed

### For Your Own Project

**Ask**: "Do I need compositional architecture?"

**Signs you DO**:
- Multiple concerns (organizational, technical, integration)
- Repeating instances (many domains, tenants, regions)
- Team boundaries drive architecture (Conway's Law)
- Need federated governance (global policies, local autonomy)

**Signs you DON'T**:
- Single team, single concern
- Simple problem fits single archetype
- No clear level/instance boundaries

**If YES**:
1. Copy this example as template
2. Identify your levels (what are boundaries?)
3. Apply archetype per level (using solution-mapper)
4. Define integration contracts
5. Validate composition (Conway's Law, ownership, contracts)
6. Apply tech-stack-mapper per level

---

## Compositional Patterns in Other Domains

### Multi-Tenant SaaS
```yaml
level_1: Marketplace/Portal (tenant management)
level_2: System of Record (per-tenant master data)
level_3: Analytics Platform (cross-tenant insights)
```

### Manufacturing
```yaml
level_1: Digital Twin (production line)
level_2: System of Record (product catalog)
level_3: Marketplace/Portal (sell goods)
```

### Healthcare
```yaml
level_1: Digital Twin (patient health)
level_2: Workflow Orchestrator (care delivery)
level_3: Integration Platform (connect providers)
```

**See**: `../../docs/compositional-archetypes.md` for more examples

---

## Framework Evolution

### Before: Single Archetype Selection

```
Requirements → Choose ONE archetype → Apply pattern
```

**Limitations**: Can't express complex systems with multiple concerns

### After: Compositional Archetypes

```
Requirements → Identify levels → Apply archetype per level → Define integration → Validate composition
```

**Benefits**:
- Express complex systems (organizational + technical)
- Handle scale (nesting: many domains, each runs marketplace)
- Conway's Law alignment (teams = boundaries)
- Independent evolution (loose coupling between levels)

### New Concepts

**Composition Patterns**:
1. **Nesting**: One archetype contains many instances of another
2. **Layering**: Different archetypes at different abstraction levels
3. **Integration**: Cross-cutting archetype connects composed layers

**Digital Twin Abstraction**:
- Can mirror physical assets (retail stores)
- Can mirror business structure (data mesh domains)
- Can mirror digital entities (user sessions)

**Marketplace Abstraction**:
- Can trade physical goods (e-commerce)
- Can trade data products (data mesh)
- Can trade services (service marketplace)

---

## Related Documentation

- **Framework**: `../../docs/compositional-archetypes.md` - Compositional pattern guide
- **Skills**: `../../skills/solution-mapper.md` - How to identify archetypes (updated with composition guidance)
- **Examples**:
  - `../broker-verification/` - Single archetype (Workflow Orchestrator)
  - `../retail-store-opening/` - Single archetype (Digital Twin of physical assets)
  - **This example** - Compositional (Digital Twin of business + Marketplace + Integration)

---

## Next Steps

1. **Understand the pattern**: Study how three archetypes compose
2. **See the benefit**: Compare to trying to fit data mesh into single archetype (doesn't work)
3. **Apply to your domain**: Identify where composition is needed in your projects
4. **Extend the framework**: Discover new compositional patterns

**Question**: What other complex systems need compositional archetypes?

---

**End of README**
