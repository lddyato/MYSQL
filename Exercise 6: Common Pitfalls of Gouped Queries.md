# Common Pitfalls of Gouped Queries

There are two main reasons grouped queries can cause problems, especially in MySQL:     
* MySQL gives the user the benefit of the doubt, and assumes we don't make (at least some kinds of) mistakes. Unfortunately, we do make those mistakes.   
* We commonly think about data as spreadsheets that allow you make calculations across rows and columns, and that allow you to keep both raw and aggregated data in the same spreadsheet. Relational databases don't work that way.    

The way these issues cause problems are:     
* When we are working with a MySQL database, we incorrectly interpret non-sensical output from illogical queries, or   
* When we are working with a non-MySQL database platform, we struggle with trying to make queries that will never work because they ask for both aggregated and non-aggregated data.

```python
%load_ext sql
%sql mysql://studentuser:studentpw@mysqlserver/dognitiondb
%sql USE dognitiondb
```

## 1. Misinterpretations due to Aggregation Mismatches
```sql
#retrieve, for each breed_type in the Dognition database, the number of unique dog_guids associated with that breed_type and weight
SELECT breed_type, COUNT(DISTINCT dog_guid) AS NumDogs, **weight**
FROM dogs
GROUP BY breed_type;

# get the number of each kind of test completed in different months of the year.
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
FROM complete_tests
GROUP BY test_name
ORDER BY test_name ASC, Month ASC;

# GROUP BY Month instead of test_name
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
FROM complete_tests
GROUP BY **Month**
ORDER BY Month ASC, test_name ASC;
```
It looks like in both of these cases, MySQL is likely populating the unaggregated column with the first value it finds in that column within the first "group" of rows it is examining.  

*So how do we prevent this from happening?*

The only way to be sure how the MySQL database will summarize a set of data in a SELECT clause is to tell it how to do so with an aggregate function.

## 2. Errors due to Aggregation Mismatches
* It is important to note that the issues I described above are the consequence of **mismatching aggregate and non-aggregate functions through the GROUP BY clause** in MySQL, but other databases manifest the problem in a different way. 
* Other databases won't allow you to run the queries described above at all. When you try to do so, you get an error message that sounds something like:
**Column 'X' is invalid in the select list because it is not contained in either an aggregate function or the GROUP BY clause.**
* Especially when you are just starting to learn MySQL, these error messages can be confusing and infuriating. A good discussion of this problem can be found here:

<http://weblogs.sqlteam.com/jeffs/archive/2007/07/20/but-why-must-that-column-be-contained-in-an-aggregate.aspx>

As a way to prevent these logical mismatches or error messages, you will often hear a rule that **"every non-aggregated field that is listed in the SELECT list must be listed in the GROUP BY list."** You have just seen that this rule is not true in MySQL, which makes MySQL both more flexible and more tricky to work with. However, it is a useful rule of thumb for helping you avoid unknown mismatch errors.

## 3. By the way, even if you want to, there is no way to intentionally include aggregation mismatches in a single query
The order of SQL queries is meant to reflect the way we write sentences, but in actuality they are actually executed in a different order than we write them. The cartoon below shows the order we write the queries being sent to the database at the top of the funnel, and the order the database usually executes the queries on the conveyer belt.
<img src="https://duke.box.com/shared/static/irmwu5o8qcx4ctapjt5h0bs4nsrii1cl.jpg">

* This diagram shows you that **data are actually grouped before the SELECT expressions are applied**. That means that when a GROUP BY expression is included in an SQL query, there is no way to use a SELECT statement to summarize data that cross multiple groups. The data will have already been separated by the time the SELECT statement is applied. The only way to get the information you want is to write two separate queries. This concept can be difficult to understand when you start using SQL for the first time after exclusively using Excel, but soon you will be come accustomed to it.
* this diagram also shows you why some platforms and some queries in some platforms crash when you try to use aliases or derived fields in WHERE, GROUP BY, or HAVING clauses. 
**If the SELECT statement hasn't been run yet, the alias or derived fields won't be available** (as a reminder, some database systems--like MySQL--have found ways to overcome this issue). 
* On the other hand, **SELECT is executed before ORDER BY clauses**. That means most database systems should be able to use aliases and derived fields in ORDER BY clauses.

