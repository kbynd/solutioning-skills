# Data Mesh - Requirements

**Source**: Data mesh principles (Zhamak Dehghani, 2019-2026)
**Domain**: Enterprise data architecture
**Problem Type**: Scaling data architecture to 100s of domains without central bottleneck

---

## Project Context

**Organization**: Large enterprise (10,000+ employees, 50+ business units)
**Current State**: Centralized data lake with monolithic data team
**Challenge**: Data team is bottleneck, can't scale to 100s of data sources, poor data quality
**Goal**: Decentralized data architecture with domain ownership, self-service, federated governance

---

## Business Problem

### Pain Points

**PP-001: Central Data Team Bottleneck**
- **Description**: 100s of business domains depend on 1 central data team (20 people)
- **Impact**: 3-6 month backlog for new data pipelines, missed business opportunities
- **Frequency**: Constant (every new data request waits in queue)

**PP-002: Poor Data Quality**
- **Description**: Central team doesn't understand domain context, creates low-quality data
- **Impact**: Analysts spend 60% of time cleaning data, not analyzing
- **Root cause**: Domain knowledge lives in business units, not central data team

**PP-003: No Data Discovery**
- **Description**: 1000s of datasets in data lake, no catalog, no metadata
- **Impact**: Analysts can't find relevant data, re-create existing datasets
- **Waste**: Duplicate work, storage costs for redundant data

**PP-004: No Accountability**
- **Description**: Central team owns all data, no domain team feels responsible
- **Impact**: Data quality issues ignored, no SLOs, no monitoring
- **Business impact**: Decisions made on bad data, revenue loss

**PP-005: Cost Opacity**
- **Description**: No visibility into data costs per domain or consumer
- **Impact**: No chargeback, no cost optimization, runaway cloud bills
- **Example**: $500K/month cloud bill, can't attribute to domains

---

## Current State (Before Data Mesh)

### Architecture
```
Business Domains (Sales, Marketing, Supply Chain, Finance)
    ↓
Extract data → Send to Central Data Team
    ↓
Central Data Lake (S3 buckets, no structure)
    ↓
Central Data Team (builds pipelines, creates datasets)
    ↓
Data Warehouse (dimensional model)
    ↓
BI Tools (Tableau, Power BI) → Analysts
```

### Problems
- **Bottleneck**: Central Data Team (20 people) can't serve 50+ domains
- **Quality**: Central team doesn't understand Sales metrics, creates wrong data
- **Discovery**: No catalog, analysts search Slack for "who has customer data?"
- **Ownership**: No one accountable for data quality
- **Cost**: No attribution, can't track spend per domain

---

## Proposed Solution (Data Mesh)

### Target State
```
Business Domains (Sales, Marketing, Supply Chain, Finance)
    ↓
Each domain OWNS their data products (domain-driven)
    ↓
Self-Service Data Platform (provision infrastructure, no tickets)
    ↓
Data Product Registry (catalog with search, metadata, SLOs)
    ↓
Federated Governance (global policies: PII, encryption, retention)
    ↓
Consumers (analysts, ML teams, applications) subscribe to data products
```

### Key Changes
1. **Domain Ownership**: Sales domain owns "customer_360" data product (not central team)
2. **Data as Product**: Each product has schema, SLOs, owner, output ports
3. **Self-Service Platform**: Domain teams provision pipelines via IaC (no tickets)
4. **Federated Governance**: Organization sets policies, platform enforces automatically
5. **Marketplace Economics**: Consumers discover products, subscribe, pay via chargeback

---

## Business Goals

**BG-001: Scale Without Central Bottleneck**
- **Target**: 100 domains can publish data products without central team involvement
- **Measurement**: % of products published without platform team tickets (target: >90%)
- **Timeline**: 18 months to reach 100 domains

**BG-002: Improve Data Quality**
- **Target**: >90% of data products meet SLOs (freshness, completeness, accuracy)
- **Measurement**: SLO compliance % (tracked per product)
- **Why**: Domain teams know their data best, have incentive to maintain quality

