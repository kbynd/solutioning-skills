# SKILL: /decode-the-meeting

## Purpose

Extract structured requirements from unstructured meeting content (transcripts, minutes, emails, interviews). Transforms stakeholder conversations into actionable inputs for solution-mapper.

## The Problem

Most requirements don't arrive as clean specifications. They emerge from:
- **Steering committee meetings** ("We're missing 30% of our gate deadlines")
- **Discovery sessions** ("The offshore team spends hours on Control-F searches")
- **Email threads** ("We need to reduce manual verification time")
- **Stakeholder interviews** ("Our biggest pain point is the LexisNexis document review")

These contain **implicit requirements** buried in:
- Complaints ("This process takes too long")
- Decisions ("We approved AWS funding for the POC")
- Questions ("Can we integrate with NMLS automatically?")
- Metrics ("195 stores per year, 95% on-time target")

## Input Sources

### 1. Meeting Minutes
**Format**: Word docs, PDFs, emails with meeting notes
**Example**: Retail Store Opening Weekly SteerCo Minutes
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

## Process

### Step 1: Read and Annotate

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

## Example Workflow

### Input: AWS Funding Email (Broker Verification)

**Raw Email**:
```
Subject: AWS Funding - POC for Counterparty Risk – Broker Verification Automation

The Counterparty Risk team is responsible for verifying brokers for the TPO and PCG channels.
Current process:
1. Salesforce record created
2. Offshore team retrieves record
3. Download broker names
4. Manual check against high-risk list (Excel, Control-F)
5. NMLS verification
6. LexisNexis check (if high-risk)
7. Download reports
8. Manual line-by-line review
9. Flag findings
10. Escalate to HQ team

Proposed: AI-driven automation
- Integrate with Salesforce
- Automate name checks
- NMLS verification (API or web)
- LexisNexis retrieval + AI document analysis
- Flag risk indicators automatically

Goals: Reduce manual effort, improve accuracy, accelerate cycles
```

### Process: Apply decode-the-meeting

**Step 1**: Annotate
- Pain points: Manual Excel searches, manual PDF reviews
- Goals: Reduce effort, improve accuracy, accelerate
- Current: 10-step manual process
- Integrations: Salesforce, NMLS, LexisNexis
- Constraints: (inferred) AWS platform, POC budget

**Step 2**: Extract structured requirements (YAML above)

**Step 3**: Output `requirements.md`

### Output: Structured Requirements

See `examples/broker-verification/requirements.md` in the repository.

---

## Templates

### Template 1: Meeting Minutes

**Input**: Retail Store Opening Weekly SteerCo Minutes

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

**File**: `requirements.md` (Markdown)

**Sections**:
1. Project Context
2. Business Problem
3. Current Manual Process (As-Is)
4. Proposed Solution (To-Be)
5. Business Goals
6. Non-Functional Requirements
7. Key Integrations
8. Success Criteria
9. Compliance Context

**See**: `examples/broker-verification/requirements.md` for complete example

---

## Validation Checklist

Before handing to solution-mapper, ensure:

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

---

## Common Pitfalls

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
- **Retail Store Opening**: (Future) Extract from SteerCo meeting minutes
