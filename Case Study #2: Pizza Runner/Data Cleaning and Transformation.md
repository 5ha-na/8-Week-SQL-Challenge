# Cleaning customer_orders table 

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
