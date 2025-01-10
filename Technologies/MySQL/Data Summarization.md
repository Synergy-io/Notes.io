# Data Summarization

## Aggregate functions
```sql
SELECT 
	MAX(invoice_total) AS highest,
    MIN(invoice_total) AS lowest,
    AVG(invoice_total) AS average,
    SUM(invoice_total*1.1) AS total,
    COUNT(invoice_total) AS number_of_invoices,
    COUNT(payment_date) AS count_of_payments,  -- return notnull payment dates
    COUNT(*) AS total_records,   -- totoal number of records
    COUNT(DISTINCT client_id) AS number_of_clients  -- to count only the unique values
FROM invoices
WHERE invoice_date>'2019-07-01';
```

## GROUP BY clause
```sql
SELECT 
	client_id,
	SUM(invoice_total) AS total_sales
FROM invoices
WHERE invoice_date>='2019-07-01' 
GROUP BY client_id
ORDER BY total_sales DESC;

SELECT 
	state,
    city,
	SUM(invoice_total) AS total_sales
FROM invoices i
JOIN clients USING (client_id)
GROUP BY state,city;
```

## HAVING clause
```sql
-- filter data aftr group by clause
-- items in having must be in select clause

--           WHERE
-- before the rows are grouped
-- can use items that are not in the select caluse

SELECT 
	client_id,
    SUM(invoice_total) AS total_sales,
    COUNT(*) AS number_of_invoices
FROM invoices
GROUP BY client_id
HAVING total_sales > 500 AND number_of_invoices>5;
```

## ROLLUP operator
```sql
-- summarise data

SELECT 
	state,
    city,
    SUM(invoice_total) AS total_sales
FROM invoices i
JOIN clients USING (client_id)
GROUP BY state, city WITH ROLLUP;

-- when we use ROLLUP we have to use the actual column name
```