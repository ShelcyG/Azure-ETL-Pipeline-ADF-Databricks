# 🚀 Azure End-to-End ETL Pipeline — ADF + Databricks

![Azure](https://img.shields.io/badge/Microsoft_Azure-0078D4?style=for-the-badge&logo=microsoft-azure&logoColor=white)
![PySpark](https://img.shields.io/badge/PySpark-E25A1C?style=for-the-badge&logo=apache-spark&logoColor=white)
![Delta Lake](https://img.shields.io/badge/Delta_Lake-00A4EF?style=for-the-badge&logo=delta&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen?style=for-the-badge)

---

## 💡 Project Goal

This repository implements a modern, scalable, end-to-end **Data Engineering Pipeline** using core Microsoft Azure services. The goal is to ingest raw data, transform it using Spark, and deliver analytics-ready data layers for BI and ML use cases.

This project focuses on production-style patterns including automated orchestration, **Medallion Architecture**, and scalable **PySpark** transformations.

---

## 🏗️ Architecture Diagram

![Architecture](Azure_Architecture_Diagram.svg)

---

## ⚙️ ADF Pipeline

![ADF Pipeline](Azure_DataFactory_Photo.svg)

---

## ☁️ Architectural Overview

The pipeline is built on an **ELT (Extract, Load, Transform)** framework, leveraging the strengths of each Azure service:

| Component | Responsibility | Role |
|---|---|---|
| ⚙️ Azure Data Factory (ADF) | Orchestration & Data Movement | Sequences and schedules workflow, controls data ingestion via Copy Activity |
| 🔥 PySpark (Google Colab) | Scalable Data Transformation | Runs transformation logic across Bronze → Silver → Gold layers |
| 🗄️ Azure Data Lake Storage (ADLS) | Data Lakehouse Storage | Provides scalable storage for all Medallion layers |
| 🔷 Delta Lake | Reliability & ACID Properties | Adds ACID transactions and versioning to the data lake |

> 📌 Note: Due to Azure student subscription limitations, PySpark runs in Google Colab instead of Databricks. ADF handles only the ingestion (raw → bronze), while transformations run in the notebook.

---

## 🥇 Medallion Architecture

| Layer | State | Purpose |
|------|------|--------|
| 🥉 Bronze | Raw Data | Stores raw ingested data (immutable source) |
| 🥈 Silver | Cleaned Data | Data cleansing, deduplication, standardization |
| 🥇 Gold | Business Data | Aggregated data for BI and reporting |

---

## 📂 Repository Structure
