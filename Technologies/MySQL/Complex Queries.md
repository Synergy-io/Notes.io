# Complex Queries

## subqueries 
```sql
SELECT *
FROM products 
WHERE unit_price > (
	SELECT unit_price
    FROM products
    WHERE product_id=3);
```

## IN operator  
```sql
SELECT *
FROM products
WHERE product_id NOT IN (
	SELECT DISTINCT product_id
    FROM order_items
);
```

## ALL keyword
```sql
-- select invoices larger than all invoices of client 3

SELECT *
FROM invoices
WHERE invoice_total>(
	SELECT MAX(invoice_total)
	FROM invoices
	WHERE client_id=3);

SELECT *
FROM invoices
WHERE invoice_total> ALL(   -- invoice total greater than to all values in the sub query
	SELECT invoice_total   -- first execute the subquery
	FROM invoices
	WHERE client_id=3);   
```
## ANY keyword     
```sql
-- select clients with at least 2 invoices

SELECT *
FROM clients
WHERE client_id = ANY (
	SELECT client_id
	FROM invoices
	GROUP BY client_id
	HAVING COUNT(*) >=2  -- filter after grouping
);
```

## Correlated Subqueries    
```sql
-- select employees whose salary is above the average in their office

-- psuedocode
-- for each employee
--    calculate the avg salary for employee.office
--    return the employee if salary > avg

SELECT *
FROM employees  e
WHERE salary > (
	SELECT AVG(salary)
    FROM employees
    WHERE office_id=e.office_id
);
```
## EXISTS Operator
```sql
 -- select clients that have an invoice
 
 SELECT DISTINCT name               -- method 1
 FROM invoices
 JOIN clients USING (client_id);
 
 SELECT *                            -- method 2
 FROM clients                        -- return the result of the subquery as a list
 WHERE client_id IN (                -- this is a negative impact for the performance
	SELECT DISTINCT client_id
    FROM invoices
);
 
 SELECT *                            -- method 3
 FROM clients c                      -- more efficient
 WHERE EXISTS (                      -- sub query doen't return a result 
	SELECT client_id                 -- just return true if exists
    FROM invoices
    WHERE client_id=c.client_id
); 
```

### subqueries in the SELECT clause
```sql
SELECT 
	invoice_id,
    invoice_total,
    (SELECT AVG(invoice_total)
		FROM invoices) AS invoice_average,
	invoice_total - (SELECT invoice_average) AS difference
FROM invoices;
```
### subqueries in the FROM clause
```sql
-- it is required to use alias in subquery of a FROM clause
-- it is better to use views in this senario

SELECT *
FROM (
	SELECT 
		client_id,
		name,
		(SELECT SUM(invoice_total)
			FROM invoices
			WHERE c.client_id=client_id) AS total_sales,
		(SELECT AVG(invoice_total) 
			FROM invoices) AS average,
		(SELECT total_sales - average) AS difference
	FROM clients c
)  AS salrs_summary
WHERE total_sales IS NOT NULL
```