# Comprehensive Guide to SQL Functions and Commands in Big Query
Below, I'll explain `UNNEST` in detail and introduce you to several other powerful BigQuery functions and commands that are essential for data manipulation and querying.

---

## 1. UNNEST

### What is UNNEST?
`UNNEST` is a BigQuery function that explodes an array into a set of rows. It's particularly useful when dealing with nested or repeated fields in your data. By using `UNNEST`, you can flatten these structures, making it easier to perform operations like joins, filters, and aggregations.

### Example Usage
Suppose you have a table `orders` with the following structure:

| order_id | items                     |
|----------|---------------------------|
| 1        | ["apple", "banana"]       |
| 2        | ["orange", "grape", "kiwi"] |

To list each item individually with its corresponding `order_id`:

```sql
SELECT
  order_id,
  item
FROM
  orders,
  UNNEST(items) AS item

Result:
order_id	item
1	apple
1	banana
2	orange
2	grape
2	kiwi

```
## 2. GENERATE_ARRAY

**What is GENERATE_ARRAY?**  
GENERATE_ARRAY creates an array of integers (or other numeric types) within a specified range. It's useful for generating sequences without recursion.

### Example Usage
Generate numbers from 1 to 10:
```sql
SELECT number
FROM UNNEST(GENERATE_ARRAY(1, 10)) AS number

Result:

number
1
2
...
10
```
## 3. GENERATE_DATE_ARRAY

**What is `GENERATE_DATE_ARRAY`?**  
Similar to `GENERATE_ARRAY`, but specifically for generating arrays of dates within a specified range.

---

### Example Usage

**Generate dates for January 2025:**

```sql
SELECT date
FROM UNNEST(GENERATE_DATE_ARRAY('2025-01-01', '2025-01-31')) AS date;

Result:

date
2025-01-01
2025-01-02
...
2025-01-31
```
## 4. ARRAY_AGG

**What is `ARRAY_AGG`?**  
`ARRAY_AGG` aggregates values into an array. It's useful for collecting multiple row values into a single array.

---

### Example Usage

Aggregate items per `order_id`:

```sql
SELECT
  order_id,
  ARRAY_AGG(item) AS items
FROM orders,
  UNNEST(items) AS item
GROUP BY order_id;

Result:

order_id | items
1        | ["apple", "banana"]
2        | ["orange", "grape", "kiwi"]
```
## 5. STRUCT

**What is `STRUCT`?**  
`STRUCT` allows you to create a complex data type that groups together multiple fields. It's useful for handling nested data.

---

### Example Usage

Create a structured field for a user's name:

```sql
SELECT
  STRUCT(first_name, last_name) AS full_name
FROM
  users

Result:

full_name
{ first_name: "John", last_name: "Doe" }
{ first_name: "Jane", last_name: "Smith" }
```
## 6. ARRAY

**What is `ARRAY`?**  
`ARRAY` is used to define an array of values. It can be used to create arrays from literals or query results.

---

### Example Usage

Create an array of specific numbers:

```sql
SELECT ARRAY<INT64>[1, 2, 3, 4, 5] AS numbers

numbers
[1, 2, 3, 4, 5]
```
## 7. LATERAL FLATTEN (Not in BigQuery, but Similar Concepts)

While BigQuery uses `UNNEST`, other SQL dialects (like Snowflake) use `LATERAL FLATTEN` to achieve similar results. It's good to be aware of these differences if you work across multiple SQL platforms.

---

## 8\. JSON Functions

BigQuery offers several JSON functions to parse and manipulate JSON data, which often contains nested structures.

### Key JSON Functions:

**`JSON\_EXTRACT / JSON\_EXTRACT\_SCALAR`**: Extracts JSON values.  
  
```sql 
SELECT JSON\_EXTRACT(json\_column, '$.field') AS field

FROM your\_table
```

**JSON\_QUERY**: Similar to JSON\_EXTRACT but can extract JSON objects or arrays.  
  
```sql
SELECT JSON\_QUERY(json\_column, '$.nested\_object') AS nested\_object

FROM your\_table
```

