# Triggers and Events

## Triggers
```sql
-- can modify data in any table except the table we consider in the trigger

DELIMITER $$
DROP TRIGGER IF EXISTS payments_after_insert;
CREATE TRIGGER payments_after_insert 
	AFTER INSERT ON payments
    FOR EACH ROW
BEGIN
	UPDATE invoices
    SET payment_total=payment_total+NEW.amount
    WHERE invoice_id=NEW.invoice_id;
END $$
DELIMITER ;

INSERT INTO payments
VALUES(DEFAULT,5,3,'2019-01-01',10,1);

-- create a trigger that gets fired when we delete a payment

DELIMITER $$
CREATE TRIGGER payments_after_delete 
	AFTER DELETE ON payments
    FOR EACH ROW
BEGIN
	UPDATE invoices
    SET payment_total=payment_total-OLD.amount
    WHERE invoice_id=OLD.invoice_id;
END $$
DELIMITER ;

DELETE FROM payments
WHERE payment_id=9;

-- view triggers
SHOW TRIGGERS;
SHOW TRIGGERS LIKE 'payments%';
```
## drop triggers
```sql
DROP TRIGGER IF EXISTS payments_after_insert;
```

###  Using triggers for auditing
```sql
CREATE TABLE payments_audit
(
	client_id 		INT 			NOT NULL, 
    date 			DATE 			NOT NULL,
    amount 			DECIMAL(9, 2) 	NOT NULL,
    action_type 	VARCHAR(50) 	NOT NULL,
    action_date 	DATETIME 		NOT NULL
);

DELIMITER $$
DROP TRIGGER IF EXISTS payments_after_insert;
CREATE TRIGGER payments_after_insert 
	AFTER INSERT ON payments
    FOR EACH ROW
BEGIN
	UPDATE invoices
    SET payment_total=payment_total+NEW.amount
    WHERE invoice_id=NEW.invoice_id;
    
    INSERT INTO payments_audit
    VALUES (NEW.client_id,NEW.date,NEW.amount,'Insert',NOW());
END $$
DELIMITER ;

DELIMITER $$
DROP TRIGGER IF EXISTS payments_after_delete;
CREATE TRIGGER payments_after_delete 
	AFTER DELETE ON payments
    FOR EACH ROW
BEGIN
	UPDATE invoices
    SET payment_total=payment_total-OLD.amount
    WHERE invoice_id=OLD.invoice_id;
    
    INSERT INTO payments_audit
    VALUES (OLD.client_id,OLD.date,OLD.amount,'Delete',NOW());
END $$
DELIMITER ;

INSERT INTO payments
VALUES (DEFAULT,5,3,'2019-01-01',10,1);

DELETE FROM payments
WHERE payment_id=11;
```

## Events
```sql
-- A task that gets executed according to a schedule

SHOW VARIABLES LIKE 'event%';
-- if it is off
-- SET GLOBAL event_scheduler=ON;

DELIMITER $$
CREATE EVENT yearly_delte_stale_audit_rows
ON SCHEDULE 
	-- if we want to execute only once --> AT
    -- regular basis --> EVERY
    EVERY 1 YEAR STARTS '2019-01-01' ENDS '2029-01-01'
    --           OPTIONAL            OPTIONAL
DO BEGIN
	DELETE FROM payments_audit
    WHERE action_date< NOW() - INTERVAL 1 YEAR;
    
    -- DATEADD(NOW(),INTERVAL 1 YEAR)
END $$
DELIMITER ;
```   
### Viewing, Dropping and Altering Events
```sql
SHOW EVENTS;

SHOW EVENTS LIKE 'yearly%';

DROP EVENT IF EXISTS yearly_delte_stale_audit_rows;

ALTER EVENT yearly_delte_stale_audit_rows ENABLE;
```


