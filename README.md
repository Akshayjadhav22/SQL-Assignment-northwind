/* IMPORTANT INSTRUCTIONS FOR LEARNERS
1) DO NOT CHANGE THE ORDER OF COLUMNS.
2) YOUR QUERY SHOULD DISPLAY COLUMNS IN THE SAME ORDER AS MENTIONED IN ALL QUESTIONS.
3) YOU CAN FIND THE ORDER OF COLUMNS IN QUESTION TEMPLATE SECTION OF EACH QUESTION.
4) USE ALIASING AS MENTIONED IN QUESTION TEMPLATE FOR ALL COLUMNS
5) DO NOT CHANGE COLUMN NAMES*/
     USE northwind;              
-- Question 1 (Marks: 2)
-- Objective: Retrieve data using basic SELECT statements
-- List the names of all customers in the database.
-- Question Template: Display CustomerName Column

SELECT CustomerName FROM customers;

-- Question 2 (Marks: 2)
-- Objective: Apply filtering using the WHERE clause
-- Retrieve the names and prices of all products that cost less than $15.
-- Question Template: Display ProductName Column
SELECT PRODUCTNAME, Price FROM products WHERE Price < 15;

-- Question 3 (Marks: 2)
-- Objective: Use SELECT to extract multiple fields
-- Display all employees first and last names.
-- Question Template: Display FirstName, LastName Columns

SELECT FirstName, LastName FROM employees;

-- Question 4 (Marks: 2)
-- Objective: Filter data using a function on date values
-- List all orders placed in the year 1997.
-- Question Template: Display OrderID, OrderDate Columns

SELECT OrderID, OrderDate FROM orders WHERE YEAR(OrderDate)=1997;

-- Question 5 (Marks: 2)
-- Objective: Apply numeric filters
-- List all products that have a price greater than $50.
-- Question Template: Display ProductName, Price Column

SELECT ProductName, Price FROM products WHERE Price > 50;

-- Question 6 (Marks: 3)
-- Objective: Perform multi-table JOIN operations
-- Show the names of customers and the names of the employees who handled their orders.
-- Question Template: Display CustomerName, FirstName, LastName Columns

SELECT C.CustomerName, CONCAT(E.FirstName," ",E.LastName) AS EmployeeName
FROM customers AS C INNER JOIN orders As O
ON C.CustomerID = O.CustomerID
INNER JOIN employees AS E
ON O.EmployeeID = E.EmployeeID;

-- Question 7 (Marks: 3)
-- Objective: Use GROUP BY for aggregation
-- List each country along with the number of customers from that country.
-- Question Template: Display Country, CustomerCount Columns

SELECT Country , COUNT(*) AS CustomerCount
FROM customers
GROUP BY Country;

-- Question 8 (Marks: 3)
-- Objective: Group data by a foreign key relationship and apply aggregation
-- Find the average price of products grouped by category.
-- Question Template: Display CategoryName, AvgPrice Columns

SELECT C.CategoryName , Avg(P.Price) AS AvgPrice
FROM products AS P INNER JOIN categories AS C
ON C.CategoryID = P.CategoryID
GROUP BY c.CategoryName;

-- Question 9 (Marks: 3)
-- Objective: Use aggregation to count records per group
-- Show the number of orders handled by each employee.
-- Question Template: Display EmployeeID, OrderCount Columns

SELECT EmployeeID, Count(*) AS OrderCount 
FROM orders
GROUP BY EmployeeID;

-- Question 10 (Marks: 3)
-- Objective: Filter results using values from a joined table
-- List the names of products supplied by "Exotic Liquids".
-- Question Template: Display ProductName Column

SELECT ProductName
FROM products AS P INNER JOIN suppliers AS S
ON P.SupplierID = S.SupplierID
WHERE SupplierName = "Exotic Liquids";

-- Question 11 (Marks: 5)
-- Objective: Rank records using aggregation and sort
-- List the top 3 most ordered products (by quantity).
-- Question Template: Display ProductID, TotalOrdered Columns

SELECT ProductID, SUM(Quantity) AS TotalOrdered
FROM orderdetails
GROUP BY ProductID
ORDER BY TotalOrdered DESC
LIMIT 3;

-- Question 12 (Marks: 5)
-- Objective: Use GROUP BY and HAVING to filter on aggregates
-- Find customers who have placed orders worth more than $10,000 in total.
-- Question Template: Display CustomerName, TotalSpent Columns

SELECT C.CustomerName,SUM(Od.Quantity * P.price) AS TotalSpent
FROM customers As C INNER JOIN orders AS O
ON C.CustomerID = O.CustomerID
INNER JOIN orderdetails AS Od
ON O.OrderID = Od.OrderID
INNER JOIN products AS P
ON Od.ProductID = P.ProductID
GROUP BY C.CustomerName
HAVING TotalSpent > 10000;

-- Question 13 (Marks: 5)
-- Objective: Aggregate and filter at the order level
-- Display order IDs and total order value for orders that exceed $2,000 in value.
-- Question Template: Display OrderID, OrderValue Columns

SELECT O.OrderID, SUM(Od.Quantity * P.Price) AS OrderValue
FROM orders AS O INNER JOIN orderdetails AS Od
ON O.OrderID = Od.OrderID
INNER JOIN products AS P
ON Od.ProductID = P.ProductID
GROUP BY O.OrderID
HAVING OrderValue > 2000;

-- Question 14 (Marks: 5)
-- Objective: Use subqueries in HAVING clause
-- Find the name(s) of the customer(s) who placed the largest single order (by value).
-- Question Template: Display CustomerName, OrderID, TotalValue Column

SELECT C.CustomerName, O.OrderID, SUM(Od.Quantity * P.Price) AS TotalValue
FROM customers AS C INNER JOIN orders AS O
ON C.CustomerID = O.CustomerID
INNER JOIN orderdetails AS Od
ON O.OrderID = Od.OrderID
INNER JOIN products AS P
ON Od.ProductID = P.ProductID
GROUP BY O.OrderID
HAVING TotalValue = ( SELECT MAX(OrderTotal) 
						FROM ( SELECT SUM(Od.Quantity * P.Price) AS OrderTotal
								FROM orders AS O INNER JOIN orderdetails AS Od
                                ON O.OrderID = Od.OrderID
                                INNER JOIN products AS P
                                ON Od.ProductID = P.ProductID
                                GROUP BY O.OrderID) AS OrderSums
	);

-- Question 15 (Marks: 5)
-- Objective: Identify records using NOT IN with subquery
-- Get a list of products that have never been ordered.
-- Question Template: Display ProductName Columns

SELECT ProductName 
FROM products
WHERE ProductID NOT IN ( SELECT DISTINCT ProductID FROM orderdetails);
