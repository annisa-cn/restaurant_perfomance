# restaurant_perfomance
this is a mock up data to practice SQL

First, we're going to create a database to store our tables. We're going to name it as 'Restaurant'.

CREATE DATABASE Restaurant

Select our database using this statement (or you can select it right from your available databases on DBMS).
USE Restaurant

The database will consists of 3 tables: Sales, Menu, and Members.
CREATE TABLE Sales(
	CustomerID VARCHAR(10),
	OrderDate DATE,
	ProductID VARCHAR(10)
)

CREATE TABLE Menu(
	ProductID VARCHAR(10),
	ProductName VARCHAR(50),
	Price INTEGER
)

CREATE TABLE Members(
	CustomerID VARCHAR(10),
	JoinDate Date
)

After creating the tables, let's input the data into it.
INSERT INTO Sales VALUES
('A', '2021-01-01', '1'), 
('A', '2021-01-01', '2'), 
('A', '2021-01-07', '2'), 
('A', '2021-01-10', '3'), 
('A', '2021-01-11', '3'), 
('A', '2021-01-11', '3'), 
('B', '2021-01-01', '2'), 
('B', '2021-01-02', '2'), 
('B', '2021-01-04', '1'), 
('B', '2021-01-11', '1'), 
('B', '2021-01-16', '3'), 
('B', '2021-02-01', '3'), 
('C', '2021-01-01', '3'), 
('C', '2021-01-01', '3'), 
('C', '2021-01-07', '3')

INSERT INTO Menu (ProductID, ProductName, Price) VALUES
('1', 'sushi', '10'), 
('2', 'curry', '15'), 
('3', 'ramen', '12')

INSERT INTO Members (CustomerID, JoinDate) VALUES
('A', '2021-01-07'), 
('B', '2021-01-09')

QUESTIONS

--If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
```SELECT s.CustomerID,
    ```SUM(CASE
		```WHEN ProductName = 'Sushi' THEN  Price*2
		```ELSE Price*1
		```END) AS Point
```FROM Sales AS s
```INNER JOIN Menu AS m
	  ```ON s.ProductID = m.ProductID
```GROUP BY s.CustomerID

To solve this question, we need to join table 
From the query above, it is shown that Customer A has the highest (86) point and Customer C has the lowest point (36)


--What is the total items and amount spent for each member before they became a member?
SELECT s.CustomerID, COUNT(s.ProductID) AS TotalItem, SUM(m.Price) AS TotalSpent
FROM Sales AS s
INNER JOIN Menu AS m
	ON s.ProductID = m.ProductID
INNER JOIN Members AS ms
	ON s.CustomerID = ms.CustomerID
WHERE OrderDate < JoinDate
GROUP BY s.CustomerID




--What is the most purchased item on the menu and how many times was it purchased by all customers?
SELECT TOP(1) m.ProductName, COUNT(CAST(s.ProductID AS INTEGER)) AS NoPurchase
FROM Sales AS s
INNER JOIN Menu AS m
	ON s.ProductID = m.ProductID
GROUP BY ProductName
ORDER BY NoPurchase DESC

SELECT CustomerID, SUM(Price) AS TotalPrice
FROM Sales AS s
JOIN Menu AS m
	ON s.ProductID = m.ProductID
GROUP BY CustomerID

SELECT CustomerID, COUNT(DISTINCT OrderDate) AS NoVisited
FROM Sales
GROUP BY CustomerID

--What was the first item from the menu purchased by each customer?

DROP TABLE IF EXISTS #TempTable
SELECT CustomerID, ProductName,
ROW_NUMBER() OVER(PARTITION BY CustomerID ORDER BY OrderDate ASC) AS Row
INTO #TempTable FROM Sales AS s
INNER JOIN Menu AS m
	ON s.ProductID = m.ProductID

SELECT CustomerID, ProductName
FROM #TempTable
WHERE Row = 1
