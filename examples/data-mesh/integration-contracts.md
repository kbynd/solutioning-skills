# Data Mesh - Integration Contracts (Concrete Examples)

**Purpose**: Show actual contract formats for communication between compositional archetype levels.

**Problem Solved**: Framework says "levels communicate via explicit contracts" but doesn't show what those contracts look like in practice.

**This document provides**: Concrete examples in real formats (API schemas, event schemas, database DDL, JSON schemas).

---

## Level 1 → Level 2: Digital Twin → Marketplace

**What's being shared**: Domain boundaries and ownership information from Digital Twin (L1) flows to Marketplace (L2) to organize data products by domain.

### Contract 1: Domain Registry API

**Format**: REST API (OpenAPI 3.0)

**Purpose**: Catalog_Manager (L2) queries Domain_Registry (L1) to know which domains exist and what they own.

```yaml
openapi: 3.0.0
info:
  title: Domain Registry API
  version: 1.0.0
  description: "Level 1 (Digital Twin) exposes domain boundaries to Level 2 (Marketplace)"

paths:
  /api/v1/domains:
    get:
      summary: "List all business domains"
      responses:
        '200':
          description: "List of domains"
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/BusinessDomain'

  /api/v1/domains/{domain_id}:
    get:
      summary: "Get domain details"
      parameters:
        - name: domain_id
          in: path
          required: true
          schema:
            type: string
            format: uuid
      responses:
        '200':
          description: "Domain details"
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/BusinessDomain'

  /api/v1/domains/{domain_id}/team:
    get:
      summary: "Get team that owns this domain"
      parameters:
        - name: domain_id
          in: path
          required: true
          schema:
            type: string
            format: uuid
      responses:
        '200':
          description: "Owning team"
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/DomainTeam'

components:
  schemas:
    BusinessDomain:
      type: object
      required:
        - domain_id
        - domain_name
        - domain_type
        - status
        - owner_team_id
      properties:
        domain_id:
          type: string
          format: uuid
          example: "550e8400-e29b-41d4-a716-446655440000"
        domain_name:
          type: string
          example: "Sales"
        domain_type:
          type: string
          enum: [operational, analytical, supporting]
          example: "operational"
        bounded_context:
          type: string
          description: "DDD bounded context description"
          example: "Customer acquisition, order management, sales forecasting"
        status:
          type: string
          enum: [active, restructuring, merged, dissolved]
          example: "active"
        owner_team_id:
          type: string
          format: uuid
          example: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
        created_at:
          type: string
          format: date-time
        updated_at:
          type: string
          format: date-time

    DomainTeam:
      type: object
      required:
        - team_id
        - team_name
        - team_size
      properties:
        team_id:
          type: string
          format: uuid
        team_name:
          type: string
          example: "Sales Analytics Team"
        team_size:
          type: integer
          example: 8
        skills:
          type: array
          items:
            type: string
          example: ["Python", "SQL", "Spark", "Domain expertise"]
        contact_email:
          type: string
          format: email
          example: "sales-analytics@company.com"
```

**Usage Example** (Catalog_Manager calling Domain_Registry):

```bash
# Catalog_Manager (L2) fetches all domains to organize catalog
curl -X GET https://domain-registry.company.com/api/v1/domains \
  -H "Authorization: Bearer ${API_TOKEN}"

# Response:
[
  {
    "domain_id": "550e8400-e29b-41d4-a716-446655440000",
    "domain_name": "Sales",
    "domain_type": "operational",
    "bounded_context": "Customer acquisition, order management",
    "status": "active",
    "owner_team_id": "7c9e6679-7425-40de-944b-e07fc1f90ae7"
  },
  {
    "domain_id": "660f9511-f39c-52e5-b827-557766551111",
    "domain_name": "Marketing",
    "domain_type": "analytical",
    "status": "active",
    "owner_team_id": "8d0f7780-8536-51ef-c948-f18gd2g01bf8"
  }
]

# Catalog_Manager then creates sections in catalog per domain
```

### Contract 2: Domain Ownership Events

**Format**: Event Schema (Avro)

**Purpose**: When domain ownership changes (L1), Provider_Portal (L2) needs to update who can publish products.