**BG-003: Accelerate Time-to-Insight**
- **Target**: <1 day from "need data" to "first query" (vs. 3-6 months today)
- **Measurement**: Time from access request to first successful query
- **Impact**: Faster business decisions, competitive advantage

**BG-004: Enable Data Democratization**
- **Target**: 1000+ data products discoverable, 500+ cross-domain subscriptions
- **Measurement**: # of cross-domain data product consumption events
- **Why**: Break down data silos, enable organization-wide insights

**BG-005: Reduce Costs**
- **Target**: 30% reduction in data infrastructure costs via chargeback awareness
- **Measurement**: Total cloud bill (before: $500K/month, after: $350K/month)
- **How**: Chargeback to consumers → domains optimize usage

---

## Non-Functional Requirements

### Performance

**NFR-P-001: Data Discovery**
- **Requirement**: <2 seconds to search data catalog and get results
- **Rationale**: Fast discovery enables self-service

**NFR-P-002: Data Consumption**
- **Requirement**: <5 minutes from approval to first query
- **Rationale**: Reduce friction in data access

**NFR-P-003: Data Freshness**
- **Requirement**: Domain-defined SLOs (real-time to daily batches)
- **Example**: Customer_360 updated every 15 minutes, Sales_Reports updated daily

### Quality

**NFR-Q-001: SLO Coverage**
- **Requirement**: 100% of data products have defined SLOs
- **Enforcement**: Cannot publish product without SLO definition

**NFR-Q-002: Metadata Completeness**
- **Requirement**: 100% of products have schema, owner, lineage, usage docs
- **Enforcement**: Validation checks in publishing workflow

**NFR-Q-003: Schema Compatibility**
- **Requirement**: 99% of schema changes are backward-compatible
- **Enforcement**: Schema registry validates compatibility before approval

### Scalability

**NFR-S-001: Domain Count**
- **Requirement**: Support 100+ domains, 1000+ data products
- **Architecture**: Decentralized publishing, federated catalog

**NFR-S-002: No Single Point of Failure**
- **Requirement**: Domain A down doesn't affect Domain B
- **Architecture**: Multi-tenancy, isolated resources per domain

### Governance

**NFR-G-001: Compliance**
- **Requirement**: 100% of products comply with PII, encryption, retention policies
- **Enforcement**: Automated scanning, policies-as-code

**NFR-G-002: Interoperability**
- **Requirement**: All products use standard formats (Parquet, Avro, JSON)
- **Enforcement**: Schema registry, governance validation

### Cost

**NFR-C-001: Cost Attribution**
- **Requirement**: 100% of data costs allocated to domain or consumer
- **Enforcement**: Usage metering, chargeback system

**NFR-C-002: Cost Visibility**
- **Requirement**: Domain teams see their costs in real-time dashboard
- **Enforcement**: Cost tracking per domain, per product

---

## Key Integrations

**INT-001: Identity Provider (Okta, Azure AD)**
- **Purpose**: Authentication, authorization (who can access which products)
- **Protocol**: OAuth 2.0, SAML

**INT-002: Cloud Cost Management (AWS Cost Explorer, Azure Cost Management)**
- **Purpose**: Track spend per domain, per product
- **Protocol**: Cloud provider APIs

**INT-003: Schema Registry (Confluent Schema Registry, AWS Glue)**
- **Purpose**: Centralized schema catalog, compatibility checking
- **Protocol**: REST API

**INT-004: Data Quality Tools (Great Expectations, Soda, dbt)**
- **Purpose**: SLO monitoring, data validation
- **Protocol**: Python libraries, SQL

**INT-005: Catalog (DataHub, OpenMetadata, Amundsen)**
- **Purpose**: Data product discovery, metadata management
- **Protocol**: REST API, GraphQL

---

## Stakeholders

### Domain Teams (50+ teams)
- **Role**: Data product owners, publishers
- **Needs**: Easy-to-use platform, templates, CI/CD
- **Pain**: Currently dependent on central team, long wait times
- **Success**: Can publish products autonomously, no tickets

