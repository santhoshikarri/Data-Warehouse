## 🗃️ Dataset Structure

This project uses structured CSV files originating from **CRM** and **ERP** systems. These datasets are ingested into a PostgreSQL-based data warehouse and transformed through Bronze, Silver, and Gold layers.

```
dataset/
├── source_crm
│   ├── cust_info.csv             # Customer information table         
│   ├── prd_info.csv              # Product information table
│   ├── sales_details.csv         # Sales information table
└── source_erp
    ├── cust_az12.csv             # Customer extra information table
    ├── erp_loc_a101.csv          # Location information table
    ├── erp_px_cat_g1v2.csv       # Product category information table
```
## Warehouse Data Architecture

![Warehouse Data Architecture](docs/warehouse/data_architecture.png)



## 🗄️ Tables

### ➡️ 1. Customer Information (`Customer Info`)

This table stores core customer master data from the CRM system.

| Column Name          | Data Type         | Description                         |
| -------------------- | ----------------- | ----------------------------------- |
| `cst_id`             | INT (Primary Key) | Unique identifier for each customer |
| `cst_key`            | VARCHAR(255)      | Business key for the customer       |
| `cst_firstname`      | VARCHAR(255)      | Customer first name                 |
| `cst_lastname`       | VARCHAR(255)      | Customer last name                  |
| `cst_marital_status` | VARCHAR(50)       | Marital status                      |
| `cst_gndr`           | VARCHAR(10)       | Gender                              |
| `cst_create_date`    | DATETIME          | Customer creation timestamp         |

---

### ➡️ 2. Product Information (`Product Info`)

This table stores product master data.

| Column Name    | Data Type         | Description                     |
| -------------- | ----------------- | ------------------------------- |
| `prd_id`       | INT (Primary Key) | Unique product identifier       |
| `prd_key`      | VARCHAR(255)      | Business key for product        |
| `prd_nm`       | VARCHAR(255)      | Product name                    |
| `prd_cost`     | INT               | Product cost                    |
| `prd_line`     | VARCHAR(255)      | Product line                    |
| `prd_start_dt` | DATE              | Product availability start date |
| `prd_end_dt`   | DATE              | Product availability end date   |

---

### ➡️ 3. Sales Details (`Sales Details`)

This fact table records individual sales transactions.

| Column Name    | Data Type         | Description                  |
| -------------- | ----------------- | ---------------------------- |
| `sls_ord_num`  | INT (Primary Key) | Sales order number           |
| `sls_prd_key`  | VARCHAR(255)      | Foreign key to Product Info  |
| `sls_cust_id`  | INT               | Foreign key to Customer Info |
| `sls_order_dt` | DATE              | Order date                   |
| `sls_ship_dt`  | DATE              | Shipment date                |
| `sls_due_dt`   | DATE              | Due date                     |
| `sls_sales`    | INT               | Total sales amount           |
| `sls_quantity` | INT               | Quantity sold                |
| `sls_price`    | INT               | Unit price                   |

---

### ➡️ 4. Customer Extra Details (`Customer Extra Details`)

Additional customer attributes sourced from ERP.

| Column Name | Data Type         | Description                  |
| ----------- | ----------------- | ---------------------------- |
| `CID`       | INT (Primary Key) | Foreign key to Customer Info |
| `BDATE`     | DATE              | Birth date                   |
| `GEN`       | VARCHAR(10)       | Gender (ERP source)          |

---

### ➡️ 5. Location Information (`Location`)

Customer geographical information.

| Column Name | Data Type         | Description                  |
| ----------- | ----------------- | ---------------------------- |
| `CID`       | INT (Primary Key) | Foreign key to Customer Info |
| `CNTRY`     | VARCHAR(255)      | Customer country             |

---

### ➡️ 6. Product Category (`Category`)

Product categorization reference table.

| Column Name   | Data Type         | Description      |
| ------------- | ----------------- | ---------------- |
| `ID`          | INT (Primary Key) | Category ID      |
| `CAT`         | VARCHAR(255)      | Category         |
| `SUBCAT`      | VARCHAR(255)      | Sub-category     |
| `MAINTENANCE` | VARCHAR(255)      | Maintenance flag |

---

## 🪴 Relationships

* `Sales Details.sls_prd_key` → `Product Info.prd_key` (Many-to-One)
* `Sales Details.sls_cust_id` → `Customer Info.cst_id` (Many-to-One)
* `Customer Extra Details.CID` → `Customer Info.cst_id` (One-to-One)
* `Location.CID` → `Customer Info.cst_id` (One-to-One)

---

## 🏗️ Data Warehouse Architecture

This project follows a **Medallion Architecture**:

* **Bronze Layer**: Raw data ingestion from CSV files
* **Silver Layer**: Cleaned and standardized datasets
* **Gold Layer**: Analytics-ready fact and dimension tables

Gold-layer objects include:

* `gold.fact_sales`
* `gold.dim_customers`
* `gold.dim_products`

---

## 📊 Analytics & Visualization (Metabase)

The Gold layer is connected to **Metabase**, an open-source BI and analytics platform that supports macOS.

### What was done in Metabase:

* Connected PostgreSQL data warehouse to Metabase
* Synced metadata and analyzed fields
* Used Gold-layer tables for analytics
* Built analytical questions and dashboards using:

  * Sales trends over time
  * Customer-level analysis
  * Product and category performance

### Why Metabase:

* Free and open-source
* Native PostgreSQL support
* Works on macOS without paid licenses
* Widely used for analytics engineering and BI prototyping

---

## 📟 Notes

* Business keys (`cst_key`, `prd_key`) improve integration across systems
* Some attributes may appear in both CRM and ERP sources due to real-world system overlaps
* Schema and data types are designed to support analytical workloads

---

##  Outcome

This project demonstrates an end-to-end **SQL Data Warehouse + BI workflow**, covering:

* Data ingestion
* Data modeling
* ETL using stored procedures
* Analytics-ready schema design
* BI integration using Metabase

This setup mirrors real-world analytics engineering practices used in production systems.
