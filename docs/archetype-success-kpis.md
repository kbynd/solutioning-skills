# Archetype Success KPIs

**Purpose**: Define measurable success indicators for each compositional archetype across the full lifecycle, with decision impact analysis showing how design choices affect downstream outcomes.

**Why This Matters**:
- **Archetypes are reality patterns**, not just diagrams - they must deliver measurable outcomes
- **Design decisions propagate** - choices made in design phase affect operations phase KPIs
- **Leading indicators predict failure** before lagging indicators show it (preventative vs reactive)
- **Compositional decisions have compound effects** - how archetypes integrate affects overall success

---

## Framework Structure

### Lifecycle Phases

Every archetype implementation moves through 5 phases:

1. **Design** - Architecting the solution (choosing archetypes, defining boundaries, modeling relationships)
2. **Build** - Implementing components (developing software, creating infrastructure)
3. **Implementation** - Deploying to production (rollout, user onboarding, adoption)
4. **Operations** - Day-to-day running (stability, performance, support)
5. **Maintenance** - Evolution over time (improvements, migrations, technical debt management)

### KPI Categories

For each phase, we track:

**Leading Indicators** (Predictive)
- Signal problems BEFORE they manifest
- Actionable - you can intervene to prevent failure
- Example: "Onboarding time increasing" predicts adoption failure before users abandon system

