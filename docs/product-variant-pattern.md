# Product Variant Pattern (Marketplace Archetype)

**Pattern Type**: Marketplace archetype - universal pattern across all marketplace implementations

**Reality Pattern**: Markets offer the same commodity in multiple configurations/variants optimized for different buyer needs, with different service levels and pricing tiers.

---

## The Universal Pattern

```yaml
Marketplace:

  BusinessEntity:
    description: "The underlying commodity or service being offered (category level)"
    examples: ["Binder (e-commerce)", "Bookings (data mesh)", "Wheat (medieval market)", "Storage (SaaS)"]

  ProductVariant:
    description: "Specific configuration/packaging of the entity (offering level)"
    examples: ["Colored with sections", "Realtime stream", "Fresh daily grain", "Hot storage"]

  ServiceLevel:
    description: "Delivery/access guarantees and quality commitments (SLA level)"
    examples: ["Same day shipping", "Freshness <5 sec", "Delivered at dawn", "99.99% uptime"]

  Customer:
    description: "Buyer with specific use case needs"
    decision_criteria: "Chooses variant based on: required features, service level needs, price sensitivity"
```

**The hierarchy**:
```
BusinessEntity (what)
  ├─ ProductVariant 1 (how packaged/configured)
  │   ├─ ServiceLevel A (delivery guarantees)
  │   └─ ServiceLevel B (alternative guarantees)
  ├─ ProductVariant 2
  │   └─ ServiceLevel C
  └─ ProductVariant 3
      └─ ServiceLevel D

Customer chooses: Entity → Variant → ServiceLevel based on use case
```

---

## Example 1: E-Commerce Marketplace (Amazon)

### Business Entity: Binder

**Product Variants** (different configurations):

```yaml
Variant_1_Plain_Binder:
  entity: "Binder"
  variant_name: "Plain binder (no customization)"

  variant_characteristics:
    color: "Standard white"
    sections: "No section dividers"
    size: "1-inch"
    customization: "None"

  service_levels:
    - sla: "Standard shipping (5-7 days)"
      price: "$5.99"
      target_customer: "Budget-conscious buyers (students, casual use)"

    - sla: "2-day shipping"
      price: "$5.99 + $3.99 shipping"
      target_customer: "Moderate urgency (office supplies)"

Variant_2_Colored_Binder_With_Sections:
  entity: "Binder"
  variant_name: "Colored binder with section dividers"

  variant_characteristics:
    color: "Blue, Red, Green, Black (customer choice)"
    sections: "5 section dividers included"
    size: "2-inch (larger capacity)"
    customization: "Color selection"

  service_levels:
    - sla: "Same day shipping (order by 2pm)"
      price: "$12.99 + $7.99 shipping"
      target_customer: "High urgency (exam prep, presentation tomorrow)"
      critical_requirement: "Need by tonight (student preparing for exam)"

    - sla: "Next day shipping"
      price: "$12.99 + $4.99 shipping"
      target_customer: "Moderate urgency (office project starting soon)"

    - sla: "Standard shipping (5-7 days)"
      price: "$12.99"
      target_customer: "No urgency (general organization, willing to wait for free shipping)"

Variant_3_Premium_Leather_Binder:
  entity: "Binder"
  variant_name: "Premium leather binder with customization"

  variant_characteristics:
    material: "Genuine leather"
    color: "Brown, Black, Burgundy"
    sections: "10 section dividers (labeled)"
    size: "3-inch (maximum capacity)"
    customization: "Monogram embossing available"

  service_levels:
    - sla: "Next day shipping + gift wrapping"
      price: "$49.99 + $9.99 shipping"
      target_customer: "Gift buyer (executive gift, graduation present)"

    - sla: "Standard shipping (free)"
      price: "$49.99"
      target_customer: "Premium buyer, not urgent (personal luxury purchase)"
```

### Customer Decision Framework

