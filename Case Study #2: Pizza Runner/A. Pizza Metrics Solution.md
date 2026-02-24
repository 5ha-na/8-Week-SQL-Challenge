## Questions 

1. How many pizzas were ordered?
   ```sql
   SELECT COUNT(pizza_id) AS total_pizzas
   FROM customer_orders_temp;
   ```
2. How many unique customer orders were made?
   ```sql
   SELECT COUNT(DISTINCT order_id) AS unique_orders
   FROM customer_orders_temp;
   ```
3. How many successful orders were delivered by each runner?
   ```sql
   SELECT runner_id, COUNT(cancellation) AS successful_orders
   FROM runner_orders_temp
   WHERE cancellation IS NOT NULL
   GROUP BY runner_id
   ORDER BY runner_id;;
   ```
   successful orders = not cancelled
4. How many of each type of pizza was delivered?
6. How many Vegetarian and Meatlovers were ordered by each customer?
7. What was the maximum number of pizzas delivered in a single order?
8. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
9. How many pizzas were delivered that had both exclusions and extras?
10. What was the total volume of pizzas ordered for each hour of the day?
11. What was the volume of orders for each day of the week?
