## Customer Orders Table
I am replacing the missing and null (lowercase) values with NULL to maintain data accuracy in the exclusions and extras column.
```sql
UPDATE customer_orders
SET exclusions = CASE WHEN exclusions IN ('','null') THEN NULL ELSE exclusions END,
  	extras = CASE WHEN extras IN ('','null') THEN NULL ELSE extras END
```
![image](https://user-images.githubusercontent.com/75075887/216663705-167da5de-dbcf-40e9-8853-a039dafb9387.png)

## Runner Orders Table
1. I have replaced the null (lowercase) values of the pickup_time, distance, duration, and cancellation columns with NULL
2. Remove "km", "mins", "minute," and "minutes" from the distance and duration column
```sql
UPDATE runner_orders
SET pickup_time = CASE WHEN pickup_time = 'null' THEN NULL ELSE pickup_time END,
    distance = CASE WHEN distance LIKE '%km' THEN RTRIM(LEFT(distance,LENGTH(distance)-2)) 
                    WHEN distance = 'null' THEN NULL ELSE distance END,
    duration = CASE WHEN duration LIKE '%min%' THEN RTRIM(LEFT(duration,2))
                    WHEN duration = 'null' THEN NULL ELSE duration END,
    cancellation = CASE WHEN cancellation IN ('','null') THEN NULL ELSE cancellation END
```
![image](https://user-images.githubusercontent.com/75075887/216671364-a50f15ca-4322-4e51-9bb7-52eeb38d4769.png)

Then change the datatype of pickup_time to TIMESTAMP, distance to NUMERIC, and duration to INT
```sql
ALTER TABLE runner_orders
ALTER COLUMN pickup_time TYPE TIMESTAMP
USING pickup_time::TIMESTAMP ;

ALTER TABLE runner_orders
ALTER COLUMN distance TYPE NUMERIC
USING distance::NUMERIC;

ALTER TABLE runner_orders
ALTER COLUMN duration TYPE INT 
USING duration::INT;
```
![image](https://user-images.githubusercontent.com/75075887/216672403-65773a03-5b1b-4521-9337-7b35cf2d6c47.png)
