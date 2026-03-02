# SLO Threshold Determination Framework

**Pattern Type**: Universal quality commitment pattern - applies to all archetypes with service level agreements

**Purpose**: Provide decision framework for determining appropriate SLO thresholds based on organizational context, use case requirements, and technical constraints. **Does NOT prescribe specific numbers** - helps you determine the right thresholds for YOUR context.

---

## Why This Framework Exists

**Common mistake**: Copying someone else's SLO thresholds (e.g., "Data mesh should use 90% SLO")

**Problem with copying**:
- Different organizations have different failure costs
- Different use cases have different quality requirements
- Different technical constraints limit what's achievable
- Different compliance mandates set different floors

**Framework approach**: Answer 5 questions to determine YOUR appropriate thresholds

---

## The 5 Questions

Every SLO threshold decision requires answering these 5 questions:

```
Question 1: What's the COST of failure? (Risk analysis)
Question 2: What's the CONSUMER use case? (Requirements analysis)
Question 3: What's TECHNICALLY feasible? (Capability analysis)
Question 4: What's LEGALLY mandated? (Compliance analysis)
Question 5: What's the COST-QUALITY trade-off? (Economic analysis)
```

**SLO determination formula**:
```
SLO_threshold = MAX(
  compliance_floor,           # Legal minimum
  MIN(
    technical_ceiling,        # Technical maximum
    cost_justified_level,     # Economic optimum
    use_case_requirement      # Business need
  )
)

Translation: "Meet compliance minimum, but don't exceed technical limits or
             pay for quality beyond what use case requires"
```

---

## Question 1: What's the Cost of Failure?

**Question**: What happens if quality falls below the threshold? What's the impact?

### Decision Formula

```yaml
cost_of_failure_analysis:

  if_cost_of_failure_is_catastrophic:
    # Examples: SEC violation, patient harm, loss of life
    slo_threshold: ">99% or higher"
    rationale: "Risk intolerable - must minimize failure probability"

  if_cost_of_failure_is_severe:
    # Examples: Revenue loss >$100K, major customer churn, regulatory warning
    slo_threshold: "95-99%"
    rationale: "Significant risk - invest in quality"

  if_cost_of_failure_is_moderate:
    # Examples: Revenue loss <$100K, minor customer frustration
    slo_threshold: "90-95%"
    rationale: "Acceptable risk - standard quality"

  if_cost_of_failure_is_low:
    # Examples: Internal analytics slightly off, no business impact
    slo_threshold: "70-90%"
    rationale: "Tolerable risk - economy quality acceptable"
```

---

### Example 1: Data Mesh - Financial Reporting Product

```yaml
product: "Finance.daily_revenue_report"

failure_scenario: "Inaccurate revenue reported to SEC in quarterly filing"

cost_of_failure:
  financial: "$10M SEC fine"
  reputational: "Stock price drop (investor loss of confidence)"
  legal: "Executive liability (personal consequences)"
  career: "CFO fired, finance team disbanded"

cost_analysis: "CATASTROPHIC"

slo_implication:
  threshold: ">99% accuracy + completeness"
  rationale: "Cost of failure ($10M+) vastly exceeds cost of achieving 99% quality"
  no_compromise: "Cannot accept lower SLO - compliance and legal risk intolerable"
```

---

### Example 2: Data Mesh - Marketing Campaign Metrics

```yaml
product: "Marketing.campaign_performance_dashboard"

failure_scenario: "Dashboard shows stale data (1-day old instead of 1-hour)"

cost_of_failure:
  financial: "$50K revenue loss (suboptimal campaign decisions)"
  operational: "Marketing team frustration (manual workarounds)"
  strategic: "Missed opportunity to pause losing campaign early"

cost_analysis: "MODERATE"

slo_implication:
  threshold: "90-95% freshness"
  rationale: "Failure costs exist but not catastrophic - standard quality sufficient"
  trade_off: "95% costs $5K/month, 99% costs $20K/month - not justified for $50K risk"
```

---

### Example 3: E-Commerce - Same-Day Shipping

```yaml
product: "Same-day shipping service level"

failure_scenario: "Package doesn't arrive same day (arrives next day instead)"

cost_of_failure:
  financial: "$7.99 refund (shipping fee) + $15 discount coupon"
  reputational: "Customer writes bad review, shares on social media"
  retention: "Customer switches to Amazon (lifetime value loss: $500)"

cost_analysis: "SEVERE (per customer), CATASTROPHIC (if widespread)"

slo_implication:
  threshold: ">95% on-time delivery"
  rationale: "Individual failure = $23 cost, but reputation damage compounds"
  tipping_point: "If SLO <90%, customers stop trusting same-day promise → brand damage"
```

---

### Example 4: Medieval Market - Daily Bread Grain

