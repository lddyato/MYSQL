# Joining Tables with Outer Joins
* Inner joins
```sql
#inner join of traditional syntax
SELECT d.dog_guid AS DogID, d.user_guid AS UserID, AVG(r.rating) AS AvgRating, COUNT(r.rating) AS NumRatings, d.breed, d.breed_group, d.breed_type
FROM dogs d JOIN reviews r
  ON d.dog_guid=r.dog_guid AND d.user_guid=r.user_guid
GROUP BY d.user_guid
HAVING NumRatings > 9
ORDER BY AvgRating DESC
LIMIT 200
# JOIN represents INNER JOIN
```
note:You could also write "INNER JOIN" instead of "JOIN" but the default in MySQL is that JOIN will mean inner join, so including the word "INNER" is optional.
* Outer joins
 + left joins
 + right joins 
 + full outer joins (recall that full outer joins are NOT supported in MySQL). 
 
 <img src="https://duke.box.com/shared/static/4s89rmm8a75tep4puyqt1tdlhic0stzx.jpg" width = "500" height = "500" >
```python
%load_ext sql
%sql mysql://studentuser:studentpw@mysqlserver/dognitiondb
%sql USE dognitiondb
```
## 1. Left and Right Joins
* When we use the traditional join syntax to write inner joins, the order you enter the tables in your query doesn't matter. In outer joins, however, the order matters a lot. 
* A left outer join will include all of the rows of the table to the left of the LEFT JOIN clause. 
* A right outer join will include all of the rows of the table to the right of the RIGHT JOIN clause. 
```sql
# left joins
SELECT r.dog_guid AS rDogID, d.dog_guid AS dDogID, r.user_guid AS rUserID, d.user_guid AS dUserID, AVG(r.rating) AS AvgRating, COUNT(r.rating) AS NumRatings, d.breed, d.breed_group, d.breed_type
FROM reviews r LEFT JOIN dogs d
  ON r.dog_guid=d.dog_guid AND r.user_guid=d.user_guid
WHERE r.dog_guid IS NOT NULL
GROUP BY r.dog_guid
HAVING NumRatings >= 10
ORDER BY AvgRating DESC;

# right joins
SELECT r.dog_guid AS rDogID, d.dog_guid AS dDogID, r.user_guid AS rUserID, d.user_guid AS dUserID, AVG(r.rating) AS AvgRating, COUNT(r.rating) AS NumRatings, d.breed, d.breed_group, d.breed_type
FROM dogs d RIGHT JOIN reviews r
  ON r.dog_guid=d.dog_guid AND r.user_guid=d.user_guid
WHERE r.dog_guid IS NOT NULL
GROUP BY r.dog_guid
HAVING NumRatings >= 10
ORDER BY AvgRating DESC;

# query a list of only the dog_guids that were NOT in the dogs table:
SELECT r.dog_guid AS rDogID, d.dog_guid AS dDogID, r.user_guid AS rUserID, d.user_guid AS dUserID, AVG(r.rating) AS AvgRating, COUNT(r.rating) AS NumRatings, d.breed, d.breed_group, d.breed_type
FROM reviews r LEFT JOIN dogs d
  ON r.dog_guid=d.dog_guid AND r.user_guid=d.user_guid
WHERE d.dog_guid IS NULL
GROUP BY r.dog_guid
HAVING NumRatings >= 10
ORDER BY AvgRating DESC;

# How would you use a left join to retrieve a list of all the unique dogs in the dogs table, and retrieve a count of how many 
# tests each one completed? Include the dog_guids and user_guids from the dogs and complete_tests tables in your output.
SELECT d.dog_guid AS dDogID, d.user_guid AS dUserID, 
c.user_guid AS cUserID, c.dog_guid AS cDogID, COUNT(test_name) AS numtests
FROM dogs d LEFT JOIN complete_tests c
  ON d.dog_guid=c.dog_guid AND d.user_guid=c.user_guid
GROUP BY d.dog_guid;                                     # 35050 rows

#  Repeat the query in the above question, but intentionally use the dog_guids from the completed_tests table 
# to group your results instead of the dog_guids from the dogs table.
SELECT d.dog_guid AS dDogID, d.user_guid AS dUserID, 
c.dog_guid AS cDogID, c.user_guid AS cUserID, COUNT(test_name) AS numtests
FROM dogs d LEFT JOIN complete_tests c
  ON d.dog_guid=c.dog_guid 
GROUP BY c.dog_guid;                                     # 17987 rows
#"AND d.user_guid=c.user_guid" should be deleted in ON clause, or 
# the output is 1 row, maybe because of the data problem in the c table
```
This time your query ran successfully, but you retrieved many fewer DogIDs because the GROUP BY clause grouped your results according to the dog_guids in the completed_tests table rather than the dog_guid table. As a result, even though you implemented your join correctly, **all of the dog_guids that were in the dogs table but not in the completed_tests table got rolled up into one row of your output where completed_tests.dogs_guid = NULL**. This is a good opportunity to remind ourselves about the differences between SELECT/GROUP BY and COUNT DISTINCT.

