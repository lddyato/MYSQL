#  Joining Tables with Inner Joins

* inner joins only output the data from rows that have equivalent values in both tables being joined. 
```python

%load_ext sql
%sql mysql://studentuser:studentpw@mysqlserver/dognitiondb
%sql USE dognitiondb
```

## step 1: Inner Joins between 2 tables
Tables in relational databases are linked through primary keys and sometimes other fields that are common to multiple tables 
<img src="https://duke.box.com/shared/static/xazeqtyq6bjo12ojvgxup4bx0e9qcn5d.jpg">

```sql
# First, start by adding all the columns we want to examine to the SELECT statement:
SELECT dog_guid AS DogID, user_guid AS UserID, AVG(rating) AS AvgRating, 
       COUNT(rating) AS NumRatings, breed, breed_group, breed_type
# then list all the tables from which the fields we are interested in come, separated by commas (with no comma at the end of the list):
FROM dogs, reviews
# then add the other restrictions:
GROUP BY user_guid
HAVING NumRatings >= 10
ORDER BY AvgRating DESC
LIMIT 200
```
* The query as written does not tell the database how the two tables are related. 
* As a consequence, rather than match up the two tables according to the values in the user_id and/or dog_id column, the database will do the only thing it knows how to do which is output every single combination of the records in the dogs table with the records in the reviews table. 
* In other words, every single row of the dogs table will get paired with every single row of the reviews table. This is known as a **Cartesian product**. 
* Not only will it be a heavy burden on the database to output a table that has the full length of one table multiplied times the full length of another (and frustrating to you, because the query would take a very long time to run), the output would be close to useless.
```sql
# tell the database how to relate the tables in the WHERE clause:
SELECT d.dog_guid AS DogID, d.user_guid AS UserID, AVG(r.rating) AS AvgRating, 
       COUNT(r.rating) AS NumRatings, d.breed, d.breed_group, d.breed_type
FROM dogs d, reviews r
WHERE d.dog_guid=r.dog_guid
GROUP BY d.user_guid
HAVING NumRatings >= 10
ORDER BY AvgRating DESC
LIMIT 200

# OR to be very careful and exclude any incorrect dog_guid or user_guid entries, you can include both shared columns in the WHERE clause:
SELECT d.dog_guid AS DogID, d.user_guid AS UserID, AVG(r.rating) AS AvgRating, 
       COUNT(r.rating) AS NumRatings, d.breed, d.breed_group, d.breed_type
FROM dogs d, reviews r
WHERE d.dog_guid=r.dog_guid AND d.user_guid=r.user_guid
GROUP BY d.user_guid
HAVING NumRatings >= 10
ORDER BY AvgRating DESC
LIMIT 200
```
### Note:
If you accidentally request a Cartesian product from datasets with billions of rows, you could be waiting for your query output for days (and will probably get in trouble with your database administrator). So **always remember to tell the database how to join your tables!**
```sql
# extract the user_guid, dog_guid, breed, breed_type, and breed_group for all animals who completed the "Yawn Warm-up" game
SELECT d.user_guid AS UserID, d.dog_guid AS DogID, d.breed, d.breed_type, d.breed_group
FROM dogs d, complete_tests c
WHERE d.dog_guid=c.dog_guid AND test_name='Yawn Warm-up';
```

## step 2: Joining More than 2 Tables
* list all the fields you want to extract in the SELECT statement, 
* specify which table they came from in the SELECT statement, 
* list all the tables from which you will need to extract the fields in the FROM statement, 
* and then tell the database how to connect the tables in the WHERE statement.

```sql
# Extract the user_guid, user's state of residence, user's zip code, dog_guid, breed, breed_type, 
# and breed_group for all animals who completed the "Yawn Warm-up" game
SELECT c.user_guid AS UserID, u.state, u.zip, d.dog_guid AS DogID, d.breed, d.breed_type, d.breed_group
FROM dogs d, complete_tests c, users u
WHERE d.dog_guid=c.dog_guid 
   AND c.user_guid=u.user_guid
   AND c.test_name="Yawn Warm-up";
# This query focuses the relationships primarily on the complete_tests table. However, it turns out that our Dognition dataset 
# has only NULL values in the user_guid column of the complete_tests table.
#  If you were to execute the query above, you would not get an error message, but your output would have 0 rows
SELECT d.user_guid AS UserID, u.state, u.zip, d.dog_guid AS DogID, d.breed, d.breed_type, d.breed_group
FROM dogs d, complete_tests c, users u
WHERE d.dog_guid=c.dog_guid 
   AND d.user_guid=u.user_guid
   AND c.test_name="Yawn Warm-up";
```
### Note
Joins are very resource intensive, so try not to join unnecessarily. In general, the more joins you have to execute, the slower your query performance will be.

## Examples
```sql
# extract the user_guid, membership_type, and dog_guid of all the golden retrievers who completed at least 1 Dognition test
SELECT DISTINCT d.user_guid AS UserID, u.membership_type, d.dog_guid AS DogID, d.breed
FROM dogs d, complete_tests c, users u
WHERE d.dog_guid=c.dog_guid
  AND d.user_guid=u.user_guid
  AND d.breed="golden retriever";

#  How many unique Golden Retrievers who live in North Carolina are there in the Dognition database?
SELECT COUNT(DISTINCT d.dog_guid) AS numdogs, d.user_guid AS UserID, d.breed, u.state
FROM dogs d, users u
WHERE d.user_guid=u.user_guid
  AND u.state="NC"
  AND d.breed="golden retriever"
ORDER BY numdogs DESC;
GROUP BY state
HAVING state="NC";

# How many unique customers within each membership type provided reviews?
SELECT COUNT(DISTINCT u.user_guid) AS numUser, u.membership_type
FROM reviews r, users u
WHERE u.user_guid=r.user_guid AND r.rating IS NOT NULL
GROUP BY u.membership_type;

# For which 3 dog breeds do we have the greatest amount of site_activity data, (as defined by non-NULL values in script_detail_id)
SELECT COUNT(s.script_detail_id) AS numid, d.breed
FROM dogs d, site_activities s
WHERE d.dog_guid=s.dog_guid AND d.user_guid=s.user_guid AND s.script_detail_di IS NOT NULL
GROUP BY d.breed
ORDER BY numid DESC;
```
