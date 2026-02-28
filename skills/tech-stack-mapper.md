# SKILL: /tech-stack-mapper

## Purpose
Maps solution components to technology stack based on **non-functional requirements (NFRs)** and **architectural principles**, not technology features. Ensures data outlives code, minimizes data store fragmentation, and uses open protocols.

## Foundational Principles

### Principle 1: Data Lives Longer Than Code
**Reality**: Applications get rewritten every 5-10 years. Data persists for decades (7+ years compliance requirement).

**Implication**:
- Data architecture ≠ Application architecture
- Data schemas evolve independently from app code
- Use open, portable data formats (SQL, Parquet, CSV)
- Avoid proprietary protocols that lock access patterns

**Anti-pattern**: Wrapping data in component-specific APIs
- APIs evolve faster than data schemas
- Breaking API changes force cascading updates
- Knowledge of data (connascence) becomes entangled

**Correct pattern**: Data accessible via open protocols
- SQL databases queryable by any SQL client (not just "our API")
- Parquet files in Data Lake readable by Spark, Synapse, DuckDB, Trino
- CSV exports for long-term retention

### Principle 2: Minimize Data Store Fragmentation
**Reality**: Each data store technology adds operational burden.

**Each additional data store means**:
- Different backup/restore procedures
- Different security models
- Different query languages
- Different operational expertise
- **Knowledge fragmentation**: "Where is the data?"

**Goal**: Minimal set of data stores, each with distinct purpose.

### Principle 3: Separate Concerns by Evolution Speed
**Reality**: UI, operations, measurements, predictions evolve at different rates.

**Lifecycle separation**:
- **UI apps** (always-on, 99.9% SLA): Evolve monthly (new features)
- **Operational systems** (transactional): Evolve quarterly (business rules change)
- **Reference data** (master data): Evolve yearly (schemas stable)
- **Measurements** (analytics): Evolve weekly (new reports)
- **Predictions** (ML models): Evolve as data patterns shift

**Implication**: Don't couple components with different evolution speeds.

### Principle 4: UI Requires Different Availability Contract
**Reality**: End users expect 24/7 access. Batch jobs can tolerate downtime.

**Availability tiers**:
- **UI Layer**: 99.9% uptime (8.76 hours downtime/year)
- **API Layer**: 99.5% uptime (43.8 hours/year)
- **Batch Processing**: 95% uptime (18.25 days/year acceptable)

**Implication**: UI architecture (CDN, global distribution) ≠ Backend architecture (regional)

## Architecture Principles Applied

### 12-Factor App Principles

**Factor VI: Stateless Processes**
- Processes are disposable, data is not
- Store state in data tier, not in-memory
- Enables horizontal scaling without sticky sessions

**Factor IV: Backing Services as Attached Resources**
- Database is a "backing service" accessed via connection string
- Swap SQL Server → PostgreSQL with config change, no code change
- Supports open protocols (ODBC, JDBC)

**Factor XI: Logs as Event Streams**
- Don't store logs in proprietary systems
- Write logs to stdout → centralized aggregator
- Logs become data (queryable, analyzable)

**Factor III: Config in Environment**
- No hardcoded database names, API endpoints
- Same code artifact runs in dev/staging/prod
- Environment variables specify backing services

### 12-Factor Agent Principles

**Unify Execution State and Business State**
- Don't maintain separate "audit trail" database (Cosmos DB for audit ≠ SQL for operational)
- Single source of truth: Business state IS audit trail (event-sourced or append-only)

**Stateless Reducers**
- Components are pure functions: input state → output state
- State lives in data tier, components are stateless compute

**Small, Focused Agents**
- Each component has narrow responsibility
- Aligns with "different evolution speeds" principle

### Evolutionary Architecture Principles

**Evolutionary Data**
- Schema evolution separate from code evolution
- Forward-compatible schemas (add fields, never break old readers)
- Migration strategy: versioned schemas, not breaking changes

**Fitness Functions (NFRs as Tests)**
- Performance: <2 hours support resolution → Test: API response time <500ms
- Accuracy: 95% on-time gates → Test: Gate calculation accuracy 100%
- Scalability: 195 stores/year → Test: Load test at 500 stores/year (2.5x headroom)

## Step 1: Extract NFRs from Context

### Input: Project Context (Retail Store Opening)

**From KPIs Document:**
- **Performance**: <2 hours resolution time (support SLA)
- **Accuracy**: 95% on-time gate completion, 99% final validation
- **Volume**: 195 stores/year in 2025
- **Compliance**: 7-year data retention (assumed from enterprise context)
- **Certification**: 100% STP certification tracking

**From Organizational Context (inferred):**
- **Enterprise**: Retail Chain = large enterprise (Microsoft shop likely)
- **Geography**: US-based, potentially global
- **Regulatory**: SOC2, potentially PCI if handling payments
- **Skills**: Corporate IT teams (SQL DBAs, not specialized data engineers)

### Derive NFRs

