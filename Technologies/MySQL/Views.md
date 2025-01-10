# Views

## creating views
```sql
CREATE VIEW sales_by_client AS
SELECT 
	c.client_id,
    c.name,
    SUM(invoice_total) AS total_sales
FROM clients c
JOIN invoices i USING (client_id)
GROUP BY client_id,name;

SELECT *
FROM sales_by_client
ORDER BY total_sales DESC;

SELECT *
FROM sales_by_client
JOIN clients USING (client_id);
```
## drop views
```sql
DROP VIEW sales_by_client;
```
## update views

Updatable views (not having below)
* DISTINCT
* Aggregate functions(MIN,MAX,SUM,...)
* GROUP BY/HAVING
* UNION

```sql
UPDATE invoices_with_balance
SET due_date=DATE_ADD(due_date,INTERVAL 2 DAY)
WHERE invoice_id=2;
```