**JSON\_VALUE**: Extracts a scalar value from JSON.  
  
```sql
SELECT JSON\_VALUE(json\_column, '$.field') AS field
FROM your\_table
```

## 9\. WINDOW FUNCTIONS

Window functions perform calculations across a set of table rows related to the current row. They are powerful for tasks like running totals, ranking, and moving averages.

### Common Window Functions:

**ROW\_NUMBER()**: Assigns a unique sequential integer to rows within a partition.  
  
```sql
SELECT

\*,

ROW\_NUMBER() OVER (PARTITION BY category ORDER BY sales DESC) AS rank

FROM

sales\_data
```

**SUM() / AVG()**: Can be used as window functions to calculate running totals or averages.  
  
```sql
SELECT

date,

sales,

SUM(sales) OVER (ORDER BY date ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running\_total

FROM

sales\_data
```

## 10\. MERGE

### What is MERGE?

MERGE allows you to perform insert, update, or delete operations in a single statement based on a condition. It's useful for synchronizing tables.

### Example Usage

Synchronize target\_table with source\_table:

```sql
MERGE target\_table T

USING source\_table S

ON T.id = S.id

WHEN MATCHED THEN

UPDATE SET T.value = S.value

WHEN NOT MATCHED THEN

INSERT (id, value) VALUES (S.id, S.value) 
```

## 11\. ARRAY\_JOIN

### What is ARRAY\_JOIN?

While BigQuery doesn’t have a direct ARRAY\_JOIN function, you can achieve similar results using ARRAY\_TO\_STRING to concatenate array elements into a single string.

### Example Usage

Concatenate items into a comma-separated string:

```sql
SELECT

order\_id,

ARRAY\_TO\_STRING(items, ', ') AS items\_list

FROM

orders

**Result:**

| order_id | items_list |
| --- | --- |
| 1 | apple, banana |
| 2 | orange, grape, kiwi |  
```

## 12\. ARRAY\_POSITION

### What is ARRAY\_POSITION?

ARRAY\_POSITION returns the position of the first occurrence of a value in an array.

### Example Usage

Find the position of "banana" in the items array:

```sql
SELECT

order\_id,

ARRAY\_POSITION(items, 'banana') AS banana\_position

FROM

orders

**Result:**

| order_id | banana_position |
| --- | --- |
| 1 | 2 |
| 2 | NULL |
```

## 13\. SAFE.ARRAY\_LENGTH

### What is SAFE.ARRAY\_LENGTH?

This function returns the length of an array. The SAFE. prefix ensures that if the array is NULL, it returns NULL instead of an error.

### Example Usage

Get the number of items per order:

```sql
SELECT

order\_id,

SAFE.ARRAY\_LENGTH(items) AS item\_count

FROM

orders

**Result:**

| order_id | item_count |
| --- | --- |
| 1 | 2 |
| 2 | 3 |
```

## 14\. IFNULL and COALESCE

### What are IFNULL and COALESCE?

These functions handle NULL values by providing default values when encountering NULLs.

**IFNULL(expression, default\_value)**: Returns expression if it's not NULL; otherwise, returns default\_value.  

```sql  
SELECT IFNULL(middle\_name, 'N/A') AS middle\_name FROM users
```

**COALESCE(expression1, expression2, ..., expressionN)**: Returns the first non-NULL expression from the list.  
  
```sql
SELECT COALESCE(phone, mobile, 'No Contact') AS contact\_number FROM contacts
```

## 15\. STRING\_AGG

### What is STRING\_AGG?

STRING\_AGG concatenates strings from multiple rows into a single string with a specified separator.

### Example Usage

List all items per order\_id as a comma-separated string:

```sql
SELECT

order\_id,

STRING\_AGG(item, ', ') AS items\_list

FROM

orders,

UNNEST(items) AS item

GROUP BY

order\_id

**Result:**

| order_id | items_list |
| --- | --- |
| 1 | apple, banana |
| 2 | orange, grape, kiwi |
```