```yaml
non_functional_requirements:

  # Performance NFRs
  performance:
    - metric: "API response time"
      target: "<500ms (p95)"
      rationale: "Support <2 hour resolution SLA requires fast issue lookup"
      source: "KPI: Average Resolution <2hrs"

    - metric: "Dashboard load time"
      target: "<2 seconds"
      rationale: "Executive dashboards must feel responsive"
      source: "User expectation for always-on UI"

    - metric: "Batch job completion"
      target: "<30 minutes for nightly aggregation"
      rationale: "Daily reports ready by 8 AM"
      source: "Operational workflow"

  # Availability NFRs
  availability:
    - tier: "UI Layer (web, mobile)"
      target: "99.9% uptime (8.76 hours downtime/year)"
      rationale: "Field technicians need 24/7 access to log work"
      source: "Always-on user expectation"

    - tier: "API Layer"
      target: "99.5% uptime"
      rationale: "Tolerates brief outages during maintenance windows"
      source: "Backend service SLA"

    - tier: "Batch Processing"
      target: "95% uptime (jobs can retry)"
      rationale: "Nightly jobs tolerate delays"
      source: "Non-critical batch workloads"

  # Scalability NFRs
  scalability:
    - metric: "Stores per year"
      current: "195 stores/year (2025)"
      target: "500 stores/year (2.5x headroom for growth)"
      rationale: "10-year projection of growth"
      source: "KPI: Annual Target = 195"

    - metric: "Concurrent users"
      current: "~100 users (PMO, field techs, executives)"
      target: "500 concurrent users (5x headroom)"
      rationale: "Scale as program expands globally"
      source: "Estimated from team size"

    - metric: "Data volume"
      current: "~1M work events/year (195 stores × 5K events per store)"
      target: "10M events/year (10-year accumulation)"
      rationale: "Data Lake must handle historical data for ML"
      source: "Calculated from store volume"

  # Data Retention NFRs
  data_retention:
    - data_type: "Operational data (gates, issues, work logs)"
      retention: "7 years"
      rationale: "Compliance (SOC2, legal discovery)"
      format: "Open format (SQL, Parquet)"
      source: "Enterprise compliance standard"

    - data_type: "Audit logs (who approved what)"
      retention: "7 years (immutable)"
      rationale: "Regulatory compliance, legal disputes"
      format: "Append-only Parquet (Data Lake)"
      source: "Compliance requirement"

    - data_type: "Analytics data (ML training)"
      retention: "Indefinite (grows forever)"
      rationale: "More historical data → better ML models"
      format: "Parquet (Data Lake)"
      source: "ML/analytics use case"

  # Security NFRs
  security:
    - requirement: "Authentication"
      target: "Enterprise SSO (Azure AD / Entra ID)"
      rationale: "Retail Chain employees use corporate credentials"
      source: "Enterprise standard"

    - requirement: "Authorization"
      target: "Role-based access (PMO, field tech, vendor, executive)"
      rationale: "Different roles have different permissions"
      source: "Organizational structure"

    - requirement: "Data encryption"
      target: "TDE at rest, TLS in transit"
      rationale: "SOC2 compliance"
      source: "Security baseline"

    - requirement: "Audit trail"
      target: "Immutable log of all changes (who, what, when)"
      rationale: "Compliance, dispute resolution"
      source: "Governance requirement"

  # Operational NFRs
  operations:
    - requirement: "Backup/Restore"
      target: "Daily backups, 7-year retention, <4 hour RTO"
      rationale: "Data loss = business disruption"
      source: "Standard enterprise DR"

    - requirement: "Monitoring/Alerting"
      target: "Real-time alerts on failures, <5 min detection"
      rationale: "Support <2 hour SLA requires fast issue detection"
      source: "KPI: Resolution <2hrs"

    - requirement: "Deployment"
      target: "Zero-downtime deployments, <30 min rollback"
      rationale: "99.9% UI availability requires no-downtime deploys"
      source: "Availability NFR"

  # Cost NFRs
  cost:
    - constraint: "Infrastructure budget"
      target: "<$150K/year operational cost"
      rationale: "Project budget envelope (estimated)"
      source: "Typical enterprise project budget"

    - constraint: "Team size"
      target: "Operable by 3-person DevOps team"
      rationale: "Small team, minimize operational complexity"
      source: "Resource constraint"
```

## Step 2: Define Minimal Data Store Architecture

### Data Store Selection Criteria

**Question 1: What is the data lifecycle?**
- **Transactional** (read/write, ACID) → SQL Database
- **Append-only** (write-once, read-many) → Data Lake
- **Ephemeral** (short-lived cache) → Redis
- **Archival** (rarely accessed, long retention) → Blob Storage

**Question 2: What is the access pattern?**
- **Low-latency reads** (<100ms) → Redis cache
- **Complex queries** (joins, aggregations) → SQL Database
- **Analytical queries** (large scans, aggregations) → Data Lake + SQL Engine
- **Bulk export** (CSV, PDF) → Blob Storage

**Question 3: What is the schema evolution pattern?**
- **Stable schema** (changes rare) → SQL Database (migrations)
- **Flexible schema** (evolving structure) → JSON column in SQL OR Data Lake (schema-on-read)
- **No schema** (unstructured text, files) → Blob Storage

### Minimal Data Store Architecture: 3 Tiers

