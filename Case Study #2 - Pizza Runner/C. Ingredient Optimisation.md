# 🍕 Case Study #2 Pizza Runner

## Solution - C. Ingredient Optimisation

### Temporary tables created to solve the below queries
-  **pizza_recipes_temp:** Splitting the character type "**toppings**" column values into rows of integer datatype for joining.  

````sql
SELECT 
  pizza_name, 
  GROUP_CONCAT(topping_name SEPARATOR ', ') AS topping_name
FROM pizza_recipes t
JOIN json_table(replace(json_array(t.toppings), ',', '","'),'$[*]' columns (topping_id TINYINT PATH '$')) j 
JOIN pizza_names USING(pizza_id)
JOIN pizza_toppings USING(topping_id)
GROUP BY 1
````

#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/87ef6778-ca5c-4228-8148-b163e0504d80)

- **customer_orders_temp:** Extracting the order_id, customer_id, exclusions (as integer), extras (as integer) and order_time from the customer_orders table. 

```sql
CREATE TEMPORARY TABLE customer_orders_temp AS
SELECT 
    order_id,
    customer_id,
    pizza_id,
    j1.exclusions,
    j2.extras,
    order_time
FROM customer_orders t
JOIN json_table(replace(json_array(t.exclusions), ',', '","'),'$[*]' columns (exclusions TINYINT PATH '$')) j1
JOIN json_table(replace(json_array(t.extras), ',', '","'),'$[*]' columns (extras TINYINT PATH '$')) j2;
```

### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/a5249c72-02e6-4ba4-bb6b-c190b64649b9)

### 1. What are the standard ingredients for each pizza?

````sql
SELECT
    *
FROM pizza_recipes_temp;
````

#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/87ef6778-ca5c-4228-8148-b163e0504d80)


### 2. What was the most commonly added extra?

````sql
SELECT 
    topping_name,
    COUNT(*) AS purchase_counts
FROM customer_orders_temp
JOIN pizza_toppings ON pizza_toppings.topping_id = customer_orders_temp.extras
GROUP BY 1
LIMIT 1;
````

#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/55954e6e-a870-458e-8bb9-744a8c6a3915)


### 3. What was the most common exclusion?

````sql
SELECT 
    topping_name,
    COUNT(*) AS purchase_counts
FROM customer_orders_temp
JOIN pizza_toppings ON pizza_toppings.topping_id = customer_orders_temp.exclusions
GROUP BY 1
LIMIT 1;
````

#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/675f2d88-2512-472e-b15d-09ce46556361)


### 4. Generate an order item for each record in the customers_orders table in the format of one of the following:
- Meat Lovers
- Meat Lovers - Exclude Beef
- Meat Lovers - Extra Bacon
- Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers

````sql
-- cte 1 is for extracting the order_id, customer_id, and aggregated topping_name of exclusions by joining t1 and pizza_toppings.

WITH cte1 AS 
(   SELECT 
      order_id,
      customer_id,
      STRING_AGG(DISTINCT topping_name, ', ') AS topping_name
    FROM t1
    INNER JOIN pizza_toppings ON t1.sep_exclusions = pizza_toppings.topping_id
    GROUP BY 1,2
)

-- cte 2 is for extracting the order_id, customer_id, and aggregated topping_name of extras by joining t2 and pizza_toppings.

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
-- cte 1 is for extracting the order_id, customer_id, and aggregated topping_name of exclusions by joining t1 and pizza_toppings.
WITH cte1 AS 
( SELECT 
	order_id,
	customer_id,
	STRING_AGG(DISTINCT topping_name, ', ') AS topping_name
  FROM t1
  INNER JOIN pizza_toppings ON t1.sep_exclusions = pizza_toppings.topping_id
  GROUP BY 1,2
)

-- cte 2 is for extracting the order_id, customer_id, and aggregated topping_name of extras by joining t2 and pizza_toppings.
,cte2 AS 
( SELECT 
  	order_id, 
  	customer_id,
  	STRING_AGG(topping_name, ', ') AS topping_name
  FROM t2
  INNER JOIN pizza_toppings ON t2.sep_extras = pizza_toppings.topping_id
  GROUP BY 1,2
)

-- cte is extracting for pizza_id and aggregated topping_name by joining pizza_recipes_temp and pizza_toppings.
, cte AS 
( SELECT 
	pizza_id,
	STRING_AGG(topping_name,', ') AS topping_name
  FROM pizza_recipes_temp
  INNER JOIN pizza_toppings ON pizza_recipes_temp.toppings = pizza_toppings.topping_id
  GROUP BY 1
)

