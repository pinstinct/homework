
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
-- NULL값은 계산되지 않음
SELECT COUNT(column_name) FROM table_name;

SELECT COUNT(CustomerID) AS OrdersFromCustomerID7 FROM Orders
WHERE CustomerID=7;

-- 테이블 record 수 반환
SELECT COUNT(*) FROM table_name;

SELECT COUNT(*) AS NumberOfOrders FROM Orders;

-- 지정된 열의 고유 값 수 반환
SELECT COUNT(DISTINCT column_name) FROM table_name;

SELECT COUNT(DISTINCT CustomerID) AS NumberOfCustomers FROM Orders;
```
