# SKILL: /solution-mapper

## Purpose
Maps validated requirements to solution architecture through **Solution Archetypes** - reusable patterns that define canonical entity types (ontology), components, and implementation patterns. Ensures solutions are built from proven architectural patterns rather than invented from scratch.

## The Missing Layer Problem

**What we did wrong before:**
```
Requirements → Solution Components (Progress_Analyst, Gate_Manager)
```

This skipped the **Solution Archetype** layer - the reusable pattern that applies across industries.

**Correct approach:**
```
Requirements → Solution Archetype → Solution Components → Universal Components → Tech Stack
```

## Step 1: Solution Archetype Catalog

### Archetype 1: Digital Twin with Ontology-Based Operations

**Definition**: Digital representation of physical processes/assets synchronized in real-time, with semantic ontology defining the mapping between physical and digital worlds.

**Characteristics**:
- Physical process happening in real world (construction, manufacturing, logistics)
- Virtual representation updated in real-time (state sync)
- Ontology defines entity types, properties, relationships
- Deviation detection (actual vs. planned)
- Historical tracking (audit trail of state changes)

**Canonical Ontology**:
```yaml
entity_types:

  PhysicalAsset:
    description: "Physical object being tracked (store, machine, shipment)"
    properties:
      - asset_id (identifier)
      - asset_type (classification)
      - location (physical coordinates or site)
      - status (current state: operational, under construction, failed)
      - metadata (flexible properties)

  Process:
    description: "Multi-step workflow with gates/milestones"
    properties:
      - process_id
      - process_type (construction, installation, production)
      - start_date
      - target_completion_date
      - current_phase
    relationships:
      - applies_to: PhysicalAsset

  WorkUnit:
    description: "Atomic unit of work (task, checklist item)"
    properties:
      - work_id
      - description
      - status (not_started, in_progress, completed, blocked)
      - assigned_to (person or team)
      - actual_start_date
      - actual_completion_date
    relationships:
      - part_of: Process
      - assigned_to: Actor

  Gate:
    description: "Quality checkpoint in process (approval required to proceed)"
    properties:
      - gate_id
      - gate_type
      - criteria (checklist of requirements)
      - status (pending, approved, rejected)
      - approved_by
      - approved_date
    relationships:
      - guards: Process
      - has_criteria: WorkUnit[]

  Actor:
    description: "Person or organization performing work"
    properties:
      - actor_id
      - actor_type (employee, contractor, vendor)
      - certifications
      - performance_metrics
    relationships:
      - performs: WorkUnit[]

  Issue:
    description: "Deviation from plan (blocker, defect, delay)"
    properties:
      - issue_id
      - severity (critical, high, medium, low)
      - status (open, assigned, resolved)
      - root_cause_category
    relationships:
      - blocks: WorkUnit
      - assigned_to: Actor

  Measurement:
    description: "Telemetry from physical world (sensors, manual logs)"
    properties:
      - measurement_id
      - measurement_type
      - value
      - timestamp
      - source (sensor_id or person_id)
    relationships:
      - about: PhysicalAsset
```

**Canonical Components**:
```yaml
canonical_components:

  Asset_Registry:
    purpose: "Master data for physical assets"
    entity_types_owned: [PhysicalAsset]
    operations: [create_asset, update_location, update_status, query_asset]

  State_Synchronizer:
    purpose: "Sync physical state → digital representation"
    entity_types_owned: [Measurement, WorkUnit (status updates)]
    operations: [ingest_telemetry, update_work_status, detect_deviations]

  Process_Orchestrator:
    purpose: "Manage multi-step processes and gates"
    entity_types_owned: [Process, Gate]
    operations: [create_process, advance_phase, approve_gate, reject_gate]

  Work_Tracker:
    purpose: "Track work unit execution"
    entity_types_owned: [WorkUnit]
    operations: [assign_work, start_work, complete_work, report_progress]

  Issue_Manager:
    purpose: "Deviation detection and resolution"
    entity_types_owned: [Issue]
    operations: [create_issue, assign_issue, resolve_issue, analyze_root_cause]

  Performance_Analyzer:
    purpose: "Analytics on historical data (trends, predictions)"
    entity_types_owned: [None - reads from all entities]
    operations: [calculate_progress, predict_delays, analyze_actor_performance]

  Digital_Twin_Viewer:
    purpose: "Visualization of digital twin state"
    entity_types_owned: [None - reads from all entities]
    operations: [render_asset_state, show_process_status, display_deviations]
```

**Required Capabilities**:
```yaml
required_capabilities:

  ontology_layer:
    must_have:
      - Semantic schema (entity type definitions, properties, relationships)
      - Schema versioning (support ontology evolution)
      - Query interface (SQL, GraphQL, SPARQL, or API)
    nice_to_have:
      - Natural language query (NL2Ontology, NL2SQL)
      - AI agent integration (agents can reason over ontology)
      - Visual ontology designer
    implementation_examples:
      - "Microsoft Fabric IQ Ontology (cloud, proprietary)"
      - "Azure Digital Twins with DTDL (cloud, proprietary)"
      - "AWS Neptune with SPARQL (cloud, open protocol)"
      - "PostgreSQL with custom ontology tables (open-source)"
      - "Neo4j graph database (open-source/commercial)"

  state_storage:
    must_have:
      - ACID transactions (handle concurrent updates to digital twin state)
      - Time-series support (track state changes over time)
      - Queryable historical data (audit trail)
      - Open data format (data outlives code)
    nice_to_have:
      - Delta/upsert support (efficient state updates)
      - Partitioning by asset or time (performance)
    data_structure_pattern: |
      /storage/
        /ontology_definitions/  (entity types, properties, relationships)
        /ontology_instances/    (actual asset records, work units, gates)
        /measurements/          (telemetry, time-series data)
    implementation_examples:
      - "Lakehouse: Delta Lake on S3/ADLS (open format)"
      - "PostgreSQL with JSONB columns (open-source)"
      - "TimescaleDB (PostgreSQL extension for time-series)"
      - "InfluxDB (time-series database)"

  real_time_sync:
    must_have:
      - Event ingestion (field devices → digital twin)
      - Stream processing (transform events → state updates)
      - At-least-once delivery (no data loss)
      - Low latency (<5 seconds p95)
    nice_to_have:
      - Exactly-once semantics
      - Backpressure handling
    data_flow_pattern: "Field devices → Event stream → Stream processor → State storage"
    implementation_examples:
      - "Kafka + Kafka Streams (open-source)"
      - "Azure Event Hubs + Stream Analytics (cloud)"
      - "AWS Kinesis + Lambda (cloud)"
      - "RabbitMQ + custom processor (open-source)"

  analytics:
    must_have:
      - SQL interface (standard query language)
      - Query ontology instances (join across entity types)
      - Aggregations (sum, average, count, percentiles)
    nice_to_have:
      - Query pushdown (don't move data, query in place)
      - Materialized views (pre-aggregate for performance)
    implementation_examples:
      - "Synapse Serverless SQL (cloud)"
      - "Presto/Trino over S3 (open-source)"
      - "PostgreSQL with foreign data wrappers"
      - "DuckDB (embedded analytics)"
```

