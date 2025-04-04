# Stored Procedures

## creating a STORED PROCEDURE
```sql
-- to take the all the below statements as one unit
-- BEGIN , BODY, END

DELIMITER $$  
CREATE PROCEDURE get_clients()
BEGIN
	SELECT * FROM clients;  
END $$

DELIMITER ;
```
## calling procedures
```sql
CALL get_clients()
```

## Dropping Stored Procedures
```sql
DROP PROCEDURE IF EXISTS get_clients;
```

## Parameters
```sql
DROP PROCEDURE IF EXISTS get_clients_by_state;

DELIMITER $$  
CREATE PROCEDURE get_clients_by_state
(
	state CHAR(2)
)
BEGIN
	SELECT * FROM clients c
    WHERE c.state=state;
END $$

DELIMITER ;

CALL get_clients_by_state('CA');
```

## Parameters with Default Value 
```sql
-- get CA IF NULL
DROP PROCEDURE IF EXISTS get_clients_by_state;

DELIMITER $$  
CREATE PROCEDURE get_clients_by_state
(
	state CHAR(2)
)
BEGIN
	IF state IS NULL THEN
		SET state='CA';
	END IF;
    
	SELECT * FROM clients c
    WHERE c.state=state;
END $$

DELIMITER ;

CALL get_clients_by_state(NULL);

-- get ALL IF NULL
DROP PROCEDURE IF EXISTS get_clients_by_state;

DELIMITER $$  
CREATE PROCEDURE get_clients_by_state
(
	state CHAR(2)
)
BEGIN
	IF state IS NULL THEN
		SELECT * FROM clients;
	ELSE 
		SELECT * FROM clients c
		WHERE c.state=state;
	END IF;
END $$

DELIMITER ;

DROP PROCEDURE IF EXISTS get_clients_by_state;

DELIMITER $$  
CREATE PROCEDURE get_clients_by_state
(
	state CHAR(2)
)
BEGIN
	SELECT * FROM clients c
	WHERE c.state=IFNULL(state, c.state);  -- 1=1
END $$

DELIMITER ;
```

### Exercise
```sql
 -- write a stored procedure called get_payments
 -- with two parameters
 
 -- client_id => INT (4)
 -- payment_method_id => TINYINT  (1) 0-255
 
 DROP PROCEDURE IF EXISTS get_payments;

DELIMITER $$  
CREATE PROCEDURE get_payments
(
	client_id  INT,
    payment_method_id TINYINT
)
BEGIN
	SELECT * FROM payments p
    WHERE 
		p.client_id=IFNULL(client_id,p.client_id) AND
        p.payment_method=IFNULL(payment_method_id,p.payment_method);
END $$

DELIMITER ;

CALL get_payments(NULL,NULL);
CALL get_payments(5,NULL);
```

## PARAMETER VALIDATON
```sql
DROP PROCEDURE IF EXISTS make_payment;

DELIMITER $$  
CREATE PROCEDURE make_payment(
	invoice_id INT,
    payment_amount DECIMAL(9,2),
    payment_date DATE 
)
BEGIN
	IF payment_amount<=0 THEN 
		SIGNAL SQLSTATE '22003'  -- this is like creating an EXCEPTION
			SET MESSAGE_TEXT = 'Invalid payment amount';
	END IF;
	UPDATE invoices i
    SET
		i.payment_total=payment_amount,
        i.payment_date=payment_date
	WHERE i.invoice_id=invoice_id;
END $$

DELIMITER ;
```

## output parameters
```sql
DROP PROCEDURE IF EXISTS get_unpaid_invoices_for_clients;

DELIMITER $$  
CREATE PROCEDURE get_unpaid_invoices_for_clients(
	clinet_id INT,
    OUT invoices_count INT,
    OUT invoices_total DECIMAL(9,2) 
)
BEGIN
	SELECT COUNT(*), SUM(invoice_total)
    INTO invoices_count,invoices_total
    FROM invoices i
    WHERE i.client_id=client_id
		AND payment_total=0;
END $$

DELIMITER ;
```

## VARIABLES 
```sql
-- user or session variable
SET @invoices_count =0;

-- local variable
-- after the stored prcedure these variables are free
```

## FUNCTIONS 
```sql
-- can only return a single value

DROP FUNCTION IF EXISTS get_risk_factor_for_client;

DELIMITER $$
CREATE FUNCTION get_risk_factor_for_client 
(
	client_id INT 
)
RETURNS INTEGER
READS SQL DATA
BEGIN
	DECLARE risk_factor DECIMAL(9,2) DEFAULT 0;
    DECLARE invoices_total DECIMAL(9,2);
    DECLARE invoices_count INT;
    
    SELECT COUNT(*) , SUM(invoice_total)
    INTO invoices_count,invoices_total
    FROM invoices i
    WHERE i.client_id=client_id;
    
    SET risk_factor=invoices_total/invoices_count*5;
    
    RETURN IFNULL(risk_factor,0);
    
END$$

DELIMITER ;

SELECT 
	client_id,
    name,
    get_risk_factor_for_client(client_id) AS risk_factor
FROM clients;

-- proc for procedures
-- fn for functions

-- procGetRiskFactor
-- getRiskFactor

-- DELIMITER $$ or //

```


