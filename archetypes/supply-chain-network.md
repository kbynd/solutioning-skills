# Supply Chain Network Archetype

**Archetype #8**: Supply Chain Network

**Pattern Origin**: Pre-digital (ancient trade routes, manufacturing chains)

**Core Pattern**: Multi-stage transformation flow where independent parties each add value and end-to-end visibility enables trust through provenance tracking.

---

## Pre-Digital Evidence

**This pattern existed for millennia before software**:

### Ancient Trade Routes (200 BC - 1400s AD)

**Silk Road** (China → Samarkand → Baghdad → Damascus → Venice):
- **Multi-stage flow**: Silk travels through multiple merchants at each city
- **Each adds value**: Chinese produce silk → Persian traders transport → Venetian merchants sell to Europe
- **Provenance tracking**: Caravan records, customs documents, trade stamps
- **Quality propagation**: Defective silk at source → poor quality at destination
- **End-to-end visibility**: Venetian merchant asks "Where did this come from?" → Trace back through chain

**Spice Routes** (India → Arabia → Egypt → Rome):
- **Independent parties**: Each region has local merchants
- **Transformation**: Spices harvested → dried → packaged → transported → sold
- **Supply chain documentation**: Bills of lading, port records
- **Trust through provenance**: "This pepper came from Malabar coast" (origin certification)

### Medieval Manufacturing Chains (1200s - 1800s)

**Wool Trade** (England):
```
Shepherd (raw wool)
   ↓
Wool Merchant (cleaned, sorted)
   ↓
Weaver (fabric)
   ↓
Dyer (colored fabric)
   ↓
Tailor (finished garments)
   ↓
Marketplace (sold to consumers)
```

**Characteristics**:
- **Each stage independent**: Different guilds, different locations
- **Material transformation**: Wool → fabric → clothes (value added at each stage)
- **Quality cascades**: Bad wool → bad fabric → bad clothes
- **Guild tracking**: Records show where material came from (provenance)
- **Impact propagation**: If weaver goes bankrupt → tailors affected

### Industrial Revolution Supply Chains (1800s)

**Cotton to Textiles** (Manchester, England):
- **Raw cotton** (imported from colonies) → **Cotton mills** (spinning) → **Textile factories** (weaving) → **Garment factories** (sewing) → **Retailers**
- **Documentation**: Factory records tracked material flow
- **Quality control**: Inspections at each stage
- **Logistics coordination**: Train schedules, shipping manifests

**Key Innovation**: End-to-end visibility through documentation (precursor to modern supply chain management)

---

## Modern Software Examples

### Data Pipelines (ETL/ELT Chains)

**Data Supply Chain**:
```
Source Systems (raw data)
   ↓
Bronze Layer (raw ingestion)
   ↓
Silver Layer (cleaned, standardized)
   ↓
Gold Layer (aggregated, business-ready)
   ↓
Data Marts (domain-specific)
   ↓
Dashboards/Analytics (consumed by users)
```

**Characteristics**:
- Multi-stage transformation (each layer adds value)
- Lineage tracking (where did this metric come from?)
- Quality propagation (bad data at source → bad dashboards)
- Impact analysis (schema change at bronze → breaks gold?)

### Data Mesh Prosumer Networks

**Cross-Domain Data Flow**:
```
Payments Domain (raw_transactions)
   ↓
Sales Domain (customer_360 - aggregated)
   ↓
Marketing Domain (campaign_performance - ML enriched)
   ↓
Executive Dashboard (business metrics)
```

**Characteristics**:
- Each domain is prosumer (consumes from upstream, produces for downstream)
- End-to-end lineage (Payments → Sales → Marketing → Dashboard)
- Governance propagation (PII from Payments → must be masked in Marketing)
- Carbon footprint (total compute cost of chain)

### Manufacturing Execution Systems

**Factory Floor Supply Chain**:
- **Raw materials** → **Component assembly** → **Product assembly** → **Quality testing** → **Packaging** → **Shipping**
- Real-time tracking (RFID, barcode scanning)
- Defect tracking (which batch had issues?)
- Traceability (food industry: farm-to-fork)

### Container Shipping Logistics

**Physical Goods Flow**:
- **Factory** → **Inland port** → **Container ship** → **Destination port** → **Warehouse** → **Retail/Customer**
- Track container location (GPS, port scans)
- Provenance (certificate of origin)
- Customs clearance at each stage

---

## Archetype vs Marketplace vs Integration Platform

### Key Differences

