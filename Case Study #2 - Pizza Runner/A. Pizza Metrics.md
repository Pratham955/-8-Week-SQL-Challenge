# 🍕 Case Study #2: Pizza runner - Pizza Metrics

## 🍝 Solution - A. Pizza Metrics

1. How many pizzas were ordered?
2. How many unique customer orders were made?
3. How many successful orders were delivered by each runner?
4. How many of each type of pizza was delivered?
5. How many Vegetarian and Meatlovers were ordered by each customer?
6. What was the maximum number of pizzas delivered in a single order?
7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
8. How many pizzas were delivered that had both exclusions and extras?
9. What was the total volume of pizzas ordered for each hour of the day?
10. What was the volume of orders for each day of the week?

***

###  1. How many pizzas were ordered?

```sql
SELECT 
  COUNT(*) AS pizza_count 
FROM customer_orders
``` 
	
#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/15643508-29ce-402a-bef7-03947b825f9a)

***

###  2. How many unique customer orders were made?

```sql
SELECT
  COUNT(*) AS unique_customer_order
FROM runner_orders
``` 
	
#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/f3027fc8-9749-4912-a4b8-64c26744b802)

***

###  3. How many successful orders were delivered by each runner?

```sql
SELECT 
  runner_id,
  COUNT(*) AS successful_orders
FROM runner_orders
WHERE cancellation IS NULL
GROUP BY 1
``` 
	
#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/bffa6abb-a7ac-4fcf-837c-21f15a17f98a)

***

###  4. How many of each type of pizza was delivered?

```sql
SELECT 
  pizza_name,
  COUNT(*) AS pizzas_delivered 
FROM runner_orders
INNER JOIN customer_orders USING(order_id)
INNER JOIN pizza_names USING(pizza_id)
WHERE cancellation IS NULL
GROUP BY 1

``` 
	
#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/883d6338-12c9-4ea5-a3e4-d0c8b4311034)

***

###  5. How many Vegetarian and Meatlovers were ordered by each customer?

```sql
SELECT 
  customer_id,
  pizza_name,
  COUNT(*) AS pizza_ordered
FROM customer_orders
INNER JOIN pizza_names USING(pizza_id)
GROUP BY 1,2
ORDER BY 1
``` 
	
#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/b1c2ea00-73c9-4210-b1eb-0d52ce38cf32)

***

###  6. What was the maximum number of pizzas delivered in a single order?

```sql
SELECT 
  COUNT(*) AS max_num_pizza_order 
FROM customer_orders 
INNER JOIN runner_orders USING(order_id)
WHERE cancellation IS NULL
GROUP BY customer_id
ORDER BY 1 DESC
LIMIT 1
``` 
	
#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/67ca7cb8-413a-4d42-a781-de72ba3bd5af)

***

###  7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

```sql
SELECT 
  customer_id,
  COUNT(CASE WHEN exclusions IS NOT NULL OR extras IS NOT NULL THEN 1 END) AS atleast_one_change,
  COUNT(CASE WHEN exclusions IS NULL AND extras IS NULL THEN 1 END) AS no_change
FROM customer_orders
INNER JOIN runner_orders USING(order_id)
WHERE cancellation IS NULL
GROUP BY 1
ORDER BY 1
``` 

#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/fb368221-e857-4169-8872-32ba39214b9a)


***

###  8. How many pizzas were delivered that had both exclusions and extras?

```sql
SELECT 
  COUNT(CASE WHEN exclusions IS NOT NULL AND extras IS NOT NULL THEN 1 END) AS both_exclusions_extras
FROM customer_orders 
INNER JOIN runner_orders USING(order_id)
WHERE cancellation IS NULL
``` 
	
#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/f544fcad-4232-40aa-8d24-67b867f97e66)

***

###  9. What was the total volume of pizzas ordered for each hour of the day?

```sql
SELECT 
  HOUR(order_time) AS hour_of_day,
  COUNT(*) AS pizza_count
FROM customer_orders 
GROUP BY 1
ORDER BY 1 
``` 
	
#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/4f300646-9f87-4b61-b817-e2a62c884bb5)

***

###  10. What was the volume of orders for each day of the week?

```sql
SELECT 
  DAYNAME(order_time) AS day_of_week,
  COUNT(*) AS pizzas_ordered	 
FROM customer_orders 
GROUP BY 1
ORDER BY 2 DESC
``` 
	
#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/0d5a4c9f-e4d5-4b46-a302-f012435669ca)

***

Click [here](https://github.com/Pratham955/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/B.%20Runner%20and%20Customer%20Experience.md) to view the solution of B. Runner and Customer Experience!