### Data Consumers (1000+ analysts, ML engineers)
- **Role**: Data product consumers
- **Needs**: Fast discovery, clear SLOs, easy access
- **Pain**: Can't find data, don't know quality, slow access
- **Success**: Find data in <2 minutes, access in <5 minutes

### Platform Team (10 people)
- **Role**: Build self-service platform, operate shared services
- **Needs**: Scalable infrastructure, cost optimization
- **Pain**: Currently builds all pipelines (bottleneck)
- **Success**: Enable domains to self-serve, focus on platform

### Governance Team (5 people)
- **Role**: Define global policies, audit compliance
- **Needs**: Automated enforcement, visibility into all products
- **Pain**: Manual compliance checks, can't scale
- **Success**: Policies enforced automatically, audit dashboard

### Executive Leadership
- **Role**: Fund initiative, measure ROI
- **Needs**: Cost savings, faster insights, compliance
- **Success**: 30% cost reduction, <1 day time-to-insight, no compliance violations

---

## Success Criteria

**SC-001: Adoption**
- **Metric**: 50 domains publishing data products (from 0 today)
- **Timeline**: 18 months
- **Measurement**: # of domains with ≥1 published product

**SC-002: Self-Service**
- **Metric**: 90% of products published without platform team tickets
- **Timeline**: 12 months
- **Measurement**: % of products published via self-service

**SC-003: Quality**
- **Metric**: 90% of products meeting SLOs
- **Timeline**: 12 months
- **Measurement**: Aggregate SLO compliance %

**SC-004: Time-to-Insight**
- **Metric**: <1 day from need to first query (from 3-6 months today)
- **Timeline**: 9 months
- **Measurement**: Median time from access request to first query

**SC-005: Cost Reduction**
- **Metric**: 30% reduction in data infrastructure costs
- **Timeline**: 18 months
- **Measurement**: Total cloud bill (before/after)

**SC-006: Cross-Domain Collaboration**
- **Metric**: 500+ cross-domain subscriptions
- **Timeline**: 18 months
- **Measurement**: # of subscriptions where consumer domain ≠ producer domain

---

## Constraints

**CONS-001: Existing Data Lake**
- **Constraint**: Must coexist with existing data lake during transition
- **Impact**: Need migration strategy, not big-bang

**CONS-002: Team Skills**
- **Constraint**: Domain teams have varying data engineering skills
- **Impact**: Need easy-to-use tools, templates, training

**CONS-003: Compliance**
- **Constraint**: GDPR, CCPA, SOC2 compliance required
- **Impact**: Automated PII detection, encryption, retention enforcement

**CONS-004: Budget**
- **Constraint**: Platform team budget: $200K/year personnel + $100K/year tools
- **Impact**: Must use cost-effective tools, OSS where possible

---

## Risk Mitigation

**RISK-001: Domain Teams Can't Self-Serve**
- **Likelihood**: High (teams lack data engineering skills)
- **Mitigation**: Provide CLI tools, templates, training, support
- **Fallback**: Platform team provides "data product as a service" initially

**RISK-002: Governance Chaos**
- **Likelihood**: Medium (domains publish inconsistent data)
- **Mitigation**: Automated policy enforcement, schema registry, validation gates
- **Fallback**: Manual governance review before publishing (slows adoption)

**RISK-003: Cost Overruns**
- **Likelihood**: Medium (domains over-provision resources)
- **Mitigation**: Cost tracking per domain, budget alerts, chargeback
- **Fallback**: Platform team sets resource quotas

**RISK-004: Low Adoption**
- **Likelihood**: Medium (domains resistant to change)
- **Mitigation**: Executive sponsorship, incentives (KPIs tied to adoption), quick wins
- **Fallback**: Start with 5 pilot domains, prove value, expand

---

## Next Steps

1. **Apply solution-mapper**: Identify compositional archetype (Digital Twin + Marketplace + Integration)
2. **Design architecture**: Define ontology, components, team structure
3. **Apply tech-stack-mapper**: Select technologies based on constraints
4. **Build MVP**: 3 pilot domains, 10 data products, 6 months
5. **Measure success**: Track adoption, quality, time-to-insight, cost

---

**End of Requirements**