```yaml
minimal_data_store_architecture:

  # Tier 1: Operational Data (ACID Transactions)
  operational_tier:
    technology: "SQL Database (Azure SQL or PostgreSQL)"

    purpose: "Current operational state (gates, issues, work units)"

    characteristics:
      - "ACID transactions (consistency guarantees)"
      - "Referential integrity (foreign keys)"
      - "Row-level locking (concurrent updates)"
      - "Open protocol: SQL (portable to any SQL database)"

    data_types:
      - "Gate records (current state: pending, approved, rejected)"
      - "Issue records (current state: open, assigned, resolved)"
      - "Work unit records (current state: not started, in progress, completed)"
      - "Vendor records (master data: vendor info, certifications)"
      - "Store records (master data: store location, owner, metadata)"
      - "User records (authentication/authorization data)"
      - "Configuration (KPI targets, SLA thresholds, gate policies)"

    retention: "7 years (compliance), then archive to Blob Storage"

    backup: "Daily automated backups, 7-year retention"

    schema_evolution:
      strategy: "Versioned migrations (Flyway, Liquibase, EF Migrations)"
      breaking_changes: "NEVER. Always backward-compatible (add columns, never drop)"

    per_component_database: true
    rationale: "Each solution component owns its data (no shared databases)"

    estimated_cost: "$200/month per component × 10 components = $2,000/month"


  # Tier 2: Historical/Analytical Data (Append-Only Events)
  analytical_tier:
    technology: "Data Lake (Azure Data Lake Gen2 with Parquet format)"

    purpose: "Immutable history for analytics, ML, audit"

    characteristics:
      - "Append-only (never update/delete)"
      - "Open format: Parquet (queryable by Spark, Synapse, DuckDB, Trino)"
      - "Partitioned by date (efficient time-range queries)"
      - "Compressed (Snappy codec, 5-10x compression)"
      - "Schema evolution: forward-compatible (add fields, never break old readers)"

    data_types:
      - "Work events (work started, work completed, work failed)"
      - "Gate events (gate submitted, gate approved, gate rejected)"
      - "Issue events (issue created, issue assigned, issue resolved)"
      - "Audit events (user login, config changed, decision made)"
      - "Performance metrics (API response times, batch job durations)"

    retention: "Indefinite (data grows forever, cost scales with volume)"

    structure: |
      /bronze/    ← Raw events (immutable, never touched)
        /2026/02/28/gate_events/part-00001.parquet
        /2026/02/28/work_events/part-00001.parquet

      /silver/    ← Cleaned events (deduped, validated)
        /2026/02/28/gate_events/part-00001.parquet

      /gold/      ← Curated datasets (aggregated, business-ready)
        /daily_progress_snapshots/2026/02/28/part-00001.parquet
        /vendor_performance_weekly/2026-W08/part-00001.parquet

    schema_evolution:
      strategy: "Schema-on-read (Data Lake stores raw bytes, reader interprets schema)"
      versioning: "Add schema_version field to each event"
      breaking_changes: "NEVER. Old files remain readable forever."

    query_engine: "Azure Synapse Serverless SQL (query Parquet files via SQL)"

    estimated_cost: "$50/month storage + $50/month query (5TB scanned) = $100/month"


  # Tier 3: High-Speed Cache (Ephemeral Data)
  cache_tier:
    technology: "Redis (Azure Cache for Redis)"

    purpose: "Reduce database load, accelerate UI dashboards"

    characteristics:
      - "In-memory (sub-millisecond latency)"
      - "Ephemeral (data can be rebuilt from source)"
      - "TTL expiration (auto-delete stale data)"
      - "Open protocol: Redis (compatible with all Redis clients)"

    data_types:
      - "Dashboard query results (portfolio progress, at-risk stores)"
      - "API response caching (reduce repeated database queries)"
      - "Session state (user preferences, UI state)"

    retention: "15 seconds to 1 hour (TTL-based expiration)"

    eviction_policy: "allkeys-lru (least recently used)"

    persistence: "Disabled (cache can rebuild from SQL/Data Lake)"

    estimated_cost: "$100/month (1GB Standard C1 tier)"


  # Optional Tier 4: File Storage (Unstructured Data)
  file_storage_tier:
    technology: "Blob Storage (Azure Blob Storage)"

    purpose: "Files, reports, archives"

    characteristics:
      - "Unstructured data (PDF, Excel, images)"
      - "Tiered storage (Hot, Cool, Archive)"
      - "Lifecycle policies (auto-tier after 90 days)"
      - "Open format: Files (universally accessible)"

    data_types:
      - "Generated reports (PDF executive summaries, Excel exports)"
      - "Email attachments (issue screenshots, approval documents)"
      - "Configuration files (email templates, report templates)"
      - "Archived operational data (SQL database archives after 7 years)"

    retention: "7 years Hot/Cool tier, then Archive tier"

    estimated_cost: "$10/month (Hot tier) + $1/month (Archive tier for old data)"


  # TOTAL DATA STORE COST
  total_infrastructure_cost:
    monthly: "$2,210/month"
    annual: "$26,520/year"

    breakdown:
      - "SQL Database: $2,000/month (10 component databases)"
      - "Data Lake: $100/month (storage + query)"
      - "Redis Cache: $100/month"
      - "Blob Storage: $10/month"
```

### Why NOT Cosmos DB?

**Original recommendation**: Cosmos DB for audit logs, flexible schema data

**User's question**: "What does Cosmos DB bring to the table?"

**Analysis**:

```yaml
cosmos_db_value_proposition:

  claimed_benefits:
    - "Document model (flexible schema)"
    - "Global distribution (multi-region writes)"
    - "Managed service (no ops)"

  reality_check:

    flexible_schema:
      claim: "JSON documents evolve easily"
      reality: "SQL databases support JSONB columns (Postgres) or JSON datatypes (SQL Server)"
      better_alternative: "SQL table with JSONB column OR schema-on-read Data Lake"
      verdict: "NOT a unique benefit"

    global_distribution:
      claim: "Multi-region writes for low latency"
      reality: "Retail Store Opening is US-centric, single region sufficient"
      requirement: "No NFR for global distribution (not a multi-region app)"
      verdict: "Solving a problem we don't have"

    managed_service:
      claim: "No ops burden"
      reality: "Azure SQL and Data Lake are also managed services"
      verdict: "NOT a unique benefit"

  costs:

    proprietary_api:
      issue: "Cosmos DB SQL API is NOT standard SQL"
      consequence: "Custom query syntax, not portable to other databases"
      locks_us_in: "Can't migrate to Postgres, MySQL, or query with standard tools"
      violates_principle: "Open protocols for data access"

    pricing_model:
      issue: "RU-based pricing (Request Units) is complex and expensive"
      typical_cost: "$200-500/month for moderate workload"
      comparison: "SQL Database: $200/month. Data Lake: $50/month. Cosmos DB: $400/month."
      verdict: "2-4x more expensive for same workload"

    knowledge_fragmentation:
      issue: "Now have 3 data stores: SQL + Data Lake + Cosmos DB"
      operational_burden:
        - "Different backup procedures (SQL: automated, Cosmos: continuous, Data Lake: versioned)"
        - "Different query languages (T-SQL, Cosmos DB SQL, Spark SQL)"
        - "Different security models (SQL: schemas, Cosmos DB: collections, Data Lake: folders)"
        - "Different monitoring (SQL: query stats, Cosmos DB: RU metrics, Data Lake: storage metrics)"
      team_impact: "Team must learn 3 systems instead of 2"
      violates_principle: "Minimize data store fragmentation"

  correct_alternative:

    audit_logs:
      instead_of: "Cosmos DB (document store)"
      use: "Data Lake (append-only Parquet files)"
      rationale: |
        - Audit logs are append-only (NEVER update/delete)
        - Parquet is immutable (perfect for audit trail)
        - Open format (queryable by any tool: Synapse, Spark, DuckDB)
        - Cheaper ($50/month vs $400/month)
        - Aligns with "data lives longer than code" principle
      query_pattern: "SELECT * FROM audit_logs WHERE user_id = 'X' AND event_date > '2026-01-01'"
      query_engine: "Azure Synapse Serverless SQL (standard SQL over Parquet files)"

    flexible_schema_data:
      instead_of: "Cosmos DB (JSON documents)"
      use: "SQL Database with JSONB column"
      example: |
        CREATE TABLE issues (
          issue_id UUID PRIMARY KEY,
          title TEXT,
          severity TEXT,
          status TEXT,
          metadata JSONB  -- Flexible fields here (vendor-specific data, custom fields)
        );

        -- Query: Fast index on structured fields, flexible on JSONB
        SELECT * FROM issues WHERE status = 'open' AND metadata->>'vendor_id' = '123';
      rationale: "Best of both worlds: structured + flexible, single database, standard SQL"

  decision: "ELIMINATE Cosmos DB. Use SQL Database + Data Lake + Redis only."
```

