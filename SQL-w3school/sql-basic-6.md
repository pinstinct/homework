### SQL CREATE INDEX 문

CREATE INDEX 문은 테이블의 인덱스를 작성하는데 사용한다. 테이블에 INDEX를 작성하여 빠르고 효율적으로 데이터를 찾을 수 있다. 사용자는 INDEX를 볼 수 없으며 검색/쿼리 속도를 높이기 위해 사용한다.

주의: 인덱스가 있는 테이블을 갱신하는 것은 인덱스가 없는 테이블을 갱신하는 것보다 오래 걸린다. 인덱스 갱신이 필요하기 때문이다. 그러므로 자주 검색할 column이나 테이블에만 인덱스를 만드어야 한다.

```sql
CREATE INDEX index_name
ON table_name (column_name);

-- CREATE UNIQUE INDEX
CREATE UNIQUE INDEX index_name
ON table_name (column_name);

-- Example
CREATE INDEX PIndex
ON Persons (LastName);

CREATE INDEX PIndex
ON Persons (LastName, FirstName);
```



### SQL DROP

DROP 문은 INDEX/TABLE/DATABASE를 삭제하는 데 사용한다.

```sql
-- DROP INDEX
-- MS Access
DROP INDEX index_name ON table_name;

-- MS SQL Server
DROP INDEX table_name.index_name;

-- DB2/Oracle:
DROP INDEX index_name;

-- MySQL
ALTER TABLE table_name DROP INDEX index_name;

-- DROP TABLE
DROP TABLE table_name;

-- DROP DATABASE
DROP DATABASE database_name;

-- TRUNCATE TABLE
-- 테이블 자체가 아닌 테이블 내부의 데이터 삭제
TRUNCATE TABLE table_name;
```



### SQL ALTER TABLE 문

ALTER TABLE 문은 기존 테이블의 열을 추가, 삭제 또는 수정하는 데 사용한다.

```sql
-- 테이블 column 추가
ALTER TABLE table_name
ADD column_name datatype;

-- 테이블 column 삭제
ALTER TABLE table_name
DROP COLUMN column_name;

-- 테이블 column의 데이터 타입 변경
-- SQL Server / MS Access
ALTER TABLE table_name
ALTER COLUMN column_name datatype;

-- My SQL / Oracle (prior version 10G)
ALTER TABLE table_name
MODIFY COLUMN column_name datatype;

-- Oracle 10G and later
ALTER TABLE table_name
MODIFY column_name datatype;

-- Example
-- Persons 테이블에 DateOfBirth column 추가
ALTER TABLE Persons
ADD DateOfBirth date;

-- Persons 테이블 DateOfBirth column 데이터 타입 변경
ALTER TABLE Persons
ALTER COLUMN DateOfBirth year;

-- Persons 테이블에 DateOfBirth column 삭제
ALTER TABLE Persons
DROP COLUMN DateOfBirth;
```



### SQL AUTO INCREMENT Field

AUTO INCREMENT 새 record가 테이블에 삽입 될 때 고유 번호를 생성한다.

```sql
-- MySQL
CREATE TABLE Persons
(
  ID int NOT NULL AUTO_INCREMENT,
  LastName varchar(255) NOT NULL,
  FirstName varchar(255),
  Address varchar(255),
  City varchar(255),
  PRIMARY KEY(ID)
);

-- AUTO_INCREMENT는 1부터 시작
-- 다른 값으로 시작할 경우
ALTER TABLE Persons AUTO_INCREMENT=100;

-- Persons 테이블에 새 record를 추가할 때,
-- ID column 값을 지정할 필요가 없다.
INSERT INTO Persons (FirstName, LastName)
VALUES ('Lars', 'Monsen');
```

### SQL VIEW 문

VIEW는 SQL 문의 result-set를 기반으로 한 가상 테이블이다. VIEW는 테이블같이 row와 column으로 구성된다. VIEW의 필드는 데이터베이스에 있는 하나 이상의 테이블 필드이다.

#### CREATE VIEW

```sql
-- VIEW는 항상 최신 데이터를 표시
CREATE VIEW view_name AS
SELECT column_name(s)
FROM table_name
WHERE condition;

-- Example
CREATE VIEW [Current Product List] AS
SELECT ProductID,ProductName
FROM Products
WHERE Discontinued=No;

-- Query
SELECT * FROM [Current Product List];
```

#### CREATE OR REPLACE VIEW

```sql
CREATE OR REPLACE VIEW view_name AS
SELECT column_name(s)
FROM table_name
WHERE condition;

-- Example
CREATE OR REPLACE VIEW [Current Product List] AS
SELECT ProductID,ProductName,Category
FROM Products
WHERE Discontinued=No;
```

#### DROP VIEW

```sql
DROP VIEW view_name;
```



### SQL Date

날짜 작업시 가장 어려운 부분은 삽입하려는 날짜의 형식이 데이터베이스의 날짜 열 형식과 일치하는지 확인하는 것이다.

#### SQL 날짜 데이터 유형

**MySQL** 에는 데이터베이스에 날짜 또는 날짜 / 시간 값을 저장하기위한 다음 데이터 유형이 있다.

- DATE - format YYYY-MM-DD
- DATETIME - format: YYYY-MM-DD HH:MI:SS
- TIMESTAMP - format: YYYY-MM-DD HH:MI:SS
- YEAR - format YYYY or YY

**SQL Server** 에는 데이터베이스에 날짜 또는 날짜 / 시간 값을 저장하기위한 다음 데이터 형식이 있다.

- DATE - format YYYY-MM-DD
- DATETIME - format: YYYY-MM-DD HH:MI:SS
- SMALLDATETIME - format: YYYY-MM-DD HH:MI:SS
- TIMESTAMP - format: a unique number

### SQL NULL Values

기본적으로 테이블은 NULL 값을 포함할수 있다. NULL은 알 수 없거나 적용할 수 없는 값이 자리 표시자로 사용한다.

#### SQL IS NULL

```sql
-- NULL 값을 가진 record만 선택할 경우
SELECT LastName,FirstName,Address FROM Persons
WHERE Address IS NULL;
```

#### SQL IS NOT NULL

```sql
-- NULL 값이 없는 record만 선택할 경우
SELECT LastName,FirstName,Address FROM Persons
WHERE Address IS NOT NULL;
```

### SQL NULL Functions

column에 NULL값이 있을 경우, NULL값을 포함해 연산을하면 결과도 NULL이다. 이럴 경우 함수를 사용해 NULL값을 0으로 변경한다.

- ISNULL() : MS Access, SQL Server
- NVL() : Oracle
- IFNULL() or COALESCE(): MySQL

### SQL Comments

```sql
-- 한줄 주석
/* 여러줄 주석입니다.
자유롭게 긴 문장을 작성합니다. */
```
