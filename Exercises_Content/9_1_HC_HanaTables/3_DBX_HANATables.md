The SAP HANA Cloud database stores data in memory using both column and row tables. Application builders can use column tables for almost every use case.

<!---Conceptually, a database table is a two dimensional data structure with cells organized in rows and columns. Computer memory however is organized as a linear sequence. For storing a table in linear memory, two options can be chosen as shown below. A row store stores a sequence of records that contains the fields of one row in the table. In a column store, the entries of a column are stored in contiguous memory locations.--->

![image](./Images/DBX_Tables/image01.png)


<!---In the SAP HANA Cloud database, tables that are organized in columns are optimized for high-performing read operations while still providing good performance for write operations. Efficient data compression is applied to save memory and speed up searches and calculations. Furthermore, some features of the SAP HANA Cloud database, such as partitioning, are available only for column tables. Column-based storage is typically suitable for big tables with bulk updates. However, update and insert performance is better on row tables. Row-based storage is typically suitable for small tables with frequent single updates.

The following outlines the criteria that can used to decide if data should be stored as column tables or row tables:

**Column Store:**
- Calculations are typically executed on individual or a small number of columns.
- The table is searched based on the values of a few columns.
- The table has a large number of columns.
- The table has a large number of rows and columnar operations are required (aggregate, scan, and so on).
- High compression rates can be achieved because the majority of the columns contain only a few distinct values.

**Row Store:**
- The application needs to process only one single record at one time (many selects and /or updates of single records).
- The application typically needs to access the complete record.
- The columns contain mainly distinct values so compression rate would be low.
- Neither aggregations nor fast searching are required.
- The table has a small number of rows (for example, configuration tables).
--->

## Advantages of Column-Based Storage

Column tables have several advantages:

- **Higher data compression rates** </br>
  In-memory columnar data storage natively utilizes compression to store data. Advanced compression techniques are available since all of the column’s data resides in memory versus on disk.

- **Higher performance for column operations such as searching and mathematical operations** </br>
  With columnar data organization, operations on single columns are as easy as iterating through an array with the added benefit of parallel operations and optimized storage designed for fast and efficient searches.

- **Elimination of the need for secondary indexes** </br>  
  In many cases, columnar data storage eliminates the need for additional index structures since storing data in columns already works like having a built-in index for each column. Eliminating secondary indexes reduces memory size, can improve write performance, and reduces development efforts.

- **Elimination of materialized aggregates** </br>
  The database uses the column store to aggregate large amounts of data with high performance and without the need for materialized aggregates. A leaner data architecture provides application builders many benefits including simplifying the data model, optimal code opportunities to accomplish business logic, and data that is always current.

- **Parallelization** </br>  
  Column-based storage also simplifies parallel execution using multiple processor cores. In a column store, data is already vertically partitioned. That means operations on different columns can easily be processed in parallel.

------
### Try it out!

<!-- Let's look at how to create Row and Column tables in HANA.
If you noticed from the SQL statements we ran in the *Getting Started* lesson, the syntax of creating a table in HANA is as follows: -->

1. Let's explore how to create Row and Column tables in SAP HANA Cloud. The syntax for creating a table in HANA is as follows:

```sql
CREATE TABLE <NAME_OF_TABLE>
```

2. By default, the SQL command creates a Column table. View the properties of a table by clicking on the table name. Filter on *GX_EMPLOYEES* and then click on **Properties** in the tab to the right.

![](./Images/DBX_Tables/image02.png)

<!-- Here you wil see more details about the table such as name, schema, size, etc.  -->

Upon clicking on **Properties** for the selected table, the additional details about the table appears including name, schema, size, and other relevant information.

3. Look for the **Table Type** property, and you will see it is listed as a **COLUMN** table.

![](./Images/DBX_Tables/image03.png)

4. Click on the **Runtime Information** tab to see the runtime details such as memory usage, disk size and record count.

![](./Images/DBX_Tables/image04.png)

To create a Row table, explicitly define the table type in the SQL statement.

