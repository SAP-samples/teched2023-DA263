
Graphs can model different kinds of networks and linked data coming from many industries, such as logistics and transportation, utility networks, knowledge representation, and text processing.

A graph is made up of a set of **vertices** and a set of **edges**. Vertices are the *entities* in the graph while the edges describe the *relationships* between vertices. 

Each edge connects two vertices - one vertex is denoted as the source and the other as the target. Edges are always directed, but they can be oriented in either direction.

The SAP HANA Cloud Graph Engine applies the use of edges and vertices to analyze complex relationships in business data and processes to boost performance and business understanding. </br>
</br>
Key features include:</br>

- Graph data processing that enhances relational business data, enabling work analysis
- Built-in graph scripting language
- SAP-delivered graph algorithms that accelerate the development of new applications
- Direct integration into SQL layer and database procedures
- Pattern-matching support for openCypher query language
- Python client for data science

</br>

![](./Images/DBX_Graph/image002.png)

</br>

------
### Try it out!

</br>

Explore the SAP HANA Cloud Graph engine with the following exercise which uses the dataset from table **GX_REVIEWS** in the local **{placeholder|userid}** schema.

## Create a Graph Workspace

1. In the Database Explorer, expand the Catalog and select the object **Graph Workspaces**.

![](./Images/DBX_Graph/image003.png)


2. Use the Database Explorer SQL Console to create table constraints by running the following statements:

```sql
ALTER TABLE "GX_EDGES"
ADD FOREIGN KEY ("start") REFERENCES "GX_NODES" ("node_id") ON UPDATE CASCADE ON DELETE CASCADE ENFORCED VALIDATED;

ALTER TABLE "GX_EDGES"
ADD FOREIGN KEY ("end") REFERENCES "GX_NODES" ("node_id") ON UPDATE CASCADE ON DELETE CASCADE ENFORCED VALIDATED;
```

![](./Images/DBX_Graph/image004.png)

3. Now create the graph workspace **GX_DELIVERIES** using the following SQL. The graph will appear in the **Graph Workspaces** upon successful creation.

```sql
CREATE GRAPH WORKSPACE "GX_DELIVERIES" 
EDGE TABLE "GX_EDGES" 
	SOURCE COLUMN "start" 
	TARGET COLUMN "end" 
	KEY COLUMN "edge_id" 
VERTEX TABLE "GX_NODES" 
	KEY COLUMN "node_id" ;
```
</br>

![](./Images/DBX_Graph/image005.png)

Now explore the *GX_DELIVERIES* graph.

4. To explore the graph, select **GX_DELIVERIES** in the catalog object **Graph Workspaces**, then right-click on it and select **View Graph**.

![](./Images/DBX_Graph/image006.png)

5. The graph is displayed as below:

![](./Images/DBX_Graph/image007.png)

> **Note:** The **Vertices** (nodes) represent all delivery locations.

Analysis of the graph can be augmented by adding context such as labels and color schemes to highlight relationships.
The following steps will change the color of the Vertices (Nodes) for priority deliveries to red.

6. Click on the **Settings** icon in the top right corner to open a configuration menu for the graph. 

![](./Images/DBX_Graph/image007.1.png)

7. On the **Vertex** tab, under *Highlight Vertex*, set the following values to highlight the cities with priority delivery orders to red:

- Column: **priority**
- Value: **TRUE**
- Color: **Red**

8. Click on **Apply** to see the changes on the graph.

![](./Images/DBX_Graph/image008.png)


It's also possible to use this method to change the *Edge* settings on the graph.
Alter the Edge configuration to display the delivery method and highlight any **drone** deliveries to red also.

9. In the graph settings menu, select the **Edge** tab and make the following changes:

- Edge Label: **method**
- Column: **method**
- Value: **drone**
- Color: **Red**

![](./Images/DBX_Graph/image010.png)

10. Click on **Apply** and then **Close** to return to the graph.

![](./Images/DBX_Graph/image011.png)

</br>

> As can be seen on the graph, delivery method edges of type **drone** are now highlighted in red where applicable.

</br>


## Explore Graph algorithms

SAP HANA Cloud Graph provides a new graph calculation node that can be used in calculation scenarios.

This node executes one of the available actions on the given graph workspace and provides the result as table output.

Calculation scenarios can be created with plain SQL as shown in the following section, or with tools such as the SAP HANA Modeler or the native SAP HANA Cloud Graph Viewer.
</br>

------
### Try it out!

Calculate the shortest path from one point to another in the graph using the built-in **Shortest Path** algorithm.

1. Click on the **Algorithms** icon beside the settings icon to open the algorithms dialog menu.

2. In the Algorithm drop down option, select **Shortest Path**.

3. Enter the following values as the inputs to the algorithm:

- Start Vertex: **Miltenberg**
- End Vertex: **Nuremburg**
- Direction: **Outgoing**
- Weight Column: **length**

![](./Images/DBX_Graph/image012.png)

4. Click on apply to see the result:

![](./Images/DBX_Graph/image013.png)

<br>

Find all nodes which are related to a particular node with the **Neighborhood** algorithm. The parameters for this algorithm include depth of search, and whether to search for incoming or outgoing edges. Let's see what other nodes are related to the Mannheim vertex.

5. In the Algorithm drop-down box, select **Neighborhood**.

6. Enter the following values as inputs:

- Start Vertex: **Mannheim**
- Direction: **Incoming**
- Min Depth: **0**
- Max Depth: **1**