**Example Domains**:
- **Retail Store Opening**: Stores (PhysicalAsset), Gate process, Work units (V3 checklist), Vendors (Actor)
- **Manufacturing**: Production lines (PhysicalAsset), Production orders (Process), Tasks (WorkUnit), Machines (Actor)
- **Supply Chain**: Shipments (PhysicalAsset), Delivery process, Handling tasks (WorkUnit), Carriers (Actor)
- **Building Operations**: Buildings (PhysicalAsset), Maintenance process, Work orders (WorkUnit), Technicians (Actor)

**When to use this archetype**:
- ✅ Physical process with digital tracking
- ✅ Multi-step workflow with quality gates
- ✅ Real-time state synchronization needed
- ✅ Deviation detection important
- ✅ Historical audit trail required
- ❌ Pure CRUD application (use System of Record instead)
- ❌ No physical component (use Workflow Orchestrator instead)

---

### Archetype 2: System of Record

**Definition**: Authoritative source for master data with CRUD operations, data quality enforcement, and change history.

**Characteristics**:
- Single source of truth for entities (customers, products, employees)
- Strong data quality rules (validation, de-duplication)
- Audit trail (who changed what, when)
- API-first (other systems consume data via API)
- Slow-changing (data changes infrequently)

**Canonical Ontology**:
```yaml
entity_types:

  MasterEntity:
    description: "Core business entity (customer, product, vendor)"
    properties:
      - entity_id (unique identifier)
      - entity_type
      - status (active, inactive, archived)
      - created_date
      - created_by
      - last_modified_date
      - last_modified_by

  EntityAttribute:
    description: "Flexible attributes for master entity"
    properties:
      - attribute_name
      - attribute_value
      - attribute_type (string, number, date, boolean)
    relationships:
      - belongs_to: MasterEntity

  EntityRelationship:
    description: "Relationship between master entities"
    properties:
      - relationship_type (parent_of, subsidiary_of, owns)
      - from_entity
      - to_entity
      - effective_date
      - end_date

  ChangeLog:
    description: "Audit trail of all changes"
    properties:
      - change_id
      - entity_id
      - field_changed
      - old_value
      - new_value
      - changed_by
      - changed_date
      - reason
```

**Canonical Components**:
```yaml
canonical_components:

  Entity_Repository:
    purpose: "CRUD operations on master entities"
    operations: [create, read, update, delete, search]

  Data_Quality_Engine:
    purpose: "Validate, de-duplicate, enrich data"
    operations: [validate, find_duplicates, merge_records, enrich_from_external]

  Change_Tracker:
    purpose: "Audit trail of all changes"
    operations: [log_change, query_history, rollback]

  API_Gateway:
    purpose: "Expose master data to consumers"
    operations: [get_entity, search_entities, subscribe_to_changes]
```

**Required Capabilities**:
```yaml
required_capabilities:

  storage:
    must_have:
      - ACID transactions (data consistency)
      - Referential integrity (foreign keys, constraints)
      - Data validation (type checking, format validation)
      - Schema management (migrations, versioning)
    nice_to_have:
      - JSONB/flexible schema (handle varying attributes)
      - Full-text search
    implementation_examples:
      - "PostgreSQL (open-source, full ACID)"
      - "MySQL (open-source)"
      - "Azure SQL Database (cloud)"
      - "Amazon RDS (cloud)"

  api:
    must_have:
      - REST API with standard verbs (GET, POST, PUT, DELETE)
      - API versioning (v1, v2 for backward compatibility)
      - Authentication & authorization
      - Rate limiting
    nice_to_have:
      - GraphQL (flexible querying)
      - OpenAPI/Swagger documentation
    implementation_examples:
      - "ASP.NET Core Web API"
      - "FastAPI (Python)"
      - "Express.js (Node.js)"
      - "Spring Boot (Java)"

  caching:
    must_have:
      - Key-value storage (fast entity lookup by ID)
      - TTL support (cache expiration)
      - Invalidation strategy (clear stale data)
    nice_to_have:
      - Distributed caching (multi-node)
      - Cache-aside pattern support
    implementation_examples:
      - "Redis (open-source)"
      - "Memcached (open-source)"
      - "Azure Cache for Redis (cloud)"
      - "Amazon ElastiCache (cloud)"

  change_data_capture:
    must_have:
      - Capture all data changes (insert, update, delete)
      - Publish changes to event stream
      - Ordering guarantees (changes in sequence)
    nice_to_have:
      - Schema evolution support
      - Filtering (only publish certain changes)
    implementation_examples:
      - "Debezium (PostgreSQL/MySQL CDC, open-source)"
      - "SQL Server CDC + Service Bus"
      - "AWS DMS (Database Migration Service with CDC)"
      - "Custom triggers + message queue"
```

**Example Domains**:
- Customer Master Data Management (MDM)
- Product Information Management (PIM)
- Employee Directory
- Vendor Registry

**When to use this archetype**:
- ✅ Need single source of truth
- ✅ Data quality critical
- ✅ Multiple systems consume same data
- ✅ Audit trail required
- ❌ Real-time telemetry (use Digital Twin)
- ❌ Complex workflows (use Workflow Orchestrator)

---

### Archetype 3: Workflow Orchestrator

**Definition**: Multi-step process automation with human-in-the-loop approvals, parallel/sequential tasks, and state management.

**Characteristics**:
- Defined workflow steps (sequential, parallel, conditional)
- State persistence (pause/resume workflows)
- Human approvals (wait for human decision)
- SLA tracking (deadlines, escalation)
- Rollback/compensation (undo failed workflows)

**Canonical Ontology**:
```yaml
entity_types:

  WorkflowDefinition:
    description: "Template for repeatable workflow"
    properties:
      - workflow_id
      - workflow_name
      - version
      - steps[] (ordered list of steps)

  WorkflowInstance:
    description: "Execution of workflow for specific case"
    properties:
      - instance_id
      - workflow_id
      - status (running, paused, completed, failed)
      - started_date
      - completed_date
      - current_step

  Step:
    description: "Single action in workflow"
    properties:
      - step_id
      - step_type (automated, human_approval, parallel, conditional)
      - assigned_to (if human step)
      - due_date
      - status (pending, in_progress, completed, skipped)
    relationships:
      - part_of: WorkflowInstance

  Approval:
    description: "Human decision point"
    properties:
      - approval_id
      - decision (approved, rejected)
      - approver
      - approval_date
      - comments
    relationships:
      - for_step: Step
```

