# Recommendation Engine Framework

**Purpose**: Define HOW to filter options, score fit, calculate TCO, and generate context-specific recommendations based on constraint profiles from discovery.

**Usage**: Claude reads this framework after discovery phase, applies rules to user's answers, generates recommendations.

**Critical Insight**: Same capability, different constraints = different technology choice. This framework makes trade-offs explicit and justifiable.

---

## Recommendation Process (6 Steps)

```yaml
step_1_filter_by_hard_constraints:
  input: "All possible technology options"
  process: "Remove options that violate non-negotiable constraints"
  output: "Feasible options"

step_2_score_by_fit:
  input: "Feasible options"
  process: "Score each option on 5 criteria (0-100 each)"
  output: "Options with fit scores"

step_3_calculate_contextualized_tco:
  input: "User's scale + volume"
  process: "Calculate 3-year TCO for THEIR specific scale"
  output: "Actual costs (not generic ranges)"

step_4_apply_decision_rules:
  input: "Scored options + TCO + constraint profile"
  process: "Apply if-then rules to choose winner"
  output: "Recommended option"

step_5_generate_rationale:
  input: "Recommendation + constraint profile"
  process: "Explain WHY given THEIR constraints"
  output: "Rationale with trade-offs"

step_6_identify_alternative_scenarios:
  input: "Recommendation + runner-up options"
  process: "What would change recommendation?"
  output: "IF X changes THEN reconsider Y"
```

---

## Step 1: Filter by Hard Constraints

**Purpose**: Remove options that violate non-negotiable requirements

### Filter Rules

```yaml
filter_rules:

  rule_1_no_operational_team:
    IF: operational_team == "none" AND learning_time == "unavailable"
    THEN: exclude ["Self-hosted Kubernetes", "Self-hosted Kafka cluster", "Complex distributed systems"]
    REASON: "Cannot operate distributed systems without SRE team and no time to learn"

  rule_2_time_critical:
    IF: deadline <= "3 months" AND consequence == "critical"
    THEN: exclude ["Build custom (12+ months)", "Self-hosted complex systems (6+ months)"]
    REASON: "Timeline requires ready-made solutions"

  rule_3_no_kubernetes_skills:
    IF: kubernetes_experts == 0 AND learning_appetite == "low"
    THEN: exclude ["Any Kubernetes-based deployment"]
    REASON: "3+ month learning curve for K8s, team can't/won't learn"

  rule_4_compliance_mandate:
    IF: compliance == "SOX" OR compliance == "HIPAA"
    THEN: exclude ["Solutions without compliance certifications"]
    REASON: "Regulatory requirements mandate certified solutions"

  rule_5_data_residency:
    IF: data_residency == "must_stay_in_EU"
    THEN: exclude ["US-only cloud services", "Solutions without EU regions"]
    REASON: "Legal requirement for data location"

  rule_6_on_premise_only:
    IF: cloud_allowed == false
    THEN: exclude ["All SaaS", "All cloud-managed services"]
    REASON: "Must self-host on-premise"

  rule_7_budget_floor:
    IF: budget < "$1M/year"
    THEN: exclude ["Enterprise SaaS with >$500K annual minimum"]
    REASON: "Cannot afford high-end enterprise solutions"

  rule_8_availability_requirement:
    IF: availability == "zero_tolerance" AND operational_team == "none"
    THEN: exclude ["Self-hosted options"]
    REASON: "24/7 SLA requires managed service or dedicated SRE team"

  rule_9_technology_mandate:
    IF: mandates == ["must_use_AWS"]
    THEN: exclude ["Azure-only", "GCP-only", "On-premise only solutions"]
    REASON: "Leadership mandate constrains options"

  rule_10_vendor_lock_in_fear:
    IF: primary_fear == "vendor_lock_in"
    THEN: deprioritize ["Proprietary SaaS", "Cloud-specific services"]
    NOTE: "Deprioritize, not exclude - still an option but needs strong justification"
```

---

