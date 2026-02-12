## Cleaning customer_orders table 

```sql
CREATE TEMP TABLE customer_orders_temp AS 
SELECT c.order_id, 
       c.customer_id, 
       c.pizza_id, 
       CASE 
	       WHEN c.exclusions IS NULL OR c.exclusions LIKE 'null' THEN ' '
           ELSE c.exclusions
           END AS exclusions,
       CASE 
	       WHEN c.extras IS NULL  or c.extras LIKE 'null' THEN ' '
           ELSE c.extras
           END AS extras,
       c.order_time
FROM customer_orders c;

SELECT *
FROM customer_orders_temp;
```

## Cleaning runner_orders table
```sql
CREATE TEMP TABLE runner_orders_temp AS
SELECT order_id,
       runner_id,
       CASE 
            WHEN pickup_time LIKE 'null' THEN ' ' 
            ELSE pickup_time
            END AS pickup_time,
       CASE
            WHEN distance LIKE 'null' THEN ' ' 
            ELSE REGEXP_REPLACE(distance, '[^0-9.]', '', 'g')
            END AS distance,
       CASE
            WHEN duration LIKE 'null' THEN ' ' 
            ELSE REGEXP_REPLACE(duration, '[^0-9.]', '', 'g')
            END AS duration,
       CASE
            WHEN cancellation LIKE 'null' OR cancellation IS NULL THEN ' '
            ELSE cancellation
            END AS cancellation
FROM runner_orders;
```
