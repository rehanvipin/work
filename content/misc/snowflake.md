---
weight: 6
title: "Snowflake"
# bookFlatSection: true
# bookToc: false
bookCollapseSection: false
---
# Snowflake, the enterprise DB (TM)
Not exactly. It's a cloud data platform apparently.

Why would you need it? Otherwise, an org can have so many DBs across departments,
with lots of data duplication and lesser opportunities for high level analysis across systems.
Also, it is cloud native and doesn't expose any hardware aspects to the users.

Apparently, the on-prem structure of past solutions were not scalable and were expensive.
Snowflake uses AWS, Azure, or GCP, and is a much more scalable solution,
with separate scaling for storage and compute. It has a pay-as-you-go model. (Like AWS billing).

It also has very good integration with many analytics software such as Power BI, Tableau.

## Architecture
Snowflake has three layers : 
* Cloud Service - For UI based actions. Also does metadata management, query optimization.
* Compute Layer - Query executor. Uses virtual warehouses (AKA warehouses) for compute. This has an inbuilt cache to cache query results if possible.
* Storage Layer - Stores the data in databases. Split into micropartitions for better reads. Supports structured and unstructured data. Data once loaded can only be accessed by SQL.

What's a virtual warehouse?
Compute resources for snowflake. It provides resources to run all the data loading / queries.
There are standard warehouses and snowpark variants (for resource-intensive processes).

### Streams & Tasks
A stream can be linked to a DB and it will track all the changes that happen in the DB.
A scheduled Task can once in a while pick up the list of changes in the stream, do any required transformations,
and merge (like a merge query) into the target table to make it up to date.

### Dynamic Tables
A new declarative way of defining transformation pipelines of data.
It can pick up data from target tables and use a query definition to prepare a target table.
It can auto-update based on the specified lag-time.

## Usage
Snowflake has a dashboard UI to manage accounts, data warehouses, databases, etc.
With it, you can create jupyter notebooks, worksheets (like notebooks but with SQL).
You can also create dashboards (simple interactive UIs with parameterized queries) or streamlit webapps.

It also has a CLI, snowSql for querying data from a DB.

You can also connect to it using JDBC / ODBC drivers or native connectors (e.g., for python).

You can load data in batches or have continuous load of data (from Kafa for example).

## Warehouse & Lake ?
What are these things and how are they different from a DB? Well these are OLAP systems unlike DBs which are mostly OLTP.
People use them to gather data from many sources and perform analytics on the large amount of data.

A warehouse typically has an ETL pipeline to load data into it after it has been processed. It mostly has structured data.

A lake typically stores the raw data, and may use the ELT methodology. It can store unstructured data in different forms.