```yaml
product: "Fresh daily grain for baker"

failure_scenario: "Grain delivery fails (baker has no grain for bread)"

cost_of_failure:
  economic: "Baker can't make bread → no revenue that day (200 families unpaid)"
  social: "200 families go hungry"
  reputation: "Baker loses customers permanently (trust broken)"
  cascading: "Baker can't pay rent → business closes"

cost_analysis: "CATASTROPHIC (for baker's livelihood)"

slo_implication:
  threshold: ">98% daily delivery"
  rationale: "Baker's business depends on reliability - single failure = catastrophic"
  alternative: "If <95%, baker finds new supplier (switching cost exists)"
```

---

## Question 2: What's the Consumer Use Case?

**Question**: How does the consumer use this? What quality do they actually need?

### Decision Formula

```yaml
use_case_analysis:

  if_use_case_is_realtime_critical:
    # Examples: Dynamic pricing, fraud detection, emergency response
    slo_threshold: ">99%"
    rationale: "Staleness or unavailability immediately impacts decisions"

  if_use_case_is_operational:
    # Examples: Customer-facing dashboards, daily operations
    slo_threshold: "95-99%"
    rationale: "Users expect reliability but can tolerate brief issues"

  if_use_case_is_batch_analytics:
    # Examples: Daily reports, weekly analysis, trend tracking
    slo_threshold: "90-95%"
    rationale: "Not time-critical - occasional delays acceptable"

  if_use_case_is_exploratory:
    # Examples: Ad-hoc analysis, ML experiments, R&D
    slo_threshold: "70-90%"
    rationale: "Quality nice-to-have but not critical for exploration"
```

---

### Example 1: Data Mesh - Realtime Repricing Engine

```yaml
consumer: "Hotel repricing engine"
use_case: "Detect booking surge → adjust room prices in real-time"

quality_requirements:
  freshness: "<5 seconds (CRITICAL - pricing decisions happen in real-time)"
  completeness: ">99.9% (missing bookings → wrong pricing → revenue loss)"
  availability: ">99.9% (downtime = can't reprice = lost revenue)"

use_case_tolerance:
  freshness_degradation: "If >30 sec stale, pricing decisions wrong (competitor wins)"
  downtime_tolerance: "1 minute downtime = $1,000 revenue loss (high-demand periods)"

slo_implication:
  threshold: ">99.9% (realtime-critical use case)"
  rationale: "Real-time decisions require real-time quality - no compromise"
  cost_justification: "Premium SLO ($500/month) justified by revenue impact ($10K+/month)"
```

---

### Example 2: Data Mesh - Daily Finance Report

```yaml
consumer: "Finance team"
use_case: "Daily revenue review meeting (9am every morning)"

quality_requirements:
  freshness: "By 9am (prefer midnight batch, 8-hour buffer acceptable)"
  accuracy: "100% (CRITICAL - finance can't tolerate errors)"
  completeness: ">95% (can explain minor gaps, but needs vast majority)"

use_case_tolerance:
  freshness_degradation: "If report arrives by 11am, still usable (meeting delayed)"
  accuracy_degradation: "Zero tolerance (errors → compliance violations)"
  downtime_tolerance: "Can skip 1 day (Monday-Friday, 1 day miss = 20% failure = 80% SLO)"

slo_implication:
  threshold: "90% freshness + 100% accuracy when delivered"
  rationale: "Batch use case tolerates occasional delays, NOT errors"
  tiered_slos: "Freshness 90% (can wait), Accuracy 100% (cannot compromise)"
```

---

### Example 3: Data Mesh - Demand Forecasting Model

```yaml
consumer: "ML forecasting model (trains weekly)"
use_case: "Predict next 2 weeks demand for capacity planning"

quality_requirements:
  freshness: "<1 week (model retrains weekly, data just needs to be ready)"
  completeness: ">80% (ML tolerates missing data with imputation)"
  accuracy: ">85% (statistical model smooths out noise)"

use_case_tolerance:
  freshness_degradation: "If data 2 weeks old, forecast still useful (trends don't change fast)"
  completeness_degradation: "Can train on 70% data (reduced accuracy but still valuable)"
  downtime_tolerance: "Can skip 1 week (train on old model, degrade gracefully)"

slo_implication:
  threshold: "80% (exploratory/experimental use case)"
  rationale: "ML use case tolerates lower quality - focus on cost over quality"
  cost_optimization: "Economy SLO ($30/month) vs Premium ($500/month) - 17x savings justified"
```

---

### Example 4: SaaS - Photo Sharing App

```yaml
consumer: "Mobile app users uploading/viewing photos"
use_case: "User expects instant photo load (<2 seconds)"

quality_requirements:
  latency: "<100ms p95 (users perceive lag >200ms)"
  availability: ">99.9% (downtime = users can't access photos = app useless)"
  durability: ">99.999999999% (11 nines - losing user photos catastrophic)"

use_case_tolerance:
  latency_degradation: "If >500ms, users complain, leave bad reviews"
  downtime_tolerance: "5 minutes/month acceptable (0.01% downtime)"
  data_loss_tolerance: "Zero tolerance (losing photos = permanent reputation damage)"

slo_implication:
  threshold: "99.9% availability + 99.999999999% durability"
  rationale: "User-facing application - quality expectations high"
  technology_choice: "Must use hot storage ($0.023/GB) - cold storage unacceptable"
```