```yaml
Customer_1_Student_Exam_Prep:
  use_case: "Organize notes for exam tomorrow"

  requirements:
    - sections: "YES (need to separate topics)"
    - urgency: "CRITICAL (exam in 24 hours)"
    - price_sensitivity: "LOW (will pay for urgency)"

  entity_chosen: "Binder"
  variant_chosen: "Colored_Binder_With_Sections"
  service_level_chosen: "Same day shipping"
  total_cost: "$20.98"
  why: "Only option delivering today, sections are must-have"

Customer_2_Office_Manager_Bulk_Purchase:
  use_case: "Buy 50 binders for company archive"

  requirements:
    - customization: "NO (standard is fine)"
    - urgency: "LOW (can wait 2 weeks)"
    - price_sensitivity: "HIGH (budget constraints, bulk purchase)"

  entity_chosen: "Binder"
  variant_chosen: "Plain_Binder"
  service_level_chosen: "Standard shipping"
  total_cost: "$5.99 × 50 = $299.50"
  why: "Lowest cost, no urgency, basic features sufficient"

Customer_3_Executive_Gift:
  use_case: "Gift for retiring CEO"

  requirements:
    - premium_quality: "YES (executive gift)"
    - customization: "YES (monogram with CEO's initials)"
    - urgency: "MODERATE (retirement in 3 days)"

  entity_chosen: "Binder"
  variant_chosen: "Premium_Leather_Binder"
  service_level_chosen: "Next day shipping + gift wrapping"
  total_cost: "$59.98"
  why: "Premium quality for executive, next day sufficient, gift wrap included"
```

---

## Example 2: Data Mesh Marketplace (Data Products)

### Business Entity: Bookings

**Product Variants** (different temporal/aggregation configurations):

```yaml
Variant_1_Realtime_Stream:
  entity: "Bookings"
  variant_name: "Realtime booking stream for dynamic pricing"

  variant_characteristics:
    freshness: "<5 seconds from booking event"
    aggregation_window: "5-minute sliding window"
    window_type: "Sliding (continuous)"
    update_frequency: "Continuous streaming"
    completeness_guarantee: ">99.9%"

  service_levels:
    - sla: "Premium (<5 sec lag, 99.9% uptime)"
      price: "$500/month"
      target_customer: "Repricing engine (revenue-critical)"
      delivery_mechanism: "Kafka stream"

Variant_2_Daily_Batch:
  entity: "Bookings"
  variant_name: "Daily aggregated bookings for financial reporting"

  variant_characteristics:
    freshness: "Refreshed every 30 minutes during business hours"
    aggregation_window: "1 calendar day (midnight-to-midnight)"
    window_type: "Fixed (date partitioned)"
    update_frequency: "Every 30 minutes"
    accuracy_guarantee: "100% (no approximations - finance requires exactness)"

  service_levels:
    - sla: "Standard (<30 min freshness, 99% uptime)"
      price: "$50/month"
      target_customer: "Finance team (compliance reporting)"
      delivery_mechanism: "S3 dataset"

Variant_3_Sliding_Window_48h:
  entity: "Bookings"
  variant_name: "48-hour sliding window for demand forecasting"

  variant_characteristics:
    freshness: "<1 hour from booking event"
    aggregation_window: "48 hours (spans 2 calendar dates)"
    window_type: "Sliding (last 48h from query time)"
    update_frequency: "Hourly"
    completeness_guarantee: ">90% (forecasting tolerates gaps)"

  service_levels:
    - sla: "Economy (<1 hour freshness, 95% uptime)"
      price: "$30/month"
      target_customer: "Demand forecasting model (ML training)"
      delivery_mechanism: "S3 dataset"
```

### Customer Decision Framework

