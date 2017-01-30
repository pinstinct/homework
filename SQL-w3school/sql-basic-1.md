### SQL SELECT 문

SELECT 문은 데이터베이스에서 데이터를 선택하는데 사용한다. 결과는 **result-set**이라고 불리는 result table에 저장된다.

```sql
SELECT * FROM table_name;
SELECT column_name, column_name FROM table_name;

-- Example
SELECT CustomerName, City From Customers;
```

#### Result-set 탐색

대부분의 DBMS는 Move-To-First-Record, Get-Record-Content, Move-To-Next-Record, … 같은 함수를 사용해 result-set 탐색을 할 수 있다.

이와 같은 프로그래밍 함수는 서버 측 언어를 통해 수행한다.

### SQL SELECT DISTINCT 문

테이블에서 column(열)은 많은 중복 값을 포함할 수 있다. DISTINCT 키워드는 고유한 값만 선택할 때 사용한다.

```sql
SELECT DISTINCT column_name, column_name FROM table_name

-- Example
SELECT DISTINCT city FROM Customers;
```

### SQL WHERE 절

WHERE 절은 record를 필터링하는데 사용한다. WHERE 절은 지정된 기준을 충족하는 record만 추출한다.

```sql
SELECT column_name, column_name
FROM table_name
WHERE column_name operator value;

-- Example
SELECT * FROM Customers
WHERE Country='Mexico';

SELECT CustomerID, Country FROM Customers
WHERE CustomerID=1;
```

##### WHERE 절 연산자

| Operator | Description                              |
| -------- | ---------------------------------------- |
| =        | Equal                                    |
| <>       | Not equal. **Note:** In some versions of SQL this operator may be written as != |
| >        | Greater than                             |
| <        | Less than                                |
| >=       | Greater than or equal                    |
| <=       | Less than or equal                       |
| BETWEEN  | Between an inclusive range               |
| LIKE     | Search for a pattern                     |
| IN       | To specify multiple possible values for a column |



### SQL AND & OR 연산자

AND & OR 연산자는 둘 이상의 조건에 따라 record를 필터링하는데 사용한다.

- AND  연산자 :  모두 참인 경우 record 표시
- OR 연산자 : 하나라도 참인 경우 record 표시

```sql
-- AND 연산자 Example
SELECT * FROM Customers
WHERE Country='Germany'
AND City='Berlin';

-- OR 연산자 Example
SELECT * FROM Customers
WHERE City='Berlin'
OR City='München';

-- AND & OR 연산자 결합 Example
SELECT * FROM Customers
WHERE Country='Germany'
AND (City='Berlin' OR City='München');
```



### SQL ORDER BY 키워드

ORDER BY 키워드는 하나 이상의 column(열)을 기준으로 result-set을 정렬하는데 사용한다. 기본적으로 record를 오름차순으로 정렬한다. 내림차순 정렬을 하려면 DESC 키워드를 사용한다.

```sql
SELECT column_name, column_name
FROM table_name
ORDER BY column_name ASC, column_name DESC

-- Example
SELECT * FROM Customers
ORDER BY Country;

SELECT * FROM Customers
ORDER BY Country DESC;

SELECT * FROM Customers
ORDER BY Country, CustomerName;

SELECT * FROM Customers
ORDER BY Country ASC, CustomerName DESC;
```



### SQL INSERT INTO 문

INSERT INTO 문은 새 record를 테이블에 삽입하는 데 사용한다.

```sql
INSERT INTO table_name
VALUES (value1, value2, vlaue3, ...);

INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, vlaue3, ...);

-- Example
-- CustomerID column은 테이블에 각각의 record 고유번호로 자동으로 업데이트
INSERT INTO Customers (CustomerName, ContactName, Address, City, PostalCode, Country)
VALUES ('Cardinal','Tom B. Erichsen','Skagen 21','Stavanger','4006','Norway');

-- 특정 Columns에 데이터 삽입
 INSERT INTO Customers (CustomerName, City, Country)
 VALUES ('Cardinal', 'Stavanger', 'Norway');

```



### SQL UPDATE 문

UPDATE 문은 테이블에 기존 record를 갱신하는데 사용한다.

```sql
UPDATE table_name
SET column1=value1, column2=value2, ...
WHERE some_column=some_value;
```

**주의** : WHERE 절은 갱신하려는 record를 지정한다. 만약, WHERE 절을 생략하면 모든 record가 갱신된다.

```sql
-- Example
UPDATE Customers
SET City='Hamburg'
WHERE CustomerID=1;

UPDATE Customers
SET ContactName='Alfred Schmidt', City='Frankfurt'
WHERE CustomerID=1;

UPDATE Customers
SET ContactName='Juan'
WHERE Country='Mexico';

-- 주의: where 절이 없으면, 모든 records가 갱신
UPDATE Customers
SET ContactName='Juan';
```

### SQL DELETE  문

DELETE  문은 테이블의 row(행, record)를 삭제하는데 사용한다. DELETE 문은 취소할 수 없으니, 주의해서 사용한다.

```sql
DELETE FROM table_name
WHERE some_column=some_value;
```

**주의** : WHERE 절은 삭제하려는 record를 지정한다. 만약, WHERE 절을 생략하면 모든 record가 삭제된다.

```sql
-- Example
DELETE FROM Customers
WHERE CustomerName='Alfreds Futterkiste' AND ContactName='Maria Anders';

-- Delete All Data
-- 테이블을 삭제하지 않고 모든 row를 삭제
-- 테이블 구조, 속성, 인덱스는 그대로 둔채 삭제하는 것
DELETE FROM table_name;
DELETE FROM Customers;
```
