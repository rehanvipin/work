---
weight: 1
title: "Fundamentals"
# bookFlatSection: true
# bookToc: false
---

# Fundamentals of DBs

A database is a collection of data. A file is also a collection of data. This is why a DB is better:

- They provide a central location for data that's used by many apps. This reduces repetition which also reduces data inconsistencies between apps.
- Fine grained security controls over the data. Can have controls over each row, column, fields etc.
- Data integrity since they can verify if the data makes sense using constraints and types. A file will accept any data and the responsibility of checking goes onto the readers / writers.
- Better concurrent access. Can lock single rows etc.
- Performance of data management & analysis operations (through SQL & optimized queries)

There are many types of DBs for different types of data models - tree, graph, relational (tables), object (apparently this is pretty powerful but potential hasn't been realized yet), and object relational (many products use this).

Relational DBs can hold many types of data but may not be as optimized for particular use cases as other specific ones. It builds relations on the fly instead of predefining them (what does this mean?).

In an OO DB the connection between objects and even the methods of the objects are preserved. This should allow fast traversal of the DB but the performance hasn't been great in real-world products.

Quality of data is important since it impacts the decision making process.

Although multiple sources of data can lead to inconsistencies it is required in situations like - different views of same data, proximity for performance, new demands from data.

Data supply chain - there are data producers (produce or modify data) and downstream consumers, and possibly multiple of them to make a chain. An error in the chain affects downstream and the exact source could be difficult to find.

An authoritative source of data is the only place where a read/write instance of the data exists. It makes more sense to have an AS for reference data than operational data.

Database design: Real world can be modelled in different levels of abstraction:

- Conceptual - only entities, attributes (optional), and relationships (only arrows and cardinality, doesn't specify which foreign key etc). It doesn't mention anything about what data is stored and how it is stored. Data entities as seen by business. Even if the underlying data is the same, each business could have a different conceptual model.
- Logical - A collation of all different conceptual models + detailed structure of each entity. No attributes should be left out. Should be independent of technology. No many-many relationships should exist at this stage (because they can only be implemented as 1:M & M:1 relationships).
- Physical - The actual data that will be stored on the databases. Needs to specify types of attributes, table names, indices, constraints etc.

In academic definition, there is a relation (basically table), tuple (row), and attribute (column).

OLAP systems - many short on-line transactions. Attention given to data integrity during concurrent access.

OLTP systems - lower volumes of transactions over large datasets.

Transactions have A(atomic) C(consistent) I(isolation) D(durability - results of transaction should not be lost) properties. Objects in DB - table, view, rule, index, stored procedure, trigger.

Logical database design captures all information needs of the system. It captures all conceptual views of the system into a model that can serve them all. The process of this design is - identify entities, attributes, keys, relationships, normalize, repeat. The information requirements from the business should be clear.

Keys - candidate keys (can uniquely identify a tuple in a relation), one of them chosen to be primary key, else an artificial key used (created by system). This could be composite.

Relationships - occur when data in one table can be linked to data in other (via a foreign key). An FK could be composite. Entity / Relationship diagrams could be drawn either via Chen's (attributes are bubbles, relations **represented** via diamonds) or Crow's notation (more concise, just lines, attributes are in entity box).

Types of relationships:

1:1 - every row in table A matches a row in table B (and vice versa). This could technically be one single table but split for performance reasons (verticle partitioning)
1:N - these two cannot be made into one table since that would cause data duplication
M:N - perfectly normal, to be implemented they would need to be split into 1:M & 1:N with an extra "realtionshpi" table which has foreign keys of both.

Normalize? Splitting tables . Why normalize? Remove data redundancy & maintain consistency. Levels:

1NF - each attribute must hold only single value ,not arrays
2NF - (first what is a function dependency? A -> B means for any rows that have same value for A, they should have same value for B, where A & B could be composite of **attributse**). No non-key column can be FD on part of the primary-key.
3NF - no non-key column can be FD on any other non-key column
BCNF - no FDs between candidate keys

What types of integrity should a relational model have? Domain (vals of **attributs** should be according to the **attr** type), entity (all of **em** should have PK), **referentaila** (all FKs should have corresponding PK **col**)

Logical design maps conceptual design onto a particular model (e.g., relational, OO, etc) whereas physical maps **logical** onto a particular DBMS and system. This requires knowledge of what kind of transactions happen usually and **how data** will be used (CRUD analysis).

Physical design may require denormalization for performance benefits (it should be done last, after all other design **stpes** e.g., precalculating and storing data).

Physical design also involves thinking about data partitioning. **Horizonatal** which stores some tuples in different **partitions** and vertical which stores some attributes in different partitions (by splitting table into two tables related by a 1:1 relationship).

Teradata is a distributed & scalable warehousing platform designed to run on commodity hardware. It splits processing of data and management of data into two separate processors. It's an RDBMS. Hadoop is a project of distributed computing.

Noxal typically refers to non-relational data stores. Some common models:

Columnar - easy to add extra attributes, efficient aggregation. Kdb+, greenplum, teradata.
Graph - nodes represents objects (from OOP). Neo4j
- Document - basic unit is a document (could be a file of XML, JSON file format). Couchdb, monogdb
Key-value - no particular schema / structure. Mongodb, apache jena

Data changing over time:

Some systems need to record changes in data over time, not just present state of data. Can't delete old data. Storing this data allows for some good analytics. Sometimes it needs to store two pieces of date information, the time since when the data was valid and the time since when the system got to know about the data. Since it has two pieces of time data it is called bitemporal system. It might also be required for the sytem to store the time since it believes the data is not accurate anymore. The **timeperiod** during which it knows the data correctly for a particular past data is known as the transaction time. Bitemporal systems represent two temporal aspects - valid timeline and transaction timeline. This is useful fro regulatory reporting & risk mgmt.

## SQL

SQL has a standard version by ANSI but most vendors add their own extensions too, since there are multiple versions of the standard, not all vendor SQL (even though standards compliant) aren't the same. DB2's extended version of SQL is known as SQL-PL whereas Sybase has Transact-SQL. The basic order & structure of SQL queries is like so:

1. Select // avoid * because if the structure of the table changes, this may not give what you expect. You can also do computations on rows or even not select any attribute from the row and just have expressions e.g., select 'x', 123. you can also have a case (which is like if-elseif-else of other langs) for a field ; case when expr then expr when ... end
2. From
3. Where // condition must be boolean expression, cannot use aliased names here. Cool conditions are 'between', 'in', 'like', etc.
4. Group by
5. Having
6. Order by // sort by column name, alias or even position in select. Can also sort on fields not in select

Distinct can be used in select to make sure all rows are unique. You could also use 'unique' but that's the old way of doing it.

Note that the entire query is processed on the server and only the final resulting rows are passed to the client.

NULL with any calculation results in NULL. Order of NULL wrt other values is not defined. How to deal with NULL? There is '<field> is [not] NULL', nullif(field, 'somevalue') which will return null if the row's field is 'somevalue', coalesce(field, 'valuetoreturniffieldisnull').</field>

Type conversions. Some simple ones are done implicitly. Db2 & ASE now have cast(expression AS dataType) function to convert between types.

User defined types in DB2 - distinct types (just alias for existing types) e.g., create distinct type ifiklol as integer with comparisons (the with comparisons part is imp so that values of the new type can be comapred), structured types, and referenced types.

Fun fact - sybase allows java objects to be stored in the DB.

DBMSs have many inbuilt functions (e.g., CAST which are ANSI standard) and some of them even have support for user-defined functions. Type of functions in DB2 - scalar(take in indivlaul scalar values and return one scalar value),

aggregate(return one scalar value per row-group), row, table(returns a table to the caller, can only be used in 'from').


There are essentially two count functions - `count([all|distinct] expr)` and there's `count(*)` which returns number of rows.

`having` is like `where` other than the fact that it is applied on the result of the aggregate whereas `where` is applied before the aggregate operation is done.

Group by allows for vector aggregates whereas if you omit it you get scalar aggregates.

### Joins:

- Inner join - what you expect (you can omit the `inner` word and it's the same as inner join)
- Left outer join, right outer join, full outer join - all what you expect
- Cross join (aka cartesian product) - every row of first table is matched with every other row of other table. Syntax is `select * from table1 cross join table2`

Join done as part of `from` clause.

If db has been modelled correctly, every table should have a primary key. In case of a multiple join, the DBMS may perform the join in any order it wishes. The joined table can be operated on like any other table, you can filter it with the `where` clause etc. If you want to apply a filter on one of the joining tables, add the filtering condition to the `on` with `and`. A DBMS can choose one of many join strategies to use for the join e.g., nested iteration (common), merge join (efficient on sorted rows / tables with indices), hash join.

### Subqueries:

Some of them can just be expressed as joins, in fact the query optimizer will convert them to join if required. You can use ANY / ALL also with the results of a single column subquery (which might seem similar to MIN / MAX except that minmax ignore NULL values). It's also pretty useful to use `exists` with them. Subqueries can appear in select, where, having, from etc. There are correlated queries (inner query references data from outer query) where the processor will run both queries together. Db2 can do set operations like union, intersect, except (all of them have options to retain duplicate rows) but both tables should have compatible types in the same order.

### Views
Views are just queries really. When you use a view, the SQL engine replaces it with the query that defines the view. If the tables referenced in the view are deleted / structure modified so view doesn't make sense, the engine will throw an error. There are restrictions on the queries that can be classified as views, they can't have `order by` or `distinct`. It is possible to have a view that updates a table (but only one!). You create one like so: `create view lolview as selectStatement` or even `create view lolview (nameForCol1, nameForCol2 ...) as selectStatement1 union selectStatement2 union selectStatement3`. Why views? They can help simplify queries. One major point for using them is that since they are replaced with their query, they can make use of current global variables (e.g., username, schema, etc) for whatever reason - maybe it should display different data to different users. Anyways, nowadays views have been supersed by (it is now recommended to use) Common Table Expressions :
It's a DB2 concept. Can be created like so: `with CTEName as (selectStmt)`. This actually creates a temporary result table with the results of the query, this can then be used by other queries. The select statement in here can contain any valid SQL. It could even be a recursive query. This is more flexible than views. Db2 has the option to create global temporary tables which are stored in the DB and can be used many times whereas CTE can only be used in the scope they are defined. Temporary tables can be indexed and have constraints (unlike CTEs). A temp table based on the result of a query is known as an MQT (materialized query table). Syntax: `create summary table idkwhat as (selectStmt)`.
CTEs aren't **greaty** for performance, if it creates an issue, go with MQTs.

Inserting data: can do value wise or even insert into a table from some other table using insert .. select. To update table you do `update tbName set idkWhat = idkWhat + `nice stuff` where someVal < 20`. Db2 has a fun `merge into someTab using selectStmt when matched then updateStmt when not matched insertStmt`. ASE has the ability to create a table from a query like so: `select ... into ...`

### Creating tables:

It's possible to create them under a tablespace of your choice. A table while creation needs attributes and constraints (if they are there). The attributes can be of user defined types as well. A structured UDT can be made with `create type typeName as () mode db2sql`. Possible to add / alter / drop columns / constraints with `modify` command. Get info of table with `describe table tbName` and of tables with `list tables for schema schemaName`. Don't have to always provide data for column while adding row, if it is a generated column e.g., col3 double generated always as (col4*0.5). If you say `generated always as identity` it will autoincrement while `generated by default as` menas although it will autoincrement, you can specify a value if you want. What if you want a global value which you can increment? Create a `sequence`.


### Procedures:

A stored procedure is basically a block of SQL. You can pass it input & output params like C#. It doesn't have a return value, you need to call with `CALL`. Then threre are also functions, which can return values and be used anywhere, the function can operate on a scalar value, row or even a table. Then there are functions which are part of structured types, which are called methods. A procedure could also be gotten from code written in other languages, this is called external routines.

A trigger can execute code before / after certain action.

### Transactions:

While using a DB from multiple places, it's possible to have race conditions and so on. To prevent these, write code within transactions. A transaction has an isolation level meaning what kind of data anomalies it prevents and what it lets through. The common isolation levels are Uncomitted Read, Cursor Stability (the default), Read Stability, Repeatable Read. Also possible to lock tables & rows explicitly. To get more info about the current running app in DB2, do `list applications show detail`.


Why use an index? Improve read performance on queries.

How does it improve performance? Reduces number of rows to search by ordering some data from them and using binary search.

What exactly is an index? First, pick some index keys (i.e., columns or some calculated value involving some columns, these pre-calculated ones can actually be very efficient since the query can make use of the calculated result and not have to do them on its own). Then create a BTree (balanced search tree) where the comparison to place data is done on each index key, first to last (threfore, the order of the index keys is important). The data is stored on the leaf nodes, and the leaf nodes are connected through links as a doubly linked list. What exact data is stored in the element? The index keys for that row of the table and a rowID which is like a reference to the row in the table.

The DBMS may choose to use the index in these ways for a query:

- KEY -> use the index to find the required row
- RANGE -> use the index to get a range of rows
- INDEX ONLY -> not really using the index tree, but it traverses the leaf level linked list to find the required rows, this is faster than going through the table because the index element likely only contains a subset of elements the table rows has.
- ALL -> doesn't use the index at all and searches through all rows of the table

An index isn't a silver bullet. If not used correctly, or forcefully used, it might make the query slower (due to disk IO, as the index doesn't have all data and will need to fetch rows one by one from the table on disk as required). Always design an index based on the queries that are being run.

B+Tree has the doubly linked list between the leaf nodes while a BTree doesn't. A query could make use of multiple indexes that exist in the system to optimize, ofc, it would be more efficient to make a single index that satisfies the needs of that query. What's the cost of indexes? RAM and disk usage. Also, insert and updates can get slower since the tree will have to rebalance itself.

An index will have an entry for every row in the table. What if you don't want that? What if you're interested in only certain rows? Put a `where` clause while making the index and it create an index for only the filtered rows. This index will now only be used for any queries that operate on the same subset of rows.

Fun fact: indexes can also be implemented by other data structures than Btrees e.g., hash table, GIN trees, BRIN trees (which all have their own use cases).

How to figure out the query execution plan and what indexes the query might use? Do `explain select ...` or `explain analyze select ...`

The indexes we talk about above are known as non-clustered index, they have references to the rows in the table. Note that in these indexes, it is also possible to store some columns in the index but not have them part of the index key. Only the leaf nodes will have data of these columns.

A clustered index is one where the leaf nodes are the actual pages of rows of the table itself. The table's rows have to be ordered acc to the index key for this to work. Since the table structure dependens on this index, there can only be one clustered index per table (this is usually the one with the primary key as the index key).

When a table doesn't have a clustered index, it is called a heap because data isn't stored in any particular order and inserts / deletions don't happen wherever there is free space.

Extra fun fact: when the table is a heap, the row reference for a non-clustered index is a pointer to the row, whereas when the table has a clustered index, the row reference for the non-clustered index is the index key to the row in the clustered index.

It's a little difficult to decide if the clustered index should be changed or a non-clustered index should be created. A guideline is that the clustered index should be something that's used by many important queries which might return lots of rows, for most other cases (like for just a particular query), use a non-clustered index and check performance.