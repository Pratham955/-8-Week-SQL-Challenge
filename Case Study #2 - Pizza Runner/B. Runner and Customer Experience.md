# 🍕 Case Study #2 Pizza Runner

## Solution - B. Runner and Customer Experience

### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)

````sql
SELECT 
  WEEK(registration_date) AS registration_week,
  COUNT(*) AS runner_signup
FROM runners
GROUP BY 1
````

#### Result set:

![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/26da91eb-35eb-4a77-93cb-8f0e5f88d568)


### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?

````sql
SELECT 
  runner_id,
  ROUND(AVG(TIMESTAMPDIFF(MINUTE,order_time,pickup_time)),2) AS avg_time
FROM customer_orders
INNER JOIN runner_orders USING(order_id)
GROUP BY 1
````

#### Result set:

![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/8587b14c-43f0-4d88-a26d-bf3b6cfcafd1)


### 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?

````sql
WITH cte AS (
SELECT 
  order_id,
  COUNT(*) AS pizzas_order_count,
  TIMESTAMPDIFF(MINUTE,order_time,pickup_time) AS prep_time
FROM customer_orders
INNER JOIN runner_orders USING(order_id)
GROUP BY 1,order_time,pickup_time )

SELECT 
  pizzas_order_count,
  ROUND(AVG(prep_time)) AS prep_time
FROM cte
GROUP BY 1
````

#### Result set:

![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/0daf99b9-cb20-49e8-b478-3f72345b3237)

- On average, a single pizza order takes 12 minutes to prepare.
- An order with 3 pizzas takes 29 minutes at an average of 9.6 minutes per pizza.
- It takes 18 minutes to prepare an order with 2 pizzas, which is 9 minutes per pizza - making 2 pizzas in a single order is the best efficiency rate.


### 4. What was the average distance travelled for each customer?

````sql
SELECT 
  customer_id,
  ROUND(AVG(distance),2) AS avg_distance
FROM customer_orders
INNER JOIN runner_orders USING(order_id)
GROUP BY 1
ORDER BY 1
````

#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/e5a54fe8-780d-4319-bc54-9cc78dfa9506)


### 5. What was the difference between the longest and shortest delivery times for all orders? 

````sql
SELECT 
  MAX(duration + TIMESTAMPDIFF(MINUTE,order_time,pickup_time)) - MIN(duration + TIMESTAMPDIFF(MINUTE,order_time,pickup_time)) AS delivery_time_difference
FROM customer_orders
INNER JOIN runner_orders USING(order_id)
````

#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/a8ce16cc-ac9b-45e0-a2c1-a00d17610241)

- Assumed delivery time also includes pickup time

### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?

````sql
SELECT 
  DISTINCT runner_id    ,
  ROUND(distance/(duration/60),2) AS avg_speed
FROM runner_orders
INNER JOIN customer_orders USING(order_id)
WHERE distance IS NOT NULL
ORDER BY 1;
````

#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/7ac17a97-764a-43d6-a145-9830339ee811)

- Runner 1’s average speed varies from 37.50 km/h to 60 km/h.
- Runner 2’s average speed varies from 35.10 km/h to 93.60 km/h. Danny should examine Runner 2, as the average speed increase by 300%
- Runner 3’s average speed is 40 km/h, which is constant.

### 7. What is the successful delivery percentage for each runner?

````sql
SELECT  
	runner_id,
	ROUND(AVG(CASE WHEN distance IS NOT NULL THEN 100 ELSE 0 END)) AS success_percent
FROM runner_orders
GROUP BY 1
````

#### Result set:

![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/7ebf17af-d3bf-4f73-9631-d2f2b3ac2aca)

- Successful delivery cannot be solely attributed to runners as order cancellations are beyond their control.

***
Click [here](https://github.com/Pratham955/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/C.%20Ingredient%20Optimisation.md) to view the  solution of C. Ingredient Optimisation!

