🚀 Azure End-to-End ETL Pipeline — ADF + Databricks


💡 **Project Goal**

This repository implements a modern, scalable, end-to-end Data Engineering Pipeline using core Microsoft Azure services. The goal is to ingest raw data, transform it using powerful Spark processing, and deliver analytics-ready data layers for business intelligence (BI) and machine learning (ML) consumption.
This project focuses on production-style patterns including automated orchestration, implementation of the Medallion Architecture, and scalable PySpark transformations.


**☁️ Architectural Overview**
The pipeline is built on an ELT (Extract, Load, Transform) framework, leveraging the strengths of each Azure service:
ComponentResponsibilityRole⚙️ Azure Data Factory (ADF)Orchestration & Data MovementSequences and schedules the workflow — controls data ingestion via Copy Activity🔥 PySpark (Google Colab)Scalable Data TransformationRuns complex transformation logic across Bronze → Silver → Gold layers🗄️ Azure Data Lake Storage (ADLS)Data Lakehouse StorageProvides tiered, scalable storage for all Medallion layers🔷 Delta LakeReliability & ACID PropertiesBrings ACID transactions and versioning to the data lake

📌 Note: Due to Azure student subscription limitations, the ETL notebook runs in Google Colab instead of Azure Databricks. The ADF pipeline handles the Copy Activity (raw → bronze). PySpark transformations (Silver & Gold layers) run via ETL_Notebook.ipynb in Google Colab connected directly to ADLS.


🥇 Medallion Architecture
Data is segregated into three distinct layers within Azure Data Lake to ensure quality, traceability, and governance:
LayerStatePurpose🥉 BronzeRaw DataStores data as-is after ingestion. Provides an immutable historical record🥈 SilverCleaned & ConformedData is cleansed, standardized, deduplicated. Ready for exploration🥇 GoldCurated & AggregatedAggregated and modeled data optimized for BI reporting and ML training

📂 Repository Structure
Azure-ETL-Pipeline-ADF-Databricks/
│
├── 📁 data/
│   ├── customers.json          ← Customer profiles with membership tiers
│   ├── products.json           ← Product catalog with categories & pricing
│   ├── transactions.json       ← Sales transactions linking all entities
│   └── stores.json             ← Store locations across US regions
│
├── 📁 notebooks/
│   └── ETL_Notebook.ipynb      ← Full PySpark ETL logic (Bronze→Silver→Gold)
│
├── 🖼️ Azure_Architecture_Diagram.svg
├── 🖼️ Azure_DataFactory_Photo.svg
└── 📄 README.md

🗄️ ADLS Storage Structure
medallion/                        ← ADLS container
├── raw/                          ← Original 4 JSON source files
│   ├── customers.json
│   ├── products.json
│   ├── transactions.json
│   └── stores.json
│
├── bronze/                       ← Raw files copied here by ADF
│   ├── customers.json
│   ├── products.json
│   ├── transactions.json
│   └── stores.json
│
├── silver/                       ← Cleaned Delta tables (PySpark)
│   ├── customers/
│   ├── products/
│   ├── transactions/
│   └── stores/
│
└── gold/                         ← Business report Delta tables
    ├── sales_by_category/
    ├── sales_by_region/
    └── customer_lifetime_value/

⚙️ Detailed Pipeline Steps
Step 1 — 📥 Data Ingestion (ADF → Bronze)

Action: ADF Copy Activity extracts all raw JSON files from raw/ folder
Sink: Loads them directly into the bronze/ folder in ADLS
Goal: Establish an immutable record of the raw source data

Step 2 — 🥈 Transformation & Cleansing (Bronze → Silver)
Action: ETL_Notebook.ipynb runs PySpark transformations:

✅ Reads raw data from the Bronze layer
✅ Handles null values (age, email, stock_quantity, rating, discount)
✅ Casts all columns to correct data types (IntegerType, DoubleType, DateType)
✅ Standardizes strings — trims whitespace, uppercases membership & region
✅ Removes duplicate records by primary key
✅ Writes cleaned data to Silver as Delta Lake tables

Step 3 — 🥇 Business Logic & Aggregation (Silver → Gold)
Action: Notebook continues with business aggregation:

✅ Reads all 4 cleaned Silver tables
✅ Joins transactions + products + customers + stores
✅ Calculates revenue: (price × quantity) − discount
✅ Produces 3 Gold report tables (see below)
✅ Writes final data to Gold as Delta Lake tables

Step 4 — 📊 Data Consumption

Result: Gold layer Delta tables are analytics-ready
Downstream tools like Power BI can connect directly to curated tables


📊 Gold Layer Output Tables
1️⃣ Sales by Category
category | total_transactions | total_units_sold | total_revenue | avg_revenue_per_sale
2️⃣ Sales by Region
region | store_name | total_transactions | total_revenue
3️⃣ Customer Lifetime Value
membership | total_orders | total_revenue | avg_order_value

🔧 How to Run
bash# 1. Clone this repository
git clone https://github.com/<your-username>/Azure-ETL-Pipeline-ADF-Databricks.git

# 2. Upload data files to ADLS
#    Go to Azure Portal → learningetl → medallion → raw/
#    Upload all 4 JSON files from data/ folder

# 3. Open notebook in Google Colab
#    Upload notebooks/ETL_Notebook.ipynb to Google Colab

# 4. Update Cell 1 in the notebook
STORAGE_ACCOUNT = "learningetl"
ACCESS_KEY = "<your-access-key>"

# 5. Run all cells — Bronze → Silver → Gold tables written to ADLS

# 6. Trigger ADF pipeline
#    Azure Portal → ADF Studio → PL_ETL_Medallion_Pipeline → Debug

🐛 Challenges & Solutions
❌ Challenge✅ SolutionDatabricks had no cluster (student subscription)Switched to Google Colab with PySparkPySpark 4.0 + Delta Lake Scala version mismatchDowngraded to PySpark 3.5.1Java not found in Colab environmentInstalled OpenJDK 11 manuallyADLS abfss:// driver missing in ColabUsed Azure Storage SDK for direct file accessADF EndpointUnsupportedAccountFeatures errorDisabled Soft Delete in Storage AccountBronze folder invisible in ADF (empty folder)Uploaded placeholder file to make directory visibleADF cannot trigger Google Colab notebookNotebook runs manually in Colab; ADF handles Copy Activity

✅ Data Engineering Features

🔄 End-to-End ELT/ETL — Full pipeline from raw ingestion to reporting-ready data
⚡ Scalability — PySpark handles large-scale parallel data transformations
🎯 Orchestration — ADF manages dependencies, scheduling, and pipeline flow
🛡️ Data Quality — Medallion Architecture enforces quality at every layer
🔷 Delta Lake — ACID transactions, time travel, and schema enforcement
📁 Modular Design — Each layer is independent and reusable


📚 Key Learnings

🏗️ Medallion Architecture cleanly separates raw, cleaned, and business-ready data
🔷 Delta Lake adds reliability (ACID transactions) to a data lake
⚙️ Azure Data Factory orchestrates pipelines without writing infrastructure code
⚡ PySpark DataFrames handle large-scale transformations efficiently
🛡️ Real-world pipelines always need data quality handling — nulls, type casting, deduplication
☁️ Azure student subscriptions have limitations — problem-solving around constraints is a real skill
