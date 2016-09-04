# Exercise 4: Summarizing Your Data
```python
%load_ext sql
%sql mysql://studentuser:studentpw@mysqlserver/dognitiondb
%sql USE dognitiondb
```
## step 1: the COUNT function
Two fundamental difference between COUNT(*) and COUNT(column_name)
* You cannot use DISTINCT with COUNT(*)
* When a column is included in a count function, null values are ignored in the count.  
When an asterisk is included in a count function, nulls are included in the count.

```sql
# how many rows are in our query output
SELECT COUNT(breed)
FROM dogs

# count the number of distinct breed names contained within all the entries in the breed column 
SELECT COUNT(DISTINCT breed)
FROM dogs

# When a column is included in the parentheses, null values are automatically ignored
SELECT COUNT(DISTINCT Dog_Guid)
FROM complete_tests

# how many individual dogs completed tests after March 1, 2014
SELECT COUNT(DISTINCT dog_guid)
FROM complete_tests
WHERE created_at > '2014-03-01'

# count the number of rows in the dogs table using COUNT(*)
SELECT COUNT(*)
FROM dogs          # output is 35050 rows

# count the number of rows in the exclude column of the dogs table:
SELECT COUNT(exclude)
FROM dogs          # output is 1025 rows

#  How many distinct dogs have an exclude flag in the dogs table (value will be "1")? 
SELECT COUNT(DISTINCT(dog_guid))
FROM dogs
WHERE exclude=1  # output is 853 rows
```

## step 2: the SUM function

```sql
# get the total number of NULL values in the exclude column
SELECT SUM(ISNULL(exclude))
FROM dogs
```

## step 3: the AVE, MIN, and MAX functions
```sql
#  retrieve the average, minimum, and maximum ratings given to "Eye Contact Game" game
SELECT test_name, 
AVG(rating) AS AVG_Rating, 
MIN(rating) AS MIN_Rating, 
MAX(rating) AS MAX_Rating
FROM reviews
WHERE test_name="Eye Contact Game";
```

|test_name | AVG_Rating |MIN_Rating |MAX_Rating|
|------|-----|-----|----|
|Memory versus Pointing| 3.5584| 0| 9 |

## step 4 Examples

```sql
# how much time it took to complete each test provided in the exam_answers table, in minutes
SELECT TIMESTAMPDIFF(MINUTE, start_time, end_time) AS duration
FROM exam_answers
LIMIT 2000;

# Include a column for Dog_Guid, start_time, and end_time in your query, and examine the output. Do you notice anything strange
SELECT TIMESTAMPDIFF(MINUTE, start_time, end_time) AS duration, dog_guid, start_time, end_time
FROM exam_answers
ORDER BY duration 
LIMIT 200;

# What is the average amount of time it took customers to complete all of the tests in the exam_answers table, if you do not exclude any data 
SELECT AVG(TIMESTAMPDIFF(MINUTE, start_time, end_time)) AS AVGduration
FROM exam_answers;

# What is the average amount of time it took customers to complete the "Treat Warm-Up" test, according to the exam_answers table 
SELECT AVG(TIMESTAMPDIFF(MINUTE, start_time, end_time)) AS AVGduration
FROM exam_answers
WHERE test_name = "Treat Warm-Up"

# How many possible test names are there in the exam_answers table?
SELECT COUNT(DISTINCT test_name) AS numtests
FROM exam_answers

# What is the minimum and maximum value in the Duration column of your query that included the data from the entire table?
SELECT 
MIN(TIMESTAMPDIFF(MINUTE, start_time, end_time)) AS minduration, 
MAX(TIMESTAMPDIFF(MINUTE, start_time, end_time)) AS maxduration
FROM exam_answers;

# How many of these negative Duration entries are there
SELECT COUNT(TIMESTAMPDIFF(MINUTE, start_time, end_time)) AS negduration 
FROM exam_answers
WHERE TIMESTAMPDIFF(MINUTE, start_time, end_time) < 0;

# How would you query all the columns of all the rows that have negative durations 
# so that you could examine whether they share any features that might give you clues about what caused the entry mistake?
SELECT * 
FROM exam_answers
WHERE TIMESTAMPDIFF(MINUTE, start_time, end_time) < 0;

# What is the average amount of time it took customers to complete all of the tests in the exam_answers table 
# when 0 and the negative durations are excluded from your calculation 
SELECT AVG(TIMESTAMPDIFF(MINUTE, start_time, end_time)) AS avgduration 
FROM exam_answers
WHERE TIMESTAMPDIFF(MINUTE, start_time, end_time) > 0;
```