---

## Question 3: What's Technically Feasible?

**Question**: What quality can we actually achieve given system constraints?

### Decision Formula

```yaml
technical_feasibility_analysis:

  step_1_identify_constraints:
    - "Upstream dependencies (third-party APIs, vendor SLAs)"
    - "Infrastructure limitations (network, compute, storage)"
    - "Data source characteristics (batch windows, API rate limits)"
    - "Team skills (operational maturity, incident response)"

  step_2_determine_ceiling:
    formula: "SLO_ceiling = MIN(upstream_slo, infrastructure_capability) - safety_buffer"
    safety_buffer: "10-20% buffer for unexpected failures"

  step_3_set_realistic_slo:
    rule: "Never promise SLO above technical ceiling (sets up for failure)"

  examples:
    if_upstream_dependency_is_99_percent:
      your_slo_max: "95-98% (leave buffer for your code failures)"

    if_infrastructure_achieves_99_9_percent:
      your_slo_max: "99% (leave buffer for deployment, bugs)"
```

---

### Example 1: Data Mesh - External API Dependency

```yaml
product: "Payments.raw_transactions (sourced from payment processor API)"

technical_constraints:
  upstream_dependency:
    provider: "Stripe API"
    sla: "99% uptime (Stripe's published SLA)"
    reality: "99.2% actual uptime (historical 6-month average)"

  your_infrastructure:
    kafka_cluster: "99.99% uptime (historical)"
    etl_pipeline: "99.5% uptime (code bugs, deployments)"

  bottleneck: "Stripe API (99%) is the limiting factor"

technical_ceiling_calculation:
  formula: "MIN(99% Stripe, 99.99% Kafka, 99.5% ETL) = 99%"
  safety_buffer: "Leave 4% buffer (99% → 95%)"
  technical_ceiling: "95%"

slo_implication:
  threshold: "90-95% (cannot exceed 95% given upstream constraint)"
  rationale: "Upstream dependency limits achievable quality"
  mitigation: "If need >95%, must negotiate better Stripe SLA or add fallback provider"
  honest_slo: "Promise 90% publicly, strive for 95% internally (under-promise, over-deliver)"
```

---

### Example 2: Data Mesh - Batch Window Constraint

```yaml
product: "Sales.daily_orders (aggregated from order events)"

technical_constraints:
  data_source:
    batch_window: "Midnight to 6am (source system batch export)"
    availability: "Data ready by 6am (100% historical reliability)"

  processing_time:
    etl_duration: "30 minutes (transform + aggregate)"
    validation: "15 minutes (quality checks)"
    total: "45 minutes"

  delivery:
    ready_by: "6:45am (latest)"
    consumer_need: "By 9am (finance meeting)"
    buffer: "2 hours 15 minutes"

technical_ceiling_calculation:
  earliest_delivery: "6:45am"
  consumer_deadline: "9am"
  buffer_available: "2h 15min (generous)"
  failure_modes: "Rare source delays (<5% of time), occasional ETL bugs (<3%)"
  technical_ceiling: "92% (source 95% × ETL 97%)"

slo_implication:
  threshold: "90% (slightly below technical ceiling for safety)"
  rationale: "Batch constraint limits freshness - can't deliver faster than 6:45am"
  cannot_improve_without: "Moving to real-time streaming (10x cost increase)"
```

---

### Example 3: E-Commerce - Seasonal Capacity Constraint

```yaml
product: "Same-day shipping (e-commerce)"

technical_constraints:
  warehouse_capacity:
    normal_days: "10,000 orders/day capacity"
    peak_days: "Black Friday, Cyber Monday (50,000 orders/day demand)"
    constraint: "Can't scale warehouse overnight"

  courier_availability:
    normal: "5 courier partners (redundancy)"
    peak: "All 5 couriers at capacity (no redundancy)"

  weather_disruptions:
    frequency: "5% of days (storms, snow)"
    impact: "Delays or cancellations unavoidable"

technical_ceiling_calculation:
  normal_days: "98% achievable (360 days/year)"
  peak_days: "70% achievable (5 days/year - overwhelming demand)"
  weather_days: "50% achievable (18 days/year - force majeure)"

  annual_calculation:
    (360 × 98%) + (5 × 70%) + (18 × 50%) / 365 = 94.8%

slo_implication:
  threshold: "93% (annual same-day delivery rate)"
  rationale: "Seasonal peaks and weather limit achievable rate"
  honest_communication: "Same-day guaranteed except peak days and severe weather"
  alternative_slo: "98% during normal days, best-effort during peaks (tiered SLO)"
```