## Step 2: Score by Fit Criteria

**Purpose**: Quantify how well each option matches constraints

### Scoring Criteria (5 dimensions)

```yaml
scoring_framework:

  criterion_1_time_to_market_fit:
    weight: 30  # Most critical for startups, less for enterprises
    calculation: |
      time_to_production = option_setup_time
      deadline_slack = user_deadline - time_to_production

      IF deadline_slack >= 6_months:
        score = 100  # Plenty of time
      ELIF deadline_slack >= 3_months:
        score = 90   # Comfortable
      ELIF deadline_slack >= 1_month:
        score = 70   # Tight but doable
      ELIF deadline_slack >= 0:
        score = 50   # Very tight
      ELSE:
        score = 0    # Can't meet deadline (should be filtered already)

  criterion_2_team_capability_fit:
    weight: 25  # Can we actually use this?
    calculation: |
      IF option_requires_skills IN team_existing_skills:
        score = 100  # Team already knows it
      ELIF option_requires_skills CAN_LEARN_IN deadline:
        score = 70   # Learnable in time
      ELIF hiring_capacity == "can_hire" AND can_hire_in_deadline:
        score = 60   # Can hire for it
      ELSE:
        score = 30   # Skill gap too large
      
      # Adjust for team size
      IF team_size < 10 AND option_requires > 1_dedicated_person:
        score *= 0.5  # Small team can't spare multiple people

  criterion_3_operational_fit:
    weight: 25  # Can we run this?
    calculation: |
      IF option_is_managed_saas:
        score = 100  # Vendor handles everything
      ELIF operational_team == "dedicated_SRE" AND option_complexity == "high":
        score = 90   # SRE team can handle
      ELIF operational_team == "1-2_people" AND option_complexity == "medium":
        score = 70   # Manageable
      ELIF operational_team == "developers" AND option_complexity == "low":
        score = 60   # Developers can handle simple systems
      ELIF operational_team == "none":
        score = 20   # Cannot self-host (should be filtered)
      
      # Adjust for on-call burden
      IF oncall_setup == "informal" AND option_adds_oncall:
        score *= 0.7  # Penalize adding to already stressed on-call

  criterion_4_cost_fit:
    weight: 15  # Does it fit budget?
    calculation: |
      option_annual_cost = calculated_TCO_for_user_scale
      budget_annual = user_budget

      cost_percentage = option_annual_cost / budget_annual

      IF cost_percentage <= 0.05:
        score = 100  # <5% of budget, easily affordable
      ELIF cost_percentage <= 0.10:
        score = 90   # 5-10%, manageable
      ELIF cost_percentage <= 0.20:
        score = 70   # 10-20%, significant but justifiable
      ELIF cost_percentage <= 0.30:
        score = 50   # 20-30%, expensive
      ELSE:
        score = 30   # >30%, too expensive (should be filtered)

  criterion_5_risk_fit:
    weight: 5   # Matches risk tolerance?
    calculation: |
      IF tech_preference == "boring" AND option_maturity == "proven":
        score = 100  # Perfect match
      ELIF tech_preference == "boring" AND option_maturity == "new":
        score = 40   # Mismatch (risky for conservative team)
      ELIF tech_preference == "bleeding_edge" AND option_maturity == "new":
        score = 100  # Match
      ELIF tech_preference == "bleeding_edge" AND option_maturity == "proven":
        score = 80   # Boring choice for innovative team (OK but not ideal)
      
      # Adjust for past trauma
      IF past_trauma MENTIONS option_type:
        score *= 0.5  # Penalize options similar to past failures

total_score_calculation: |
  total = (
    time_fit * 0.30 +
    capability_fit * 0.25 +
    operational_fit * 0.25 +
    cost_fit * 0.15 +
    risk_fit * 0.05
  )
  
  # Adjust weights by organization type
  IF stage == "startup":
    time_weight = 0.40  # Time more critical
    cost_weight = 0.20  # Cost more critical
  ELIF stage == "enterprise":
    operational_weight = 0.35  # Operations more critical
    time_weight = 0.15  # Time less critical
```

