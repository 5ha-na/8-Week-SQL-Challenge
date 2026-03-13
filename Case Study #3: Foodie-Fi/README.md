### Questions:

## A. Customer Journey
Based off the 8 sample customers provided in the sample from the subscriptions table, write a brief description about each customer’s onboarding journey.

Try to keep it as short as possible - you may also want to run some sort of join to make your explanations a bit easier!

Gather data for 8 customers: 
```sql
SELECT s.customer_id, 
	s.plan_id, 
    p.plan_name, 
    s.start_date
FROM foodie_fi.subscriptions s
INNER JOIN foodie_fi.plans p 
ON p.plan_id=s.plan_id
WHERE s.customer_id IN (1,2,11,13,15,16,18,19)
ORDER BY s.customer_id, s.start_date
```

## B. Data Analysis Questions
1. How many customers has Foodie-Fi ever had?
```sql
SELECT COUNT(DISTINCT(customer_id)) AS Total_Customers
FROM foodie_fi.subscriptions

ANS: 1000 customers
```
2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value
```sql
SELECT DATE_PART('month', s.start_date) AS Month_no, --only returns numerical value of month
TO_CHAR(s.start_date, 'FMMonth') AS Month, --formats the date
COUNT(s.customer_id) AS Total
FROM foodie_fi.subscriptions s
INNER JOIN foodie_fi.plans p
ON p.plan_id=s.plan_id
WHERE p.plan_id = 0
GROUP BY DATE_PART('month', s.start_date), TO_CHAR(s.start_date, 'FMMonth')
ORDER BY Month_no
```

3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name
```sql
SELECT p.plan_id, p.plan_name, 
COUNT(DISTINCT(s.customer_id)) AS Total
FROM foodie_fi.subscriptions s
INNER JOIN foodie_fi.plans p
ON s.plan_id=p.plan_id
WHERE s.start_date >= '2021-01-01'
GROUP BY p.plan_name, p.plan_id
ORDER BY p.plan_id
```

4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
```sql
SELECT 
	COUNT(DISTINCT(s.customer_id)) AS No_Customers,
	ROUND( 100.0* COUNT(DISTINCT(s.customer_id)) / 
		(SELECT COUNT(DISTINCT(s.customer_id))
		 FROM foodie_fi.subscriptions s),1) AS Churn_perc
FROM foodie_fi.subscriptions s
WHERE plan_id = 4
```
5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?
6. What is the number and percentage of customer plans after their initial free trial?
7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?
8. How many customers have upgraded to an annual plan in 2020?
9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?
10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)
11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?


## C. Challenge Payment Question
The Foodie-Fi team wants you to create a new payments table for the year 2020 that includes amounts paid by each customer in the subscriptions table with the following requirements:

1. monthly payments always occur on the same day of month as the original start_date of any monthly paid plan
2. upgrades from basic to monthly or pro plans are reduced by the current paid amount in that month and start immediately
3. upgrades from pro monthly to pro annual are paid at the end of the current billing period and also starts at the end of the month period
once a customer churns they will no longer make payments