```yaml
Customer_1_Repricing_Engine:
  use_case: "Detect booking surge to adjust hotel pricing in real-time"

  requirements:
    - freshness: "<5 seconds (CRITICAL - pricing decisions in real-time)"
    - completeness: ">99.9% (missing bookings → wrong pricing → revenue loss)"
    - cost_tolerance: "HIGH (revenue impact justifies premium)"

  entity_chosen: "Bookings"
  variant_chosen: "Realtime_Stream"
  service_level_chosen: "Premium"
  total_cost: "$500/month"
  why: "Only variant meeting <5 sec freshness, completeness critical for pricing accuracy"

Customer_2_Finance_Team:
  use_case: "Daily revenue estimation for financial reporting"

  requirements:
    - accuracy: "100% (CRITICAL - financial compliance, SOX regulations)"
    - freshness: "30 min acceptable (not real-time critical - reporting is EOD)"
    - cost_tolerance: "LOW (budget-conscious, low-margin use case)"

  entity_chosen: "Bookings"
  variant_chosen: "Daily_Batch"
  service_level_chosen: "Standard"
  total_cost: "$50/month"
  why: "Meets accuracy requirement, don't need real-time, 10x cheaper than stream"

Customer_3_Demand_Forecasting_Model:
  use_case: "Predict next 2 days demand for capacity planning"

  requirements:
    - window: "48-hour sliding (CRITICAL - spans dates, can't use daily partitions)"
    - completeness: "90% acceptable (ML tolerates some gaps)"
    - freshness: "1 hour acceptable (forecasting not real-time)"

  entity_chosen: "Bookings"
  variant_chosen: "Sliding_Window_48h"
  service_level_chosen: "Economy"
  total_cost: "$30/month"
  why: "Only variant with 48h sliding window, ML tolerates lower quality/freshness"
```

---

## Example 3: Medieval Marketplace (Grain Market)

### Business Entity: Wheat Grain

**Product Variants** (different processing/freshness levels):

```yaml
Variant_1_Fresh_Daily_Grain:
  entity: "Wheat grain"
  variant_name: "Fresh daily grain (harvested <24 hours)"

  variant_characteristics:
    freshness: "Harvested <24 hours ago"
    processing: "Raw grain (unprocessed)"
    quality: "Inspected by guild (premium quality)"
    quantity_unit: "Bushel"

  service_levels:
    - sla: "Daily delivery at dawn"
      price: "10 silver coins per bushel"
      target_customer: "Bakers (need fresh for daily bread)"
      critical_requirement: "Daily bread requires fresh grain"

Variant_2_Bulk_Monthly_Grain:
  entity: "Wheat grain"
  variant_name: "Bulk monthly grain (harvested <30 days)"

  variant_characteristics:
    freshness: "Harvested <30 days ago"
    processing: "Raw grain (unprocessed)"
    quality: "Standard guild inspection"
    quantity_unit: "Wagon load (100 bushels)"

  service_levels:
    - sla: "Monthly delivery (first of month)"
      price: "600 silver coins per wagon (40% volume discount)"
      target_customer: "Storage warehouses (bulk purchase for resale)"

Variant_3_Milled_Flour:
  entity: "Wheat grain"
  variant_name: "Milled flour (value-added processing)"

  variant_characteristics:
    freshness: "Milled <7 days ago (from fresh grain)"
    processing: "Milled to fine flour"
    quality: "Premium (fine sifting, no impurities)"
    quantity_unit: "Sack"

  service_levels:
    - sla: "Weekly delivery (market day)"
      price: "15 silver coins per sack"
      target_customer: "Confectioners (need processed, high quality)"
      critical_requirement: "Fine baking requires milled flour, not raw grain"
```

### Customer Decision Framework

```yaml
Customer_1_Baker:
  use_case: "Daily bread production (serving 200 families)"

  requirements:
    - freshness: "CRITICAL (<24 hours - bread quality depends on grain freshness)"
    - quantity: "5 bushels per day"
    - cost_tolerance: "MEDIUM (can pass cost to bread prices)"

  entity_chosen: "Wheat grain"
  variant_chosen: "Fresh_Daily_Grain"
  service_level_chosen: "Daily delivery at dawn"
  total_cost: "50 silver/day (10 × 5 bushels)"
  why: "Freshness critical for bread quality, daily delivery ensures no storage needed"

Customer_2_Warehouse_Owner:
  use_case: "Bulk storage for resale during winter (grain speculation)"

  requirements:
    - freshness: "NOT CRITICAL (storing for months anyway)"
    - quantity: "LARGE (500 bushels for winter stock)"
    - cost_tolerance: "LOW (profit margin depends on buy-low-sell-high)"

  entity_chosen: "Wheat grain"
  variant_chosen: "Bulk_Monthly_Grain"
  service_level_chosen: "Monthly delivery"
  total_cost: "3,000 silver (5 wagons @ 600 each)"
  why: "Volume discount critical, freshness irrelevant (storing long-term), monthly delivery fine"

Customer_3_Confectioner:
  use_case: "Premium pastries for nobility (wedding cakes)"

  requirements:
    - processing: "CRITICAL (need flour, not raw grain - no mill available)"
    - quality: "PREMIUM (serving nobility - finest quality required)"
    - freshness: "MODERATE (weekly delivery sufficient)"

  entity_chosen: "Wheat grain"
  variant_chosen: "Milled_Flour"
  service_level_chosen: "Weekly delivery"
  total_cost: "60 silver/week (4 sacks @ 15 each)"
  why: "Must buy processed (no mill), premium quality for nobility, weekly sufficient"
```

