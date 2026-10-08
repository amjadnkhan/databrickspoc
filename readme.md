# Databricks AI & Data Engineering — Portfolio

---

## Professional Summary

Data Engineer with hands-on experience building AI-powered data pipelines and analytics solutions on the Databricks Lakehouse Platform. Skilled in integrating SQL AI functions with enterprise data to automate classification, extraction, and reporting workflows in regulated industries. Proficient in Unity Catalog governance, Delta Lake, serverless compute, and Azure cloud infrastructure.

---

## Technical Skills

| Category | Technologies |
|---|---|
| **Platform** | Databricks Lakehouse, Azure Databricks, Unity Catalog, Delta Lake |
| **AI/ML** | SQL AI Functions (ai_query, ai_classify, ai_extract, ai_summarize, ai_analyze_sentiment), Foundation Model APIs, Prompt Engineering, LLM Integration (Claude, Llama) |
| **Data Engineering** | PySpark, Databricks SQL, CTEs, Materialized Views, Streaming Tables, Lakeflow Spark Declarative Pipelines |
| **Governance** | Unity Catalog (catalog/schema/table grants), Role-Based Access Control, Azure Access Connector, Private Endpoints, Network Connectivity Configs |
| **Cloud / Infra** | Azure ADLS Gen2, Azure Entra ID (AAD), SCIM provisioning, Private Endpoints, VNet injection, NCC |
| **DevOps** | Databricks Git Integration, GitHub, CI/CD Workflows, Repos |
| **Visualization** | Databricks AI/BI Dashboards, Power BI integration |

---

## Project 1: AI-Powered Cybersecurity Incident Classification Pipeline

**Role:** Data Engineer | **Industry:** Energy & Utilities (Regulated)
**Platform:** Databricks SQL, Azure Databricks, Unity Catalog

### Business Problem
The organization generates thousands of operational incident records across multiple facilities. Manual review of each record for cybersecurity relevance was time-intensive, inconsistent, and could not scale. The security team needed an automated, auditable classification system aligned to industry compliance frameworks.

### Solution Built
Designed and implemented an end-to-end AI classification pipeline using Databricks SQL AI functions — no Python, no model training, no external APIs required.

### Architecture
```
Source Table (incident records)
    ↓
Data Preparation (CONCAT 6 text columns, COALESCE for NULLs)
    ↓
CTE Pipeline (WITH ... AS)
    ↓
ai_query() → Claude 3.7 Sonnet (prompt engineering with classification criteria)
    ↓
from_json() → Parse structured JSON response
    ↓
Delta Table (permanent, auditable results)
    ↓
Dashboard / Reporting
```

### Key Technical Contributions
* **Prompt Engineering:** Designed a structured classification prompt that returns consistent JSON output with three fields: `cyber_relevant` (yes/no), `escalation_score` (0–3), and `justification` — aligned to industry compliance frameworks (NIST CSF, CIS Controls)
* **Cost-Optimized Pipeline:** Implemented `LIMIT`-based test batches (10 rows) before full runs, `temperature=0` for deterministic outputs, and `max_tokens=300` to control per-query cost
* **Batch Processing Strategy:** Designed year-based batch processing (`WHERE record_year = 2024`) allowing incremental classification of historical records without re-processing
* **Data Quality Handling:** Used `COALESCE(column, 'N/A')` to handle NULL values across 6 text columns, preventing prompt injection failures
* **Structured Parsing:** Applied `from_json()` to convert LLM string responses into queryable struct columns, with `CASE` expressions for human-readable escalation labels
* **Governance:** Results stored in Unity Catalog-governed Delta tables with full audit trail, accessible to security dashboards

### Results
* Automated classification of 2,500+ incident records across multiple facilities
* Reduced manual review time from days to minutes
* Consistent scoring aligned to industry compliance frameworks
* Enabled trend analysis: yearly and quarterly cybersecurity classification distribution

### Technologies Used
`ai_query()` | `from_json()` | Claude 3.7 Sonnet | CTE Pipelines | Delta Lake | Unity Catalog | Serverless SQL Warehouse

---

## Project 2: Security Metric AI Enrichment & Risk Classification

**Role:** Data Engineer | **Industry:** Energy & Utilities (Regulated)
**Platform:** Databricks SQL, Azure Databricks, Unity Catalog

### Business Problem
The security dashboard tracked 39 cybersecurity metrics across multiple business units, but lacked automated risk domain tagging, executive-friendly summaries, and trend analysis. Manual categorization was inconsistent and couldn't scale as new metrics were added.

### Solution Built
Built a multi-function AI enrichment pipeline using Databricks SQL AI functions to automatically classify, summarize, and analyze security metrics — all in pure SQL.

### Architecture
```
metric_master (definitions)
    ↓ JOIN via bridge table
metric_target_monthly_snapshot → metric_monthly_snapshot (values)
    ↓
Multi-function AI enrichment in one SELECT:
  • ai_classify() → Risk Domain
  • ai_summarize() → Executive Summary
  • ai_analyze_sentiment() → Tone Analysis
    ↓
--name → Python DataFrame + SQL temp view
    ↓
Delta Table / Dashboard
```

