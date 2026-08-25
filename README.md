<p align="center">
  <img width="4950" height="1284" alt="image" src="https://github.com/user-attachments/assets/f16a8129-dd0c-4eea-b271-29c233a48ba5" />
</p>

# payement-gateway-databricks-lakehouse-pipeline

An end-to-end, production-grade financial payment pipeline built on **Databricks (Medallion Architecture)** and powered by **LLM-driven AI Briefings**.

payement-gateway-databricks-lakehouse-pipeline standardizes multi-source semi-structured payment event logs (Razorpay, Dodo Payments, and Slice), computes BI-ready data marts, and generates automated executive risk summaries.

---

## Architecture Overview

```
[ Synthetic Event Generator ] ➔ (Razorpay / Dodo / Slice)
                                        │
                                        ▼
   BRONZE: raw_payment_json ➔ [ Immutable append-only logs ]
                                        │
                                        ▼
   SILVER: silver_transactions ➔ [ PySpark MERGE INTO, FX Conversion & Schema Conformity ]
                                        │
                                        ▼
   GOLD: financial_marts ➔ [ Daily KPIs & Top Root Causes ]
                                        │
                                        ▼
   AI LAYER: Groq Engine ➔ [ Llama-3.3-70B Automated Risk & Incident Briefings ]

```

---

## Project Structure

```
payment-gateway-databricks-pipeline/
├── .env                       # Environment configuration
├── .gitignore                 # Git ignore rules
├── 00_setup_tables            # Unity Catalog & Delta table initializations
├── 01_data_generation         # Synthetic payment payload generator
├── 02_bronze_transform        # Bronze layer raw JSON ingestion
├── 03_silver_transform        # Silver layer schema conforming & MERGE logic
├── 04_gold_transform          # Gold layer KPI rollups & failure aggregations
├── 05_ai_agent                # Groq LLM integration & Slack alerting
├── 06_pipeline                # SDK Orchestrator & Databricks Workflow Job setup
└── README.md                  # Project documentation

```

---

## Medallion Data Pipeline

* **Bronze Layer (`02_bronze_transform`):** Preserves immutable, raw payment events across all providers with zero data loss.
* **Silver Layer (`03_silver_transform`):** Standardizes transaction amounts into base currency (`INR`), enforces schema constraints, masks sensitive fields, and performs idempotent upserts via Delta `MERGE INTO`.
* **Gold Layer (`04_gold_transform`):** Aggregates daily performance metrics (Success Rates, Total Volumes, Settled Volumes) and top error codes.
* **AI Layer (`05_ai_agent`):** Consumes Gold Markdown tables and feeds context into `llama-3.3-70b-versatile` via the Groq API to generate executive briefings.

---

## Quickstart

### Prerequisites

* Databricks Workspace (Runtime 13.3+ LTS recommended)
* Databricks SDK (`pip install databricks-sdk`)
* Groq API Key ( `groq_api_key`)

### Deploy & Run the Pipeline

To deploy the workflow job programmatically and trigger execution, run notebook **`06_pipeline`**:

```python
from databricks.sdk import WorkspaceClient

w = WorkspaceClient()

# Orchestrator handles job creation/reset and scheduling automatically
run_now_response = w.jobs.run_now(job_id=job_id)
print(f"Run ID: {run_now_response.response.run_id}")

```

---

## Scheduling & Orchestration

The pipeline is orchestrated via **Databricks Workflows** and configured to run automatically:

* **Schedule:** `0 0 12 ? * MON-FRI *` (Mon–Fri at 12:00 PM UTC)