---

## Example 4: SaaS Marketplace (Cloud Storage)

### Business Entity: Storage Service

**Product Variants** (different storage tiers):

```yaml
Variant_1_Hot_Storage:
  entity: "Storage Service"
  variant_name: "Hot storage (frequently accessed data)"

  variant_characteristics:
    access_pattern: "Frequent (millisecond retrieval)"
    durability: "99.999999999% (11 nines)"
    replication: "Multi-region synchronous"
    minimum_storage_duration: "None (pay per day)"

  service_levels:
    - sla: "Premium (99.99% availability, <10ms p95 latency)"
      price: "$0.023/GB/month"
      target_customer: "Web applications (user-uploaded photos, active documents)"

Variant_2_Warm_Storage:
  entity: "Storage Service"
  variant_name: "Warm storage (infrequently accessed data)"

  variant_characteristics:
    access_pattern: "Infrequent (minutes retrieval)"
    durability: "99.999999999% (11 nines)"
    replication: "Single-region asynchronous"
    minimum_storage_duration: "30 days"

  service_levels:
    - sla: "Standard (99.9% availability, retrieval in minutes)"
      price: "$0.0125/GB/month + $0.01/GB retrieval fee"
      target_customer: "Backup systems (monthly backups, disaster recovery)"

Variant_3_Cold_Storage_Archival:
  entity: "Storage Service"
  variant_name: "Cold storage (archival data)"

  variant_characteristics:
    access_pattern: "Rare (hours retrieval)"
    durability: "99.999999999% (11 nines)"
    replication: "Single-region tape archive"
    minimum_storage_duration: "90 days"

  service_levels:
    - sla: "Archive (99% availability, retrieval in 3-12 hours)"
      price: "$0.004/GB/month + $0.09/GB retrieval fee"
      target_customer: "Compliance archives (7-year retention, rarely accessed)"
```

### Customer Decision Framework

```yaml
Customer_1_Photo_Sharing_App:
  use_case: "Store user-uploaded photos (10M active users)"

  requirements:
    - access_speed: "CRITICAL (<100ms - users expect instant load)"
    - availability: ">99.9% (user-facing SLA)"
    - access_frequency: "HIGH (users view photos daily)"

  entity_chosen: "Storage Service"
  variant_chosen: "Hot_Storage"
  service_level_chosen: "Premium"
  total_cost: "$2,300/month (100TB @ $0.023/GB)"
  why: "Instant access required, high availability critical, frequent access pattern"

Customer_2_Backup_System:
  use_case: "Monthly database backups (disaster recovery)"

  requirements:
    - access_speed: "MODERATE (minutes acceptable - disaster recovery scenario)"
    - access_frequency: "LOW (only accessed if disaster occurs)"
    - cost_tolerance: "LOW (backups are overhead, not revenue-generating)"

  entity_chosen: "Storage Service"
  variant_chosen: "Warm_Storage"
  service_level_chosen: "Standard"
  total_cost: "$625/month (50TB @ $0.0125/GB) + occasional retrieval fees"
  why: "Minutes retrieval acceptable for DR, infrequent access, 50% cheaper than hot"

Customer_3_Financial_Compliance_Archive:
  use_case: "7-year audit trail retention (regulatory requirement)"

  requirements:
    - access_speed: "LOW (hours acceptable - only accessed during audits)"
    - access_frequency: "VERY LOW (1-2 times per year for audits)"
    - retention: "CRITICAL (7 years mandatory by regulation)"
    - cost_tolerance: "VERY LOW (pure compliance overhead, no business value)"

  entity_chosen: "Storage Service"
  variant_chosen: "Cold_Storage_Archival"
  service_level_chosen: "Archive"
  total_cost: "$400/month (100TB @ $0.004/GB) + ~$200/year retrieval (2 audits)"
  why: "Rare access (audits only), hours retrieval acceptable, 83% cheaper than hot"
```

