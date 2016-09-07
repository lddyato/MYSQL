# Summaries of Groups of Data

```python
%load_ext sql
%sql mysql://studentuser:studentpw@mysqlserver/dognitiondb
%sql USE dognitiondb
```

## Step 1: GOURP BY

```sql
# query the average rating for each of the 40 tests in the Reviews table
SELECT test_name, AVG(rating) AS AVG_Rating
FROM reviews
GROUP BY test_name;

# get the total number of tests completed each month
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
FROM complete_tests
GROUP BY Month;

# get the total number of each type of test completed each month
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
FROM complete_tests
GROUP BY test_name, Month;

# MySQL allows you to use aliases in a GROUP BY clause, but some database systems do not.
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
FROM complete_tests
GROUP BY test_name, MONTH(created_at);

# Different database servers might default to ordering the outputs in a certain way, 
# but you shouldn't rely on that being the case.
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
FROM complete_tests
GROUP BY test_name, Month
ORDER BY test_name ASC, Month ASC;
# OR
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
FROM complete_tests
GROUP BY 1, 2
ORDER BY 1 ASC, 2 ASC;

# calculates the number of distinct female and male dogs in each breed group of the Dogs table, 
# sorted by the total number of dogs in descending order 
SELECT breed_group, gender, COUNT(DISTINCT dog_guid)AS numdogs
FROM dogs
GROUP BY gender, breed_group
ORDER BY numdogs DESC;

# OR
SELECT breed_group, gender, COUNT(DISTINCT dog_guid)AS numdogs
FROM dogs
GROUP BY 2, 1
ORDER BY 3 DESC;
```

## step 2: the HAVING clause
Just like you can query subsets of rows using the WHERE clause, you can query subsets of aggregated groups using the HAVING clause. However, wheras the expression that follows a WHERE clause has to be applicable to each row of data in a column, the expression that follows a HAVING clause has to be applicable or computable using a group of data.
```sql
# examine the number of tests completed only during the winter holiday months of November and December
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
FROM complete_tests
WHERE MONTH(created_at)=11 OR MONTH(created_at)=12
GROUP BY 1, 2
ORDER BY 3 DESC;

# output only the test-month pairs that had at least 20 records in them, you would add a HAVING clause, 
# because the stipulation of at least 20 records only makes sense and is only computable at the aggregated group level:
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
FROM complete_tests
WHERE MONTH(created_at)=11 OR MONTH(created_at)=12
GROUP BY 1, 2
HAVING COUNT(created_at)>=20
ORDER BY 3 DESC;

# calculates the number of distinct female and male dogs in each breed group of the Dogs table,  
# (1) excludes the NULL and empty string entries in the breed_group field, 
# (2) excludes any groups that don't have at least 1,000 distinct Dog_Guids in them
# (3)sorted by the total number of dogs in descending order 
SELECT breed_group, gender, COUNT(DISTINCT dog_guid)AS numdogs
FROM dogs
WHERE breed_group IS NOT NULL AND breed_group!=""
GROUP BY 2, 1
HAVING numdogs>=1000
ORDER BY 3 DESC;
```

```sql
# outputs the average number of tests completed and average mean inter-test-interval for every breed type, 
# sorted by the average number of completed tests in descending order
SELECT breed_type, 
AVG(total_tests_completed) AS avgtottest,
AVG(mean_iti_days) AS avgdays, 
AVG(mean_iti_minutes) AS avgminutes 
FROM dogs
GROUP BY 1
ORDER BY 2 DESC;

# outputs the average amount of time (in hours) it took customers to complete each type of test 
# where any individual reaction times over 6000 minutes are excluded 
# and only average reaction times that are greater than 0 minutes are included
SELECT 
AVG(TIMESTAMPDIFF(HOUR, start_time, end_time)) AS avgtest, test_name
FROM exam_answers
WHERE TIMESTAMPDIFF(MINUTE, start_time, end_time) < 6000 AND TIMESTAMPDIFF(MINUTE, start_time, end_time) >0
GROUP BY test_name
ORDER BY avgtest DESC;

# outputs the total number of unique User_Guids in each combination of State and ZIP code (postal code) in the United States, 
# sorted first by state name in ascending alphabetical order, 
# and second by total number of unique User_Guids in descending order 
SELECT state, zip, country, COUNT(DISTINCT user_guid) AS numusers
FROM users
WHERE country="US"
GROUP BY state, zip
ORDER BY state ASC, numusers DESC;

# outputs the total number of unique User_Guids in each combination of State and ZIP code in the United States 
# that have at least 5 users, sorted first by state name in ascending alphabetical order, 
# and second by total number of unique User_Guids in descending order
SELECT state, zip, country, COUNT(DISTINCT user_guid) AS numusers
FROM users
WHERE country="US"
GROUP BY state, zip
HAVING numusers>=5
ORDER BY state ASC, numusers DESC;
```