---

## Step 3: Calculate Contextualized TCO

**Purpose**: Calculate 3-year total cost of ownership for THEIR specific scale (not generic ranges)

### TCO Components

```yaml
tco_calculation:

  component_1_license_fees:
    managed_saas: "vendor_price(user_scale)"
    self_hosted_commercial: "license_cost(user_scale)"
    open_source: "$0"
    
  component_2_infrastructure_cost:
    managed_saas: "$0 (included in SaaS price)"
    self_hosted: "compute + storage + networking for user_volume"
    
    calculation_example:
      user_volume: "10M records, 1000 queries/day"
      compute: "2× m5.large EC2 ($140/month × 2) = $280/month"
      storage: "500 GB EBS ($50/month)"
      networking: "$30/month"
      total_infrastructure: "$360/month = $4,320/year"
  
  component_3_labor_operational:
    managed_saas: "$0 (vendor operates)"
    self_hosted_simple: "0.25 FTE × $150K = $37.5K/year"
    self_hosted_kubernetes: "0.5-1 FTE × $150K = $75-150K/year"
    self_hosted_complex_distributed: "1-2 FTE × $150K = $150-300K/year"
    
  component_4_labor_learning_curve:
    year_1_only: true
    calculation: |
      IF team_knows_tech:
        learning_cost = $0
      ELSE:
        learning_time_per_engineer = 3_months  # average
        engineers_learning = 2  # typically
        productivity_loss = 50%  # while learning
        engineer_cost = $150K/year = $12.5K/month
        
        learning_cost = (
          engineers_learning × 
          learning_time_per_engineer × 
          productivity_loss × 
          engineer_cost
        )
        = 2 × 3 × 0.5 × $12.5K = $37.5K one-time
  
  component_5_support_contract:
    community_support: "$0"
    bronze_support: "$10-50K/year"
    platinum_support: "$100-500K/year"
    
  component_6_maintenance:
    managed_saas: "$0 (vendor handles)"
    self_hosted: "0.1-0.3 FTE × $150K = $15-45K/year"
    
  component_7_monitoring_observability:
    managed_saas: "$0 (included) or $5K/year (external APM)"
    self_hosted: "$10-30K/year (Datadog, PagerDuty, etc.)"
    
  component_8_disaster_recovery:
    managed_saas: "$0 (vendor handles multi-region)"
    self_hosted: "$10-100K/year (backups, multi-region setup)"
    
  component_9_security_compliance:
    managed_saas: "$0 (vendor certified)"
    self_hosted: "$20-100K/year (audits, certifications, tooling)"
    
  component_10_exit_cost:
    amortized_over_3_years: true
    calculation: |
      exit_cost = data_migration_effort + application_rewrite_effort
      probability_of_exit = 0.3  # 30% chance of switching in 3 years
      amortized_annual = (exit_cost × probability_of_exit) / 3
      
    examples:
      postgres: "$50K migration (portable SQL) × 0.3 / 3 = $5K/year"
      aws_lambda: "$500K rewrite (proprietary) × 0.3 / 3 = $50K/year"

total_3_year_tco: |
  year_1_total = (
    license_fees +
    infrastructure +
    operational_labor +
    learning_curve +  # Year 1 only
    support +
    maintenance +
    monitoring +
    disaster_recovery +
    security_compliance +
    exit_cost_amortized
  )
  
  year_2_total = year_1_total - learning_curve  # No learning cost
  year_3_total = year_2_total
  
  total_3_year = year_1_total + year_2_total + year_3_total
```

### Example TCO Calculation