| Aspect | Marketplace | Integration Platform | Supply Chain Network |
|--------|------------|---------------------|-------------------|
| **Pattern** | Transaction point | Connect independent systems | Multi-stage flow |
| **Structure** | Single location, buyers meet sellers | Hub-spoke or mesh | Sequential stages, transformations |
| **Parties** | Many sellers, many buyers (N:M) | Independent systems (N:N) | Sequential parties (A → B → C) |
| **Focus** | Discovery, quality, transaction | Interoperability, standards | Provenance, traceability, quality cascade |
| **Pre-digital** | Grand Bazaar, stock exchange | Postal system, trade network | Silk Road, manufacturing chain |
| **Trust mechanism** | Reputation, ratings | Standards, contracts | Provenance, end-to-end visibility |
| **What's tracked** | Offerings, transactions, usage | Schemas, policies, connections | Flows, transformations, lineage |

### When to Use Which

**Use Marketplace** when:
- ✅ Many providers offering similar goods/services
- ✅ Discovery is key problem (buyers need to find sellers)
- ✅ Quality assessment needed (ratings, reviews)
- ✅ Transaction point (access control, subscriptions)

**Use Integration Platform** when:
- ✅ Independent systems need to interoperate
- ✅ Standards required (common formats, protocols)
- ✅ Federated governance (global policies, local enforcement)
- ✅ Point-to-point or hub-spoke connections

**Use Supply Chain Network** when:
- ✅ Multi-stage transformations (raw → processed → finished)
- ✅ Provenance critical (where did this come from?)
- ✅ Quality propagation matters (upstream defect → downstream impact)
- ✅ Impact analysis needed (change here → who breaks?)
- ✅ Compliance tracking (ethical sourcing, carbon footprint)

### These Often Combine

**Data Mesh Example** (uses all three):
- **Marketplace** (Level 2): Domains publish data products, consumers subscribe
- **Integration Platform** (Level 3): Federated governance, schema registry, catalog
- **Supply Chain Network** (Level 3): Cross-domain lineage, quality propagation, impact analysis

**Why all three needed**:
- Marketplace: Enables discovery and transactions
- Integration Platform: Enables interoperability between domains
- Supply Chain Network: Enables trust through provenance

**Remove any one**:
- No Marketplace → Can't find data products
- No Integration Platform → Domains can't interoperate
- No Supply Chain → Can't trust data (unknown provenance)

---

## Canonical Ontology

**Entity Types** (from pre-digital supply chains):

### SupplyChainNode
```yaml
description: "Stage in multi-stage flow (like merchant at trade route city)"
properties:
  - node_id: "unique identifier"
  - node_name: "Bronze Layer, Sales Domain, Textile Factory"
  - node_type: "raw_source, transformation, aggregation, endpoint"
  - stage_number: "position in chain (0 = source, N = endpoint)"
  - transformation: "what this stage does (clean, aggregate, enrich)"
  - owner: "who operates this node"
  - status: "active, degraded, failed"
relationships:
  - upstream_from: SupplyChainNode[] (inputs)
  - downstream_to: SupplyChainNode[] (outputs)
```

### MaterialFlow
```yaml
description: "Movement through supply chain (like goods moving through trade route)"
properties:
  - flow_id: "unique identifier"
  - material_type: "raw data, aggregated data, ML predictions"
  - source_node: "where it came from"
  - destination_node: "where it's going"
  - transformation_applied: "how it changed (SQL, Spark, Python)"
  - volume: "size (GB, rows, records)"
  - timestamp: "when it flowed"
relationships:
  - from: SupplyChainNode
  - to: SupplyChainNode
  - part_of: LineageChain
```

### LineageChain
```yaml
description: "End-to-end path (like Silk Road route)"
properties:
  - chain_id: "unique identifier"
  - root_sources: "where did it start? (source systems)"
  - endpoint: "where does it end? (dashboard, API)"
  - full_path: "all nodes traversed (A → B → C → D)"
  - chain_depth: "number of hops/stages"
  - total_carbon_footprint: "computational cost (CPU-hours, dollars)"
relationships:
  - contains: MaterialFlow[] (all flows in chain)
  - starts_at: SupplyChainNode[] (root sources)
  - ends_at: SupplyChainNode (endpoint)
```

