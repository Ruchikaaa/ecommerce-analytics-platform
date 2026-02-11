# E-commerce Analytics Platform (110M+ Records)
### **End-to-End Medallion Lakehouse on Databricks**

## 📋 Project Overview
This project implements a production-grade **Medallion Architecture** to process and analyze over **110 million e-commerce events**. The platform handles high-volume streaming ingestion, historical data tracking (SCD Type 2), and business-critical KPI generation while enforcing strict security through Row-Level and Column-Level security.



---

## 🏗️ Architecture: The Medallion Journey

### 🥉 Bronze: Raw Ingestion
* **Technology**: Databricks **Auto Loader** (`cloudFiles`).
* **Scale**: 110M+ CSV records ingested incrementally from ADLS Gen2.
* **Key Features**: Schema evolution, fault-tolerant checkpointing, and audit metadata injection.

### 🥈 Silver: Cleaning & Validation
* **Technology**: Delta Live Tables (DLT).
* **Quality Gates**: Implements **DLT Expectations** to quarantine records with invalid prices or null identifiers.
* **Logic**: Type casting, behavioral flagging, and deduplication across the 110M event stream.

### 🥇 Gold: Star Schema & KPIs
* **Modeling**: Full **Star Schema** implementation.
* **SCD Type 2**: Tracking historical product price changes using the `apply_changes` API to maintain point-in-time accuracy.
* **Aggregates**: Business-ready tables for Brand Performance and Customer Behavioral Metrics.

---

## 🛡️ Governance & Security (Domain 5)
Leveraging **Unity Catalog**, the platform implements an enterprise-ready security model:
* **Row-Level Security (RLS)**: Dynamically filters events based on user group (e.g., Finance sees purchases, Marketing sees behavior).
* **Column-Level Security (CLS)**: Protects PII through hashing (`user_id`) and redacts sensitive financial data (`price`) for unauthorized roles.
* **Unified RBAC**: Explicit permission management for `finance_team`, `marketing_team`, and `ops_team`.



---

## ⚡ Performance Optimization
* **Z-Ordering**: Multi-dimensional clustering on `event_time` and `product_id` to maximize file skipping.
* **Photon Engine**: High-performance vectorized execution used for heavy shuffles and joins.
* **Shuffle Tuning**: Optimized `spark.sql.shuffle.partitions` (set to 400) to handle the 110M record volume.
* **Broadcast Joins**: Strategic use of `broadcast()` for dimension tables to minimize network I/O.

---

## 🎡 DevOps & CI/CD
* **Infrastructure as Code (IaC)**: Defined via **Databricks Asset Bundles (DABs)** for environment portability.
* **CI/CD**: **GitHub Actions** workflows automate deployments to **Dev** and **Prod** workspaces.
* **Orchestration**: A unified **Databricks Workflow** (DAG) manages the pipeline, unit tests, and security audits.



---

## 🧪 Testing & Quality Assurance
A comprehensive `unittest` suite ensures platform reliability:
1.  **Schema Validation**: Integration checks against real ADLS Gen2 source files.
2.  **Logic Verification**: Unit tests for transformation thresholds and boundary cases.
3.  **Data Reconciliation**: Proves row-count integrity from Bronze to Gold.
4.  **Integrity Hashes**: SHA-256 verification to ensure data has not been corrupted.

---

## 🚀 Deployment Instructions
1.  **Prerequisites**: 
    * Databricks CLI configured.
    * Access to Azure ADLS Gen2 storage account.
2.  **Deploy the Bundle**:
    ```bash
    databricks bundle deploy -t dev
    ```
3.  **Trigger the Workflow**:
    * Navigate to the **Workflows** tab in Databricks and run `E2E_Ecommerce_Analytics_Workflow`.