```json
{
  "type": "record",
  "name": "DomainOwnershipChanged",
  "namespace": "com.company.datamesh.events.domain",
  "doc": "Published by Domain_Ownership_Manager (L1) when domain ownership changes",
  "fields": [
    {
      "name": "event_id",
      "type": "string",
      "doc": "Unique event identifier"
    },
    {
      "name": "event_timestamp",
      "type": "long",
      "logicalType": "timestamp-millis",
      "doc": "When ownership change occurred"
    },
    {
      "name": "domain_id",
      "type": "string",
      "doc": "Domain that changed ownership"
    },
    {
      "name": "domain_name",
      "type": "string",
      "doc": "Human-readable domain name"
    },
    {
      "name": "previous_owner_team_id",
      "type": ["null", "string"],
      "default": null,
      "doc": "Previous owning team (null if new domain)"
    },
    {
      "name": "new_owner_team_id",
      "type": "string",
      "doc": "New owning team"
    },
    {
      "name": "change_reason",
      "type": {
        "type": "enum",
        "name": "OwnershipChangeReason",
        "symbols": ["ORG_RESTRUCTURE", "TEAM_MERGE", "TEAM_SPLIT", "INITIAL_ASSIGNMENT"]
      },
      "doc": "Why ownership changed"
    },
    {
      "name": "effective_date",
      "type": "long",
      "logicalType": "timestamp-millis",
      "doc": "When new ownership takes effect"
    }
  ]
}
```

**Kafka Topic Configuration**:
```yaml
topic: domain-ownership-events
partitions: 10
replication_factor: 3
retention_ms: 31536000000  # 1 year
```

**Usage Example** (Provider_Portal consuming events):

```python
# Provider_Portal (L2) subscribes to domain ownership events
from confluent_kafka import Consumer
import avro.io

consumer = Consumer({
    'bootstrap.servers': 'kafka.company.com:9092',
    'group.id': 'provider-portal-ownership-sync',
    'auto.offset.reset': 'earliest'
})

consumer.subscribe(['domain-ownership-events'])

for msg in consumer:
    event = avro_deserialize(msg.value)

    # Update Provider_Portal access control
    update_publishing_permissions(
        domain_id=event['domain_id'],
        allowed_team_id=event['new_owner_team_id']
    )

    # Revoke old team's access
    if event['previous_owner_team_id']:
        revoke_publishing_permissions(
            domain_id=event['domain_id'],
            revoked_team_id=event['previous_owner_team_id']
        )
```

### Contract 3: Domain Metrics Database View

**Format**: SQL View (PostgreSQL)

**Purpose**: Quality_Rating_System (L2) joins domain metrics from L1 to show domain health alongside product quality.

```sql
-- Domain_Registry (L1) exposes this view for Level 2 components
CREATE OR REPLACE VIEW domain_health_metrics AS
SELECT
    d.domain_id,
    d.domain_name,
    d.domain_type,
    d.status,
    t.team_name,
    t.team_size,

    -- Aggregate metrics from DomainMetrics table
    COUNT(DISTINCT dp.product_id) as products_count,
    AVG(qr.rating_value) as avg_quality_score,
    SUM(um.metric_value) FILTER (WHERE um.metric_type = 'queries_per_day') as total_usage,
    SUM(cm.cost_total) as total_cost_monthly

FROM business_domains d
LEFT JOIN domain_teams t ON d.owner_team_id = t.team_id
LEFT JOIN data_products dp ON dp.offered_by = d.domain_id
LEFT JOIN quality_ratings qr ON qr.for_product_id = dp.product_id
    AND qr.measurement_date > NOW() - INTERVAL '7 days'
LEFT JOIN usage_metrics um ON um.about_product_id = dp.product_id
    AND um.timestamp > NOW() - INTERVAL '30 days'
LEFT JOIN cost_metrics cm ON cm.domain_id = d.domain_id
    AND cm.month = DATE_TRUNC('month', NOW())

WHERE d.status = 'active'
GROUP BY d.domain_id, d.domain_name, d.domain_type, d.status, t.team_name, t.team_size;

-- Grant read access to Level 2 components
GRANT SELECT ON domain_health_metrics TO catalog_manager_role;
GRANT SELECT ON domain_health_metrics TO quality_rating_system_role;
GRANT SELECT ON domain_health_metrics TO domain_analytics_role;
```