### ProvenanceCertificate
```yaml
description: "Origin certification (like guild stamps, origin labels)"
properties:
  - certificate_id: "unique identifier"
  - certified_product: "what is certified (data product, dataset)"
  - origin_verified: "true if all sources verified"
  - compliance_status: "compliant (PII handled correctly, no violations)"
  - ethical_sourcing: "all sources meet governance policies"
  - certification_date: "when verified"
  - certifying_authority: "who verified (governance team)"
relationships:
  - certifies: DataProduct (or other entity)
  - based_on: LineageChain (traced full provenance)
```

### QualityDefect
```yaml
description: "Defect propagation (like bad wheat → bad bread)"
properties:
  - defect_id: "unique identifier"
  - defect_type: "slo_violation, schema_break, data_corruption"
  - origin_node: "where did defect start? (root cause)"
  - propagation_path: "which nodes affected? (downstream cascade)"
  - severity: "critical (breaks downstream) | warning"
  - detected_at: "timestamp"
  - resolution_status: "root cause fixed | propagating | cleared"
relationships:
  - originated_at: SupplyChainNode (root cause)
  - propagated_to: SupplyChainNode[] (affected nodes)
  - impacts: DataProduct[] (end products affected)
```

### ImpactAnalysis
```yaml
description: "What breaks if this changes? (like supplier bankruptcy impact)"
properties:
  - analysis_id: "unique identifier"
  - change_type: "schema change, node removal, SLO change"
  - source_node: "what's changing?"
  - blast_radius: "how many downstream nodes affected?"
  - affected_nodes: "which specific nodes break?"
  - mitigation_plan: "how to prevent breakage (migration, compatibility layer)"
  - notification_sent: "did we alert affected parties?"
relationships:
  - change_at: SupplyChainNode
  - affects: SupplyChainNode[] (downstream)
  - requires_action_from: Owner[] (downstream owners)
```

---

## Canonical Components

**From pre-digital supply chains** (what existed before software):

### 1. Lineage_Tracker
**Pre-digital analog**: Trade route documentation, caravan manifests

**Purpose**: Track material flow through all stages

**Operations**:
- Extract lineage from transformations (parse SQL, code)
- Build full chain: Root sources → all hops → endpoint
- Calculate lineage depth (number of transformation stages)
- Visualize supply chain (graph diagram)

**Entity types owned**: MaterialFlow, LineageChain

### 2. Provenance_Verifier
**Pre-digital analog**: Guild inspectors checking origin stamps

**Purpose**: Verify ethical sourcing through full chain

**Operations**:
- Trace to root sources (where did this originate?)
- Verify compliance at each stage (PII handling, encryption)
- Check carbon footprint (total compute cost of chain)
- Issue provenance certificate (green checkmark)

**Entity types owned**: ProvenanceCertificate

### 3. Quality_Propagation_Monitor
**Pre-digital analog**: Inspectors tracing defects through manufacturing chain

**Purpose**: Track how defects cascade downstream

**Operations**:
- Detect root violation (upstream node broke SLO)
- Trace propagation path (which downstream nodes affected?)
- Alert affected parties (notify all downstream owners)
- Track resolution (root cause fixed → clear alerts)

**Entity types owned**: QualityDefect

### 4. Impact_Analyzer
**Pre-digital analog**: Analyzing impact of supplier disruption

**Purpose**: Predict blast radius of changes

**Operations**:
- Simulate change (what if this schema changes?)
- Calculate affected nodes (who breaks?)
- Estimate blast radius (how many hops affected?)
- Notify consumers BEFORE breaking change
- Provide migration path (how to adapt)

**Entity types owned**: ImpactAnalysis

### 5. Supply_Chain_Coordinator
**Pre-digital analog**: Trade route logistics (coordinating caravans)

**Purpose**: Orchestrate multi-stage flows

**Operations**:
- Coordinate dependencies (wait for upstream before processing)
- Handle backpressure (upstream slow → downstream throttles)
- Manage failures (upstream breaks → downstream pauses)
- Optimize routes (find efficient transformation paths)

**Entity types owned**: MaterialFlow (coordination)

### 6. Carbon_Footprint_Calculator
**Pre-digital analog**: Cost accounting through supply chain

**Purpose**: Calculate total computational cost

**Operations**:
- Measure per-stage cost (CPU, memory, storage)
- Aggregate chain cost (sum all transformation costs)
- Identify expensive stages (optimize here first)
- Allocate costs (who pays for what?)

**Entity types owned**: LineageChain (cost metrics)

---

## Required Capabilities

**Technology must provide** (archetype-agnostic):