---

### Example 4: SaaS - Multi-Region Complexity

```yaml
product: "Global storage service (multi-region)"

technical_constraints:
  single_region:
    availability: "99.99% achievable (redundancy within region)"
    cost: "Low (no cross-region replication)"

  multi_region:
    availability: "99.999% achievable (auto-failover across regions)"
    cost: "5x higher (bandwidth, replication, orchestration)"
    complexity: "10x operational burden (debugging cross-region issues)"

technical_ceiling_calculation:
  single_region_ceiling: "99.99% (4 nines)"
  multi_region_ceiling: "99.999% (5 nines)"

  team_maturity:
    current: "Junior SRE team (limited 24/7 coverage)"
    realistic: "99.9% with current team (3 nines)"
    aspirational: "99.99% with mature team + tooling (4 nines)"

slo_implication:
  threshold: "99.9% (current team capability)"
  rationale: "Team maturity limits achievable SLO - can't promise more than we can deliver"
  evolution_path: "Start 99.9% → 99.99% (year 2) → 99.999% (year 3)"
  honest_approach: "Promise based on current capability, not aspirational"
```

---

## Question 4: What's Legally Mandated?

**Question**: Are there regulatory or compliance requirements that set a minimum threshold?

### Decision Formula

```yaml
compliance_analysis:

  step_1_identify_regulations:
    - "Industry regulations (FINRA, HIPAA, PCI-DSS, SOX, GDPR)"
    - "Contractual obligations (customer SLAs, vendor contracts)"
    - "Internal policies (corporate governance, audit requirements)"

  step_2_determine_floor:
    rule: "Compliance sets MINIMUM, not maximum"
    formula: "SLO_threshold >= compliance_floor"

  step_3_exceed_if_needed:
    principle: "Can go higher for business reasons, but cannot go lower"

  examples:
    sox_financial_accuracy:
      mandate: "100% accuracy for financial data"
      implication: "Cannot compromise on accuracy SLO"

    hipaa_patient_data:
      mandate: "Audit trail required for all access"
      implication: "100% logging SLO (cannot drop logs)"

    gdpr_deletion:
      mandate: "Delete personal data within 30 days of request"
      implication: ">95% deletion workflow completion"
```

---

### Example 1: Data Mesh - SOX Financial Reporting

```yaml
product: "Finance.general_ledger (accounting data)"

regulatory_mandates:
  regulation: "Sarbanes-Oxley Act (SOX)"

  requirements:
    accuracy: "100% - financial statements must be accurate"
    completeness: "100% - all transactions must be recorded"
    auditability: "100% - all changes logged with timestamp and user"
    retention: "7 years - data must be retained and accessible"

  penalties_for_violation:
    financial: "SEC fines up to $25M"
    criminal: "Executives face prison (up to 20 years)"
    corporate: "Delisting from stock exchange"

compliance_floor:
  accuracy_slo: ">99.99% (allow 0.01% error rate for technical issues)"
  completeness_slo: ">99.99% (must capture virtually all transactions)"
  audit_log_slo: "100% (cannot lose audit records - legal requirement)"
  data_retention_slo: "100% (cannot lose data within 7-year window)"

slo_implication:
  cannot_go_below: "99.99% (compliance floor)"
  business_decision: "Set 99.99% even if business only needs 95% (compliance mandates)"
  cost_acceptance: "Must pay premium for compliance - no choice"
  architecture_implication: "Event sourcing required (immutable audit trail)"
```

---

### Example 2: Data Mesh - GDPR Personal Data

```yaml
product: "Customer.profile (contains PII - emails, names)"

regulatory_mandates:
  regulation: "GDPR (General Data Protection Regulation)"

  requirements:
    deletion_timeliness: "Delete PII within 30 days of request (right to be forgotten)"
    encryption: "PII must be encrypted at rest and in transit"
    access_logging: "Log all access to PII (audit trail)"
    breach_notification: "Report breaches within 72 hours"

  penalties_for_violation:
    financial: "€20M or 4% of global revenue (whichever is higher)"
    reputational: "Public disclosure of breach (brand damage)"

compliance_floor:
  deletion_slo: ">95% (requests processed within 30 days)"
  encryption_slo: "100% (all PII encrypted - no exceptions)"
  access_logging_slo: "100% (cannot drop audit logs - legal requirement)"
  breach_detection_slo: ">99% (must detect within 72 hours)"

slo_implication:
  cannot_go_below: "95% deletion, 100% encryption, 100% logging"
  business_flexibility: "Can set higher (e.g., 99% deletion) but not lower"
  cost_justification: "Encryption/logging overhead required - no alternative"
```

---

### Example 3: Healthcare - HIPAA Patient Records

