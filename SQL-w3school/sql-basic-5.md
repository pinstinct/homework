### SQL 제약

SQL 제약 조건은 테이블의 데이터에 대한 규칙을 지정하는 데 사용한다.

```sql
CREATE TABLE table_name
(
  column_name1 data_type(size) constraint_name,
  column_name2 data_type(size) constraint_name,
  column_name3 data_type(size) constraint_name,
  ...
);
```

- NOT NULL - 열(column)이 NULL 값을 저장할 수 없음을 나타낸다.
- UNIQUE - 열의 각 행(row)이 고유 한 값을 가져야한다.
- PRIMARY KEY - NOT NULL과 UNIQUE의 조합이다. 열이 고유한 ID를 갖도록 보장한다.
- FOREIGN KEY - 다른 테이블의 값과 일치하도록 한 테이블의 데이터 무결성 보장
- CHECK - 열의 값이 특정 조건을 충족하는지 확인한다.
- DEFAULT - 열의 기본값을 지정한다.

### SQL NOT NULL 제약

NOT NULL 제약 조건은 열(column)이 NULL 값을 허용하지 않도록한다. 즉, 필드가 항상 값을 포함하도록 한다.

```sql
CREATE TABLE PersonsNotNull
(
P_Id int NOT NULL,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255)
);
```

### SQL UNIQUE 제약

UNIQUE 제약 조건은 데이터베이스 테이블의 각 record를 고유하게 식별한다.

UNIQUE와 PRIMARY KEY 제약 조건은 열(column) 또는 열 집합의 고유성을 보장한다. PRIMARY KEY 제약 조건에는 자동으로 UNIQUE 제약 조건이 정의되어 있다.

주의 : 테이블 당 UNIQUE 제약 조건을 많이 가질 수 있지만, PRIMARY KEY 제약 조건 테이블당 하나만 가질 수 있습니다.

```sql
-- SQL Server / Oracle / MS Access
CREATE TABLE Persons
(
  P_Id int NOT NULL UNIQUE,
  LastName varchar(255) NOT NULL,
  FirstName varchar(255),
  Address varchar(255)
  City varchar(255)
);

-- MySQL
CREATE TABLE Persons
(
  P_Id int NOT NULL,
  LastName varchar(255) NOT NULL,
  FirstName varchar(255),
  Address varchar(255)
  City varchar(255)
  UNIQUE(P_Id)
);

-- MySQL / SQL Server / Oracle / MS Access
CREATE TABLE Persons
(
  P_Id int NOT NULL,
  LastName varchar(255) NOT NULL,
  FirstName varchar(255),
  Address varchar(255),
  City varchar(255),
  CONSTRAINT uc_PersonID UNIQUE (P_Id,LastName)
);

-- on ALTER TABLE
-- 이미 만들어진 테이블에 제약조건을 만들 때

-- MySQL / SQL Server / Oracle / MS Access
ALTER TABLE Persons
ADD UNIQUE (P_Id);

-- MySQL / SQL Server / Oracle / MS Access
ALTER TABLE Persons
ADD CONSTRAINT uc_PersonID UNIQUE (P_Id,LastName);


-- UNIQUE 제약 삭제
-- SQL Server / Oracle / MS Access
ALTER TABLE Persons
DROP INDEX uc_PersonID;

-- MySQL
ALTER TABLE Persons
DROP CONSTRAINT uc_PersonID;
```

### SQL PRIMARY KEY 제약

PRIMARY KEY 제약 조건은 데이터베이스 테이블의 각 record를 고유하게 식별한다. PRIMARY KEY는 UNIQUE 값이 포함되야 한며, column은 NULL 값을 포함할 수 없다. 대부분의 테이블에는 기본 키(PRIMARY KEY)가 있어야하며 각 테이블에는 기본 키가 하나만있을 수 있다.

```sql
-- on CREATE TABLE
-- SQL Server / Oracle / MS Access
CREATE TABLE Persons
(
  P_Id int NOT NULL PRIMARY KEY,
  LastName varchar(255) NOT NULL,
  FirstName varchar(255),
  Address varchar(255),
  City varchar(255)
);

-- MySQL
CREATE TABLE Persons
(
  P_Id int NOT NULL,
  LastName varchar(255) NOT NULL,
  FirstName varchar(255),
  Address varchar(255),
  City varchar(255),
  PRIMARY KEY (P_Id)
);

-- MySQL / SQL Server / Oracle / MS Access
-- 주의 : 기본키는 pk_PersonID 하나이다.
-- pk_PersonID = P_Id + LastName 조합하여 구성
CREATE TABLE Persons
(
  P_Id int NOT NULL,
  LastName varchar(255) NOT NULL,
  FirstName varchar(255),
  Address varchar(255),
  City varchar(255),
  CONSTRAINT pk_PersonID PRIMARY KEY (P_Id,LastName)
);

-- on ALTER TABLE
-- MySQL / SQL Server / Oracle / MS Access
ALTER TABLE Persons
ADD PRIMARY KEY (P_Id);

-- 주의 : ALTER TABLE 문을 사용하여 기본 키를 추가하는 경우,
-- 기본 키 column은 NULL 값을 포함하지 않도록 이미 선언해야 함
ALTER TABLE Persons
ADD CONSTRAINT pk_PersonID PRIMARY KEY (P_Id,LastName);

-- PRIMARY KEY 제약 삭제
-- SQL Server / Oracle / MS Access
ALTER TABLE Persons
DROP CONSTRAINT pk_PersonID;

-- MySQL
ALTER TABLE Persons
DROP PRIMARY KEY;
```

