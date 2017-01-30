### SQL JOIN

SQL JOIN 절은 두 개 이상의 테이블의 행(row)을 공통 필드를 기준으로 결합하는 데 사용됩니다.

가장 일반적인 유형의 조인은 SQL INNER JOIN (단순 조인)입니다. SQL INNER JOIN은 조인 조건이 충족되는 여러 테이블의 모든 행을 리턴합니다.

```sql
SELECT Orders.OrderID, Customers.CustomerName, Orders.OrderDate
FROM Orders
INNER JOIN Customers
ON Orders.CustomerID=Customers.CustomerID;
```

- INNER JOIN
- LEFT JOIN
- RIGHT JOIN
- FULL JOIN

### SQL INNER JOIN 키워드

INNER JOIN 키워드는 두 테이블의 열(columns)이 일치하는 한 두 테이블의 모든 행(row)을 선택한다.



```sql
SELECT column_name
FROM table1
INNER JOIN table2
ON table1.column_name=table2.column_name;

-- Example
SELECT *
FROM Customers
INNER JOIN Orders
ON Customers.CustomerID=Orders.CustomerID

SELECT Customers.CustomerName, Orders.OrderID
FROM Customers
INNER JOIN Orders
ON Customers.CustomerID=Orders.CustomerID
ORDER BY Customers.CustomerName;
```

### SQL LEFT JOIN 키워드

LEFT JOIN 키워드는 왼쪽 테이블 (table1)의 모든 행과 오른쪽 테이블 (table2)의 일치하는 행을 반환한다. 오른쪽 테이블(table2)에서 일치하는 것이 없으면 결과는 NULL이다.


```sql
SELECT column_name
FROM table1
LEFT JOIN table2
ON table1.column_name=table2.column_name;

-- Example
SELECT Customers.CustomerName, Orders.OrderID
FROM Customers
LEFT JOIN Orders
ON Customers.CustomerID=Orders.CustomerID
ORDER BY Customers.CustomerName;
```



### SQL RIGHT JOIN 키워드

RIGHT JOIN 키워드는 왼쪽 테이블 (table1)에 일치하는 행과 함께 오른쪽 테이블 (table2)의 모든 행을 리턴한다. 왼쪽 테이블에서 일치하는 것이 없으면 결과는 NULL이다.

```sql
SELECT column_name(s)
FROM table1
RIGHT JOIN table2
ON table1.column_name=table2.column_name;

-- Example
SELECT Orders.OrderID, Employees.FirstName
FROM Orders
RIGHT JOIN Employees
ON Orders.EmployeeID=Employees.EmployeeID
ORDER BY Orders.OrderID;
```



### SQL FULL OUTER JOIN 키워드

FULL OUTER JOIN 키워드는 왼쪽 테이블 (table1)과 오른쪽 테이블 (table2)의 모든 행을 리턴합니다. LEFT 와 RIGHT 조인의 결과를 결합합니다.

```sql
SELECT column_name(s)
FROM table1
FULL OUTER JOIN table2
ON table1.column_name=table2.column_name;

-- Example
SELECT Customers.CustomerName, Orders.OrderID
FROM Customers
FULL OUTER JOIN Orders
ON Customers.CustomerID=Orders.CustomerID
ORDER BY Customers.CustomerName;
```
