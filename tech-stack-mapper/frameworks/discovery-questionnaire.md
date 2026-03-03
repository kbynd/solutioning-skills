# Discovery Questionnaire Framework

**Purpose**: Define WHAT to ask to understand organizational constraints that drive technology selection decisions.

**Usage**: Claude reads this framework, then generates project-specific questions based on the capability being evaluated.

**Critical Insight**: Technology choices are constrained by human and political realities, not just technical requirements. These questions surface those constraints.

---

## Question Categories

### Section 1: Organization Context

**What This Reveals**: Scale, maturity, funding stage, growth trajectory

**Questions to Ask**:

1. **Team Size**
   - "How many engineers on your team?"
   - Options: 1-10 (small), 11-50 (mid-size), 50-200 (large), 200+ (enterprise)
   - **Why it matters**: Small teams can't afford operational overhead; large teams can justify platform investment

2. **Engineering Budget**
   - "What's your annual engineering budget?"
   - Options: <$1M (constrained), $1M-$10M (moderate), >$10M (well-funded)
   - **Why it matters**: Determines cash vs time trade-offs (buy SaaS vs build/operate)

3. **Funding Stage**
   - "What's your funding/business stage?"
   - Options: Pre-seed, Series A-B, Series C+, Profitable, Enterprise
   - **Why it matters**: Startups optimize for speed, enterprises for control

4. **Organization Age**
   - "How long has your organization been around?"
   - Options: <1 year (new), 1-3 years (growing), 3-10 years (mature), 10+ years (established)
   - **Why it matters**: Reveals technical debt, existing systems, change capacity

---

### Section 2: Time Constraints

**What This Reveals**: Urgency, competitive pressure, consequences of delay

**Questions to Ask**:

1. **Deadline**
   - "When do you need this operational in production?"
   - Options: ASAP (weeks), 3 months, 6 months, 12+ months, no hard deadline
   - **Why it matters**: Drives buy vs build, managed vs self-hosted

2. **Consequence of Missing Deadline**
   - "What happens if you miss this deadline?"
   - Options: Company dies, lose major customer, miss quarterly OKR, no major consequence
   - **Why it matters**: Severity determines acceptable risk and cost trade-offs

3. **Competitive Pressure**
   - "Are you under competitive pressure to ship?"
   - Options: Critical (competitor launching soon), moderate (market window), low (internal initiative)
   - **Why it matters**: High pressure = pay for speed (managed services)

---

### Section 3: Team Capabilities

**What This Reveals**: Existing skills, learning capacity, hiring ability

**Questions to Ask**:

1. **Existing Skills**
   - "What technologies does your team already know well?"
   - Format: Free-form list (e.g., "Python, AWS, Postgres, Docker")
   - **Why it matters**: Familiarity reduces time to production, operational risk

2. **Senior Engineers**
   - "How many senior engineers (5+ years experience)?"
   - Options: 0-1, 2-5, 5-10, 10+
   - **Why it matters**: Seniors can learn new tech faster, handle complexity

3. **Platform/SRE Team**
   - "Do you have a dedicated SRE or platform engineering team?"
   - Options: No (devs handle ops), 1-2 people part-time, dedicated team (5+)
   - **Why it matters**: No SRE team = can't self-host complex systems reliably

4. **Learning Appetite**
   - "Your team's appetite for learning new technologies?"
   - Options: Low (must ship fast), moderate (if clear value), high (love learning)
   - **Why it matters**: Low appetite = stick to known tech, high = can adopt new tools

5. **Hiring Capacity**
   - "Can you hire for specialized skills?"
   - Options: No budget, hard to find (6+ months), can hire (1-3 months)
   - **Why it matters**: Can't hire = must use team's existing skills

---

### Section 4: Existing Investments

**What This Reveals**: Sunk costs, vendor relationships, technical debt, lock-in

**Questions to Ask**:

1. **Infrastructure Platform**
   - "What infrastructure do you already have?"
   - Options: AWS, Azure, GCP, on-premise, hybrid, greenfield (none)
   - **Why it matters**: Existing cloud = leverage existing services, contracts

2. **Vendor Contracts**
   - "Any existing vendor contracts or enterprise agreements?"
   - Format: List (e.g., "Oracle DB enterprise license $2M", "Microsoft EA", "AWS credits $100K")
   - **Why it matters**: Sunk costs create pressure to maximize existing investments

3. **Sunk Cost Magnitude**
   - "Significant sunk costs to consider?"
   - Examples: "$2M Oracle license paid through 2027", "Custom platform built over 3 years"
   - **Why it matters**: Sunk cost fallacy vs pragmatic reuse

4. **Technical Debt**
   - "How hard to change your existing stack?"
   - Options: Greenfield (no existing system), doable (some dependencies), locked in (too many deps)
   - **Why it matters**: High lock-in reduces options, increases migration risk

---

### Section 5: Operational Reality

**What This Reveals**: Who runs systems, availability requirements, support capacity

**Questions to Ask**:

1. **Who Runs Production**
   - "Who will operate this system in production?"
   - Options: No one yet, developers (on-call), 1-2 ops people, dedicated SRE team
   - **Why it matters**: No ops team = can't self-host reliably