## 16\. PARTITION BY and ORDER BY in Window Functions

### What are PARTITION BY and ORDER BY?

These clauses define how window functions operate over subsets of data.

*   **PARTITION BY**: Divides the result set into partitions to which the window function is applied.
*   **ORDER BY**: Defines the order of rows within each partition.

### Example Usage

Calculate the cumulative sales per category:

```sql
SELECT

category,

sale\_date,

sales,

SUM(sales) OVER (PARTITION BY category ORDER BY sale\_date) AS cumulative\_sales

FROM

sales\_data

**Result:**

| category | sale_date | sales | cumulative_sales |
| --- | --- | --- | --- |
| A | 2025-01-01 | 100 | 100 |
| A | 2025-01-02 | 150 | 250 |
| B | 2025-01-01 | 200 | 200 |
| B | 2025-01-03 | 300 | 500 |
```

## 17\. Temporary Tables and Common Table Expressions (CTEs)

### What are CTEs?

Common Table Expressions allow you to define temporary result sets that can be referenced within a SELECT, INSERT, UPDATE, or DELETE statement. They improve readability and organization of complex queries.

### Example Usage

Using a CTE to calculate average sales:

```sql
WITH average\_sales AS (

SELECT category, AVG(sales) AS avg\_sales

FROM sales\_data

GROUP BY category

)

SELECT

s.category,

s.sales,

a.avg\_sales,

s.sales - a.avg\_sales AS sales\_diff

FROM

sales\_data s

JOIN

average\_sales a

ON

s.category = a.category

**Result:**

| category | sales | avg_sales | sales_diff |
| --- | --- | --- | --- |
| A | 100 | 125 | -25 |
| A | 150 | 125 | 25 |
| B | 200 | 250 | -50 |
| B | 300 | 250 | 50 |
```

## 18\. TIMESTAMP and DATE Functions

BigQuery offers a variety of functions to manipulate date and time data.

### Key Functions:

**CURRENT\_DATE()**: Returns the current date.  
  
SELECT CURRENT\_DATE() AS today

**DATE\_ADD / DATE\_SUB**: Adds or subtracts days from a date.  
  
```sql
SELECT DATE\_ADD('2025-01-01', INTERVAL 7 DAY) AS next\_week
```

**EXTRACT**: Extracts parts of a date/time.  
  
```sql
SELECT EXTRACT(YEAR FROM sale\_date) AS sale\_year FROM sales\_data
```

**FORMAT\_DATE**: Formats a date according to a specified format.  
  
```sql
SELECT FORMAT\_DATE('%B %d, %Y', sale\_date) AS formatted\_date FROM sales\_data
```

## 19\. CASE Statements

### What is a CASE Statement?

CASE allows you to perform conditional logic within your queries, similar to if-else statements in programming languages.

### Example Usage

Categorize sales performance:

```sql
SELECT

sales,

CASE

WHEN sales > 500 THEN 'High'

WHEN sales BETWEEN 200 AND 500 THEN 'Medium'

ELSE 'Low'

END AS performance

FROM

sales\_data

**Result:**

| sales | performance |
| --- | --- |
| 100 | Low |
| 300 | Medium |
| 600 | High |
```

## 20\. STRING Functions

BigQuery provides numerous functions to manipulate and analyze string data.

### Key String Functions:

**CONCAT / CONCAT\_WS**: Concatenates strings.  

```sql  
SELECT CONCAT(first\_name, ' ', last\_name) AS full\_name FROM users
```

**SUBSTR / SUBSTRING**: Extracts a substring from a string.  

```sql  
SELECT SUBSTR(email, 1, 5) AS email\_prefix FROM users
``` 

**UPPER / LOWER**: Converts strings to upper or lower case.  

```sql  
SELECT UPPER(product\_name) AS uppercase\_name FROM products
```

**REPLACE**: Replaces occurrences of a substring.  

```sql  
SELECT REPLACE(address, 'Street', 'St.') AS short\_address FROM addresses
```

**TRIM**: Removes leading and trailing whitespace.  