**Usage Example** (Quality_Rating_System querying view):

```sql
-- Quality_Rating_System (L2) shows domain health in dashboards
SELECT
    domain_name,
    team_name,
    products_count,
    avg_quality_score,
    CASE
        WHEN avg_quality_score >= 90 THEN 'GREEN'
        WHEN avg_quality_score >= 70 THEN 'YELLOW'
        ELSE 'RED'
    END as health_status,
    total_usage as queries_per_month,
    total_cost_monthly
FROM domain_health_metrics
ORDER BY avg_quality_score DESC;
```

---

## Level 2 → Level 3: Marketplace → Integration + Supply Chain

**What's being shared**: Data product metadata, schemas, and lineage from Marketplace (L2) flows to Integration Platform and Supply Chain Network (L3).

### Contract 4: Data Product Registration API

**Format**: REST API (OpenAPI 3.0)

**Purpose**: When domain publishes product via Provider_Portal (L2), it registers with Federated_Catalog_Aggregator (L3).

```yaml
openapi: 3.0.0
info:
  title: Federated Catalog API
  version: 1.0.0
  description: "Level 3 (Integration Platform) accepts product registrations from Level 2 (Marketplace)"

paths:
  /api/v1/catalog/products:
    post:
      summary: "Register new data product"
      security:
        - BearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/DataProductRegistration'
      responses:
        '201':
          description: "Product registered successfully"
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/RegistrationConfirmation'
        '400':
          description: "Validation failed"
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ValidationError'
        '403':
          description: "Not authorized to publish for this domain"

components:
  schemas:
    DataProductRegistration:
      type: object
      required:
        - product_name
        - domain_id
        - schema
        - output_ports
        - slo_targets
      properties:
        product_name:
          type: string
          example: "customer_360"
        product_description:
          type: string
          example: "Aggregated customer profile with purchase history and preferences"
        domain_id:
          type: string
          format: uuid
          example: "550e8400-e29b-41d4-a716-446655440000"
        product_owner:
          type: string
          format: email
          example: "jane.doe@company.com"
        schema:
          $ref: '#/components/schemas/DataContractSchema'
        output_ports:
          type: array
          items:
            $ref: '#/components/schemas/OutputPort'
        slo_targets:
          $ref: '#/components/schemas/SLOTargets'
        input_dependencies:
          type: array
          items:
            $ref: '#/components/schemas/InputDependency'
          description: "Prosumer: What this product consumes (for lineage tracking)"
        tags:
          type: array
          items:
            type: string
          example: ["customer", "analytics", "ml-ready"]

    DataContractSchema:
      type: object
      required:
        - schema_format
        - schema_definition
      properties:
        schema_format:
          type: string
          enum: [avro, protobuf, json-schema, sql-ddl]
          example: "avro"
        schema_definition:
          type: string
          description: "Schema in specified format (JSON string)"
          example: |
            {
              "type": "record",
              "name": "Customer360",
              "fields": [
                {"name": "customer_id", "type": "string"},
                {"name": "email_hash", "type": "string"},
                {"name": "lifetime_value", "type": "double"},
                {"name": "last_purchase_date", "type": "long", "logicalType": "timestamp-millis"}
              ]
            }
        backward_compatible:
          type: boolean
          description: "Is this version backward compatible with previous?"

    OutputPort:
      type: object
      required:
        - port_type
        - endpoint
      properties:
        port_type:
          type: string
          enum: [sql, rest-api, graphql, s3, kafka]
          example: "sql"
        endpoint:
          type: string
          example: "postgresql://data-mesh.company.com:5432/sales/customer_360"
        authentication:
          type: string
          enum: [oauth, api-key, iam-role, certificate]
          example: "iam-role"
        rate_limit:
          type: object
          properties:
            queries_per_second:
              type: integer
              example: 100
            gb_per_day:
              type: integer
              example: 1000

    SLOTargets:
      type: object
      required:
        - freshness
        - completeness
      properties:
        freshness:
          type: string
          description: "Max data age (ISO 8601 duration)"
          example: "PT1H"  # <1 hour
        completeness:
          type: number
          format: float
          minimum: 0
          maximum: 100
          description: "Percentage of required fields populated"
          example: 95.0
        accuracy:
          type: string
          description: "Validation rules that must pass"
          example: "email_hash matches regex ^[a-f0-9]{64}$"

    InputDependency:
      type: object
      description: "Prosumer: Upstream product this consumes (for supply chain tracking)"
      required:
        - source_product_id
        - dependency_type
      properties:
        source_product_id:
          type: string
          format: uuid
          example: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
        source_product_name:
          type: string
          example: "Payments.raw_transactions"
        dependency_type:
          type: string
          enum: [required, optional]
          example: "required"
        usage_pattern:
          type: string
          enum: [aggregation, join, filter, pass-through]
          example: "aggregation"

    RegistrationConfirmation:
      type: object
      properties:
        product_id:
          type: string
          format: uuid
        status:
          type: string
          enum: [registered, pending_validation, rejected]
        catalog_url:
          type: string
          format: uri
          example: "https://catalog.company.com/products/customer_360"
        schema_registry_id:
          type: integer
          description: "ID in schema registry"
          example: 12345

    ValidationError:
      type: object
      properties:
        errors:
          type: array
          items:
            type: object
            properties:
              field:
                type: string
              message:
                type: string
          example:
            - field: "schema.schema_definition"
              message: "PII field 'email' must be hashed or encrypted"
            - field: "slo_targets.freshness"
              message: "Freshness target PT15M too aggressive for batch source"
```

