### SQL UNION 연산자

UNION 연산자는 두 개 이상의 SELECT 문의 result-set을 결합하는 데 사용한다. UNION 내의 각 SELECT 문은 동일한 수의 열(column)을 가져야한다. 열은 유사한 데이터 유형을 가져야한다. 또한 각 SELECT 문의 열은 동일한 순서 여야합니다.

```sql
SELECT column_name FROM table1
UNION
SELECT column_name FROM table2
```

**주의** : UNION 연산자는 기본적으로 고유 값만 선택한다. 중복 값을 허용하려면 UNION에 **ALL 키워드**를 사용한다.

```sql
SELECT column_name FROM table1
UNION ALL
SELECT column_name FROM table2
```

UNION의 result-set에 있는 열(column) 이름은 일반적으로 UNION의 첫 번째 SELECT 문의 열 이름과 같다.

```sql
-- Example
SELECT City FROM Customers
UNION
SELECT City FROM Suppliers
ORDER BY City;

SELECT City FROM Customers
UNION ALL
SELECT City FROM Suppliers
ORDER BY City;

SELECT City, Country FROM Customers
WHERE Country='Germany'
UNION ALL
SELECT City, Country FROM Suppliers
WHERE Country='Germany'
ORDER BY City;
```



### SQL SELECT INTO 문

SELECT INTO 문은 한 테이블의 데이터를 복사하여 새 테이블에 삽입한다.

```sql
-- 새로운 테이블에 모든 column을 복사
SELECT *
INTO newtable [IN externaldb]
FROM table1;

-- 새로운 테이블에 원하는 column만 복사
SELECT column_name
INTO newtable [IN externaldb]
FROM table1;

-- Example
SELECT *
INTO CustomersBackup2013
FROM Customers;

-- IN 절을 이용, 다른 데이터베이스의 새로운 테이블에 복사
SELECT *
INTO CustomersBackup2013 IN 'Backup.mdb'
FROM Customers;

SELECT CustomerName, ContactName
INTO CustomersBackup2013
FROM Customers;

SELECT *
INTO CustomersBackup2014
FROM Customers
WHERE Country='Germany';

SELECT Customers.CustomerName, Orders.OrderID
INTO CustomersOrderBackup2013
From Customers
LEFT JOIN Orders
ON Customers.CustomerID = Orders.CustomerID
```

### SQL INSERT INTO SELECT 문

INSERT INTO SELECT 문은 한 테이블의 데이터를 복사해 기존 테이블에 삽입한다. 이미 있는 행(row)에는 영향을 주지 않는다.

```sql
INSERT INTO table2
SELECT * FROM table1;

INSERT INTO table2
(column_name)
SELECT column_name
FROM table1;

-- Example
INSERT INTO Customers (CustomerName, Country)
SELECT SupplierName, Country FROM Suppliers;

INSERT INTO Customers (CustomerName, Country)
SELECT SupplierName, Country FROM Suppliers
WHERE Country='Germany';
```

### SQL CREATE DATABASE 문

CREATE DATABASE 문은 데이터베이스를 만드는데 사용한다.

```sql
CREATE DATABASE dbname;

-- Example
CREATE DATABASE my_db;
```

### SQL CREATE TABLE 문

CREATE TABLE 문은 데이터베이스의 테이블을 만드는데 사용한다. 테이블은 행(row)과 열(column)로 구성되고 테이블 이름이 있다.

```sql
CREATE TABLE table_name
(
  column_name1 data_type(size),
  column_name2 data_tyee(size),
  column_name3 data_type(size),
  ...
);

-- Example
CREATE TABLE Persons
(
  PersonID int,
  LastName varchar(255),
  FirstName varchar(255),
  Address varchar(255),
  City varchar(255)
);
```
