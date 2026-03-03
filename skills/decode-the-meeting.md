# SKILL: /decode-the-meeting

## Purpose

Extract structured requirements from unstructured meeting content (transcripts, minutes, emails, interviews). Transforms stakeholder conversations into actionable inputs for solution-mapper.

## The Problem

Most requirements don't arrive as clean specifications. They emerge from:
- **Steering committee meetings** ("We're missing 30% of our gate deadlines")
- **Discovery sessions** ("The offshore team spends hours on Control-F searches")
- **Email threads** ("We need to reduce manual verification time")
- **Stakeholder interviews** ("Our biggest pain point is the LexisNexis document review")

But **what people say ≠ what they mean**. Meetings have layers of subtext:
- **Surface**: "Gate tracking is manual and slow" (stated problem)
- **Blame**: "Because vendors don't update status" (pointing at others)
- **Fear**: "I might get blamed for delays I can't control" (never spoken)
- **Want**: "I want to LOOK in control and BE ABLE TO BLAME OTHERS" (true motivation)

**This skill decodes BOTH**:
1. **Explicit requirements** (what they say) → Structured YAML
2. **Subtext** (what they mean) → Political reality, true motivations

Understanding subtext is critical because:
- **Solutions must address real fears**, not just stated problems
- **Adoption depends on politics**, not just technical merit
- **What kills projects** is usually Layer 4 (fear), not Layer 1 (surface problem)

## Input Sources

### 1. Meeting Minutes
**Format**: Word docs, PDFs, emails with meeting notes
**Example**: Weekly SteerCo Minutes
**Contains**:
- Decisions made
- Issues raised
- Action items
- KPIs mentioned
- Stakeholder concerns

### 2. Discovery Session Transcripts
**Format**: Video transcripts (Zoom, Teams), audio recordings
**Example**: Broker verification kickoff call
**Contains**:
- Current process walkthrough
- Pain points ("manual Control-F searches")
- Desired outcomes ("reduce from 1 day to 2 hours")
- Integration points ("Salesforce, NMLS, LexisNexis")

### 3. Email Threads
**Format**: Email conversations, Slack threads
**Example**: AWS funding request email
**Contains**:
- Problem statements
- Proposed solutions
- Budget constraints
- Timeline expectations

### 4. Interviews
**Format**: 1-on-1 stakeholder conversations
**Example**: Back-office team lead interview
**Contains**:
- Day-in-the-life workflows
- Frustrations and bottlenecks
- Workarounds and hacks
- Ideal future state

## The 5 Layers of Meeting Subtext

**Critical insight**: Most meetings contain 5 layers - what people say is Layer 1 (surface), but Layers 4-5 drive actual decisions.

### Layer 1: Surface Problem (What They Say)

**Characteristics**:
- Explicitly stated problem
- Sounds rational, objective
- "Safe" to say in public meetings

**Keywords**: "manual", "slow", "inefficient", "error-prone", "time-consuming"

**Example** (NSO):
```
"Gate tracking is manual and slow. We're missing 30% of our deadlines."
```

**What to extract**: The stated problem (this goes in requirements.md)

**But don't stop here** - this is only the surface.

---

### Layer 2: Pointing at Others (Blame Deflection)

**Characteristics**:
- Immediately blames other people/teams
- "It's not MY fault, it's THEIR fault"
- Shifts responsibility away from self

**Keywords**: "because", "vendors don't", "team doesn't", "they never", "if only they would"

**Example**  (NSO):
```
"Because vendors don't update their status in the system"
"Because construction doesn't tell us when they're delayed"
"The offshore team doesn't follow the process correctly"
```

**What this reveals**:
- **Turf wars**: Construction vs. Tech vs. Vendors (siloed teams)
- **No accountability**: Everyone blames everyone else
- **Real problem**: Lack of visibility and shared accountability (not just "manual tracking")

**What to extract**:
```yaml
stakeholder_dynamics:
  - conflict: "Tech team vs. Vendors (status updates)"
  - conflict: "Tech team vs. Construction (delay notifications)"
  - blame_pattern: "Everyone deflects responsibility"
  - real_issue: "No shared accountability system"
```

---

### Layer 3: Upstream/Downstream Deflection (Systemic Blame)

**Characteristics**:
- Blames "the system" or "other departments"
- **Upstream blame**: "We get bad inputs" (planning, forecasting)
- **Downstream blame**: "They don't execute properly" (field teams, vendors)
- "I'm stuck in the middle" positioning

**Keywords**:
- Upstream: "planning", "unrealistic timelines", "we weren't consulted", "they don't understand reality"
- Downstream: "field teams", "don't follow process", "they don't care", "execution problems"

**Example** (NSO):
```
Upstream: "Planning gives us unrealistic timelines without consulting us"
Downstream: "Field teams don't follow the gate process we designed"
```

**What this reveals**:
- **Power dynamics**: Speaker feels powerless (caught between planning and execution)
- **Conway's Law**: Organizational structure is broken (silos, poor communication)
- **Real problem**: Lack of influence over upstream decisions AND downstream execution

**What to extract**:
```yaml
organizational_dynamics:
  - position: "Middle management (caught between planning and field)"
  - power_gap: "No control over upstream (planning) OR downstream (execution)"
  - conway_law_issue: "Siloed organization, poor communication across layers"
  - real_need: "Visibility and influence across organizational boundaries"
```

---

### Layer 4: The Real Fear (Never Spoken)

**THIS IS THE MOST IMPORTANT LAYER**

**Characteristics**:
- **Never explicitly stated** in meetings
- Emotional, personal
- Career risk, reputation risk
- "What keeps them up at night"

**What people fear** (but never say):
- "I might get blamed for delays I can't control"
- "I'll lose budget / headcount / credibility"
- "Executives will think I'm incompetent"
- "I'll be the scapegoat when this fails"
- "My team will be reorganized out of existence"

**How to detect** (indirect signals):

**Signal 1: Defensive language**
```
"We've been doing this for years, it's always worked"
→ Fear: Change will expose that old way was broken (and I approved it)
```

**Signal 2: Emphasis on NOT being blamed**
```
"We need clear documentation of who committed to what deadlines"
→ Fear: I'll be blamed when others miss deadlines
```

**Signal 3: Desire for "visibility" or "executive dashboards"**
```
"We need real-time visibility for leadership"
→ Fear: Executives don't trust me, I need to prove I'm in control
```

**Signal 4: Over-specification of audit trails**
```
"We need timestamped records of every vendor status update"
→ Fear: I'll need proof it wasn't my fault when things go wrong
```

**Signal 5: Resistance to automation that removes their visibility**
```
"We should keep manual approval for X, we can't fully automate it"
→ Fear: If automated, I lose control (and visibility to executives)
```

**Example** (NSO - reading between lines):

**What they say** (Layer 1):
```
"We need an executive dashboard showing gate completion status in real-time"
```

**What they fear** (Layer 4):
```
"Executives don't trust me to deliver 195 stores on time.
If I can't show them green/yellow/red status in real-time, they'll think I don't have control.
When stores miss gates (30% do), executives will blame ME, not the vendors or construction.
I need to be able to point to data showing 'I did my part, THEY failed.'"
```

**What to extract**:
```yaml
hidden_fears:
  - fear: "Blame for delays beyond control"
    evidence: "Emphasis on audit trails, timestamped commitments"

  - fear: "Loss of executive trust"
    evidence: "Requests for executive dashboards, real-time visibility"

  - fear: "Perceived loss of control"
    evidence: "Resistance to full automation, wants manual checkpoints"

  - fear: "Budget/headcount cuts if perceived as failing"
    evidence: "Defensive posture about current performance"
```

---

### Layer 5: What They Actually Want (Never Explicitly Stated)