## Step 3: Map Solution Components to Data Stores

### Mapping Logic

```yaml
component_to_datastore_mapping:

  # Principle: Each component owns its operational data (SQL database)
  # Principle: All components write events to shared Data Lake (analytics)
  # Principle: Components cache hot queries in Redis (performance)

  Progress_Analyst:
    operational_data:
      store: "SQL Database (Progress_Analyst_DB)"
      tables:
        - "progress_snapshots (daily aggregated progress by store)"
        - "progress_calculations (% complete, at-risk flags)"
      rationale: "Current state queries (which stores are at-risk NOW)"

    historical_data:
      store: "Data Lake (/gold/daily_progress_snapshots/)"
      format: "Parquet (partitioned by date)"
      rationale: "Historical trends, ML training data"

    cache:
      store: "Redis"
      keys: "portfolio:{region}, at-risk-stores"
      ttl: "15 seconds (dashboard refresh rate)"
      rationale: "Dashboard loads <2 sec (NFR)"

  Gate_Manager:
    operational_data:
      store: "SQL Database (Gate_Manager_DB)"
      tables:
        - "gates (gate records: pending, approved, rejected)"
        - "gate_checklists (V3 checklist items)"
        - "gate_policies (approval rules)"
      rationale: "ACID transactions (gate approval is transactional)"

    audit_data:
      store: "Data Lake (/bronze/gate_events/)"
      events: "GateSubmitted, GateApproved, GateRejected"
      format: "Parquet (append-only, immutable)"
      rationale: "Audit trail (who approved what, when) - open format, queryable forever"

    cache:
      store: "Redis"
      keys: "pending-gates, gates:store:{store_id}"
      ttl: "60 seconds"
      rationale: "Reduce database load for approval dashboard"

  Accountability_Tracker:
    operational_data:
      store: "SQL Database (Accountability_Tracker_DB)"
      tables:
        - "action_items (what, who, when, status)"
        - "escalations (escalation path, timestamps)"
      rationale: "Current accountability state (who owns what NOW)"

    audit_data:
      store: "Data Lake (/bronze/accountability_events/)"
      events: "ActionCreated, ActionCompleted, ActionEscalated"
      format: "Parquet"
      rationale: "Accountability history (prove action was assigned on X date)"

  Field_Operations:
    operational_data:
      store: "SQL Database (Field_Operations_DB)"
      tables:
        - "work_units (V3 checklist items, completion status)"
        - "field_logs (tech arrival/departure times)"
      rationale: "Current work state (which work items completed)"

    offline_cache:
      store: "SQLite on mobile device"
      sync_to: "SQL Database (when online)"
      rationale: "Mobile offline-first (12-factor: stateless, disposable)"

    event_stream:
      store: "Data Lake (/bronze/work_events/)"
      events: "WorkStarted, WorkCompleted, WorkFailed"
      format: "Parquet"
      rationale: "Work history for analytics (time to complete by vendor, task type)"

  # ... (repeat for all 10 components)
```

## Step 4: Technology Selection by Architectural Layer

### Layer 1: Data Tier (3 Technologies)

```yaml
data_tier_technologies:

  # Technology 1: SQL Database
  sql_database:
    selected_technology: "Azure SQL Database"

    alternatives_evaluated:
      - "PostgreSQL (Azure Database for PostgreSQL)"
      - "MySQL"
      - "SQL Server on VMs"

    decision_rationale:
      principle: "Open protocol (SQL standard)"

      why_azure_sql:
        - "Standard SQL (T-SQL dialect, 99% ANSI SQL compatible)"
        - "Managed service (automated backups, patching, HA)"
        - "Likely Retail Chain existing Microsoft EA (lower cost)"
        - "Team skills: SQL DBAs common in enterprise IT"
        - "Portability: Can migrate to SQL Server, PostgreSQL with minimal changes"

      why_not_postgres:
        - "Postgres is also open standard (excellent choice)"
        - "Decision: Use Azure SQL IF Retail Chain has Microsoft EA, else Postgres"
        - "Both satisfy NFRs and principles"

      why_not_cosmos_db:
        - "Proprietary API (locks access patterns)"
        - "Violates 'open protocols' principle"
        - "2-4x more expensive"
        - "Adds operational complexity (RU tuning)"

    fitness_functions:
      - "Query performance: <500ms for p95 API queries"
      - "Portability: Can export to CSV, restore on Postgres in <1 hour"
      - "Cost: <$250/month per database (Standard S3 tier)"

    configuration:
      tier: "Standard S3 (100 DTUs) per component database"
      backup: "Automated daily backups, 7-year retention, point-in-time restore"
      security: "Private endpoint (no public internet), TDE encryption at rest"
      schema_versioning: "Entity Framework Migrations (versioned, code-first)"

    cost: "$200/month per database × 10 components = $2,000/month"


  # Technology 2: Data Lake
  data_lake:
    selected_technology: "Azure Data Lake Storage Gen2 (ADLS Gen2)"

    format: "Apache Parquet"

    alternatives_evaluated:
      - "AWS S3 + Parquet"
      - "Google Cloud Storage + Parquet"
      - "Databricks Delta Lake"

    decision_rationale:
      principle: "Data outlives code, open format"

      why_parquet:
        - "Columnar format (10x faster analytical queries than row-based)"
        - "Open standard (Apache project, readable by 20+ tools)"
        - "Compressed (Snappy codec, 5-10x compression ratio)"
        - "Schema evolution (add columns without breaking old files)"
        - "Self-describing (schema embedded in file, no external catalog needed)"

      why_not_csv:
        - "No schema (ambiguous data types)"
        - "No compression (10x larger files)"
        - "Slow queries (must scan entire file)"
        - "Use CSV only for final export/archival"

      why_not_delta_lake:
        - "Delta Lake is excellent (ACID on Data Lake, time travel)"
        - "Decision: Overkill for append-only event logs"
        - "Adds complexity (Delta Lake metadata layer)"
        - "Use Parquet for simplicity, upgrade to Delta if needed"

      why_not_json:
        - "Verbose (3-5x larger than Parquet)"
        - "No columnar format (slow analytics)"
        - "Use JSON only for flexible schema staging (Bronze layer)"

    fitness_functions:
      - "Query performance: Synapse SQL scans 1TB in <30 seconds"
      - "Portability: Parquet files readable by Spark, DuckDB, Pandas, Trino"
      - "Cost: <$20/month per TB stored"

    configuration:
      structure: "Bronze (raw) → Silver (cleaned) → Gold (curated) medallion architecture"
      partitioning: "By date (YYYY/MM/DD) for efficient time-range queries"
      compression: "Snappy (balanced compression ratio and query speed)"
      schema_versioning: "Add schema_version field to each event (forward-compatible)"

    query_engine: "Azure Synapse Serverless SQL (query Parquet via standard SQL)"

    cost: "$50/month storage (5TB) + $50/month query (5TB scanned) = $100/month"


  # Technology 3: Cache
  cache:
    selected_technology: "Azure Cache for Redis"

    alternatives_evaluated:
      - "In-memory cache (ASP.NET Core IMemoryCache)"
      - "Memcached"

    decision_rationale:
      principle: "12-factor: stateless processes"

      why_redis:
        - "Distributed cache (shared across multiple API instances)"
        - "Open protocol (Redis wire protocol, compatible with all clients)"
        - "Rich data structures (strings, hashes, sets, sorted sets)"
        - "TTL expiration (auto-delete stale data)"

      why_not_in_memory_cache:
        - "Violates 12-factor: stateless processes"
        - "Each API instance has separate cache (cache miss on load balancer round-robin)"
        - "Lost on pod restart (disposability principle)"

      why_not_memcached:
        - "Simpler than Redis (only key-value)"
        - "Decision: Redis has richer data structures (sorted sets for leaderboards)"
        - "Redis is more common in Azure ecosystem"

    fitness_functions:
      - "Cache hit rate: >80% for dashboard queries"
      - "Latency: <10ms for cache lookups"
      - "Cost: <$150/month"

    configuration:
      tier: "Standard C1 (1GB memory)"
      eviction: "allkeys-lru (evict least recently used when full)"
      persistence: "Disabled (ephemeral cache, rebuilds from SQL/Data Lake)"
      ttl: "15 seconds (dashboards) to 1 hour (reference data)"

    cost: "$100/month (Standard C1 tier)"
```

