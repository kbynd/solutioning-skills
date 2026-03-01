# SKILLs - Core Framework Components

This directory contains the three core SKILLs that form the foundation of the architecture framework.

## Overview

The framework uses a three-stage process:

1. **`decode-the-meeting`**: Unstructured content → Structured requirements
2. **`solution-mapper`**: Requirements → Archetype → Components (technology-agnostic)
3. **`tech-stack-mapper`**: Components → Technologies (budget/skills constrained)

This separation ensures:
- Requirements are extracted systematically (not ad-hoc notes)
- Architectural patterns are reusable across cloud platforms
- Technology choices are driven by constraints (not hype)
- Cost is considered upfront (not as an afterthought)

---

## SKILL 0: `/decode-the-meeting`

**Purpose**: Extract structured requirements from unstructured meeting content (transcripts, minutes, emails, interviews).

**File**: `decode-the-meeting.md`

### What It Does

1. **Reads Unstructured Content**
   - Meeting minutes (SteerCo, discovery sessions)
   - Email threads (funding requests, stakeholder communications)
   - Transcripts (Zoom calls, interviews)
   - Documents (proposals, presentations)

2. **Identifies Key Information**
   - Pain points (complaints, workarounds, frustrations)
   - Business goals (reduce, improve, accelerate, eliminate)
   - KPIs and metrics (95% target, 195 stores/year)
   - Current process (as-is workflows)
   - Decisions made (platform, budget, timeline)
   - Integrations (Salesforce, NMLS, LexisNexis)
   - Constraints (budget, platform, skills, compliance)

3. **Extracts Structured Requirements**
   - Project context (what, who, when, why)
   - Business problem (pain points with impact)
   - Current state (step-by-step as-is process)
   - Business goals (quantified targets)
   - NFRs (performance, accuracy, scalability, cost, security)
   - Stakeholders (roles, responsibilities, pain points)
   - Success criteria (measurable outcomes)

4. **Outputs to Solution-Mapper**
   - `requirements.md` (formatted for human review + solution-mapper input)
   - Keywords for archetype matching ("multi-step", "approval", "workflow")

### Input

- Meeting minutes (Word, PDF, emails)
- Discovery session transcripts (Zoom, Teams)
- Email threads (funding requests, proposals)
- Stakeholder interviews (1-on-1 conversations)

### Output

`requirements.md` containing:
- Project context
- Business problem and pain points
- Current manual process (as-is)
- Proposed solution (to-be)
- Business goals and success criteria
- Non-functional requirements
- Key integrations
- Constraints and stakeholders

### When to Use

- Starting discovery (extracting requirements from kickoff meetings)
- Processing SteerCo minutes (ongoing requirements capture)
- Analyzing email threads (funding requests, proposals)
- Documenting interviews (stakeholder pain points)

### Example

**Input**: AWS funding request email (Broker Verification)

**Process**:
1. Read email: "Manual 10-step process, Control-F searches, LexisNexis reviews..."
2. Annotate: Pain points, goals, integrations, constraints
3. Extract: Structured YAML
4. Write: `requirements.md`

**Output**: See `examples/broker-verification/requirements.md`

---

## SKILL 1: `/solution-mapper`

**Purpose**: Map validated requirements to solution architecture through **Solution Archetypes**.

**File**: `solution-mapper.md` (1,755 lines)

### What It Does

1. **Identifies Solution Archetype** (from catalog of 7)
   - Digital Twin with Ontology-Based Operations
   - System of Record
   - Workflow Orchestrator
   - Analytics Platform
   - Integration Platform
   - Collaboration Hub
   - Marketplace/Portal

2. **Maps Domain to Canonical Ontology**
   - Broker → MasterEntity
   - Broker Verification → WorkflowInstance
   - Risk Check → Custom entity type

3. **Instantiates Solution Components**
   - Workflow_Engine → Broker_Verification_Engine
   - Approval_Manager → HQ_Review_Manager
   - Performance_Analyzer → Risk_Analyzer

4. **Defines Required Capabilities**
   - What technology must provide (not which technology)
   - Workflow orchestration: stateful, long-running, human-in-the-loop
   - Document analysis: PDF parsing, NLU, structured output

### Input

- Project requirements
- Business problems
- Keywords (multi-step, approval, workflow, dashboard, etc.)

### Output