### Key Technical Contributions
* **Multi-function AI Queries:** Combined `ai_classify()`, `ai_summarize()`, and `ai_analyze_sentiment()` in a single SQL SELECT — enriching each metric in one pass
* **Custom Risk Domain Taxonomy:** Defined 7 risk domains (Identity & Access, Vulnerability Management, Incident Response, Compliance, Network Security, Data Protection, Awareness & Training) and classified 20+ active metrics automatically
* **Bridge Table Discovery:** Identified and resolved a three-table JOIN pattern (`metric_master → metric_target_monthly_snapshot → metric_monthly_snapshot`) where tables had no direct key relationship
* **Cross-Language Chaining:** Used `--name` syntax to bridge SQL results into Python for pandas analysis, charting, and export
* **Executive Reporting:** Built `ai_query()` prompts that generate board-ready executive summaries from aggregated metric data
* **Reusable Views:** Designed AI-enriched views that any team member can query without re-running AI functions

### Results
* Automated risk domain classification for all 20 active security metrics
* Generated executive summaries eliminating manual report writing
* Identified metric coverage gaps across business units
* Created reusable SQL patterns for future metric onboarding

### Technologies Used
`ai_classify()` | `ai_summarize()` | `ai_analyze_sentiment()` | `ai_query()` | `ai_extract()` | Bridge Table JOINs | CTEs | `--name` SQL-to-Python chaining | Delta Lake | Unity Catalog

---

## Project 3: Unity Catalog Governance & Infrastructure Design

**Role:** Data Engineer | **Industry:** Energy & Utilities (Regulated)
**Platform:** Azure Databricks, Unity Catalog, Azure ADLS Gen2

### Key Contributions
* **Access Model Design:** Designed role-based access patterns for Developers (read/write/create), Readers (read-only), and External Users (per-table grants) using Unity Catalog SQL GRANT hierarchy
* **Cross-Catalog Data Sharing:** Implemented cross-catalog access within the same metastore using grant chains (`USE CATALOG → USE SCHEMA → SELECT`) — enabling teams to query across multiple catalogs without data duplication
* **Private Endpoint Networking:** Diagnosed and resolved serverless compute 403 errors caused by missing NCC private endpoint rules for private-endpoint ADLS storage accounts
* **Azure Access Connector:** Leveraged Managed Identity authentication via Azure Access Connector — eliminating manual storage credential management across catalogs
* **Compute Strategy:** Evaluated and documented All-Purpose Clusters, Jobs Clusters, Serverless Compute, and SQL Warehouses (Serverless/Pro/Classic) with cost optimization recommendations
* **AD Group Integration:** Designed the full identity flow from Entra ID (AAD) → SCIM sync → Account Console → Workspace assignment → Catalog grants for three user personas

### Deliverables
* Comprehensive Databricks architecture reference document covering workspace vs catalog governance, compute selection, networking, and role-based access
* SQL Warehouse Quick Reference (Pro vs Serverless) with environment-specific test results and DNS/network route findings
* Git integration setup and repository management workflows

---

## Key Patterns & Approaches Demonstrated

### AI Pipeline Design Pattern
```
1. Preview data (SELECT ... LIMIT 5)           — understand the source
2. Prepare text (CONCAT + COALESCE)             — combine columns, handle NULLs
3. Test small (LIMIT 10 + temp view)            — validate before scale
4. Review results                               — check for errors/misclassification
5. Run full (CREATE TABLE + batch by year)      — scale with cost control
6. Analyze (GROUP BY + dashboard)               — derive insights
```

### Prompt Engineering Best Practices
* Define a **role** for the LLM ("You are a security analyst...")
* Specify exact **output format** (JSON with field names)
* Use `temperature=0` for **deterministic** results
* Set `max_tokens` to **control cost** per query
* Include **classification criteria** in the prompt for auditability

### Cost Optimization
* Always test with `LIMIT 10` before full runs
* Batch by time period (`WHERE year = 2024`) for large datasets
* Use `ai_classify()` over `ai_query()` when categories are fixed (cheaper)
* Combine multiple AI functions in one SELECT (single table scan)
* Use CTE to aggregate before AI (AI runs on 5 summary rows, not 5,000 raw rows)

---

## Certifications & Learning Path

| Completed | Area |
|---|---|
| Databricks SQL AI Functions (9 functions) | ai_gen, ai_classify, ai_extract, ai_summarize, ai_analyze_sentiment, ai_translate, ai_similarity, ai_fix_grammar, ai_query |
| Unity Catalog Governance | Catalog/Schema/Table grants, RBAC, cross-catalog sharing |
| Azure Networking for Databricks | Private Endpoints, NCC, Access Connector, VNet injection |
| Delta Lake & Compute | Tables, Views, Materialized Views, Serverless vs Pro |
| Git Integration | Repos, clone, branch, commit/push via API |

| Next Steps | Area |
|---|---|
| `ai_forecast()` | Time-series prediction (pending admin preview enablement) |
| Knowledge Graph (SQL) | Entity extraction + graph traversal using AI functions |
| Model Serving | Deploy AI pipelines as REST endpoints |
| Vector Search | RAG applications for document Q&A |
| Lakeflow Spark Declarative Pipelines | Production-grade streaming + batch ETL |
