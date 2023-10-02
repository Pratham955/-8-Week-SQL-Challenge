## Customer Orders Table
I am replacing the missing and null (lowercase) values with NULL to maintain data consistency in the exclusions and extras column.
```sql
UPDATE customer_orders
SET exclusions = CASE WHEN exclusions IN ('','null') THEN NULL ELSE exclusions END,
  	extras = CASE WHEN extras IN ('','null') THEN NULL ELSE extras END
```
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/a364b88b-87c7-452c-ba69-74f82d8aef9e)

## Runner Orders Table
1. I have replaced the null (lowercase) values of the pickup_time, distance, duration, and cancellation columns with NULL
2. Remove "km", "mins", "minute," and "minutes" from the distance and duration column
```sql
UPDATE runner_orders
SET pickup_time = CASE WHEN pickup_time = 'null' THEN NULL ELSE pickup_time END,
    distance = CASE WHEN distance LIKE '%km' THEN TRIM(REPLACE(distance, 'km', '')) WHEN distance = 'null' THEN NULL ELSE distance END,
    duration = CASE WHEN duration LIKE '%min%' THEN TRIM(LEFT(duration,2)) WHEN duration = 'null' THEN NULL ELSE duration END,
    cancellation = CASE WHEN cancellation IN ('','null') THEN NULL ELSE cancellation END
```
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/742d52f4-8130-47a6-a630-3f5e5c344aa5)

Then change the datatype of pickup_time to TIMESTAMP, distance to NUMERIC, and duration to INT
```sql
ALTER TABLE runner_orders
MODIFY COLUMN pickup_time TIMESTAMP;

ALTER TABLE runner_orders
MODIFY COLUMN distance NUMERIC;

ALTER TABLE runner_orders
MODIFY COLUMN duration INT;
```
![image](https://github.com/Pratham955/8-Week-SQL-Challenge/assets/75075887/0c574630-dcaa-4b79-b6ac-0e4ce74a18f9)

