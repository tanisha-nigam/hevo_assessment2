This project focuses on cleaning and transforming messy e-commerce data that was ingested from PostgreSQL into Snowflake using Hevo. The raw data contains duplicates, inconsistent formats, missing values, inactive records, and orphan references.
The objective is to convert this raw data into a single, analytics-ready dataset using Snowflake SQL models without modifying the source tables.

Tech Stack Used

Source Database: PostgreSQL (Local)

Data Pipeline: Hevo (Logical Replication / CDC)

Data Warehouse: Snowflake (Trial Account)

Transformations: Snowflake SQL (SELECT-based models)

Version Control: GitHub

Raw Tables Ingested into Snowflake

The following tables were ingested as-is into Snowflake via Hevo:

customers_raw

orders_raw

products_raw

country_dim

These tables intentionally contain data quality issues and were not modified directly.

Data Cleaning & Transformation Approach

All transformations were implemented using SELECT-only SQL queries, ensuring:

Raw data immutability

Clear lineage

Easy validation and debugging

Step-by-Step Transformations

1. Deduplicate Customers

Goals

Keep only the most recent record per customer_id

Standardize emails to lowercase

Normalize phone numbers to 10 digits or mark as "Unknown"

Logic

Used ROW_NUMBER() over customer_id ordered by updated_at DESC

Retained only the latest record

Cleaned email and phone formats during selection

2. Fix Nulls & Country Code Issues

Goals

Standardize country values using country_dim

Handle variations like usa, UnitedStates, IND, SINGAPORE

Replace created_at nulls with 1900-01-01

Logic

Normalized country values using string cleanup (UPPER, REPLACE)

Joined with country_dim to derive ISO codes

Used COALESCE for default timestamps

3. Clean Orders Data

Goals

Remove exact duplicate orders

Handle invalid amounts:

Negative → 0

Null → fallback value

Standardize currency codes

Derive amount_usd

Logic

Used DISTINCT to remove duplicates

Applied conditional logic for amount correction

Standardized currencies using UPPER()

Converted amounts into USD using predefined conversion rates

4. Clean Products Data

Goals

Properly capitalize product names

Standardize category names (Title Case)

Mark inactive products clearly

Logic

Used string formatting functions for name standardization

Replaced inactive products (active_flag = 'N') with "Discontinued Product"

5. Final Unified Dataset

Goals

Join customers, orders, and products

Preserve orphan records

Apply meaningful placeholders

Rules Applied

Missing customer → "Orphan Customer"

Missing product → "Unknown Product"

Orders with invalid references are retained

Mixed currencies handled consistently

Edge Case Handling

Customers with all fields null → "Invalid Customer"

Orders referencing missing customers/products are still included

No records were dropped silently

Validation Strategy

Each transformation was verified using:

Row count comparisons

Null checks

Duplicate detection queries

Sample record inspection

This ensured correctness at every stage.