`solution-architecture.yaml` containing:
- Archetype selection + rationale
- Domain ontology (entity types, properties, relationships)
- Solution components (instantiated from archetype)
- Required capabilities (handed to tech-stack-mapper)
- Team structure (Conway's Law alignment)

### When to Use

- Starting a new project (greenfield)
- Redesigning existing system (brownfield)
- Evaluating architecture proposals (is this the right pattern?)

### Example

**Input**: "Verify brokers, 10-step process, HQ approval, LexisNexis background checks"

**Process**:
1. Keyword match: "multi-step" (6), "approval" (6), "workflow" (6) → **Workflow Orchestrator**
2. Map ontology: Broker verification → WorkflowInstance, Steps → VerificationStep
3. Instantiate: Workflow_Engine → Broker_Verification_Engine
4. Output: `solution-architecture.yaml`

**See**: `../examples/broker-verification/solution-architecture.yaml`

---

## SKILL 2: `/tech-stack-mapper`

**Purpose**: Select specific technologies based on **non-functional requirements**, **architectural principles**, and **organizational constraints**.

**File**: `tech-stack-mapper.md` (1,366 lines)

### What It Does

1. **Extracts NFRs from Context**
   - Performance: <2 hours workflow, <500ms API
   - Accuracy: >95% recall, <20% false positives
   - Cost: <$5K/month POC, <$15K/month production
   - Platform: AWS (funding requirement)

2. **Applies Architectural Principles**
   - Data lives longer than code (PostgreSQL, not DynamoDB)
   - Minimize data stores (3, not 6+)
   - 12-factor compliance (stateless, config in env)

3. **Defines Minimal Data Architecture**
   - Tier 1: PostgreSQL (operational: brokers, workflows, approvals)
   - Tier 2: S3 (documents, audit logs)
   - Tier 3: Redis (cache: risk list lookups)

4. **Maps Components to Technologies**
   - Broker_Verification_Engine → AWS Step Functions (not Temporal/Camunda)
   - Risk_Analyzer → AWS Bedrock Claude Sonnet (not SageMaker)
   - Broker_Registry → RDS PostgreSQL (not DynamoDB)

5. **Estimates Costs**
   - POC: $274/month base + $500-$5K variable = $774-$5,274/month
   - Production: $600-$5,500/month (after optimizations)

6. **Defines Fitness Functions**
   - Workflow time <2 hours (p95) → Step Functions duration metric
   - API latency <500ms (p95) → API Gateway latency metric
   - Risk recall >95% → Manual validation (100 sample reports)

7. **Plans Evolution Strategy**
   - POC: Prove value (serverless, pay-per-use)
   - Production: Optimize (Aurora Serverless, Claude Haiku)
   - Future: ML scoring, real-time alerts

### Input

- Solution architecture (from solution-mapper)
- Budget constraints
- Team skills
- Cloud platform preference
- NFRs (performance, security, compliance)

### Output

`technology-stack.yaml` containing:
- NFR extraction (from project context)
- Architectural principles applied
- Data architecture (3 tiers)
- Component technology mapping
- Cost estimates (POC + production)
- Fitness functions (NFRs as tests)
- Evolution strategy (POC → production → future)

### When to Use

- After solution-mapper (have archetype + components)
- Estimating project costs (POC + production)
- Choosing between technology alternatives (PostgreSQL vs. DynamoDB)
- Defining acceptance criteria (fitness functions)

### Example

**Input**: Solution architecture with Broker_Verification_Engine component

**Process**:
1. Extract NFRs: <$5K/month, AWS platform, <2 hour workflow
2. Apply principles: Data longevity → PostgreSQL, not DynamoDB
3. Select tech: Step Functions (human approval built-in)
4. Estimate cost: $25/month Step Functions + $120 RDS + ... = $274/month
5. Define fitness: Workflow time <2 hours → CloudWatch metric
6. Output: `technology-stack.yaml`

**See**: `../examples/broker-verification/technology-stack.yaml`

---

## How the SKILLs Work Together

### Sequential Flow

```
0. decode-the-meeting:
   Email/Meeting → "Manual 10-step process, Control-F searches, HQ approval..."
              ↓
   Extract: Pain points, goals, integrations, constraints
              ↓
   Output: requirements.md (structured requirements)

1. solution-mapper:
   requirements.md → "Broker verification, 10 steps, HQ approval"
              ↓
   Archetype: Workflow Orchestrator
              ↓
   Components: Broker_Verification_Engine, HQ_Review_Manager, ...
              ↓
   Required capabilities: Stateful workflow, human-in-the-loop, ...
              ↓
   Output: solution-architecture.yaml

2. tech-stack-mapper:
   solution-architecture.yaml + Constraints (AWS, <$5K, Python team)
              ↓
   Technologies: Step Functions, Lambda, RDS, Bedrock
              ↓
   Costs: $774-$5,274/month POC ✅
              ↓
   Fitness functions: Workflow time <2hrs, API <500ms
```

### Why Three Separate SKILLs?

**Separation of Concerns**:
- `decode-the-meeting`: Focuses on **extraction** (unstructured → structured)
- `solution-mapper`: Focuses on **patterns** (reusable across clouds)
- `tech-stack-mapper`: Focuses on **constraints** (specific to project)

**Benefits**:
- Requirements extraction is **systematic** (not ad-hoc notes)
- Archetype catalog is **cloud-agnostic** (applies to AWS, Azure, GCP, on-prem)
- Technology choices are **justified** (not based on hype)
- Cost is **transparent** (known upfront, not discovered later)

**Example**: Same meeting → Same archetype → Different tech stacks

**Meeting content** (AWS funding email):
→ "Manual 10-step broker verification, Control-F searches, HQ approval..."

**decode-the-meeting** → `requirements.md`:
→ Pain points, goals, integrations, constraints (platform-agnostic)

**solution-mapper** → Workflow Orchestrator archetype:
→ Same pattern whether deploying on AWS, Azure, or on-prem

**tech-stack-mapper** → Different platforms:
- **AWS**: Step Functions, Lambda, RDS, Bedrock
- **Azure**: Logic Apps, Functions, SQL Database, OpenAI
- **Open-source**: Temporal, Kubernetes, PostgreSQL, Ollama

Same meeting → Same archetype → Different tech stacks!

---

## Usage Patterns

### Pattern 1: New Project (Greenfield)

1. Apply `/decode-the-meeting` → Extract requirements from meetings/emails → `requirements.md`
2. Apply `/solution-mapper` → Identify archetype, map ontology → `solution-architecture.yaml`
3. Apply `/tech-stack-mapper` → Select technologies, estimate costs → `technology-stack.yaml`
4. Generate IaC (Terraform/CloudFormation)
5. Implement components
6. Run fitness function tests
7. Deploy

### Pattern 2: Existing Project (Brownfield)

1. Apply `/decode-the-meeting` → Document current state from interviews/observations
2. Reverse engineer current architecture → Which archetype?
3. Identify gaps (missing components, wrong archetype)
4. Apply `/solution-mapper` → Correct archetype
5. Apply `/tech-stack-mapper` → Optimal tech stack
5. Plan migration strategy (incremental, not big-bang)

### Pattern 3: Architecture Review

1. Read existing architecture documentation
2. Apply `/solution-mapper` → Is the archetype correct?
3. Apply `/tech-stack-mapper` → Are the technologies justified?
4. Check fitness functions → Are NFRs testable?
5. Provide feedback

---

## File Sizes and Complexity

| SKILL | Lines | Focus | Key Concepts | Examples |
|-------|-------|-------|--------------|----------|
| `decode-the-meeting.md` | 778 | Extraction | Pain points, goals, constraints, stakeholders | Broker Verification email, SteerCo minutes |
| `solution-mapper.md` | 1,755 | Patterns | 7 archetypes, ontology, components | Retail Store Opening, Broker Verification |
| `tech-stack-mapper.md` | 1,366 | Technologies | 12-factor, evolutionary, data longevity | Retail Store Opening, Broker Verification |

All three files are comprehensive references you'll return to repeatedly.

---

## Next Steps

1. **Read the complete example**: `../examples/broker-verification/` (full three-stage walkthrough)
2. **Study one SKILL deeply**: Start with decode-the-meeting (most practical starting point)
3. **Study one archetype**: Workflow Orchestrator or Digital Twin (most common)
4. **Apply to your project**: Process a meeting/email → Extract requirements → Run through all three SKILLs
5. **Share feedback**: What archetypes are missing? What principles need clarification?

---

## Related Documentation

- **Archetype Deep Dives**: `../archetypes/` (coming soon)
- **Principles Library**: `../principles/` (12-factor app, evolutionary architecture)
- **Templates**: `../templates/` (YAML templates for requirements, solution architecture, tech stack)
- **Examples**: `../examples/` (complete worked examples with all three SKILLs applied)
