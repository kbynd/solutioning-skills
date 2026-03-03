# Input Requirements - Startup MVP Example

## Context

**Organization**: Early-stage startup (3 months old)
**Project**: Building data mesh for internal analytics
**Current state**: Manual spreadsheets, ad-hoc SQL queries, no central catalog

## Requirements from solution-mapper

solution-mapper identified the following archetypes needed:

### Archetype 1: Marketplace (Level 2)
**Capability**: Metadata catalog and search
**Purpose**: Enable domains to publish data products, consumers to discover them
**Scale**: 
- 10 domains expected
- 50 data products (first year)
- 20 consumers (data analysts, data scientists)
- 1000 catalog queries/day

### Archetype 2: Supply Chain Network (Level 3)
**Capability**: Lineage tracking and impact analysis
**Purpose**: Track dependencies between data products, predict breaking change impact
**Scale**:
- 100 lineage edges expected
- 20 transformations/day

## Initial Requirements

From stakeholder interviews:

1. **Business Driver**: "We waste 10 hours/week searching for data"
2. **Timeline**: "Need this working in Q1 (3 months from now)"
3. **Success Metric**: "Reduce data discovery time from 2 hours to 5 minutes"
4. **Budget**: "We have $500K runway, need to be profitable in 12 months"
5. **Team**: "5 engineers total, all focused on building product features"

## Questions for tech-stack-mapper

User asks: **"Help me choose technology for the data catalog (Marketplace archetype)"**

tech-stack-mapper will now run Phase 1 (Discovery) to understand constraints...
