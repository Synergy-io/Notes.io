# Data Manipulation

## Inserting

```sql
INSERT INTO customers

VALUES (
	DEFAULT,
    'John',
    'Smith',
    '1990-01-01',
    NULL,  -- or DEFAULT both these keywords will have the same result
    'address',
    'city',
    'CA',
    DEFAULT);

another way
-- in this method we can list them in any order

INSERT INTO customers(
	first_name,
    last_name,
    birth_date,
    address,
    city,
    state)
VALUES (
    'John',
    'Smith',
    '1990-01-01',
    'address',
    'city',
    'CA');
``` 
### Inserting multiple values 
```sql
INSERT INTO shippers(name)
VALUES('Shipper1'),
	  ('Shipper2'),
      ('Shipper3');
```

### Inserting Hierarchical rows 
```sql
order - Parent
order items - Child
order has one or more order items

INSERT INTO orders(customer_id,order_date,status)
VALUES(1,'2019-01-02',1);

-- SELECT last_insert_id();  => 11 (new order_id)
INSERT INTO order_items
VALUES
	   (last_insert_id(),1,1,2.95),
    (last_insert_id(),2,1,3.95)
```

### Creating a copy of a table 
```sql
copy the whole table
CREATE TABLE orders_archived AS 
SELECT * FROM orders -- sub query

using select as a sub query of an insert table
INSERT INTO orders_archived
SELECT *
FROM orders
WHERE order_date< '2019-01-01'
```

## Updating

### Updating a single row 
```sql
UPDATE invoices
SET payment_total=DEFAULT, payment_date= NULL
WHERE invoice_id=1;

UPDATE invoices
SET 
	payment_total=invoice_total*0.5,
    payment_date=due_date
WHERE invoice_id=3;
```
### Updating multiple rows
```sql
UPDATE invoices
SET 
	payment_total=invoice_total*0.5,
    payment_date=due_date
WHERE client_id=3;

UPDATE invoices
SET 
	payment_total=invoice_total*0.5,
    payment_date=due_date
WHERE client_id IN (3,4);
```

### Using Subqueries in Updates
```sql
UPDATE invoices
SET 	
	payment_total=invoice_total*0.5,
    payment_date=due_date
WHERE client_id IN
	(SELECT client_id
    FROM clients
    WHERE state IN ('CA','NY'));
    
UPDATE invoices
SET 	
	payment_total=invoice_total*0.5,
    payment_date=due_date
-- SELECT *
-- FROM invoices
WHERE payment_date IS NULL;
```

## Deleting

### deleting rows
```sql
DELETE FROM invoices
WHERE client_id=(
		SELECT client_id
        FROM clients
        WHERE name='Myworks')


