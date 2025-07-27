---
weight: 4
title: "Snowflake"
# bookFlatSection: true
# bookToc: false
bookCollapseSection: false
---
# Snowflake, the enterprise data warehouse
Not exactly. It's a cloud data platform apparently.

Why would you need it? Otherwise, an org can have so many DBs across departments,
with lots of data duplication and lesser opportunities for high level analysis across systems.
Also, it is cloud native and doesn't expose any hardware aspects to the users. Less SRE work.

Apparently, the on-prem structure of past solutions were not scalable and were expensive.
Snowflake uses AWS, Azure, or GCP, and is a much more scalable solution,
with separate scaling for storage and compute. It has a pay-as-you-go model. (Like AWS billing).

It also has very good integration with many analytics software such as Power BI, Tableau.

They have some courses to get started:
* [Level Up: First Concepts Series Track](https://learn.snowflake.com/en/pages/level-up-track/)
* [Level Up: Performance Series Track](https://learn.snowflake.com/en/pages/level-up-performance-track/)

## Architecture
Snowflake has three main layers : 
* Cloud Service - For UI based actions. Also does metadata management, query optimization, authentication etc.
* Compute Layer - Query executor. Uses virtual warehouses (AKA warehouses) for compute. This has an inbuilt cache to cache query results if possible.
* Storage Layer - Stores the data in one of the cloud storages. Stores data in a columnar format. Split into micropartitions (shards) for better reads. Supports structured and unstructured data. Data once loaded can only be accessed by SQL.

Users only interact with the cloud service layer. They do not interact with the compute / storage layers or the cloud providers.
In fact, snowflake can replicate data across cloud providers and regions for better availability.
Each cloud provider maintains availability within a region by having multiple zones.

Two more were added recently, to make it a "AI Data Cloud" :
* cortex AI - gives access to agents, LLMs, document analyzers which can help get more value from data.
* snowgrid - connects multiple cloud providers to provide fast global access to data.

The web UI is called snowsight.

What's a virtual warehouse?
Compute resources for snowflake. It provides resources to run all the data loading / queries.
They can be scaled horizontally and vertically.
There are standard warehouses and snowpark variants (for resource-intensive processes, through the use of cloud functions).

### Streams & Tasks
A stream can be linked to a DB and it will track all the changes that happen in the DB.
A scheduled Task can once in a while pick up the list of changes in the stream, do any required transformations,
and merge (like a merge query) into the target table to make it up to date.

### Dynamic Tables
A new declarative way of defining transformation pipelines of data.
It can pick up data from target tables and use a query definition to prepare a target table.
It can auto-update based on the specified lag-time.

### Stages
These are locations where data can be loaded before transforming and moving to a database table.
They can be either internal in snowflake, or external in one of the cloud providers (e.g., An AWS S3 Bucket).

There are two default internal stages : USER (one per user. accessible only by them) and TABLE (one per table, to load data into that table).
If there is a need for sharing data in the stages or loading into multiple tables, NAMED stages can be helpful.

They are also useful when exporting data from tables AKA Unloading. Once it is a stage, users can move it wherever (e.g., local disk).

### Iceberg Tables
If orgs don't want to store data in snowflake, they can store data externally in iceberg tables and still have it accessible within snowflakes.
It is based on the open-source Apache Iceberg table format.
The list of tables and their locations are stored in a catalog, which can be stored within snowflake or outside.

Using this means that orgs need to take responsibility for operations & maintainence of these tables.

Snowflake connects to these tables via "external volumes" which contain connection info.

Snowflake will charge for compute on these tables but not for storage.

## Usage
Snowflake has a dashboard UI to manage accounts, data warehouses, databases, etc.
With it, you can create jupyter notebooks, worksheets (like notebooks but with SQL).
You can also create dashboards (simple interactive UIs with parameterized queries) or streamlit webapps.

It also has a CLI, snowSql for querying data from a DB.

You can also connect to it using JDBC / ODBC drivers or native connectors (e.g., for python).

You can load data in batches or have continuous load of data (from Kafa for example).

### Data Loading
Data can be loaded into snowflake via a stage -> transform -> load process.
The staging can be internal or from an external cloud.
The moving and transformation is done by the `COPY INTO` command which understands multiple data formats and allows for transformations on them.
It takes data from a stage and puts it in a table.

Alternatively, if data is to be streamed into snowflake, snowpipe can be useful to take it from the stage to target using cloud functions instead of virtual warehouses.

Even more drastically, you can use "external tables" and not have data stored in snowflake at all.
It can be stored in some cloud provider's solution and have a reference passed to snowflake.
It is like a wrapper on an external stage, and hence are read-only.
Querying them can be slower, so it's recommended to use a materialized-view so data is not fetched for each query.

### Resource Monitors
Virtual Warehouse usage is paid for using credits. The cost is determined by the number of running warehouses, running time, resources used by each one.

To prevent unexpected credit usage, resource monitors can be set for warehouses.
One monitor can be set for multiple, but a VWH can have only one monitor at a time.
For each monitor, you can set the quota of credits available. These can send notifications, or suspend usage on breach of a threshold of the quota (e.g., 50%, 85%).

### Ecosystem
Snowflake has many inbuilt tools e.g., snowcli, snowSQL, JDBC & ODBC driver etc. to interact with the platform.

There are also many third-party tools which integrate well with snowflake e.g., fivetran for data movement, and powerBI.

Snowflake also worked with "partners" to provide solutions to work on snowflake data. e.g., dbt, informatica.
It's possible to use their services directly without creating a separate account, through "partner connect".

### Accounts
Accounts in snowflake are tied to cloud providers and have unique IDs. It's not a user account!

An account represents a single snowflake deployment. It contains users, roles, databases etc.

Access is managed through roles. Each object (e.g., warehouse, databse) is owned by one role, who can give different privileges to other roles.
Users / programs are granted roles. Roles can also be granted to other roles, creating a hierarchy.

### Object Hierarchy
Organization -> Account -> Database -> Schema -> Stage, Table, View. Each can have one or more of the next one e.g., an organizaiton is made of multiple accounts.

A DB is a *container* of schema *objects*, table *objects*, etc. It's just a logical grouping.

Snowflake has support for short-term and temporary tables so that the hierarchy isn't unecessarily polluted.

Objects can be replicated in other container containers, but they always belong to one container only.

### Backup & Recovery
Snowflake maintains snapshots of data upto 90 days ago for enterprise accounts. It's called "time-travel" and can be used to rollback any mistakes.
It can also be used to understand data modification patterns over time. Past data can be queried / cloned as well.

Fail-safe is an additional 7 days on top of time-travel to recover any lost data.

Zero-copy clones can be used to create instant copies of large datasets. Useful for creating testing environments.

To ensure better availability in case of issues, databases can be replicated across regions and clouds.

Failover can ensure that if the primary object goes down, traffic is routed to the secondary replica through a client-redirect (through the connection URL).
A failover group can specify which objects should be replicated and failed over, and the frequency of each replication.

## Warehouse & Lake ?
What are these things and how are they different from a DB? Well these are OLAP systems unlike DBs which are mostly OLTP.
People use them to gather data from many sources and perform analytics on the large amount of data.

A warehouse typically has an ETL pipeline to load data into it after it has been processed. It mostly has structured data.

A lake typically stores the raw data, and may use the ELT methodology. It can store unstructured data in different forms.