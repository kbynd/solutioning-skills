# Archetype Completeness Methodology

## Purpose

This methodology helps predict **adoption success** of any architecture implementation by measuring how completely it implements the chosen reality archetype.

**Key Insight**: Incomplete archetype implementation → Poor adoption

From reality:
- Incomplete marketplace (no discovery, no quality signals) → Few merchants, fewer buyers, market dies
- Incomplete twin (mirror doesn't update with physical changes) → Divergence, trust collapses
- Incomplete workflow (missing approval step) → Process broken, workarounds emerge

**This framework provides**: Systematic way to measure archetype completeness and predict adoption rates.

---

## Core Principle

**Archetype Completeness → Adoption Success**

```
100% Archetype Completeness → 80-90% Adoption (full pattern implemented)
70% Archetype Completeness  → 50-60% Adoption (functional with gaps)
40% Archetype Completeness  → 20-30% Adoption (struggling, critical gaps)
15% Archetype Completeness  → 10-20% Adoption (infrastructure only, no pattern)
```

**Why this correlation exists**:
- Reality patterns work because ALL components work together
- Remove discovery from marketplace → Buyers can't find sellers → No transactions
- Remove quality signals → Buyers don't trust → No purchases
- Infrastructure alone ≠ Pattern (roads without shops = no commerce)

---

## Methodology Steps

### Step 1: Identify Target Archetype

From requirements, determine which reality archetype the implementation claims to follow.

**Example** (Data Mesh):
- Claims: "Data products as tradeable commodities"
- Claims: "Domain ownership with self-service"
- Claims: "Federated governance"
- **Archetype**: Marketplace/Portal (Level 2 of compositional pattern)

### Step 2: Map Reality Pattern Components

For the identified archetype, list ALL components from the reality pattern (pre-digital).

**Example** (Marketplace from 1455 Grand Bazaar):

| Reality Component | Pre-Digital Implementation | Purpose |
|-------------------|---------------------------|---------|
| **Discovery** | Walk through stalls, see goods, read signs | Buyers find sellers |
| **Quality Signals** | Merchant reputation, certifications, return policy | Buyers assess trust |
| **Transactions** | Purchase contracts, currency exchange | Complete the trade |
| **Usage Tracking** | Sales records, market fees | Merchants track ROI |
| **Supply Chain** | Provenance ("Where from?"), ingredient tracking | Trust and quality propagation |
| **Governance** | Inspectors, weights & measures, posted rules | Enforce standards |
| **Onboarding** | Rent stall, set up shop, post pricing | Sellers join market |

**Total Components**: 7 (for Marketplace archetype)

### Step 3: Evaluate Implementation Completeness

For each reality component, assess the implementation:

**Scoring**:
- ✅ **Fully Implemented** (1.0): Component works as in reality
- 🟡 **Partially Implemented** (0.5): Component exists but has gaps
- ❌ **Missing** (0.0): Component not implemented

**Example** (AWS Lake Formation Data Mesh):

| Reality Component | AWS Implementation | Score | Gap Impact |
|-------------------|-------------------|-------|------------|
| **Discovery** | ❌ Must know exact table name, no search | 0.0 | 🔴 CRITICAL - Blocks adoption |
| **Quality Signals** | ❌ No SLO tracking, no compliance display | 0.0 | 🔴 CRITICAL - Kills trust |
| **Transactions** | ✅ Lake Formation access control | 1.0 | 🟢 Works |
| **Usage Tracking** | ❌ No metering, no cost allocation | 0.0 | 🔴 CRITICAL - No ROI proof |
| **Supply Chain** | ❌ No cross-domain lineage | 0.0 | 🟡 MODERATE - Trust issues |
| **Governance** | 🟡 Manual Lake Formation rules | 0.5 | 🟡 MODERATE - Doesn't scale |
| **Onboarding** | 🟡 Complex AWS setup, no templates | 0.5 | 🟡 MODERATE - High friction |

**Completeness Score**: (0 + 0 + 1 + 0 + 0 + 0.5 + 0.5) / 7 = **2.0 / 7 = 29%**

### Step 4: Identify Critical vs Nice-to-Have

Not all components are equally important. Categorize by impact:

**Critical** (Must Have for Adoption):
- Missing these → Pattern doesn't work → Adoption fails
- Example: Marketplace without discovery = Buyers can't find sellers

**Important** (Should Have):
- Missing these → Pattern works poorly → Reduced adoption
- Example: Marketplace without usage tracking = Sellers can't prove ROI

**Nice-to-Have** (Optional):
- Missing these → Pattern works, some friction
- Example: Marketplace without premium onboarding = High friction, but possible

**Example** (Marketplace):

**Critical**:
1. Discovery (can't find products = no adoption)
2. Quality Signals (don't trust products = no usage)
3. Transactions (can't access products = no value)

**Important**:
4. Usage Tracking (can't prove ROI = funding cut)
5. Supply Chain (can't trust dependencies = avoid using)

**Nice-to-Have**:
6. Governance (manual workarounds possible)
7. Onboarding (platform team can help)

**Adjusted Score**: Critical components weighted 2x

```
Adjusted = (Critical × 2 + Important + Nice) / (Critical_Count × 2 + Important_Count + Nice_Count)

AWS Example:
= (1.0 × 2 + 0.5 + 0.5) / (3 × 2 + 2 + 2)
= 3.0 / 10
= 30%
```

### Step 5: Predict Adoption

Use completeness score to predict adoption rate:

| Completeness Score | Predicted Adoption | Why |
|--------------------|-------------------|-----|
| **80-100%** | 70-90% | Full pattern implemented, works as in reality |
| **60-79%** | 50-70% | Functional with gaps, workarounds needed |
| **40-59%** | 30-50% | Core works, but friction high |
| **20-39%** | 15-30% | Critical gaps, only enthusiasts adopt |
| **0-19%** | 5-15% | Infrastructure only, pattern broken |

**Example** (AWS Lake Formation):
- **Score**: 29% (adjusted for critical components)
- **Prediction**: 15-30% adoption
- **Why**: 3 critical components missing (discovery, quality, usage tracking)

### Step 6: Identify Gaps and Remediation

For each missing/partial component, identify what's needed to close the gap.

**Example** (AWS Lake Formation):

| Gap | Reality Component | What's Missing | Remediation |
|-----|------------------|----------------|-------------|
| 🔴 **Gap 1** | Discovery | Searchable catalog, product listing, faceted filtering | Add OpenMetadata or DataHub (not Glue Catalog) |
| 🔴 **Gap 2** | Quality Signals | SLO definition, compliance monitoring, quality scores | Add Great Expectations + Datadog for SLO tracking |
| 🔴 **Gap 3** | Usage Tracking | Query metering, cost allocation, ROI dashboard | Add custom usage metering + AWS Cost Explorer API |
| 🟡 **Gap 4** | Supply Chain | Cross-domain lineage, impact analysis | Add OpenLineage + OpenMetadata |
| 🟡 **Gap 5** | Governance | Policy-as-code, automated validation | Add OPA (Open Policy Agent) |
| 🟡 **Gap 6** | Onboarding | Self-service templates, CLI tools | Build Terraform modules + CLI wrapper |

**Estimated Improvement**:
- Close Gap 1, 2, 3 (critical) → 60% completeness → 50-70% adoption
- Close all gaps → 85% completeness → 70-90% adoption

---

## Archetype-Specific Scorecards

### Marketplace/Portal Scorecard

| Component | Pre-Digital Example | Digital Requirement | Weight |
|-----------|-------------------|-------------------|--------|
| **Discovery** | Browse stalls, see goods | Searchable catalog, product listings | Critical (2x) |
| **Quality Signals** | Merchant reputation, certifications | SLO compliance, quality scores | Critical (2x) |
| **Transactions** | Purchase, contracts | Access control, subscriptions | Critical (2x) |
| **Usage Tracking** | Sales records, market fees | Query metering, cost allocation | Important (1x) |
| **Supply Chain** | Provenance tracking | Data lineage, dependencies | Important (1x) |
| **Governance** | Inspectors, posted rules | Policy-as-code, validation gates | Nice-to-Have (1x) |
| **Onboarding** | Rent stall, set up | Self-service templates, CLI | Nice-to-Have (1x) |

**Total Weight**: 10 (3×2 + 2×1 + 2×1)

### Digital Twin Scorecard

| Component | Pre-Digital Example | Digital Requirement | Weight |
|-----------|-------------------|-------------------|--------|
| **Asset Registry** | Physical inventory, org chart | Entity repository, domain registry | Critical (2x) |
| **State Synchronization** | Model matches physical | Real-time telemetry, event ingestion | Critical (2x) |
| **Deviation Detection** | Model ≠ physical → Alert | Anomaly detection, alerting | Critical (2x) |
| **Historical Tracking** | Evolution over time | Audit trail, time-series storage | Important (1x) |
| **Performance Analysis** | KPI tracking | Analytics, dashboards | Nice-to-Have (1x) |

**Total Weight**: 8 (3×2 + 1×1 + 1×1)

### Workflow Orchestrator Scorecard

| Component | Pre-Digital Example | Digital Requirement | Weight |
|-----------|-------------------|-------------------|--------|
| **Workflow Engine** | Multi-step processes | State machine, task routing | Critical (2x) |
| **Approval Manager** | Human sign-offs | Notification, approval UI | Critical (2x) |
| **Task Manager** | Work assignments | Task queue, assignment logic | Critical (2x) |
| **Audit Trail** | Paper trail, signatures | Immutable log, compliance | Important (1x) |
| **SLA Tracking** | Deadlines | Escalation, SLA monitoring | Nice-to-Have (1x) |

**Total Weight**: 8 (3×2 + 1×1 + 1×1)

### System of Record Scorecard

| Component | Pre-Digital Example | Digital Requirement | Weight |
|-----------|-------------------|-------------------|--------|
| **Entity Repository** | Ledger, registry | Master data storage | Critical (2x) |
| **Data Quality Engine** | Validation rules | Schema enforcement, validation | Critical (2x) |
| **Change Tracker** | Amendment log | Audit trail, versioning | Critical (2x) |
| **Search/Query** | Index, lookup | API, query interface | Important (1x) |
| **Integration API** | Request/response | REST/GraphQL API | Nice-to-Have (1x) |

**Total Weight**: 8 (3×2 + 1×1 + 1×1)

---

## Worked Example: AWS Lake Formation Data Mesh

**Full analysis**: See `../examples/data-mesh/aws-gap-analysis.md`

### Step 1: Identify Archetype

**Source**: [AWS Blog - Design a data mesh architecture using AWS Lake Formation and AWS Glue](https://aws.amazon.com/blogs/big-data/design-a-data-mesh-architecture-using-aws-lake-formation-and-aws-glue/)

**Claims**: Data products, domain ownership, self-service, federated governance

**Archetype**: Marketplace/Portal (Level 2 of data mesh composition)

### Step 2: Map Reality Components

**Reality Pattern**: Grand Bazaar (1455), Medieval Markets

**Components**: Discovery, Quality Signals, Transactions, Usage Tracking, Supply Chain, Governance, Onboarding

### Step 3: Evaluate Completeness

| Component | AWS Implementation | Score | Evidence |
|-----------|-------------------|-------|----------|
| Discovery | ❌ No catalog search, must know table name | 0.0 | Glue Data Catalog ≠ searchable catalog |
| Quality Signals | ❌ No SLO tracking, no compliance display | 0.0 | No quality monitoring shown |
| Transactions | ✅ Lake Formation permissions | 1.0 | Cross-account sharing works |
| Usage Tracking | ❌ No metering, no cost allocation | 0.0 | No usage analytics shown |
| Supply Chain | ❌ No lineage tracking | 0.0 | No cross-domain dependencies |
| Governance | 🟡 Manual Lake Formation rules | 0.5 | Exists but manual (doesn't scale) |
| Onboarding | 🟡 AWS account setup required | 0.5 | Possible but high friction |

**Raw Score**: 2.0 / 7 = **29% completeness**

### Step 4: Critical Component Weighting

**Critical**: Discovery, Quality Signals, Transactions (weight 2x)
**Important**: Usage Tracking, Supply Chain (weight 1x)
**Nice-to-Have**: Governance, Onboarding (weight 1x)

**Adjusted Score**: (0×2 + 0×2 + 1×2 + 0×1 + 0×1 + 0.5×1 + 0.5×1) / 10 = **3.0 / 10 = 30%**

### Step 5: Predict Adoption

**Completeness**: 30%
**Predicted Adoption**: 15-30%

**Why**:
- 3 critical components missing (discovery, quality, usage)
- Consumers can't FIND products → Email producers (defeats purpose)
- Consumers can't ASSESS quality → Don't trust data → Verify themselves
- Producers can't SHOW ROI → Management cuts funding

**Expected Timeline**:
- Month 1-3: 3-5 pilot domains (with platform team support)
- Month 4-6: Adoption stalls, complaints about discoverability
- Month 7-9: Only 10% of domains participate
- Month 10-12: Project quietly dies, revert to central data lake

### Step 6: Gap Remediation

**To reach 60% completeness (50-70% adoption)**:

| Gap | Solution | Cost | Timeline |
|-----|----------|------|----------|
| Discovery | Add OpenMetadata (catalog search) | $2,500/month | 3 months |
| Quality Signals | Add Great Expectations (SLO monitoring) | $500/month | 2 months |
| Usage Tracking | Build metering + Cost Explorer integration | $1,000/month | 3 months |

**Total**: +$4,000/month → 60% completeness → 50-70% adoption

**To reach 85% completeness (70-90% adoption)**:

Add lineage (OpenLineage), governance automation (OPA), self-service CLI

**Total**: +$6,000/month → 85% completeness → 70-90% adoption

---

## Key Takeaways

### 1. Infrastructure ≠ Pattern

**Common Mistake**: "We built the infrastructure (AWS, Kubernetes, databases), why isn't it adopted?"

**Reality**: Infrastructure provides PLUMBING, not PATTERN.

**Example**:
- AWS Lake Formation = Roads, utilities (necessary infrastructure)
- Marketplace = Shops, signage, quality checks (commerce pattern)
- **Roads without shops = No commerce**

### 2. Critical Components Are Critical

You can't skip critical components and expect adoption:
- Marketplace without discovery = Buyers can't find sellers
- Twin without state sync = Model diverges from reality
- Workflow without approvals = Process broken

**Fix 100% of critical components BEFORE adding nice-to-haves.**

### 3. Partial Implementation Worse Than None

**Why**: Raises expectations, then disappoints.

**Example**:
- Announce "We have data mesh!" (expectation: can find data products)
- Reality: Must email producer to learn table name (disappointment)
- Result: Trust lost, harder to fix later

**Better**: Pilot with full pattern in narrow scope, then expand.

### 4. Archetype Completeness Predicts Adoption

This isn't theory - it's observed reality:

**From marketplaces**:
- Complete markets (discovery, quality, transactions) = High adoption
- Incomplete markets (no signage, no quality) = Markets die

**From digital twins**:
- Complete twins (real-time sync, deviation alerts) = High usage
- Incomplete twins (stale data, no sync) = Ignored, revert to manual

**From workflows**:
- Complete workflows (all approval steps) = Process followed
- Incomplete workflows (missing steps) = Shadow processes emerge

**Pattern is universal**: Incomplete archetype → Poor adoption

---

## Usage Guidelines

### When to Use This Methodology

**Use when**:
- ✅ Evaluating vendor solutions (does this implement the pattern fully?)
- ✅ Designing architecture (what components must we build?)
- ✅ Predicting project success (will this get adopted?)
- ✅ Prioritizing roadmap (which gaps to close first?)
- ✅ Justifying budget (why do we need OpenMetadata + Great Expectations?)

**Don't use when**:
- ❌ Requirements unclear (must identify archetype first)
- ❌ Single-use tool (not a pattern, just a utility)
- ❌ Greenfield with no adoption goal (build what you need)

### How to Present Findings

**To Executives**:
```
Current completeness: 30%
Predicted adoption: 15-30%
Critical gaps: Discovery, Quality Signals, Usage Tracking

Investment needed: $4K/month
Expected adoption: 50-70% (vs current 15-30%)
ROI: 3x more domains participate → 3x more data products → 10x more insights
```

**To Architects**:
```
Archetype: Marketplace/Portal
Components: 7 total (3 critical, 2 important, 2 nice-to-have)
Implemented: 1/3 critical (Transactions only)
Missing: Discovery, Quality Signals (both critical)

Recommendation: Add OpenMetadata (discovery) + Great Expectations (quality)
Cost: $3K/month
Benefit: Jump from 30% → 60% completeness
```

**To Developers**:
```
Users can't find data products because Glue Catalog isn't searchable.

Build:
1. OpenMetadata integration (catalog search)
2. Product listing page (browse all products)
3. Faceted filtering (by domain, freshness, quality)

Why: Marketplace without discovery = buyers can't find sellers = no adoption
```

---

## Next Steps

1. **Identify your archetype** - Which reality pattern are you implementing?
2. **Map reality components** - What components exist in the pre-digital pattern?
3. **Evaluate completeness** - Score your current implementation
4. **Predict adoption** - Use completeness score to forecast
5. **Close critical gaps** - Fix must-haves before nice-to-haves
6. **Measure actual adoption** - Validate prediction, refine model

**Remember**: Reality patterns work because ALL components work together. Partial implementation = Partial adoption.

---

**Related Documentation**:
- `../examples/data-mesh/aws-gap-analysis.md` - Complete worked example
- `compositional-archetypes.md` - How archetypes compose
- `../skills/solution-mapper.md` - How to identify archetypes from requirements

**End of Methodology**
