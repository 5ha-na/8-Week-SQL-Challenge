## Questions 

1. How many pizzas were ordered?
   ```sql
   SELECT COUNT(c.pizza_id) AS total_pizzas
   FROM customer_orders_temp c;
   ```
   ANS: 14 pizzas
2. How many unique customer orders were made?
   ```sql
   SELECT COUNT(DISTINCT order_id) AS unique_orders
   FROM customer_orders_temp;
   ```
   ANS: 10 unique orders
3. How many successful orders were delivered by each runner?
   ```sql
   SELECT r.runner_id, COUNT(r.cancellation) AS successful_orders
   FROM runner_orders_temp r
   WHERE r.cancellation LIKE ' ' OR cancellation LIKE '' --have to account for ' ' and ''
   GROUP BY r.runner_id
   ORDER BY r.runner_id;
   ```
   successful orders = Not Cancelled
   
   ANS:
   runner_id successful_orders
   1           4
   2           3
   3           1
   
5. How many of each type of pizza was delivered?
   ```sql
   SELECT COUNT(r.order_id) AS count,
          p.pizza_name
   FROM customer_orders_temp c
   RIGHT JOIN runner_orders_temp r 
   ON c.order_id = r.order_id
   INNER JOIN pizza_names p
   ON c.pizza_id = p.pizza_id
   WHERE r.cancellation LIKE ' ' OR r.cancellation LIKE ''
   GROUP BY p.pizza_name; 
   ```

   ANS:
   count    pizza_name
   3        Vegetarian
   9        Meatlovers
 
6. How many Vegetarian and Meatlovers were ordered by each customer?
   ```sql
   SELECT c.customer_id, p.pizza_name, c.pizza_id
   FROM customer_orders_temp c
   INNER JOIN pizza_names p
   ON c.pizza_id = p.pizza_id
   GROUP BY p.pizza_name, c.customer_id, c.pizza_id
   ORDER BY c.customer_id;
   ```
9. What was the maximum number of pizzas delivered in a single order?
10. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
11. How many pizzas were delivered that had both exclusions and extras?
12. What was the total volume of pizzas ordered for each hour of the day?
13. What was the volume of orders for each day of the week?
