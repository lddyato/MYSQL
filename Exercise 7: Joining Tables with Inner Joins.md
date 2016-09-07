#  Joining Tables with Inner Joins

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

