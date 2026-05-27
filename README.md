Project Overview
This project implements a modern, scalable, end-to-end Data Engineering Pipeline using core Microsoft Azure services. Raw JSON data is ingested via Azure Data Factory, stored in Azure Data Lake Storage Gen2, and transformed through the Medallion Architecture (Bronze → Silver → Gold) using PySpark on Google Colab.
The final Gold layer produces analytics-ready Delta Lake tables for business intelligence reporting.

Tech Stack
ToolPurposeAzure Data Factory (ADF)Pipeline orchestration — copies raw files to Bronze layerAzure Data Lake Storage Gen2Tiered storage for all medallion layersGoogle Colab + PySparkData transformation engine (Bronze → Silver → Gold)Delta LakeReliable ACID-compliant open table formatGitHubVersion control and project documentation

Medallion Architecture
LayerFormatDescription🥉 BronzeJSONRaw data as-is — immutable historical record🥈 SilverDelta LakeCleaned, deduplicated, type-cast data🥇 GoldDelta LakeAggregated, joined, business-ready reports

Source Data
Four JSON files simulate a real-world retail business dataset:
FileRecordsDescriptioncustomers.json15Customer profiles with name, city, membership tierproducts.json15Product catalog with categories, price, stock, ratingtransactions.json25Sales transactions linking customers, products, storesstores.json4Store locations across US regions

Intentional nulls and type inconsistencies were included to demonstrate real-world data cleansing in the Silver layer.


Pipeline Steps
Step 1 — Data Ingestion (ADF → Bronze)

ADF Copy Activity reads all JSON files from the raw/ folder in ADLS
Copies them as-is into the bronze/ folder
Establishes an immutable record of the raw source data

Step 2 — Cleansing (Bronze → Silver)
PySpark transformations in ETL_Notebook.ipynb:

Fill null values (age, email, stock_quantity, rating, discount)
Cast all columns to correct data types (IntegerType, DoubleType, DateType)
Standardize strings — trim whitespace, uppercase membership and region fields
Remove duplicate records by primary key
Write cleaned data to Silver as Delta Lake tables

Step 3 — Aggregation (Silver → Gold)
Three business report tables are produced:
Sales by Category
category | total_transactions | total_units_sold | total_revenue | avg_revenue_per_sale
Sales by Region
region | store_name | total_transactions | total_revenue
Customer Lifetime Value
membership | total_orders | total_revenue | avg_order_value
Step 4 — Output
Gold layer Delta tables are the final deliverable — ready for BI tools or ML pipelines.

Repository Structure
Azure-ETL-Pipeline-ADF-Databricks/
│
├── data/
│   ├── customers.json
│   ├── products.json
│   ├── transactions.json
│   └── stores.json
│
├── notebooks/
│   └── ETL_Notebook.ipynb
│
├── Azure_Architecture_Diagram.svg
└── README.md

ADLS Storage Structure
medallion/  (ADLS container)
├── raw/           ← original 4 JSON source files (uploaded manually)
├── bronze/        ← raw files copied here by ADF Copy Activity
├── silver/        ← 4 cleaned Delta tables written by PySpark
│   ├── customers/
│   ├── products/
│   ├── transactions/
│   └── stores/
└── gold/          ← 3 aggregated business report Delta tables
    ├── sales_by_category/
    ├── sales_by_region/
    └── customer_lifetime_value/

Challenges & Solutions
ChallengeSolutionDatabricks had no cluster (student subscription)Switched to Google Colab with PySparkPySpark 4.0 + Delta Lake Scala version mismatchDowngraded to PySpark 3.5.1Java not found in ColabInstalled OpenJDK 11 manuallyADLS abfss:// driver missing in ColabUsed Azure Storage SDK for file accessADF EndpointUnsupportedAccountFeatures errorDisabled Soft Delete in Storage Account settingsBronze folder invisible in ADF browser (empty folder)Uploaded placeholder file to make directory visible

Key Learnings

Medallion Architecture cleanly separates raw, cleaned, and business-ready data
Delta Lake adds ACID transactions and schema enforcement to a data lake
Azure Data Factory orchestrates pipelines without writing infrastructure code
PySpark DataFrames handle large-scale transformations efficiently in distributed compute
Real-world pipelines always require data quality handling — nulls, type casting, deduplication


How to Run

Clone this repository
Upload the 4 JSON files from data/ into your ADLS raw/ folder
Open notebooks/ETL_Notebook.ipynb in Google Colab
Update STORAGE_ACCOUNT and ACCESS_KEY in Cell 1
Run all cells — Bronze → Silver → Gold tables will be written to ADLS
In Azure Data Factory, trigger the pipeline to copy raw/ → bronze/