```sql  
SELECT TRIM(name) AS trimmed\_name FROM users
```

## 21\. Handling NULLs

Understanding how to handle NULL values is crucial for accurate data analysis.

### Functions to Handle NULLs:

**IFNULL**: Replaces NULL with a specified value.  
  
```sql
SELECT IFNULL(middle\_name, 'N/A') AS middle\_name FROM users
```

**COALESCE**: Returns the first non-NULL value in a list.  

```sql  
SELECT COALESCE(phone, mobile, 'No Contact') AS contact\_number FROM contacts
```

**NULLIF**: Returns NULL if two expressions are equal.  

```sql  
SELECT NULLIF(status, 'inactive') AS active\_status FROM users
```

## 22\. Conditional Aggregation

Performing aggregations based on conditions allows for more nuanced data summaries.

### Example Usage

Count the number of high and low sales per category:

```sql
SELECT

category,

COUNTIF(sales > 500) AS high\_sales\_count,

COUNTIF(sales <= 500) AS low\_sales\_count

FROM

sales\_data

GROUP BY

category

**Result:**

| category | high_sales_count | low_sales_count |
| --- | --- | --- |
| A | 1 | 2 |
| B | 3 | 0 |
```

## 23\. Regular Expressions

BigQuery supports powerful regular expression functions for pattern matching and string manipulation.

### Key Regex Functions:

**REGEXP\_CONTAINS**: Checks if a string matches a regex pattern.  

```sql  
SELECT

email,

REGEXP\_CONTAINS(email, r'@gmail\\.com$') AS is\_gmail

FROM

users
``` 
**REGEXP\_EXTRACT**: Extracts matching substrings.  

```sql   
SELECT

REGEXP\_EXTRACT(phone, r'(\\d{3})-\\d{3}-\\d{4}') AS area\_code

FROM

contacts
```

**REGEXP\_REPLACE**: Replaces substrings matching a regex pattern.  

```sql  
SELECT

REGEXP\_REPLACE(address, r'\\d+', '') AS address\_no\_number

FROM

addresses
```

## 24\. Geospatial Functions

For working with geographical data, BigQuery offers several geospatial functions.

### Key Geospatial Functions:

**ST\_GEOGPOINT**: Creates a geographical point.  

```sql  
SELECT ST\_GEOGPOINT(longitude, latitude) AS location FROM places
``` 

**ST\_DISTANCE**: Calculates the distance between two geographical points.  

```sql  
SELECT

place1,

place2,

ST\_DISTANCE(ST\_GEOGPOINT(lon1, lat1), ST\_GEOGPOINT(lon2, lat2)) AS distance\_meters

FROM

locations
``` 

**ST\_WITHIN**: Checks if one geography is within another.  

```sql  
SELECT

place,

ST\_WITHIN(location, ST\_GEOGFROMTEXT('POLYGON((...))')) AS is\_within

FROM

places
```

## 25\. User-Defined Functions (UDFs)

### What are UDFs?

User-Defined Functions allow you to create custom functions using SQL or JavaScript to encapsulate complex logic for reuse.

### Example Usage

Create a simple SQL UDF to calculate the square of a number:

```sql
CREATE TEMP FUNCTION square(x FLOAT64) AS (

x \* x

);
SELECT

number,

square(number) AS number\_squared

FROM

UNNEST(\[1, 2, 3, 4, 5\]) AS number

**Result:**

| number | number_squared |
| --- | --- |
| 1 | 1 |
| 2 | 4 |
| 3 | 9 |
| 4 | 16 |
| 5 | 25 |
``` 

## 26\. Array and Struct Nesting

BigQuery allows you to nest arrays within structs and vice versa, enabling the modeling of complex hierarchical data.

### Example Usage

Define a struct with nested arrays:

```sql
SELECT

user\_id,

STRUCT(

ARRAY<STRING>\['reading', 'traveling'\] AS hobbies,

ARRAY<INT64>\[25, 30\] AS favorite\_numbers

) AS profile

FROM

users

**Result:**

| user_id | profile |
| --- | --- |
| 1 | {hobbies: ["reading", "traveling"], favorite_numbers: [25, 30]} |
| 2 | {hobbies: ["gaming"], favorite_numbers: [42]} |
```