![](./Images/DBX_Graph/image014.png)

7. Click on **Apply** to see the resulting graph.

![](./Images/DBX_Graph/image015.png)

</br>

## Algorithms Using GraphScript

GraphScript is a high-level, powerful, domain-specific language. GraphScript eases the development and integration of complex graph algorithms.
</br>

------
### Try it out!

1. Call the **Nearest-Neighbor** built-in algorithm from GraphScript. In the SQL Console paste and execute following code which creates a procedure called **SP_NHOOD**:


```sql
CREATE TYPE "TT_NODES" AS TABLE ("node_id" NVARCHAR(50), "name" NVARCHAR(50));


CREATE OR REPLACE PROCEDURE "SP_NHOOD"(
	IN startV NVARCHAR(50),
	IN minDepth INTEGER,
	IN maxDepth INTEGER,
	OUT res "TT_NODES")
LANGUAGE GRAPH READS SQL DATA AS
BEGIN
  GRAPH g = Graph("GX_DELIVERIES");
  VERTEX v_s = Vertex(:g, :startV);
  MULTISET<VERTEX> ms_n = Neighbors(:g, :v_s, :minDepth, :maxDepth);
  res = SELECT :v."node_id", :v."name" FOREACH v IN :ms_n;
END;

```

![](./Images/DBX_Graph/image016.png)


2. Now call the procedure **SP_NHOOD** with different parameters to check results.

```sql
CALL "SP_NHOOD"('Berlin', 0, 2, ?);
```

![](./Images/DBX_Graph/image017.png)

</br>

Use GraphScript to create a custom traverse algorithm.

3. In the SQL Console paste and execute following code. It creates a procedure to calculate **Shortest-Path** distances to cities with high priority status.

```sql
CREATE TYPE "TT_PRIORITY" AS TABLE ("node_id" NVARCHAR(50), "distance" INTEGER, "hops" BIGINT);

CREATE OR REPLACE PROCEDURE "SP_PRIORITY"(IN startV NVARCHAR(50), OUT res "TT_PRIORITY")
LANGUAGE GRAPH READS SQL DATA AS
BEGIN
	GRAPH g = Graph("GX_DELIVERIES");
	VERTEX v_s = Vertex(:g, :startV);
	MULTISET<Vertex> rests = v IN Vertices(:g) WHERE :v."IN_SCOPE" == 1;
	ALTER g ADD TEMPORARY VERTEX ATTRIBUTE (INT "distance" = 0);
	ALTER g ADD TEMPORARY VERTEX ATTRIBUTE (BIGINT "hops" = 0L);
	FOREACH rest in :rests {
		VERTEX v_rest = Vertex(:g, :rest."node_id");
		WeightedPath<INT> p = Shortest_Path(:g, :v_s, :v_rest, (Edge conn) => INTEGER { return :conn."length"; } );
		rest."hops" = Length(:p);
		rest."distance" = Weight(:p);
	}
	res = SELECT :v."node_id", :v."distance", :v."hops" FOREACH v IN :rests;
END;

CALL SP_PRIORITY(STARTV => ?,RES => ?)

```

![](./Images/DBX_Graph/image018.png)

4. This code creates an **SP_PRIORITY** object in Procedures. Right-click on the procedure name and select **Generate CALL Statement With UI**.

![](./Images/DBX_Graph/image020.png)

5. The procedure's call statement is generated. Provide input value **Berlin** and click Run.

![](./Images/DBX_Graph/image021.png)

The result of the GraphScript is the shortest distance in meters to each city plus the number of hops to get there.


![](./Images/DBX_Graph/image022.png)


</br>


## Pattern Matching Using OpenCypher in SQL

Application builders can also apply pattern matching to their solutions. **OpenCypher** is a declarative graph query language for graph pattern matching developed by the OpenCypher Implementers Group.

Use OpenCypher directly within a SQL statement:</br>

1. Run the following query from the SQL console:

```sql
SELECT * FROM OPENCYPHER_TABLE( GRAPH WORKSPACE "GX_DELIVERIES" QUERY
    '
    MATCH (point1)
    WHERE point1.fragile = ''TRUE''
    RETURN point1.node_id as destination, point1.fragile as fragile_deliveries
    '
) ORDER BY "destination";
```

![](./Images/DBX_Graph/image023.png)

>**Note:** Only matched nodes with **'fragile' = 'TRUE'** have been selected.


2. Select only edges (or relationships in Cypher terminology) from a city with a high priority to a valid city.

```sql
SELECT * FROM OPENCYPHER_TABLE( GRAPH WORKSPACE GX_DELIVERIES QUERY
    '
	MATCH (point1)-[conn]->(point2)
	WHERE point1.IN_SCOPE = 1 AND point2.priority = ''TRUE''
	RETURN point1.node_id as delivery_start, conn.length as distance, conn.mode as delivery_type, point2.node_id as delivery_end
    '
) ORDER BY "delivery_start";
```

![](./Images/DBX_Graph/image024.png)


**Well done!** This completes the lesson on the SAP HANA Cloud Graph Engine. For further information on this topic, check out the following link.
</br>

- [SAP HANA Cloud, SAP HANA Database Graph](https://help.sap.com/docs/hana-cloud-database/sap-hana-cloud-sap-hana-database-graph-reference/sap-hana-cloud-sap-hana-database-graph-reference?locale=en-US)