**Usage Example** (Provider_Portal registering product):

```bash
curl -X POST https://federated-catalog.company.com/api/v1/catalog/products \
  -H "Authorization: Bearer ${DOMAIN_TEAM_TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "product_name": "customer_360",
    "product_description": "Aggregated customer profile",
    "domain_id": "550e8400-e29b-41d4-a716-446655440000",
    "product_owner": "jane.doe@company.com",
    "schema": {
      "schema_format": "avro",
      "schema_definition": "{\"type\":\"record\",\"name\":\"Customer360\",...}",
      "backward_compatible": true
    },
    "output_ports": [
      {
        "port_type": "sql",
        "endpoint": "postgresql://data-mesh.company.com:5432/sales/customer_360",
        "authentication": "iam-role"
      }
    ],
    "slo_targets": {
      "freshness": "PT1H",
      "completeness": 95.0
    },
    "input_dependencies": [
      {
        "source_product_id": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
        "source_product_name": "Payments.raw_transactions",
        "dependency_type": "required",
        "usage_pattern": "aggregation"
      }
    ],
    "tags": ["customer", "analytics", "pii-hashed"]
  }'
```

### Contract 5: Schema Registry Protocol

**Format**: Confluent Schema Registry API

**Purpose**: Provider_Portal (L2) registers Avro/Protobuf schemas with Schema_Registry (L3) for version management and compatibility checking.

```bash
# Register schema for data product
curl -X POST https://schema-registry.company.com/subjects/customer_360-value/versions \
  -H "Content-Type: application/vnd.schemaregistry.v1+json" \
  -d '{
    "schema": "{\"type\":\"record\",\"name\":\"Customer360\",\"namespace\":\"com.company.sales\",\"fields\":[{\"name\":\"customer_id\",\"type\":\"string\"},{\"name\":\"email_hash\",\"type\":\"string\"},{\"name\":\"lifetime_value\",\"type\":\"double\"}]}"
  }'

# Response:
{
  "id": 12345
}

# Check compatibility before registering new version
curl -X POST https://schema-registry.company.com/compatibility/subjects/customer_360-value/versions/latest \
  -H "Content-Type: application/vnd.schemaregistry.v1+json" \
  -d '{
    "schema": "{\"type\":\"record\",\"name\":\"Customer360\",\"namespace\":\"com.company.sales\",\"fields\":[{\"name\":\"customer_id\",\"type\":\"string\"},{\"name\":\"email_hash\",\"type\":\"string\"},{\"name\":\"lifetime_value\",\"type\":\"double\"},{\"name\":\"churn_risk_score\",\"type\":[\"null\",\"double\"],\"default\":null}]}"
  }'

# Response:
{
  "is_compatible": true
}
```

### Contract 6: Lineage Extraction Event

**Format**: Event Schema (OpenLineage standard)

**Purpose**: When data product pipeline runs (L2), it emits lineage events to Cross_Domain_Lineage_Tracker (L3).