---

## The Universal Framework Pattern

### Ontology (Archetype-Level)

```yaml
Marketplace_Archetype:

  canonical_entities:

    BusinessEntity:
      description: "The underlying commodity or service (category level)"
      properties:
        - entity_id: "unique identifier"
        - entity_name: "Binder, Bookings, Wheat, Storage"
        - entity_category: "Physical goods, Data, Agricultural, Service"

    ProductVariant:
      description: "Specific configuration/packaging (offering level)"
      properties:
        - variant_id: "unique identifier"
        - variant_name: "descriptive name"
        - parent_entity: "which BusinessEntity this is variant of"

        # Variant characteristics (domain-specific)
        - variant_characteristics: "color, freshness, processing, access_pattern (varies by domain)"

        # Differentiation (universal)
        - differentiation_dimension: "feature_set | quality_tier | temporal_pattern | processing_level"
        - target_customer_segment: "who this variant serves"

      relationships:
        - variant_of: BusinessEntity
        - offered_with: ServiceLevel[]

    ServiceLevel:
      description: "Delivery/access guarantees (SLA level)"
      properties:
        - service_level_id: "unique identifier"
        - service_level_name: "Premium, Standard, Economy"

        # SLA guarantees (domain-specific)
        - sla_guarantees: "delivery_time | freshness | availability | latency"

        # Pricing (universal)
        - price: "cost per unit"
        - price_model: "per_unit | subscription | tiered | usage_based"

      relationships:
        - applies_to: ProductVariant

    Customer:
      description: "Buyer with use case needs"
      properties:
        - customer_id: "unique identifier"
        - use_case: "what customer is trying to achieve"

        # Requirements (drive choice)
        - critical_requirements: "must-have features or SLAs"
        - price_sensitivity: "low | medium | high"
        - urgency: "immediate | moderate | flexible"

      relationships:
        - subscribes_to: ProductVariant (with ServiceLevel)
```

---

## Decision Framework (Universal)

**Customer decision process**:

```yaml
decision_process:

  step_1_entity_selection:
    question: "What commodity/service do I need?"
    answer: "Binder, Bookings, Wheat, Storage (based on use case)"

  step_2_variant_selection:
    question: "Which variant matches my requirements?"
    criteria:
      - "Feature needs (sections? processing? access speed?)"
      - "Quality requirements (accuracy? freshness? durability?)"
      - "Quantity/scale (single unit? bulk? continuous?)"
    answer: "Variant that meets critical requirements"

  step_3_service_level_selection:
    question: "What service level do I need?"
    criteria:
      - "Urgency (immediate? can wait?)"
      - "SLA needs (availability? latency?)"
      - "Price sensitivity (premium acceptable? budget-constrained?)"
    answer: "ServiceLevel balancing cost vs urgency"

  step_4_cost_benefit_analysis:
    formula: |
      value_delivered - total_cost = net_value

      If net_value > 0: Purchase
      If net_value < 0: Find cheaper alternative or don't purchase

    examples:
      - "Repricing engine: $500 stream generates $10K revenue → net value $9.5K → purchase"
      - "Finance team: $50 daily batch meets compliance → net value positive → purchase"
      - "Student: $21 same-day binder enables passing exam → net value positive → purchase"
```

---

## Variant Differentiation Strategies (Universal Patterns)

### Strategy 1: Feature Differentiation

**Pattern**: Variants offer different feature sets

