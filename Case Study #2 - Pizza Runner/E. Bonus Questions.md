# 🍕 Case Study #2 Pizza Runner

## Solution - E. Bonus Questions

If Danny wants to expand his range of pizzas - how would this impact the existing data design? Write an INSERT statement to demonstrate what would happen if a new Supreme pizza with all the toppings was added to the Pizza Runner menu?

Supreme pizza has all toppings on it.

![image](https://user-images.githubusercontent.com/77529445/168252509-fa26acf9-5442-439a-869f-d28f4e90b0ac.png)

We'd have to insert data into pizza_names and pizza_recipes tables

***

```sql
CREATE TEMP TABLE new_pizza_recipes AS
( SELECT *
  FROM pizza_recipes
)
 
INSERT INTO new_pizza_recipes 
VALUES (3, '1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12');
``` 
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/218276699-66e593c2-4554-4e62-8610-dcfd97b5ca36.png)

***
```sql
CREATE TEMP TABLE new_pizza_names AS
( SELECT *
  FROM pizza_names
)

INSERT INTO new_pizza_names 
VALUES (3, 'Supreme');
``` 
#### Result set:
![image](https://user-images.githubusercontent.com/75075887/218276837-f164b771-a5e6-44d7-a662-e86a2f7d8521.png)

***
```sql
SELECT * 
FROM new_pizza_names 
INNER JOIN new_pizza_recipes USING(pizza_id)
``` 

#### Result set:
![image](https://user-images.githubusercontent.com/75075887/218277007-308cbd0c-195a-4354-8b78-c000823c4a56.png)

***
Click [here](https://github.com/Pratham955/8-Week-SQL-Challenge) to move back to the 8-Week-SQL-Challenge repository!
