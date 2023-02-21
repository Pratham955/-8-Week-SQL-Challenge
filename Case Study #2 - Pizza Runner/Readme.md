# 🍕 Case Study #2 - Pizza Runner
<p align="center">
<img src="https://user-images.githubusercontent.com/75075887/216649188-c4c730c3-fab8-4b3a-9034-3b1124739e1e.png" alt="Image" width="500" height="500">

View the case study [here](https://8weeksqlchallenge.com/case-study-2/)

## 📚 Table of Contents
- [Business Task](#business-task)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Key learnings](#key-learnings)
  
***

## Business Task
Danny requires our assistance to clean his data and apply some basic calculations to direct his runners better and optimize Pizza Runner’s operations to expand his new Pizza Empire so that he can get seed funding.

## Entity Relationship Diagram

![image](https://user-images.githubusercontent.com/75075887/216654172-421f7dcb-088a-4120-b136-b0f35925cc8e.png)


## Key learnings
- Using **to_char()** function to extract the day from the timestamp for grouping
- Learn about the **corr(x,y)** function to find the relationship between two variables
- Learn two functions to split a string and produce a column with all string parts, each in a separate row
  - **unnest(string_to_array(text, delimiter)**: First, the string_to_array(text, delimiter) function splits the string and converts it into a text array by the             specified delimiter. Then unnest() function produces a column of array elements as rows.
  - **regexp_split_to_table(text, delimiter)**: It directly splits the string into a column of split parts with a given delimiter.
- Learn about the **string_agg()** function to concatenate a list of strings which is separated by a delimiter
- Learn two functions to determine the location in the string where the substring is being matched:
  - **strpos(string, substring)**
  - **position(substring IN string)**
- Learn to join multiple **CTEs** and **temporary tables** 
- Learn to use **aggregate functions** with **CASE statements** and **subquery**
  
 
  
 
Click [here](https://github.com/Pratham955/8-Week-SQL-Challenge/blob/main/Case%20Study%20%231%20-%20Danny's%20Diner/Solutions.md) to view the solutions of the case study!