**Lagging Indicators** (Retrospective)
- Measure outcomes AFTER they occur
- Confirm success/failure but hard to change after the fact
- Example: "Total users" tells you adoption happened (or didn't) but too late to fix

**Decision Impact Analysis**
- Shows how choices made NOW affect KPIs LATER
- Makes trade-offs explicit (fast onboarding vs quality enforcement)
- Enables informed decisions (not blind guesses)

---

## Marketplace/Portal Archetype (Level 2)

**Reality Pattern**: Medieval market - vendors (domain teams) sell goods (data products), buyers (consumers) browse and purchase, inspectors (governance) enforce quality standards

**Pre-Digital Example**: Medieval wool market in Flanders (1200s-1400s)
- Vendors: Wool merchants from England
- Buyers: Cloth manufacturers from Italy
- Inspectors: Guild officials enforcing weight/quality standards
- KPIs tracked: Market days per year, number of vendors, transaction volume, quality violations

---

### Design Phase

**Objective**: Define marketplace structure - product taxonomy, domain boundaries, quality standards, discovery mechanisms

#### Leading Indicators

| KPI | Target | Measurement | Why Leading | Failure Signal |
|-----|--------|-------------|-------------|----------------|
| **Product discovery completeness** | 100% of existing datasets identified | # datasets cataloged / # datasets in org | Incomplete discovery → gaps in marketplace → consumers can't find data | <80% discovered → users won't trust catalog |
| **Domain boundary clarity** | >80% stakeholder agreement on ownership | Survey: "Is it clear which domain owns customer data?" | Unclear boundaries → turf wars → political resistance → adoption failure | <70% agreement → expect political blockers |
| **Data contract template coverage** | 100% product types have templates | # templates created / # product types (dataset, API, stream, dashboard, ML) | Missing templates → slow onboarding → domains abandon self-service | Missing API template → API products can't onboard |
| **Ontology completeness** | All core entities modeled (Product, Domain, Contract, SLO) | # entities in ontology / # entities needed | Incomplete ontology → can't express requirements → workarounds emerge | Missing "Subscription" entity → can't track consumers |
| **Governance policy coverage** | 100% of compliance requirements have policies | # policies defined / # compliance mandates (PII, retention, etc.) | Missing policies → violations slip through → compliance incidents | No PII policy → data breach risk |

#### Lagging Indicators

| KPI | Target | Measurement | Why Lagging |
|-----|--------|-------------|-------------|
| **Design review approval** | 100% of design docs approved by stakeholders | Stakeholder sign-off | Tells you design is approved, but doesn't predict implementation success |
| **Architecture documentation completeness** | 100% of components documented | # components with docs / # total components | Measures documentation effort, but doesn't guarantee clarity |

#### Decision Impact Analysis

**Decision 1: Product Granularity**

```yaml
decision: "How granular should data products be?"

option_1_coarse_products:
  description: "1 large product per domain (e.g., 'Sales_Data' with all sales data)"
  design_phase_impact:
    modeling_complexity: "LOW (fewer products to model)"
    onboarding_time: "LOW (faster to publish one big product)"

  build_phase_impact:
    catalog_complexity: "LOW (50 products total for 50 domains)"
    schema_size: "HIGH (massive schemas, 100+ fields per product)"

  operations_phase_impact:
    discovery_time: "HIGH (hard to find specific data in large product)"
    reusability: "LOW (consumers forced to take entire product, can't subset)"
    query_performance: "LOW (large products = slow queries)"
    breaking_change_risk: "HIGH (one schema change affects all consumers)"

  maintenance_phase_impact:
    evolution_velocity: "LOW (hard to evolve monolithic products)"
    consumer_migration_cost: "HIGH (breaking changes affect many consumers)"

option_2_fine_products:
  description: "5-10 focused products per domain (e.g., 'Sales.Orders', 'Sales.Returns', 'Sales.Customer_Purchases')"
  design_phase_impact:
    modeling_complexity: "HIGH (more products to model and track)"
    onboarding_time: "MEDIUM (each product needs contract, SLO)"

  build_phase_impact:
    catalog_complexity: "HIGH (250-500 products total)"
    schema_size: "LOW (focused schemas, 10-20 fields per product)"

  operations_phase_impact:
    discovery_time: "LOW (specific products easy to find and understand)"
    reusability: "HIGH (consumers get exactly what they need)"
    query_performance: "HIGH (small products = fast queries)"
    breaking_change_risk: "LOW (changes isolated to specific product)"

  maintenance_phase_impact:
    evolution_velocity: "HIGH (small products easy to evolve independently)"
    consumer_migration_cost: "LOW (breaking changes affect fewer consumers)"

recommendation: "Fine granularity (5-10 products per domain)"
rationale: |
  Operations and Maintenance benefits far outweigh Design and Build complexity.
  - Consumers can find and use specific data (not forced to take everything)
  - Products evolve independently (breaking changes isolated)
  - Performance is better (smaller schemas, faster queries)
  - YES, catalog has more products (250 vs 50), but that's manageable with good search/tagging

trade_offs_accepted:
  - More upfront modeling work (Design phase)
  - Larger catalog to manage (Build/Ops phase)
  - Worth it for reusability and evolution velocity
```

**Decision 2: Quality Enforcement Strictness**

```yaml
decision: "How strict should quality enforcement be at publish time?"

option_1_strict_enforcement:
  description: "Products MUST pass all governance policies before publishing (blocking)"
  design_phase_impact:
    policy_definition_effort: "HIGH (must define complete policy set upfront)"

  build_phase_impact:
    onboarding_time: "HIGH (domains blocked until all issues fixed)"
    onboarding_friction: "HIGH (domains frustrated by rejections)"
    false_positive_rate: "MEDIUM (overly strict policies block valid products)"

  operations_phase_impact:
    catalog_quality: "HIGH (90%+ SLO compliance from day 1)"
    consumer_trust: "HIGH (consumers trust catalog, no garbage data)"
    support_load: "LOW (fewer quality-related support tickets)"

  maintenance_phase_impact:
    quality_trend: "STABLE or IMPROVING (enforcement prevents degradation)"

option_2_lenient_enforcement:
  description: "Products can publish with warnings (non-blocking governance)"
  design_phase_impact:
    policy_definition_effort: "LOW (can refine policies over time)"

  build_phase_impact:
    onboarding_time: "LOW (fast - publish immediately with warnings)"
    onboarding_friction: "LOW (domains happy, no rejections)"
    false_positive_rate: "LOW (warnings don't block anyone)"

  operations_phase_impact:
    catalog_quality: "LOW (<70% SLO compliance, degrades over time)"
    consumer_trust: "LOW (consumers find broken products, stop using catalog)"
    support_load: "HIGH (many quality-related complaints)"

  maintenance_phase_impact:
    quality_trend: "DEGRADING (no enforcement → technical debt accumulates)"

recommendation: "Tiered strictness (strict for critical, lenient for experimental)"
rationale: |
  Different product tiers need different enforcement:

  CRITICAL products (financial, compliance, customer-facing):
    - Strict enforcement (must pass all policies)
    - High quality from day 1
    - Example: Payments.transactions, Customer.pii_data

  STANDARD products (internal analytics, reporting):
    - Moderate enforcement (must pass critical policies, warnings on others)
    - Balance quality with onboarding speed
    - Example: Sales.weekly_reports, Marketing.campaign_metrics

  EXPERIMENTAL products (ML experiments, exploratory):
    - Lenient enforcement (warnings only, no blocking)
    - Fast iteration, lower quality acceptable
    - Example: DataScience.customer_segmentation_v3_experiment

implementation:
  - Product metadata includes: product_tier = "critical" | "standard" | "experimental"
  - Governance engine enforces based on tier
  - Domains start with "experimental", graduate to "standard", promote to "critical"
```

**Decision 3: Self-Service vs Platform-Managed**

```yaml
decision: "Who provisions infrastructure for data products?"

option_1_full_self_service:
  description: "Domains provision their own infrastructure (Terraform, Kubernetes, etc.)"
  design_phase_impact:
    iam_model_complexity: "HIGH (domains need AWS permissions, RBAC)"

  build_phase_impact:
    domain_skill_requirements: "VERY HIGH (Terraform, K8s, AWS, Docker, Python)"
    onboarding_time: "VERY HIGH (domains must learn platform tools)"
    adoption_rate: "LOW (only 5% of domains have these skills)"

  operations_phase_impact:
    platform_support_load: "LOW (domains self-support)"
    infrastructure_consistency: "LOW (each domain does it differently)"
    security_risk: "HIGH (domains may misconfigure, create vulnerabilities)"

  maintenance_phase_impact:
    upgrade_coordination: "NIGHTMARE (50 domains using different versions)"

option_2_platform_managed:
  description: "Platform team provisions all infrastructure, domains provide business logic only"
  design_phase_impact:
    iam_model_complexity: "LOW (platform has permissions, not domains)"

  build_phase_impact:
    domain_skill_requirements: "LOW (SQL, YAML only)"
    onboarding_time: "LOW (domains write SQL, platform deploys)"
    adoption_rate: "HIGH (80% of domains can write SQL)"

  operations_phase_impact:
    platform_support_load: "HIGH (platform is bottleneck for all deployments)"
    infrastructure_consistency: "HIGH (platform enforces standards)"
    security_risk: "LOW (platform controls all infrastructure)"

  maintenance_phase_impact:
    upgrade_coordination: "EASY (platform upgrades all domains at once)"

recommendation: "Platform-managed with self-service escape hatch"
rationale: |
  Start with platform-managed (80% adoption), allow self-service for advanced teams (5%).

  LEVEL 1 (Platform-Managed) - 80% of domains:
    - Domain provides: SQL transformations, data quality rules (YAML)
    - Platform provides: Infrastructure (Terraform), CI/CD, deployment, monitoring
    - Skill requirements: SQL, YAML, Git (basic)
    - Onboarding: <1 week

  LEVEL 2 (Template-Based Self-Service) - 15% of domains:
    - Domain provides: SQL + dbt models + config files
    - Platform provides: Terraform modules (pre-built), CI/CD templates
    - Skill requirements: SQL, dbt, Git, YAML (intermediate)
    - Onboarding: 2-4 weeks

  LEVEL 3 (Full Self-Service) - 5% of domains:
    - Domain provides: Everything (Terraform, Python, Docker, K8s)
    - Platform provides: Shared services only (catalog, governance, lineage)
    - Skill requirements: Terraform, Python, K8s, AWS (advanced)
    - Onboarding: 1-3 months

  Evolution path: Start everyone at Level 1, domains graduate to Level 2/3 as skills mature

kpi_impact:
  build_adoption_rate:
    platform_managed: "80% domains (40/50 domains)"
    full_self_service: "5% domains (2-3/50 domains)"
    recommendation_impact: "80% > 5% → Platform-managed wins for breadth"

  operations_quality:
    platform_managed: "HIGH (standardized, secure, consistent)"
    full_self_service: "VARIABLE (depends on domain skills)"
```

---

### Build Phase

**Objective**: Implement marketplace infrastructure - catalog, portal, governance engine, output ports

#### Leading Indicators

| KPI | Target | Measurement | Why Leading | Failure Signal |
|-----|--------|-------------|-------------|----------------|
| **Product onboarding time** | <1 day (8 hours) from commit to published | Time: `git push` → product visible in catalog | Slow onboarding → frustration → domains abandon self-service | >3 days → expect low adoption |
| **Schema validation pass rate** | >90% products pass validation on first try | # passing / # submitted | Low pass rate → confusing contracts → onboarding friction | <70% → contracts too complex |
| **Policy enforcement coverage** | 100% products scanned by governance | # scanned / # in catalog | Gaps in enforcement → quality erosion → consumer trust loss | <100% → governance has holes |
| **Catalog API uptime** | >99.9% (3 nines) | Uptime monitoring (PagerDuty) | Downtime → domains can't publish → adoption blocked | <99% → infrastructure instability |
| **Portal usability score** | >80% users find product in <5 min | User study: time to find relevant product | Poor UX → consumers bypass marketplace → adoption failure | <60% → redesign UI |

#### Lagging Indicators

| KPI | Target | Measurement | Why Lagging |
|-----|--------|-------------|-------------|
| **Products published** | 50 products in first 6 months | `COUNT(products WHERE status = 'published')` | Measures adoption but doesn't predict future growth |
| **Domains with products** | 10 domains (20% of org) by month 6 | `COUNT(DISTINCT domain_id FROM products)` | Measures breadth but slow-moving (monthly measurement) |
| **Governance policies active** | 15 policies enforcing by month 3 | `COUNT(policies WHERE status = 'active')` | Measures policy coverage but not effectiveness |

#### Decision Impact Analysis

**Decision 4: Automated vs Manual Quality Review**

```yaml
decision: "Should product quality checks be automated or manual?"

option_1_automated:
  description: "OPA policies + schema validators automatically approve/reject products"
  build_phase_impact:
    policy_development_effort: "HIGH (must codify all rules in Rego)"
    onboarding_time: "LOW (instant feedback, no waiting for human)"
    false_positive_rate: "MEDIUM (automated checks can be overly strict)"

  operations_phase_impact:
    manual_toil: "ZERO (no human review needed for standard products)"
    quality_consistency: "HIGH (same rules applied every time, no human bias)"
    onboarding_throughput: "HIGH (can onboard 100 products/day)"

  maintenance_phase_impact:
    policy_evolution: "MEDIUM (must update Rego code, test, deploy)"

option_2_manual:
  description: "Human reviewers approve each product before publishing"
  build_phase_impact:
    policy_development_effort: "LOW (reviewers use judgment, not code)"
    onboarding_time: "HIGH (waiting for human approval, 1-3 days)"
    false_positive_rate: "LOW (humans can handle edge cases)"

  operations_phase_impact:
    manual_toil: "VERY HIGH (reviewers become bottleneck)"
    quality_consistency: "LOW (different reviewers apply rules differently)"
    onboarding_throughput: "LOW (bottleneck: 5-10 products/day max)"

  maintenance_phase_impact:
    policy_evolution: "LOW (just update review checklist)"

recommendation: "Automated with manual escape hatch"
rationale: |
  AUTOMATED (default path - 90% of products):
    - Standard products (dataset, API, stream) → OPA policies auto-approve
    - Fast onboarding (<1 day)
    - Consistent enforcement

  MANUAL OVERRIDE (edge cases - 10% of products):
    - Complex products (new product type, unusual SLO requirements)
    - Failed automated check but valid reason (policy_override_requested = true)
    - Human reviews edge cases only

  Best of both worlds:
    - High throughput (automated handles volume)
    - Quality judgment (humans handle exceptions)

kpi_impact:
  build_onboarding_time:
    automated: "<1 day (instant approval)"
    manual: "1-3 days (waiting for reviewer)"
  operations_throughput:
    automated: "100 products/day (no bottleneck)"
    manual: "5-10 products/day (reviewer bottleneck)"
```

---

### Implementation Phase

**Objective**: Roll out marketplace to organization, onboard domains, drive consumer adoption

#### Leading Indicators

| KPI | Target | Measurement | Why Leading | Failure Signal |
|-----|--------|-------------|-------------|----------------|
| **Consumer query success rate** | >95% queries return results without errors | # successful / # total queries | Low success → frustration → abandonment | <85% → data quality or infrastructure issues |
| **Product discovery time** | <5 minutes to find relevant product | User study: "I need customer data" → found `Sales.customer_360` | Slow discovery → consumers bypass marketplace | >10 min → improve search/tagging |
| **Early SLO compliance rate** | >90% products meet SLO in first month | # products SLO≥90% / # total products | Early failures predict long-term quality issues | <80% → onboarding quality gates too weak |
| **Domain onboarding velocity** | 2-3 domains/month onboarding | # new domains publishing per month | Slowing velocity → adoption plateauing | <1 domain/month → investigate barriers |
| **Consumer satisfaction (NPS)** | >50 (promoters exceed detractors) | Survey: "Would you recommend this marketplace?" | Declining NPS predicts abandonment | <30 → users unhappy, fix UX/quality |

#### Lagging Indicators

| KPI | Target | Measurement | Why Lagging |
|-----|--------|-------------|-------------|
| **Active consumers** | 100 consumers by month 6 | `COUNT(DISTINCT consumer_id FROM query_logs WHERE last_30_days)` | Measures adoption success but doesn't predict future growth |
| **Query volume** | 10,000 queries/day by month 6 | `SUM(queries) per day` | Measures usage but lags behind consumer onboarding |
| **Data volume served** | 1 TB/day served to consumers | `SUM(bytes_transferred) per day` | Measures scale but doesn't predict quality |

#### Decision Impact Analysis

**Decision 5: Rollout Strategy (Big Bang vs Gradual)**

```yaml
decision: "How should we roll out the marketplace to the organization?"

option_1_big_bang:
  description: "Launch to all 50 domains simultaneously"
  implementation_phase_impact:
    time_to_full_rollout: "1 month (everyone at once)"
    support_team_load: "OVERWHELMING (50 teams learning simultaneously)"
    incident_blast_radius: "CRITICAL (bugs affect entire org)"
    rollback_feasibility: "IMPOSSIBLE (can't rollback 50 domains)"

  operations_phase_impact:
    incident_frequency: "HIGH (bugs discovered in production, affect everyone)"
    user_satisfaction: "LOW (poor experience due to bugs/overload)"

  maintenance_phase_impact:
    technical_debt: "HIGH (rushed launch → cut corners → debt accumulates)"

option_2_gradual_pilot:
  description: "5 pilot domains → learn → expand to remaining 45"
  implementation_phase_impact:
    time_to_full_rollout: "6 months (pilot 2 months, expand 4 months)"
    support_team_load: "MANAGEABLE (5 teams at a time)"
    incident_blast_radius: "CONTAINED (bugs affect only pilots)"
    rollback_feasibility: "EASY (can rollback 5 domains)"

  operations_phase_impact:
    incident_frequency: "LOW (bugs caught in pilot, fixed before expansion)"
    user_satisfaction: "HIGH (smooth experience, lessons applied)"

  maintenance_phase_impact:
    technical_debt: "LOW (time to do it right, learnings incorporated)"

recommendation: "Gradual pilot rollout"
rationale: |
  PHASE 1: Pilot (Months 1-2)
    - 5 friendly domains (willing to tolerate bugs, provide feedback)
    - Learn: What's confusing? What breaks? What's missing?
    - Fix: Bugs, UX issues, missing features
    - Validate: SLO compliance, consumer satisfaction, onboarding time

  PHASE 2: Expand (Months 3-6)
    - 3-5 domains per month (manageable support load)
    - Apply learnings from pilot
    - Iterate based on feedback

  PHASE 3: Full Rollout (Month 7+)
    - Remaining domains onboard voluntarily (forced migration avoided)

  Benefits over big bang:
    - Bugs contained (only pilots affected)
    - Support manageable (not overwhelmed)
    - Learnings applied (better experience for later domains)
    - Rollback possible (if pilot fails, only 5 domains affected)

kpi_impact:
  implementation_incident_rate:
    big_bang: "HIGH (10+ critical incidents in month 1)"
    gradual: "LOW (2-3 incidents contained to pilots)"
  operations_user_satisfaction:
    big_bang: "60 NPS (many users had bad initial experience)"
    gradual: "75 NPS (smooth rollout, bugs caught early)"
```

---

### Operations Phase

**Objective**: Maintain stable, high-quality marketplace - SLO compliance, consumer trust, operational excellence

#### Leading Indicators

| KPI | Target | Measurement | Why Leading | Failure Signal |
|-----|--------|-------------|-------------|----------------|
| **SLO compliance trend** | >90% products maintain SLO (stable or improving) | Weekly: # products SLO≥90% / # total | Downward trend predicts quality crisis | Dropping from 92% → 88% → 84% → quality degrading |
| **Consumer satisfaction trend** | >80% satisfied (stable or improving) | Quarterly NPS survey | Declining satisfaction predicts abandonment | NPS dropping from 70 → 60 → 50 → users leaving |
| **Support ticket trend** | Decreasing or stable (not increasing) | Weekly: `COUNT(tickets WHERE created_this_week)` | Rising tickets predict systemic issues | 50 → 70 → 90 tickets/week → something breaking |
| **Schema breaking change rate** | <5% of schema changes are breaking | # breaking / # total schema changes | High rate predicts consumer pipeline breakage | >10% breaking → consumers angry |
| **Deprecated product migration rate** | >90% consumers migrate within 30 days | # migrated / # total consumers (per deprecated product) | Slow migration → breaking changes → incidents | <70% migrated → expect downstream failures |

#### Lagging Indicators

| KPI | Target | Measurement | Why Lagging |
|-----|--------|-------------|-------------|
| **Total products in catalog** | 300 products by end of year 1 | `COUNT(products WHERE status = 'published')` | Measures growth but doesn't predict sustainability |
| **Total active consumers** | 500 consumers by end of year 1 | `COUNT(DISTINCT consumer_id FROM query_logs WHERE last_30_days)` | Measures breadth but doesn't predict satisfaction |
| **Average query latency** | <500ms p95 | `p95(query_duration_ms)` | Measures performance but users already experienced slowness |
| **Catalog uptime** | >99.9% (3 nines) | Monthly uptime % | Measures reliability but incidents already happened |

#### Decision Impact Analysis

**Decision 6: SLO Enforcement (Strict vs Lenient)**

```yaml
decision: "What happens when a product fails to meet its SLO?"

option_1_strict_enforcement:
  description: "Products automatically marked 'degraded' and hidden from catalog search when SLO <90%"
  operations_phase_impact:
    catalog_quality: "HIGH (only high-quality products visible)"
    consumer_trust: "HIGH (consumers trust search results)"
    domain_pressure: "HIGH (domains must fix issues to stay visible)"
    false_positives: "MEDIUM (transient failures hide products temporarily)"

  maintenance_phase_impact:
    quality_trend: "IMPROVING (strong incentive to maintain quality)"
    product_churn: "MEDIUM (some products deprecated due to enforcement)"

option_2_lenient_enforcement:
  description: "Products stay visible with warning badge ('SLO not met - use with caution')"
  operations_phase_impact:
    catalog_quality: "LOW (many degraded products visible)"
    consumer_trust: "LOW (consumers find broken products, lose trust)"
    domain_pressure: "LOW (no incentive to fix - product still visible)"
    false_positives: "ZERO (no products hidden)"

  maintenance_phase_impact:
    quality_trend: "DEGRADING (no enforcement → technical debt accumulates)"
    product_churn: "LOW (products never deprecated, even if broken)"

recommendation: "Tiered enforcement based on product criticality"
rationale: |
  CRITICAL products (financial, compliance, customer-facing):
    - Strict enforcement
    - SLO <90% → product hidden from search
    - SLO <70% → product auto-deprecated (30-day notice to consumers)
    - Justification: Can't have broken critical products

  STANDARD products (internal analytics):
    - Moderate enforcement
    - SLO <90% → warning badge ("degraded quality")
    - SLO <70% → hidden from search
    - Justification: Balance quality with flexibility

  EXPERIMENTAL products:
    - Lenient enforcement
    - SLO <90% → warning badge only
    - Never auto-hidden or auto-deprecated
    - Justification: Experiments expected to be unstable

kpi_impact:
  operations_catalog_quality:
    strict: "95% of visible products meet SLO"
    lenient: "75% of visible products meet SLO"
  operations_consumer_trust:
    strict: "85 NPS (users trust catalog)"
    lenient: "60 NPS (users frustrated by broken products)"
```

---

### Maintenance Phase

**Objective**: Evolve marketplace over time - migrations, schema evolution, technical debt management, continuous improvement

#### Leading Indicators

| KPI | Target | Measurement | Why Leading | Failure Signal |
|-----|--------|-------------|-------------|----------------|
| **Schema evolution success rate** | >95% changes are backward compatible | # backward-compatible / # total schema changes | Breaking changes → consumer pipelines break → trust erosion | <90% → domains not following best practices |
| **Technical debt backlog trend** | Decreasing or stable (not growing) | Story points in 'tech debt' backlog over time | Growing debt predicts future maintenance crisis | Debt growing 20%/quarter → unsustainable |
| **Security vulnerability fix time** | <7 days for critical, <30 days for high | Time from discovery to patch deployed | Slow fixes → security incidents → compliance violations | Critical vulnerabilities open for >14 days → risk |
| **Platform upgrade adoption rate** | >90% domains on latest version within 90 days | # domains on current version / # total domains | Fragmentation predicts upgrade nightmare | <70% on current → version sprawl |
| **Consumer migration success rate** | >95% consumers migrate successfully when product deprecated | # successful migrations / # total migrations | Failed migrations → broken consumer pipelines | <85% → migration tooling inadequate |

#### Lagging Indicators

| KPI | Target | Measurement | Why Lagging |
|-----|--------|-------------|-------------|
| **Product churn rate** | <10% products deprecated per year | (# deprecated / # total) per year | Measures product lifecycle health but doesn't predict churn |
| **Average product age** | 12-18 months (not too new, not stale) | `MEDIAN(DATEDIFF(NOW(), product_created_date))` | Tells you if products actively maintained but doesn't predict future |
| **Breaking changes per quarter** | <5% of schema changes | # breaking / # total (per quarter) | Measures stability but consumers already impacted |
| **Cost per product** | <$100/month per product | Total platform cost / # products | Measures efficiency but doesn't predict cost overruns |

#### Decision Impact Analysis

**Decision 7: Schema Evolution Strategy**

```yaml
decision: "How should data product schemas evolve over time?"

option_1_breaking_changes_allowed:
  description: "Domains can make breaking schema changes anytime (with deprecation notice)"
  maintenance_phase_impact:
    domain_flexibility: "HIGH (domains evolve quickly)"
    consumer_pipeline_breakage: "HIGH (consumers must update frequently)"
    consumer_satisfaction: "LOW (constant breaking changes frustrate consumers)"
    migration_overhead: "HIGH (consumers spend time migrating, not building)"

  long_term_sustainability: "LOW (consumers abandon products with frequent breaks)"

option_2_versioned_schemas:
  description: "Domains publish new schema versions (v1, v2) alongside old, consumers migrate gradually"
  maintenance_phase_impact:
    domain_flexibility: "MEDIUM (can evolve, but must support multiple versions)"
    domain_maintenance_burden: "HIGH (maintaining v1 + v2 simultaneously)"
    consumer_pipeline_breakage: "ZERO (consumers migrate on their timeline)"
    consumer_satisfaction: "HIGH (no forced migrations)"
    migration_overhead: "LOW (consumers migrate when ready)"

  long_term_sustainability: "HIGH (consumers trust products won't break unexpectedly)"

option_3_append_only_evolution:
  description: "Schemas can only ADD fields (never remove/rename), breaking changes require new product"
  maintenance_phase_impact:
    domain_flexibility: "LOW (very constrained evolution)"
    domain_maintenance_burden: "MEDIUM (occasional new products needed)"
    consumer_pipeline_breakage: "ZERO (backward compatible by design)"
    consumer_satisfaction: "VERY HIGH (pipelines never break)"
    schema_bloat: "HIGH (old fields accumulate, never removed)"

  long_term_sustainability: "VERY HIGH (ultimate stability, consumers love it)"

recommendation: "Append-only with versioned escape hatch"
rationale: |
  DEFAULT (80% of cases): Append-only evolution
    - Add fields freely (backward compatible)
    - Mark fields deprecated (but keep them)
    - Consumers immune to breaking changes
    - Example: Add 'customer_segment' field to Sales.customer_360

  ESCAPE HATCH (20% of cases): Versioned schemas
    - When breaking change unavoidable (e.g., fix fundamental design flaw)
    - Publish new version (Sales.customer_360_v2)
    - Support both v1 and v2 for 6 months
    - Auto-migrate consumers after 6 months (with tooling support)

  Best of both worlds:
    - Stability (default append-only)
    - Flexibility (versioning when needed)

kpi_impact:
  maintenance_consumer_satisfaction:
    breaking_allowed: "60 NPS (frustrated by constant breaks)"
    versioned: "75 NPS (happy, but some migration pain)"
    append_only: "85 NPS (love the stability)"

  maintenance_pipeline_breakage_rate:
    breaking_allowed: "15% consumers experience breaking change per quarter"
    versioned: "5% consumers experience breaking change per quarter"
    append_only: "0% consumers experience breaking change"
```

---

---

## Supply Chain Network Archetype (Level 3)

**Reality Pattern**: Medieval Silk Road - goods transformed across multiple stages (silk from China → spices in Persia → textiles in Italy), each node adds value, provenance tracked, quality cascades

**Pre-Digital Example**: Silk Road trade network (200 BC - 1400s AD)
- Nodes: Merchant caravans (transformers/intermediaries)
- Flow: Silk, spices, precious metals moving through network
- Provenance: Seals, stamps certifying origin and quality
- KPIs tracked: Days to destination, goods lost/damaged rate, quality at each stage

---

### Design Phase

**Objective**: Define supply chain topology - dependencies, transformations, quality propagation rules

#### Leading Indicators

| KPI | Target | Measurement | Why Leading | Failure Signal |
|-----|--------|-------------|-------------|----------------|
| **Dependency declaration completeness** | 100% products declare upstream dependencies | # products with dependencies declared / # products | Undeclared dependencies → missed lineage → breaking changes surprise consumers | <90% → lineage gaps |
| **Lineage schema coverage** | All transformation types modeled | # transformation types in schema / # types in org | Missing transformation types → can't track certain flows | Missing "ML feature engineering" → gaps |
| **Quality propagation rule coverage** | 100% of SLO types have propagation rules | # rules defined / # SLO types | Missing rules → quality failures don't cascade → false sense of health | No freshness propagation rule → stale data spreads silently |
| **Impact analysis test scenarios** | 10+ breaking change scenarios modeled | # test scenarios documented | Insufficient testing → impact analysis wrong → surprise breakage | <5 scenarios → inadequate coverage |

#### Lagging Indicators

| KPI | Target | Measurement | Why Lagging |
|-----|--------|-------------|-------------|
| **Lineage graph completeness** | Design document approved | Stakeholder sign-off | Approval doesn't predict implementation success |

#### Decision Impact Analysis

**Decision: Lineage Discovery Method**

```yaml
decision: "How is lineage between products captured?"

option_1_auto_discovery:
  description: "Parse SQL/ETL code to auto-discover dependencies"
  design_phase_impact:
    modeling_complexity: "LOW (no manual declaration)"
  build_phase_impact:
    onboarding_friction: "LOW (domains don't declare dependencies)"
    parser_development_effort: "HIGH (must parse SQL, Spark, Python, etc.)"
  operations_phase_impact:
    lineage_accuracy: "MEDIUM (75% - misses external APIs, manual processes)"
    impact_analysis_accuracy: "MEDIUM (missed dependencies → surprise breakage)"
  maintenance_phase_impact:
    lineage_drift: "HIGH (code changes break parser → stale lineage)"

option_2_manual_declaration:
  description: "Domains declare dependencies in data contract YAML"
  design_phase_impact:
    modeling_complexity: "MEDIUM (must design dependency schema)"
  build_phase_impact:
    onboarding_friction: "MEDIUM (domains list dependencies)"
    parser_development_effort: "ZERO (just read YAML)"
  operations_phase_impact:
    lineage_accuracy: "HIGH (95% - explicitly declared)"
    impact_analysis_accuracy: "HIGH (complete dependencies → accurate predictions)"
  maintenance_phase_impact:
    lineage_drift: "LOW (contracts updated when code changes)"

recommendation: "Manual declaration with automated validation"
rationale: |
  Domains declare in contract:
    ```yaml
    upstream_dependencies:
      - product_id: "Payments.raw_transactions"
        slo_propagation: "freshness_cascade"
      - product_id: "Customer.profile"
        slo_propagation: "completeness_required"
    ```

  Platform validates by parsing actual code and comparing to declared dependencies.
  Alert if mismatch.

kpi_impact:
  operations_impact_analysis_accuracy:
    auto: "75% (20 surprise breaking changes/quarter)"
    manual: "95% (5 surprise breaking changes/quarter)"
```

---

### Build Phase

#### Leading Indicators

| KPI | Target | Measurement | Why Leading | Failure Signal |
|-----|--------|-------------|-------------|----------------|
| **Lineage ingestion latency** | <5 minutes from code deploy to lineage updated | Time: code deploy → lineage graph updated | High latency → stale lineage → wrong impact analysis | >15 min → lineage too slow |
| **Orphaned node detection rate** | 100% orphaned products detected within 24 hours | # orphaned detected / # actual orphaned | Undetected orphans → broken pipelines → consumer failures | <90% → detection gaps |
| **Quality cascade test coverage** | All SLO types have cascade tests | # tested SLO types / # total SLO types | Untested cascades → wrong propagation → false health signals | Missing freshness tests → stale data spreads |

#### Lagging Indicators

| KPI | Target | Measurement | Why Lagging |
|-----|--------|-------------|-------------|
| **Lineage edges captured** | 500+ edges by month 3 | `COUNT(lineage_edges)` | Measures coverage but doesn't predict accuracy |
| **Supply chain depth** | Average 3-5 hops from source to consumer | `AVG(path_length)` | Measures complexity but doesn't predict failures |

---

### Operations Phase

#### Leading Indicators

| KPI | Target | Measurement | Why Leading | Failure Signal |
|-----|--------|-------------|-------------|----------------|
| **Quality propagation accuracy** | >95% downstream failures predicted by upstream SLO violations | # predicted failures / # actual failures | Low accuracy → surprise consumer breakage | <85% → propagation logic wrong |
| **Impact analysis query time** | <2 seconds for "what breaks if I change this?" | Query: "downstream consumers of product X" | Slow queries → architects skip impact analysis → breaking changes deployed | >10 seconds → graph too slow |
| **Lineage freshness** | 100% of lineage updated within 5 minutes of code change | # updated / # changed | Stale lineage → wrong impact analysis → breaking changes missed | <95% → lineage update pipeline broken |
| **Provenance verification rate** | >99% of data products have complete provenance chain | # products with full lineage / # total | Missing provenance → can't trace PII/compliance → audit failures | <95% → lineage gaps |

#### Lagging Indicators

| KPI | Target | Measurement | Why Lagging |
|-----|--------|-------------|-------------|
| **Breaking change incidents** | <5 incidents/quarter due to missed dependencies | Count of incidents where "didn't know X depended on Y" | Measures impact accuracy but incidents already happened |
| **Total lineage edges** | 2,000+ edges (300 products × avg 7 edges) | `COUNT(lineage_edges)` | Measures scale but doesn't predict quality |

---

## Digital Twin Archetype (Level 1)

**Reality Pattern**: Medieval guild registry - official record of all guilds (merchants, craftsmen, lawyers), their members, territories, and privileges

**Pre-Digital Example**: London guild registries (1100s-1800s)
- Registry: Written records of guild membership, territories
- Updates: New members admitted, old members retire, territories redrawn
- KPIs tracked: Registry accuracy (% members correctly listed), update lag (days to record changes)

---

### Design Phase

**Objective**: Define organizational model - domains, teams, ownership boundaries, update mechanisms

#### Leading Indicators

| KPI | Target | Measurement | Why Leading | Failure Signal |
|-----|--------|-------------|-------------|----------------|
| **Domain boundary clarity** | >90% stakeholders agree on domain ownership | Survey: "Is it clear which domain owns customer data?" | Unclear boundaries → turf wars → political resistance | <80% → expect conflicts |
| **Ownership completeness** | 100% of datasets mapped to domains | # datasets with owners / # total datasets | Orphaned datasets → no one responsible → quality degrades | <95% → ownership gaps |
| **Domain taxonomy depth** | 2-3 levels (not too shallow, not too deep) | Max depth of domain hierarchy | Too shallow → massive domains, too deep → complexity | >5 levels → over-complicated |
| **Conway's Law alignment** | >80% domains align with team structure | # domains matching teams / # total domains | Misalignment → communication overhead → slow delivery | <70% → org structure mismatch |

#### Lagging Indicators

| KPI | Target | Measurement | Why Lagging |
|-----|--------|-------------|-------------|
| **Domain registry approved** | Design doc signed off | Stakeholder approval | Approval doesn't guarantee operational accuracy |

#### Decision Impact Analysis

**Decision: Domain Granularity**

```yaml
decision: "How many domains should we define?"

option_1_coarse_10_domains:
  description: "10 large domains (Sales, Marketing, Customer, Finance, etc.)"
  design_phase_impact:
    modeling_complexity: "LOW (few domains to model)"
    ownership_clarity: "LOW (each domain has 10+ teams → fuzzy ownership)"
  operations_phase_impact:
    governance_coordination: "HIGH (large domains → many stakeholders)"
    domain_autonomy: "LOW (too many teams in one domain → coordination needed)"
  maintenance_phase_impact:
    reorganization_impact: "HIGH (team moves affect large domains)"

option_2_fine_50_domains:
  description: "50 focused domains (aligned with teams)"
  design_phase_impact:
    modeling_complexity: "HIGH (many domains to model)"
    ownership_clarity: "HIGH (each domain has 1-2 teams → clear ownership)"
  operations_phase_impact:
    governance_coordination: "LOW (small domains → few stakeholders)"
    domain_autonomy: "HIGH (teams independent)"
  maintenance_phase_impact:
    reorganization_impact: "LOW (team moves affect only their domain)"

recommendation: "Fine granularity (40-60 domains for 50-team org)"
rationale: "Conway's Law - domains should mirror teams for clear ownership"

kpi_impact:
  design_ownership_clarity:
    coarse: "60% stakeholder agreement"
    fine: "85% stakeholder agreement"
  operations_domain_autonomy:
    coarse: "LOW (teams wait on each other)"
    fine: "HIGH (teams move independently)"
```

---

### Operations Phase

#### Leading Indicators

| KPI | Target | Measurement | Why Leading | Failure Signal |
|-----|--------|-------------|-------------|----------------|
| **Domain registry accuracy** | >95% of records match reality | Manual audit: registry vs actual teams | Stale registry → wrong ownership → accountability failures | <90% → registry drifting |
| **Ownership update lag** | <7 days from team change to registry updated | Time: team reorg → registry reflects change | High lag → stale ownership → escalations go to wrong team | >14 days → update process broken |
| **Orphaned dataset detection rate** | 100% orphaned datasets flagged within 24 hours | # orphaned detected / # actual orphaned | Undetected orphans → no owner → quality degrades | <100% → detection gaps |

#### Lagging Indicators

| KPI | Target | Measurement | Why Lagging |
|-----|--------|-------------|-------------|
| **Total domains registered** | 50 domains | `COUNT(domains)` | Measures coverage but doesn't predict accuracy |
| **Average team size per domain** | 1-2 teams per domain | `AVG(teams per domain)` | Measures granularity but doesn't predict effectiveness |

---

## Workflow Orchestrator Archetype

**Reality Pattern**: Medieval trade guild apprenticeship - multi-stage approval process (apprentice → journeyman → master), inspections at each gate, guild elders approve/reject

**Pre-Digital Example**: Stonemason guild certification (1200s-1500s)
- Workflow: Submit work → journeyman inspects → master approves → guild records
- Gates: Quality check at each stage, rejection loops back
- KPIs tracked: Approval time (days from submit to approved), rejection rate, appeal rate

---

### Design Phase

#### Leading Indicators

| KPI | Target | Measurement | Why Leading | Failure Signal |
|-----|--------|-------------|-------------|----------------|
| **Approval flow coverage** | 100% business processes have defined workflows | # workflows defined / # processes | Missing workflows → ad-hoc approvals → inconsistency | <90% → gaps in coverage |
| **SLA definition completeness** | All approval stages have time SLAs | # stages with SLAs / # total stages | Missing SLAs → no accountability → slow approvals | <100% → no time commitments |
| **Escalation path clarity** | 100% workflows have escalation rules | # workflows with escalation / # total | Missing escalations → stuck workflows → delays | <100% → bottleneck risk |

#### Decision Impact Analysis

**Decision: Approval Strictness**

```yaml
decision: "How many approval stages should critical workflows have?"

option_1_minimal_1_approval:
  description: "Single approval (domain owner approves product publication)"
  design_phase_impact:
    workflow_complexity: "LOW (simple single-stage)"
  operations_phase_impact:
    approval_latency: "LOW (<1 day average)"
    quality_oversight: "LOW (single person can make mistakes)"
    compliance_risk: "HIGH (insufficient checks)"

option_2_multi_stage_3_approvals:
  description: "Three approvals (domain owner → security → governance)"
  design_phase_impact:
    workflow_complexity: "HIGH (multi-stage coordination)"
  operations_phase_impact:
    approval_latency: "HIGH (3-5 days average)"
    quality_oversight: "HIGH (multiple reviewers catch issues)"
    compliance_risk: "LOW (thorough review)"

recommendation: "Tiered based on product criticality"
rationale: |
  CRITICAL products (financial, PII, compliance):
    - 3-stage approval (owner → security → governance)
    - Justification: Risk warrants thorough review

  STANDARD products:
    - 2-stage approval (owner → automated governance)
    - Justification: Balance speed with quality

  EXPERIMENTAL products:
    - 1-stage approval (owner only)
    - Justification: Speed over rigor for experiments

kpi_impact:
  operations_approval_latency:
    minimal: "<1 day (fast but risky)"
    multi_stage: "3-5 days (slow but safe)"
    tiered: "1-5 days depending on product (balanced)"
```

---

### Operations Phase

#### Leading Indicators

| KPI | Target | Measurement | Why Leading | Failure Signal |
|-----|--------|-------------|-------------|----------------|
| **Approval latency trend** | Decreasing or stable | Weekly: p95 approval time | Increasing latency → bottlenecks forming → escalations rising | Latency increasing 10%/week → approver overloaded |
| **Rejection rate** | 10-20% (not too high, not too low) | # rejected / # submitted | Too high → poor quality submissions, too low → rubber-stamping | >30% → quality issues, <5% → not reviewing |
| **SLA breach rate** | <5% approvals miss SLA | # breached / # total approvals | High breach rate → approvers overloaded → escalations | >10% → bottleneck |
| **Escalation rate** | <10% workflows escalated | # escalated / # total workflows | High escalations → approvers blocking → process broken | >20% → systemic issues |

#### Lagging Indicators

| KPI | Target | Measurement | Why Lagging |
|-----|--------|-------------|-------------|
| **Workflows completed** | 500 workflows/month | `COUNT(workflows WHERE status = 'completed')` | Measures throughput but doesn't predict future bottlenecks |
| **Average approval time** | <2 days p50 | `MEDIAN(approval_duration)` | Measures speed but users already waited |

---

## Integration Platform Archetype

**Reality Pattern**: Medieval postal system - standardized message format, delivery routes, authentication (seals/signatures), routing logic

**Pre-Digital Example**: Venetian postal service (1400s-1600s)
- Messages: Standardized letter format, sealed envelopes
- Routes: Known paths between cities
- KPIs tracked: Delivery time (days), lost letter rate, forgery detection

---

### Design Phase

#### Leading Indicators

| KPI | Target | Measurement | Why Leading | Failure Signal |
|-----|--------|-------------|-------------|----------------|
| **Contract coverage** | 100% integrations have defined contracts | # integrations with contracts / # total | Missing contracts → integration breaks silently | <95% → contract gaps |
| **Contract format standardization** | All contracts use same format (OpenAPI, Avro, etc.) | # standard contracts / # total | Format inconsistency → hard to validate → runtime errors | <90% → standards not enforced |
| **Versioning strategy defined** | 100% contracts have versioning rules | # versioned / # total | Missing versioning → breaking changes → consumer failures | <100% → version chaos |

#### Decision Impact Analysis

**Decision: Contract Enforcement**

```yaml
decision: "When should contract validation happen?"

option_1_design_time:
  description: "Validate contracts during development (pre-commit hooks, CI)"
  operations_phase_impact:
    runtime_errors: "LOW (errors caught before deployment)"
    deployment_speed: "MEDIUM (blocked if validation fails)"
    consumer_trust: "HIGH (guaranteed valid contracts)"

option_2_runtime:
  description: "Validate contracts during API calls (runtime validation)"
  operations_phase_impact:
    runtime_errors: "HIGH (invalid data reaches production)"
    deployment_speed: "HIGH (no pre-deployment blocking)"
    consumer_trust: "LOW (runtime errors frustrate consumers)"

recommendation: "Both (defense in depth)"
rationale: "Design-time catches most errors, runtime catches edge cases"

kpi_impact:
  operations_integration_error_rate:
    design_time_only: "5% (some edge cases slip through)"
    runtime_only: "15% (many errors reach production)"
    both: "2% (comprehensive validation)"
```

---

### Operations Phase

#### Leading Indicators

| KPI | Target | Measurement | Why Leading | Failure Signal |
|-----|--------|-------------|-------------|----------------|
| **Contract compliance rate** | >99% API calls match contract | # compliant calls / # total calls | Low compliance → integration breaking → consumer failures | <95% → validation gaps |
| **API uptime** | >99.9% (3 nines) | Uptime monitoring | Downtime → integrations fail → cascading failures | <99.5% → infrastructure issues |
| **API latency trend** | p95 latency stable or decreasing | Weekly: p95 response time | Increasing latency → performance degradation → timeouts rising | Latency increasing 10%/week → scaling issues |
| **Breaking change rate** | <2% of contract changes are breaking | # breaking / # total changes | High rate → consumer pipelines break frequently | >5% → versioning not working |

#### Lagging Indicators

| KPI | Target | Measurement | Why Lagging |
|-----|--------|-------------|-------------|
| **Total integrations** | 200 integrations | `COUNT(integrations)` | Measures scale but doesn't predict health |
| **API call volume** | 1M calls/day | `SUM(api_calls)` | Measures usage but doesn't predict errors |

---

## System of Record Archetype

**Reality Pattern**: Medieval land registry - single authoritative record of property ownership, immutable ledger, legal source of truth

**Pre-Digital Example**: Domesday Book (1086 England)
- Purpose: Single source of truth for land ownership and taxes
- Immutability: Written record, changes appended (not erased)
- KPIs tracked: Coverage (% of land recorded), accuracy (disputes resolved), update frequency

---

### Design Phase

#### Leading Indicators

| KPI | Target | Measurement | Why Leading | Failure Signal |
|-----|--------|-------------|-------------|----------------|
| **Entity model completeness** | 100% business entities modeled | # entities in model / # real-world entities | Missing entities → incomplete system of record | <95% → gaps in coverage |
| **Uniqueness constraint coverage** | All entities have unique identifiers | # entities with unique IDs / # total | Missing constraints → duplicates → data quality issues | <100% → duplicate risk |
| **Audit trail design coverage** | 100% write operations logged | # operations with audit / # total operations | Missing audit → can't trace changes → compliance failures | <100% → audit gaps |

#### Decision Impact Analysis

**Decision: Immutability Level**

```yaml
decision: "Can records be updated or only appended?"

option_1_mutable:
  description: "Records can be updated in place (UPDATE statements allowed)"
  operations_phase_impact:
    audit_complexity: "HIGH (must track before/after for each change)"
    storage_cost: "LOW (no historical versions stored)"
    compliance_risk: "HIGH (can't prove 'what was the value on 2023-01-15?')"

option_2_immutable_append_only:
  description: "Records never updated, new versions appended (event sourcing)"
  operations_phase_impact:
    audit_complexity: "LOW (full history automatically preserved)"
    storage_cost: "HIGH (all historical versions stored)"
    compliance_risk: "LOW (complete audit trail, point-in-time queries)"

recommendation: "Immutable (append-only) for system of record"
rationale: "System of record requires auditability - worth the storage cost"

kpi_impact:
  operations_audit_completeness:
    mutable: "60% (manual audit logging, gaps occur)"
    immutable: "100% (automatic full history)"
```

---

### Operations Phase

#### Leading Indicators

| KPI | Target | Measurement | Why Leading | Failure Signal |
|-----|--------|-------------|-------------|----------------|
| **Data accuracy rate** | >99.9% records match source of truth | Sample audit: system records vs ground truth | Low accuracy → wrong decisions made on bad data | <99% → data quality issues |
| **Write latency trend** | p95 write latency stable | Weekly: p95 write time | Increasing latency → database slowdown → application timeouts | Latency increasing → scaling issues |
| **Duplicate detection rate** | 100% duplicates flagged before write | # duplicates caught / # duplicate attempts | Missed duplicates → data integrity compromised | <99.9% → constraint validation failing |
| **Audit trail completeness** | 100% changes have audit records | # changes with audit / # total changes | Missing audit → compliance violations | <100% → audit logging broken |

#### Lagging Indicators

| KPI | Target | Measurement | Why Lagging |
|-----|--------|-------------|-------------|
| **Total records** | 10M records | `COUNT(records)` | Measures scale but doesn't predict quality |
| **Write throughput** | 1,000 writes/second | `SUM(writes) per second` | Measures capacity but doesn't predict accuracy |

---

## Cross-Archetype Considerations

**Critical Insight**: Compositional decisions (how archetypes integrate) create **compound effects** on KPIs.

### Example: Marketplace + Supply Chain Integration

**Scenario**: Data mesh with Marketplace (Level 2) + Supply Chain (Level 3) for lineage tracking

**Compositional Decision**: Should lineage be auto-discovered or manually declared?

```yaml
decision: "How is lineage between products captured?"

option_1_auto_discovery:
  description: "Platform automatically discovers lineage by parsing SQL queries, ETL code"

  marketplace_impact:
    build_onboarding_time: "LOW (domains don't declare lineage manually)"
    operations_lineage_accuracy: "MEDIUM (70-80% accurate - misses complex transformations)"

  supply_chain_impact:
    operations_lineage_completeness: "MEDIUM (misses manual processes, external sources)"
    operations_impact_analysis_accuracy: "MEDIUM (incomplete lineage → missed dependencies)"

  compound_effect:
    - Marketplace onboarding is fast (good)
    - BUT lineage gaps → impact analysis wrong → breaking changes surprise consumers (bad)
    - NET: Fast onboarding but poor quality propagation tracking

option_2_manual_declaration:
  description: "Domains declare upstream dependencies in data contract YAML"

  marketplace_impact:
    build_onboarding_time: "MEDIUM (domains must list dependencies)"
    operations_lineage_accuracy: "HIGH (95%+ accurate - declared explicitly)"

  supply_chain_impact:
    operations_lineage_completeness: "HIGH (domains document all sources)"
    operations_impact_analysis_accuracy: "HIGH (complete lineage → accurate impact analysis)"

  compound_effect:
    - Marketplace onboarding slower (costs time upfront)
    - BUT lineage complete → impact analysis accurate → breaking changes predicted (good)
    - NET: Slower onboarding but high-quality quality propagation tracking

recommendation: "Manual declaration with automated validation"
rationale: |
  Domains declare lineage in contract:
    ```yaml
    upstream_dependencies:
      - product_id: "Payments.raw_transactions"
        relationship: "source"
      - product_id: "Customer.profile"
        relationship: "enrichment"
    ```

  Platform auto-validates:
    - Parse actual SQL/ETL code
    - Compare to declared dependencies
    - Alert if mismatch (declared vs actual)

  Best of both worlds:
    - Accuracy (manual declaration)
    - Validation (auto-discovery catches mistakes)

kpi_impact:
  marketplace_build_onboarding_time:
    auto: "4 hours (fast)"
    manual: "6 hours (slower, but acceptable)"

  supply_chain_operations_impact_analysis_accuracy:
    auto: "75% accuracy (missed dependencies cause surprise breakage)"
    manual: "95% accuracy (comprehensive dependency tracking)"

  compound_kpi_operations_breaking_change_incidents:
    auto: "20 incidents/quarter (missed dependencies)"
    manual: "5 incidents/quarter (accurate impact analysis)"
```

**Key Lesson**: Design decisions ripple across archetypes. A marketplace onboarding decision affects supply chain lineage quality, which affects operations incident rate.

---

## Template for Other Archetypes

To add KPIs for another archetype (Digital Twin, Supply Chain, Workflow Orchestrator, etc.):

```yaml
archetype_name: "ARCHETYPE_NAME"
reality_pattern: "Pre-digital analog and what it did"

lifecycle_phases:
  design_phase:
    leading_indicators:
      - name: "KPI_NAME"
        target: "Quantified target"
        measurement: "How to measure"
        why_leading: "What failure it predicts"
        failure_signal: "Warning threshold"

    lagging_indicators:
      - name: "KPI_NAME"
        target: "Quantified target"
        measurement: "How to measure"
        why_lagging: "Why it's retrospective"

    decision_impact_analysis:
      - decision: "Key design choice"
        options:
          option_1:
            downstream_kpi_impact:
              build_phase_kpi: "Impact on build"
              ops_phase_kpi: "Impact on operations"
          option_2:
            downstream_kpi_impact:
              build_phase_kpi: "Different impact"
        recommendation: "Which option and why"

  build_phase:
    # [Same structure]

  implementation_phase:
    # [Same structure]

  operations_phase:
    # [Same structure]

  maintenance_phase:
    # [Same structure]
```

---

## Next Archetypes to Document

**Priority order**:
1. **Supply Chain Network (Level 3)** - Lineage tracking, quality propagation, provenance
2. **Digital Twin (Level 1)** - Org chart accuracy, domain ownership clarity
3. **Integration Platform** - Contract compliance, API uptime, integration health
4. **Workflow Orchestrator** - Approval latency, workflow completion rate, human-in-the-loop effectiveness
5. **System of Record** - Data accuracy, auditability, single source of truth compliance
6. **Analytics Platform** - Query performance, insight delivery time, self-service analytics adoption
7. **Event-Driven Platform** - Event lag, ordering guarantees, streaming throughput

---

## How to Use This Framework

### During Design Phase
1. Choose archetypes for your solution
2. Review KPIs for chosen archetypes
3. Make explicit decisions using Decision Impact Analysis
4. Document trade-offs and predictions

### During Build Phase
1. Instrument leading indicators (dashboards, alerts)
2. Track onboarding time, validation pass rate, etc.
3. If leading indicators degrade → investigate and fix BEFORE operations

### During Implementation Phase
1. Monitor early SLO compliance, consumer satisfaction
2. Adjust rollout pace based on leading indicators
3. Don't proceed to next phase if leading indicators failing

### During Operations Phase
1. Watch for trend changes (SLO compliance declining, NPS dropping)
2. Leading indicators predict problems → fix proactively
3. Lagging indicators confirm success → celebrate

### During Maintenance Phase
1. Track schema evolution success rate, technical debt
2. Plan migrations based on leading indicators
3. Continuous improvement loop

---

**End of Document**

*This framework is living - as we gain operational experience with compositional archetypes, KPIs will be refined and expanded.*
