
 
Project Overview
 
This project implements an end-to-end data engineering pipeline using Databricks, Amazon S3, and Delta Lake based on the Medallion Architecture (Bronze → Silver → Gold).
 
The pipeline ingests raw retail data from S3, performs data cleaning and transformation, and generates analytics-ready datasets for business insights.
 
---
 
Objectives
 
- Build a scalable data pipeline using Medallion Architecture
- Clean and standardize raw data
- Handle missing and inconsistent values
- Create fact and dimension tables
- Generate business KPIs for analysis
 
---
 
Architecture
 
S3 (Raw CSV Files)
        ↓
Bronze Layer (Raw Tables)
        ↓
Silver Layer (Cleaned & Transformed Data)
        ↓
Gold Layer (Analytics & KPIs)
 
---
Data Sources
 
File Name| Description
Customers.csv| Customer details
Products.csv| Product information
Sales.csv| Transaction data
Stores.csv| Store details
Exchange_Rate.csv| Currency conversion
Data_Dictionary.csv| Metadata
 
---
 
Bronze Layer (Raw Data)
 
Purpose
 
Store raw data from S3 without transformation.
 
Process
 
- Read CSV files from S3
- Rename columns (remove spaces)
- Store data in Delta tables
 
Sample Code
 
df = spark.read.format("csv") \
    .option("header", True) \
    .load("s3://electronics-retailer-bucket/bronze/Customers.csv")
 
df.write.format("delta") \
    .mode("overwrite") \
    .saveAsTable("01_prod_bronze.raw.customers")
 
---
 
Silver Layer (Data Cleaning & Transformation)
 
Purpose
 
Clean and standardize data for analysis.
 
Transformations
 
  Sales
 
- Handle NULL "delivery_date"
- Convert date formats
- Remove duplicates
- Fill missing "customerkey"
 
 Customers
 
- Fill missing "state", "state_code", "zip_code"
- Use window functions for data enrichment
- Standardize text (UPPERCASE)
 
 Products
 
- Clean price columns ("unit_cost_usd", "unit_price_usd")
- Remove special characters
- Standardize categories
 
Stores
 
- Fill missing "square_meters"
- Format "open_date"
 
  Exchange Rate
 
- Standardize currency codes
- Convert exchange rates to numeric
 
---
 
  Gold Layer (Analytics Layer)
 
  Purpose
 
Create business-ready datasets.
 
---
 
  Fact Table: "fact_sales"
 
Contains:
 
- Order details
- Revenue in USD
- Delivery days
- Sales channel (Online / Store)
 
---
 
  Dimension Tables
 
- "dim_customers"
- "dim_products"
- "dim_stores"
 
---
 
  KPIs Generated
 
  1. Monthly Revenue
 
- Total revenue per month
 
  2. Peak Month Analysis
 
- Top 3 revenue months
- Percentage contribution
 
  3. Category Revenue
 
- Revenue by product category
 
  4. Delivery Performance
 
- Average delivery time
 
  5. Online vs Store Sales
 
- Channel-wise comparison
 
 6. Customer Insights
 
- Average spend per customer
- Repeat customer rate
 
---
 
  Orchestration
 
- Implemented using Databricks Jobs
- Workflow:
 
Bronze → Silver → Gold
 
- Parameterized notebooks for reusability
- Automated execution
 
---
 
  Key Concepts Used
 
- Medallion Architecture
- Delta Lake
- Window Functions
- Data Cleaning Techniques
- Joins & Aggregations
- Schema Management
 
---
 
  Challenges & Solutions
 
  Missing State / Zip Code
 
  Solved using window functions and fallback mapping
 
  Missing Delivery Date
 
  Kept NULL for in-store purchases
 
  Schema Mismatch in Delta
 
  Resolved using "overwriteSchema" / "ALTER TABLE"
 
  Currency Conversion
 
  Joined with exchange rate table
 

 
