# SAP HANA Cloud Document Store

SAP HANA Cloud Document Store provides the ability to manage complex business data in its native structure with full flexibility and simplified integration options. 

Some of the features include:

- Native support for JSON documents
- Full schema flexibility
- Dedicated storage with the processing power of an in-memory database
- Support for operations on JSON documents through SQL
- Integration with the data structures of common programming languages

![](./Images/050_Intro_Concept.png)

------
### Try it out! 

Switch to the Database Explorer to work through the following exercises which use the dataset from table **GX_REVIEW**

1. Select the catalog object **JSON Collections** to display the list of Collections.

2. Right-click on the collection **GX_REVIEW**.

3. Click on **Generate SELECT Statement**.

>**Note:** SAP HANA Cloud uses the same SQL to access collections as column and row tables.

![Start](./Images/100_DBX_Start.png)

4. Execute the provided statement in the resulting SQL console.

![Result](./Images/110_GX_REVIEW.png)

5. Add a filter clause to the query by running the following statement.

```sql
SELECT * FROM GX_REVIEW 
 WHERE REVIEW_ID='R_00012';
 ```

6. The result is in a JSON string:

![Filtered](./Images/120_REVIEW_filtered.png)

7. Specify each of the columns in the SQL statement to see the result in a relational format

```sql
SELECT 
    REVIEW_ID,
    CUSTOMER_ID,
    PRODUCT_ID,
    REVIEW_RATING,
    REVIEW_TEXT 
FROM 
    GX_REVIEW 
WHERE 
    REVIEW_ID='R_00012';
```

![](./Images/125_REVIEW_select.png)

8. Insert a new JSON Object to the collection.

```sql
INSERT INTO "GX_REVIEW" 
       VALUES('{"REVIEW_ID":"R_100123",
       "CUSTOMER_ID":"C_000000205",
       "PRODUCT_ID":"P_0046",
       "REVIEW_RATING":5,
       "REVIEW_TEXT":"Absolutely perfect"}'
       );
```

![insert](./Images/130_REVIEW_insert.png)


9. Query the collection to look for the new entry.

```sql
SELECT * FROM GX_REVIEW WHERE REVIEW_ID='R_100123';
```

![ins_select](./Images/135_REVIEW_insert_select.png)

10. Update the inserted entry and query the entry.

```sql
UPDATE GX_REVIEW 
    SET REVIEW_TEXT='Hello again' 
        WHERE REVIEW_ID='R_100123';

SELECT * FROM GX_REVIEW 
    WHERE REVIEW_ID='R_100123';
```

![update](./Images/140_REVIEW_update.png)

11. Aggregate the JSON Objects

```sql
SELECT 
    PRODUCT_ID,
    AVG(TO_BIGINT(REVIEW_RATING)),
    Count(PRODUCT_ID)
FROM 
    GX_REVIEW 
GROUP BY 
    PRODUCT_ID 
ORDER BY 
    PRODUCT_ID ASC;
```

![Aggregate](./Images/150_REVIEW_aggregate.png)

12. The JSON data in the document store can easily combine with business data.
   
```sql
SELECT 
       PID,
       PRODUCT_NAME,
       AVGRATING 
FROM DOCSTOREVIEW 
        INNER JOIN "GX_PRODUCTS" ON DOCSTOREVIEW.PID = PRODUCT_ID;
```
There is a view called "DOCSTOREVIEW" that encapsulated the access to the Collection
![](./Images/160_REVIEW_view.png)


13. Finally, join Reviews with Customer and Product data to add more context:

You will find the view **DocStoreView_Base** in the HDI project
```sql
SELECT 
    PID,
    P.PRODUCT_NAME, 
    CUSTOMER_ID,
    RATING 
FROM DOCSTOREVIEW_BASE
        INNER JOIN GX_PRODUCTS AS P ON DOCSTOREVIEW_BASE.PID = P.PRODUCT_ID;
```

![Adding cust_ID](./Images/175_REVIEW_CUST_ID.png)

14. Now add in the customer details and review text:

```sql
Select 
    distinct PR.PID,
    PR.CUSTOMER_FIRSTNAME,
    GP.PRODUCT_NAME, 
    PR.RATING, 
    PR.REVIEW_TEXT 
from (
   select * 
       from    DOCSTOREVIEW_BASE RE, 
               GX_CUSTOMERS GC
                  where RE.CUSTOMER_ID = GC.CUSTOMER_ID) PR, 
               GX_PRODUCTS GP
                  where PR.PID = GP.PRODUCT_ID
;
```

![All together](./Images/180_REVIEW_CUST.png)

**Further Reading**
For further information on this topic, check out the following link:</br>

- [SAP HANA Cloud, SAP HANA Database JSON Document Store Guide](https://help.sap.com/docs/HANA_CLOUD_DATABASE/f2d68919a1ad437fac08cc7d1584ff56/dca379e9c94940e998d9d4b5c656d1bd.html)

**Well done!!** This completes the lesson on the SAP HANA Cloud Document Store.

- Continue to - [Exercise 5 - MULTI-MODEL--GRAPH](../9_5_HC_Graph/8_DBX_Graph.md)
- Continue to - [Main page](../../README.md)