### Layer 2: Compute Tier (Stateless, 12-Factor)

```yaml
compute_tier_technologies:

  # Technology 1: API/Business Logic
  api_compute:
    selected_technology: "Azure Container Apps (ASP.NET Core 8)"

    language: ".NET 8 (C#)"

    alternatives_evaluated:
      - "Azure App Service"
      - "Azure Kubernetes Service (AKS)"
      - "Azure Functions"
      - "Python (FastAPI)"

    decision_rationale:
      principle: "12-factor: stateless processes, port binding, concurrency"

      why_dotnet:
        - "Team skills: Microsoft shop (inferred from enterprise context)"
        - "Performance: C# compiled, faster than Python for API workloads"
        - "Ecosystem: Entity Framework (SQL ORM), Rich libraries"
        - "Portability: .NET 8 runs on Linux (not locked to Windows)"

      why_container_apps:
        - "Serverless containers (scale to zero, pay per use)"
        - "12-factor: disposability (fast startup <5 sec, graceful shutdown)"
        - "12-factor: port binding (containers expose HTTP port)"
        - "12-factor: concurrency (scale out by adding containers)"
        - "Simpler than AKS (no Kubernetes complexity)"
        - "Cheaper than App Service (pay only when running)"

      why_not_azure_functions:
        - "Functions are serverless (good for event-driven)"
        - "Decision: Container Apps better for HTTP APIs (always-on, <500ms latency NFR)"
        - "Use Functions for batch jobs (nightly aggregation)"

      why_not_python:
        - "Python excellent for data science (use for ML model training)"
        - "C# faster for API workloads (compiled vs interpreted)"
        - "Team skills favor .NET"

    fitness_functions:
      - "API response time: <500ms (p95)"
      - "Cold start: <5 seconds (disposability)"
      - "Horizontal scaling: 1 → 10 instances in <30 seconds"
      - "Zero-downtime deployment: Blue-green deployment, <30 sec rollback"

    configuration:
      framework: "ASP.NET Core 8 Minimal APIs (lightweight, fast startup)"
      architecture: "Clean Architecture (Domain, Application, Infrastructure layers)"
      containerization: "Docker (multi-stage build, distroless base image)"
      scaling: "Auto-scale 1-10 instances based on CPU (>70%) or HTTP queue depth"

    cost: "$50/month per component (1 instance baseline) × 10 = $500/month baseline"


  # Technology 2: Batch Processing
  batch_compute:
    selected_technology: "Azure Functions (Timer Trigger) for simple jobs"

    alternative: "Azure Synapse Spark for large-scale data processing"

    decision_rationale:
      principle: "12-factor: admin processes as one-off processes"

      why_functions:
        - "Serverless (pay per execution, not idle time)"
        - "12-factor: separate admin processes from API processes"
        - "Timer trigger (CRON schedule: '0 0 2 * * *' = 2 AM daily)"

      why_not_synapse_spark:
        - "Spark overkill for simple jobs (nightly aggregation < 1M events)"
        - "Use Spark ONLY if batch job processes >10M rows"
        - "Decision: Start with Functions, upgrade to Spark if needed"

    fitness_functions:
      - "Batch job completion: <30 minutes"
      - "Cost: <$20/month (serverless, pay per execution)"

    cost: "$20/month (estimated 30 daily jobs × $0.02 per job)"


  # Technology 3: Workflow Orchestration
  workflow_compute:
    selected_technology: "Azure Durable Functions (for simple workflows)"

    alternative: "Temporal.io (for complex, long-running workflows)"

    decision_rationale:
      principle: "12-factor agents: stateless reducers, pause/resume"

      why_durable_functions:
        - "Serverless (no infrastructure to manage)"
        - "Built-in state persistence (workflow state in Azure Storage)"
        - "Pause/resume (12-factor agents: human-in-the-loop approval)"
        - "Native C# (team skills match)"

      why_not_temporal:
        - "Temporal excellent for complex workflows (100+ steps)"
        - "Retail Chain workflows are simple (4-gate approval, 3-level escalation)"
        - "Decision: Start with Durable Functions, migrate to Temporal if complexity grows"

    fitness_functions:
      - "Workflow state persistence: survives VM restarts"
      - "Human-in-the-loop: pause for 5 days waiting for approval, resume instantly"
      - "Cost: <$50/month"

    cost: "$50/month (estimated workflow executions)"
```