**Canonical Components**:
```yaml
canonical_components:

  Workflow_Engine:
    purpose: "Execute workflow steps, manage state"
    operations: [start_workflow, advance_step, pause, resume, cancel]

  Approval_Manager:
    purpose: "Human-in-the-loop approvals"
    operations: [request_approval, submit_decision, escalate]

  SLA_Monitor:
    purpose: "Track deadlines, trigger escalations"
    operations: [check_deadlines, escalate_overdue, send_reminders]

  Workflow_Designer:
    purpose: "Define/edit workflow templates"
    operations: [create_workflow, add_step, define_conditions]
```

**Required Capabilities**:
```yaml
required_capabilities:

  orchestration:
    must_have:
      - Stateful workflow execution (pause/resume)
      - Long-running processes (hours/days/weeks)
      - Human-in-the-loop (wait for external input)
      - Error handling (retry, compensation, rollback)
      - Workflow versioning (deploy new versions without breaking in-flight workflows)
    nice_to_have:
      - Visual workflow designer
      - Parallel execution (run steps concurrently)
      - Conditional branching (if/else logic)
      - Sub-workflows (reusable workflow components)
    implementation_examples:
      - "Temporal.io (open-source, cloud-agnostic)"
      - "Azure Durable Functions (cloud)"
      - "AWS Step Functions (cloud)"
      - "Camunda (open-source/commercial)"
      - "Apache Airflow (open-source, batch-oriented)"

  state_storage:
    must_have:
      - Persist workflow state (survive process restarts)
      - ACID transactions (consistency)
      - Query workflow instances (find paused/failed workflows)
    nice_to_have:
      - Time-travel debugging (replay workflow from any point)
      - State compression (minimize storage)
    implementation_examples:
      - "PostgreSQL (open-source)"
      - "Azure Storage (cloud, for Durable Functions)"
      - "MySQL (open-source)"
      - "Cassandra (distributed, eventual consistency)"

  notifications:
    must_have:
      - Send alerts (email, SMS, push)
      - Template support (parameterized messages)
      - Delivery tracking (confirm notification sent)
    nice_to_have:
      - Multi-channel (email + SMS + in-app)
      - Escalation policies (retry with different channels)
    implementation_examples:
      - "SendGrid (email, SaaS)"
      - "Twilio (SMS, SaaS)"
      - "Azure Communication Services (cloud)"
      - "SMTP server + Kafka (open-source)"
```

**Example Domains**:
- Purchase Order Approval (3-tier approval: manager → director → CFO)
- Employee Onboarding (multi-department coordination)
- Change Request Management (submit → review → approve → implement)
- **Retail Store Gate Approval** (checklist → review → approve/reject)

**When to use this archetype**:
- ✅ Multi-step process with defined sequence
- ✅ Human approvals required
- ✅ Long-running (hours/days/weeks)
- ✅ Need pause/resume capability
- ❌ Simple CRUD (use System of Record)
- ❌ Real-time streaming (use Event Processing)

---

### Archetype 4: Analytics Platform

**Definition**: Transform raw data into insights via aggregations, visualizations, and predictive models.

**Characteristics**:
- Read-heavy (queries, not writes)
- Historical data (analyze trends over time)
- Aggregations (sum, average, count, percentiles)
- Visualizations (charts, dashboards)
- Predictive models (forecasting, anomaly detection)

**Canonical Ontology**:
```yaml
entity_types:

  DataSource:
    description: "Origin of data (databases, APIs, files)"
    properties:
      - source_id
      - source_type (sql_database, api, csv_file)
      - connection_info
      - refresh_schedule

  Dataset:
    description: "Curated data for analysis (fact tables, dimensions)"
    properties:
      - dataset_id
      - dataset_name
      - schema
      - last_refresh_date
    relationships:
      - sourced_from: DataSource[]

  Metric:
    description: "Calculated measure (KPI, aggregation)"
    properties:
      - metric_id
      - metric_name
      - formula (SQL expression)
      - unit (dollars, hours, percentage)

  Report:
    description: "Visualization of data"
    properties:
      - report_id
      - report_type (dashboard, paginated_report)
      - visualizations[] (charts, tables, cards)
    relationships:
      - uses: Dataset[]
      - calculates: Metric[]
```

**Canonical Components**:
```yaml
canonical_components:

  Data_Ingestion:
    purpose: "Extract data from sources → lakehouse"
    operations: [extract, transform, load, schedule_refresh]

  Semantic_Model:
    purpose: "Define business logic (metrics, relationships)"
    operations: [define_metric, create_relationship, set_security]

  Query_Engine:
    purpose: "Execute analytical queries"
    operations: [aggregate, filter, join, cache_results]

  Visualization_Layer:
    purpose: "Render charts, dashboards, reports"
    operations: [create_dashboard, export_pdf, embed_report]
```

**Required Capabilities**:
```yaml
required_capabilities:

  storage:
    must_have:
      - Columnar format (efficient analytical queries)
      - Open format (avoid vendor lock-in, data outlives tools)
      - Schema evolution (add columns without breaking existing queries)
      - Partitioning (optimize query performance)
    nice_to_have:
      - Time-travel (query historical versions)
      - ACID on data lake (Delta Lake pattern)
    implementation_examples:
      - "Parquet files in S3/ADLS (open format)"
      - "Delta Lake (open format with ACID)"
      - "Apache Iceberg (open format with ACID)"
      - "PostgreSQL with columnar extension (cstore_fdw)"

  semantic_layer:
    must_have:
      - Business logic layer (metrics defined once, used everywhere)
      - Relationships (join logic abstracted from users)
      - Security (row-level, column-level access control)
    nice_to_have:
      - Natural language queries (NL2SQL)
      - Self-service data modeling
    implementation_examples:
      - "dbt (open-source, SQL-based transformations)"
      - "Power BI Semantic Model (cloud)"
      - "Looker LookML (cloud)"
      - "Apache Superset (open-source)"
      - "MetaBase (open-source)"

  query_engine:
    must_have:
      - SQL interface (standard analytics language)
      - Query optimization (push-down, predicate pruning)
      - Scalability (handle GB to TB data)
    nice_to_have:
      - Query federation (join data from multiple sources)
      - MPP (massively parallel processing)
    implementation_examples:
      - "DuckDB (embedded, in-process analytics)"
      - "Presto/Trino (open-source, distributed SQL)"
      - "Apache Spark SQL (open-source)"
      - "Synapse Serverless SQL (cloud)"
      - "Amazon Athena (cloud)"

  visualization:
    must_have:
      - Charts (line, bar, pie, scatter)
      - Dashboards (multiple visualizations, filters)
      - Export (PDF, Excel, image)
      - Embedding (integrate into applications)
    nice_to_have:
      - Real-time updates (live dashboards)
      - Mobile-responsive
      - Natural language interface
    implementation_examples:
      - "Apache Superset (open-source)"
      - "Grafana (open-source)"
      - "Plotly Dash (Python, open-source)"
      - "Power BI (cloud/desktop)"
      - "Tableau (commercial)"
      - "Custom: React + Recharts/Victory (open-source libraries)"
```