```yaml
example_data_catalog:

  user_scale:
    domains: 50
    data_products: 200
    users: 100
    queries_per_day: 1000
    records: "10M metadata records"

  option_1_atlan_saas:
    license: "$36K/year (based on 100 users)"
    infrastructure: "$0"
    operational_labor: "$0"
    learning_curve: "$0 (no learning needed)"
    support: "$0 (included platinum)"
    maintenance: "$0"
    monitoring: "$0"
    disaster_recovery: "$0"
    security_compliance: "$0"
    exit_cost: "$30K rewrite × 0.3 / 3 = $3K/year"
    
    year_1: "$36K + $3K = $39K"
    year_2: "$36K + $3K = $39K"
    year_3: "$36K + $3K = $39K"
    total_3_year: "$117K"

  option_2_openmetadata_docker:
    license: "$0 (open source)"
    infrastructure: "$360/month = $4,320/year"
    operational_labor: "0.5 FTE × $150K = $75K/year"
    learning_curve: "$37.5K (year 1 only)"
    support: "$0 (community)"
    maintenance: "$15K/year"
    monitoring: "$10K/year (Datadog)"
    disaster_recovery: "$15K/year"
    security_compliance: "$20K/year"
    exit_cost: "$50K migration × 0.3 / 3 = $5K/year"
    
    year_1: "$4K + $75K + $37.5K + $15K + $10K + $15K + $20K + $5K = $181.5K"
    year_2: "$139K (no learning cost)"
    year_3: "$139K"
    total_3_year: "$459.5K"

  winner: "Atlan SaaS ($117K < $459.5K over 3 years)"
  insight: "Open source NOT cheaper when labor costs included"
```

---

## Step 4: Apply Decision Rules

**Purpose**: Use if-then logic to choose winner based on constraint profile

### Decision Rules

```yaml
decision_rules:

  rule_1_time_critical_existential:
    priority: 1  # Highest priority
    IF:
      - deadline_consequence == "company_dies"
      - time_to_market_delta > "2 months between options"
    THEN:
      recommendation: "Fastest option (usually managed SaaS)"
      override: "Even if most expensive - time to market is existential"
    
  rule_2_no_operational_capacity:
    priority: 2
    IF:
      - operational_team == "none"
      - availability_requirement == "24/7"
    THEN:
      recommendation: "Managed service only"
      reason: "Cannot self-host reliably without ops team"
    
  rule_3_hidden_costs_exceed_saas:
    priority: 3
    IF:
      - self_hosted_tco > saas_tco
      - team_size < 30
    THEN:
      recommendation: "Managed SaaS"
      reason: "Open source labor costs exceed SaaS cash costs at your scale"
    
  rule_4_economies_of_scale:
    priority: 4
    IF:
      - team_size > 100
      - operational_team == "dedicated_SRE_5plus"
      - volume_predictable == true
      - self_hosted_tco < (0.7 × saas_tco)
    THEN:
      recommendation: "Self-hosted"
      reason: "Scale justifies operational investment, 30%+ cost savings"
    
  rule_5_skill_gap_no_time:
    priority: 5
    IF:
      - team_capability_fit < 50
      - deadline < "6 months"
      - learning_appetite == "low"
    THEN:
      recommendation: "Option matching existing skills"
      reason: "No time to learn new technology"
    
  rule_6_compliance_mandate:
    priority: 6
    IF:
      - compliance IN ["SOX", "HIPAA", "PCI-DSS"]
      - option_not_certified == true
    THEN:
      recommendation: "Exclude non-certified options"
      reason: "Regulatory requirement non-negotiable"
    
  rule_7_past_trauma_avoidance:
    priority: 7
    IF:
      - past_trauma MENTIONS option_type
      - trauma_severity == "high"
    THEN:
      recommendation: "Deprioritize similar options"
      reason: "Team has legitimate fear based on past experience"
    
  rule_8_vendor_lock_in_fear:
    priority: 8
    IF:
      - primary_fear == "vendor_lock_in"
      - option_exit_cost > "$500K"
    THEN:
      recommendation: "Prefer portable solutions (open source, standard SQL)"
      reason: "Fear is legitimate - high exit costs create risk"
    
  rule_9_budget_constrained_has_expertise:
    priority: 9
    IF:
      - budget_percentage > 0.20
      - operational_team EXISTS
      - team_capability_fit > 70
    THEN:
      recommendation: "Self-hosted to save cash"
      reason: "Team can handle operations, budget is tight"
    
  rule_10_resume_driven_if_time_permits:
    priority: 10  # Lowest priority
    IF:
      - deadline_slack > "6 months"
      - team_wants_to_learn == true
      - operational_fit > 60
    THEN:
      recommendation: "Allow team preference (learning opportunity)"
      reason: "Have time to learn, good for retention"
```

