# Broker Verification Automation - Complete Example

This folder contains a complete worked example of applying the architecture framework to a real-world project: ** Counterparty Risk - Broker Verification Automation**.

## Project Overview

**Team**: Production Back Office - Counterparty Risk
**Challenge**: Manual 10-step broker verification process taking 1 day per broker
**Goal**: AI-driven automation reducing to <2 hours with >90% accuracy

## What's in This Example

### 1. `requirements.md`
**Source**: Extracted from AWS funding POC request email (Feb 2026)

**Contents**:
- Business problem and pain points
- Current manual process (10 steps)
- Proposed automation solution
- Non-functional requirements (performance, accuracy, cost, compliance)
- Key integrations (Salesforce, NMLS, LexisNexis)
- Success criteria

**Use this to see**: How to extract and document requirements from stakeholder communications

---

### 2. `solution-architecture.yaml`
**Source**: Output from `/solution-mapper` SKILL

**Contents**:
- **Archetype selection**: Workflow Orchestrator (not Digital Twin)
- **Domain ontology**: 7 entity types (Broker, WorkflowInstance, VerificationStep, RiskCheck, RiskFinding, Approval, ExternalSystem)
- **Solution components**: 5 components instantiated from canonical patterns
  - SC-001: Broker_Verification_Engine (workflow orchestration)
  - SC-002: HQ_Review_Manager (human approval)
  - SC-003: Integration_Orchestrator (Salesforce, NMLS, LexisNexis)
  - SC-004: Risk_Analyzer (AI document analysis)
  - SC-005: Broker_Registry (master data)
- **Required capabilities**: Handed off to tech-stack-mapper (no specific technologies yet)
- **Team structure**: Conway's Law alignment (5 teams, 5 components)
- **KPI mapping**: Business goals → architectural components

**Use this to see**: How solution-mapper identifies archetypes and maps requirements to solution components

---

### 3. `technology-stack.yaml`
**Source**: Output from `/tech-stack-mapper` SKILL

**Contents**:
- **NFR extraction**: Extracted from project context (performance, accuracy, cost, compliance)
- **Architectural principles**: Data longevity, minimal data stores, 12-factor compliance
- **Data architecture**: 3 data stores (PostgreSQL, S3, Redis) vs. 6+ in typical microservices
- **Technology selections**:
  - Workflow: AWS Step Functions (not Temporal/Camunda)
  - Compute: AWS Lambda (Python 3.12)
  - Database: RDS PostgreSQL (not DynamoDB)
  - AI: AWS Bedrock (Claude Sonnet 3.5)
  - Cache: ElastiCache Redis
- **Cost estimates**:
  - POC: $774 - $5,274/month (within $5K budget ✅)
  - Production: $600 - $5,500/month (ROI: $30K/year savings)
- **Fitness functions**: 6 testable NFRs (workflow time, API latency, accuracy, etc.)
- **Evolution strategy**: POC → Production → ML enhancements

**Use this to see**: How tech-stack-mapper selects technologies based on constraints (budget, team skills, platform)

---

## How This Example Was Created

### Step 1: Requirements Discovery
1. Read the request email
2. Extract business problem, current process, proposed solution
3. Identify NFRs (performance, accuracy, cost, compliance)
4. Document in `requirements.md`

### Step 2: Solution Mapping (via `/solution-mapper`)
1. **Keyword analysis**:
   - "multi-step process" → Workflow Orchestrator (6 matches)
   - "approval" → Workflow Orchestrator
   - "integrate" → Integration Platform (4 matches)
   - Primary archetype: **Workflow Orchestrator** ✅

2. **Ontology mapping**:
   - Broker application → WorkflowInstance
   - Verification steps → VerificationStep
   - HQ review → Approval
   - Risk checks → RiskCheck, RiskFinding

3. **Component instantiation**:
   - Workflow_Engine → Broker_Verification_Engine
   - Approval_Manager → HQ_Review_Manager
   - ETL_Engine → Integration_Orchestrator
   - Performance_Analyzer (AI) → Risk_Analyzer
   - Entity_Repository → Broker_Registry