## 27\. ARRAYS in JOIN Operations

You can use arrays in JOIN operations by leveraging functions like UNNEST to flatten arrays before joining.

### Example Usage

Join orders with their items:

```sql
SELECT

o.order\_id,

i.item\_name

FROM

orders o

JOIN

UNNEST(o.items) AS i

ON TRUE

**Result:**

| order_id | item_name |
| --- | --- |
| 1 | apple |
| 1 | banana |
| 2 | orange |
| 2 | grape |
| 2 | kiwi |
```

## 28\. Handling Nested and Repeated Fields

BigQuery natively supports nested (struct) and repeated (array) fields, which can be accessed and manipulated using the functions and methods discussed above.

### Example Usage

Access nested fields:

```sql
SELECT

user\_id,

profile.hobbies,

profile.favorite\_numbers

FROM

users

**Result:**

| user_id | hobbies | favorite_numbers |
| --- | --- | --- |
| 1 | ["reading", "traveling"] | [25, 30] |
| 2 | ["gaming"] | [42] |
```

## 29\. Advanced Array Functions

### ARRAY\_SLICE

Extract a subset of an array.

```sql
SELECT

ARRAY\_SLICE(items, 1, 2) AS first\_two\_items

FROM

orders

**Result:**

| first_two_items |
| --- |
| ["apple", "banana"] |
| ["orange", "grape"] |
```

### ARRAY\_REVERSE

Reverse the order of elements in an array.

```sql
SELECT

ARRAY\_REVERSE(items) AS reversed\_items

FROM

orders

**Result:**

| reversed_items |
| --- |
| ["banana", "apple"] |
| ["kiwi", "grape", "orange"] |
```

### ARRAY\_CONCAT

Concatenate two arrays.

```sql
SELECT

ARRAY\_CONCAT(array1, array2) AS combined\_array

FROM

your\_table
```

## 30\. Performance Optimization Tips

### Use LIMIT and WHERE Clauses

When experimenting with new functions, use LIMIT and WHERE to restrict the amount of data processed, improving query performance.

```sql
SELECT

\*

FROM

your\_table

WHERE

condition

LIMIT 100
```

### Avoid Cross Joins When Possible

Be cautious with UNNEST as it can lead to cross joins, which may multiply the number of rows and impact performance.

### Leverage Partitioning and Clustering

Optimize your tables by partitioning and clustering based on frequently queried columns to reduce query costs and improve speed.

```sql
CREATE TABLE your\_dataset.your\_table

PARTITION BY DATE(timestamp\_column)

CLUSTER BY user\_id AS

SELECT

\*

FROM

source\_table
```

### Use Materialized Views

For frequently run complex queries, consider using materialized views to cache results and speed up query execution.

CREATE MATERIALIZED VIEW your\_dataset.mv\_name AS

```sql
SELECT

columns

FROM

your\_table

WHERE

conditions
```

## 31\. Additional Resources

To further enhance your BigQuery skills, consider exploring the following resources:

*   [**BigQuery Official Documentation**](https://cloud.google.com/bigquery/docs): Comprehensive guides and references.
*   [**BigQuery SQL Reference**](https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax): Detailed syntax and function descriptions.
*   [**Google Cloud BigQuery Tutorials**](https://cloud.google.com/bigquery/docs/tutorials): Step-by-step tutorials for various use cases.
*   [**BigQuery Public Datasets**](https://cloud.google.com/bigquery/public-data): Sample datasets to practice querying.

## Conclusion

BigQuery offers a rich set of functions and commands that empower you to handle complex data structures, perform advanced analytics, and optimize query performance. Functions like UNNEST, GENERATE\_ARRAY, and ARRAY\_AGG are just the tip of the iceberg. By familiarizing yourself with these tools, you'll be well-equipped to tackle a wide range of data challenges.