---

## Step 5: Generate Rationale

**Purpose**: Explain WHY recommendation fits THEIR constraints

### Rationale Template

```yaml
rationale_structure:

  opening: |
    "Given YOUR specific constraints:"
    [List 3-5 most critical constraints]
    
    Example:
      ✓ 5 person team (can't spare FTE on ops)
      ✓ 3 month deadline (need fast time to market)
      ✓ No K8s expertise (rules out complex deployments)
      ✓ Conservative risk profile (prefer proven over DIY)

  why_this_wins: |
    "[Recommended option] wins because:"
    1. [Primary benefit tied to top constraint]
    2. [Secondary benefit tied to second constraint]
    3. [Tertiary benefit]
    
    Example:
      "Atlan (managed SaaS) wins because:
      1. Time: 2 weeks vs 8 weeks (leaves 10 weeks for data mesh work)
      2. Operations: Zero burden (frees up 0.5 FTE = 10% of team)
      3. Cost: $36K < $62K hidden costs of self-hosted
      4. Risk: Proven solution, matches conservative tolerance"

  trade_offs_accepted: |
    "Trade-offs you're accepting:"
    - [Trade-off 1]: [Description]
    - [Trade-off 2]: [Description]
    
    "Why these trade-offs are worth it:"
    [Explanation tied to constraints]
    
    Example:
      "Trade-offs:
      - Higher cash cost: $36K vs $12K infra-only
      - Vendor lock-in: Medium (data export possible but friction)
      - Less customization: Limited to Atlan features
      
      Why worth it:
      Your team is capacity-constrained (5 people) and time-pressured (3 months).
      Operational burden would hurt more than cash cost.
      Can migrate to self-hosted later if constraints change."

  what_makes_this_recommendation_reversible: |
    "Alternative scenarios:"
    
    IF [constraint X changes]:
      THEN: "Reconsider [option Y]"
      BECAUSE: [Reason]
    
    Example:
      "IF deadline extends to 12 months:
        THEN: Reconsider self-hosted OpenMetadata
        BECAUSE: More time to set up operations properly
      
      IF you hire 2 SRE engineers:
        THEN: Self-hosted becomes viable
        BECAUSE: SRE team can handle ops burden
      
      IF need features Atlan doesn't have:
        THEN: May need custom or self-hosted
        BECAUSE: Validate requirements in Atlan trial first"
```

---

## Step 6: Identify Risks and Mitigations

**Purpose**: Surface risks of chosen recommendation, provide mitigations

### Risk Categories

```yaml
risk_identification:

  risk_1_vendor_pricing_lock_in:
    applicable_to: ["Managed SaaS"]
    warning: "Vendor may increase prices once you're locked in"
    mitigation: "Negotiate multi-year contract with price cap or max annual increase"
    
  risk_2_feature_gaps:
    applicable_to: ["All options"]
    warning: "Solution may lack specific features you need"
    mitigation: "Run 2-week POC validating top 5 use cases before committing"
    
  risk_3_operational_burden_underestimated:
    applicable_to: ["Self-hosted"]
    warning: "Operations may take more FTE than estimated"
    mitigation: "Start with managed service, migrate to self-hosted after hiring SRE"
    
  risk_4_learning_curve_longer_than_expected:
    applicable_to: ["New technology"]
    warning: "Learning may take 6 months, not 3"
    mitigation: "Bring in consultant for first 3 months to accelerate ramp-up"
    
  risk_5_hidden_costs_emerge:
    applicable_to: ["Self-hosted"]
    warning: "Disaster recovery, security, compliance costs not estimated"
    mitigation: "Add 30% buffer to TCO estimate"
    
  risk_6_vendor_support_quality:
    applicable_to: ["Managed SaaS"]
    warning: "Support may be slower than advertised"
    mitigation: "Test support during trial (file 2-3 tickets, measure response)"
    
  risk_7_exit_cost_higher_than_estimated:
    applicable_to: ["Proprietary SaaS", "Cloud-specific services"]
    warning: "Migration may cost $1M, not $500K"
    mitigation: "Document exit strategy before committing, maintain portable data formats"
```

