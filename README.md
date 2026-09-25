Global Sales & Customer Tiering Analytics Pipeline 

This repository showcases an end-to-end data pipeline built in Snowflake, implementing a modern Medallion Architecture (Bronze -> Silver -> Gold). The project simulates a multi-region retail environment to process raw transactional data, normalize currencies, handle automated bulk ingestions and deliver business-ready customer value segmentation.

Architecture & Data Flow

The project is structured into three distinct layers to ensure data quality, traceability and high-performance analytics:

Bronze Layer (Raw): Ingests raw multi-country CSV files directly from cloud storage (Google Cloud Storage) using bulk COPY INTO commands, regular expression pattern matching, and robust error-handling protocols.

Silver Layer (Cleaned & Typed): Transforms string-based raw fields into strongly-typed relational models. It applies business logic such as gross-to-net revenue adjustments and integrates custom SQL utility functions to assign fiscal quarters dynamically.

Gold Layer (Aggregated Analytics): Focuses on business value by generating high-performance reporting tables. It leverages advanced Window Functions (PERCENT_RANK()) to perform RFM-style customer value tiering (e.g., VIP Platinum, Gold, Silver).

Tech Stack & Skills Highlight

Cloud Data Warehouse: Snowflake (ACCOUNTADMIN, Custom Warehouses, External Stages).

Cloud Storage & Integration: Google Cloud Storage (GCS), Storage Integrations, File Formats.

SQL Advanced Concepts: User-Defined Functions (UDFs), Window Functions, Common Table Expressions (CTEs), Explicit Type Casting (TRY_CAST), and Conditional Logic (CASE...WHEN).

Data Engineering Practices: Automated bulk ingestion patterns, fault-tolerant loading (ON_ERROR = 'CONTINUE'), and multi-tier architectural design.

📂 Repository Structure
Plaintext
├── 01_setup_and_infrastructure.sql  # Warehouse, database, and Medallion schemas setup
├── 02_bronze_ingestion.sql          # Storage integration, external stage, and bulk copy
├── 03_utility_functions.sql         # Custom SQL functions for fiscal calendar mapping
├── 04_silver_transformation.sql     # Data cleansing, typing, and net revenue calculations
└── 05_gold_analytics.sql            # Window functions and customer tiering models


Key Highlights from the Code

Fault-Tolerant Bulk Ingestion: Utilizing regex patterns (PATTERN = '.*_sales_2019\.csv') combined with ON_ERROR = 'CONTINUE' to ensure pipeline resilience during large batch uploads.

Advanced Customer Segmentation: Using window partitioning (PERCENT_RANK() OVER (PARTITION BY STORE_COUNTRY ORDER BY LIFETIME_SPEND DESC)) to automatically classify global buyers into targeted tiers for marketing and BI reporting.

Note: This repository represents a simulated enterprise project designed to demonstrate modern data engineering patterns and cloud analytics capabilities.