**Example Domains**:
- Sales Analytics (revenue, pipeline, win rate)
- Operations Analytics (efficiency, utilization, SLA compliance)
- **Retail Store Progress Analytics** (portfolio health, at-risk stores, vendor performance)

**When to use this archetype**:
- ✅ Need insights from historical data
- ✅ Dashboards/reports required
- ✅ Predictive analytics valuable
- ❌ Real-time operational decisions (use Digital Twin)
- ❌ Transactional workloads (use System of Record)

---

### Archetype 5: Integration Platform

**Definition**: Connect disparate systems via data synchronization, API orchestration, and event distribution.

**Characteristics**:
- System-to-system communication (no human users)
- Data transformation (map between different schemas)
- Protocol translation (REST → SOAP, File → API)
- Error handling (retry, dead-letter queue)
- Monitoring (data flow observability)

**Canonical Ontology**:
```yaml
entity_types:

  System:
    description: "External system to integrate with"
    properties:
      - system_id
      - system_name
      - system_type (erp, crm, legacy, api)
      - endpoint (URL or connection string)
      - authentication (credentials, API key)

  DataFlow:
    description: "Movement of data between systems"
    properties:
      - flow_id
      - source_system
      - target_system
      - flow_type (batch, real_time, on_demand)
      - schedule (if batch)
    relationships:
      - from: System
      - to: System

  Transformation:
    description: "Mapping between source and target schemas"
    properties:
      - transformation_id
      - source_schema
      - target_schema
      - mapping_rules (field mappings, transformations)
    relationships:
      - used_in: DataFlow

  FlowExecution:
    description: "Instance of data flow run"
    properties:
      - execution_id
      - flow_id
      - start_time
      - end_time
      - status (success, failed, partial)
      - records_processed
      - errors[]
```

**Canonical Components**:
```yaml
canonical_components:

  Connector_Registry:
    purpose: "Manage system connections and credentials"
    operations: [register_system, test_connection, rotate_credentials]

  ETL_Engine:
    purpose: "Extract, transform, load data"
    operations: [extract_from_source, apply_transformations, load_to_target]

  API_Orchestrator:
    purpose: "Coordinate multi-step API calls"
    operations: [call_api, aggregate_responses, handle_errors]

  Event_Router:
    purpose: "Distribute events to subscribers"
    operations: [publish_event, subscribe, route_to_handlers]

  Error_Handler:
    purpose: "Retry failed integrations, alert on errors"
    operations: [retry_with_backoff, send_to_dlq, alert_ops]
```

**Required Capabilities**:
```yaml
required_capabilities:

  batch_integration:
    must_have:
      - Scheduled execution (cron, time-based triggers)
      - Data transformation (map source → target schema)
      - Error handling (retry, logging)
      - Incremental loading (only new/changed records)
    nice_to_have:
      - Visual pipeline designer
      - Parallel execution (process batches concurrently)
      - Data quality checks (validation, profiling)
    implementation_examples:
      - "Apache Airflow (open-source, Python-based)"
      - "Prefect (open-source, Python-based)"
      - "Azure Data Factory (cloud)"
      - "AWS Glue (cloud)"
      - "Pentaho Data Integration (open-source)"

  real_time_integration:
    must_have:
      - Message broker (pub/sub pattern)
      - At-least-once delivery (reliability)
      - Ordering guarantees (FIFO for related messages)
      - Protocol support (HTTP, AMQP, MQTT)
    nice_to_have:
      - Exactly-once semantics
      - Message filtering (routing rules)
      - Schema registry (enforce message structure)
    implementation_examples:
      - "Apache Kafka (open-source)"
      - "RabbitMQ (open-source, AMQP)"
      - "Azure Service Bus (cloud)"
      - "Amazon SQS/SNS (cloud)"
      - "NATS (open-source, lightweight)"

  api_orchestration:
    must_have:
      - Multi-step API coordination (call API A, then B, then C)
      - Error handling (retry, fallback)
      - Request/response transformation
      - Authentication handling (OAuth, API keys)
    nice_to_have:
      - Visual workflow designer
      - Conditional logic (if/else branching)
      - Parallel API calls
    implementation_examples:
      - "Custom orchestrator (FastAPI, Express.js)"
      - "n8n (open-source, workflow automation)"
      - "Azure Logic Apps (cloud)"
      - "AWS Step Functions (cloud)"
      - "Temporal.io (open-source, code-first)"

  error_handling:
    must_have:
      - Dead-letter queue (failed messages for manual review)
      - Exponential backoff (retry with increasing delays)
      - Alerting (notify on repeated failures)
      - Logging (audit trail of all errors)
    nice_to_have:
      - Circuit breaker (stop calling failing service)
      - Poison message detection (identify messages that always fail)
    implementation_examples:
      - "Kafka + DLQ topic (open-source)"
      - "RabbitMQ + DLQ exchange (open-source)"
      - "Azure Service Bus + DLQ (cloud)"
      - "Custom: Message queue + monitoring service"
```

**Example Domains**:
- ERP ↔ CRM synchronization
- Legacy system modernization (mainframe → cloud)
- **Retail Store Opening ↔ Store Master, Finance, HR systems**

**When to use this archetype**:
- ✅ Need to connect 3+ disparate systems
- ✅ Data transformation required
- ✅ Different protocols/formats
- ❌ Single system (no integration needed)
- ❌ Real-time UI (use different archetype)

---

### Archetype 6: Collaboration Hub

**Definition**: Enable teams to communicate, share content, and coordinate work.