```yaml
product: "Patient electronic health records (EHR system)"

regulatory_mandates:
  regulation: "HIPAA (Health Insurance Portability and Accountability Act)"

  requirements:
    data_integrity: "Patient records must be accurate and complete"
    access_control: "Only authorized personnel can access records"
    audit_trail: "Log all access attempts (who, when, what)"
    availability: "Emergency access required (life-or-death scenarios)"
    retention: "6 years minimum (varies by state)"

  penalties_for_violation:
    financial: "Up to $1.5M per violation category per year"
    criminal: "Prison for willful neglect (up to 10 years)"

compliance_floor:
  availability_slo: ">99.9% (emergency room needs access 24/7)"
  audit_logging_slo: "100% (legal requirement - cannot drop logs)"
  data_integrity_slo: ">99.99% (patient safety - errors can kill)"
  access_control_slo: "100% (unauthorized access = breach = fine)"

slo_implication:
  cannot_go_below: "99.9% availability, 100% logging, 99.99% integrity"
  life_safety: "Higher than typical business apps (patient lives depend on it)"
  cost_irrelevant: "Must achieve compliance regardless of cost (legal mandate)"
```

---

### Example 4: E-Commerce - PCI-DSS Payment Data

```yaml
product: "Payment processing system (stores credit card data)"

regulatory_mandates:
  regulation: "PCI-DSS (Payment Card Industry Data Security Standard)"

  requirements:
    encryption: "Card data encrypted at rest and in transit (AES-256)"
    access_logging: "All access to card data logged"
    data_retention: "Delete card data after transaction (unless required)"
    penetration_testing: "Quarterly security scans + annual penetration tests"

  penalties_for_violation:
    financial: "Fines from card networks ($5K-$100K per month)"
    operational: "Loss of ability to process cards (business shutdown)"
    reputational: "Public breach disclosure (customer loss)"

compliance_floor:
  encryption_slo: "100% (all card data encrypted - no exceptions)"
  logging_slo: "100% (audit trail required - cannot drop logs)"
  tokenization_slo: ">99.99% (failed tokenization = storing plaintext = violation)"
  access_control_slo: "100% (unauthorized access = breach = fine)"

slo_implication:
  cannot_go_below: "100% encryption, 100% logging, 99.99% tokenization"
  business_existential: "Losing PCI compliance = cannot process payments = out of business"
  cost_justified: "Security costs mandatory (not optional)"
```

---

## Question 5: What's the Cost-Quality Trade-off?

**Question**: What does it cost to achieve higher quality? Are there diminishing returns?

### Decision Formula

```yaml
cost_quality_analysis:

  step_1_map_cost_curve:
    pattern: "Exponential - each additional 9 costs more than the last"
    examples:
      - "90% → 95%: 2x cost increase"
      - "95% → 99%: 5x cost increase"
      - "99% → 99.9%: 10x cost increase"
      - "99.9% → 99.99%: 20x cost increase"

  step_2_calculate_value:
    formula: "value_delivered - total_cost = net_value"
    decision_rule: "Increase SLO while net_value > 0"

  step_3_find_optimum:
    optimum_point: "Where marginal cost equals marginal value"
    examples:
      - "If use case values 99% at $10K but costs $20K → don't do it"
      - "If use case values 95% at $5K and costs $2K → do it"
```

---

### Example 1: Data Mesh - Bookings Product (Cost Curve)

```yaml
product: "Bookings.realtime_stream"

cost_by_slo_level:

  80_percent_slo:
    architecture: "Daily batch (simple ETL, cron job)"
    infrastructure: "Single EC2 instance + RDS"
    monitoring: "Basic CloudWatch"
    total_cost: "$500/month"
    downtime_allowed: "6 days/month (20%)"

  90_percent_slo:
    architecture: "Hourly batch (orchestrated ETL)"
    infrastructure: "Auto-scaling EC2 + RDS read replica"
    monitoring: "CloudWatch + PagerDuty"
    total_cost: "$1,000/month (2x increase)"
    downtime_allowed: "3 days/month (10%)"

  95_percent_slo:
    architecture: "15-minute micro-batch (complex orchestration)"
    infrastructure: "Kubernetes cluster + managed Kafka"
    monitoring: "Datadog + on-call rotation"
    total_cost: "$5,000/month (5x increase)"
    downtime_allowed: "1.5 days/month (5%)"

  99_percent_slo:
    architecture: "Real-time streaming (Kafka + Flink)"
    infrastructure: "Multi-AZ Kafka + Flink cluster + RDS HA"
    monitoring: "Full observability stack + 24/7 on-call"
    total_cost: "$20,000/month (20x increase)"
    downtime_allowed: "7 hours/month (1%)"

  99_9_percent_slo:
    architecture: "Multi-region streaming (active-active)"
    infrastructure: "Multi-region Kafka + Flink + auto-failover"
    monitoring: "Enterprise observability + dedicated SRE team"
    total_cost: "$100,000/month (100x increase)"
    downtime_allowed: "43 minutes/month (0.1%)"

cost_curve_observation:
  pattern: "Each 9 costs exponentially more"
  80→90: "2x cost for 10% improvement"
  90→95: "5x cost for 5% improvement"
  95→99: "4x cost for 4% improvement"
  99→99.9: "5x cost for 0.9% improvement"

  law_of_diminishing_returns: "Cost rises faster than quality improvement"
```