```sql
# Write a query using COUNT DISTINCT to determine how many distinct dog_guids there are in the completed_tests table.
SELECT COUNT(DISTINCT dog_guid) AS numDogs
FROM complete_tests;                     #17,986
```
### Note
* The result of your **COUNT DISTINCT** clause should be 17,986 which is one row less than the number of rows you retrieved from your query in Question 4. 
* That's because **COUNT DISTINCT** does **NOT** count **NULL** values, while **SELECT/GROUP BY** clauses **roll up NULL** values into one group. 
* **If you want to infer the number of distinct entries from the results of a query using joins and GROUP BY clauses, remember to include an "IS NOT NULL" clause to ensure you are not counting NULL values.**

```sql
# extract all of the breed information of every dog a user_guid in the users table owns. 
# If a user_guid in the users table does not own a dog, we want that information as well. 
# Include the dog_guid from the dogs table, and user_guid from both the users and dogs tables in your output.
SELECT d.breed, d.breed_type, d. breed_group, d.dog_guid AS dDogID, d.user_guid AS dUserID, u.user_guid AS uUserID
FROM users u LEFT JOIN dogs d
  ON u.user_guid=d.user_guid;         #35050 distinct dog_guids in the dogs table (output is 952557 rows)

# Adapt the query you wrote above so that it counts the number of rows the join will output per user_id. 
# Sort the results by this count in descending order. 
# Remember that if you include dog_guid or breed fields in this query, 
# they will be randomly populated by only one of the values associated with a user_guid.
SELECT d.breed, d.dog_guid AS dDogID, d.user_guid AS dUserID, 
  u.user_guid AS uUserID, COUNT(*) AS numrows
FROM users u LEFT JOIN dogs d
  ON u.user_guid=d.user_guid
GROUP BY uUserID
ORDER BY numrows DESC;                # 33,193 rows
```
breed	|dDogID	|dUserID|	uUserID|	numrows
|-----|--------|------|-------|
Shih Tzu|	fd7bfb52-7144-11e5-ba71-058fbc01cf0b|	ce7b75bc-7144-11e5-ba71-058fbc01cf0b|	ce7b75bc-7144-11e5-ba71-058fbc01cf0b|	913138
Shih Tzu|	fd423714-7144-11e5-ba71-058fbc01cf0b|	ce225842-7144-11e5-ba71-058fbc01cf0b|	ce225842-7144-11e5-ba71-058fbc01cf0b|	442
Shih Tzu|	fd40bd62-7144-11e5-ba71-058fbc01cf0b|	ce2258a6-7144-11e5-ba71-058fbc01cf0b|	ce2258a6-7144-11e5-ba71-058fbc01cf0b|	320
This query told us that user 'ce7b75bc-7144-11e5-ba71-058fbc01cf0b' would be associated with 913,138 rows in the output of the outer join we designed!    
We are going to work with the second user_guid in the output you just generated, 'ce225842-7144-11e5-ba71-058fbc01cf0b', because it would be associated with 442 output rows, and 442 rows are much easier to work with than 913,138.
```sql
# How many rows in the users table are associated with user_guid 'ce225842-7144-11e5-ba71-058fbc01cf0b'?
SELECT COUNT(user_guid)
FROM users 
WHERE user_guid="ce225842-7144-11e5-ba71-058fbc01cf0b";   # 17 rows are exact duplicates of each other
```
|COUNT(user_guid)|
|----|
|17|
```sql
#  Examine all the rows in the dogs table that are associated with user_guid 'ce225842-7144-11e5-ba71-058fbc01cf0b'?
SELECT COUNT(*)
FROM dogs
WHERE user_guid='ce225842-7144-11e5-ba71-058fbc01cf0b';  
# 26 rows with many entries that have "Shih Tzu" in breed and "190" in  weight.
```
|COUNT(*)|
|------|
|26|
since there were multiple rows that had the same user_guid in the users table, **each one of these rows got paired up with each row in the dogs table that had the same user_guid.** The result was 442 rows, because 17 (instances of the user_guid in the users table) x 26 (instances of the user_guid in the dogs table) = 442.