### Lineage Extraction
- Parse transformation code (SQL, Spark, Python) to extract dependencies
- Auto-discover flows (don't require manual documentation)
- Column-level lineage (which fields flow where?)

**Technologies**: OpenLineage, dbt lineage, Spark lineage, custom parsers

### Lineage Storage
- Graph database (nodes = stages, edges = flows)
- Handle large graphs (1000s of nodes, 10000s of edges)
- Fast queries (find all downstream in <1 second)

**Technologies**: Neo4j, JanusGraph, PostgreSQL with pg_graph, Amazon Neptune

### Lineage Visualization
- Network diagrams (supply chain graphs)
- Highlight paths (show specific chain)
- Interactive exploration (click node → see details)

**Technologies**: D3.js force graphs, NetworkX, Graphviz, Cytoscape.js

### Impact Analysis
- Traverse graph (find all downstream from node)
- Calculate blast radius (count affected nodes)
- Simulate changes (what-if analysis)

**Technologies**: Graph algorithms (BFS/DFS), custom traversal code

### Quality Propagation
- Event streaming (propagate SLO violations downstream)
- Trace root cause (find where violation originated)
- Clear alerts when fixed (remove propagated warnings)

**Technologies**: Kafka, Kinesis, event-driven architecture

### Provenance Tracking
- Audit trail (full lineage history)
- Compliance verification (check policies at each stage)
- Certification (issue "verified" badge)

**Technologies**: Immutable logs, blockchain (for tamper-proof), audit databases

---

## Fitness Functions

**How to validate Supply Chain Network implementation**:

### FF1: Lineage Completeness
```python
def test_lineage_coverage():
    all_products = get_all_data_products()
    products_with_lineage = get_products_with_lineage()
    coverage = len(products_with_lineage) / len(all_products)
    assert coverage > 0.90, "90% of products must have lineage tracked"
```

### FF2: Impact Analysis Speed
```python
def test_impact_analysis_performance():
    product = select_random_product()
    start = time.now()
    affected = analyze_impact(product, change_type="schema_change")
    duration = time.now() - start
    assert duration < 5.0, "Impact analysis must complete in <5 seconds"
```

### FF3: Quality Propagation Latency
```python
def test_quality_alert_propagation():
    root_product = create_slo_violation()
    start = time.now()

    # Wait for downstream alerts
    downstream = get_downstream_products(root_product)
    all_alerted = wait_for_alerts(downstream, timeout=60)

    duration = time.now() - start
    assert all_alerted, "All downstream products must be alerted"
    assert duration < 30.0, "Alerts must propagate in <30 seconds"
```

### FF4: Provenance Verification Depth
```python
def test_provenance_depth():
    products = get_all_products()
    for product in products:
        chain = get_lineage_chain(product)
        root_sources = get_root_sources(chain)
        assert len(root_sources) > 0, f"{product} has no root sources"
        assert all(is_verified(source) for source in root_sources), \
               f"{product} has unverified sources"
```

### FF5: Blast Radius Accuracy
```python
def test_blast_radius_calculation():
    # Known test case: Product A has 5 direct consumers, 12 total downstream
    product_a = get_product("test_product_a")
    analysis = analyze_impact(product_a)

    assert analysis.direct_consumers == 5
    assert analysis.total_downstream == 12
    assert set(analysis.affected_nodes) == set([known list])
```

---

## Common Mistakes

### Mistake 1: Confusing Supply Chain with Integration Platform

**Wrong**: "We have APIs connecting systems, so we have supply chain"

**Right**: Supply Chain requires **multi-stage transformations** and **end-to-end lineage**
- APIs = Integration Platform (connect systems)
- Multi-stage flow + provenance = Supply Chain Network

### Mistake 2: Manual Lineage Documentation

**Wrong**: "Developers document lineage in wiki pages"

**Right**: Lineage must be **auto-extracted** from code
- Manual docs go stale immediately
- Developers forget to update
- Use OpenLineage, dbt lineage, Spark lineage parsers

### Mistake 3: Lineage Without Impact Analysis

**Wrong**: "We show lineage graphs, done"

**Right**: Lineage is foundation, but need **operational use cases**:
- Impact analysis (what breaks if I change?)
- Quality propagation (defect at source → alert downstream)
- Provenance verification (compliance through chain)
- Without these, lineage is just pretty diagrams with no value

### Mistake 4: Ignoring Quality Propagation

**Wrong**: "Each domain monitors own quality, independent"

**Right**: Quality violations **cascade** through supply chain
- If upstream breaks SLO, downstream CAN'T meet SLO
- Must propagate alerts (notify downstream consumers)
- Must track root cause (fix source, not symptoms)

### Mistake 5: No Carbon Footprint Tracking

**Wrong**: "Just run all transformations, cost doesn't matter"

**Right**: Multi-stage chains are **expensive**
- Measure cost per stage (CPU, storage, network)
- Aggregate total chain cost
- Optimize expensive stages first
- Charge back to consumers (incentivize efficiency)

---

## Real-World Adoption Examples

### Netflix (Entertainment Data Supply Chain)

**Pattern**: Raw viewing events → Aggregated metrics → ML features → Recommendations → User experience

**Supply Chain Components**:
- Lineage tracking (where did this recommendation come from?)
- Impact analysis (change in aggregation → which ML models break?)
- Quality propagation (bad events → bad recommendations)

**Why it works**: Trust through provenance (explain why user saw recommendation)

### Uber (Operational Data Supply Chain)

**Pattern**: Raw GPS pings → Trip aggregation → Pricing models → Driver dispatch → Rider experience

**Supply Chain Components**:
- End-to-end lineage (GPS → dispatch decision)
- Quality monitoring (bad GPS → bad pricing → refund needed)
- Carbon footprint (computational cost of surge pricing)

**Why it works**: Operational excellence requires understanding full chain

### Food Industry (Farm-to-Fork Traceability)

**Pattern**: Farm → Processing plant → Distribution → Retail → Consumer

**Supply Chain Components**:
- Provenance tracking (where did this lettuce come from?)
- Quality propagation (E. coli at farm → recall all downstream)
- Compliance (organic certification through chain)

**Why it works**: Food safety requires end-to-end visibility

---

## Relationship to Other Archetypes

### Complements Marketplace/Portal

**Marketplace provides**:
- Discovery (find products)
- Quality signals (ratings, SLOs)
- Transactions (subscriptions)

**Supply Chain adds**:
- Provenance (where products came from)
- Trust (verify ethical sourcing)
- Quality propagation (upstream defects → downstream alerts)

**Together**: Marketplace + Supply Chain = Full trust (can find products AND trust their origin)

### Complements Integration Platform

**Integration Platform provides**:
- Interoperability (standards, schemas)
- Federated governance (global policies)
- Unified catalog (search across systems)

**Supply Chain adds**:
- End-to-end flows (how data moves through stages)
- Impact analysis (what breaks if system changes?)
- Quality cascade (how violations propagate)

**Together**: Integration + Supply Chain = Connected systems with full visibility

### Distinct from Digital Twin

**Digital Twin**: Software mirrors physical/business asset state
- Real-time sync (physical changes → digital updates)
- Single entity focus (one store, one patient, one machine)
- Deviation detection (model ≠ reality → alert)

**Supply Chain**: Software tracks multi-stage flows
- Multi-stage focus (raw → processed → finished)
- Material transformation (value added at each stage)
- Provenance tracking (where did this come from?)

**Different patterns for different problems**

---

## Framework Integration

### Update Solution Archetypes List

**Previous**: 7 archetypes
1. Digital Twin
2. Workflow Orchestrator
3. System of Record
4. Analytics Platform
5. Integration Platform
6. Collaboration Hub
7. Marketplace/Portal

**Now**: 8 archetypes (add Supply Chain Network)

### Update README.md Archetype Table

Add row:
```markdown
| **Supply Chain Network** | Silk Road, wool trade chains, manufacturing supply chains | Data mesh lineage, ETL pipelines, manufacturing execution systems |
```

### Update Compositional Patterns Documentation

**Data Mesh Level 3 uses TWO archetypes**:
- Integration Platform (federated governance, schema registry, catalog)
- Supply Chain Network (lineage tracking, impact analysis, quality propagation)

**Both needed, different purposes, complementary**

---

## Next Steps

1. **Add to framework README**: Update archetype count to 8
2. **Create scorecard**: Supply Chain Network archetype completeness scoring
3. **Add examples**: More supply chain implementations (manufacturing, logistics)
4. **Fitness functions**: Expand testing guidance
5. **Technology mapping**: Supply chain tech stacks (OpenLineage, Neo4j, etc.)

---

**Sources**:
- Pre-digital supply chains: Silk Road history, medieval guild records, industrial revolution documentation
- Modern software: Data mesh implementations (Netflix, Uber, LinkedIn), manufacturing execution systems
- Alvin Toffler: "The Third Wave" (prosumer concept)
- Supply chain management literature: Provenance tracking, traceability systems

**End of Supply Chain Network Archetype**