---

### Value Analysis per Use Case

```yaml
use_case_1_repricing_engine:
  quality_need: "99.9% (real-time critical)"

  value_calculation:
    revenue_impact: "$50K/month (dynamic pricing optimization)"
    failure_cost: "$10K/month lost (if SLO <99%)"
    total_value: "$60K/month"

  cost_benefit:
    99_9_percent_cost: "$100K/month"
    value_delivered: "$60K/month"
    net_value: "-$40K/month (NEGATIVE)"

  decision: "99% SLO (not 99.9%)"
  rationale: "99% costs $20K, delivers $50K value → net value +$30K"
  trade_off_accepted: "Accept 7 hours/month downtime (vs 43 minutes) to save $80K"

use_case_2_finance_daily_report:
  quality_need: "90% (batch use case)"

  value_calculation:
    compliance_requirement: "Must have report (SOX)"
    failure_cost: "Low (1-day delay acceptable 10% of time)"
    total_value: "$5K/month (enables compliance)"

  cost_benefit:
    90_percent_cost: "$1K/month"
    value_delivered: "$5K/month"
    net_value: "+$4K/month (POSITIVE)"

  decision: "90% SLO (don't over-invest)"
  rationale: "95% costs $5K (equal to value) - not worth it"
  trade_off_accepted: "Accept 3 days/month miss (finance can tolerate)"

use_case_3_ml_forecasting:
  quality_need: "80% (experimental)"

  value_calculation:
    business_value: "$2K/month (capacity planning optimization)"
    failure_cost: "Very low (model degrade gracefully)"
    total_value: "$2K/month"

  cost_benefit:
    80_percent_cost: "$500/month"
    value_delivered: "$2K/month"
    net_value: "+$1.5K/month (POSITIVE)"

  decision: "80% SLO (economy tier)"
  rationale: "ML tolerates low quality - optimize for cost"
  trade_off_accepted: "6 days/month downtime acceptable (not real-time critical)"
```

---

### Example 2: SaaS - Cloud Storage Tiers

```yaml
product: "Storage service (availability tiers)"

cost_by_availability_tier:

  99_percent_availability:
    architecture: "Single-region, standard replication (3 copies)"
    infrastructure: "S3 Standard equivalent"
    cost: "$0.023/GB/month"
    downtime: "7.3 hours/month (432 minutes)"

  99_9_percent_availability:
    architecture: "Multi-AZ, synchronous replication"
    infrastructure: "S3 with cross-AZ replication"
    cost: "$0.035/GB/month (1.5x)"
    downtime: "43.2 minutes/month"

  99_99_percent_availability:
    architecture: "Multi-region, active-active"
    infrastructure: "S3 with cross-region replication + Route53 failover"
    cost: "$0.115/GB/month (5x)"
    downtime: "4.3 minutes/month"

customer_value_analysis:

  photo_app_customer:
    use_case: "User-uploaded photos (frequently accessed)"
    downtime_cost: "$1,000/minute (users can't access photos)"
    acceptable_downtime: "<45 minutes/month"

    decision:
      chosen_tier: "99.9% ($0.035/GB)"
      cost: "100TB × $0.035 = $3,500/month"
      downtime_cost: "43 min × $1K = $43K/month cost avoided"
      net_value: "+$39.5K/month (worth it)"

    why_not_99_99:
      cost: "$11,500/month (3.3x more expensive)"
      additional_downtime_avoided: "39 minutes (43 → 4 minutes)"
      value_of_39_minutes: "$39K"
      net_value: "+$27K vs 99.9% (marginal improvement not worth 3.3x cost)"

  backup_system_customer:
    use_case: "Monthly database backups (rarely accessed)"
    downtime_cost: "$100/hour (only matters during disaster recovery)"
    acceptable_downtime: "<8 hours/month"

    decision:
      chosen_tier: "99% ($0.023/GB)"
      cost: "50TB × $0.023 = $1,150/month"
      downtime_cost: "7.3 hours × $100 = $730/month"
      net_value: "Acceptable (disaster recovery rare)"

    why_not_99_9:
      cost: "$1,750/month (1.5x more expensive)"
      downtime_avoided: "6.5 hours (saves $650)"
      net_value: "Not worth it (pay $600 more to save $650 - marginal)"
```

---

## Putting It All Together: Decision Matrix

**Framework**: Answer all 5 questions, then determine SLO threshold

