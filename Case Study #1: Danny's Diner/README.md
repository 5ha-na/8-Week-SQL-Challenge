## Questions and Solutions

1. What is the <ins>total amount</ins> each customer spent at the restaurant?
``` sql
SELECT s.customer_id,
       SUM(mn.price)  
FROM dannys_diner.sales s
INNER JOIN dannys_diner.menu mn
  ON s.product_id=mn.product_id
GROUP BY s.customer_id  
ORDER BY s.customer_id;
```

2. How many days has each customer visited the restaurant?
```sql
SELECT s.customer_id, 
       COUNT(s.customer_id) AS Total_Visit
FROM dannys_diner.sales s
GROUP BY s.customer_id
ORDER BY s.customer_id; 
```

3. What was <ins>the first item</ins> from the menu purchased by each customer?
```sql
WITH items_ranked AS (
SELECT s.customer_id, 
       mn.product_name, 
       s.order_date,
       DENSE_RANK () OVER (PARTITION BY s.customer_id ORDER BY s.order_date) AS rank
FROM dannys_diner.sales s
INNER JOIN dannys_diner.menu mn 
	ON s.product_id=mn.product_id) --end of CTE
    
SELECT ir.customer_id, ir.product_name
FROM items_ranked ir
WHERE rank = 1
GROUP BY ir.customer_id, ir.product_name;
```

4. What is the <ins>most purchased item</ins> on the menu and <ins>how many times was it purchased</ins> by all customers?
```sql
SELECT mn.product_name AS Most_Purchased_Item,
       COUNT(s.product_id) AS Num_Orders
FROM dannys_diner.sales s
INNER JOIN dannys_diner.menu mn
  ON s.product_id=mn.product_id
GROUP BY mn.product_name
ORDER BY Num_Orders DESC
LIMIT 1;
```

5. Which item was the <ins>most popular</ins> for each customer?
```sql
WITH popular_items AS 
(SELECT s.customer_id, 
        mn.product_name, 
        COUNT(s.product_id) AS order_count,
        DENSE_RANK () OVER (PARTITION BY s.customer_id ORDER BY count(s.product_id) desc) AS rank
FROM dannys_diner.sales s
INNER JOIN dannys_diner.menu mn
	ON s.product_id=mn.product_id
GROUP BY s.customer_id, mn.product_name) --end of CTE

SELECT pi.customer_id, pi.product_name,pi.order_count
FROM popular_items pi 
WHERE rank = 1
ORDER BY pi.customer_id;
```

6. Which item was purchased first by the customer **<ins>after</ins> they became a member**?
```sql
WITH first_purchase AS 
(SELECT s.customer_id, 
        s.order_date, 
        mm.join_date, 
        mn.product_name, 
        RANK() OVER (PARTITION BY s.customer_id ORDER BY s.order_date) AS rank
FROM dannys_diner.sales s
INNER JOIN dannys_diner.members mm
  ON s.customer_id=mm.customer_id
INNER JOIN dannys_diner.menu mn
  ON s.product_id=mn.product_id
WHERE s.order_date >= mm.join_date) --end of CTE

SELECT fp.customer_id, fp.product_name
FROM first_purchase fp
WHERE rank=1;

ANS: A=curry B=Sushi
```

7. Which item was purchased **<ins>just before</ins> the customer became a member**?
```sql
WITH pre_member AS 
(SELECT s.customer_id, 
        s.order_date, 
        mm.join_date, 
        mn.product_name, 
        RANK() OVER (PARTITION BY s.customer_id ORDER BY s.order_date DESC) AS rank
FROM dannys_diner.sales s
INNER JOIN dannys_diner.members mm
  ON s.customer_id=mm.customer_id
INNER JOIN dannys_diner.menu mn
  ON s.product_id=mn.product_id
WHERE s.order_date < mm.join_date) --end of CTE

SELECT pm.customer_id, pm.product_name
FROM pre_member pm
WHERE rank=1;

ANS: A=sushi&curry B=sushi
```

8. What is the total items and amount spent for each member **<ins>before</ins> they became a member**?
```sql
SELECT s.customer_id, 
       COUNT(s.product_id) AS Total_items, 
       SUM(mn.price) AS Total_amount_spent
FROM dannys_diner.sales s
INNER JOIN dannys_diner.members mm
  ON s.customer_id=mm.customer_id
INNER JOIN dannys_diner.menu mn
  ON s.product_id=mn.product_id
WHERE s.order_date < mm.join_date
GROUP BY s.customer_id
ORDER BY s.customer_id;
```

9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
```sql
WITH points_cte AS 
(SELECT mn.product_id, 
	      CASE WHEN mn.product_id=1 THEN mn.price*20
        ELSE mn.price*10
        END AS "points"
FROM dannys_diner.menu mn) --end of CTE

SELECT s.customer_id, SUM(pt.points) AS total_points
FROM dannys_diner.sales s
INNER JOIN points_cte pt
  ON s.product_id=pt.product_id
GROUP BY s.customer_id
ORDER BY s.customer_id;
```

10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
```sql
need to work on date manipulation
TBA
```

## Bonus Questions: 
1.Recreate the table output using the available data: 
```sql
SELECT s.customer_id, 
	     s.order_date, 
       mn.product_name,
       mn.price, 
       CASE 
            WHEN s.order_date < mm.join_date THEN 'N'
            WHEN s.order_date >= mm.join_date THEN 'Y'
            ELSE 'N' --Accomodate for customer_id C
            END AS member
FROM dannys_diner.sales s
LEFT JOIN dannys_diner.members mm --left join because you want all the data in Sales table & data matching the members table 
  ON s.customer_id=mm.customer_id
INNER JOIN dannys_diner.menu mn
  ON s.product_id=mn.product_id
ORDER BY s.customer_id, s.order_date, mn.product_name;
```
2. Rank the table
```sql
WITH member_cte AS
(SELECT s.customer_id, 
        s.order_date, 
        mn.product_name, 
        mn.price,
        CASE 
            WHEN s.order_date < mm.join_date THEN 'N'
            WHEN s.order_date >= mm.join_date THEN 'Y'
            ELSE 'N' --Accomodate for customer_id C
            END AS member
FROM dannys_diner.sales s
LEFT JOIN dannys_diner.members mm  
  ON s.customer_id=mm.customer_id
JOIN dannys_diner.menu mn
  ON s.product_id=mn.product_id
ORDER BY s.customer_id, s.order_date, mn.product_name) --end of CTE

SELECT mc.*,
       CASE WHEN mc.member like 'N' THEN NULL
       ELSE RANK () OVER (PARTITION BY mc.customer_id, mc.member ORDER BY mc.order_date asc)
       END AS rank
FROM member_cte mc;
```
