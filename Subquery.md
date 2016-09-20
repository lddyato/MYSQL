# subquery
A MySQL subquery is a query that is nested inside another query such as `SELECT`, `INSERT`, `UPDATE` or `DELETE`. 
In addition, a MySQL subquery can be nested inside another subquery.
A MySQL subquery is also called an **inner query** while the query that contains the subquery is called an **outer query**.

Let’s take a look at the following subquery that returns employees who locate in the offices in the USA.

* The subquery returns all offices codes of the offices that locate in the USA.
* The outer query selects the last name and first name of employees whose office code is in the result set returned by the subquery.
MySQL Subquery

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

## Step 2: MySQL subquery with IN and NOT IN operators
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