**THIS IS THE TRUE MOTIVATION**

**Characteristics**:
- The **real** outcome they're seeking
- Often political/career-oriented, not purely operational
- **NOT** the same as Layer 1 (stated goal)

**What people actually want** (rarely stated):

**Want 1: LOOK in control** (to executives)
```
Stated: "We need better gate tracking"
Actually want: "I need executives to see that I'M in control, even when I'm not"
Solution needed: Executive dashboard (makes ME look good)
```

**Want 2: AVOID boring/manual work** (without admitting laziness)
```
Stated: "Manual tracking is error-prone and inefficient"
Actually want: "I hate doing this boring work, but can't say that"
Solution needed: Automation (frees me from tedious tasks)
```

**Want 3: BE ABLE TO BLAME OTHERS** (when things go wrong)
```
Stated: "We need clear accountability for vendor status updates"
Actually want: "When deadlines are missed, I need proof it wasn't my fault"
Solution needed: Audit trails, timestamped commitments (CYA - Cover Your Ass)
```

**Want 4: INCREASE power/influence** (empire building)
```
Stated: "We should centralize all gate tracking in our team"
Actually want: "My team controls critical process → more budget → more headcount → more power"
Solution needed: System that routes everything through their team
```

**Want 5: REDUCE career risk** (protect myself)
```
Stated: "We need to improve our on-time completion rate"
Actually want: "If we fail, I need to show I did everything right (not get fired)"
Solution needed: Metrics showing my team's performance vs. others' failures
```

**Example** (NSO - true motivations):

**What they say** (Layer 1):
```
"We need to automate gate tracking to improve our 95% on-time target"
```

**What they actually want** (Layer 5):
```
I want to:
1. LOOK in control → Executive dashboard showing I'm managing 195 stores
2. AVOID manual Excel work → Automation handles boring status updates
3. BE ABLE TO BLAME OTHERS → Audit trails show when vendors/construction failed
4. PROTECT my budget → Show executives I'm hitting targets (95% goal)
5. KEEP my team relevant → If I control the dashboard, my team stays important
```

**What to extract**:
```yaml
true_motivations:
  - want: "Executive visibility (look in control)"
    solution_implication: "Dashboard is CRITICAL (not just nice-to-have)"
    priority: "HIGH (career protection)"

  - want: "Eliminate boring manual work"
    solution_implication: "Automation must be seamless (no manual fallback)"
    priority: "MEDIUM (quality of life)"

  - want: "Blame deflection capability"
    solution_implication: "Audit trails, timestamped commitments, vendor accountability tracking"
    priority: "CRITICAL (CYA when failures happen)"

  - want: "Protect team's role/budget"
    solution_implication: "System routes through their team (maintains control)"
    priority: "HIGH (political survival)"
```

---

## How Layers Connect

