# MySQL Subquery
A MySQL subquery is a query that is nested inside another query such as `SELECT`, `INSERT`, `UPDATE` or `DELETE`. 
In addition, a MySQL subquery can be nested inside another subquery.
A MySQL subquery is also called an **inner query** while the query that contains the subquery is called an **outer query**.

## The main reasons to use subqueries
* Sometimes they are the most logical way to retrieve the information you want
* They can be used to isolate each logical part of a statement, which can be helpful for troubleshooting long and complicated queries
* Sometimes they run faster than joins, easier read than joins.

**Subqueries must be enclosed in parentheses. Subqueries have a couple of rules that joins don't:**
* ORDER BY phrases cannot be used in subqueries (although ORDER BY phrases can still be used in outer queries that contain subqueries).
* Subqueries in SELECT or WHERE clauses that return more than one row must be used in combination with operators that are explicitly designed to handle multiple values, such as the IN operator. Otherwise, subqueries in SELECT or WHERE statements can output no more than 1 row.* 

## Why would you use subqueries?
* "On the fly calculations" (or, doing calculations as you need them) > < = !=
* Testing membership: IN, NOT IN, EXISTS, and NOT EXISTS operators
* Accurate logical representations of desired output and Derived Tables


```sql
# look at just the data from rows whose durations were greater than the average
SELECT *
FROM exam_answers 
WHERE TIMESTAMPDIFF(minute,start_time,end_time) >
    (SELECT AVG(TIMESTAMPDIFF(minute,start_time,end_time)) AS AvgDuration
     FROM exam_answers
     WHERE TIMESTAMPDIFF(minute,start_time,end_time)>0);

# use a subquery to extract all the data from exam_answers that had test durations that were greater than the average duration for the "Yawn Warm-Up" game?
SELECT *
FROM exam_answers 
WHERE TIMESTAMPDIFF(minute,start_time,end_time) >
    (SELECT AVG(TIMESTAMPDIFF(minute,start_time,end_time)) AS AvgDuration
     FROM exam_answers
     WHERE TIMESTAMPDIFF(minute,start_time,end_time)>0 and test_name="yawn Warm-Up");
```     

### IN and NOT IN
Subqueries can also be useful for assessing whether groups of rows are members of other groups of rows. To use them in this capacity, we need to know about and practice the IN, NOT IN, EXISTS, and NOT EXISTS operators.
```sql
# Use an IN operator to determine how many entries in the exam_answers tables are from the "Puzzles", "Numerosity", or "Bark Game" tests. 
SELECT COUNT(*)
FROM exam_answers
WHERE subcategory_name IN ('Puzzles','Numerosity','Bark Game');

# Use a NOT IN operator to determine how many unique dogs in the dog table are NOT in the "Working", "Sporting", or "Herding" breeding groups
SELECT COUNT(DISTINCT dog_guid)
FROM dogs
WHERE breed_group NOT IN ('Working','Sporting','Herding');
```



### EXISTS and NOT EXISTS
EXISTS and NOT EXISTS perform similar functions to IN and NOT IN, but EXISTS and NOT EXISTS can only be used in subqueries. 
* unlike IN/NOT IN statements, EXISTS/NOT EXISTS are logical statements. 
* Rather than returning raw data, per se, EXISTS/NOT EXISTS statements return a value of TRUE or FALSE. 
* As a practical consequence, EXISTS statements are often written using an asterisk after the SELECT clause rather than explicit column names. The asterisk is faster to write, and since the output is just going to be a logical true/false either way, it does not matter whether you use an asterisk or explicit column names.
```sql
# retrieve a list of all the users in the users table who were also in the dogs table
SELECT DISTINCT u.user_guid AS uUserID
FROM users u
WHERE EXISTS (SELECT d.user_guid
              FROM dogs d 
              WHERE u.user_guid =d.user_guid);
OR:
SELECT DISTINCT u.user_guid AS uUserID
FROM users u
WHERE EXISTS (SELECT *
              FROM dogs d 
              WHERE u.user_guid =d.user_guid);
# The results would be equivalent to an inner join with GROUP BY query.

#  determine the number of unique users in the users table who were NOT in the dogs table using a NOT EXISTS clause? 
SELECT DISTINCT u.user_guid AS uUserID
FROM users u
WHERE NOT EXISTS (SELECT *
              FROM dogs d 
              WHERE u.user_guid =d.user_guid);
```
3) Accurate logical representations of desired output and Derived Tables
```sql
# a list of each dog a user in the users table owns, with its accompanying breed information whenever possible
SELECT u.user_guid AS uUserID, d.user_guid AS dUserID, d.dog_guid AS dDogID, d.breed
FROM users u LEFT JOIN dogs d
   ON u.user_guid=d.user_guid
   
# Once we saw the "exploding rows" phenomenon due to duplicate rows, we wrote a follow-up query in Question 7 to assess how many rows would be outputted per user_id when we left joined the users table on the dogs table
SELECT u.user_guid AS uUserID, d.user_guid AS dUserID, count(*) AS numrows
FROM users u LEFT JOIN dogs d
   ON u.user_guid=d.user_guid
GROUP BY u.user_guid
ORDER BY numrows DESC

# the method we used to arrive at this was not very pretty or logically satisfying. Rather than joining many duplicated rows and fixing the results later with the GROUP BY clause, it would be much more elegant if we could simply join the distinct UserIDs in the first place. 
SELECT DistinctUUsersID.user_guid AS uUserID, d.user_guid AS dUserID, count(*) AS numrows
FROM (SELECT DISTINCT u.user_guid 
      FROM users u) AS DistinctUUsersID 
LEFT JOIN dogs d
   ON DistinctUUsersID.user_guid=d.user_guid
GROUP BY DistinctUUsersID.user_guid
ORDER BY numrows DESC
```
Queries that include subqueries always run the innermost subquery first, and then run subsequent queries sequentially in order from the innermost query to the outermost query.

























