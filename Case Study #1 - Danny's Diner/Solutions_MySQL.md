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
``` 
	
#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/e22a7d00-a97d-44c5-9389-2e4f576ea6eb)

***

###  2. How many days has each customer visited the restaurant?

```sql
SELECT 
    customer_id,
    COUNT(DISTINCT order_date) AS number_of_visited_days
FROM sales
GROUP BY 1
``` 
	
#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/f0adce42-d716-44dd-b408-e3d99a5386cd)

***

###  3. What was the first item from the menu purchased by each customer?

```sql
WITH first_item AS (
SELECT 
    customer_id,
    product_name,
    DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY order_date) AS ranking
FROM sales
INNER JOIN menu USING(product_id)
)

SELECT 
    DISTINCT customer_id,
    product_name
FROM first_item
WHERE ranking = 1
``` 
	
#### Result set:

![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/db16ef88-8f8c-4374-8fe9-4bd3b6327a68)

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
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/db8129bd-bb38-4e4d-aab8-8803b1a8afcf)

***

###  5. Which item was the most popular for each customer?

```sql
WITH most_popular AS (
SELECT 
    customer_id,
    product_name,
    DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY COUNT(*) DESC) AS ranking,
    COUNT(*) AS order_frequency
FROM sales
INNER JOIN menu USING(product_id)
GROUP BY 1,2
)

SELECT 
    customer_id,
    product_name,
    order_frequency
FROM most_popular
WHERE ranking = 1
``` 

#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/098a58ce-d01d-4e58-b8f2-11c5cce59f12)

***

###  6. Which item was purchased first by the customer after they became a member?

```sql
WITH cte AS (
SELECT 
    customer_id,
    product_name,
    DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY order_date) AS ranking
FROM sales
INNER JOIN members USING(customer_id)
INNER JOIN menu USING(product_id)
WHERE order_date >= join_date
)

SELECT
    customer_id,
    product_name
FROM cte
WHERE ranking = 1
``` 
	
#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/c36a52e7-82c9-48db-94d6-58f9139ce8fb)

***

###  7. Which item was purchased just before the customer became a member?

```sql
WITH cte AS (
SELECT 
    customer_id,
    product_name,
    DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY order_date DESC) AS ranking
FROM sales
INNER JOIN members USING(customer_id)
INNER JOIN menu USING(product_id)
WHERE order_date < join_date
)

SELECT
    customer_id,
    product_name
FROM cte
WHERE ranking = 1
``` 
	
#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/609cadc0-dd4e-43de-84bb-f4a65fd775cf)

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
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/d97f06fc-6ef2-4ca6-8711-2cf1513a9a0b)

***

###  9.  If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
```sql
SELECT 
    customer_id,
    SUM(CASE WHEN product_name ='sushi' THEN price*20 ELSE price*10 END) AS customer_points
FROM sales
INNER JOIN menu USING(product_id)
GROUP BY 1
``` 
	
#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/ddf609c2-ec7e-4cf6-8a6a-7c5d6b916b8c)


***

###  10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January

```sql
SELECT 
    customer_id,
    SUM(CASE WHEN product_name ='sushi' THEN price*20
        WHEN order_date between join_date AND join_date + 6 THEN price*20
        ELSE price*10
        END) AS customer_points
FROM sales
INNER JOIN members USING(customer_id)
INNER JOIN menu USING(product_id)
WHERE MONTH(order_date) = 1
GROUP BY 1
ORDER BY 1

``` 

#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/69dc9e93-487e-4e34-916c-aad6574df294)

#### Assumption:
- Before becoming a member, each $1 spent earns 10 points. However, for sushi, each $1 spent earns 20 points.
- From Day 1 to Day 7 (the first week of membership), each $1 spent for any item earns 20 points.
- From Day 8 to the last day of January 2021, each $1 spent earns 10 points. However, sushi continues to earn double the points at 20 points per $1 spent.


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
LEFT JOIN members USING(customer_id)
INNER JOIN menu USING(product_id)
``` 
	
#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/26159b5e-b1ea-4a4c-81f9-d5f99e639ea4)


***

#### Rank All The Things
Danny also requires further information about the ranking of customer products, but he purposely does not need the ranking for non-member purchases so he expects null ranking values for the records when customers are not yet part of the loyalty program.

```sql
SELECT 
    customer_id,
    order_date,
    product_name,
    price,
    CASE WHEN order_date >= join_date THEN 'Y' ELSE 'N' END AS member,
    CASE WHEN order_date >= join_date THEN DENSE_RANK() OVER (PARTITION BY customer_id,order_date >= join_date ORDER BY order_date)
    ELSE NULL END AS ranking
FROM sales
LEFT JOIN members USING(customer_id)
INNER JOIN menu USING(product_id)
``` 

#### Result set:
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/306ca2be-14a4-4d47-803d-df7c3be1497d)


***


Click [here](https://github.com/Pratham955/8-Week-SQL-Challenge) to move back to the 8-Week-SQL-Challenge repository!