**Layer progression** (what's happening psychologically):

```
Layer 1: Surface Problem
   ↓ (blame deflection)
Layer 2: Point at specific people/teams
   ↓ (systemic deflection)
Layer 3: Blame upstream/downstream (system is broken)
   ↓ (fear emerges)
Layer 4: "I'm scared of being blamed, losing budget, looking incompetent"
   ↓ (real motivation)
Layer 5: "I want to LOOK good, AVOID work, PROTECT myself, BLAME others"
```

**Why this matters**:

**If you only solve Layer 1** (surface problem):
- You build "better gate tracking"
- But it doesn't address Layer 4 fears (being blamed)
- Adoption fails because people sabotage it (it doesn't serve their interests)

**If you understand Layer 5** (true wants):
- You build "better gate tracking" (Layer 1)
- WITH executive dashboard (Layer 5: look in control)
- AND audit trails (Layer 5: blame others)
- AND automation (Layer 5: avoid boring work)
- → Adoption succeeds because it serves their true motivations

---

## Decoding Subtext: Practical Techniques

### Technique 1: Listen for Blame Words

**When you hear**:
- "Because they..."
- "If only they would..."
- "The problem is that [other team]..."

**You're at**: Layer 2 (pointing at others)

**Ask yourself**: Who are they blaming? Why? What's the turf war?

---

### Technique 2: Map Upstream/Downstream Complaints

**When you hear**:
- "We get unrealistic timelines from..." → Upstream blame
- "The field teams don't..." → Downstream blame

**You're at**: Layer 3 (systemic deflection)

**Ask yourself**: Is this person stuck in middle management? Do they feel powerless?

---

### Technique 3: Detect CYA Language

**When you hear**:
- "We need clear documentation of..."
- "Audit trails showing..."
- "Timestamped records of..."
- "Executive dashboard with..."

**You're seeing**: Layer 4 (fear of blame) → Layer 5 (want to deflect blame)

**Ask yourself**: What are they afraid of? What evidence do they want when things go wrong?

---

### Technique 4: Notice What's Defended

**When you hear**:
- "We've always done it this way..."
- "Manual approval is necessary because..."
- "We can't fully automate X..."

**You're seeing**: Layer 4 (fear of change exposing problems or losing control)

**Ask yourself**: What would they lose if this changed? Power? Visibility? Job security?

---

### Technique 5: Read Executive Dashboard Requests

**When you hear**:
- "Leadership needs real-time visibility"
- "Executives want a dashboard showing..."

**You're seeing**: Layer 4 (fear of losing executive trust) → Layer 5 (want to look in control)

**Ask yourself**: Do executives actually care, or is this person insecure about their credibility?

---

## Process

### Step 1: Read and Annotate (BOTH Explicit + Subtext)

**CRITICAL**: Extract TWO types of information:
1. **Explicit Requirements** (what they say) → Technical requirements
2. **Subtext** (what they mean) → Political reality, adoption blockers

---

#### Part A: Explicit Requirements

**What to look for**:

```yaml
pain_points:
  keywords: ["manual", "time-consuming", "slow", "error-prone", "frustrating", "bottleneck"]
  indicators:
    - Complaints about current process
    - Workarounds being used
    - Repetitive tasks mentioned
    - Time estimates ("takes 1 day", "hours of work")

  example: |
    "The team downloads all broker names from the workstream and manually checks
    each one against a high-risk party list using Control-F on multiple Excel tabs."

    → Pain Point: Manual, repetitive Excel searches (error-prone, time-consuming)

business_goals:
  keywords: ["reduce", "improve", "accelerate", "automate", "eliminate", "increase"]
  indicators:
    - Desired outcomes
    - Success criteria
    - ROI statements
    - Efficiency targets

  example: |
    "The end goal is to significantly reduce manual effort, eliminate repetitive tasks,
    improve accuracy, and accelerate broker onboarding and renewal decision cycles."

    → Goals: Reduce effort, eliminate tasks, improve accuracy, accelerate cycles

kpis_and_metrics:
  keywords: ["95%", "target", "SLA", "KPI", "metric", "%", "hours", "days"]
  indicators:
    - Quantitative targets
    - Performance metrics
    - Compliance requirements
    - Volume projections

  example: |
    "Construction Gates 1 & 2: 95% on-time completion for handoff to tech"
    "195 restaurants opening in 2025"

    → KPIs: 95% on-time, 195 stores/year

decisions_made:
  keywords: ["approved", "decided", "agreed", "selected", "chosen"]
  indicators:
    - Platform choices (AWS, Azure, etc.)
    - Budget approvals
    - Vendor selections
    - Timeline commitments

  example: |
    "AWS Funding - POC for Counterparty Risk"

    → Decision: AWS platform (funding approved)

current_process:
  keywords: ["currently", "today", "we use", "existing", "manual process"]
  indicators:
    - Step-by-step workflows
    - Tools mentioned (Salesforce, Excel, LexisNexis)
    - Team structure
    - Data sources

  example: |
    "1. New broker application submitted → Salesforce record created
     2. Offshore team retrieves Salesforce record
     3. Download broker names from workstream
     4. Manual check against high-risk list (Excel, Control-F)..."

    → Current: 10-step manual process, uses Salesforce/Excel/LexisNexis

integrations:
  keywords: ["integrate", "API", "connect to", "pull from", "sync with"]
  indicators:
    - External systems mentioned
    - Data sources
    - Third-party services
    - APIs or web services

  example: |
    "Integrate with Salesforce for automated data ingestion"
    "Perform NMLS verifications (via API or automated web lookup)"
    "Conduct LexisNexis report retrieval"

    → Integrations: Salesforce, NMLS, LexisNexis

constraints:
  keywords: ["budget", "timeline", "team", "compliance", "regulation", "must"]
  indicators:
    - Budget limits
    - Timeline deadlines
    - Compliance requirements
    - Team skill gaps
    - Platform requirements

  example: |
    "POC budget: <$5,000/month (AWS funding, 3 months)"
    "Financial services compliance (FINRA, CFPB)"
    "Team: Python/Node.js developers, AWS-focused"

    → Constraints: $5K/month POC budget, financial compliance, AWS platform, Python team

stakeholders:
  keywords: ["team", "department", "role", "owner", "approver"]
  indicators:
    - Who is involved
    - Approval chains
    - Responsibilities
    - Conway's Law implications

  example: |
    "Offshore back-office team member retrieves the Salesforce record"
    "All flagged findings are escalated to the headquarters business team"

    → Stakeholders: Offshore back-office (execution), HQ team (approval)
```

---

#### Part B: Subtext Detection (Political Reality)

**CRITICAL**: While annotating explicit requirements, simultaneously listen for subtext layers.

**Apply the 5-layer detection techniques**:

```yaml
layer_1_surface_problem:
  what_to_look_for: "Explicit problem statements (safe to say publicly)"
  keywords: ["manual", "slow", "inefficient", "error-prone"]

  example: |
    "Gate tracking is manual and slow. We're missing 30% of deadlines."

    → Surface Problem: Manual tracking, missed deadlines
    → BUT DON'T STOP HERE - look for deeper layers

layer_2_blame_deflection:
  what_to_look_for: "Immediately blames other people/teams"
  keywords: ["because", "vendors don't", "team doesn't", "they never", "if only they"]

  detection_technique: |
    Listen for "Because THEY..." statements immediately after stating a problem.

  example: |
    "Because vendors don't update their status"
    "Construction doesn't tell us when they're delayed"

    → Blame Pattern: Tech team blames Vendors + Construction
    → Reveals: Turf wars, siloed teams, no shared accountability

layer_3_systemic_deflection:
  what_to_look_for: "Blames upstream (inputs) or downstream (execution)"
  keywords:
    upstream: ["planning", "unrealistic", "we weren't consulted", "don't understand reality"]
    downstream: ["field teams", "don't follow process", "execution problems"]

  detection_technique: |
    Map speaker's position - are they stuck in the middle?

  example: |
    "Planning gives us unrealistic timelines" (upstream)
    "Field teams don't follow the gate process" (downstream)

    → Position: Middle management (powerless)
    → Reveals: Lack of influence over inputs AND outputs

layer_4_hidden_fears:
  what_to_look_for: "Indirect signals of fear (NEVER explicitly stated)"

  detection_signals:
    - signal: "Defensive language about current approach"
      keywords: ["we've always done it this way", "it's worked for years"]
      fear_revealed: "Change will expose that old way was broken (I approved it)"

    - signal: "Emphasis on audit trails / documentation"
      keywords: ["clear documentation of who committed", "timestamped records"]
      fear_revealed: "I'll be blamed when others fail"

    - signal: "Executive visibility requests"
      keywords: ["leadership needs visibility", "executive dashboard", "real-time status"]
      fear_revealed: "Executives don't trust me, I need to prove control"

    - signal: "Resistance to full automation"
      keywords: ["manual approval is necessary", "can't fully automate", "human oversight required"]
      fear_revealed: "If automated, I lose control/visibility"

    - signal: "Over-specification of accountability"
      keywords: ["need to know who's responsible", "clear ownership", "escalation paths"]
      fear_revealed: "I need someone to blame when this fails"

  example: |
    "We need an executive dashboard showing gate status in real-time"

    → Surface: Better visibility
    → Hidden Fear: "Executives don't trust me. If I can't show control, I'll be blamed."

layer_5_true_motivations:
  what_to_look_for: "What they ACTUALLY want (political/career goals)"

  motivation_patterns:
    - want: "LOOK in control (to executives)"
      indicators: ["executive dashboard", "real-time visibility", "leadership reporting"]
      solution_priority: "CRITICAL (career protection)"

    - want: "AVOID boring manual work"
      indicators: ["manual", "repetitive", "time-consuming", "we spend hours"]
      solution_priority: "MEDIUM (quality of life)"

    - want: "BE ABLE TO BLAME OTHERS"
      indicators: ["audit trail", "timestamped", "vendor commitments", "accountability"]
      solution_priority: "CRITICAL (CYA when failures happen)"

    - want: "INCREASE power/budget"
      indicators: ["centralize", "our team should own", "consolidate under us"]
      solution_priority: "HIGH (empire building)"

    - want: "PROTECT job/team"
      indicators: ["our team is critical to", "only we can", "automation can't replace"]
      solution_priority: "CRITICAL (survival)"

  example: |
    "We need automated gate tracking with an executive dashboard"

    → What they say: Better tracking
    → What they want:
      - LOOK in control (dashboard for executives)
      - AVOID Excel work (automation)
      - BLAME others (audit trails showing vendor/construction failures)
```

**Integration Strategy**:

When reading meeting content, annotate BOTH simultaneously:

```
Example passage:
"Gate tracking is manual and slow (Layer 1). Vendors don't update status (Layer 2).
We need an executive dashboard with real-time visibility (Layer 4 signal)."

Annotations:
[EXPLICIT] Pain: Manual tracking
[EXPLICIT] Goal: Automation
[SUBTEXT - L2] Blame: Vendors (turf war)
[SUBTEXT - L4] Fear: Loss of executive trust (needs to prove control)
[SUBTEXT - L5] Want: LOOK in control (dashboard is CRITICAL, not nice-to-have)
```

### Step 2: Extract Structured Requirements

**Template**:

```yaml
requirements_extraction:

  project_context:
    project_name: "Counterparty Risk - Broker Verification Automation"
    client: "PennyMac Financial Services"
    department: "Production Back Office - Counterparty Risk"
    date: "2026-02-28"
    source: "AWS Funding Request Email"

  business_problem:
    summary: "Manual 10-step broker verification process taking 1 day per broker"

    pain_points:
      - id: PP-001
        description: "Manual Excel Control-F searches across multiple tabs"
        impact: "Time-consuming, error-prone, thousands of entries to check"
        stakeholder: "Offshore back-office team"

      - id: PP-002
        description: "Manual LexisNexis PDF review (line-by-line)"
        impact: "Slow, requires specialized knowledge, inconsistent"
        stakeholder: "Back-office analysts"

      - id: PP-003
        description: "High volume of broker applications (new + renewals)"
        impact: "Team overwhelmed, process doesn't scale"
        stakeholder: "Counterparty Risk team lead"

  current_state:
    process_name: "Broker Verification (Manual)"
    process_steps:
      - step: 1
        description: "Salesforce record creation"
        type: "automated"
        tool: "Salesforce"

      - step: 2
        description: "Offshore team retrieves record"
        type: "manual"
        pain_point: PP-001

      - step: 3
        description: "Download broker names from workstream"
        type: "manual"
        tool: "Salesforce export"

      - step: 4
        description: "Check against high-risk party list"
        type: "manual"
        tool: "Excel (Control-F searches)"
        pain_point: PP-001

      - step: 5
        description: "NMLS ID verification"
        type: "manual"
        tool: "NMLS website"

      - step: 6
        description: "LexisNexis background check (if high-risk)"
        type: "manual"
        tool: "LexisNexis API"

      - step: 7
        description: "Download multi-page LexisNexis reports"
        type: "manual"
        tool: "LexisNexis portal"

      - step: 8
        description: "Manual document review (line-by-line)"
        type: "manual"
        pain_point: PP-002

      - step: 9
        description: "Flag negative findings"
        type: "manual"
        pain_point: PP-002

      - step: 10
        description: "Escalate to HQ team for decision"
        type: "manual (email/ticket)"
        stakeholder: "HQ business team"

    total_time: "1 day per broker (8 hours)"
    team_size: "Offshore back-office team (size unknown)"
    volume: "~1,000 brokers/year (estimated)"

  business_goals:
    - goal: "Reduce manual effort"
      target: "80% reduction (10 steps → 2 steps)"
      measurement: "Time per broker: 1 day → <2 hours"

    - goal: "Eliminate repetitive tasks"
      target: "Automate Excel searches, PDF reviews"
      measurement: "Zero manual Control-F, zero manual PDF line-reading"

    - goal: "Improve accuracy"
      target: ">90% AI accuracy (matches human judgment)"
      measurement: "AI findings vs. human review comparison"

    - goal: "Accelerate decision cycles"
      target: "Broker onboarding/renewal <2 hours (vs. 1 day)"
      measurement: "Workflow execution time"

  proposed_solution:
    approach: "AI-driven agentic automation for Level-1 back-office workflow"

    automation_scope:
      - "Salesforce integration (automated data ingestion)"
      - "High-risk list name matching (automated)"
      - "NMLS verification (API or web automation)"
      - "LexisNexis report retrieval (automated)"
      - "AI document analysis (PDF parsing, risk extraction)"
      - "Risk scoring and flagging (automated)"
      - "HQ review (human-in-the-loop approval, not automated)"

    technologies_mentioned:
      - "AI-driven agentic automation"
      - "Salesforce integration"
      - "NMLS API or automated web lookup"
      - "LexisNexis API"
      - "Document analysis (AI)"

  integrations:
    - system: "Salesforce"
      purpose: "Broker application source of truth"
      integration_type: "Read (data ingestion)"
      data: "Broker applications, names, metadata"

    - system: "NMLS (National Mortgage Licensing System)"
      purpose: "Identity verification"
      integration_type: "API or web scraping"
      data: "NMLS ID validation, broker status"

    - system: "LexisNexis"
      purpose: "Background checks (litigation, criminal, regulatory)"
      integration_type: "API (report retrieval)"
      data: "Multi-page PDF reports"

    - system: "High-Risk Party List"
      purpose: "Compliance screening"
      integration_type: "File import (Excel)"
      data: "Spreadsheet with multiple tabs, thousands of entries"

  non_functional_requirements:

    performance:
      - metric: "Workflow execution time"
        target: "<2 hours per broker"
        source: "Business goal (10x faster than 1-day manual)"

      - metric: "API response time"
        target: "<500ms (p95)"
        source: "User expectation (responsive dashboard)"

      - metric: "Document analysis latency"
        target: "<30 seconds per LexisNexis report"
        source: "Workflow step SLA"

    accuracy:
      - metric: "Risk detection recall"
        target: ">95%"
        rationale: "Must catch all actual risks (regulatory compliance)"

      - metric: "False positive rate"
        target: "<20%"
        rationale: "Balance automation with human oversight"

      - metric: "NMLS verification accuracy"
        target: "100%"
        rationale: "Identity verification is critical (compliance)"

    scalability:
      - metric: "Broker applications per year"
        current: "~1,000"
        target: "5,000 (5x growth headroom)"

      - metric: "Concurrent workflows"
        target: "100 in-flight simultaneously"

    cost:
      - constraint: "POC budget"
        target: "<$5,000/month"
        duration: "3 months (AWS funding)"
        source: "AWS Funding approval"

      - constraint: "Production budget (estimated)"
        target: "<$15,000/month"
        rationale: "ROI: save 1 FTE ($8K/month) + improve accuracy"

    data_retention:
      - data_type: "Broker verification records"
        retention: "7 years"
        format: "Open format (SQL + S3)"
        rationale: "Financial services compliance (FINRA, CFPB)"

      - data_type: "LexisNexis PDFs"
        retention: "7 years (immutable)"
        format: "S3 with versioning, Glacier archival"
        rationale: "Legal discovery, audit trail"

    security:
      - requirement: "Authentication"
        target: "Corporate SSO (Azure AD or Okta)"

      - requirement: "Data encryption"
        target: "S3 encryption at rest, TLS in transit"
        rationale: "PII protection (broker names, NMLS IDs)"

      - requirement: "Secrets management"
        target: "AWS Secrets Manager for API keys"
        rationale: "LexisNexis, NMLS, Salesforce credentials"

    compliance:
      - requirement: "Financial services regulations"
        regulators: ["FINRA", "CFPB", "State licensing boards"]
        mandates: ["NMLS verification mandatory", "Background checks for high-risk", "7-year retention"]

  constraints:
    platform:
      constraint: "AWS (required)"
      rationale: "AWS funding approved for POC"

    budget:
      constraint: "<$5,000/month POC, <$15,000/month production"
      rationale: "AWS funding limit, ROI calculation"

    timeline:
      constraint: "3-month POC"
      rationale: "Prove value before production investment"

    team_skills:
      current: "Python/Node.js developers, AWS-focused"
      gap: "No specialized ML engineers (use managed AI services)"
      rationale: "Team can build on AWS, but can't train custom ML models"

    compliance:
      constraint: "Financial services compliance (FINRA, CFPB, 7-year retention)"
      impact: "Architecture must support audit trail, data retention, PII protection"

  stakeholders:
    - role: "Counterparty Risk Team"
      department: "Production Back Office"
      responsibility: "Broker verification (current manual process)"
      pain_points: [PP-001, PP-002, PP-003]

    - role: "Offshore Back-Office Team"
      responsibility: "Execute verification steps (retrieval, checks, reviews)"
      impacted_by: "Automation (workflow changes)"

    - role: "HQ Business Team"
      responsibility: "Final approval decision on flagged brokers"
      involvement: "Human-in-the-loop (not automated)"

    - role: "IT/Automation Team"
      responsibility: "Build and maintain POC/production system"
      skills: "Python, Node.js, AWS"

  success_criteria:
    - criterion: "80% reduction in manual effort"
      measurement: "10 steps → 2 steps (upload application, review AI summary)"

    - criterion: ">90% AI accuracy"
      measurement: "AI findings match human review >90% of time"

    - criterion: "<2 hour workflow execution time"
      measurement: "End-to-end time from Salesforce trigger to final approval"

    - criterion: "Zero security/compliance violations"
      measurement: "Audit findings, regulatory review"

    - criterion: "Within POC budget"
      measurement: "<$5,000/month actual spend"

  keywords_for_solution_mapper:
    archetype_indicators:
      - "multi-step process" (10 steps) → Workflow Orchestrator
      - "approval" (HQ team decision) → Workflow Orchestrator
      - "workflow" (broker onboarding/renewal) → Workflow Orchestrator
      - "human-in-the-loop" (HQ review required) → Workflow Orchestrator
      - "integrate" (Salesforce, NMLS, LexisNexis) → Integration Platform
      - "AI document analysis" → Analytics Platform (AI)
      - "broker registry" → System of Record

  # NEW: Subtext Analysis (Political Reality)
  subtext_analysis:

    layer_1_surface_problem:
      stated_problem: "Manual gate tracking, missing 30% of deadlines"
      source: "Explicit statements in meeting"

    layer_2_blame_deflection:
      blame_patterns:
        - blamed_party: "Vendors"
          quote: "Vendors don't update their status"
          turf_war: "Tech team vs. Vendors (status updates)"

        - blamed_party: "Construction"
          quote: "Construction doesn't tell us when delayed"
          turf_war: "Tech team vs. Construction (delay notifications)"

      real_issue: "No shared accountability system (everyone blames everyone)"

    layer_3_systemic_deflection:
      upstream_blame:
        - quote: "Planning gives unrealistic timelines"
          reveals: "No control over inputs (planning doesn't consult tech)"

      downstream_blame:
        - quote: "Field teams don't follow the gate process"
          reveals: "No control over execution (field doesn't comply)"

      position: "Middle management (powerless - caught between planning and field)"
      conway_law_issue: "Siloed organization, poor cross-layer communication"

    layer_4_hidden_fears:
      fears_detected:
        - fear: "Blame for delays beyond my control"
          evidence: "Emphasis on audit trails, timestamped vendor commitments"
          impact_on_solution: "Audit trail is CRITICAL (not optional)"

        - fear: "Loss of executive trust"
          evidence: "Requests for 'executive dashboard', 'real-time visibility'"
          impact_on_solution: "Dashboard is career protection (HIGH priority)"

        - fear: "Perceived loss of control"
          evidence: "Resistance to full automation, wants manual checkpoints"
          impact_on_solution: "Include human-in-the-loop touchpoints (don't black-box everything)"

        - fear: "Budget/headcount cuts if seen as failing"
          evidence: "Defensive language about current performance"
          impact_on_solution: "Solution must show THEIR team's value (not replace them)"

    layer_5_true_motivations:
      what_they_actually_want:

        - want: "LOOK in control (to executives)"
          indicators:
            - "Executive dashboard"
            - "Real-time visibility for leadership"
          solution_implication: "Dashboard is CRITICAL (not nice-to-have)"
          priority: "CRITICAL (career protection)"
          design_requirement: "Executive-facing UI showing green/yellow/red status, drill-down by store"

        - want: "AVOID boring manual work"
          indicators:
            - "Manual tracking is time-consuming"
            - "Spend hours on Excel updates"
          solution_implication: "Automation must be seamless (eliminate manual data entry)"
          priority: "MEDIUM (quality of life)"
          design_requirement: "Automated status ingestion from vendors/construction"

        - want: "BE ABLE TO BLAME OTHERS (when failures happen)"
          indicators:
            - "Need clear accountability"
            - "Timestamped vendor commitments"
            - "Audit trail"
          solution_implication: "CYA capabilities are CRITICAL"
          priority: "CRITICAL (blame deflection)"
          design_requirement: "Immutable audit log showing: who committed what, when, did they deliver?"

        - want: "PROTECT my team's relevance"
          indicators:
            - "Our team manages the process"
            - "We coordinate between vendors and construction"
          solution_implication: "Solution routes through their team (maintains control)"
          priority: "HIGH (political survival)"
          design_requirement: "Workflow includes approval steps owned by tech team"

        - want: "REDUCE career risk"
          indicators:
            - "Need to hit 95% on-time target"
            - "195 stores is a lot of visibility"
          solution_implication: "Metrics showing THEIR performance vs. OTHERS' failures"
          priority: "CRITICAL (job security)"
          design_requirement: "Reporting separates tech performance from vendor/construction failures"

    adoption_risk_assessment:
      critical_for_adoption:
        - requirement: "Executive dashboard (Layer 5: look in control)"
          risk_if_missing: "HIGH - Stakeholder will resist/sabotage (doesn't serve their interests)"

        - requirement: "Audit trail / blame deflection (Layer 5: CYA)"
          risk_if_missing: "CRITICAL - Stakeholder will block (need protection when failures happen)"

        - requirement: "Keep tech team in approval loop (Layer 5: protect relevance)"
          risk_if_missing: "HIGH - Stakeholder fears being automated away"

      moderate_for_adoption:
        - requirement: "Automation (Layer 5: avoid work)"
          risk_if_missing: "MEDIUM - They'll be annoyed but won't block project"

      what_will_kill_this_project:
        - scenario: "Build automation without dashboard"
          outcome: "Stakeholder feels exposed (no way to show executives they're in control) → sabotage"

        - scenario: "Build automation without audit trails"
          outcome: "Stakeholder fears blame when stores miss gates → blocks deployment"

        - scenario: "Full automation (no human checkpoints)"
          outcome: "Stakeholder fears irrelevance → political resistance"

    solution_design_implications:
      must_haves:
        - "Executive dashboard (green/yellow/red status, drill-down)"
        - "Audit trail (who committed what, when, did they deliver)"
        - "Tech team approval touchpoints (maintain their role)"
        - "Reporting separating tech vs. vendor/construction performance"

      architecture_requirements:
        - "Immutable event log (audit trail)"
        - "Executive UI (separate from operational UI)"
        - "Workflow orchestrator (human-in-the-loop for tech team)"
        - "Metrics separated by responsible party (tech vs. vendors vs. construction)"

      political_success_factors:
        - "Make tech team LOOK good (dashboard shows their performance)"
        - "Make tech team FEEL safe (audit trail protects them from blame)"
        - "Keep tech team RELEVANT (workflow routes through them)"
```

### Step 3: Output to Solution-Mapper

**File**: `requirements.md` (formatted for human review + solution-mapper input)

**Contents**:
1. **Project Context**: What, who, when, why
2. **Business Problem**: Pain points with impact
3. **Current State**: As-is process (step-by-step)
4. **Business Goals**: Desired outcomes with targets
5. **Proposed Solution**: High-level approach
6. **Integrations**: External systems and data sources
7. **NFRs**: Performance, accuracy, scalability, cost, security, compliance
8. **Constraints**: Platform, budget, timeline, skills, compliance
9. **Stakeholders**: Who's involved, responsibilities, pain points
10. **Success Criteria**: How to measure success
11. **Keywords**: For archetype matching

---

## Complete Example:  NSO Gate Tracking

### Input: SteerCo Meeting Minutes (Excerpt)

**Raw Meeting Content**:
```
RSO Weekly Steering Committee - 2026-02-15

ATTENDEES:
- Tech Lead (Sarah)
- Construction Manager (Mike)
- Vendor Coordinator (Jim)
- Planning Director (Lisa)

DISCUSSION:

Sarah (Tech Lead): "We're missing 30% of our Construction Gate 1 deadlines. Gate tracking
is completely manual - we have an Excel spreadsheet that gets updated maybe once a week.
By the time we see a delay, it's too late to recover.

The biggest issue is that vendors don't update their status in the system. Construction
doesn't notify us when they hit delays on their end. We're constantly chasing people for
information.

And honestly, Planning gives us these timelines that are completely unrealistic. They don't
understand what it actually takes to get 195 stores online. Then the field teams don't
follow the gate process we designed - they just skip steps when they're behind schedule.

What we need is real-time visibility for leadership. The executives are asking me every
week for status updates, and I'm scrambling to pull together reports from five different
sources. We need an executive dashboard that shows green/yellow/red status for every store.

I also think we need better documentation of who committed to what deadlines. When a store
misses its opening date, we need to be able to show exactly where the breakdown happened -
was it construction? Was it vendors? We need timestamped records so we can trace accountability.

Look, I'm not saying automation will solve everything. We still need manual approval for
critical gates - you can't just black-box this process. But we could definitely automate
the data collection and status aggregation."

ACTION ITEMS:
- Investigate automated gate tracking solution
- Include executive dashboard requirement
- Ensure audit trail for vendor/construction commitments
```

---

### Step 1: Dual Annotation (Explicit + Subtext)

#### Explicit Requirements (What Sarah Said)

```yaml
[EXPLICIT] Pain Point: "Gate tracking is manual Excel, updated weekly"
[EXPLICIT] Impact: "By the time we see delay, too late to recover"
[EXPLICIT] Goal: "Real-time visibility"
[EXPLICIT] KPI: "Missing 30% of Construction Gate 1 deadlines"
[EXPLICIT] Target: "95% on-time (inferred from KPI doc)"
[EXPLICIT] Integration: "Five different data sources (construction, vendors, etc.)"
[EXPLICIT] Requirement: "Executive dashboard (green/yellow/red status)"
[EXPLICIT] Requirement: "Audit trail (timestamped commitments)"
[EXPLICIT] Constraint: "Manual approval required for critical gates"
```

#### Subtext Analysis (What Sarah Means)

```yaml
[SUBTEXT - L1] Surface: "Gate tracking is manual and slow"

[SUBTEXT - L2] Blame Deflection:
  - "Vendors don't update their status" → Blames vendors
  - "Construction doesn't notify us" → Blames construction
  - Turf War: Tech vs. Vendors vs. Construction (siloed, no accountability)

[SUBTEXT - L3] Systemic Deflection:
  - Upstream: "Planning gives unrealistic timelines, don't understand reality"
  - Downstream: "Field teams don't follow the gate process, skip steps"
  - Position: Middle management (powerless - no control over inputs OR outputs)
  - Conway's Law: Siloed organization (planning → tech → field, no feedback loops)

[SUBTEXT - L4] Hidden Fears (Signals):
  - "Executives asking me every week for status" → Fear: Loss of executive trust
  - "Need to show where breakdown happened" → Fear: Being blamed for others' failures
  - "We need manual approval, can't black-box" → Fear: Loss of control/visibility
  - "Scrambling to pull reports from five sources" → Fear: Looking incompetent/disorganized

[SUBTEXT - L5] True Motivations:
  - WANT: "LOOK in control" → Evidence: "Real-time visibility for leadership", "executive dashboard"
  - WANT: "AVOID boring work" → Evidence: "Automate data collection, status aggregation"
  - WANT: "BE ABLE TO BLAME OTHERS" → Evidence: "Timestamped records", "show where breakdown happened"
  - WANT: "PROTECT my team's role" → Evidence: "We still need manual approval" (keep team relevant)
```

---

### Step 2: Structured Requirements with Subtext

```yaml
requirements_extraction:

  project_context:
    project_name: "ald's RSO - Automated Gate Tracking"
    client: "ald's"
    stakeholder: "Tech Lead (Sarah)"
    date: "2026-02-15"
    source: "Weekly SteerCo Meeting Minutes"

  business_problem:
    summary: "Missing 30% of Construction Gate 1 deadlines due to manual Excel tracking"

    pain_points:
      - id: PP-001
        description: "Manual Excel tracking, updated weekly (stale data)"
        impact: "Delays discovered too late to recover"
        stakeholder: "Tech Lead"

      - id: PP-002
        description: "Vendors don't update status, construction doesn't notify delays"
        impact: "Constant chasing for information (reactive, not proactive)"
        stakeholder: "Tech Lead, Vendor Coordinator, Construction Manager"

      - id: PP-003
        description: "Scrambling to pull reports from 5 sources for exec updates"
        impact: "Time-consuming, error-prone, makes tech team look disorganized"
        stakeholder: "Tech Lead (to executives)"

  business_goals:
    - goal: "Achieve 95% on-time gate completion"
      current: "70% (missing 30%)"
      target: "95%"
      measurement: "Gate completion status (Construction Gate 1)"

    - goal: "Real-time visibility for leadership"
      current: "Weekly Excel updates"
      target: "Live dashboard (green/yellow/red by store)"
      measurement: "Executive can see status without asking tech lead"

    - goal: "Reduce manual data collection effort"
      current: "Weekly manual updates from 5 sources"
      target: "Automated data ingestion"
      measurement: "Time spent on status updates (hours/week)"

  # NEW: Subtext Analysis
  subtext_analysis:

    layer_2_blame_deflection:
      blame_patterns:
        - blamed_party: "Vendors"
          quote: "Vendors don't update their status"
          turf_war: "Tech vs. Vendors (information flow)"

        - blamed_party: "Construction"
          quote: "Construction doesn't notify us when they hit delays"
          turf_war: "Tech vs. Construction (delay notifications)"

      real_issue: "No shared accountability system - everyone operates in silos"

    layer_3_systemic_deflection:
      upstream_blame:
        - quote: "Planning gives unrealistic timelines, don't understand reality"
          reveals: "No control over inputs (planning doesn't consult tech)"

      downstream_blame:
        - quote: "Field teams don't follow the gate process, skip steps"
          reveals: "No control over execution (field ignores tech's process)"

      position: "Middle management (Sarah feels powerless)"
      conway_law_issue: "Siloed organization - planning → tech → field (no feedback)"

    layer_4_hidden_fears:
      fears_detected:
        - fear: "Loss of executive trust"
          evidence: "Executives asking me every week for status, I'm scrambling"
          impact_on_solution: "Executive dashboard is CRITICAL (career protection)"

        - fear: "Blame for delays caused by others"
          evidence: "Need to show where breakdown happened - construction? vendors?"
          impact_on_solution: "Audit trail showing who failed is CRITICAL (CYA)"

        - fear: "Perceived loss of control"
          evidence: "Can't black-box this, we need manual approval for critical gates"
          impact_on_solution: "Include human-in-the-loop checkpoints (don't automate away tech team)"

        - fear: "Looking incompetent to executives"
          evidence: "Scrambling to pull reports from 5 sources"
          impact_on_solution: "Automated aggregation (make Sarah look organized/in-control)"

    layer_5_true_motivations:
      what_they_actually_want:

        - want: "LOOK in control (to executives)"
          indicators:
            - "Real-time visibility for leadership"
            - "Executive dashboard"
            - "Green/yellow/red status for every store"
          solution_implication: "Executive-facing UI is CRITICAL (not nice-to-have)"
          priority: "CRITICAL (Sarah's career depends on executive confidence)"
          design_requirement: "Dashboard accessible to executives WITHOUT asking Sarah"

        - want: "AVOID boring manual work"
          indicators:
            - "Automate data collection"
            - "Status aggregation"
            - "Scrambling to pull reports"
          solution_implication: "Automated ingestion from all 5 sources"
          priority: "MEDIUM (quality of life)"
          design_requirement: "Integrations with construction, vendors, field systems"

        - want: "BE ABLE TO BLAME OTHERS (when stores miss gates)"
          indicators:
            - "Documentation of who committed to what deadlines"
            - "Timestamped records"
            - "Show where breakdown happened"
          solution_implication: "Audit trail is CRITICAL (protect Sarah from blame)"
          priority: "CRITICAL (CYA when 30% of stores miss gates)"
          design_requirement: "Immutable log: vendor committed X by date Y, delivered Z (late)"

        - want: "PROTECT tech team's relevance"
          indicators:
            - "Can't black-box this"
            - "We still need manual approval for critical gates"
          solution_implication: "Keep tech team in workflow (human-in-the-loop)"
          priority: "HIGH (political survival - don't automate away Sarah's team)"
          design_requirement: "Approval workflow routed through tech team"

    adoption_risk_assessment:
      critical_for_adoption:
        - requirement: "Executive dashboard"
          risk_if_missing: "CRITICAL - Sarah will resist (doesn't address her fear of losing executive trust)"

        - requirement: "Audit trail (blame deflection)"
          risk_if_missing: "CRITICAL - Sarah will block deployment (needs CYA when stores fail)"

        - requirement: "Tech team approval touchpoints"
          risk_if_missing: "HIGH - Sarah fears being automated away (political resistance)"

      what_will_kill_this_project:
        - scenario: "Build automation without executive dashboard"
          outcome: "Sarah still scrambling to answer exec questions → she sabotages"

        - scenario: "Build automation without audit trail"
          outcome: "When stores miss gates, Sarah gets blamed → she blocks deployment"

        - scenario: "Full automation (no human checkpoints for tech team)"
          outcome: "Sarah's team becomes irrelevant → political resistance"

    solution_design_implications:
      must_haves_for_political_success:
        - "Executive dashboard (separate from operational UI - execs see without asking Sarah)"
        - "Audit trail (immutable log showing: vendor/construction committed X, delivered Y)"
        - "Tech team approval workflow (Sarah's team stays relevant)"
        - "Reporting separating tech vs. vendor vs. construction performance (Sarah looks good)"

      architecture_requirements:
        - "Event sourcing (immutable audit log for CYA)"
        - "Executive UI (read-only dashboard for leadership)"
        - "Workflow orchestrator (human-in-the-loop for tech approvals)"
        - "Multi-source integration (construction, vendors, field, planning)"
        - "Metrics dashboard (separates performance by responsible party)"

      political_success_factors:
        - "Make Sarah LOOK good to executives (dashboard shows she's in control)"
        - "Make Sarah FEEL safe (audit trail protects her from blame)"
        - "Keep Sarah's team RELEVANT (approval workflow routes through them)"
        - "Free Sarah from boring work (automated data collection)"

  # Explicit requirements continue...
  integrations:
    - system: "Construction tracking system"
      purpose: "Gate completion status"
      integration_type: "Read (automated polling or webhook)"

    - system: "Vendor status system"
      purpose: "Vendor STP certification, installation status"
      integration_type: "Read (automated API calls)"

    - system: "Field execution system"
      purpose: "On-site validation, go-live readiness"
      integration_type: "Read (data ingestion)"

    - system: "Planning system"
      purpose: "Store opening timeline, gate deadlines"
      integration_type: "Read (deadline reference data)"

    - system: "Executive reporting system"
      purpose: "Dashboard for leadership"
      integration_type: "Write (push aggregated status)"

  keywords_for_solution_mapper:
    archetype_indicators:
      - "multi-step process" (gate workflow) → Workflow Orchestrator
      - "approval" (manual approval for critical gates) → Workflow Orchestrator
      - "dashboard" (executive visibility) → Portal
      - "audit trail" (timestamped commitments) → System of Record (event log)
      - "integrate 5 sources" → Integration Platform
      - "real-time" → Event-Driven Platform
```

---

### Step 3: Key Insights (Why Subtext Matters)

**If you ONLY extract explicit requirements** (what Sarah said):
```
Requirement: "Automate gate tracking with executive dashboard"

Solution: Build dashboard + automation
```

**Result**: Project fails because...
- Sarah resists deployment (doesn't address her fear of being blamed)
- No audit trail → Sarah gets blamed when stores fail → she blocks system
- Full automation → Sarah's team irrelevant → political sabotage

---

**If you ALSO extract subtext** (what Sarah means):
```
Layer 4 Fear: "I'm terrified of being blamed for delays I can't control"
Layer 5 Want: "I need to LOOK in control AND BLAME others when things fail"

Solution: Build dashboard + automation + CRITICAL additions:
  - Immutable audit trail (who committed what, when did they fail)
  - Reporting separating tech vs. vendor vs. construction performance
  - Tech team approval workflow (keeps Sarah's team relevant)
```

**Result**: Project succeeds because...
- Sarah LOOKS good (executives see she's in control via dashboard)
- Sarah FEELS safe (audit trail protects her when stores fail)
- Sarah's team stays RELEVANT (workflow routes through them)
- Sarah ADOPTS the system (it serves her political needs, not just operational needs)

---

## Templates

### Template 1: Meeting Minutes

**Input**: ald's RSO Weekly SteerCo Minutes

**Extract**:
```yaml
decisions:
  - "Approved gate process change: 4 gates instead of 5"
  - "Selected Microsoft Fabric for data platform"

issues_raised:
  - "30% of stores missing Construction Gate 1 deadlines"
  - "Vendor STP certification tracking is manual (Excel)"

kpis_mentioned:
  - "95% on-time gate completion target"
  - "195 stores opening in 2025"

action_items:
  - "Implement automated gate status tracking"
  - "Integrate with vendor STP certification system"
```

### Template 2: Discovery Session Transcript

**Input**: Zoom call transcript

**Extract**:
```yaml
pain_points:
  - speaker: "Back-office team lead"
    quote: "We spend hours doing Control-F searches in Excel"
    pain_point: "Manual, repetitive Excel searches"
    impact: "Time-consuming, error-prone"

current_process:
  - speaker: "Analyst"
    quote: "I download the Salesforce report, then open the risk list spreadsheet..."
    process_step: "Manual export from Salesforce → Excel lookup"

desired_state:
  - speaker: "Manager"
    quote: "Ideally, the system would automatically flag high-risk brokers"
    goal: "Automated risk detection"
```

### Template 3: Email Thread

**Input**: AWS funding request

**Extract**:
```yaml
budget_constraints:
  - "POC budget: <$5,000/month (AWS funding)"

platform_decisions:
  - "AWS (funding approved)"

timeline:
  - "3-month POC"
```

---

## Output Format

**File**: `requirements.md` (Markdown + YAML)

**Sections**:

### Part A: Explicit Requirements (Technical)
1. Project Context
2. Business Problem
3. Current Manual Process (As-Is)
4. Proposed Solution (To-Be)
5. Business Goals
6. Non-Functional Requirements
7. Key Integrations
8. Success Criteria
9. Compliance Context

### Part B: Subtext Analysis (Political Reality)
10. **Layer 2**: Blame Deflection Patterns (turf wars, siloed teams)
11. **Layer 3**: Systemic Deflection (upstream/downstream, power dynamics)
12. **Layer 4**: Hidden Fears (what they're afraid of but not saying)
13. **Layer 5**: True Motivations (what they actually want)
14. **Adoption Risk Assessment**: What will kill this project if missing
15. **Solution Design Implications**: Must-haves for political success

### Critical Output

**Two-column format**:
```
┌─────────────────────────────┬────────────────────────────────┐
│ EXPLICIT REQUIREMENTS       │ SUBTEXT ANALYSIS               │
│ (What they said)            │ (What they mean)               │
├─────────────────────────────┼────────────────────────────────┤
│ "Automate gate tracking"    │ L4 Fear: Blame for delays      │
│                             │ L5 Want: LOOK in control       │
│                             │ → Executive dashboard CRITICAL │
├─────────────────────────────┼────────────────────────────────┤
│ "Need audit trail"          │ L5 Want: BLAME others          │
│                             │ → Immutable log CRITICAL       │
├─────────────────────────────┼────────────────────────────────┤
│ "Manual approval needed"    │ L4 Fear: Team irrelevance      │
│                             │ L5 Want: PROTECT role          │
│                             │ → Human-in-the-loop CRITICAL   │
└─────────────────────────────┴────────────────────────────────┘
```

**Why this format**: Forces architect to connect explicit requirements to political motivations, ensuring both are addressed.

**See**: `examples/nso-gate-tracking/requirements.md` for complete example

---

## Validation Checklist

Before handing to solution-mapper, ensure:

### Explicit Requirements (Technical)

✅ **Business problem clearly stated** (what pain points exist?)
✅ **Current process documented** (as-is step-by-step)
✅ **Proposed solution outlined** (to-be high-level approach)
✅ **Goals quantified** (80% reduction, >90% accuracy)
✅ **NFRs extracted** (performance, accuracy, cost, security)
✅ **Integrations identified** (Salesforce, NMLS, LexisNexis)
✅ **Constraints documented** (AWS platform, $5K budget, Python team)
✅ **Stakeholders mapped** (who's involved, who approves)
✅ **Success criteria defined** (how to measure success)
✅ **Keywords present** (for archetype matching: "multi-step", "approval", "workflow")

### Subtext Analysis (Political Reality)

✅ **Layer 2 blame patterns identified** (who's blaming whom? turf wars?)
✅ **Layer 3 systemic deflection mapped** (upstream/downstream blame? powerless middle management?)
✅ **Layer 4 hidden fears detected** (what are they afraid of but not saying?)
✅ **Layer 5 true motivations extracted** (what do they ACTUALLY want?)
✅ **Adoption risks assessed** (what will kill this project if missing?)
✅ **Solution design implications documented** (must-haves for political success)

### Critical Question

✅ **"If we build what they SAID they want (Layer 1), will they actually adopt it?"**
   - If NO → You haven't addressed Layers 4-5 (fears and true motivations)
   - If YES → You understand both explicit requirements AND subtext

---

## Why Projects Fail: Missing Subtext

**Reality**: Most failed projects had correct TECHNICAL solutions but ignored POLITICAL reality.

### Case Study: The "Perfect" Solution That Got Blocked

**Scenario**: Data team builds beautiful data catalog with auto-discovery, lineage tracking, quality metrics.

**What they said (Layer 1)**:
```
"We need better data discovery - analysts spend hours finding the right datasets"
```

**What they built**:
```
✅ Auto-discovery (crawls all databases)
✅ Lineage tracking (shows data provenance)
✅ Quality metrics (SLO compliance scores)
```

**Result**: **BLOCKED by data team lead. Project shelved.**

**Why?**

**What they DIDN'T detect (Layer 4-5)**:
```
Layer 4 Fear: "If analysts can self-serve, my team becomes irrelevant"
Layer 5 Want: "I need to CONTROL access (maintain my team's gatekeeper role)"

Missing requirement: "Data access requests routed through my team (approval workflow)"
```

**The solution was TECHNICALLY perfect but POLITICALLY unacceptable.**

If they'd detected the subtext, they would have built:
- ✅ Auto-discovery + lineage + quality metrics (solves Layer 1)
- ✅ **Approval workflow** (addresses Layer 4 fear - team stays relevant)
- ✅ **Audit trail showing team's value** (addresses Layer 5 want - prove team is critical)

→ Project would have succeeded.

---

### The Pattern

**Projects fail when**:
- You solve Layer 1 (surface problem)
- But ignore Layer 4 (fear) and Layer 5 (true motivation)
- Stakeholder feels threatened → political resistance → project blocked

**Projects succeed when**:
- You solve Layer 1 (surface problem)
- AND address Layer 4 (make them feel safe)
- AND satisfy Layer 5 (give them what they actually want)
- Stakeholder feels served → political support → adoption

**The hard truth**: Technical correctness is necessary but not sufficient. Political alignment is required for adoption.

---

## Common Pitfalls

### ❌ Ignoring Subtext (Most Common Failure)

**Bad**:
```
Meeting content: "We need automated gate tracking. Vendors don't update status. We need
executive visibility and audit trails."

Extraction: "Build automated gate tracking system"
(Ignores WHY they want executive visibility and audit trails)
```

**Good**:
```
Meeting content: [same as above]

Extraction:
  Explicit: "Build automated gate tracking"
  Subtext:
    - Layer 2: Blames vendors (turf war)
    - Layer 4 Fear: "I'll be blamed for delays I can't control"
    - Layer 5 Want: "LOOK in control (exec dashboard) + BLAME others (audit trail)"

  Solution design:
    - Automation (solves Layer 1)
    + Executive dashboard (solves Layer 5 - look in control)
    + Audit trail showing vendor failures (solves Layer 5 - blame others)
```

**Why this matters**: Without understanding Layers 4-5, you build a system that stakeholder will resist/sabotage.

---

### ❌ Jumping to Technology Too Early

**Bad**:
```
Requirements: "We need a microservices architecture with Kubernetes"
```

**Good**:
```
Requirements: "We need to verify 1,000 brokers/year with <2 hour turnaround.
Current manual process has 10 steps. HQ approval required for high-risk cases."
```

Let solution-mapper determine the archetype, let tech-stack-mapper choose Kubernetes (or not).

### ❌ Vague Goals

**Bad**:
```
Goal: "Make the process faster"
```

**Good**:
```
Goal: "Reduce workflow execution time from 1 day to <2 hours (10x improvement)"
Measurement: "Step Functions execution duration (CloudWatch metric)"
```

### ❌ Missing Constraints

**Bad**:
```
Requirements: "Automate broker verification"
(No budget, no timeline, no platform, no compliance mentioned)
```

**Good**:
```
Requirements: "Automate broker verification"
Constraints:
  - Budget: <$5K/month POC
  - Platform: AWS (funding approved)
  - Compliance: FINRA, CFPB, 7-year retention
  - Team: Python developers, no ML expertise
```

### ❌ No Stakeholder Context

**Bad**:
```
Requirements: "Implement AI automation"
```

**Good**:
```
Requirements: "Implement AI automation"
Stakeholders:
  - Offshore team: Executes current manual process (will use new system)
  - HQ team: Makes final approval decisions (human-in-the-loop, not automated)
```

---

## Usage Instructions

### For Solo Architect

1. **Read meeting minutes/emails/transcripts**
2. **Annotate** (pain points, goals, constraints, keywords)
3. **Extract** using template above
4. **Write** `requirements.md`
5. **Validate** with checklist
6. **Hand off** to solution-mapper

### For Team/Discovery Process

1. **Conduct discovery sessions** (interviews, workshops)
2. **Record** (video, audio, notes)
3. **Transcribe** (if needed)
4. **Collaboratively annotate** (team reviews together)
5. **Extract requirements** (use YAML template)
6. **Review with stakeholders** (validate understanding)
7. **Finalize** `requirements.md`
8. **Hand off** to solution-mapper

### For AI-Assisted Extraction

If using AI (Claude, GPT-4) to process transcripts:

**Prompt**:
```
You are extracting requirements from meeting content.

Read the attached meeting minutes/transcript/email.

Extract:
1. Business problem (pain points, current issues)
2. Current process (as-is steps)
3. Business goals (desired outcomes, targets)
4. NFRs (performance, accuracy, cost, security)
5. Integrations (external systems, APIs)
6. Constraints (budget, platform, timeline, skills)
7. Stakeholders (who's involved, responsibilities)
8. Success criteria (how to measure success)

Output as YAML using the template in decode-the-meeting.md
```

---

## Next Steps

After extracting requirements:

1. **Review** `requirements.md` with stakeholders (validate understanding)
2. **Apply** `/solution-mapper` → Identify archetype, map ontology
3. **Apply** `/tech-stack-mapper` → Select technologies, estimate costs
4. **Generate** deliverables → `solution-architecture.yaml`, `technology-stack.yaml`
5. **Build** the system

---

## Related SKILLs

- **Next**: `/solution-mapper` (takes requirements.md as input)
- **After that**: `/tech-stack-mapper` (takes solution-architecture.yaml as input)

---

## Examples

- **Broker Verification**: See `examples/broker-verification/requirements.md` (extracted from AWS funding email)
- **ald's NSO**: (Future) Extract from SteerCo meeting minutes
