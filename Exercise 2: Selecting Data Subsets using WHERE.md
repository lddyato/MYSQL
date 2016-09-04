# Exercise 2: Using WHERE to select specific data

```python
%load_ext sql
%sql mysql://studentuser:studentpw@mysqlserver/dognitiondb
%sql USE dognitiondb
```

## step 1: =,<,>,<=, >=, !=, <>
```python
#  know which Dognition customers received access to Dognition's first four tests for free. These customers have a 1 in the "free_start_user" column of the users table
%sql 
SELECT user_guid, free_start_user
FROM users
WHERE free_start_user=1;

# select the Dog IDs for the dogs in the Dognition data set that were DNA tested (these should have a 1 in the dna_tested field of the dogs table
%%sql
SELECT dog_guid, dna_tested
FROM dogs
WHERE dna_tested=1;
```

## step 2: BETWEEN & AND, OR
```python
# select the Dog IDs of dogs who weighed between 10 and 50 pounds
%%sql
SELECT dog_guid, weight
FROM dogs
WHERE weight BETWEEN 10 AND 50;

# select the Dog IDs of dogs who were "fixed" (neutered) OR DNA tested
%%sql
SELECT dog_guid, dog_fixed, dna_tested
FROM dogs
WHERE dog_fixed=1 OR dna_tested=1;

# select the Dog IDs of dogs who were fixed but NOT DNA tested, you could query:
%%sql
SELECT dog_guid, dog_fixed, dna_tested
FROM dogs
WHERE dog_fixed=1 AND dna_tested!=1

# query the User IDs of customers who bought annual subscriptions, indicated by a "2" in the membership_type field of the users table
%%sql
SELECT user_guid
FROM users
WHERE membership_type=2;
```

## step 3: using WHERE to interact with text data "strings" **IN**, **LIKE**
* WHERE breed LIKE ("s%") = the breed must start with "s", but can have any number of letters after the "s"
* WHERE breed LIKE ("%s") = the breed must end with "s", but can have any number of letters before the "s"
* WHERE breed LIKE ("%s%") = the breed must contain an "s" somewhere in its name, but can have any number of letters before or after the "s"

```python
# look at data from dogs of the breed "Golden Retrievers." 
%%sql
SELECT dog_guid, breed
FROM dogs
WHERE breed='golden retriever'; #MySQL accepts both double and single quotation marks

# look at all the data from Golden Retrievers and Poodles, you could certainly use the OR operator, 
but the IN operator would be even more efficient 
%%sql
SELECT dog_guid, breed
FROM dogs
WHERE breed IN ("golden retriever","poodle");

#The LIKE operator allows you to specify a pattern that the textual data you query has to match. 
For example, if you wanted to look at all the data from breeds whose names started with "s"
%%sql
SELECT dog_guid, breed
FROM dogs
WHERE breed LIKE ("s%");

# query all the data from customers located in the state of North Carolina (abbreviated "NC") or New York (abbreviated "NY")? 
%%sql
SELECT *
FROM users
WHERE state IN ("NC","NY");

# select all of the User IDs of customers who have female dogs whose breed includes the word "terrier" somewhere in its name 
%%sql
SELECT user_guid, gender, breed
FROM dogs
WHERE gender='female' AND breed LIKE ("%terrier%");
```

## step 4: using WHERE to interact with datetime data
* DATE - format YYYY-MM-DD
* DATETIME - format: YYYY-MM-DD HH:MI:SS
* TIMESTAMP - format: YYYY-MM-DD HH:MI:SS
* YEAR - format YYYY or YY
* TIMEDIFF or DATEDIFF: find the difference in time between two time stamps or dates

```python
# select data from only a single day of the week
%%sql
SELECT dog_guid, created_at
FROM complete_tests
WHERE DAYNAME(created_at)="Tuesday"

# select all the Dog IDs and time stamps of tests completed after the 15 of every month with this command that extracts the "DAY" date part out of each time stamp:
SELECT dog_guid, created_at
FROM complete_tests
WHERE DAY(created_at) > 15

# select all the Dog IDs and time stamps of completed tests from after February 4, 2014 by treating date entries as text clauses 
SELECT dog_guid, created_at
FROM complete_tests
WHERE created_at > '2014-02-04'

# select all the Dog IDs and time stamps of Dognition tests completed before October 15, 2015 
%%sql
SELECT dog_guid, created_at
FROM complete_tests
WHERE created_at<'2015-10-15';

# retrieve the Dog ID, subcategory_name, and test_name fields, in that order, 
of the first 10 reviews entered in the Reviews table to be submitted in 2014?
%%sql
SELECT dog_guid, subcategory_name, test_name
FROM reviews
WHERE YEAR(created_at)=2014
LIMIT 10;


# select the Dog ID, test name, and subcategory associated with each completed test for the first 100 tests entered in October, 2014?
%%sql
SELECT dog_guid, test_name, subcategory_name
FROM complete_tests
WHERE YEAR(created_at)="2014" and MONTH(created_at)=10
LIMIT 100;
```

## step 5: IS NULL and IS NOT NULL

```python
# select only the rows that have non-null data
SELECT user_guid
FROM users
WHERE free_start_user IS NOT NULL;

# select only the rows that have null data 
SELECT user_guid
FROM users
WHERE free_start_user IS NULL;

# select all the User IDs of customers who do not have null values in the State field of their demographic information 
%%sql
SELECT user_guid
FROM users
WHERE state IS NOT NULL;
```
