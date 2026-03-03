# Enterprise Scale Example

**Scenario**: Large financial services company implementing data mesh

## Context

**Organization**: Established enterprise (15 years old)
**Size**: 200 data engineers, 50 domains, 500+ data products expected
**Budget**: $50M engineering budget annually
**Platform Team**: Dedicated 10-person SRE team

## Key Constraints (Different from Startup)

| Constraint | Startup MVP | Enterprise Scale |
|------------|-------------|------------------|
| Team size | 5 engineers | 200 engineers |
| Deadline | 3 months (critical) | 18 months (strategic) |
| Ops team | None | 10 SRE engineers |
| Budget | $500K (constrained) | $50M (well-funded) |
| Primary fear | Complexity | Loss of control |
| Compliance | None | SOX, HIPAA |
| Scale | 50 products | 500+ products |

---

## Recommendation: Self-Hosted OpenMetadata (Kubernetes)

**Why Different from Startup**:

### 1. Economies of Scale
```yaml
atlan_saas_cost_at_scale:
  users: 500
  products: 500
  annual_cost: "$500K/year (enterprise tier)"
  3_year_total: "$1.5M"

self_hosted_cost_at_scale:
  infrastructure: "$50K/year (K8s cluster, managed DBs)"
  operational_labor: "$200K/year (2 FTE out of 10 SRE team)"
  maintenance: "$50K/year"
  monitoring: "$30K/year"
  disaster_recovery: "$20K/year"
  3_year_total: "$1.05M"

SAVINGS: "$450K over 3 years (30% cost reduction)"
```

### 2. Platform Team Can Operate
```yaml
startup_constraint: "No ops team (can't self-host)"
enterprise_reality: "10 SRE engineers (2 FTE = 20% of SRE capacity)"

operational_fit:
  startup: 40/100 (developers can't handle K8s)
  enterprise: 90/100 (SRE team expertise in K8s, distributed systems)
```

### 3. Control for Compliance
```yaml
compliance_requirements: ["SOX", "HIPAA"]

saas_limitation:
  - "Vendor controls security patches (timing not guaranteed)"
  - "Audit trail may not meet SOX requirements"
  - "HIPAA BAA required (vendor controls)"

self_hosted_advantage:
  - "Full control over security patches (apply immediately)"
  - "Custom audit trail (immutable event sourcing)"
  - "HIPAA compliance in-house (no vendor BAA needed)"
```

### 4. Time Pressure Different
```yaml
startup: "3 months (existential) → MUST choose fastest option"
enterprise: "18 months (strategic) → CAN invest in proper setup"

time_to_production:
  saas: "4 weeks (still fastest)"
  self_hosted_kubernetes: "4 months (acceptable within 18-month timeline)"

rationale: "Have 14 months remaining for data mesh work (vs 2.5 months lost to catalog setup)"
```

---

## TCO Comparison

| Cost Component | Managed SaaS | Self-Hosted | Winner |
|----------------|--------------|-------------|--------|
| Year 1 | $500K | $400K | Self-hosted |
| Year 2 | $500K | $350K | Self-hosted |
| Year 3 | $500K | $350K | Self-hosted |
| **3-Year Total** | **$1.5M** | **$1.1M** | **Self-hosted saves $400K** |

**Key Insight**: At enterprise scale, self-hosting becomes cost-effective (opposite of startup).

---

## When This Recommendation Changes

### Scenario: Platform Team Downsized
```yaml
IF: "SRE team shrinks from 10 → 2 people"
THEN: "Reconsider managed SaaS"
BECAUSE: "2 FTE burden on 2-person team = 100% capacity (unsustainable)"
```

### Scenario: Faster Time to Market Needed
```yaml
IF: "Deadline changes from 18 months → 3 months"
THEN: "Choose managed SaaS"
BECAUSE: "Can't afford 4-month catalog setup (consumes entire timeline)"
```

### Scenario: Scale Decreases 10×
```yaml
IF: "Scale decreases from 500 products → 50 products"
THEN: "Reconsider managed SaaS"
BECAUSE: "SaaS cost drops to $50K/year, labor costs stay $350K/year"
```

---

## Key Lesson

**Same capability (data catalog), different constraints → opposite recommendation**:
- **Startup (5 people, 3 months)**: Managed SaaS wins (4× faster, 4× cheaper when labor included)
- **Enterprise (200 people, 18 months)**: Self-hosted wins (30% cheaper, control for compliance)

**What changed**:
1. Economies of scale (500 products vs 50 products)
2. Operational capacity (10 SRE vs 0 SRE)
3. Time pressure (18 months vs 3 months)
4. Compliance requirements (SOX/HIPAA vs none)

**Framework principle**: No universal "best" - constraints drive decisions.

---

**End of Enterprise Scale Example**
