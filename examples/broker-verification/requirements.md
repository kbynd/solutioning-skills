# Counterparty Risk - Broker Verification Automation

## Project Context

**Source**: POC Request (Feb 2026)
**Team**:  Production Back Office - Counterparty Risk
**Channels**: TPO (Third-Party Origination) and PCG (Private Client Group)

## Business Problem

The Counterparty Risk team verifies brokers entering the Financier network. The current process is highly manual, time-consuming, and error-prone.

**Pain Points:**
- High volume of broker applications (new + renewals)
- 10-step manual verification process
- Time-consuming: 1 day per broker (vs. desired <2 hours)
- Repetitive tasks: Excel Control-F searches, manual document reviews
- Risk of human error in compliance-critical verification

## Current Manual Process (As-Is)

1. **Salesforce Record Creation**: New broker application submitted → Salesforce record created
2. **Record Retrieval**: Offshore back-office team retrieves Salesforce record
3. **Broker Name Download**: Download all broker names from workstream
4. **High-Risk List Check**: Manually check each broker against high-risk party list
   - List stored in Excel spreadsheet (multiple tabs, thousands of entries)
   - Use Control-F to search each tab for name variations
5. **NMLS ID Verification**: Verify broker via NMLS website
6. **LexisNexis Background Check**: For high-risk brokers, run LexisNexis check
7. **Report Download**: Download multi-page LexisNexis reports (PDF)
8. **Manual Document Review**: Review documents line-by-line
9. **Flag Findings**: Identify and highlight negative findings (litigation, adverse info)
10. **Escalation**: Escalate flagged findings to HQ business team for review and action

## Proposed Solution (To-Be)

AI-driven agentic automation to streamline Level-1 back-office workflow.

**Automation Scope:**
- ✅ Integrate with Salesforce (automated data ingestion)
- ✅ Automate name checks against high-risk party list
- ✅ Perform NMLS verifications (API or automated web lookup)
- ✅ Conduct LexisNexis report retrieval
- ✅ Automated document analysis (AI-powered PDF parsing)
- ✅ Identify and flag risk indicators without manual intervention
- ⚠️ Human approval required for final decision (HQ team review)

## Business Goals

1. **Reduce manual effort**: 10 steps → 2 steps (upload application, review AI summary)
2. **Eliminate repetitive tasks**: No more Excel Control-F, manual PDF reviews
3. **Improve accuracy**: AI catches patterns humans might miss
4. **Accelerate cycles**: Broker onboarding/renewal from 1 day → <2 hours

## Non-Functional Requirements

### Performance
- Workflow execution time: <2 hours per broker (vs. 1 day manual)
- API response time: <500ms (p95) for status queries
- Document analysis: <30 seconds per LexisNexis report

### Accuracy
- Risk detection recall: >95% (catch all actual risks)
- False positive rate: <20% (minimize unnecessary HQ reviews)
- NMLS verification: 100% accuracy (no identity mismatches)

### Volume/Scalability
- Current: ~1,000 broker applications/year (estimated)
- Target: Support 5,000/year (5x growth headroom)
- Concurrent workflows: 100 brokers in-flight simultaneously

### Data Retention
- Verification records: 7 years (financial services compliance)
- LexisNexis PDFs: 7 years (immutable, legal discovery)
- Audit logs: 7 years (who approved what, when)

### Security
- Authentication: Corporate SSO (Azure AD or Okta)
- Authorization: RBAC (back-office, HQ reviewer, admin)
- Data encryption: At rest (S3) and in transit (TLS)
- Secrets management: API keys for LexisNexis, NMLS

### Cost
- POC budget: <$5,000/month (AWS funding, 3 months)
- Production budget: <$15,000/month (ROI: save 1 FTE = $8K/month)

### Platform
- AWS (explicit requirement from funding request)
- Team skills: Python/Node.js developers, AWS-focused
- No specialized ML engineers (use managed AI services)

## Key Integrations

1. **Salesforce**: CRM (broker application source of truth)
2. **NMLS**: National Mortgage Licensing System (identity verification)
3. **LexisNexis**: Background check reports (risk assessment)
4. **High-Risk Party List**: Excel spreadsheet (internal compliance data)

## Success Criteria

- ✅ 80% reduction in manual effort (10 steps → 2 steps)
- ✅ >90% AI accuracy (matches human judgment)
- ✅ <2 hour workflow execution time
- ✅ Zero security/compliance violations
- ✅ Within POC budget (<$5K/month)

## Compliance Context

**Industry**: Financial Services (Mortgage)
**Regulators**: FINRA, CFPB, state licensing boards
**Key Requirements**:
- NMLS verification mandatory (federal requirement)
- Background checks for high-risk indicators (fraud prevention)
- 7-year record retention (legal discovery, audit trail)
- PII protection (broker names, NMLS IDs)