5. Open a new SQL console session and paste in the following SQL which will create a Row type table called **GX_EMPLOYEES_ROW** and populate it with the same data as before:

```sql
CREATE ROW TABLE GX_EMPLOYEES_ROW as (SELECT * FROM "HC_DEV"."GX_EMPLOYEES");
```

6. The newly created table can now be seen in DB Explorer. Click on this table to open the details.

![](./Images/DBX_Tables/image05.png)

7. Now select the runtime tab and observe the *Memory Consumption* details. The amount of memory required for a row table is higher than with the same data residing in a column table.

![](./Images/DBX_Tables/image06.png)

Column tables are more efficient and support faster query processing than row tables. For further information on data storage in SAP HANA Cloud, click [here](https://help.sap.com/docs/HANA_SERVICE_CF/6a504812672d48ba865f4f4b268a881e/bd2e9b88bb571014b5b7a628fca2a132.html).

------
## Table Partitioning

The partitioning feature of the SAP HANA Cloud database splits column-store tables into smaller, more manageable parts.

Partitioning is transparent for SQL queries and data manipulation language (DML) statements. There are additional data definition statements (DDL) for partitioning itself:

- Create table partitions
- Re-partition tables
- Merge partitions to one table
- Add/delete partitions
- Perform the delta merge operation on certain partitions

</br>

When a table is partitioned, the split is done in such a way that each partition contains a different set of rows of the table. There are several alternatives available for specifying how the rows are assigned to the partitions of a table, for example, hash partitioning or partitioning by range.

The following are the typical advantages of partitioning:

**Large table sizes** </br>
Each partition as well as a table without partitions can store 2 billion records. Adding more partitions to a table configures the table for more capacity.

**Parallelization** </br>
Partitioning allows operations to be parallelized by using several execution threads for each table.

**Partition pruning** </br>
Queries are analyzed to determine whether or not they match the given partitioning specification of a table (static partition pruning) or match the content of specific columns in aging tables (dynamic partition pruning). If a match is found, it is possible to determine the specific partitions that hold the data being queried and avoid accessing and loading into memory partitions which are not required.

<!---**Improved performance of the delta merge operation** </br>
The performance of the delta merge operation depends on the size of the main index. If data is only being modified on some partitions, fewer partitions will need to be delta merged and therefore performance will be better.

**Explicit partition handling** </br>
Applications may actively control partitions, for example, by adding partitions to store the data for an upcoming month.--->

</br>

------
### Try it out!

Explore how to partition a table using practical examples. For demonstration purposes, use the **GX_EMPLOYEES** table, which contains a conveniently rounded number of 100,000 records. Note that any table can be partitioned.

Currently, the GX_EMPLOYEES table does not contain any partitions, and all 100,000 records are in a single table. Confirm by examining the metadata of the table in the SAP HANA Cloud Database Explorer. Upon inspection, observe that the Partition tab is blank, indicating the absence of any partitions in the table.

![](./Images/DBX_Tables/image07.png)

By clicking on the **Columns** tab, it can be seen that all columns are sitting on Partition ID = 0:

![](./Images/DBX_Tables/image08.png)

Now let's partition the table.

1. Open a new SQL console and paste in the following code:

```sql
ALTER TABLE GX_EMPLOYEES PARTITION BY HASH(EMPLOYEE_ID) PARTITIONS 3;
```

This will partition the *GX_EMPLOYEES* table into 3 partitions, via a *HASH* partition function on the *EMPLOYEE_ID* column.

2. Refresh the metadata screen for the table, and you should now see that GX_EMPLOYEES is now made up of 3 separate partitions, with about 33k records in each partition.

![](./Images/DBX_Tables/image09.png)

3. Run the following SQL query in the console and observe the result:

```sql
SELECT COUNT(*) FROM GX_EMPLOYEES;
```
<!-- 4. You should see that 100k records are returned as the result. Although the table is now in 3 separate partitions, you do not need to alter any queries on the table. -->

4. Upon querying the table, it can be observed that 100,000 records are returned as the result. Despite the fact that the table has been partitioned into three separate partitions, there is no need to make any alterations to the queries performed on the table. The partitioning is handled transparently by SAP HANA Cloud, ensuring that the query results remain consistent and accurate without requiring any modifications to the queries themselves.

5. We can merge all the partitions and return to one single table with the following sql:

```sql
ALTER TABLE GX_EMPLOYEES MERGE PARTITIONS;
```
</br>

Refresh the metadata screen for the table to see that it is a non-partitioned table again.

<!-- **Well done!** You should now have a good understanding of the management and partitioning of tables in HANA. -->


<!-- The default table type is column-type tables. If the table type in a CREATE TABLE statement is not explicitly stated then the table will automatically be a column table. It is possible to override this behavior by setting the configuration parameter default_table_type in the sql section of the indexserver.ini file. The default table type for temporary tables is row-type.

-------END OF ORIGINAL SECTION HERE ------------------->
</br>

------

## Data In Memory

SAP HANA Cloud is an 'In-Memory' database, meaning that it stores data in a computer’s main memory (RAM) instead of on traditional disks or solid-state drives (SSD). While most databases today have added more in-memory capabilities, they are still primarily a disk-based storage database. SAP HANA Cloud was built from the ground up to work with data in-memory and leverage other storage mechanisms as necessary to balance performance and cost. Retrieval from memory is much faster than from a disk or SSD, resulting in split-second response times.

Therefore, memory is a fundamental resource of the SAP HANA Cloud database. Understanding how the SAP HANA Cloud database requests, uses, and manages this resource is crucial to the understanding of SAP HANA Cloud.

## Overview of HANA Used Memory

The dominant part of the used memory in the SAP HANA Cloud database is the space used by data tables. Separate measurements are available for column-store tables and row-store tables.

![](./Images/DBX_TAbles/DBX_Image_HANAUsedMemoryCol.png)

<!---![](./Images/DBX_Image_HANAMemStore.png)--->

The column store is the part of the SAP HANA Cloud Database that manages data organized in columns in memory. This enables high data compression rates and faster aggregations. 

<!---The Row Store is optimized for high performance of concurrent read and write operations. Row store tables reside in main memory always.--->

The column store is optimized for both read and write operations. This is achieved through two data structures: Main storage and Delta storage.

![](./Images/DBX_Tables/DBX_Image_MainDelta.png)

<!--- ## Tables Involved

Local Tables:

* CUSTOMERS
* EMPLOYEES
* PRODUCTS
* SALES
--->
<!-- To show this, we will first create a copy of the **GX_EMPLOYEES** Table, with Delta Merge switched off initially. Then we will populate this table with data. -->

To demonstrate the concept, create a copy of the **GX_EMPLOYEES** table with Delta Merge initially disabled. This will allow us to examine the behavior of the table without the Delta Merge feature. Subsequently, the table will be populated with data for further analysis.


1. Click on the SQL icon in the top left corner of Database Explorer to open a new SQL Console.

![](./Images/DBX_Tables/image14.png)


2. Copy and paste the following SQL statement and execute it by clicking on the green **Run** icon or by pressing the **F8** function key.

```sql
CREATE COLUMN TABLE "LOCAL_EMPLOYEES" AS (SELECT * FROM GX_EMPLOYEES) NO AUTO MERGE;
```
</br>

![](./Images/DBX_Tables/image15_2.png)


<!-- >**Note:** When you create a table in HANA, Delta Merge is automatically enabled by default - you don't have to explicitly mention how the table should handle the auto merge process upon creation. We are just specifying 'No Auto Merge' in this situation for demonstration purposes. For further details on the delta merge process, click [here](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/bd9ac728bb57101482b2ebfe243dcd7a.html). -->

>**Note:** It is important to note that when creating a table in SAP HANA Cloud, the Delta Merge feature is automatically enabled by default. There is no need to explicitly specify how the table should handle the auto merge process during creation. However, in this situation, we are specifying "No Auto Merge" for demonstration purposes to showcase the behavior without Delta Merge. For further information on the delta merge process, click [here](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/bd9ac728bb57101482b2ebfe243dcd7a.html).

<br>

Now it is possible to check the details of this table by selecting it in the Catalog.

3. Expand **Catalog**, navigate to **Tables** and find the newly created table under the user schema.

>**Note:** Filter on schema - **{placeholder|userid}** - and the table name if necessary to find it more easily.

![](./Images/DBX_Tables/image10.png)

<br>

4. Select the **LOCAL_EMPLOYEES** table to see its meta data.

Note a list of all the table columns, and their data types:

![](./Images/DBX_Tables/image11.png)

5. Select the **Runtime Information** tab.

<!-- Here you will see further details about the table such as number of records, table size in memory and on disk, and other details about Partitions and Columns.
For now we are just concerned with the section on **Memory Consumption**. -->

In the table details section, users will find additional information about the table, including the number of records, table size in memory and on disk, as well as details about partitions and columns. Specifically, the **Memory Consumption** section provides insights relevant to our current focus.

In here, users can explore and analyze the memory consumption of the table, which is crucial for understanding the resource utilization and performance implications. By examining the memory consumption metrics, users can gain valuable insights into the table's memory footprint and make informed decisions regarding table management and optimization.


![](./Images/DBX_Tables/image12.png)

<br>

From here, the total memory size of the table, along with how much of that is currently in the Delta Store and how much is in the Main Store, can be seen. Observe the fact that all the data is currently in delta, and the size of the table in memory is over 12 MB.

>**Note:** As we created the table with *AUTO MERGE* switched off, all of the recently inserted data is currently residing in Delta. This is not the typical situation as tables in SAP HANA Cloud are created with *AUTO MERGE* enabled by default. This is just for the purposes of this guided experience, as the merge into main store would have already taken place by the time we opened the information in Database Explorer.

Now let's run some statements against this table and observe the results.

6. Copy and paste the following SQL query into an SQL console and execute it by clicking on the green **Run** icon or by pressing the **F8** function key.

```sql
SELECT * FROM "LOCAL_EMPLOYEES";
```
7. Once the results are returned, click on **Messages** to see the execution statistics.

![](./Images/DBX_Tables/image13.png)

![](./Images/DBX_Tables/image16.png)

Observe the main attributes of the query, such as *Elapsed Time*, *Prepare Time* and *Peak Memory consumed*.

8. Now run the statement again by pressing the **F8** key once more, and observe the differences in these attributes.

![](./Images/DBX_Tables/image16_Run2.png)

The time for statement preparation has reduced significantly, as this statement is now stored in the statement cache. Memory used has also been reduced by a large factor.

The next step is to perform a delta merge on the table and observe the results.

9. Copy and paste the following into an SQL console and execute it:

```sql
MERGE DELTA OF "LOCAL_EMPLOYEES";
```

10. Now return to the tab which has the meta data of the *LOCAL_EMPLOYEES* table open, and click refresh to observe the change in memory statistics.

![](./Images/DBX_Tables/image17.png)

The total memory consumption of the table has been reduced significantly, and the majority of the data is now stored in the Table's Main Storage area.

![](./Images/DBX_Tables/image17_merge.png)
 
Let's repeat the previous steps of running a *Select* SQL query against the table and observing the resulting statistics.

11. Copy and paste the following SQL query and execute it:

```sql
SELECT * FROM "LOCAL_EMPLOYEES";
```
</br>

12. Once the results are returned, click on **Messages** to see the execution statistics.

![](./Images/DBX_Tables/image16_Run3.png)

Note that the Execution time, Prepare time and Memory used in the query should all be quite similar to the 2nd execution above.

<!-- We can draw the following conclusions from this latest execution of the statement: -->

From the latest execution of the statement, the following conclusions can be drawn:

* By merging the table data from Delta store to Main store, the data becomes highly compressed.
* The statement read performance is not negatively impacted by this highly compressed state
* Writing to the table is optimized by utilizing the Delta store of the table which is a Row-based table.

</br>

## Data Loaded In-Memory

In regards to data loading, there are different states for a Column table in HANA:

* **Unloaded** - none of the column store data is loaded to main memory.
* **Partially Loaded** - parts of the column store data are loaded to main memory e.g. only a few columns recently used in a query.
* **Fully Loaded** - all data of the column store is loaded into main memory.

Normally, SAP HANA Cloud manages the loading and unloading of tables with the aim to keep all relevant data in memory.

Column tables are loaded on demand, column by column when they are first accessed. This means that columns that are never used will not be loaded and memory waste is avoided. When the SAP HANA Cloud database runs out of allocate-able memory, it will try to free up some memory by unloading unimportant data (such as caches) and even table columns that have not been used recently. 

Though usually not necessary, it is possible to manually load and unload tables or columns from memory.
Have a look at this in order to show the impact on performance.

1. Select the **LOCAL_EMPLOYEES** table from the Explorer window on the left to open its meta data.

2. In the **Runtime Information** tab, select **Columns** to see more details about the table columns.

![](./Images/DBX_Tables/image18a.png)


3. As already seen, running queries on this table and selecting all columns, will result in each column being loaded into memory (Loaded status = 'TRUE').

![](./Images/DBX_Tables/image18.png)

4. Run the following SQL query again just to observe the runtime:

```SQL
/* Columns loaded in memory */
SELECT * FROM "LOCAL_EMPLOYEES";
```

5. Observe a similar runtime to before.

![](./Images/DBX_Tables/image19.png)

6. Force the table to be unloaded from memory. Copy and paste the following SQL, then execute it in the SQL console:

```SQL
UNLOAD "LOCAL_EMPLOYEES";
```


7. Go back to the tab with the meta data for the table still open, click on **refresh** and observe the updated values. The Loaded column is now set to 'FALSE' for all columns of the table.

![](./Images/DBX_Tables/image20.png)


8. Now run the **`SELECT *`** query again and observe the run-times. Either return to the SQL console that was previously open, and re-run the statement, or copy and paste the following SQL into a new console and execute:

```SQL
/* Columns not loaded into memory */
SELECT * FROM "LOCAL_EMPLOYEES";
```

> **Note** that the statement execution time includes an initial load back into memory.

![](./Images/DBX_Tables/image21.png)


9. As the previous query used all columns, the table should be fully loaded into memory again. To check this, click on 'Refresh' in the table metadata tab and observe the values in the 'Loaded' column.

![](./Images/DBX_Tables/image22.png)

</br>

### Partially Loading Data

In the above examples, everything was selected from the table with a **```SELECT *```** query, thus SAP HANA Cloud will load the whole table into memory.
But what if we are only interested in a subset of columns in the table? In many database transactions, queries will typically be designed to select only the relevant data from the tables involved, as this results in better performance.

Let's observe what happens when a query is executed that only selects a few columns from the table.

1. Unload the entire table from memory as it is currently fully loaded. Execute the following query in the SQL console:

```SQL
UNLOAD "LOCAL_EMPLOYEES";
```

>**Note:** Double-check if the table has been unloaded by refreshing the table metadata screen as before, and verifying the *Loaded* column is set to *FALSE* for all columns.


2. Run the following query which only selects four columns from our table:

```sql
/* Select certain columns only - columns not yet in memory */
SELECT EMPLOYEE_ID, (EMPLOYEE_FIRSTNAME || ' ' || EMPLOYEE_LASTNAME) AS NAME, EMPLOYEE_SALARY
FROM "LOCAL_EMPLOYEES"
WHERE EMPLOYEE_SALARY > 50000;
```

3. After the query has finished, refresh the metadata tab for the table and check the **Loaded** column. Only the four columns used in the query are set to *TRUE*. This means all data that was not relevant for the query was left on disk, and *only the required data* was moved to main memory.

![](./Images/DBX_Tables/image23.png)

**Well done!** By now, you have acquired a solid understanding of in memory column tables and the advantages they offer. You now have experience creating these types of tables, exploring table properties, and effectively creating partitions for improved performance and data management.
