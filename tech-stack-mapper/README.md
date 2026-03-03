# Tech Stack Mapper

**Purpose**: Generate contextualized technology recommendations based on organizational constraints, team capabilities, and project requirements.

**Key Insight**: Same capability, different constraints = different technology choice. This framework makes trade-offs explicit and helps organizations make informed decisions that fit THEIR reality.

---

## What Problem Does This Solve?

Common technology selection mistakes:

1. **Cargo cult**: "Netflix uses microservices, so should we" (ignoring 1000× scale difference)
2. **Resume-driven development**: "Team wants to learn Kubernetes" (business needs ignored)
3. **Hidden cost blindness**: "It's free, it's open source!" (ignoring $150K/year operational labor)
4. **Sunk cost fallacy**: "We paid $2M for Oracle, must use it for everything"
5. **Vendor marketing**: "This tool solves all problems!" (ignoring fit with constraints)

**Tech-stack-mapper** surfaces these biases and generates recommendations based on YOUR constraints, not someone else's blog post.

---

## How It Works

### Two-Phase Process

**Phase 1: Discovery (Generate Questions)**
- Skill reads `frameworks/discovery-questionnaire.md`
- Generates project-specific questions
- Captures organizational constraints
- Output: Constraint profile (YAML)

**Phase 2: Recommendation (Apply Framework)**
- Skill reads `frameworks/recommendation-engine.md`
- Filters options by hard constraints
- Scores remaining options on 5 criteria
- Calculates contextualized TCO for YOUR scale
- Applies decision rules
- Output: Recommendation with rationale

---

## Structure

```
tech-stack-mapper/
├── README.md                           # This file
├── frameworks/
│   ├── discovery-questionnaire.md      # WHAT to ask and WHY
│   └── recommendation-engine.md        # HOW to filter, score, decide
└── examples/
    ├── startup-mvp/                    # 5 person team, 3 month deadline
    │   ├── 1-input-requirements.md
    │   ├── 2-generated-questions.md
    │   ├── 3-user-answers.yaml
    │   └── 4-recommendation.md
    └── enterprise-scale/               # 200 person team, multi-year timeline
        └── [similar structure]
```

---

## Frameworks

### Discovery Questionnaire Framework

**File**: `frameworks/discovery-questionnaire.md`

**Purpose**: Defines WHAT questions to ask to surface organizational constraints

**Question Categories**:
1. **Organization Context** - Team size, budget, funding stage, maturity
2. **Time Constraints** - Deadline, consequence of delay, competitive pressure
3. **Team Capabilities** - Existing skills, learning appetite, hiring capacity
4. **Existing Investments** - Sunk costs, vendor contracts, technical debt
5. **Operational Reality** - Who runs production, availability needs, on-call setup
6. **Support Expectations** - Support model, acceptable downtime, patch urgency
7. **Compliance Constraints** - Regulatory requirements, data residency, audit trails
8. **Risk Tolerance** - Tech preference, past trauma, primary fears
9. **Political Realities** - Decision authority, mandates, vendor relationships

**Output**: Structured constraint profile (YAML)

---

### Recommendation Engine Framework

**File**: `frameworks/recommendation-engine.md`

**Purpose**: Defines HOW to generate recommendations from constraint profiles

**Process** (6 steps):
1. **Filter by Hard Constraints** - Remove options that violate non-negotiable requirements
2. **Score by Fit** - Quantify how well each option matches (5 criteria, 0-100 each)
3. **Calculate Contextualized TCO** - 3-year total cost for YOUR scale (not generic)
4. **Apply Decision Rules** - If-then logic to choose winner
5. **Generate Rationale** - Explain WHY given THEIR constraints
6. **Identify Alternative Scenarios** - When would recommendation change?

**Output**: Recommendation report with rationale, trade-offs, risks, next steps

---

## Examples

### Startup MVP Example

**Constraints**:
- 5 person team
- 3 month deadline
- No ops team
- $500K runway
- Founder trauma (previous startup over-engineered infra and died)

**Capability**: Data catalog (Marketplace archetype)

**Recommendation**: Managed SaaS (Atlan)
- **Why**: 4× faster (2 weeks vs 8 weeks), zero ops burden, 4.2× cheaper when labor included ($117K vs $493.5K over 3 years)
- **Key Insight**: "Free" open source costs more when labor included

**Files**: `examples/startup-mvp/`

---

### Enterprise Scale Example (To Be Added)

