# Numeric Functions


## Numeric functions                              
```sql
SELECT ROUND(5.68,1);

SELECT TRUNCATE(5.78,1);

-- SMALLEST integer that is GREATER THAN OR EQUAL to the number
SELECT CEILING(5.2);

-- LARGEST integer that is LESS THAN OR EQUAL to the number
 SELECT FLOOR(5.7);
 
 SELECT ABS(-5.2);
 
 SELECT RAND();  -- RANDOM number between 0 and 1
 ```

## String functions    
```sql
SELECT LENGTH('Sky');
SELECT UPPER('car');
SELECT LOWER('CAR');
SELECT LTRIM('  cloud'); -- LEFT TRIM
SELECT RTRIM('DOG   '); -- RIGHT TRIM
SELECT TRIM('   HAT   ');  -- REMOVE ANY LEADING OR TRAILING SPACES
SELECT LEFT('Kindergarden',4); -- returning few characters from the left
SELECT RIGHT('Kindergarden',4); -- returning few characters from the right
SELECT SUBSTRING('Kindergarden',3,5); 
SELECT SUBSTRING('Kindergarden',3);   -- return 3 position to end of the string

-- return the first occurance of the character
-- searching is not case sensitive
SELECT LOCATE('D','Kindergarden');   -- 4
SELECT LOCATE('z','Kindergarden');  -- 0
SELECT LOCATE('garden','Kindergarden');  -- 7

SELECT REPLACE('Kindergarten','garten','garden'); 

SELECT CONCAT('FIRST','LAST');

SELECT CONCAT(first_name,' ',last_name) AS full_name
FROM customers;
```

## Date functions   
```sql
SELECT NOW();
SELECT CURDATE();
SELECT CURTIME();
SELECT YEAR(NOW());
SELECT MONTH(NOW());
SELECT HOUR(NOW());
SELECT DAYNAME(NOW());
SELECT MONTHNAME(NOW());
SELECT EXTRACT(DAY FROM NOW());
```

## Formating dates and times                           
```sql
SELECT DATE_FORMAT(NOW(),'%y');  -- 24
SELECT DATE_FORMAT(NOW(),'%Y');  -- 2024

SELECT DATE_FORMAT(NOW(),'%m');  -- 09
SELECT DATE_FORMAT(NOW(),'%M');  -- September

SELECT DATE_FORMAT(NOW(),'%M %d %Y');  -- September 15 2024

SELECT TIME_FORMAT(NOW(),'%H:%i %p');  -- 16:33 PM
```

## calculating dates and times                          
```sql
SELECT DATE_ADD(NOW(),INTERVAL 1 YEAR);  -- 2025-09-15 16:34:59

SELECT DATEDIFF('2024-09-15','2024-09-20');

SELECT TIME_TO_SEC('09:00');
```

## IFNULL and COALESCE Functions                       
```sql
SELECT 
	order_id,
    IFNULL(shipper_id, 'Not assigned') AS shipper
FROM orders;

-- returns first notnull value
SELECT 
	order_id,
    COALESCE(shipper_id,comments, 'Not assigned') AS shipper
FROM orders;
```

## IF function                                
```sql
SELECT
	order_id,
    order_date,
    IF(
		YEAR(order_date)=YEAR(NOW()),
        'Active',
        'Archived') AS category
FROM orders;
```

## CASE operator                              
```sql
SELECT 
	order_id,
    CASE
		WHEN YEAR(order_date) = YEAR('2019-01-01') THEN 'Active'
        WHEN YEAR(order_date) = YEAR('2019-01-01')-1 THEN 'Last year'
        WHEN YEAR(order_date) < YEAR('2019-01-01')-1 THEN 'Archived'
        ELSE 'Future'
	END AS category
FROM orders;
```