### SQL FOREIGN KEY 제약

한 테이블의 FOREIGN KEY는 다른 테이블의 PRIMARY KEY를 가리킨다.

```sql
-- on CREATE TABLE
-- SQL Server / Oracle / MS Access
CREATE TABLE Orders
(
  O_ID int NOT NULL PRIMARY KEY,
  OrderNo int NOT NULL,
  P_Id int FOREIGN KEY REFERENCES Persons(P_Id)
);

-- MySQL
CREATE TABLE Orders
(
O_Id int NOT NULL,
OrderNo int NOT NULL,
P_Id int,
PRIMARY KEY (O_Id),
FOREIGN KEY (P_Id) REFERENCES Persons(P_Id)
);

-- MySQL / SQL Server / Oracle / MS Access
CREATE TABLE Orders
(
  O_Id int NOT NULL,
  OrderNo int NOT NULL,
  P_Id int,
  PRIMARY KEY (O_Id),
  CONSTRAINT fk_PerOrders FOREIGN KEY (P_Id)
  REFERENCES Persons(P_Id)
);

-- on ALTER TABLE
-- MySQL / SQL Server / Oracle / MS Access
ALTER TABLE Orders
ADD FOREIGN KEY (P_Id)
REFERENCES Persons(P_Id);

ALTER TABLE Orders
ADD CONSTRAINT fk_PerOrders
FOREIGN KEY (P_Id)
REFERENCES Persons(P_Id);

-- FOREIGN KEY 제약 삭제
-- SQL Server / Oracle / MS Access
ALTER TABLE Orders
DROP CONSTRAINT fk_PerOrders;

-- MySQL
ALTER TABLE Orders
DROP FOREIGN KEY fk_PerOrders;
```

### SQL CHECK 제약

CHECK 제약 조건은 열(column)에 배치 할 수있는 값 범위를 제한하는 데 사용한다.

```sql
-- on CREATE TABLE
-- SQL Server / Oracle / MS Access
CREATE TABLE Persons
(
  P_Id int NOT NULL CHECK (P_Id>0),
  LastName varchar(255) NOT NULL,
  FirstName varchar(255),
  Address varchar(255),
  City varchar(255)
);

-- MySQL
CREATE TABLE Persons
(
  P_Id int NOT NULL,
  LastName varchar(255) NOT NULL,
  FirstName varchar(255),
  Address varchar(255),
  City varchar(255),
  CHECK (P_Id>0)
);

-- MySQL / SQL Server / Oracle / MS Access
CREATE TABLE Persons
(
  P_Id int NOT NULL,
  LastName varchar(255) NOT NULL,
  FirstName varchar(255),
  Address varchar(255),
  City varchar(255),
  CONSTRAINT chk_Person CHECK (P_Id>0 AND City='Sandnes')
);

-- on ALTER TABLE
-- MySQL / SQL Server / Oracle / MS Access
ALTER TABLE Persons
ADD CHECK (P_Id>0);

ALTER TABLE Persons
ADD CONSTRAINT chk_Person CHECK (P_Id>0 AND City='Sandnes');

-- CHECK 제약 삭제
-- SQL Server / Oracle / MS Access
ALTER TABLE Persons
DROP CONSTRAINT chk_Person;

-- MySQL
ALTER TABLE Persons
DROP CHECK chk_Person;
```

### SQL DEFAULT 제약

DEFAULT 제약은 열(column)에 기본 값을 삽입하는 데 사용한다.

```sql
-- on CREATE TABLE
-- My SQL / SQL Server / Oracle / MS Access
CREATE TABLE Persons
(
  P_Id int NOT NULL,
  LastName varchar(255) NOT NULL,
  FirstName varchar(255),
  Address varchar(255),
  City varchar(255) DEFAULT 'Sandnes'
);

CREATE TABLE Orders
(
  O_Id int NOT NULL,
  OrderNo int NOT NULL,
  P_Id int,
  OrderDate date DEFAULT GETDATE()
);

-- on ALTER TABLE
-- SQL Server / MS Access
ALTER TABLE Persons
ALTER COLUMN City SET DEFAULT 'SANDNES';

-- MySQL
ALTER TABLE Persons
ALTER City SET DEFAULT 'SANDNES';

-- Oracle
ALTER TABLE Persons
MODIFY City DEFAULT 'SANDNES';

-- DEFAULT 제약 삭제
-- SQL Server / Oracle / MS Access
ALTER TABLE Persons
ALTER COLUMN City DROP DEFAULT

-- MySQL
ALTER TABLE Persons
ALTER City DROP DEFAULT
```
