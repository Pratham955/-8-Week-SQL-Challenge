# 🍕 Case Study #2 Pizza Runner

## Solution - D. Pricing and Ratings


###  1. If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees?

```sql
SELECT
	SUM(CASE WHEN pizza_name LIKE 'M%' THEN 12 ELSE 10 END) AS total_revenue
FROM customer_orders
INNER JOIN pizza_names USING(pizza_id)
INNER JOIN runner_orders USING(order_id)
WHERE distance IS NOT NULL
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/218274403-f64e7357-4f7f-4d11-9709-bd02773a4809.png)

***

###  2. What if there was an additional $1 charge for any pizza extras? Add cheese is $1 extra

```sql
-- cte 2 is for extracting the order_id, customer_id, and aggregated topping_name of extras by joining t2 and pizza_toppings.
WITH cte2 AS 
( SELECT 
    order_id, 
    customer_id,
    STRING_AGG(topping_name, ',') AS topping_name
  FROM t2
  INNER JOIN pizza_toppings ON t2.sep_extras = pizza_toppings.topping_id
  GROUP BY 1,2
)

SELECT
  SUM(CASE WHEN pizza_name LIKE 'M%' THEN 12 ELSE 10 END) + COUNT(*) - COUNT(DISTINCT customer_orders.order_id) 
  + SUM(DISTINCT CASE WHEN topping_name LIKE '%Cheese%' THEN 1 END) AS total_revenue
FROM customer_orders
INNER JOIN pizza_names USING(pizza_id)
INNER JOIN runner_orders USING(order_id)
LEFT JOIN cte2 ON customer_orders.order_id = cte2.order_id AND customer_orders.customer_id = cte2.customer_id
WHERE distance IS NOT NULL
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/218275205-c2165227-9a68-430a-af67-6fb12691b9cf.png)

***

###  3. The Pizza Runner team now wants to add an additional ratings system that allows customers to rate their runner, how would you design an additional table for this new dataset - generate a schema for this new table and insert your own data for ratings for each successful customer order between 1 to 5.

```sql
SELECT
	DISTINCT order_id,
	CASE WHEN EXTRACT(Minute FROM pickup_time - order_time) + duration < 30 THEN '5'
	 	 WHEN EXTRACT(Minute FROM pickup_time - order_time) + duration BETWEEN 30 AND 45 THEN '4'
		 WHEN EXTRACT(Minute FROM pickup_time - order_time) + duration > 45 THEN '2'
		 WHEN cancellation = 'Restaurant Cancellation' THEN '1'
		 ELSE 'No Rating'
	END AS rating
FROM customer_orders
INNER JOIN runner_orders USING(order_id)
ORDER BY 1
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/218275385-ae94f166-712b-4e45-949e-fb77bf0e1873.png)

***

###  4. Using your newly generated table - can you join all of the information together to form a table which has the following information for successful deliveries?
- customer_id
- order_id
- runner_id
- rating
- order_time
- pickup_time
- Time between order and pickup
- Delivery duration
- Average speed
- Total number of pizzas

```sql
-- The previous query is used for cte.
WITH cte AS 
( SELECT
	DISTINCT order_id,
	CASE WHEN EXTRACT(Minute FROM pickup_time - order_time) + duration < 30 THEN '5'
	 	 WHEN EXTRACT(Minute FROM pickup_time - order_time) + duration BETWEEN 30 AND 45 THEN '4'
		 WHEN EXTRACT(Minute FROM pickup_time - order_time) + duration > 45 THEN '2'
		 WHEN cancellation = 'Restaurant Cancellation' THEN '1'
		 ELSE 'No Rating'
	END AS ratings
  FROM customer_orders
  INNER JOIN runner_orders USING(order_id)
)

SELECT 
	order_id,
	customer_id,
	runner_id,
	ratings,
	order_time,
	pickup_time,
	pickup_time - order_time AS time_diff,
	duration,
	ROUND(AVG(distance/(duration/60.0)),2) AS speed,
	COUNT(*) AS num_of_pizzas
FROM cte
INNER JOIN customer_orders USING(order_id)
INNER JOIN runner_orders USING(order_id)
GROUP BY 1,2,3,4,5,6,8
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/218275587-16d9b156-ee00-4da5-8d8d-f5e2e9b5f823.png)

***

###  5. If a Meat Lovers pizza was $12 and Vegetarian $10 fixed prices with no cost for extras and each runner is paid $0.30 per kilometre traveled - how much money does Pizza Runner have left over after these deliveries?

```sql
SELECT
  SUM(CASE WHEN pizza_name LIKE 'M%' THEN 12 ELSE 10 END) 
  - 0.30 * ( SELECT SUM(distance) 
             FROM 
              ( SELECT DISTINCT order_id,
                       distance
                FROM customer_orders
                INNER JOIN runner_orders USING(order_id)
                WHERE distance IS NOT NULL
              ) as sum_distance
            ) AS profit
FROM customer_orders
INNER JOIN pizza_names USING(pizza_id)
INNER JOIN runner_orders USING(order_id)
WHERE distance IS NOT NULL
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/218276384-aa7e218f-609d-44a3-9720-eb3c526254a6.png)

***

Click [here](https://github.com/Pratham955/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/E.%20Bonus%20Questions.md) to view the solution of E. Bonus Questions!
