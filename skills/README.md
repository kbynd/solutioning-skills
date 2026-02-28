# SKILLs - Core Framework Components

This directory contains the two core SKILLs that form the foundation of the architecture framework.

## Overview

The framework uses a two-stage process:

1. **`solution-mapper`**: Requirements → Archetype → Components (technology-agnostic)
2. **`tech-stack-mapper`**: Components → Technologies (budget/skills constrained)

This separation ensures:
- Architectural patterns are reusable across cloud platforms
- Technology choices are driven by constraints (not hype)
- Cost is considered upfront (not as an afterthought)

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
1. solution-mapper:
   Requirements → "Broker verification, 10 steps, HQ approval"
              ↓
   Archetype: Workflow Orchestrator
              ↓
   Components: Broker_Verification_Engine, HQ_Review_Manager, ...
              ↓
   Required capabilities: Stateful workflow, human-in-the-loop, ...

2. tech-stack-mapper:
   Required capabilities + Constraints (AWS, <$5K, Python team)
              ↓
   Technologies: Step Functions, Lambda, RDS, Bedrock
              ↓
   Costs: $774-$5,274/month POC ✅
              ↓
   Fitness functions: Workflow time <2hrs, API <500ms
```

### Why Two Separate SKILLs?

**Separation of Concerns**:
- `solution-mapper`: Focuses on **patterns** (reusable across clouds)
- `tech-stack-mapper`: Focuses on **constraints** (specific to project)

**Benefits**:
- Archetype catalog is **cloud-agnostic** (applies to AWS, Azure, GCP, on-prem)
- Technology choices are **justified** (not based on hype)
- Cost is **transparent** (known upfront, not discovered later)

**Example**: Same archetype, different platforms
- **Broker Verification (AWS)**: Step Functions, Lambda, RDS, Bedrock
- **Broker Verification (Azure)**: Logic Apps, Functions, SQL Database, OpenAI
- **Broker Verification (Open-source)**: Temporal, Kubernetes, PostgreSQL, Ollama

Same archetype, different tech stack!

---

## Usage Patterns

### Pattern 1: New Project (Greenfield)

1. Extract requirements → `requirements.md`
2. Apply `/solution-mapper` → `solution-architecture.yaml`
3. Apply `/tech-stack-mapper` → `technology-stack.yaml`
4. Generate IaC (Terraform/CloudFormation)
5. Implement components
6. Run fitness function tests
7. Deploy

### Pattern 2: Existing Project (Brownfield)

1. Reverse engineer current architecture → Which archetype?
2. Identify gaps (missing components, wrong archetype)
3. Apply `/solution-mapper` → Correct archetype
4. Apply `/tech-stack-mapper` → Optimal tech stack
5. Plan migration strategy (incremental, not big-bang)

### Pattern 3: Architecture Review

1. Read existing architecture documentation
2. Apply `/solution-mapper` → Is the archetype correct?
3. Apply `/tech-stack-mapper` → Are the technologies justified?
4. Check fitness functions → Are NFRs testable?
5. Provide feedback

---

## File Sizes and Complexity

| SKILL | Lines | Archetypes | Principles | Examples |
|-------|-------|------------|------------|----------|
| `solution-mapper.md` | 1,755 | 7 | Conway's Law | Retail Store Opening, Broker Verification |
| `tech-stack-mapper.md` | 1,366 | N/A | 12-factor, Evolutionary, Data longevity | Retail Store Opening, Broker Verification |

Both files are comprehensive references you'll return to repeatedly.

---

## Next Steps

1. **Read the examples**: `../examples/broker-verification/` (full walkthrough)
2. **Study one archetype deeply**: Start with Workflow Orchestrator or Digital Twin
3. **Apply to your project**: Extract requirements, run through both SKILLs
4. **Share feedback**: What archetypes are missing? What principles need clarification?

---

## Related Documentation

- **Archetype Deep Dives**: `../archetypes/` (coming soon)
- **Principles Library**: `../principles/` (12-factor app, evolutionary architecture)
- **Templates**: `../templates/` (YAML templates for solution architecture, tech stack)
- **Examples**: `../examples/` (complete worked examples)