The important things I want you to remember from this example of joins with duplicates are that duplicate rows and table relationships that have table-to-table mappings of greater than 1 have multiplicative effects on your query results, due to the way relational databases combine tables. If you write queries that aggregate over a lot of joined tables, it can be very difficult to catch issues that output results you don't intend, because the aggregated results will hide clues from you. To prevent this from happening, I recommend you adopt the following practices:
* Avoid making assumptions about your data or your analyses. For example, rather than assume that all the values in a column are unique just because some documentation says they should be, check for yourself!
* Always look at example outputs of your queries before you strongly interpret aggregate calculations. Take extra care to do this when your queries require joins.
* When your queries require multiple layers of functions or joins, examine the output of each layer or join first before you combine them all together.
* Adopt a healthy skepticsm of all your data and results. If you see something you don't expect, make sure you explore it before interpreting it strongly or incorporating it into other analyses.

## 2. Full outer join
* Full outer joins include all of the rows in both tables in an ON clause, regardless of whether there is a value that links the row of one table with a row in the other table. 
* As with left or right joins, whenever a value in a row does not have a matching value in the joined table, NULLs will be entered for all values in the joined table.
* Outer joins are used very rarely. The most practical application is if you want to export all of your raw data to another program for visualization or analysis. 
* The syntax for outer joins is the same as for inner joins, but you replace the word "inner" with " full outer":
```sql
SELECT r.dog_guid AS rDogID, d.dog_guid AS dDogID, r.user_guid AS rUserID, d.user_guid AS dUserID, AVG(r.rating) AS AvgRating, COUNT(r.rating) AS NumRatings, d.breed, d.breed_group, d.breed_type
FROM reviews r FULL OUTER JOIN dogs d
  ON r.dog_guid=d.dog_guid AND r.user_guid=d.user_guid
WHERE r.dog_guid IS NOT NULL
GROUP BY r.dog_guid
ORDER BY AvgRating DESC;
```
* **HOWEVER! MySQL does not support full outer joins.**
* If you wanted to imitate a full outer join in mySQL, you could follow one of the methods described at this website:
<http://www.xaprb.com/blog/2006/05/26/how-to-write-full-outer-join-in-mysql/>

## Examples
**Question 10:** How would you write a query that used a left join to return the number of distinct user_guids that were in the users table, but not the dogs table (your query should return a value of 2226)?
```sql
SELECT COUNT(DISTINCT u.user_guid) 
FROM users u LEFT JOIN dogs d
  ON u.user_guid=d.user_guid
WHERE d.user_guid IS NULL;
```

|COUNT(DISTINCT u.user_guid)|
|----|
|2226|
 

**Question 11:** How would you write a query that used a right join to return the number of distinct user_guids that were in the users table, but not the dogs table (your query should return a value of 2226)?
```sql
SELECT COUNT(DISTINCT u.user_guid) 
FROM dogs d RIGHT JOIN users u
  ON u.user_guid=d.user_guid
WHERE d.user_guid IS NULL;
```
 
|COUNT(DISTINCT u.user_guid)|
|----|
|2226|

**Question 12:** Use a left join to create a list of all the unique dog_guids that are contained in the site_activities table, but not the dogs table, and how many times each one is entered. Note that there are a lot of NULL values in the dog_guid of the site_activities table, so you will want to exclude them from your list. (Hint: if you exclude null values, the results you get will have two rows with words in their site_activities dog_guid fields instead of real guids, due to mistaken entries)
```sql
SELECT s.dog_guid, COUNT(*) AS snumdogs
FROM site_activities s LEFT JOIN dogs d
  ON s.dog_guid=d.dog_guid
WHERE d.dog_guid IS NULL AND s.dog_guid IS NOT NULL
GROUP BY s.dog_guid;
```
|dog_guid	|snumdogs|
|---------|--------|
|Membership	|5587|
|PortalContent	|12|
If delete the "GROUP BY snumdogs", then the output turns out to be
```sql
SELECT s.dog_guid, COUNT(*) AS snumdogs
FROM site_activities s LEFT JOIN dogs d
  ON s.dog_guid=d.dog_guid
WHERE d.dog_guid IS NULL AND s.dog_guid IS NOT NULL;
```
dog_guid|	snumdogs
-----|----
Membership|	5599




```sql
SELECT COUNT(DISTINCT dog_guid)
FROM site_activities;
```
|COUNT(DISTINCT dog_guid)|
|----|
|25234|