Let’s take a look at the following subquery that returns employees who locate in the offices in the USA.

* The subquery returns all offices codes of the offices that locate in the USA.
* The outer query selects the last name and first name of employees whose office code is in the result set returned by the subquery.

<img src="http://www.mysqltutorial.org/wp-content/uploads/2013/02/mysql-subquery.gif">

You can use a subquery anywhere that you use an expression. 
In addition, you must enclose a subquery in parentheses.

## Step 1: MySQL subquery within a WHERE clause
<img src="http://www.mysqltutorial.org/wp-content/uploads/2013/05/payments_table.png">

### comparison operators = > <
```sql
# returns the customer who has the maximum payment.
SELECT customerNumber,
       checkNumber,
       amount
FROM payments
WHERE amount = (
 SELECT MAX(amount) 
        FROM payments
);
```
<img src="http://www.mysqltutorial.org/wp-content/uploads/2013/02/mysql-subquery-with-equal-operator.png">

```sql
# use a subquery to calculate the average payment using the AVG aggregate function. 
# Then, in the outer query, you query payments that are greater than the average payment
SELECT customerNumber,
       checkNumber,
    amount
FROM payments
WHERE amount > (
 SELECT AVG(amount) 
    FROM payments
);
```
<img src="http://www.mysqltutorial.org/wp-content/uploads/2013/02/mysql-subquery-with-greater-than-operator.png">


If a subquery returns more than one value, you can use other operators such as IN or NOT IN operator in the WHERE clause.
<img src="http://www.mysqltutorial.org/wp-content/uploads/2009/12/customers_orders_tables.png">

```sql
#  find customer who has not ordered any products
SELECT customername
FROM customers
WHERE customerNumber NOT IN(
 SELECT DISTINCT customernumber
 FROM orders
);
```

When a subquery is used with EXISTS or NOT EXISTS operator, a subquery returns a Boolean value of TRUE or FALSE. The subquery acts as an existence check.
```sql
# select a list of customers who have at least one order with total sales greater than 10K.
SELECT
 customerName
FROM
 customers
WHERE
 EXISTS (
 SELECT
 priceEach * quantityOrdered
 FROM
 orderdetails
 WHERE
 priceEach * quantityOrdered > 10000
 GROUP BY
 orderNumber
 );
 ```

## Step 2: MySQL subquery in FROM clause
When you use a subquery in the `FROM` clause, the result set returned from a subquery is used as a table. 
This table is referred to as a **derived table** or **materialized subquery**.
```sql
# finds the maximum, minimum and average number of items in sale orders
SELECT
 MAX(items),
 MIN(items),
 FLOOR(AVG(items))
FROM
 (
 SELECT
 orderNumber,
 COUNT(orderNumber) AS items
 FROM
 orderdetails
 GROUP BY
 orderNumber
 ) AS lineitems;
 ```
<img src="http://www.mysqltutorial.org/wp-content/uploads/2013/02/mysql-subquery-from-clause-example.png">

## Step 3: MySQL correlated subquery
* In the previous examples, you notice that the subquery is independent. It means you can execute the subquery as a single query.
* A correlated subquery is a subquery that uses the information from the outer query. In other words, a correlated subquery depends on the outer query. 
* A correlated subquery is evaluated once for each row in the outer query.

```sql
# select products whose buy prices are greater than the average buy price of all products for a specific product line.
# The inner query executes for every product line because the product line is changed for every row. 
# Hence, the average buy price will also change.
SELECT
 productname,
 buyprice
FROM
 products AS p1
WHERE
 buyprice > (
 SELECT
 AVG(buyprice)
 FROM
 products
 WHERE
 productline = p1.productline
 )
```
<img src="http://www.mysqltutorial.org/wp-content/uploads/2013/02/MySQL-correlated-subquery-example.png">