```json
{
  "eventType": "COMPLETE",
  "eventTime": "2026-03-01T15:30:00.000Z",
  "run": {
    "runId": "550e8400-e29b-41d4-a716-446655440000",
    "facets": {
      "nominalTime": {
        "_producer": "https://github.com/OpenLineage/OpenLineage/tree/0.21.0/client/python",
        "_schemaURL": "https://openlineage.io/spec/facets/1-0-0/NominalTimeRunFacet.json",
        "nominalStartTime": "2026-03-01T15:00:00.000Z",
        "nominalEndTime": "2026-03-01T15:30:00.000Z"
      }
    }
  },
  "job": {
    "namespace": "sales_domain",
    "name": "build_customer_360",
    "facets": {
      "documentation": {
        "_producer": "https://github.com/OpenLineage/OpenLineage/tree/0.21.0/client/python",
        "_schemaURL": "https://openlineage.io/spec/facets/1-0-0/DocumentationJobFacet.json",
        "description": "Aggregate customer data from raw transactions and CRM"
      },
      "sql": {
        "_producer": "https://github.com/OpenLineage/OpenLineage/tree/0.21.0/client/python",
        "_schemaURL": "https://openlineage.io/spec/facets/1-0-0/SQLJobFacet.json",
        "query": "INSERT INTO sales.customer_360 SELECT customer_id, SHA256(email) as email_hash, SUM(amount) as lifetime_value FROM payments.raw_transactions JOIN crm.customer_profile USING (customer_id) GROUP BY customer_id"
      }
    }
  },
  "inputs": [
    {
      "namespace": "payments_domain",
      "name": "raw_transactions",
      "facets": {
        "dataSource": {
          "_producer": "https://github.com/OpenLineage/OpenLineage/tree/0.21.0/client/python",
          "_schemaURL": "https://openlineage.io/spec/facets/1-0-0/DataSourceDatasetFacet.json",
          "name": "postgresql://data-mesh.company.com:5432/payments",
          "uri": "postgresql://data-mesh.company.com:5432/payments/raw_transactions"
        },
        "schema": {
          "_producer": "https://github.com/OpenLineage/OpenLineage/tree/0.21.0/client/python",
          "_schemaURL": "https://openlineage.io/spec/facets/1-0-0/SchemaDatasetFacet.json",
          "fields": [
            {"name": "transaction_id", "type": "STRING"},
            {"name": "customer_id", "type": "STRING"},
            {"name": "amount", "type": "DOUBLE"},
            {"name": "timestamp", "type": "TIMESTAMP"}
          ]
        }
      }
    },
    {
      "namespace": "crm_domain",
      "name": "customer_profile",
      "facets": {
        "dataSource": {
          "_producer": "https://github.com/OpenLineage/OpenLineage/tree/0.21.0/client/python",
          "_schemaURL": "https://openlineage.io/spec/facets/1-0-0/DataSourceDatasetFacet.json",
          "name": "postgresql://data-mesh.company.com:5432/crm",
          "uri": "postgresql://data-mesh.company.com:5432/crm/customer_profile"
        }
      }
    }
  ],
  "outputs": [
    {
      "namespace": "sales_domain",
      "name": "customer_360",
      "facets": {
        "dataSource": {
          "_producer": "https://github.com/OpenLineage/OpenLineage/tree/0.21.0/client/python",
          "_schemaURL": "https://openlineage.io/spec/facets/1-0-0/DataSourceDatasetFacet.json",
          "name": "postgresql://data-mesh.company.com:5432/sales",
          "uri": "postgresql://data-mesh.company.com:5432/sales/customer_360"
        },
        "schema": {
          "_producer": "https://github.com/OpenLineage/OpenLineage/tree/0.21.0/client/python",
          "_schemaURL": "https://openlineage.io/spec/facets/1-0-0/SchemaDatasetFacet.json",
          "fields": [
            {"name": "customer_id", "type": "STRING"},
            {"name": "email_hash", "type": "STRING"},
            {"name": "lifetime_value", "type": "DOUBLE"}
          ]
        }
      }
    }
  ],
  "producer": "https://github.com/OpenLineage/OpenLineage/tree/0.21.0/client/python"
}
```