SELECT 
	customer_orders.order_id,
	customer_orders.customer_id,
	exclusions,
	extras,
	pizza_id,
	CASE WHEN exclusions IS NULL AND extras IS NULL THEN CONCAT(pizza_name,': ',cte.topping_name)
		 WHEN exclusions IS NOT NULL AND extras IS NULL THEN CONCAT(pizza_name,': ',REPLACE(cte.topping_name,CONCAT(cte1.topping_name,', '),''))
		 WHEN exclusions IS NULL AND extras IS NOT NULL THEN 
		 CASE WHEN strpos( cte.topping_name, cte2.topping_name) > 0 THEN CONCAT(pizza_name,': ',REPLACE(cte.topping_name,cte2.topping_name,CONCAT('2x',cte2.topping_name)))
		 ELSE CONCAT(pizza_name,': ',cte.topping_name,', ',cte2.topping_name)
		 END
	ELSE 
		CASE WHEN cte1.topping_name LIKE '%,%' THEN REPLACE(REPLACE(CONCAT(pizza_name,': ',REPLACE(REPLACE(cte.topping_name,CONCAT(SPLIT_PART(cte1.topping_name,',',1),', '),''),CONCAT(SPLIT_PART(cte1.topping_name,',',2),','),'')),SPLIT_PART(cte2.topping_name,',',1),CONCAT('2x',SPLIT_PART(cte2.topping_name,',',1))),SPLIT_PART(cte2.topping_name,',',2),CONCAT(' 2x',LTRIM(SPLIT_PART(cte2.topping_name,',',2))))
		ELSE REPLACE(REPLACE(CONCAT(pizza_name,': ',REPLACE(cte.topping_name,CONCAT(cte1.topping_name,', '),'')),SPLIT_PART(cte2.topping_name,',',1),CONCAT('2x',SPLIT_PART(cte2.topping_name,',',1))),SPLIT_PART(cte2.topping_name,',',2),CONCAT(' 2x',LTRIM(SPLIT_PART(cte2.topping_name,',',2))))
		END 
	END AS order_item
FROM customer_orders
INNER JOIN cte USING(pizza_id)
INNER JOIN pizza_names USING(pizza_id)
LEFT JOIN cte1 ON customer_orders.order_id = cte1.order_id AND customer_orders.customer_id = cte1.customer_id
LEFT JOIN cte2 ON customer_orders.order_id = cte2.order_id AND customer_orders.customer_id = cte2.customer_id
ORDER BY 1
````

#### Result set:
![image](https://user-images.githubusercontent.com/75075887/218166001-1b32ccbc-2386-495d-83aa-06bb928d48e7.png)


### 6. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?

````sql
-- cte 1 is for extracting the order_id, customer_id, and aggregated topping_name of exclusions by joining t1 and pizza_toppings.
WITH cte1 AS 
( SELECT 
	order_id,
	customer_id,
	STRING_AGG(DISTINCT topping_name, ', ') AS topping_name
  FROM t1
  INNER JOIN pizza_toppings ON t1.sep_exclusions = pizza_toppings.topping_id
  GROUP BY 1,2
)

-- cte 2 is for extracting the order_id, customer_id, and aggregated topping_name of extras by joining t2 and pizza_toppings.
,cte2 AS 
( SELECT 
  	order_id, 
  	customer_id,
  	STRING_AGG(topping_name, ', ') AS topping_name
  FROM t2
  INNER JOIN pizza_toppings ON t2.sep_extras = pizza_toppings.topping_id
  GROUP BY 1,2
)

-- cte is extracting for pizza_id and aggregated topping_name by joining pizza_recipes_temp and pizza_toppings.
, cte AS 
( SELECT 
	pizza_id,
	STRING_AGG(topping_name,', ') AS topping_name
  FROM pizza_recipes_temp
  INNER JOIN pizza_toppings ON pizza_recipes_temp.toppings = pizza_toppings.topping_id
  GROUP BY 1
)

SELECT 
	TRIM( unnest( string_to_array(	
        	CASE WHEN exclusions IS NULL AND extras IS NULL THEN cte.topping_name
		WHEN exclusions IS NOT NULL AND extras IS NULL THEN REPLACE(cte.topping_name,CONCAT(cte1.topping_name,','),'')
		WHEN exclusions IS NULL AND extras IS NOT NULL THEN CONCAT(cte.topping_name,',',cte2.topping_name)
		ELSE 
			CASE WHEN cte1.topping_name LIKE '%,%' THEN CONCAT(REPLACE(REPLACE(cte.topping_name,CONCAT(SPLIT_PART(cte1.topping_name,',',1),','),''),CONCAT(SPLIT_PART(cte1.topping_name,',',2),','),''),',',SPLIT_PART(cte2.topping_name,',',1),',',SPLIT_PART(cte2.topping_name,',',2))
			ELSE CONCAT(REPLACE(cte.topping_name,CONCAT(cte1.topping_name,','),''),',',SPLIT_PART(cte2.topping_name,',',1),',',SPLIT_PART(cte2.topping_name,',',2))
			END 
		END , ','))) AS topping_name ,
	COUNT(*) AS frequency
FROM customer_orders
INNER JOIN cte USING(pizza_id)
LEFT JOIN cte1 ON customer_orders.order_id = cte1.order_id AND customer_orders.customer_id = cte1.customer_id
LEFT JOIN cte2 ON customer_orders.order_id = cte2.order_id AND customer_orders.customer_id = cte2.customer_id
GROUP BY 1
ORDER BY 2 DESC
````

#### Result set:
![image](https://user-images.githubusercontent.com/75075887/218171025-698bc62c-83c3-4ec6-a66a-3b85d8961703.png)


***
Click [here](https://github.com/Pratham955/8-Week-SQL-Challenge/blob/main/Case%20Study%20%232%20-%20Pizza%20Runner/D.%20Pricing%20and%20Ratings.md) to view the  solution of D. Pricing and Ratings!
