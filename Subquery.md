# MySQL Subquery
A MySQL subquery is a query that is nested inside another query such as `SELECT`, `INSERT`, `UPDATE` or `DELETE`. 
In addition, a MySQL subquery can be nested inside another subquery.
A MySQL subquery is also called an **inner query** while the query that contains the subquery is called an **outer query**.

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

### IN and NOT IN
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

### EXISTS and NOT EXISTS
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