2. **Availability Requirements**
   - "Is 24/7 availability required?"
   - Options: Yes (revenue-critical), business hours critical, downtime tolerable
   - **Why it matters**: 24/7 requires robust ops or managed service

3. **Current On-Call Setup**
   - "Current on-call setup?"
   - Options: No on-call, developers rotate, dedicated SRE
   - **Why it matters**: Developer on-call already stretched = don't add more systems to manage

4. **2am Failure Scenario**
   - "When system breaks at 2am Saturday, what happens?"
   - Options: Wake up developer, wait until Monday, call vendor support, SRE handles
   - **Why it matters**: Reveals realistic support capacity

---

### Section 6: Support Expectations

**What This Reveals**: Support model preference, SLA expectations, self-sufficiency

**Questions to Ask**:

1. **Support Model Preference**
   - "When system breaks, who should fix it?"
   - Options: We figure it out (community), basic vendor support, platinum 24/7 support
   - **Why it matters**: Community support = need in-house expertise

2. **Acceptable Downtime**
   - "Acceptable downtime for this system?"
   - Options: Zero tolerance (<5 min/month), minimal (<1 hour/month), can wait (<1 day)
   - **Why it matters**: Determines SLA tier needed

3. **Security Patch Urgency**
   - "How fast must critical security patches be applied?"
   - Options: Same day, within week, whenever convenient
   - **Why it matters**: Same day = need managed service or dedicated team

---

### Section 7: Compliance Constraints

**What This Reveals**: Regulatory mandates, audit requirements, data residency

**Questions to Ask**:

1. **Regulatory Requirements**
   - "Any regulatory compliance requirements?"
   - Options: SOX, HIPAA, PCI-DSS, GDPR, FedRAMP, none
   - **Why it matters**: Compliance may mandate specific controls, vendors, audit trails

2. **Data Residency**
   - "Data residency restrictions?"
   - Options: Must stay in US, must stay in EU, specific region, no restrictions
   - **Why it matters**: Limits vendor/region choices

3. **Audit Trail Requirements**
   - "Audit trail requirements?"
   - Options: Full audit (immutable logs), basic logging, none
   - **Why it matters**: Audit requirements may dictate technology features

4. **Cloud Services Allowed**
   - "Can you use public cloud services?"
   - Options: Yes (any cloud), only specific regions, no (on-premise only)
   - **Why it matters**: On-premise only = can't use managed cloud services

---

### Section 8: Risk Tolerance

**What This Reveals**: Innovation vs stability preference, past trauma, fears

**Questions to Ask**:

1. **Technology Preference**
   - "Your team's technology preference?"
   - Options: Bleeding edge (love new tech), moderate (proven but modern), boring (stick to old reliable)
   - **Why it matters**: Boring preference = PostgreSQL, bleeding edge = NewDB

2. **Build vs Buy Preference**
   - "Build vs buy preference?"
   - Options: Prefer build (control), prefer buy (speed), case-by-case
   - **Why it matters**: Cultural preference affects decision weighting

3. **Past Experiences**
   - "Past experiences with technology choices (good or bad)?"
   - Format: Free-form ("Kubernetes was a disaster", "Managed Kafka saved us")
   - **Why it matters**: Past trauma creates strong bias (avoid or embrace)

4. **Primary Fear**
   - "What keeps you up at night about this decision?"
   - Options: Downtime, runaway costs, security breach, complexity, vendor lock-in, team can't operate
   - **Why it matters**: Fear drives decision more than opportunity

---

### Section 9: Political Realities

**What This Reveals**: Decision authority, mandates, vendor relationships, resume-driven development

**Questions to Ask**:

1. **Decision Authority**
   - "Who has final say on technology decisions?"
   - Options: CTO (founder), engineering leads, team consensus, procurement/finance
   - **Why it matters**: Multiple stakeholders = need buy-in narrative

2. **Technology Mandates**
   - "Any mandates from leadership?"
   - Examples: "Must use AWS", "must use open source", "must use Microsoft stack"
   - **Why it matters**: Mandates are hard constraints

3. **Vendor Relationships**
   - "Existing vendor relationships or partnerships?"
   - Examples: "AWS partner (get discounts)", "Microsoft shop (EA)"
   - **Why it matters**: Partnerships create incentives, discounts, lock-in

4. **Resume-Driven Development**
   - "Team desire to learn specific technologies?"
   - Examples: "Team wants to learn Kubernetes", "Team wants React on resume"
   - **Why it matters**: Career goals vs business pragmatism

---

## Question Prioritization

Not all questions matter equally. Prioritize based on **decision impact**:

### Critical Questions (Always ask):
1. Team size (organization context)
2. Deadline (time constraints)
3. Who runs production (operational reality)
4. Budget (organization context)
5. Existing skills (team capabilities)

These 5 questions filter 80% of options.

### Important Questions (Ask for major decisions):
6. Platform/SRE team
7. Availability requirements
8. Compliance requirements
9. Primary fear
10. Technology mandates

### Optional Questions (Ask if needed for tiebreakers):
11. Learning appetite
12. Past experiences
13. Vendor relationships
14. All remaining questions

