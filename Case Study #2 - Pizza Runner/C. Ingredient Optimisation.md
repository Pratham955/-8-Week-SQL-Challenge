# 🍕 Case Study #2 Pizza Runner

## Solution - C. Ingredient Optimisation

### pizza_recipes_temp: Temporary table for Q1

````sql
CREATE TEMPORARY TABLE pizza_recipes_temp AS
SELECT 
	pizza_id,
	unnest(string_to_array(toppings, ','))::INT AS toppings
FROM pizza_recipes
````

#### Result set:

![image](https://user-images.githubusercontent.com/75075887/216979723-bf248eda-b057-4a69-8249-d7c63d0be0cd.png)


### 1. What are the standard ingredients for each pizza?

````sql
SELECT
	pizza_name,
 	STRING_AGG(topping_name, ', ') AS standard_ingredients
FROM pizza_names
INNER JOIN pizza_recipes_temp USING(pizza_id)
INNER JOIN pizza_toppings ON pizza_recipes_temp.toppings = pizza_toppings.topping_id
GROUP BY 1
````

#### Result set:

![image](https://user-images.githubusercontent.com/75075887/216980646-599c0aa8-cfbe-4e18-a605-aa3c2a63a4d7.png)


### 2. What was the most commonly added extra?

````sql
WITH cte AS 
(   SELECT 
    unnest(string_to_array(extras, ','))::INT AS topping_id
    FROM customer_orders
)

SELECT 
  topping_name, 
  COUNT(*) AS frequency
FROM CTE
INNER JOIN pizza_toppings USING(topping_id)
GROUP BY 1
ORDER BY 2 DESC
LIMIT 1
````

#### Result set:

![image](https://user-images.githubusercontent.com/75075887/216984553-e1b1a1bf-97b6-438f-aa77-0f7537594252.png)


### 3. What was the most common exclusion?

````sql
WITH cte AS 
(	SELECT 
		unnest(string_to_array(exclusions, ','))::INT AS topping_id
	FROM customer_orders
)

SELECT 
	topping_name, 
	COUNT(*) AS frequency
FROM CTE
INNER JOIN pizza_toppings USING(topping_id)
GROUP BY 1
ORDER BY 2 DESC
LIMIT 1
````

#### Result set:
![image](https://user-images.githubusercontent.com/75075887/216985166-253f14b0-ba85-4fce-b4bb-5c64bb2119ab.png)


### 4. Generate an order item for each record in the customers_orders table in the format of one of the following:
- Meat Lovers
- Meat Lovers - Exclude Beef
- Meat Lovers - Extra Bacon
- Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers

#### Temporary Tables t1 
```sql
CREATE TEMPORARY TABLE t1 AS
(   SELECT 
      order_id, 
      customer_id,
      unnest(string_to_array(exclusions, ','))::INT AS sep_exclusions
    FROM customer_orders
)
```
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/217000629-87caebf2-62f9-485c-baef-686c65f7408f.png)


#### Temporary Tables t2
```sql
CREATE TEMPORARY TABLE t2 AS
(   SELECT 
      order_id, 
      customer_id,
      regexp_split_to_table(extras, ',')::INT AS sep_extras
    FROM customer_orders
) 

```
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/217000762-16068507-32f9-4261-8417-340fe8860be7.png)


````sql
WITH cte1 AS 
(   SELECT 
      order_id,
      customer_id,
      STRING_AGG(DISTINCT topping_name, ', ') AS topping_name
    FROM t1
    INNER JOIN pizza_toppings ON t1.sep_exclusions = pizza_toppings.topping_id
    GROUP BY 1,2
)

,cte2 AS 
(   SELECT 
        order_id, 
        customer_id,
        STRING_AGG(topping_name, ', ') AS topping_name
    FROM t2
    INNER JOIN pizza_toppings ON t2.sep_extras = pizza_toppings.topping_id
    GROUP BY 1,2
)

SELECT 
	customer_orders.order_id,
	customer_orders.customer_id,
	CASE WHEN exclusions IS NULL AND extras IS NULL THEN pizza_name
	     WHEN exclusions IS NOT NULL AND extras IS NULL THEN CONCAT(pizza_name, ' - Exclude ', cte1.topping_name)
		 WHEN exclusions IS NULL AND extras IS NOT NULL THEN CONCAT(pizza_name, ' - Include ', cte2.topping_name)
		 ELSE CONCAT(pizza_name, ' - Exclude ',cte1.topping_name, ' - Include ',cte2.topping_name)
	END order_item	
FROM customer_orders
INNER JOIN pizza_names USING(pizza_id)
LEFT JOIN cte1 ON customer_orders.order_id = cte1.order_id AND customer_orders.customer_id = cte1.customer_id
LEFT JOIN cte2 ON customer_orders.order_id = cte2.order_id AND customer_orders.customer_id = cte2.customer_id
ORDER BY 1
````

#### Result set:

![image](https://user-images.githubusercontent.com/75075887/217001284-a4b7b902-95f3-4191-9a4b-8350b5bcdd83.png)


### 5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients
 - For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"

````sql

````

#### Result set:


### 6. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?

````sql

````

#### Result set:


***
Click [here](https://github.com/manaswikamila05/8-Week-SQL-Challenge/blob/main/Case%20Study%20%23%202%20-%20Pizza%20Runner/C.%20Ingredient%20Optimisation.md) to view the  solution of D. Pricing and Ratings!