4. **Output**: `solution-architecture.yaml` (archetype + ontology + components + capabilities)

### Step 3: Technology Selection (via `/tech-stack-mapper`)
1. **NFR extraction**:
   - Performance: <2 hours workflow, <500ms API
   - Accuracy: >95% recall, <20% false positives
   - Cost: <$5K/month POC, <$15K/month production
   - Platform: AWS (funding requirement)

2. **Apply principles**:
   - Data longevity: PostgreSQL + S3 (not DynamoDB)
   - Minimal stores: 3 (PostgreSQL, S3, Redis) not 6+
   - 12-factor: Stateless Lambda, config in env, logs as streams

3. **Select technologies**:
   - Workflow: Step Functions (human approval, visual designer)
   - AI: Bedrock Claude Sonnet (document analysis)
   - Database: RDS PostgreSQL (open format, 7-year retention)
   - Cache: Redis (risk list lookups)

4. **Estimate costs**: $774-$5,274/month POC ✅ Within budget

5. **Define fitness functions**: 6 testable NFRs

6. **Output**: `technology-stack.yaml` (tech stack + costs + fitness functions + evolution)

---

## Key Insights from This Example

### ✅ Correct Archetype Selection
- **NOT Digital Twin** (like Retail Store Opening)
- **YES Workflow Orchestrator** (multi-step approval process)
- Demonstrates archetype catalog is reusable across domains

### ✅ Cost-Aware but Not Cost-Driven
- `solution-mapper` focused on **patterns** (ontology, components)
- `tech-stack-mapper` focused on **constraints** (budget, skills, platform)
- Separation of concerns working correctly

### ✅ Principles Over Features
- Chose PostgreSQL over DynamoDB (data longevity, open format)
- Chose 3 data stores vs. 6+ (minimize fragmentation)
- Chose Step Functions over Temporal (team skills, AWS-native)

### ✅ Evolution Strategy
- POC: Prove value with minimal cost (serverless, pay-per-use)
- Production: Optimize (Aurora Serverless, Claude Haiku)
- Future: ML risk scoring, real-time alerts

### ✅ Fitness Functions
- NFRs are testable (not just documented)
- Workflow time, API latency, accuracy, scalability
- Alerts trigger when NFRs violated

---

## How to Use This Example

### For Learning
1. Read `requirements.md` → Understand the business problem
2. Read `solution-architecture.yaml` → See how archetype was selected
3. Read `technology-stack.yaml` → See how technologies were chosen
4. Compare to Retail Store Opening example (different archetype, same framework)

### For Your Own Project
1. Copy `requirements.md` template → Fill in your project details
2. Apply `/solution-mapper` → Identify archetype, map ontology
3. Apply `/tech-stack-mapper` → Select technologies based on constraints
4. Generate your own `solution-architecture.yaml` and `technology-stack.yaml`

### For Validation
- Use this as a reference when reviewing architecture proposals
- Check: Did they identify the right archetype?
- Check: Did they minimize data stores?
- Check: Did they define fitness functions?
- Check: Did they consider evolution strategy?

---

## Next Steps

If you were building this POC, you would:

1. **Generate IaC** (Terraform or CloudFormation) from `technology-stack.yaml`
2. **Implement Step Functions state machine** (workflow definition)
3. **Build Lambda functions** (one per workflow step)
4. **Set up RDS PostgreSQL** (create schema from ontology)
5. **Configure Bedrock** (AI document analysis prompt)
6. **Deploy API Gateway** (expose APIs for HQ dashboard)
7. **Implement fitness function tests** (CI/CD validation)
8. **Run POC** (100 real broker verifications)
9. **Measure success** (80% effort reduction, >90% accuracy)
10. **Transition to production** (optimize costs, scale to 1,000+/month)

---

## Related Examples

- **Retail Store Opening**: Digital Twin archetype (track physical stores, real-time state sync)
- **Broker Verification**: Workflow Orchestrator archetype (multi-step approval, human-in-the-loop)

Both used the same framework but resulted in different architectures. This proves the archetype catalog is **reusable and domain-agnostic**.