---

## How Questions Map to Decisions

### Question → Decision Impact Examples

```yaml
question: "Do you have SRE team?"
answer: "No"
impact:
  filters_out: ["Self-hosted Kubernetes", "Self-hosted Kafka cluster"]
  reason: "Can't operate complex distributed systems without SRE"

question: "Deadline?"
answer: "3 months (critical)"
impact:
  filters_out: ["Build custom"]
  prioritizes: ["Managed SaaS over self-hosted"]
  reason: "No time for 6-12 month build timeline"

question: "Team knows Kubernetes?"
answer: "0 engineers"
impact:
  filters_out: ["Any Kubernetes-based solution"]
  reason: "3+ month learning curve exceeds deadline"

question: "Budget?"
answer: "<$1M/year"
impact:
  filters_out: ["Enterprise SaaS with $500K minimum"]
  prioritizes: ["Open source or lower-tier SaaS"]
  reason: "Can't afford high-end enterprise solutions"

question: "Primary fear?"
answer: "Vendor lock-in"
impact:
  deprioritizes: ["AWS Lambda", "Proprietary SaaS"]
  prioritizes: ["Open source", "Portable solutions"]
  reason: "Fear drives decision more than optimization"
```

---

## Question Adaptation by Capability

The skill should adapt questions based on capability:

### Example: Data Catalog (Marketplace Archetype)

Generic question: "Who runs production?"
**Adapted**: "Who will operate the data catalog in production - run searches, handle metadata ingestion failures, manage schema changes?"

Generic question: "Availability requirements?"
**Adapted**: "If the catalog goes down, what's the impact? Can data engineers still work, or is everything blocked?"

### Example: Message Queue

Generic question: "Who runs production?"
**Adapted**: "Who will operate the message queue - handle broker failures, rebalance partitions, monitor consumer lag?"

Generic question: "Availability requirements?"
**Adapted**: "If the message queue goes down, what happens? Do all services stop, or can they degrade gracefully?"

---

## Output Format

After asking questions, structure answers as:

```yaml
constraint_profile:
  organization:
    team_size: 5
    budget: "$500K runway"
    stage: "pre-seed startup"
    age: "3 months"

  time_constraints:
    deadline: "3 months"
    consequence: "Miss quarterly OKR, frustrate stakeholders"
    competitive_pressure: "Moderate"

  team_capabilities:
    existing_skills: ["Python", "AWS", "Postgres", "Docker"]
    senior_engineers: 1
    platform_team: false
    learning_appetite: "Low (must ship fast)"
    hiring_capacity: "Cannot hire"

  existing_investments:
    infrastructure: "AWS (have $25K credits)"
    vendor_contracts: []
    sunk_costs: "None (greenfield)"
    technical_debt: "None"

  operational_reality:
    who_runs_production: "Developers on-call (no SRE)"
    availability_requirement: "Business hours critical"
    oncall_setup: "Informal (whoever's awake)"
    support_capacity: "Figure it out ourselves"

  compliance:
    regulations: []
    data_residency: "No restrictions"
    audit_requirements: "Basic logging"
    cloud_allowed: true

  risk_tolerance:
    tech_preference: "Boring, proven"
    build_vs_buy: "Prefer buy (focus on product)"
    past_trauma: "Founder's previous startup over-engineered infra, ran out of cash"
    primary_fear: "Complexity, premature optimization"

  political:
    decision_authority: "CTO (founder)"
    mandates: ["Focus on product, not infrastructure"]
    vendor_relationships: ["AWS (have credits)"]
    resume_driven: "None (team focused on shipping)"
```

---

## Example Question Flow

**Capability**: Data Catalog for Data Mesh

**Questions Generated by Skill**:

```
I need to understand your organizational constraints to recommend the right 
data catalog technology. I'll ask 15 questions across 5 categories.

CRITICAL QUESTIONS (Must answer):

Q1: How many engineers will work on the data mesh initiative?
    a) 1-10 (small team)
    b) 11-50 (mid-size)
    c) 50+ (large team)

Q2: When do you need the data catalog operational in production?
    a) ASAP (within weeks)
    b) 3 months
    c) 6-12 months
    d) No hard deadline

Q3: Who will operate the catalog in production (run searches, handle failures)?
    a) No one yet / developers will handle it
    b) 1-2 people can support part-time
    c) Dedicated SRE team

Q4: What's your annual engineering budget?
    a) <$1M (constrained)
    b) $1M-$10M (moderate)
    c) >$10M (well-funded)

Q5: What technologies does your team know well?
    (List: e.g., "Python, AWS, Postgres, Kubernetes, Docker")

[Based on answers, skill asks 5-10 more questions from Important/Optional categories]

IMPORTANT QUESTIONS (Asked selectively):

Q6: Do you have a dedicated platform/SRE team?
Q7: Is 24/7 availability required?
Q8: Any regulatory compliance requirements?
Q9: What's your primary fear about this decision?
Q10: Any mandates from leadership (must use AWS, etc.)?

[Skill captures all answers in structured format above]
```

---

**End of Discovery Questionnaire Framework**