---

## Complete Recommendation Output

```yaml
recommendation_report:

  constraint_profile:
    # From discovery phase

  capability_needed: "Metadata catalog and search"
  archetype: "Marketplace (Level 2)"

  filtered_options:
    build_custom:
      filtered_reason: "12 month timeline exceeds 3 month deadline"
    
    self_hosted_kubernetes:
      filtered_reason: "Team has 0 K8s experts, no time to learn (3 month deadline)"

  remaining_options:
    
    option_1_atlan_saas:
      time_to_market: "2 weeks"
      tco_3_year: "$117K"
      fit_scores:
        time_fit: 100
        capability_fit: 100
        operational_fit: 100
        cost_fit: 80
        risk_fit: 100
        total: 96
      
    option_2_openmetadata_docker:
      time_to_market: "6-8 weeks"
      tco_3_year: "$459K"
      fit_scores:
        time_fit: 70
        capability_fit: 60
        operational_fit: 40
        cost_fit: 50
        risk_fit: 60
        total: 56

  recommendation:
    chosen_option: "Atlan (Managed SaaS)"
    
    rationale: |
      Given YOUR specific constraints:
        ✓ 5 person team (can't spare 0.5 FTE on catalog ops)
        ✓ 3 month deadline (need fast time to market)
        ✓ No K8s expertise (rules out self-hosted K8s)
        ✓ Conservative risk profile (prefer proven over DIY)
      
      Atlan wins because:
        1. Time: 2 weeks vs 6-8 weeks (leaves more time for data mesh work)
        2. Operations: Zero burden (frees up 0.5 FTE)
        3. Cost: $39K/year < $139K/year self-hosted (paradoxically cheaper)
        4. Risk: Proven, matches conservative tolerance
    
    trade_offs_accepted:
      higher_cash_cost: "$36K vs $12K infra-only"
      vendor_lock_in: "Medium (data export possible)"
      less_customization: "Limited to Atlan features"
      
      why_worth_it: |
        Your team is capacity-constrained (5 people) and time-pressured (3 months).
        Operational burden would hurt more than cash cost.
        Can migrate to self-hosted later if constraints change.
    
    alternatives_if_constraints_change:
      - IF: "Deadline extends to 12 months"
        THEN: "Reconsider self-hosted OpenMetadata"
        BECAUSE: "More time to set up operations properly"
      
      - IF: "You hire 2 SRE engineers"
        THEN: "Self-hosted becomes viable"
        BECAUSE: "SRE team can handle ops burden"
      
      - IF: "Costs become >10% of budget"
        THEN: "Migrate to self-hosted to save money"
        BECAUSE: "Scale makes ops investment worthwhile"
    
    risks_to_watch:
      - risk: "Atlan pricing increases"
        mitigation: "Negotiate multi-year contract with price cap"
      
      - risk: "Feature gaps"
        mitigation: "Run 2-week POC validating top 5 use cases"
      
      - risk: "Vendor support quality"
        mitigation: "Test support during trial (file tickets)"
    
    next_steps:
      1. "Start Atlan free trial (2 weeks)"
      2. "Validate top 5 use cases"
      3. "Test support (file 2-3 tickets)"
      4. "If validates → negotiate contract"
      5. "If gaps → reassess (maybe self-hosted)"
```

---

**End of Recommendation Engine Framework**