**Characteristics**:
- Multi-user (real-time collaboration)
- Content sharing (documents, files, links)
- Threaded conversations (comments, replies)
- Notifications (mentions, updates)
- Presence (who's online, typing indicators)

**Canonical Ontology**:
```yaml
entity_types:

  Workspace:
    description: "Container for team collaboration (channel, project)"
    properties:
      - workspace_id
      - workspace_name
      - members[] (user IDs)
      - permissions (role-based access)

  Message:
    description: "Communication between users"
    properties:
      - message_id
      - content (text, rich formatting)
      - author
      - timestamp
      - reactions[] (emoji reactions)
    relationships:
      - in_workspace: Workspace
      - reply_to: Message (for threads)

  ContentItem:
    description: "Shared file or link"
    properties:
      - item_id
      - item_type (file, link, embed)
      - url_or_path
      - uploaded_by
      - upload_date
    relationships:
      - shared_in: Workspace
```

**Canonical Components**:
```yaml
canonical_components:

  Messaging_Engine:
    purpose: "Real-time message delivery"
    operations: [send_message, edit_message, react, create_thread]

  Content_Repository:
    purpose: "Store and serve shared files"
    operations: [upload_file, download_file, preview, version_history]

  Notification_Service:
    purpose: "Alert users of activity"
    operations: [notify_mention, notify_reply, send_digest]

  Presence_Tracker:
    purpose: "Track user online status"
    operations: [update_status, check_who_is_online]
```

**Required Capabilities**:
```yaml
required_capabilities:

  real_time_messaging:
    must_have:
      - Bidirectional communication (server push to clients)
      - Low latency (<500ms message delivery)
      - Connection management (handle reconnects)
      - Broadcasting (send to all users in workspace)
    nice_to_have:
      - Typing indicators
      - Read receipts
      - Message history (offline message delivery)
    implementation_examples:
      - "WebSockets (native browser API)"
      - "Socket.io (open-source, Node.js)"
      - "SignalR (ASP.NET)"
      - "Phoenix Channels (Elixir)"
      - "Pusher (SaaS)"

  content_storage:
    must_have:
      - Object storage (files, images, attachments)
      - Secure URLs (signed URLs for access control)
      - Versioning (track file changes)
      - CDN integration (fast delivery)
    nice_to_have:
      - Virus scanning
      - Image thumbnailing
      - Video transcoding
    implementation_examples:
      - "MinIO (open-source, S3-compatible)"
      - "Amazon S3 (cloud)"
      - "Azure Blob Storage (cloud)"
      - "Google Cloud Storage (cloud)"
      - "Local filesystem + nginx (simple deployments)"

  notifications:
    must_have:
      - Multiple channels (in-app, email, push)
      - User preferences (opt-in/opt-out)
      - Batching (digest notifications)
    nice_to_have:
      - Smart notifications (reduce noise)
      - Notification history
    implementation_examples:
      - "Firebase Cloud Messaging (push, free tier)"
      - "OneSignal (push, freemium)"
      - "Custom: WebSockets (in-app) + SMTP (email)"
      - "Azure Notification Hubs (cloud)"
```

**Example Domains**:
- Team chat (Slack, Teams)
- Project collaboration (Asana, Jira comments)
- Document collaboration (Google Docs comments)

**When to use this archetype**:
- ✅ Team coordination needed
- ✅ Real-time communication
- ✅ Content sharing
- ❌ Workflow automation (use Workflow Orchestrator)
- ❌ Analytics (use Analytics Platform)

---

### Archetype 7: Marketplace/Portal

**Definition**: Connect buyers and sellers (or providers and consumers) with multi-tenancy, catalog, and transactions.

**Characteristics**:
- Multi-tenant (each seller/provider has isolated data)
- Catalog (browse/search offerings)
- Transactions (purchase, subscription)
- Ratings/reviews (social proof)
- Provider onboarding (self-service registration)

**Canonical Ontology**:
```yaml
entity_types:

  Provider:
    description: "Seller or service provider"
    properties:
      - provider_id
      - provider_name
      - verification_status
      - rating (average rating)

  Offering:
    description: "Product or service for sale"
    properties:
      - offering_id
      - title
      - description
      - price
      - category
    relationships:
      - offered_by: Provider

  Consumer:
    description: "Buyer or service consumer"
    properties:
      - consumer_id
      - consumer_name
      - payment_method

  Transaction:
    description: "Purchase or subscription"
    properties:
      - transaction_id
      - amount
      - transaction_date
      - status (pending, completed, refunded)
    relationships:
      - consumer: Consumer
      - offering: Offering

  Review:
    description: "Rating and feedback"
    properties:
      - review_id
      - rating (1-5 stars)
      - comment
      - review_date
    relationships:
      - by: Consumer
      - for: Offering
```

**Canonical Components**:
```yaml
canonical_components:

  Catalog_Manager:
    purpose: "Browse/search offerings"
    operations: [list_offerings, search, filter_by_category]

  Transaction_Processor:
    purpose: "Handle purchases"
    operations: [create_order, process_payment, issue_refund]

  Review_System:
    purpose: "Collect and display ratings"
    operations: [submit_review, calculate_average_rating, moderate_reviews]

  Provider_Portal:
    purpose: "Self-service for providers"
    operations: [register_provider, create_offering, view_analytics]
```

**Required Capabilities**:
```yaml
required_capabilities:

  multi_tenancy:
    must_have:
      - Data isolation (each provider's data separated)
      - Tenant provisioning (onboard new providers)
      - Tenant-scoped queries (prevent data leakage)
    nice_to_have:
      - Custom branding per tenant
      - Tenant-specific configuration
    implementation_examples:
      - "Row-level security in PostgreSQL (schema: tenant_id column)"
      - "Separate database per tenant"
      - "Shared database with tenant context middleware"

  catalog_search:
    must_have:
      - Full-text search (search titles, descriptions)
      - Faceted filtering (filter by category, price range)
      - Relevance ranking (sort results by relevance)
    nice_to_have:
      - Auto-complete
      - Typo tolerance
      - Semantic search (understand intent)
    implementation_examples:
      - "Elasticsearch (open-source)"
      - "Meilisearch (open-source, easy to use)"
      - "Vespa.ai (open-source, advanced search + vector search)"
      - "PostgreSQL full-text search (built-in)"
      - "Azure Cognitive Search (cloud)"
      - "Algolia (SaaS)"

  payment_processing:
    must_have:
      - Payment gateway integration
      - Transaction management (pending, completed, refunded)
      - PCI compliance (tokenization, don't store card data)
      - Multi-currency support
    nice_to_have:
      - Subscription billing
      - Split payments (marketplace takes commission)
    implementation_examples:
      - "Stripe (SaaS, developer-friendly)"
      - "PayPal (SaaS)"
      - "Braintree (SaaS)"
      - "Adyen (SaaS, enterprise)"

  review_moderation:
    must_have:
      - Review submission
      - Star ratings aggregation
      - Report inappropriate content
    nice_to_have:
      - AI-powered moderation (detect spam, abuse)
      - Verified purchase badges
    implementation_examples:
      - "Custom implementation (database + moderation queue)"
      - "Azure Content Moderator (cloud, AI moderation)"
      - "Perspective API by Google (free, toxicity detection)"
```

**Example Domains**:
- E-commerce marketplace (Amazon, Etsy)
- SaaS app marketplace (Salesforce AppExchange)
- Service marketplace (Upwork, Fiverr)

**When to use this archetype**:
- ✅ Multi-tenant (many providers)
- ✅ Catalog/search needed
- ✅ Transactions involved
- ❌ Single provider (use e-commerce site)
- ❌ Internal tool (use different archetype)

---

## Step 2: Map Requirements to Archetype

### Mapping Process

```yaml
archetype_identification:

  step_1_analyze_requirements:
    question: "What is the primary business problem?"

    indicators:
      digital_twin:
        - "Physical process with digital tracking"
        - "Real-time state synchronization"
        - "Deviation detection"
        - "Multi-step process with gates"
        - Keywords: "track", "monitor", "progress", "status", "real-time"

      system_of_record:
        - "Master data management"
        - "Single source of truth"
        - "Data quality enforcement"
        - "Multiple systems consume same data"
        - Keywords: "master", "registry", "authoritative", "CRUD"

      workflow_orchestrator:
        - "Multi-step approval process"
        - "Human-in-the-loop decisions"
        - "SLA tracking and escalation"
        - "Pause/resume workflows"
        - Keywords: "approval", "workflow", "escalate", "assign"

      analytics_platform:
        - "Historical trend analysis"
        - "Dashboards and reports"
        - "Predictive analytics"
        - "KPI tracking"
        - Keywords: "insights", "trends", "forecast", "dashboard", "report"

      integration_platform:
        - "Connect disparate systems"
        - "Data synchronization"
        - "Protocol translation"
        - Keywords: "integrate", "sync", "connect", "external system"

  step_2_match_to_archetype:
    process: |
      1. Extract keywords from requirements
      2. Count matches for each archetype
      3. Select archetype with highest match score
      4. If tie, select primary archetype + secondary patterns

  step_3_validate_match:
    questions:
      - "Does the archetype's canonical ontology match our domain entities?"
      - "Do the canonical components address our requirements?"
      - "Are there gaps (requirements not covered by archetype)?"
      - "Are there overlaps (need multiple archetypes)?"
```

### Example: Retail Store Opening Requirements → Archetype

```yaml
retail_store_mapping:

  requirements_summary:
    - "Track store construction and tech installation progress"
    - "Gate approval workflow (Construction G1/G2, Tech G1/G2)"
    - "Real-time work logging from field technicians"
    - "Vendor performance tracking"
    - "Issue triage and resolution"
    - "Executive dashboard with portfolio health"
    - "95% on-time gate completion KPI"

  keyword_analysis:
    digital_twin_keywords: ["track", "progress", "real-time", "status", "gate", "work logging"] = 6 matches
    workflow_orchestrator_keywords: ["gate approval", "workflow"] = 2 matches
    analytics_platform_keywords: ["dashboard", "portfolio health", "KPI", "vendor performance"] = 4 matches
    system_of_record_keywords: ["vendor", "store"] = 2 matches

  archetype_selection:
    primary: "Digital Twin with Ontology-Based Operations"
    rationale: |
      - Physical stores being constructed (PhysicalAsset)
      - Digital tracking via V3 Checklist (WorkUnit)
      - Real-time state sync (field techs log work → digital update)
      - Gate process with quality checkpoints (Gate entity)
      - Deviation detection (at-risk stores, issues)

    secondary_patterns:
      - "Workflow Orchestrator (for gate approval subprocess)"
      - "Analytics Platform (for executive dashboards)"
      - "System of Record (for vendor/store master data)"

  ontology_mapping:
    canonical_entity: "PhysicalAsset"
    maps_to: "Store"
    properties:
      - asset_id → store_id
      - location → store_location
      - status → construction_status

    canonical_entity: "Process"
    maps_to: "RSO Process (per store)"
    properties:
      - process_type → "New Store Opening"
      - current_phase → "Construction" | "Tech Installation" | "Final Validation"

    canonical_entity: "WorkUnit"
    maps_to: "V3 Checklist Item"
    properties:
      - description → checklist_item_description
      - assigned_to → vendor_id or tech_id

    canonical_entity: "Gate"
    maps_to: "Construction Gate 1, Construction Gate 2, Tech Gate 1, Tech Gate 2"
    properties:
      - criteria → checklist_items (must be 100% complete)

    canonical_entity: "Actor"
    maps_to: "Vendor"
    properties:
      - actor_type → "Vendor"
      - certifications → STP_certification_status

    canonical_entity: "Issue"
    maps_to: "Issue"
    properties:
      - severity → high/medium/low
      - blocks → work_unit_id
```

## Step 3: Instantiate Solution Components from Archetype

### Instantiation Process

```yaml
component_instantiation:

  from_archetype: "Digital Twin with Ontology-Based Operations"

  canonical_component: "Asset_Registry"
  instantiated_as: "Store_Master"
  customization:
    entity_types_owned: [Store (PhysicalAsset)]
    operations:
      - create_store(store_id, location, region, owner)
      - update_construction_status(store_id, new_status)
      - query_stores_by_region(region)

  canonical_component: "Process_Orchestrator"
  instantiated_as: "Gate_Manager"
  customization:
    entity_types_owned: [Gate, RSO_Process]
    operations:
      - submit_gate_for_approval(gate_id, store_id)
      - approve_gate(gate_id, approver, rationale)
      - reject_gate(gate_id, approver, reason)
      - query_pending_gates()

  canonical_component: "Work_Tracker"
  instantiated_as: "Field_Operations"
  customization:
    entity_types_owned: [WorkUnit (V3 Checklist Item)]
    operations:
      - log_work_completion(work_unit_id, tech_id, timestamp)
      - get_checklist_status(store_id) → % complete
      - assign_work_to_vendor(work_unit_id, vendor_id)

  canonical_component: "Issue_Manager"
  instantiated_as: "Issue_Resolver"
  customization:
    entity_types_owned: [Issue]
    operations:
      - create_issue(description, severity, store_id)
      - triage_issue(issue_id) → assign team
      - resolve_issue(issue_id, resolution, root_cause)

  canonical_component: "Performance_Analyzer"
  instantiated_as: "Progress_Analyst"
  customization:
    operations:
      - calculate_portfolio_progress(region) → % stores on track
      - identify_at_risk_stores(threshold_days) → stores likely to miss gates
      - analyze_vendor_performance(vendor_id) → on-time %, issue count

  canonical_component: "Digital_Twin_Viewer"
  instantiated_as: "Executive_Command_Center"
  customization:
    operations:
      - render_portfolio_dashboard() → RAG status, KPIs
      - show_store_detail(store_id) → current phase, gate status, issues
```

## Step 4: Map to Universal Components

(This layer already existed in previous version - maps solution components to technology-agnostic building blocks)

```yaml
solution_component: "Store_Master"
universal_components:
  - data_store (Store records)
  - api_gateway (Expose store data)
  - cache (Frequently accessed stores)

solution_component: "Gate_Manager"
universal_components:
  - data_store (Gate records)
  - workflow_orchestrator (Approval process)
  - notification_system (Alerts)
  - audit_log (Approval decisions)

solution_component: "Progress_Analyst"
universal_components:
  - analytics_engine (Aggregate progress)
  - dashboard (Visualize portfolio)
  - ml_inference (Predict delays)
```

## Output Format

### Solution Architecture Specification

**NOTE**: The example below shows a complete solution architecture for Retail Store Opening. Specific technology references (e.g., "Fabric IQ Ontology", "Azure OpenAI") are examples of ONE possible implementation. The solution-mapper outputs the archetype, ontology, and components. The tech-stack-mapper then selects specific technologies based on budget, skills, and NFRs.

```yaml
solution_architecture:

  project: "Retail Store Opening Smart Tracker (Example Implementation)"
  archetype: "Digital Twin with Ontology-Based Operations"
  secondary_patterns: ["Workflow Orchestrator", "Analytics Platform"]

  # Ontology Definition
  ontology:

    entity_types:

      Store:
        extends: "PhysicalAsset (from Digital Twin archetype)"
        properties:
          - store_id: string
          - store_number: string
          - location: geo_coordinates
          - region: string
          - owner: string
          - construction_status: enum [planning, foundation, framing, tech_installation, final_validation, open]
        relationships:
          - has_process: RSO_Process
          - has_gates: Gate[]

      RSO_Process:
        extends: "Process (from Digital Twin archetype)"
        properties:
          - process_id: string
          - store_id: string
          - start_date: date
          - target_open_date: date
          - current_phase: enum [construction, tech_installation, final_validation]
        relationships:
          - for_store: Store
          - has_gates: Gate[]

      V3_Checklist_Item:
        extends: "WorkUnit (from Digital Twin archetype)"
        properties:
          - work_id: string
          - checklist_item_number: string
          - description: string
          - status: enum [not_started, in_progress, completed, blocked]
          - assigned_to: string (vendor_id or tech_id)
          - gate_requirement: string (which gate this is required for)
          - actual_start_date: datetime
          - actual_completion_date: datetime
        relationships:
          - part_of: RSO_Process
          - assigned_to: Vendor
          - required_for: Gate

      Gate:
        extends: "Gate (from Digital Twin archetype)"
        properties:
          - gate_id: string
          - gate_type: enum [CG1, CG2, TG1, TG2]
          - store_id: string
          - target_date: date
          - status: enum [pending, submitted, approved, rejected]
          - checklist_completion_pct: number
          - approved_by: string
          - approved_date: datetime
          - rationale: text
        relationships:
          - guards: RSO_Process
          - requires: V3_Checklist_Item[]

      Vendor:
        extends: "Actor (from Digital Twin archetype)"
        properties:
          - vendor_id: string
          - vendor_name: string
          - vendor_type: enum [USRD, RPF, Contractor]
          - stp_certification_status: boolean
          - stp_expiration_date: date
          - performance_rating: number
        relationships:
          - performs: V3_Checklist_Item[]

      Issue:
        extends: "Issue (from Digital Twin archetype)"
        properties:
          - issue_id: string
          - description: text
          - severity: enum [critical, high, medium, low]
          - status: enum [new, triaged, assigned, resolved, closed]
          - root_cause_category: string
          - created_date: datetime
          - assigned_to: string (team or person)
          - resolution: text
        relationships:
          - blocks: V3_Checklist_Item
          - affects: Store

      Work_Event:
        extends: "Measurement (from Digital Twin archetype)"
        properties:
          - event_id: string
          - event_type: enum [work_started, work_completed, work_failed]
          - work_id: string
          - timestamp: datetime
          - technician_id: string
          - location: geo_coordinates (where work happened)
        relationships:
          - about: V3_Checklist_Item

  # Solution Components (instantiated from archetype)
  solution_components:

    - component_id: SC-001
      name: "Store_Master"
      archetype_component: "Asset_Registry"
      entity_types_owned: [Store]

      operations:
        - create_store(store_id, location, region, owner)
        - update_construction_status(store_id, new_status)
        - query_stores_by_region(region)
        - get_store_details(store_id)

      data_storage:
        ontology: "Fabric IQ Ontology (Store entity type)"
        instances: "Lakehouse: /ontology_instances/stores.delta"

      api:
        - "GET /api/stores → List all stores"
        - "GET /api/stores/{store_id} → Store details"
        - "PUT /api/stores/{store_id}/status → Update status"

      team_ownership: "Master Data Team (2 engineers)"
      # Budget determined by tech-stack-mapper based on technology choices and NFRs
      # budget: "$70K build, $25K/year sustaining"

    - component_id: SC-002
      name: "Gate_Manager"
      archetype_component: "Process_Orchestrator"
      entity_types_owned: [Gate, RSO_Process]

      operations:
        - create_rso_process(store_id)
        - submit_gate_for_approval(gate_id, submitted_by)
        - approve_gate(gate_id, approver, rationale)
        - reject_gate(gate_id, approver, reason)
        - query_pending_gates(approver)
        - calculate_gate_readiness(gate_id) → checklist_completion_pct

      data_storage:
        ontology: "Fabric IQ Ontology (Gate, RSO_Process entity types)"
        instances: "Lakehouse: /ontology_instances/gates.delta, rso_processes.delta"
        events: "Lakehouse: /measurements/gate_events.delta (audit trail)"

      workflow:
        technology: "Azure Durable Functions"
        pattern: |
          1. Gate submitted
          2. Check checklist completion (must be 100%)
          3. Check for blocking issues
          4. Pause: Wait for human approval
          5. Record approval/rejection in ontology
          6. Emit gate event to event stream

      api:
        - "POST /api/gates/{gate_id}/submit → Submit for approval"
        - "POST /api/gates/{gate_id}/approve → Approve gate"
        - "GET /api/gates/pending → Pending approvals"

      team_ownership: "PMO Systems Team (3 engineers)"
      # Budget determined by tech-stack-mapper based on technology choices and NFRs
      # budget: "$120K build, $40K/year sustaining"

    - component_id: SC-003
      name: "Field_Operations"
      archetype_component: "Work_Tracker + State_Synchronizer"
      entity_types_owned: [V3_Checklist_Item, Work_Event]

      operations:
        - get_checklist_for_store(store_id) → V3_Checklist_Item[]
        - log_work_start(work_id, tech_id, timestamp)
        - log_work_completion(work_id, tech_id, timestamp)
        - assign_work_to_vendor(work_id, vendor_id)
        - calculate_checklist_completion(store_id) → percentage

      data_storage:
        ontology: "Fabric IQ Ontology (V3_Checklist_Item entity type)"
        instances: "Lakehouse: /ontology_instances/work_units.delta"
        events: "Lakehouse: /measurements/work_events.delta (real-time telemetry)"

      real_time_sync:
        pattern: |
          Mobile app → Event Hub → Stream processing → Update work_units.delta
          (Field tech logs work → Digital twin state updated in real-time)

      mobile_app:
        technology: "React Native + WatermelonDB (offline-first)"
        offline_pattern: |
          Work logged offline → Stored in SQLite
          When online → Sync to Event Hub

      api:
        - "GET /api/work/checklist/{store_id} → V3 Checklist"
        - "POST /api/work/{work_id}/start → Log work start"
        - "POST /api/work/{work_id}/complete → Log work completion"

      team_ownership: "Field Systems Team (4 engineers)"
      # Budget determined by tech-stack-mapper based on technology choices and NFRs
      # budget: "$100K build, $40K/year sustaining"

    - component_id: SC-004
      name: "Progress_Analyst"
      archetype_component: "Performance_Analyzer"
      entity_types_owned: [None - reads from all entities]

      operations:
        - calculate_portfolio_progress(region) → summary stats
        - identify_at_risk_stores(threshold_days) → Store[]
        - analyze_vendor_performance(vendor_id) → metrics
        - predict_gate_completion_date(store_id, gate_type) → predicted_date (ML)

      data_storage:
        reads_from: "Fabric IQ Ontology (all entity types)"
        query_engine: "Synapse Serverless SQL over lakehouse"
        cache: "Redis (dashboard query results, 15-sec TTL)"

      analytics:
        queries: |
          -- Portfolio progress
          SELECT region,
                 COUNT(*) AS total_stores,
                 SUM(CASE WHEN status = 'open' THEN 1 ELSE 0 END) AS opened,
                 SUM(CASE WHEN at_risk = true THEN 1 ELSE 0 END) AS at_risk
          FROM ontology_instances.stores
          GROUP BY region

          -- At-risk stores (gate due in <5 days, completion <80%)
          SELECT s.store_id, g.gate_type, g.target_date,
                 DATEDIFF(day, GETDATE(), g.target_date) AS days_until_gate,
                 c.completion_pct
          FROM ontology_instances.stores s
          JOIN ontology_instances.gates g ON s.store_id = g.store_id
          JOIN (SELECT store_id, AVG(completion) AS completion_pct
                FROM ontology_instances.work_units
                GROUP BY store_id) c ON s.store_id = c.store_id
          WHERE DATEDIFF(day, GETDATE(), g.target_date) < 5
            AND c.completion_pct < 0.80

      ml_model:
        model: "Gate completion date prediction"
        features: [current_completion_pct, days_to_gate, vendor_on_time_rate, issue_count]
        training_data: "Lakehouse: /measurements/work_events.delta (historical data)"

      api:
        - "GET /api/progress/portfolio?region={region} → Portfolio summary"
        - "GET /api/progress/at-risk → At-risk stores"
        - "GET /api/progress/vendor/{vendor_id} → Vendor performance"

      team_ownership: "Analytics & BI Team (4 engineers)"
      # Budget determined by tech-stack-mapper based on technology choices and NFRs
      # budget: "$150K build, $50K/year sustaining"

    - component_id: SC-005
      name: "Issue_Resolver"
      archetype_component: "Issue_Manager"
      entity_types_owned: [Issue]

      operations:
        - create_issue(description, severity, store_id)
        - triage_issue(issue_id) → assign team (AI-assisted)
        - assign_issue(issue_id, team, severity)
        - resolve_issue(issue_id, resolution, root_cause)
        - escalate_issue(issue_id, escalation_level)

      data_storage:
        ontology: "Fabric IQ Ontology (Issue entity type)"
        instances: "Lakehouse: /ontology_instances/issues.delta"

      ai_agent:
        agent: "Issue Triage Agent"
        purpose: "Auto-categorize severity, suggest team assignment"
        technology: "Azure OpenAI + Fabric IQ Ontology (NL2Ontology)"
        pattern: |
          1. New issue created
          2. LLM reads issue description
          3. LLM queries ontology for similar historical issues (RAG)
          4. LLM suggests: severity, team, root cause category
          5. Human approves or overrides

      team_ownership: "Operations Support Team (3 engineers)"
      # Budget determined by tech-stack-mapper based on technology choices and NFRs
      # budget: "$110K build, $40K/year sustaining"

    - component_id: SC-006
      name: "Executive_Command_Center"
      archetype_component: "Digital_Twin_Viewer"
      entity_types_owned: [None - visualization only]

      operations:
        - render_portfolio_dashboard() → Power BI dashboard
        - show_store_detail(store_id) → drill-down view
        - export_executive_summary() → PDF report

      visualization:
        technology: "Power BI Embedded (queries Fabric IQ Ontology directly)"
        dashboards:
          - "Portfolio Health (RAG status by region)"
          - "Gate Performance (% on-time by gate type)"
          - "At-Risk Stores (stores likely to miss deadlines)"
          - "Vendor Scorecard (vendor performance rankings)"

      natural_language:
        technology: "Fabric IQ NL2Ontology"
        examples:
          - "Which stores in the Midwest are at risk?" → SQL query generated
          - "Show me vendors with <90% on-time rate" → SQL query generated

      team_ownership: "Executive Systems Team (2 engineers)"
      # Budget determined by tech-stack-mapper based on technology choices and NFRs
      # budget: "$80K build, $30K/year sustaining"

  # Technology Stack Selection
  # NOTE: Specific technology choices and costs are determined by tech-stack-mapper
  # based on archetype requirements, NFRs, budget constraints, and team skills.
  # See tech-stack-mapper.md for technology selection process.

  # Conway's Law Alignment
  team_structure:
    - "Master Data Team → Store_Master"
    - "PMO Systems Team → Gate_Manager"
    - "Field Systems Team → Field_Operations (mobile app)"
    - "Analytics & BI Team → Progress_Analyst"
    - "Operations Support Team → Issue_Resolver"
    - "Executive Systems Team → Executive_Command_Center"

  # KPI Support
  kpi_mapping:
    - kpi: "Construction Gates 1 & 2: 95% on-time"
      supported_by: "Gate_Manager (tracks gate approvals), Progress_Analyst (measures on-time %)"

    - kpi: "Tech Gates 1 & 2: 95% on-time"
      supported_by: "Gate_Manager, Progress_Analyst"

    - kpi: "Final Validation: 99% all tech working"
      supported_by: "Field_Operations (V3 checklist completion)"

    - kpi: "STP Certification: 100% of vendor FSRs"
      supported_by: "Vendor component in ontology (certification tracking)"
```

## Validation Checklist

✅ **Archetype identified**: Matched requirements to archetype catalog
✅ **Ontology defined**: Domain entities mapped to canonical ontology
✅ **Archetype components instantiated**: Solution components mapped from canonical components
✅ **Required capabilities identified**: Each archetype layer has capability requirements defined
✅ **Conway's Law alignment**: Component boundaries align with team boundaries
✅ **KPIs supported**: Business KPIs traceable to ontology entities and components

## Usage Instructions

1. **Extract validated requirements** (from requirements gathering process)
2. **Identify Solution Archetype** (analyze keywords, match requirements to archetype catalog)
3. **Map domain to canonical ontology** (PhysicalAsset → Store, WorkUnit → V3_Checklist_Item)
4. **Instantiate canonical components** (Asset_Registry → Store_Master, customize operations)
5. **Identify required capabilities** (from archetype's required_capabilities sections)
6. **Define team structure** (Conway's Law: component boundaries = team boundaries)
7. **Output solution architecture spec** (archetype + ontology + components + capability requirements)
8. **Hand off to tech-stack-mapper** (technology selection based on capabilities, NFRs, budget, skills)
