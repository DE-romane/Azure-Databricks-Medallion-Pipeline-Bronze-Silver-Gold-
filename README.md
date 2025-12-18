# Azure-Databricks-Medallion-Pipeline (Bronze-Silver-Gold)
## Business Request

In this project, the company identified a gap in understanding customer demographics—specifically the **gender distribution of customers** and how it impacts **product purchases and sales performance**. Stakeholders requested a **KPI-driven dashboard** that provides:

* Sales performance split by **gender** and **product category**
* Key metrics such as **total products sold** and **total sales revenue**
* Interactive **filters for gender, product category, and date**
* A user-friendly interface to support business decision-making

The source data resides in an **on‑premises SQL Server**, requiring a secure and automated cloud-based analytics solution.

---

## Solution Overview

To meet this business need, an **end-to-end Azure data pipeline** was designed and implemented. The solution:

* Extracts data from the on‑premises SQL Server
* Loads raw data into Azure Data Lake Storage
* Transforms data using Databricks into analytics-ready datasets
* Serves curated data through Synapse Serverless SQL views
* Feeds a custom-built **Power BI KPI dashboard**

The pipeline is **fully automated with daily scheduling**, ensuring stakeholders always have access to **accurate, up‑to‑date insights**.

---

## Data Source

* **Primary Source**: On‑premise SQL Server (AdventureWorksLT2019 database)
* **Data Domain**: SalesLT schema (customers, products, sales, addresses)
* **Ingestion Method**: Azure Data Factory using Self‑Hosted Integration Runtime

---

## High-Level Workflow

1. Ingest on‑prem SQL data into Azure Data Lake (Bronze)
2. Transform raw data using Databricks (Bronze → Silver → Gold)
3. Expose curated data via Azure Synapse Serverless SQL views
4. Build reports and dashboards in Power BI
5. Secure and govern access using Entra ID and Azure IAM
6. Automate and test the end‑to‑end pipeline

---

## Step-by-Step Architecture

### 1. Environment Setup

* Create Azure resources: Resource Group, Storage Account (ADLS Gen2), ADF, Databricks, Synapse, Key Vault
* Configure SQL Server locally and restore AdventureWorks database
* Store SQL credentials securely in Azure Key Vault

---

### 2. Data Ingestion (ADF)

* Install and configure Self‑Hosted Integration Runtime
* Build ADF pipelines to:

  * Copy individual tables from on‑prem SQL
  * Dynamically ingest all SalesLT tables using Lookup + ForEach
* Store ingested data as **Parquet files** in the **Bronze layer**

---

### 3. Data Transformation (Databricks)

* Mount ADLS containers (Bronze, Silver, Gold)
* **Bronze → Silver (L1)**:

  * Data type standardization (datetime → date)
  * Write cleaned data in **Delta format**
* **Silver → Gold (L2)**:

  * Column renaming (PascalCase → UPPER_SNAKE_CASE)
  * Create analytical fact and dimension tables

---

### 4. Orchestration (ADF + Databricks)

* Trigger Databricks notebooks directly from ADF pipelines
* Chain ingestion → transformation steps
* Enable monitoring and debugging via pipeline runs

---

### 5. Data Serving (Synapse Analytics)

* Create **Serverless SQL Pool** database
* Query Delta tables directly from the Data Lake
* Dynamically generate SQL views for each Gold table using:

  * Stored procedures
  * Synapse pipelines with metadata + ForEach

---

### 6. Reporting (Power BI)

* Connect Power BI Desktop to Synapse Serverless SQL
* Import analytical views
* Build semantic model (facts & dimensions)
* Create dashboards (KPIs, sales metrics, customer insights)

---

### 7. Security & Governance

* Use **Azure Entra ID** security groups
* Assign RBAC roles at Resource Group and Storage levels
* Use Managed Identities and Key Vault for secret management

---

### 8. Automation & Testing

* Schedule ADF triggers for daily ingestion
* Insert new records into SQL source for validation
* Verify automatic propagation to Data Lake, Synapse views, and Power BI reports

---

## Outcome

* Fully automated, secure, and scalable **end‑to‑end Azure data platform**
* Supports analytics, BI reporting, and future incremental processing
