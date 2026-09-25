# Global Sales & Customer Tiering Analytics Pipeline 

This repository showcases an end-to-end data pipeline built in **Snowflake**, implementing a modern **Medallion Architecture (Bronze -> Silver -> Gold)**. The project simulates a multi-region retail environment to process raw transactional data, normalize formats, handle automated bulk ingestions, and deliver business-ready customer value segmentation.

---

## Architecture & Data Flow

The project is structured into three distinct layers to ensure data quality, traceability, and high-performance analytics:

1. **Bronze Layer (Raw):** Ingests raw multi-country CSV files directly from cloud storage (Google Cloud Storage) using bulk `COPY INTO` commands, regular expression pattern matching, and robust error-handling protocols.
2. **Silver Layer (Cleaned & Typed):** Transforms string-based raw fields into strongly-typed relational models. It applies business logic such as gross-to-net revenue adjustments and integrates custom SQL utility functions to assign fiscal quarters dynamically.
3. **Gold Layer (Aggregated Analytics):** Focuses on business value by generating high-performance reporting tables. It leverages advanced **Window Functions** (`PERCENT_RANK()`) to perform RFM-style customer value tiering (e.g., VIP Platinum, Gold, Silver).

---

## Tech Stack & Skills Highlight

* **Cloud Data Warehouse:** Snowflake (`ACCOUNTADMIN`, Custom Warehouses, External Stages).
* **Cloud Storage & Integration:** Google Cloud Storage (GCS), Storage Integrations, File Formats.
* **SQL Advanced Concepts:** User-Defined Functions (UDFs), Window Functions, Common Table Expressions (CTEs), Explicit Type Casting (`TRY_CAST`), and Conditional Logic (`CASE...WHEN`).
* **Data Engineering Practices:** Automated bulk ingestion patterns, fault-tolerant loading (`ON_ERROR = 'CONTINUE'`), and multi-tier architectural design.

---

## 📂 Repository Structure & Execution

To keep the pipeline streamlined and easy to test, the entire end-to-end process is contained within a single, well-documented script:

* **`global_sales_pipeline.sql`**: Contains the complete lifecycle script—from warehouse setup, storage integration, and bronze ingestion to utility functions, silver transformations, and gold analytics.

---

## Key Highlights from the Code

* **Fault-Tolerant Bulk Ingestion:** Utilizing regex patterns (`PATTERN = '.*_sales_2026\.csv'`) combined with `ON_ERROR = 'CONTINUE'` to ensure pipeline resilience during large batch uploads.
* **Advanced Customer Segmentation:** Using window partitioning (`PERCENT_RANK() OVER (PARTITION BY STORE_COUNTRY ORDER BY LIFETIME_SPEND DESC)`) to automatically classify global buyers into targeted tiers for marketing and BI reporting.

---

## 📊 Business Intelligence & Power BI Integration

To transform the processed data into actionable insights, the `GOLD_CUSTOMER_SALES_SUMMARY` table feeds directly into a Power BI executive dashboard. 

* **Connection Mode:** Connected via **Import Mode** (optimized for fast local calculations on aggregated summaries) with a **Scheduled Daily Refresh** configured in Power BI Service to sync automatically right after the Snowflake pipeline execution; or optionally via **DirectQuery** for live transactional reporting against Snowflake.

### Dashboard Preview
![Power BI Executive Dashboard](assets/powerbi_dashboard.png)

### Core DAX Measures (`power_bi_measures.dax`)

The report utilizes optimized DAX measures to compute dynamic business metrics:

```dax
-- Total Net Revenue across all filtered contexts
Total_Net_Revenue = SUM('GOLD_CUSTOMER_SALES_SUMMARY'[LIFETIME_SPEND])

-- Total Unique Orders placed by customers
Total_Orders_Count = SUM('GOLD_CUSTOMER_SALES_SUMMARY'[TOTAL_ORDERS])

-- Average Revenue Per User (ARPU)
ARPU = 
DIVIDE(
    [Total_Net_Revenue], 
    DISTINCTCOUNT('GOLD_CUSTOMER_SALES_SUMMARY'[CUSTOMER_ID]), 
    0
)

-- VIP Platinum Revenue (Highlighting high-value segments)
VIP_Platinum_Revenue = 
CALCULATE(
    [Total_Net_Revenue],
    'GOLD_CUSTOMER_SALES_SUMMARY'[CUSTOMER_SEGMENT] = "VIP Platinum"
)

-- VIP Platinum Revenue Share percentage
VIP_Platinum_Share = 
DIVIDE(
    [VIP_Platinum_Revenue], 
    [Total_Net_Revenue], 
    0
)

Production Note: 
This pipeline can be fully automated using Snowflake Tasks & Streams on a CRON schedule to ensure continuous ingestion before Power BI queries the Gold layer via DirectQuery.