### Layer 3: Integration Tier (Event-Driven, 12-Factor)

```yaml
integration_tier_technologies:

  # Technology 1: Async Messaging
  messaging:
    selected_technology: "Azure Service Bus"

    alternatives_evaluated:
      - "Azure Event Hubs"
      - "RabbitMQ"
      - "Kafka"

    decision_rationale:
      principle: "12-factor: backing services, loose coupling"

      why_service_bus:
        - "Pub/Sub pattern (topics + subscriptions)"
        - "Reliable delivery (at-least-once, dead-letter queue)"
        - "Managed service (no ops burden)"
        - "Open protocol: AMQP (not proprietary)"

      why_not_event_hubs:
        - "Event Hubs for high-volume streaming (millions/sec)"
        - "RSO events are low-volume (~100/hour)"
        - "Decision: Service Bus for <10K msgs/hour, Event Hubs for >100K"

      why_not_kafka:
        - "Kafka requires ops burden (Zookeeper, brokers)"
        - "Managed Kafka (Confluent Cloud) is expensive"
        - "Decision: Service Bus simpler for enterprise use case"

    fitness_functions:
      - "Message delivery: 99.9% success rate"
      - "Latency: <100ms message publish to subscriber receive"
      - "Dead-letter queue: <1% messages sent to DLQ"

    cost: "$10/month (Standard tier, low volume)"


  # Technology 2: API Gateway
  api_gateway:
    selected_technology: "Azure API Management (APIM)"

    alternatives_evaluated:
      - "Kong"
      - "AWS API Gateway"
      - "NGINX"

    decision_rationale:
      principle: "12-factor: port binding, backing services"

      why_apim:
        - "Centralized API gateway (single entry point for all 10 components)"
        - "Rate limiting (protect backends from abuse)"
        - "JWT validation (authentication at gateway)"
        - "Caching (reduce backend load)"
        - "Developer portal (API docs for vendors)"

      why_not_kong:
        - "Kong excellent (open source, widely used)"
        - "Decision: APIM better Azure integration, managed service"

      cost_caveat:
        - "APIM is expensive ($700/month Standard tier)"
        - "Alternative: Use Azure Front Door ($50/month) for simple routing + caching"
        - "Decision: Start with Front Door, upgrade to APIM if need advanced features"

    cost: "$50/month (Azure Front Door) OR $700/month (APIM Standard)"
```

### Layer 4: UI Tier (Always-On, 99.9% SLA)

```yaml
ui_tier_technologies:

  # Technology 1: Web UI
  web_ui:
    selected_technology: "React 18 + Next.js 14 (Static Site Generation)"

    hosting: "Azure Static Web Apps OR Azure CDN + Blob Storage"

    decision_rationale:
      principle: "UI = always-on, different availability contract"

      why_static_site:
        - "Static HTML/JS/CSS served from CDN (globally distributed)"
        - "99.99% SLA (higher than API tier)"
        - "Low cost ($10/month vs $500/month for App Service)"
        - "Fast (CDN edge caching, <100ms TTFB)"

      why_nextjs:
        - "Static Site Generation (build HTML at deploy time, not runtime)"
        - "React ecosystem (rich component libraries: Material-UI, Recharts)"
        - "TypeScript (type safety, reduces runtime errors)"

      architecture:
        - "UI is decoupled from APIs (different deployment, different SLA)"
        - "UI calls APIs via HTTPS/REST (loose coupling)"
        - "UI fails independently (API down doesn't crash UI)"

    fitness_functions:
      - "Availability: 99.9% uptime (8.76 hours/year downtime)"
      - "Page load time: <2 seconds (p95)"
      - "Cost: <$50/month"

    cost: "$10/month (Azure Static Web Apps with CDN)"


  # Technology 2: Mobile UI
  mobile_ui:
    selected_technology: "React Native + Expo"

    offline_support: "WatermelonDB (offline-first SQLite)"

    decision_rationale:
      principle: "Field techs need offline access (no cell signal in construction sites)"

      why_react_native:
        - "Code sharing with web (React components reusable)"
        - "Cross-platform (iOS + Android from single codebase)"
        - "Offline-first (WatermelonDB syncs when online)"

      why_expo:
        - "OTA updates (update app without App Store approval)"
        - "Managed build service (no Xcode/Android Studio needed)"

      offline_architecture:
        - "Work logs saved to SQLite (works offline)"
        - "Sync to backend when online (conflict resolution: last-write-wins)"
        - "12-factor: stateless backend (mobile device holds state, backend is dumb store)"

    cost: "$0/month (client-side app, no hosting cost)"


  # Technology 3: Dashboards
  dashboards:
    selected_technology: "Power BI Embedded"

    alternatives_evaluated:
      - "Custom React dashboards (Recharts, D3.js)"
      - "Tableau"
      - "Grafana"

    decision_rationale:
      principle: "Separate concerns by evolution speed (dashboards evolve weekly)"

      why_power_bi:
        - "BI team expertise (Power BI is Microsoft standard for enterprises)"
        - "Self-service (business users create reports without engineering)"
        - "Native Azure integration (DirectQuery to Synapse, SQL)"
        - "Row-Level Security (RLS based on Azure AD roles)"

      why_not_custom_react:
        - "Custom dashboards require engineering for every change"
        - "Power BI enables business users to self-serve (faster iteration)"

      embedding:
        - "Power BI reports embedded in React app (iframe + JavaScript SDK)"
        - "SSO via Azure AD (seamless auth)"

    cost: "$735/month (Power BI Embedded A1 SKU: 1 vCore)"
```

## Step 5: Output Format

### Technology Stack Specification