**Constraints**:
- 200 person team
- Multi-year timeline
- Dedicated platform team (10 SRE engineers)
- $50M engineering budget
- Compliance (SOX, HIPAA)

**Capability**: Same data catalog

**Recommendation**: Self-hosted (OpenMetadata on Kubernetes)
- **Why**: Economies of scale, control for compliance, platform team can operate, save $500K/year at scale
- **Key Insight**: Self-hosting makes sense at enterprise scale with dedicated platform team

---

## How to Use

### As a Human User

1. Identify capability needed (from solution-mapper or requirements)
2. Run through discovery questions (use `frameworks/discovery-questionnaire.md`)
3. Capture answers in structured format (see example YAML)
4. Apply recommendation engine (use `frameworks/recommendation-engine.md`)
5. Generate recommendation report

### As Claude (Skill)

1. User invokes: "Help me choose technology for [capability]"
2. Read `frameworks/discovery-questionnaire.md`
3. Generate project-specific questions
4. Capture user answers in constraint profile
5. Read `frameworks/recommendation-engine.md`
6. Apply filters, scoring, TCO calculation, decision rules
7. Generate recommendation report
8. Output to user

---

## Key Principles

### 1. No Universal "Best"
Every organization is different. Same capability, different constraints = different technology choice.

**Example**:
- Startup (5 people, 3 months) → Managed SaaS
- Enterprise (200 people, multi-year) → Self-hosted

### 2. Make Biases Explicit
Surface cognitive biases:
- Cargo cult: "Netflix uses X, so should we"
- Sunk cost: "We paid for Oracle, must maximize it"
- Resume-driven: "Team wants to learn Y"
- Availability bias: "Just read about Z, let's use it"

Address biases explicitly in recommendations.

### 3. Expose Hidden Costs
"Free" open source has hidden costs:
- Operational labor ($75-150K/year)
- Learning curve ($37.5K one-time)
- Maintenance ($15-45K/year)
- Monitoring ($10-30K/year)
- Disaster recovery ($10-100K/year)

Calculate TOTAL cost, not just license fees.

### 4. Context-Specific TCO
Don't use generic cost ranges like "$0-500K/year".

Calculate for THEIR specific scale:
- 50 products vs 500 products
- 100 users vs 1000 users
- 1000 queries/day vs 1M queries/day

Use their volume to calculate actual costs.

### 5. Reversibility
Show what would change recommendation:
- "IF you hire SRE team, THEN reconsider self-hosted"
- "IF scale 10×, THEN self-hosted saves $200K/year"
- "IF need custom features, THEN may need open source"

Decisions aren't permanent - constraints change.

### 6. Trade-offs, Not Optimization
No perfect choice. Make trade-offs explicit:
- "Higher cash cost, but zero operational burden"
- "Vendor lock-in, but can migrate later"
- "Less customization, but standard needs met"

Explain why trade-offs are worth it for THEIR situation.

---

## Integration with Other Skills

### From solution-mapper
```
solution-mapper identifies archetypes → tech-stack-mapper chooses technology
```

Example:
1. solution-mapper: "Need Marketplace (L2) + Supply Chain (L3)"
2. solution-mapper: "Capabilities: Catalog, Lineage, API Gateway"
3. User: "Help me choose tech"
4. tech-stack-mapper: [Runs discovery + recommendation]

### To implementation-roadmap
```
tech-stack-mapper chooses tech → implementation-roadmap plans rollout
```

Example:
1. tech-stack-mapper: "Recommend managed SaaS"
2. User: "How do we roll this out?"
3. implementation-roadmap: "Given SaaS choice, here's phased rollout..."

---

## When to Use

**Use tech-stack-mapper for**:
- Strategic technology selection (databases, platforms, orchestration)
- High-risk, hard-to-reverse choices
- SaaS vs self-hosted decisions
- Build vs buy decisions
- Multi-option evaluation (3+ viable options)

**Don't use for**:
- Library choices (use what team knows)
- Tactical decisions (logging library, CSS framework)
- When constraints are obvious ("We're an AWS shop, use AWS services")
- Trivial selections

---

## Contributing

To add a new example:

1. Create folder: `examples/[scenario-name]/`
2. Add files:
   - `1-input-requirements.md` - What user asked for
   - `2-generated-questions.md` - Questions skill asked
   - `3-user-answers.yaml` - What user answered
   - `4-recommendation.md` - What skill recommended
3. Show complete end-to-end flow
4. Demonstrate how different constraints lead to different recommendations

---

## License

Part of the Compositional Architecture Framework.

---

**End of README**
