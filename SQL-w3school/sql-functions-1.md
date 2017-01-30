
## SQL Functions

SQL은 데이터에 대한 계산을 수행하는 많은 내장 함수를 가지고 있다.

### SQL Aggregate Functions

column의 값들을 계산해 single 값으로 반환한다.

- AVG() - Returns the average value
- COUNT() - Returns the number of rows
- FIRST() - Returns the first value
- LAST() - Returns the last value
- MAX() - Returns the largest value
- MIN() - Returns the smallest value
- SUM() - Returns the sum



### SQL Scalar functions

입력 값을 기반으로 single 값을 반환

- UCASE() - Converts a field to upper case
- LCASE() - Converts a field to lower case
- MID() - Extract characters from a text field
- LEN() - Returns the length of a text field
- ROUND() - Rounds a numeric field to the number of decimals specified
- NOW() - Returns the current system date and time
- FORMAT() - Formats how a field is to be displayed



### SQL AVG() Function

숫자 column의 평균 값을 반환한다.

```sql
SELECT AVG(Price) AS PriceAverage FROM Products;

SELECT ProductName, Price FROM Products
WHERE Price>(SELECT AVG(Price) FROM Products);
```

### SQL COUNT() Function

지정된 조건과 일치하는 row의 수를 반환한다.

```sql
-- 지정된 column 개수 반환
-- NULL값은 계산되지 않음
SELECT COUNT(column_name) FROM table_name;

-- Example
-- CustomerID colum 중 CustomerID=7인 개수
SELECT COUNT(CustomerID) FROM Orders
WHERE CustomerID=7;


-- 테이블 record 개수 반환
SELECT COUNT(*) FROM table_name;

-- Example
SELECT COUNT(*) AS NumberOfOrders FROM Orders;


-- 지정된 열의 고유 값 개수 반환
SELECT COUNT(DISTINCT column_name) FROM table_name;

-- Example
-- CustomerID에 unique 값 개수 반환
SELECT COUNT(DISTINCT CustomerID) AS NumberOfCustomers FROM Orders;
```

### SQL FIRST() Function
선택한 column의 첫번째 값을 반환한다.

```sql
-- MS Access
SELECT FIRST(column_name) FROM table_name;

SELECT FIRST(CustomerName) AS FirstCustomer FROM Customers;

-- MS Access에서만 지원하므로,
-- 다른 시스템에서는 다음과 같이 해결한다.
-- SQL Server
SELECT TOP 1 column_name FROM table_name
ORDER BY column_name ASC;

SELECT TOP 1 CustomerName FROM Customers
ORDER BY CustomerID ASC;

-- MySQL
SELECT column_name FROM table_name
ORDER BY column_name ASC
LIMIT 1;

SELECT CustomerName FROM Customers
ORDER BY CustomerID ASC
LIMIT 1;

-- Oracle
SELECT column_name FROM table_name
WHERE ROWNUM <=1
ORDER BY column_name ASC;

SELECT CustomerName FROM Customers
WHERE ROWNUM <=1
ORDER BY CustomerID ASC;
```

### SQL LAST() Function
선택한 column의 마지막 값을 반환한다.

```sql
-- MS Access
SELECT LAST(column_name) FROM table_name;

SELECT LAST(CustomerName) AS LastCustomer FROM Customers;

-- MS Access에서만 지원하므로,
-- 다른 시스템에서는 다음과 같이 해결한다.
-- SQL Server
SELECT TOP 1 column_name FROM table_name
ORDER BY column_name DESC;

SELECT TOP 1 CustomerName FROM Customers
ORDER BY CustomerID DESC;

-- MySQL
SELECT column_name FROM table_name
ORDER BY column_name DESC
LIMIT 1;

SELECT CustomerName FROM Customers
ORDER BY CustomerID DESC
LIMIT 1;

-- Oracle
SELECT column_name FROM table_name
ORDER BY column_name DESC
WHERE ROWNUM <=1;

SELECT CustomerName FROM Customers
ORDER BY CustomerID DESC
WHERE ROWNUM <=1;
```

### SQL MAX() Function
선택한 column의 가장 큰 값을 반환한다.

```sql
SELECT MAX(column_name) FROM table_name;

-- Example
SELECT MAX(Price) AS HighestPrice FROM Products;
```

### SQL MIN() Function
선택한 column의 가장 작은 값을 반환한다.

```sql
SELECT MIN(column_name) FROM table_name;

-- Example
SELECT MIN(Price) AS SmallestOrderPrice FROM Products;
```

### SQL SUM() Function
선택한 숫자 column의 총 합을 반환한다.

```sql
SELECT SUM(column_name) FROM table_name;

-- Example
SELECT SUM(Quantity) AS TotalItemsOrdered FROM OrderDetails;
```