```yaml
technology_stack_specification:

  project: "Retail Store Opening Smart Tracker"
  generated_date: "2026-02-28"

  # NFRs Addressed
  nfrs_addressed:
    performance: "<2 sec dashboard load, <500ms API response (p95)"
    availability: "99.9% UI uptime, 99.5% API uptime"
    scalability: "195 stores/year → 500 stores/year (2.5x headroom)"
    data_retention: "7 years operational data, indefinite analytics data"
    cost: "<$150K/year infrastructure"

  # Architectural Principles Applied
  principles_applied:
    - "Data lives longer than code (open formats: SQL, Parquet)"
    - "Minimize data store fragmentation (3 tiers: SQL, Data Lake, Redis)"
    - "Separate concerns by evolution speed (UI ≠ API ≠ Batch)"
    - "UI requires different availability contract (99.9% vs 99.5%)"
    - "12-factor: stateless processes, backing services, logs as streams"
    - "12-factor agents: stateless reducers, pause/resume, small focused agents"
    - "Evolutionary architecture: schema evolution, fitness functions"

  # Data Tier (3 Technologies)
  data_tier:

    sql_database:
      technology: "Azure SQL Database"
      purpose: "Operational data (current state, ACID transactions)"
      open_protocol: "SQL (T-SQL dialect, 99% ANSI SQL)"
      portability: "Can migrate to PostgreSQL, SQL Server, MySQL"
      per_component: "10 databases (one per component, no sharing)"
      cost: "$2,000/month ($200 × 10 components)"
      fitness_functions:
        - "Query performance: <500ms (p95)"
        - "Portability: Export to CSV, restore on Postgres <1 hour"

    data_lake:
      technology: "Azure Data Lake Gen2 (Parquet format)"
      purpose: "Historical/analytical data (append-only events, ML training)"
      open_format: "Apache Parquet (columnar, compressed, self-describing)"
      portability: "Readable by Spark, Synapse, DuckDB, Trino, Pandas"
      structure: "Bronze (raw) → Silver (cleaned) → Gold (curated)"
      query_engine: "Azure Synapse Serverless SQL (standard SQL over Parquet)"
      cost: "$100/month (storage + query)"
      fitness_functions:
        - "Query performance: 1TB scan <30 seconds"
        - "Portability: Files readable by 5+ tools (Spark, DuckDB, etc.)"

    cache:
      technology: "Azure Cache for Redis"
      purpose: "High-speed cache (reduce database load, <100ms reads)"
      open_protocol: "Redis wire protocol"
      ephemeral: true (data rebuilds from SQL/Data Lake)
      cost: "$100/month (Standard C1, 1GB)"
      fitness_functions:
        - "Cache hit rate: >80%"
        - "Latency: <10ms"

  # Compute Tier (Stateless, 12-Factor)
  compute_tier:

    api_business_logic:
      technology: "Azure Container Apps (ASP.NET Core 8)"
      language: ".NET 8 (C#)"
      architecture: "Clean Architecture (Domain, Application, Infrastructure)"
      12_factor_compliance:
        - "Stateless processes (horizontal scaling, disposability)"
        - "Port binding (container exposes HTTP on port 8080)"
        - "Concurrency (scale out 1-10 instances)"
        - "Config in environment (connection strings via env vars)"
      cost: "$500/month baseline (10 components × $50 each)"
      fitness_functions:
        - "API response: <500ms (p95)"
        - "Cold start: <5 seconds"
        - "Zero-downtime deployment: blue-green, <30 sec rollback"

    batch_processing:
      technology: "Azure Functions (Timer Trigger)"
      purpose: "Nightly aggregation, scheduled jobs"
      12_factor_compliance: "Admin processes as one-off processes"
      cost: "$20/month (serverless, pay per execution)"
      fitness_functions:
        - "Batch job completion: <30 minutes"

    workflow_orchestration:
      technology: "Azure Durable Functions"
      purpose: "Multi-step workflows (gate approval, issue escalation)"
      12_factor_agent_compliance: "Stateless reducers, pause/resume"
      cost: "$50/month"
      fitness_functions:
        - "State persistence: survives VM restarts"
        - "Pause/resume: 5-day approval wait"

  # Integration Tier (Event-Driven)
  integration_tier:

    async_messaging:
      technology: "Azure Service Bus"
      purpose: "Decouple components (pub/sub pattern)"
      open_protocol: "AMQP (Advanced Message Queuing Protocol)"
      topology: "Topics + Subscriptions (gate-events, work-events, issue-events)"
      cost: "$10/month (Standard tier)"
      fitness_functions:
        - "Message delivery: 99.9% success"
        - "Latency: <100ms publish to receive"

    api_gateway:
      technology: "Azure Front Door (initially) OR APIM (if advanced features needed)"
      purpose: "Single API entry point, caching, rate limiting"
      cost: "$50/month (Front Door) OR $700/month (APIM)"

  # UI Tier (Always-On, 99.9% SLA)
  ui_tier:

    web_ui:
      technology: "React 18 + Next.js 14 (Static Site Generation)"
      hosting: "Azure Static Web Apps + CDN"
      availability: "99.9% SLA (higher than API tier)"
      cost: "$10/month"
      fitness_functions:
        - "Page load: <2 seconds (p95)"
        - "Availability: 99.9%"

    mobile_ui:
      technology: "React Native + Expo"
      offline_support: "WatermelonDB (SQLite, offline-first)"
      cost: "$0/month (client-side app)"

    dashboards:
      technology: "Power BI Embedded"
      embedding: "Embedded in React app"
      sso: "Azure AD passthrough"
      cost: "$735/month (A1 SKU, 1 vCore)"

  # Operations Tier
  operations_tier:

    logging:
      technology: "Azure Application Insights"
      log_pattern: "12-factor: logs as event streams (stdout → App Insights)"
      retention: "90 days hot, export to Data Lake for long-term"
      cost: "$50/month (estimated 25GB logs)"

    monitoring:
      technology: "Azure Monitor + Application Insights"
      alerts: "API response time >500ms, availability <99%, failures >1%"
      cost: "Included in App Insights"

  # Security Tier
  security_tier:

    authentication:
      technology: "Azure Active Directory (Entra ID)"
      protocol: "OAuth 2.0 / OpenID Connect"
      sso: "Corporate credentials (Retail Chain AAD tenant)"
      cost: "Included in Microsoft 365 Enterprise"

    authorization:
      technology: "Azure AD Application Roles + RBAC"
      roles: "PMO, USRD, FieldTech, Vendor, Executive"
      enforcement: "API level (attribute-based authorization)"
      cost: "$0 (built-in)"

    secrets_management:
      technology: "Azure Key Vault"
      purpose: "Database connection strings, API keys"
      access: "Managed Identity (no secrets in code/config)"
      cost: "$5/month"

  # Total Cost Summary
  total_cost:
    monthly: "$3,630/month"
    annual: "$43,560/year"

    breakdown:
      data_tier: "$2,200/month (SQL $2K, Data Lake $100, Redis $100)"
      compute_tier: "$570/month (Container Apps $500, Functions $70)"
      integration_tier: "$60/month (Service Bus $10, Front Door $50)"
      ui_tier: "$745/month (Static Web Apps $10, Power BI $735)"
      operations_tier: "$50/month (App Insights)"
      security_tier: "$5/month (Key Vault)"

    vs_budget_nfr: "Within $150K/year budget ($43,560 < $150,000 ✓)"

    cost_optimization:
      - "Reserved instances (1-year): Save $6K/year (15% discount)"
      - "Power BI: Start with Power BI Pro ($10/user) instead of Embedded, upgrade later"
      - "Container Apps: Scale to zero on weekends, save $200/month"
      - "Potential savings: $10K/year"

  # Skills Gap Analysis
  skills_gap:
    total_technologies: 15
    team_likely_proficient: 11 (73%)
    training_needed: 4 (27%)

    gaps:
      - technology: "Azure Synapse Serverless SQL"
        current: "Beginner"
        required: "Intermediate"
        training: "2 weeks (Microsoft Learn)"
        priority: "HIGH"

      - technology: "Parquet / Data Lake"
        current: "None"
        required: "Beginner"
        training: "1 week (hands-on lab)"
        priority: "HIGH"

      - technology: "React Native + Expo"
        current: "Beginner (have React web)"
        required: "Intermediate"
        training: "3 weeks (vendor training)"
        priority: "MEDIUM"

      - technology: "Azure Durable Functions"
        current: "None"
        required: "Intermediate"
        training: "1 week (Microsoft docs + sample)"
        priority: "MEDIUM"

  # Fitness Functions (Evolutionary Architecture)
  fitness_functions:

    performance:
      - "API response time: <500ms (p95) - Automated test on every deploy"
      - "Dashboard load time: <2 sec - Lighthouse CI on every PR"
      - "Batch job completion: <30 min - Alert if exceeds"

    availability:
      - "UI uptime: 99.9% - Monitor via Azure Monitor, alert if <99.9% monthly"
      - "API uptime: 99.5% - Monitor via Azure Monitor"

    scalability:
      - "Load test: 500 concurrent users, <500ms response - Run monthly"
      - "Data volume: Query 10M events in <30 sec - Test quarterly"

    data_portability:
      - "SQL export: Export SQL database to CSV, import to Postgres <1 hour - Test quarterly"
      - "Parquet portability: Query Data Lake from DuckDB, Spark, Trino - Test on every schema change"

    cost:
      - "Monthly cost: <$4,000 - Alert if exceeds $5,000 (20% buffer)"
      - "Cost per store: <$20/month - Track as metric"

    security:
      - "No secrets in code: Scan repo for secrets on every commit - Block merge if found"
      - "TDE encryption: Verify all databases have TDE enabled - Monthly audit"

  # Migration Path (Evolutionary Architecture)
  migration_path:

    phase_1_pi1:
      components: "Progress_Analyst, Gate_Manager, Field_Operations"
      technologies: "SQL Database, Data Lake (Bronze only), Redis, Container Apps"
      cost: "$1,500/month"
      duration: "3 months"

    phase_2_pi2:
      components: "Add 4 more components"
      technologies: "Add Service Bus (event-driven), Silver/Gold layers in Data Lake"
      cost: "$2,500/month"
      duration: "3 months"

    phase_3_pi3:
      components: "Remaining 3 components + ML"
      technologies: "Add Azure ML (predictive models)"
      cost: "$3,630/month (full stack)"
      duration: "3 months"

    evolution_strategy:
      - "Start simple: SQL + Redis + Container Apps (proven, low risk)"
      - "Add complexity incrementally: Data Lake → Event-driven → ML"
      - "Prove fitness functions at each phase (performance, cost, portability)"
      - "Allow technology swaps: If Azure SQL too expensive, migrate to Postgres (fitness function: portability)"

  # Decision Log
  decision_log:

    eliminated_technologies:

      cosmos_db:
        reason: "Proprietary API, 2-4x cost, violates 'open protocols' principle"
        replaced_with: "SQL Database (JSONB column) + Data Lake (Parquet)"

      kafka:
        reason: "Ops burden, overkill for low-volume messaging (<10K msgs/hour)"
        replaced_with: "Azure Service Bus (managed, AMQP protocol)"

      databricks_delta_lake:
        reason: "Overkill for append-only event logs, adds complexity"
        replaced_with: "Parquet files (simpler, sufficient)"

      azure_kubernetes_service:
        reason: "Too complex for small team (3-person DevOps)"
        replaced_with: "Azure Container Apps (simpler, serverless)"

    deferred_technologies:

      azure_ml:
        reason: "Predictive models not needed in PI-1"
        timeline: "Add in PI-3 (after 3 months operational data for training)"

      temporal_io:
        reason: "Workflows are simple in PI-1 (4-gate approval)"
        timeline: "Migrate from Durable Functions if workflows become complex"

      graphql:
        reason: "REST APIs sufficient for current use cases"
        timeline: "Add if mobile app needs flexible querying"
```

