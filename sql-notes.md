### SQL JOINS

1. **INNER JOIN**

 ![inner join](https://www.w3schools.com/sql/img_innerjoin.gif)

Includes only the records that are equal in both of the tables 

```
SELECT c.customerName, c.customerId, o.orderId
FROM Customers c 
INNER JOIN orders o ON c.customerId = o.customerId

```
JOINING three tables

```
SELECT C.customerName, C.customerId, O.orderId, S.shipperId
FROM Orders O
((INNER JOIN Customers C ON C.customerId = O.customerId)
INNER JOIN Shippers S ON S.shipperId = O.shipperId)
```

2. **LEFT JOIN**

 ![left join](https://www.w3schools.com/sql/img_leftjoin.gif)

Includes all the record from the left table and the records that are similar in both the tables.

```
SELECT c.customerName, c.customerId, o.orderId
FROM Customers c 
LEFT  JOIN Orders o ON c.customerId = o.customerId
```


3. **RIGHT () JOIN**

 ![left join](https://www.w3schools.com/sql/img_rightjoin.gif)

RIGHT () JOIN: Returns all records from the right table, and the matched records from the left table

4. **FULL () JOIN**

 ![left join](https://www.w3schools.com/sql/img_fulljoin.gif)

FULL () JOIN: Returns all records when there is a match in either left or right table.

*Note* FULL OUTER JOIN and FULL JOIN are same. It also applies to INNER and OUTER

## Null Functions

COALESCE()
- Returns the first non null value in the list ``` SELECT COALESCE(NULL, 1, 2, 'W3Schools.com'); ```
- COALESCE() also lets you return an alternative value if an expression is NULL. 
```
SELECT ProductName, UnitPrice * (UnitsInStock + COALESCE(UnitsOnOrder, 0))
FROM Products;
```

### Groups 

The GROUP BY statement groups rows that have the same values into summary rows, like "find the number of customers in each country".

The GROUP BY statement is often used with aggregate functions (COUNT(), MAX(), MIN(), SUM(), AVG()) to group the result-set by one or more columns.

GROUP BY With JOIN Example
```
SELECT Shippers.ShipperName,COUNT(Orders.OrderID) AS NumberOfOrders 
FROM Orders
LEFT JOIN Shippers ON Orders.ShipperID = Shippers.ShipperID
GROUP BY ShipperName;
```
*Note* The HAVING clause was added to SQL because the WHERE keyword cannot be used with aggregate functions.

**Questions**

- Which table exactly is the "left" table and "right" table in a JOIN statement (SQL)?

 "Left" table is the one defined first in the sql query or  "left" is the result of everything that appears first in the whole FROM clause. Moreover because the = operator at the ON part of the JOIN clause is symmetric (if a = b then b = a) so the result is the same no matter the order.

So in the below example Customers is "left" and Orders is "right" and both the qeries are same.

```
SELECT c.customerName, c.customerId, o.orderId
FROM Customers c 
LEFT  JOIN Orders o ON c.customerId = o.customerId
```

```
SELECT c.customerName, c.customerId, o.orderId
FROM Customers c 
LEFT  JOIN Orders o ON  o.customerId = c.customerId
```

- How to Test for NULL Values?
It is not possible to test for NULL values with comparison operators, such as =, <, or <>.

We will have to use the IS NULL and IS NOT NULL operators instead.