**Kafka Topic for Lineage Events**:
```yaml
topic: openlineage-events
partitions: 20
replication_factor: 3
retention_ms: 15552000000  # 180 days
```

**Usage Example** (Cross_Domain_Lineage_Tracker consuming events):

```python
# Cross_Domain_Lineage_Tracker (L3) builds lineage graph
from openlineage.client import OpenLineageClient
import neo4j

lineage_consumer = OpenLineageClient.subscribe('openlineage-events')
neo4j_driver = neo4j.GraphDatabase.driver("bolt://neo4j:7687")

for event in lineage_consumer:
    if event['eventType'] == 'COMPLETE':
        with neo4j_driver.session() as session:
            # Create nodes for inputs, outputs, job
            for input_ds in event['inputs']:
                session.run(
                    "MERGE (d:Dataset {namespace: $ns, name: $name})",
                    ns=input_ds['namespace'],
                    name=input_ds['name']
                )

            for output_ds in event['outputs']:
                session.run(
                    "MERGE (d:Dataset {namespace: $ns, name: $name})",
                    ns=output_ds['namespace'],
                    name=output_ds['name']
                )

            # Create lineage edges
            for input_ds in event['inputs']:
                for output_ds in event['outputs']:
                    session.run("""
                        MATCH (input:Dataset {namespace: $input_ns, name: $input_name})
                        MATCH (output:Dataset {namespace: $output_ns, name: $output_name})
                        MERGE (input)-[r:FLOWS_TO {job: $job_name, transformation: $sql}]->(output)
                        """,
                        input_ns=input_ds['namespace'],
                        input_name=input_ds['name'],
                        output_ns=output_ds['namespace'],
                        output_name=output_ds['name'],
                        job_name=event['job']['name'],
                        sql=event['job']['facets']['sql']['query']
                    )
```

---

## Level 1 → Level 3: Digital Twin → Platform

**What's being shared**: Domain list and cost metrics from Digital Twin (L1) flows to Self_Serve_Data_Platform (L3) for resource provisioning and cost allocation.

### Contract 7: Platform Provisioning Webhook

**Format**: Webhook (HTTP POST)

**Purpose**: When new domain is created in Domain_Registry (L1), it triggers Self_Serve_Data_Platform (L3) to provision infrastructure.

```yaml
# Webhook configuration in Domain_Registry (L1)
webhooks:
  - name: "provision-domain-infrastructure"
    url: "https://platform.company.com/api/v1/provision"
    events:
      - domain.created
      - domain.status_changed
    auth:
      type: "bearer"
      token_env: "PLATFORM_WEBHOOK_TOKEN"
    retry:
      max_attempts: 3
      backoff: exponential
```

**Webhook Payload Schema** (JSON Schema):

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "Domain Created Event",
  "type": "object",
  "required": ["event_type", "event_id", "timestamp", "domain"],
  "properties": {
    "event_type": {
      "type": "string",
      "enum": ["domain.created", "domain.status_changed"],
      "example": "domain.created"
    },
    "event_id": {
      "type": "string",
      "format": "uuid"
    },
    "timestamp": {
      "type": "string",
      "format": "date-time"
    },
    "domain": {
      "type": "object",
      "required": ["domain_id", "domain_name", "owner_team_id"],
      "properties": {
        "domain_id": {
          "type": "string",
          "format": "uuid"
        },
        "domain_name": {
          "type": "string",
          "pattern": "^[a-z0-9-]+$",
          "example": "sales"
        },
        "domain_type": {
          "type": "string",
          "enum": ["operational", "analytical", "supporting"]
        },
        "owner_team_id": {
          "type": "string",
          "format": "uuid"
        },
        "resource_requirements": {
          "type": "object",
          "properties": {
            "storage_gb": {
              "type": "integer",
              "minimum": 10,
              "default": 100
            },
            "compute_tier": {
              "type": "string",
              "enum": ["small", "medium", "large"],
              "default": "medium"
            }
          }
        }
      }
    }
  }
}
```

**Usage Example** (Self_Serve_Data_Platform receiving webhook):

```python
# Self_Serve_Data_Platform (L3) webhook endpoint
from flask import Flask, request, jsonify
import terraform_wrapper

app = Flask(__name__)