## Usage Instructions

1. **Extract NFRs from project context** (KPIs, stakeholder interviews, discovery questionnaires)
2. **Apply architectural principles** (12-factor, evolutionary, user-specific concerns)
3. **Define minimal data store architecture** (3 tiers: SQL, Data Lake, Redis)
4. **Map components to data stores** (each component owns SQL DB, writes events to Data Lake)
5. **Select technologies by layer** (data, compute, integration, UI, ops, security)
6. **Define fitness functions** (automated tests for NFRs)
7. **Document decisions** (what was eliminated, why, what replaces it)

## Validation Checklist

✅ **Data lives longer than code**: Open formats (SQL, Parquet, CSV)
✅ **Minimal data stores**: 3 technologies (SQL, Data Lake, Redis) instead of 5+ (eliminated Cosmos DB)
✅ **Open protocols**: SQL (ANSI standard), Parquet (Apache standard), AMQP (open standard)
✅ **Evolution speed separation**: UI (static site), API (containers), Batch (functions), Data (versioned schemas)
✅ **UI always-on**: 99.9% SLA via CDN (higher than API tier)
✅ **12-factor compliance**: Stateless processes, config in env, logs as streams, backing services
✅ **Evolutionary architecture**: Fitness functions, incremental migration, technology swaps possible
✅ **Cost within NFR**: $43,560/year < $150,000/year budget
✅ **Skills gap manageable**: 73% team proficient, 27% trainable in 2-4 weeks
