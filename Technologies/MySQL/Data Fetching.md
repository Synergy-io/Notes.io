# Data Fetching

## From a single table

### comparison operators

```sql 

SELECT *
FROM customers
-- WHERE points>3000
-- WHERE state='VA' -- or "VA" for texual data
-- WHERE state <> 'VA' -- or !=
WHERE birth_date > '1990-01-01';
```

### AND , OR , NOT operators
```sql
SELECT *
FROM customers
WHERE not (birth_date>'1990-01-01' or 
	(points>1000 and state='VA'));
```

### IN operator
```sql
SELECT *
FROM customers
WHERE state IN ('VA', 'FL', 'GA');
-- WHERE state NOT IN ('VA', 'FL', 'GA')
```

### BETWEEN operator
```sql
SELECT *
FROM customers
-- WHERE points>= 1000 AND points<=3000
WHERE points BETWEEN 1000 AND 3000;
```

### LIKE operator
```sql
SELECT *
FROM customers
-- WHERE last_name LIKE 'b%'  -- % represents any number of characters
-- WHERE last_name LIKE '%b%' 
-- WHERE last_name LIKE '%y' 
WHERE last_name LIKE 'b____y';  -- _ represents a single character
```

### REGEXP operator
```sql
SELECT *
FROM customers
-- WHERE last_name LIKE '%field%';
-- WHERE last_name REGEXP 'field';
-- WHERE last_name REGEXP '^field'; -- ^indicates the begining of a string
-- WHERE last_name REGEXP 'field$';  -- $ indicates the end
-- WHERE last_name REGEXP 'field$|^mac|rose';
-- WHERE last_name REGEXP '[gim]e';
WHERE last_name REGEXP '[a-h]e'; 

-- ^ begining
-- $ end
-- | logical or
-- [abcd]
-- [a-f] 
```

### IS NULL operator
```sql
SELECT *
FROM customers
WHERE phone IS NOT NULL;
```

### ORDER BY clause
```sql
SELECT *
FROM customers
-- ORDER BY first_name DESC;  -- DESC indicates descending order
ORDER BY state, first_name;

-- in MYSQL we can order whether the column is select or not
SELECT first_name,last_name ,10 AS points
FROM customers
ORDER BY points, first_name;

-- xxxxx not recommended xxxxxx
-- SELECT first_name,last_name ,10 AS points
-- FROM customers
-- ORDER BY 1,2  -- sorted by first_name and last_name 
```

### LIMIT clause
```sql
SELECT * 
FROM customers
LIMIT 3;
-- LIMIT 300; -- can get all the 10 customers

SELECT * 
FROM customers
LIMIT 6,3;  -- skip 6 and show 3
```

## From multiple tables

### Inner Joins

```sql
-- if we have the same column in 2 tables we need to prefix them 

SELECT order_id ,first_name,last_name,o.customer_id -- or customers.customer_id
FROM orders o -- can use o instead of orders repititively
-- INNER keyword is optional
JOIN customers c
	ON o.customer_id=c.customer_id;
```

### Joining across database
```sql
-- Product table is in sql_store and sql_inventry (in 2 different databases)
-- we want to join product table in the sql_inventry 

-- we just need to prefix with the correct databse with the table

USE sql_inventory;

SELECT *
FROM sql_store.order_items oi
JOIN sql_inventory.products p
	ON oi.product_id = p.product_id;
```   

### Self join
```sql
-- helps to find the manager of each employee in the same table

USE sql_hr;

SELECT 
    e.employee_id,
    e.first_name,
    m.first_name AS manager 
FROM employees e
JOIN employees m
	ON e.reports_to = m.employee_id;
```

### Joining multiple tables
```sql
USE sql_store;
SELECT 
	o.order_id,
    o.order_date,
    c.first_name,
    c.last_name,
    os.name AS status
FROM orders o
JOIN customers c
	ON o.customer_id=c.customer_id
JOIN order_statuses os
	ON o.status=os.order_status_id;
```

### Compound Join Conditions
```sql
-- when joining the selected column must uniquely identify each item in the table
-- in order_items there are 2 primary keys

USE sql_store;
SELECT *
FROM order_items oi
JOIN order_item_notes oin
	ON oi.order_id = oin.order_id
    AND oi.product_id= oin.product_id;
```

### explicit join 
```sql
-- SELECT *
-- FROM orders o
-- JOIN customers c
-- 	ON o.customer_id=c.customer_id

-- It is better to use explicit join than the implicit

-- Implicit Join Syntax

SELECT *
FROM orders o,customers c
WHERE o.customer_id=c.customer_id;
```

### Outer Joins
```sql
-- We can't see customers without an order, if we join orders and customers

-- 2 types of outer joins 
-- 1. left join - all the records in left table are returned
-- 2. right join - all the records in right table are returned
SELECT 
	c.customer_id,
    c.first_name,
    o.order_id
FROM customers c
LEFT JOIN orders o  -- LEFT OUTER JOIN orders o (OUTER keyword is optional)
	ON c.customer_id=o.customer_id
ORDER BY c.customer_id;
```

### Outer Join Between Multiple Tables
```sql
-- as practice try to use left join

SELECT 
	c.customer_id,
    c.first_name,
    o.order_id,
    sh.name AS shipper
FROM customers c
LEFT JOIN orders o
	ON c.customer_id=o.customer_id
LEFT JOIN shippers sh
	ON o.shipper_id=sh.shipper_id
ORDER BY c.customer_id;
```

### Self Outer Joins
```sql
USE sql_hr;
SELECT 
	e.employee_id,
    e.first_name,
    m.first_name AS manager
FROM employees e
LEFT JOIN employees m
	ON e.reports_to=m.employee_id;
 ```   
### The USING keyword
```sql
-- both joining column names should be same and unique
USE sql_store;

SELECT 
	o.order_id,
    c.first_name,
    sh.name AS shipper
FROM orders o
JOIN customers c
	-- ON o.customer_id=c.customer_id
    USING (customer_id)
JOIN shippers sh
	USING (shipper_id);
    
SELECT *
FROM order_items oi
JOIN order_item_notes oin
		-- ON oi.order_id=oin.order_Id AND
			-- oi.product_id=oin.product_id
		USING (order_id,product_id);
``` 

### natural join   (not recommended)
```sql
USE sql_store;
SELECT 
	o.order_id,
	c.first_name
FROM orders o
NATURAL JOIN customers c;
```

### CROSS JOIN
```sql
-- join every record from the first table with every record in the second table

SELECT 
	c.first_name AS customer,
    p.name AS product
FROM  customers c  
CROSS JOIN products p
ORDER BY c.first_name;
```

### Unions
```sql
-- combine records from multiple queries
SELECT 
	order_id,
    order_date,
    'Active' AS status
FROM orders
WHERE order_date >= '2019-01-01'
UNION
SELECT 
	order_id,
    order_date,
    'Archived' AS status
FROM orders
WHERE order_date < '2019-01-01';

-- number of columns that each query returns must be equal

SELECT first_name
FROM customers
UNION
SELECT name
FROM shippers;
```