```yaml
decision_matrix:

  step_1_collect_answers:
    q1_cost_of_failure: "Catastrophic | Severe | Moderate | Low"
    q2_use_case: "Realtime-critical | Operational | Batch | Exploratory"
    q3_technical_ceiling: "99.99% | 99% | 95% | 90%"
    q4_compliance_floor: "99.99% | 99% | 95% | None"
    q5_cost_justified: "99.9% | 99% | 95% | 90%"

  step_2_apply_formula:
    slo_threshold: |
      MAX(
        compliance_floor,
        MIN(
          technical_ceiling,
          cost_justified_level,
          use_case_requirement
        )
      )

  step_3_validate:
    - "Is SLO above compliance floor? (legal requirement)"
    - "Is SLO below technical ceiling? (achievable)"
    - "Is cost justified by value? (economic)"
    - "Does SLO meet use case need? (functional)"
```

---

### Example: Finance Daily Revenue Report

```yaml
product: "Finance.daily_revenue_report"

question_answers:
  q1_cost_of_failure:
    answer: "Catastrophic (SEC violation, $10M fine)"
    implication: "Needs high SLO (>99%)"

  q2_use_case:
    answer: "Batch (daily report for 9am meeting)"
    implication: "Can tolerate delays (90-95% acceptable)"

  q3_technical_ceiling:
    answer: "95% (depends on payment processor 99% uptime)"
    implication: "Cannot exceed 95%"

  q4_compliance_floor:
    answer: "99.99% accuracy (SOX requirement)"
    implication: "Accuracy must be >99.99%, but freshness can be lower"

  q5_cost_justified:
    answer: "99% costs $5K, 95% costs $2K, 90% costs $1K"
    implication: "95% optimal (cost-effective given technical ceiling)"

formula_application:
  compliance_floor: "None for freshness (SOX requires accuracy, not timeliness)"
  technical_ceiling: "95%"
  cost_justified: "95%"
  use_case_requirement: "90%"

  result: |
    MAX(
      None,               # No compliance floor for freshness
      MIN(
        95%,              # Technical ceiling
        95%,              # Cost justified
        90%               # Use case requirement
      )
    ) = 90%

final_decision:
  freshness_slo: "90% (by 9am daily)"
  accuracy_slo: "99.99% (SOX compliance)"

  rationale:
    - "Use case only needs 90% freshness (batch tolerance)"
    - "Technical ceiling is 95% (could do better, but don't need to)"
    - "Cost optimized at 90% ($1K vs $5K for 99%)"
    - "Accuracy is separate SLO (compliance mandates 99.99%)"

  tiered_slos:
    note: "Different quality dimensions have different thresholds"
    freshness: "90% (cost-optimized for batch use case)"
    accuracy: "99.99% (compliance floor, non-negotiable)"
    completeness: "95% (acceptable for daily report)"
```

---

## Common Patterns Across Domains

### Pattern 1: Realtime Systems Need High SLO

```yaml
examples:
  - "Repricing engine: 99%+ (decisions happen in real-time)"
  - "Fraud detection: 99.9%+ (fraud must be blocked immediately)"
  - "Same-day shipping: 95%+ (customer expectation)"
  - "Emergency 911 system: 99.999% (lives depend on it)"

formula: "Realtime-critical → 99%+ SLO (cost justified by use case)"
```

---

### Pattern 2: Batch Systems Tolerate Lower SLO

```yaml
examples:
  - "Daily finance report: 90% (can miss 1 day/week)"
  - "Weekly sales analysis: 85% (can miss 1 week/month)"
  - "Monthly grain delivery: 80% (warehouse has buffer stock)"
  - "Annual tax report: 95% (deadline is 1 year, not 1 day)"

formula: "Batch/periodic → 80-95% SLO (cost optimized)"
```

---

### Pattern 3: Compliance Sets Floor (Cannot Go Lower)

```yaml
examples:
  - "Financial data: 99.99% accuracy (SOX mandate)"
  - "PII deletion: 95% within 30 days (GDPR mandate)"
  - "Audit logs: 100% retention (regulatory requirement)"
  - "Payment encryption: 100% (PCI-DSS mandate)"

formula: "Compliance_floor is MINIMUM - can go higher, not lower"
```

---

### Pattern 4: Cost Rises Exponentially

```yaml
cost_curve:
  90_to_95_percent: "2x cost increase"
  95_to_99_percent: "5x cost increase"
  99_to_99_9_percent: "10x cost increase"
  99_9_to_99_99_percent: "20x cost increase"

law_of_diminishing_returns:
  observation: "Each 9 costs exponentially more"
  principle: "Only pay for quality that use case justifies"
  anti_pattern: "Gold-plating (99.99% SLO for batch use case)"
```

---

### Pattern 5: Technical Ceiling Limits Achievable

```yaml
examples:
  - "Upstream API 99% → you can't promise 99.9%"
  - "Batch window constraint → can't promise real-time freshness"
  - "Seasonal capacity → can't promise 99% during peaks"
  - "Junior team → can't promise 99.99% without mature ops"

principle: "Never promise SLO above technical capability (under-promise, over-deliver)"
```

