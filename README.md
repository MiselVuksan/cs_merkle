# Data Engineering Case Study: Medallion Architecture with PySpark

This repository contains a complete end-to-end data engineering pipeline implementing a **Medallion Architecture** (Bronze, Silver, Gold layers) using PySpark and Databricks. The project transforms raw CSV data from an S3 bucket into optimized Delta tables and a final Gold-layer data mart for business insights.

## Project Structure

As shown in the repository layout:

* **`notebooks/`**: Contains the primary assignment notebook detailing the full ETL flow.
* **`src/`**: Houses core transformation logic and modular code for checks and tests.
* **`open_questions.md`**: Strategic analysis of industrialization, dbt-core migration, and architectural scaling.
* **`README.md`**: Project overview and documentation.