@app.route('/api/v1/provision', methods=['POST'])
def provision_domain():
    event = request.json

    if event['event_type'] == 'domain.created':
        domain_id = event['domain']['domain_id']
        domain_name = event['domain']['domain_name']
        team_id = event['domain']['owner_team_id']

        # Provision infrastructure using Terraform
        resources = terraform_wrapper.apply(f"""
        module "domain_{domain_name}" {{
          source = "./modules/domain-infrastructure"

          domain_id   = "{domain_id}"
          domain_name = "{domain_name}"
          owner_team  = "{team_id}"

          s3_bucket_name = "data-mesh-{domain_name}"
          database_name  = "{domain_name}_db"

          # IAM roles
          publisher_role_name = "{domain_name}-publisher"
          consumer_role_name  = "{domain_name}-consumer"
        }}
        """)

        return jsonify({
            "status": "provisioned",
            "resources": {
                "s3_bucket": resources['s3_bucket_arn'],
                "database": resources['database_endpoint'],
                "publisher_role": resources['publisher_role_arn']
            }
        }), 201

    return jsonify({"status": "ignored"}), 200
```

### Contract 8: Cost Allocation Database

**Format**: SQL Database Schema (PostgreSQL)

**Purpose**: DomainMetrics (L1) writes cost data that Self_Serve_Data_Platform (L3) uses for chargeback.

```sql
-- Shared schema between L1 and L3
CREATE SCHEMA IF NOT EXISTS cost_allocation;

-- Domain_Registry (L1) maintains this table
CREATE TABLE cost_allocation.domain_costs (
    domain_id UUID NOT NULL,
    domain_name VARCHAR(255) NOT NULL,
    month DATE NOT NULL,  -- First day of month

    -- Cost breakdown
    storage_cost_usd DECIMAL(10, 2) DEFAULT 0,
    compute_cost_usd DECIMAL(10, 2) DEFAULT 0,
    network_cost_usd DECIMAL(10, 2) DEFAULT 0,
    total_cost_usd DECIMAL(10, 2) GENERATED ALWAYS AS
        (storage_cost_usd + compute_cost_usd + network_cost_usd) STORED,

    -- Usage metrics that drove costs
    storage_gb BIGINT,
    compute_hours DECIMAL(10, 2),
    network_egress_gb BIGINT,
    queries_count BIGINT,

    -- Attribution
    owner_team_id UUID NOT NULL,
    cost_center VARCHAR(100),

    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),

    PRIMARY KEY (domain_id, month),
    FOREIGN KEY (domain_id) REFERENCES public.business_domains(domain_id),
    FOREIGN KEY (owner_team_id) REFERENCES public.domain_teams(team_id)
);

CREATE INDEX idx_domain_costs_month ON cost_allocation.domain_costs(month);
CREATE INDEX idx_domain_costs_team ON cost_allocation.domain_costs(owner_team_id);

-- Self_Serve_Data_Platform (L3) writes platform costs here
CREATE TABLE cost_allocation.platform_costs (
    month DATE NOT NULL PRIMARY KEY,

    -- Shared platform costs (allocated across domains)
    catalog_cost_usd DECIMAL(10, 2),
    governance_cost_usd DECIMAL(10, 2),
    lineage_tracking_cost_usd DECIMAL(10, 2),
    schema_registry_cost_usd DECIMAL(10, 2),
    total_platform_cost_usd DECIMAL(10, 2),

    -- Active domains this month (for allocation)
    active_domains_count INTEGER,
    cost_per_domain_usd DECIMAL(10, 2) GENERATED ALWAYS AS
        (CASE WHEN active_domains_count > 0
              THEN total_platform_cost_usd / active_domains_count
              ELSE 0 END) STORED,

    created_at TIMESTAMP DEFAULT NOW()
);

-- Materialized view for total cost per domain (L1 costs + allocated platform costs)
CREATE MATERIALIZED VIEW cost_allocation.domain_total_costs AS
SELECT
    dc.domain_id,
    dc.domain_name,
    dc.month,
    dc.total_cost_usd as domain_direct_cost_usd,
    pc.cost_per_domain_usd as platform_allocated_cost_usd,
    (dc.total_cost_usd + pc.cost_per_domain_usd) as total_cost_usd,
    dc.owner_team_id,
    dc.cost_center