---

## Framework Application Checklist

**Before setting SLO thresholds**:

```yaml
checklist:
  q1_cost_of_failure:
    - [ ] Identified failure scenarios
    - [ ] Quantified financial, reputational, legal costs
    - [ ] Determined severity (catastrophic, severe, moderate, low)

  q2_use_case:
    - [ ] Documented consumer use cases
    - [ ] Identified quality requirements (freshness, accuracy, availability)
    - [ ] Determined tolerance for degradation

  q3_technical_feasibility:
    - [ ] Mapped upstream dependencies and their SLAs
    - [ ] Assessed infrastructure capabilities
    - [ ] Calculated technical ceiling with safety buffer

  q4_compliance:
    - [ ] Identified applicable regulations
    - [ ] Documented mandatory requirements
    - [ ] Determined compliance floor (minimum acceptable)

  q5_cost_quality:
    - [ ] Mapped cost curve (cost by SLO level)
    - [ ] Calculated value delivered by each SLO tier
    - [ ] Found economic optimum (marginal cost = marginal value)

  final_decision:
    - [ ] Applied formula: MAX(compliance_floor, MIN(ceiling, cost_justified, use_case))
    - [ ] Validated SLO is achievable (below technical ceiling)
    - [ ] Validated SLO is compliant (above compliance floor)
    - [ ] Validated SLO is economically justified (net value positive)
    - [ ] Documented rationale (why this threshold, not higher/lower)
```

---

## Anti-Patterns (What NOT to Do)

### ❌ Anti-Pattern 1: Copying Someone Else's SLO

```yaml
bad_example:
  approach: "Google uses 99.9% SLO, so we should too"
  problem: "Google has different use cases, costs, constraints than you"
  result: "Over-invest (99.9% when 90% sufficient) or under-invest (90% when 99.9% required)"

correct_approach:
  principle: "Answer the 5 questions for YOUR context"
  result: "SLO tailored to your use case, not copied from others"
```

---

### ❌ Anti-Pattern 2: Promising Above Technical Ceiling

```yaml
bad_example:
  approach: "Promise 99% SLO when upstream dependency only 95%"
  problem: "Cannot achieve what you promise (guaranteed to fail SLO)"
  result: "Customer dissatisfaction, breach of contract, reputation damage"

correct_approach:
  principle: "Never promise SLO above technical ceiling"
  example: "If upstream 95%, promise 90% (leave buffer for your failures)"
```

---

### ❌ Anti-Pattern 3: Ignoring Compliance Floor

```yaml
bad_example:
  approach: "Set 90% SLO when SOX requires 99.99% accuracy"
  problem: "Legal violation (compliance not optional)"
  result: "SEC fines, audit failures, executive liability"

correct_approach:
  principle: "Compliance sets MINIMUM - cannot go below"
  example: "SOX requires 99.99% → set 99.99% regardless of cost"
```

---

### ❌ Anti-Pattern 4: Gold-Plating (Over-Investment)

```yaml
bad_example:
  approach: "Build 99.9% SLO for experimental ML model"
  problem: "ML tolerates 80% SLO - paying 10x for unnecessary quality"
  result: "Wasted budget, delayed delivery, over-engineering"

correct_approach:
  principle: "Match SLO to use case need (don't over-invest)"
  example: "ML experiment → 80% SLO (economy tier) saves $95K/month"
```

---

### ❌ Anti-Pattern 5: Prescriptive Numbers (Framework Error)

```yaml
bad_example:
  framework_says: "Data mesh SLO must be 90%"
  problem: "Every organization has different use cases, costs, constraints"
  result: "Framework becomes organization-specific, not universal"

correct_approach:
  framework_says: "Answer the 5 questions to determine YOUR SLO threshold"
  result: "Framework is universal, SLO is context-specific"
```

---

## Summary

**Framework Principle**:
> SLO thresholds are context-specific, not universal. Answer 5 questions to determine the right threshold for YOUR context.

**The 5 Questions**:
1. Cost of failure? (Risk analysis)
2. Consumer use case? (Requirements analysis)
3. Technical feasibility? (Capability analysis)
4. Legal mandate? (Compliance analysis)
5. Cost-quality trade-off? (Economic analysis)

**The Formula**:
```
SLO_threshold = MAX(
  compliance_floor,
  MIN(
    technical_ceiling,
    cost_justified_level,
    use_case_requirement
  )
)
```

**The Principle**:
- Meet compliance minimum (legal requirement)
- Don't exceed technical ceiling (achievable)
- Don't pay for quality beyond use case need (economical)
- Balance all constraints (holistic decision)

**Universal Across Domains**:
- Data mesh (Bookings SLO)
- E-commerce (Shipping SLO)
- SaaS (Storage availability SLO)
- Healthcare (EHR availability SLO)
- Medieval markets (Grain delivery SLO)

**Framework applies everywhere quality commitments exist.**

---

**End of Document**
