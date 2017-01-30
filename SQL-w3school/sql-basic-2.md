### SQL SELECT TOP 절

SELECT TOP 절은 리턴 할 record 수를 지정하는 데 사용한다. 많은 수의 레코드를 반환하면 성능에 영향을 줄 수 있다. (주의 : 모든 데이터베이스 시스템이 SELECT TOP 절을 지원하는 것은 아니다.)

```sql
-- SQL Server, MS Access
SELECT TOP number|percent column_name
FROM table_name;

SELECT TOP 2 * FROM Customers;
SELECT TOP 50 PERCENT * FROM Customers;

-- MySQL
SELECT column_name
FROM table_name
LIMIT numbers;

SELECT *
FROM Persons
LIMIT 5;

-- Oracle
SELECT column_name
FROM table_name
WHERE ROWNUM <= numbers;

SELECT *
FROM Persons
WHERE ROWNUM <=5;
```

### SQL LIKE 연산자

LIKE 연산자는 WHERE 절에서 column(열)의 지정된 패턴을 검색하는 데 사용됩니다.

```sql
SELECT column_name
FROM table_name
WHERE column_name LIKE pattern;

-- Example
-- % 기호는 와일드카드
SELECT * FROM Customers
WHERE City LIKE 's%';

SELECT * FROM Customers
WHERE City LIKE 's%';

SELECT * FROM Customers
WHERE Country LIKE '%land%';

SELECT * FROM Customers
WHERE Country NOT LIKE '%land%';
```

### SQL Wildcards

SQL에서 와일드 카드 문자는 SQL LIKE 연산자와 함께 사용한다. 와일드 카드 문자를 사용하여 문자열의 다른 문자를 대체 할 수 있다.

| Wildcard      | Description      |
| ------------- | ---------------- |
| %             | 0개 이상의 문자 대체     |
| _             | 1개 문자만 대체        |
| [*charlist*]  | 문자 세트/범위 설정      |
| [!*charlist*] | NOT [*charlist*] |



```sql
-- % 와일드 카드
SELECT * FROM Customers
WHERE City LIKE 'ber%';

SELECT * FROM Customers
WHERE City LIKE '%es%';

-- _ 와일드 카드
SELECT * FROM Customers
WHERE City LIKE '_erlin';

SELECT * FROM Customers
WHERE City LIKE 'L_n_on';

-- [charlist] 와일드 카드
SELECT * FROM Customers
WHERE City LIKE '[bsp]%';

SELECT * FROM Customers
WHERE City LIKE '[a-c]%';

SELECT * FROM Customers
WHERE City LIKE '[!bsp]%';

SELECT * FROM Customers
WHERE City NOT LIKE '[bsp]%';
```

### SQL IN 연산자

IN 연산자를 사용하면 WHERE 절에 여러 값을 지정할 수 있다.

```sql
SELECT column_name
FROM table_name
WHERE column_name in (value1, value2, ...);

-- Example
SELECT * FROM Customers
WHERE City in ('Paris', 'London');
```

### SQL BETWEEN 연산자

BETWEEN 연산자는 범위 내의 값을 선택한다. 값은 숫자, 텍스트 또는 날짜 일 수 있다.

```sql
SELECT column_name
FROM table_name
WHERE column_name BETWEEN value AND value2;

-- Example
SELECT * FROM Products
WHERE Price BETWEEN 10 AND 20;

SELECT * FROM Products
WHERE price NOT BETWEEN 10 AND 20;

SELECT * FROM Products
WHERE (Price BETWEEN 10 AND 20)
AND NOT CategoryID IN (1,2,3);

SELECT * FROM Products
WHERE ProductName BETWEEN 'C' AND 'M';

SELECT * FROM Products
WHERE ProductName NOT BETWEEN 'C' AND 'M';

SELECT * FROM Orders
WHERE OrderDate BETWEEN #07/04/1996# AND #07/09/1996#;
```

주의 : BETWEEN 연산자는 다른 데이터베이스에서 다른 결과를 생성 할 수 있다. 따라서 데이터베이스가 BETWEEN 연산자를 어떻게 처리하는지 확인해야 한다.

### SQL Aliases

SQL Aliases는 테이블이나 column(열)에 임시 이름을 제공하는데 사용한다. 기본적으로 aliases가 생성되면 colunmn 이름을 보다 쉽게 읽을 수 있다.

```sql
-- Columns
SELECT column_name AS alias_name
FROM table_name;

-- Tables
SELECT column_name
FROM table_name AS alias_name;

-- Example
-- Columns
-- column 이름에 공백이 있으면 대괄호([])로 묶는다.
SELECT CustomerName AS Customer, ContactName AS [Contact Person]
FROM Customers;

SELECT CustomerName, Address+', '+City+', '+PostalCode+', '+Country AS Address
FROM Customers;

-- in MySQL
SELECT CustomerName, CONCAT(Address,', ',City,', ',PostalCode,', ',Country) AS Address
FROM Customers;

SELECT o.OrderID, o.OrderDate, c.CustomerName
FROM Customers AS c, Orders AS o
WHERE c.CustomerName="Around the Horn" AND c.CustomerID=o.CustomerID;
```