```yaml
examples:
  e_commerce_binder:
    base: "Plain white binder"
    variant_1: "+ color choice"
    variant_2: "+ color choice + section dividers"
    variant_3: "+ leather + sections + monogram"

  data_mesh_bookings:
    base: "Daily batch data"
    variant_1: "+ real-time streaming"
    variant_2: "+ sliding window aggregation"
```

---

### Strategy 2: Quality Tier Differentiation

**Pattern**: Variants offer different quality guarantees at different prices

```yaml
examples:
  saas_storage:
    economy: "99% availability, hours retrieval"
    standard: "99.9% availability, minutes retrieval"
    premium: "99.99% availability, milliseconds retrieval"

  data_mesh_bookings:
    economy: "90% completeness, 1-hour freshness"
    standard: "95% completeness, 30-min freshness"
    premium: "99.9% completeness, 5-sec freshness"
```

---

### Strategy 3: Temporal Pattern Differentiation

**Pattern**: Variants offer different temporal characteristics

```yaml
examples:
  medieval_grain:
    daily_fresh: "Harvested <24 hours, delivered daily"
    weekly_processed: "Milled <7 days, delivered weekly"
    monthly_bulk: "Harvested <30 days, delivered monthly"

  data_mesh_bookings:
    realtime_stream: "Continuous, <5 sec lag"
    hourly_refresh: "Updated every hour"
    daily_batch: "Updated daily at midnight"
```

---

### Strategy 4: Processing Level Differentiation

**Pattern**: Variants offer different levels of value-added processing

```yaml
examples:
  medieval_grain:
    raw_grain: "Unprocessed wheat"
    milled_flour: "Processed to flour (value added)"
    baked_bread: "Fully processed (highest value)"

  data_mesh:
    raw_events: "Unprocessed event stream"
    cleaned_dataset: "Processed, deduplicated"
    aggregated_metrics: "Summarized, business logic applied"
```

---

## When to Use This Pattern

**Use Product Variant pattern when**:

1. **Same entity serves multiple use cases** with conflicting requirements
   - Example: Bookings for repricing (real-time) vs finance (batch, accurate)

2. **Customers have different urgency/quality/cost trade-offs**
   - Example: Student (urgent, premium price) vs office (bulk, economy)

3. **Differentiation creates pricing tiers**
   - Example: Fresh daily grain (premium) vs bulk monthly (discount)

4. **Service levels vary by customer segment**
   - Example: Hot storage (apps) vs cold storage (archives)

---

## Implementation Guidance

### For Framework Users

When implementing a Marketplace archetype:

1. **Identify BusinessEntities** (what commodities/services you offer)
2. **Define ProductVariants** for each entity:
   - What differentiation strategies apply? (features, quality, temporal, processing)
   - What customer segments exist?
   - What are their conflicting requirements?
3. **Define ServiceLevels** for each variant:
   - What SLA guarantees?
   - What pricing tiers?
4. **Map customer use cases** → requirements → variant choice

---

## Cross-Domain Comparison

| Aspect | E-Commerce | Data Mesh | Medieval Market | SaaS Storage |
|--------|-----------|-----------|-----------------|--------------|
| **Entity** | Binder | Bookings | Wheat grain | Storage |
| **Variant Strategy** | Features (color, sections) | Temporal (realtime, daily, sliding) | Processing (raw, milled) | Access speed (hot, warm, cold) |
| **Service Level** | Shipping speed | Freshness SLA | Delivery schedule | Availability SLA |
| **Pricing Driver** | Urgency + features | Freshness + quality | Freshness + processing | Access speed + durability |
| **Customer Segments** | Students, offices, executives | Pricing engines, finance, ML models | Bakers, warehouses, confectioners | Apps, backups, compliance |

**Common pattern**: Same entity → variants optimized for different segments → service levels match urgency/quality needs → pricing reflects value

---

## Next Steps

This pattern should be referenced by:
- Marketplace archetype documentation
- Solution-mapper skill (when identifying marketplace pattern)
- Data mesh example (as concrete instantiation)
- Any future marketplace implementations

---

**End of Document**
