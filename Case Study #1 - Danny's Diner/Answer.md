# :ramen: :curry: :sushi: Case Study #1: Danny's Diner

## Case Study Questions

1. What is the total amount each customer spent at the restaurant?
2. How many days has each customer visited the restaurant?
3. What was the first item from the menu purchased by each customer?
4. What is the most purchased item on the menu and how many times was it purchased by all customers?
5. Which item was the most popular for each customer?
6. Which item was purchased first by the customer after they became a member?
7. Which item was purchased just before the customer became a member?
8. What is the total items and amount spent for each member before they became a member?
9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
***

###  1. What is the total amount each customer spent at the restaurant?

```sql
SELECT 
	customer_id,
	SUM(price) AS total_spending
FROM sales
INNER JOIN menu USING(product_id)
GROUP BY 1
ORDER BY 1
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/213462411-ef51e066-7843-46b0-bc0b-3a9c119c41e8.png)

***

###  2. How many days has each customer visited the restaurant?

```sql
SELECT 
	customer_id,
	COUNT(DISTINCT order_date) AS number_of_visited_days
FROM sales
GROUP BY 1
ORDER BY 1
``` 
	
#### Result set:

![image](https://user-images.githubusercontent.com/75075887/213481490-b6032c6a-fb8b-4077-9d36-7067374c9f2d.png)
***

###  3. What was the first item from the menu purchased by each customer?

```sql
WITH cte AS (
	SELECT
		customer_id,
		product_name,
		DENSE_RANK () OVER (PARTITION BY customer_id ORDER BY order_date) AS rank
	FROM sales
	INNER JOIN menu USING(product_id)
)

SELECT  
	customer_id,
	product_name
FROM cte
WHERE rank = 1
GROUP BY 1,2
``` 
	
#### Result set:

![image](https://user-images.githubusercontent.com/75075887/213482548-013cb2ab-f156-4683-a71a-3341e436eb03.png)

***

###  4. What is the most purchased item on the menu and how many times was it purchased by all customers?

```sql
SELECT 
	product_name, 
	COUNT(*) AS frequency
FROM sales
INNER JOIN menu USING(product_id)
GROUP BY 1
ORDER BY 2 DESC
LIMIT 1
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/213483194-31971f22-03b2-4cf2-a9b7-4265b38bf66a.png)

***

###  5. Which item was the most popular for each customer?

```sql
WITH cte AS (
	SELECT
		customer_id,
		product_name,
		RANK () OVER (PARTITION BY customer_id ORDER BY COUNT(*) DESC) AS rank,
		COUNT(*) AS order_frequency
	FROM sales
	INNER JOIN menu USING(product_id)
	GROUP BY 1,2 
)

SELECT  
	customer_id,
	product_name,
	order_frequency
FROM cte
WHERE rank = 1
``` 

#### Result set:
![image](https://user-images.githubusercontent.com/75075887/213484361-ace0e9f7-4f52-48d1-8e4b-560f23043e21.png)
***

###  6. Which item was purchased first by the customer after they became a member?

```sql
WITH cte AS (
	SELECT
		customer_id,
		product_name,
		DENSE_RANK () OVER (PARTITION BY customer_id ORDER BY order_date) AS rank
	FROM sales
	INNER JOIN members USING(customer_id)
	INNER JOIN menu USING(product_id)
	WHERE order_date >= join_date 
)

SELECT  
	customer_id,
	product_name
FROM cte
WHERE rank = 1
GROUP BY 1,2
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/213485710-4421d4a5-ff6f-483a-996a-86bae9a05ee9.png)
***

###  7. Which item was purchased just before the customer became a member?

```sql
WITH cte AS (
	SELECT
		customer_id,
		product_name,
		DENSE_RANK () OVER (PARTITION BY customer_id ORDER BY order_date DESC) AS rank
	FROM sales
	INNER JOIN members USING(customer_id)
	INNER JOIN menu USING(product_id)
	WHERE order_date < join_date 	
)

SELECT  
	customer_id,
	product_name
FROM cte
WHERE rank = 1
GROUP BY 1,2
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/213486447-faba02ea-e85f-42d2-a6c2-d862bb5ac46a.png)

***

###  8. What is the total items and amount spent for each member before they became a member?

```sql
SELECT
	customer_id,
	COUNT(DISTINCT product_name) AS total_items,
	SUM(price) AS amount_spent
FROM sales
INNER JOIN members USING(customer_id)
INNER JOIN menu USING(product_id)
WHERE order_date < join_date 
GROUP BY 1
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/213487097-48986e37-d1d5-4d83-9849-8dced434b32d.png)

***

###  9.  If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
```sql
SELECT
	customer_id, 
	SUM(CASE WHEN product_name = 'sushi' THEN price*20 ELSE price*10 END) AS customer_points
FROM sales
INNER JOIN menu USING(product_id)
GROUP BY 1
ORDER BY 1
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/213488313-5180ae47-c869-41f6-b8b0-d1c073548c2f.png)


***

###  10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January

```sql
SELECT
	customer_id, 
	SUM(CASE WHEN order_date < join_date THEN price*10
			 WHEN order_date <= (join_date + INTERVAL '6 DAY') THEN price*20
			 ELSE price*10
	END) AS customer_points
FROM sales
INNER JOIN menu USING(product_id)
INNER JOIN members USING(customer_id)
WHERE EXTRACT(MONTH FROM order_date) = 1
GROUP BY 1
ORDER BY 1
``` 

#### Result set:
![image](https://user-images.githubusercontent.com/75075887/213492525-89353eb0-ace4-48d3-9602-65dd28d35d00.png)
***

###  Bonus Questions

#### Join All The Things
Create basic data tables that Danny and his team can use to quickly derive insights without needing to join the underlying tables using SQL. Fill Member column as 'N' if the purchase was made before becoming a member and 'Y' if the after is amde after joining the membership.

```sql
SELECT
	customer_id, 
	order_date,
	product_name,
	price,
	CASE WHEN order_date >= join_date THEN 'Y' ELSE 'N' END AS member
FROM sales
LEFT JOIN menu USING(product_id) 					-- LEFT OR INNER JOIN FOR THIS LINE
LEFT JOIN members USING(customer_id)
ORDER BY 1,2
``` 
	
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/213493901-5049ee4b-451b-4be6-babd-c23c05a06dde.png)

***

#### Rank All The Things
Danny also requires further information about the ranking of customer products, but he purposely does not need the ranking for non-member purchases so he expects null ranking values for the records when customers are not yet part of the loyalty program.

```sql
WITH cte AS (
	SELECT
		customer_id, 
		order_date,
		product_name,
		price,
		CASE WHEN order_date >= join_date THEN 'Y' ELSE 'N' END AS member	
	FROM sales
	LEFT JOIN menu USING(product_id) 					-- LEFT OR INNER JOIN FOR THIS LINE
	LEFT JOIN members USING(customer_id)
	ORDER BY 1,2
)

SELECT 
	*, 
	CASE WHEN member = 'Y' THEN RANK () OVER (PARTITION BY customer_id,member ORDER BY order_date) 
	END AS rank
FROM cte
``` 

#### Result set:
![image](https://user-images.githubusercontent.com/75075887/213494964-5ff8a846-7e68-4910-be0a-4518f648f975.png)


***


Click [here]((https://github.com/Pratham955/8-Week-SQL-Challenge) to move back to the 8-Week-SQL-Challenge repository!


