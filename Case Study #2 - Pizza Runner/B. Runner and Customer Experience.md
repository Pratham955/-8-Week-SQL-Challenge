# 🍕 Case Study #2 Pizza Runner

## Solution - B. Runner and Customer Experience

### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)

````sql
SELECT 
	TO_CHAR(registration_date,'W') AS registration_week,
	COUNT(*) AS runner_signup
FROM runners
GROUP BY 1
ORDER BY 1
````

#### Result set:

![image](https://user-images.githubusercontent.com/75075887/216775816-d0c30f53-4352-45dd-9724-ede787eee8e2.png)


### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?

````sql
SELECT 
	DISTINCT runner_id,
	ROUND(AVG(EXTRACT(MINUTE FROM pickup_time - order_time)),3) AS avg_time
FROM customer_orders
INNER JOIN runner_orders USING(order_id)
WHERE distance IS NOT NULL
GROUP BY 1
ORDER BY 1
````

#### Result set:

![image](https://user-images.githubusercontent.com/75075887/216776467-72954e63-1c98-49b5-9451-f0d67329bce4.png)


### 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?

````sql
WITH cte AS 
(
	SELECT 
		COUNT(order_id) AS pizzas_order_count,
		EXTRACT(MINUTE FROM pickup_time - order_time) AS prep_time
	FROM customer_orders
	INNER JOIN runner_orders USING(order_id)
	WHERE distance IS NOT NULL
	GROUP BY order_id,pickup_time,order_time
)

SELECT 
	pizzas_order_count,
	ROUND(AVG(prep_time)) AS prep_time
FROM cte
GROUP BY 1
ORDER BY 1
````

#### Result set:

![image](https://user-images.githubusercontent.com/75075887/216778512-f8f50616-4b62-472d-a50d-8b6a587760b4.png)

We can also find a relationship by using the corr() function.
```sql
SELECT 
	ROUND(corr(pizzas_order_count,prep_time)::NUMERIC,3) AS correlation
FROM cte
```
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/216778691-9e0c58a0-f870-4990-a3b2-d1cdce978708.png)

0.837 is highly correlated, so there is a positive relation between the number of orders and preparation time.

### 4. What was the average distance travelled for each customer?

````sql
SELECT 
	customer_id,
	ROUND(AVG(distance),2) AS avg_distance
FROM customer_orders
INNER JOIN runner_orders USING(order_id)
WHERE distance IS NOT NULL
GROUP BY 1
ORDER BY 1
````

#### Result set:
![image](https://user-images.githubusercontent.com/75075887/216780652-f20cd337-654a-41b5-8274-c7d897587523.png)


### 5. What was the difference between the longest and shortest delivery times for all orders?

````sql
SELECT 
	MAX(duration) - MIN(duration) AS delivery_time_difference
FROM customer_orders
INNER JOIN runner_orders USING(order_id)
WHERE distance IS NOT NULL
````

#### Result set:

![image](https://user-images.githubusercontent.com/75075887/216780748-2825f5c9-5f66-42d0-8504-0bb25a57c6cb.png)

### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?

````sql
SELECT 
	DISTINCT runner_id,
	customer_id,
	distance,
	duration,
	ROUND(distance/(duration/60.0),2) AS avg_speed
FROM customer_orders
INNER JOIN runner_orders USING(order_id)
WHERE distance IS NOT NULL
ORDER BY 1,5
````

#### Result set:

![image](https://user-images.githubusercontent.com/75075887/216781179-eaff174c-2107-4b4b-97a4-81e1948ba6af.png)

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
ORDER BY 1
````

#### Result set:

![image](https://user-images.githubusercontent.com/75075887/216782060-e1adae2d-a286-4831-8459-29523d110789.png)

***
Click [here](https://github.com/manaswikamila05/8-Week-SQL-Challenge/blob/main/Case%20Study%20%23%202%20-%20Pizza%20Runner/C.%20Ingredient%20Optimisation.md) to view the  solution of C. Ingredient Optimisation!
