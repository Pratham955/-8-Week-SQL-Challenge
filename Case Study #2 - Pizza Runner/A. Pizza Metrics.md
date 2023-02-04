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
![image](https://user-images.githubusercontent.com/75075887/216674031-f798d28b-3135-4060-ac3f-139e8aec4c94.png)

***

###  2. How many unique customer orders were made?

```sql
SELECT 
  COUNT(DISTINCT order_id) AS unique_customer_order
FROM customer_orders_temp;
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/216674490-da862e47-55f4-4f93-9aaa-d411988b125d.png)

***

###  3. How many successful orders were delivered by each runner?

```sql
SELECT 
	runner_id,
	COUNT(*) AS successful_orders
FROM runner_orders
WHERE distance IS NOT NULL
GROUP BY 1
ORDER BY 1
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/216674983-69510b99-8dfd-479d-8ea3-8b1658d5d47b.png)

***

###  4. How many of each type of pizza was delivered?

```sql
SELECT 
	pizza_name,
	COUNT(*) AS pizzas_delivered 
FROM customer_orders
INNER JOIN pizza_names USING(pizza_id)
INNER JOIN runner_orders USING(order_id)
WHERE distance IS NOT NULL
GROUP BY 1
ORDER BY 1
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/216675408-fa798f55-1b6c-47c3-9a79-ffc3f1d6fe23.png)

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
![image](https://user-images.githubusercontent.com/75075887/216675743-cc95d4c2-62c8-44ee-aac2-97b19a92e666.png)

***

###  6. What was the maximum number of pizzas delivered in a single order?

```sql
SELECT 
	COUNT(*) AS max_num_pizza_order
FROM customer_orders
INNER JOIN runner_orders USING(order_id)
WHERE distance IS NOT NULL
GROUP BY order_id
ORDER BY 1 DESC
LIMIT 1
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/216676722-0832bf1b-3d1b-4a3b-ade4-04f6f69d8879.png)

***

###  7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

```sql
SELECT 
	customer_id,
	COUNT(CASE WHEN exclusions IS NOT NULL OR extras IS NOT NULL THEN 1 END) AS atleast_one_change,
	COUNT(CASE WHEN exclusions IS NULL AND extras IS NULL THEN 1 END) AS no_change
FROM customer_orders
INNER JOIN runner_orders USING(order_id)
WHERE distance IS NOT NULL
GROUP BY 1
ORDER BY 1
``` 

#### Result set:
![image](https://user-images.githubusercontent.com/75075887/216677940-3a75b199-91fa-4e0b-ab67-1b64300f1423.png)6

***

###  8. How many pizzas were delivered that had both exclusions and extras?

```sql
SELECT 
	COUNT(*) AS both_exclusions_extras
FROM customer_orders
INNER JOIN runner_orders USING(order_id)
WHERE distance IS NOT NULL AND exclusions IS NOT NULL AND extras IS NOT NULL
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/216679113-10a03411-c89d-43a1-9c13-72e1426a6f4e.png)

***

###  9. What was the total volume of pizzas ordered for each hour of the day?

```sql
SELECT 
	EXTRACT(HOUR FROM order_time) AS hour_of_day,
	COUNT(*) AS pizza_count
FROM customer_orders
GROUP BY 1
ORDER BY 1
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/216679926-bd13c8e1-c69a-4847-acac-994fb09d82af.png)

***

###  10. What was the volume of orders for each day of the week?

```sql
SELECT 
	TO_CHAR(order_time, 'Day') AS day_of_week,
	COUNT(*) AS pizzas_ordered	 
FROM customer_orders
GROUP BY 1
ORDER BY 2 DESC
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/216683908-393b2ece-d616-4693-836a-e9c133a1aadc.png)

***

Click [here](https://github.com/Pratham955/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/B.%20Runner%20and%20Customer%20Experience.md) to view the solution of B. Runner and Customer Experience!
