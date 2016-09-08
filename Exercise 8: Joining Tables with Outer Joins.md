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
SELECT DISTINCT d.dog_guid AS dDogID, d.user_guid AS dUserID, 
c.user_guid AS cUserID, c.dog_guid AS cDogID, COUNT(test_name) AS numtests
FROM dogs d LEFT JOIN complete_tests c
  ON d.dog_guid=c.dog_guid AND d.user_guid=c.user_guid
GROUP BY d.dog_guid;                                     # 35050 rows

#  Repeat the query in the above question, but intentionally use the dog_guids from the completed_tests table 
# to group your results instead of the dog_guids from the dogs table.
SELECT DISTINCT d.dog_guid AS numDogs, d.user_guid AS dUserID, 
c.dog_guid AS cDogID, c.user_guid AS cUserID, COUNT(test_name) AS numtests
FROM dogs d LEFT JOIN complete_tests c
  ON d.dog_guid=c.dog_guid AND d.user_guid=c.user_guid
GROUP BY c.dog_guid;                           
```















```







