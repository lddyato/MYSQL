# Exercise 3: Formatting Selected Data

```python
%load_ext sql
%sql mysql://studentuser:studentpw@mysqlserver/dognitiondb
%sql USE dognitiondb
```
## step 1: Use AS to change the tiles of the columns
```python
# change the name of the time stamp field of the completed_tests table from "created_at" to "time_stamp" in your output
%%sql
SELECT dog_guid, created_at AS time_stamp
FROM complete_tests

# Note that if you use an alias that includes a space, the alias must be surrounded in quotes
%%sql
SELECT dog_guid, created_at AS "time stamp"
FROM complete_tests

# make an alias for a table
%%sql
SELECT dog_guid, created_at AS "time stamp"
FROM complete_tests AS tests

# change the title of the "start_time" field in the exam_answers table to "exam start time" in a query output
%%sql
SELECT start_time AS "exam start time"
FROM exam_answers
```
## step 2: Use DISTINCT to remove duplicate rows
Note:
* if you use the DISTINCT clause on a column that has NULL values, 
MySQL will include one NULL value in the DISTINCT output from that column.
* When the DISTINCT clause is used with multiple columns in a SELECT statement, 
the combination of all the columns together is used to determine the uniqueness of a row in a result set.

```python
# select all the breeds of dogs by removing duplicate breed
%%sql
SELECT DISTINCT breed
FROM dogs;

# select all the possible combinations of states and cities in the users table
%%sql
SELECT DISTINCT state, city
FROM users;
# If you examine the query output carefully, you will see that there are many rows with California (CA) 
# in the state column and four rows that have Gainesville in the city column (Georgia, Arkansas, Florida, 
# and Virginia all have cities named Gainesville in our user table), 
# but no two rows have the same state and city combination.

# When you use the DISTINCT clause with the LIMIT clause in a statement, 
# MySQL stops searching when it finds the number of unique rows specified in the LIMIT clause, 
# not when it goes through the number of rows in the LIMIT clause.

# the first 5 different breeds, not the distinct breeds in the first 5 rows
%%sql
SELECT DISTINCT breed
FROM dogs LIMIT 5;

# list all the possible combinations of test names and subcategory names in complete_tests table
%%sql
SELECT DISTINCT test_name, subcategory_name
FROM complete_tests;
```

## step 3: Use ORDER BY to sort the output of your query
```python
# select the breeds of dogs in the dog table sorted in alphabetical order
# The default order is ASC.
%%sql
SELECT DISTINCT breed
FROM dogs 
ORDER BY breed

# sort the output in ascending order
%%sql
SELECT DISTINCT breed
FROM dogs 
ORDER BY breed DESC
```
Combining ORDER BY with LIMIT gives you an easy way to select the "top 10" and "last 10" in a list or column

```python
#  select the User IDs and Dog IDs of the 5 customer-dog pairs 
# who spent the least median amount of time between their Dognition tests
%%sql
SELECT DISTINCT user_guid, median_ITI_minutes
FROM dogs 
ORDER BY median_ITI_minutes
LIMIT 5

# or spent the least median amount of time between their Dognition tests
%%sql
SELECT DISTINCT user_guid, (median_ITI_minutes * 60) AS median_ITI_sec
FROM dogs 
ORDER BY median_ITI_sec DESC
LIMIT 5

#  find the User ID, Dog ID, and test name of the first 10 tests to ever be completed in the Dognition database
%%sql
SELECT user_guid, dog_guid, test_name
FROM complete_tests
ORDER BY created_at 
LIMIT 10

# select all the distinct User IDs of customers in the United States (abbreviated "US") 
# and sort them according to the states they live in in alphabetical order first  
%%sql
SELECT DISTINCT user_guid, state, membership_type
FROM users
WHERE country="US"
ORDER BY state ASC, membership_type ASC

#  only select rows that do not have null values in either the state or membership_type column
%%sql
SELECT DISTINCT user_guid, state, membership_type
FROM users
WHERE country="US" AND state IS NOT NULL and membership_type IS NOT NULL
ORDER BY state ASC, membership_type ASC

# get a list of all the subcategories of Dognition tests, in alphabetical order, with no test listed more than once
%%sql
SELECT DISTINCT subcategory_name
FROM complete_tests
ORDER BY subcategory_name 
```

## step 4: Export your query results to a text file

```python
breed_list = %sql SELECT DISTINCT breed FROM dogs ORDER BY breed;
breed_list.csv('breed_list.csv')

#  create a text file with a list of all the non-United States countries of Dognition customers with no country listed more than once?
non_us = %sql SELECT DISTINCT country FROM users where country != "US";
non_us.csv('non_us.csv')

# create a text file with a list of all the customers with yearly memberships 
# who live in the state of North Carolina (USA) and joined Dognition after March 1, 2014, 
# sorted so that the most recent member is at the top of the list.
subusers=%sql 
SELECT DISTINCT user_guid, state, created_at 
FROM users 
WHERE membership_type=2 AND state="NC" AND country="US" AND created_at > '2014-03-01' 
ORDER BY created_at DESC;
```

## step 5: REPLACE(), TRIM()
* **REPLACE(str,from_str,to_str)**
Returns the string str with all occurrences of the string from_str replaced by the string to_str. 
REPLACE() performs a case-sensitive match when searching for from_str.
* **TRIM()** returns a string after removing all prefixes or suffixes from the given string.
 + **Syntax**: `TRIM([{BOTH | LEADING | TRAILING} [remstr] FROM ] str)`
 + **BOTH**: 	Indicates that prefixes from both left and right are to be removed.
 + **LEADING**: Indicates that only leading prefixes are to be removed.
 + **TRAILING**: Indicates that only trailing prefixes are to be removed.
 + **remstr**: The string to be removed.
 + **FROM**: Keyword
 + **str**: The actual string from where remstr is to be removed.
 
```python
# replace any dashes included in the breed names with no character
%%sql
SELECT DISTINCT breed,
REPLACE(breed,'-','') AS breed_fixed
FROM dogs
ORDER BY breed_fixed

# TRIM()
%%sql
SELECT DISTINCT breed, TRIM(LEADING '-' FROM breed) AS breed_fixed
FROM dogs
ORDER BY breed_fixed
```

## step 6: UPPER()
```python
# select a list of these names in upper case, sorted in alphabetical order.
%%sql
SELECT DISTINCT UPPER(breed)
FROM dogs
ORDER BY breed 
```