FROM cost_allocation.domain_costs dc
JOIN cost_allocation.platform_costs pc ON dc.month = pc.month;

-- Refresh monthly
CREATE UNIQUE INDEX ON cost_allocation.domain_total_costs(domain_id, month);

-- Grant permissions
GRANT SELECT ON cost_allocation.domain_costs TO domain_analytics_role;
GRANT SELECT ON cost_allocation.platform_costs TO domain_analytics_role;
GRANT SELECT ON cost_allocation.domain_total_costs TO domain_analytics_role;

GRANT INSERT, UPDATE ON cost_allocation.domain_costs TO domain_metrics_writer_role;
GRANT INSERT, UPDATE ON cost_allocation.platform_costs TO platform_cost_writer_role;
```

**Usage Example** (Domain_Analytics querying costs):

```sql
-- Domain_Analytics (L1) shows cost trends
SELECT
    domain_name,
    month,
    domain_direct_cost_usd,
    platform_allocated_cost_usd,
    total_cost_usd,
    LAG(total_cost_usd) OVER (PARTITION BY domain_id ORDER BY month) as prev_month_cost,
    ROUND(
        ((total_cost_usd - LAG(total_cost_usd) OVER (PARTITION BY domain_id ORDER BY month))
         / LAG(total_cost_usd) OVER (PARTITION BY domain_id ORDER BY month)) * 100,
        2
    ) as cost_change_percent
FROM cost_allocation.domain_total_costs
WHERE month >= DATE_TRUNC('month', NOW() - INTERVAL '6 months')
ORDER BY domain_name, month;
```

---

## Summary: Contract Formats by Integration Point

| Integration Point | Contract Type | Format | Purpose |
|-------------------|--------------|---------|---------|
| **L1 → L2** | | | |
| Domain boundaries | REST API | OpenAPI 3.0 | Catalog_Manager queries domains |
| Domain ownership | Event stream | Avro schema | Provider_Portal updates access control |
| Domain metrics | Database view | SQL DDL | Quality_Rating shows domain health |
| **L2 → L3** | | | |
| Product registration | REST API | OpenAPI 3.0 | Federated_Catalog accepts products |
| Schema versioning | Schema Registry API | Confluent SR protocol | Schema_Registry manages versions |
| Lineage extraction | Event stream | OpenLineage JSON | Lineage_Tracker builds graph |
| **L1 → L3** | | | |
| Infrastructure provisioning | Webhook | JSON Schema | Platform provisions resources |
| Cost allocation | Database schema | SQL DDL | Platform tracks costs per domain |

---

## Key Insights

### 1. Multiple Contract Types Needed

**Not just one format**:
- APIs for synchronous queries (OpenAPI)
- Events for async notifications (Avro, OpenLineage)
- Database schemas for shared state (SQL DDL)
- Webhooks for automation triggers (JSON Schema)

**Choose based on integration pattern**:
- Request/response → REST API
- Publish/subscribe → Event stream
- Shared data → Database view
- Automation → Webhook

### 2. Standard Formats Where Possible

**Use industry standards**:
- OpenAPI 3.0 for REST APIs (not custom docs)
- Avro/Protobuf for events (not plain JSON)
- OpenLineage for lineage (not custom format)
- Confluent Schema Registry protocol (not proprietary)

**Why**: Tooling ecosystem (code generation, validation, visualization)

### 3. Contracts Are Versioned

**All contracts must support evolution**:
- API versions in URL (`/api/v1/...`)
- Schema versions in registry (backward compatibility checks)
- Event schema evolution (Avro default values)
- Database migrations (ALTER not DROP/CREATE)

**Breaking changes require migration period**

### 4. Contracts Are Enforced

**Not just documentation**:
- OpenAPI → Generate client SDKs, server validation
- Avro schema → Kafka serializers reject invalid data
- SQL DDL → Foreign keys enforce referential integrity
- Webhooks → Signature verification prevents spoofing

**Contracts that aren't enforced → ignored**

---

**Next Steps**:
1. Add these examples to compositional-architecture.yaml (reference this file)
2. Create contract validation tests (fitness functions)
3. Generate client SDKs from OpenAPI specs
4. Set up schema registry in tech stack

**End of Integration Contracts**
