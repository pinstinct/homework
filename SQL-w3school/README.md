## Intro

### SQL 이란?

SQL은 데이터베이스를 접근하고 조작하기 위한 표준 언어다.

- 구조화 된 쿼리 언어
- ANSI (American National Standards Institute) 표준이다.
  - ANSI 표준이지만 다른 SQL 버전이 있다.
  - 하지만, ANSI 표준을 준수하기 위해 이들은 모두 SELECT, UPDATE, DELETE, INSERT, WHERE와 같은 주요 명령을 비슷한 방식으로 지원한다.

### 웹 사이트에서 SQL 사용

데이터베이스의 데이터를 보여주는 웹 사이트를 구축하려면 다음이 필요하다.

- RDBMS 데이터베이스 프로그램 (예 : MS Access, SQL Server, MySQL)
- 서버 측 스크립팅 언어 (PHP, ASP)
- 원하는 데이터를 얻기 위해 SQL
- HTML / CSS

### RDBMS

RDBMS는 관계형 데이터베이스 관리 시스템의 약자이다. 테이블은 관련 데이터 항목의 모음이며 열과 행으로 구성된다.



## Syntax

### Database Tables

데이터베이스는 하나 혹은 그 이상의 테이블을 가진다. 각 테이블은 name(예: 'Customer', 'Orders')으로 식별한다. 테이블은 데이터가 있는 records (행, row)를 포함한다.

아래는 'Customers' 테이블이다.

| CustomerID | CustomerName                       | ContactName        | Address                       | City        | PostalCode | Country |
| ---------- | ---------------------------------- | ------------------ | ----------------------------- | ----------- | ---------- | ------- |
| 1          | Alfreads Futterkiste               | Maria Anders       | Ober Str. 57                  | Berlin      | 12209      | Germany |
| 2          | Ana Trujillo Emparedados y helados | Ana Trujillo       | Avda. de la Constitución 2222 | México D.F. | 05021      | Mexico  |
| 3          | Antonio Moreno Taquería            | Antonio Moreno     | Mataderos 2312                | México D.F. | 05023      | Mexico  |
| 4          | Around the Horn                    | Thomas Hardy       | 120 Hanover Sq.               | London      | WA1 1DP    | UK      |
| 5          | Berglunds snabbköp                 | Christina Berglund | Berguvsvägen 8                | Luleå       | S-958 22   | Sweden  |

위의 테이블은 5개의 records(각 고객마다 하나의 행)와 7개의 columns(CustomerID, CustomerName, ContactName, Address, City, PostalCode, and Country)을 포함한다.

### SQL 문

데이터베이스에 수행해야 하는 대부분의 작업은 SQL 문을 통해 이루어진다.

```sql
SELECT * FROM Customers;
```

- SQL 키워드는 대소문자를 구별하지 않는다. (select is the same as SELECT)
- 세미콜론(;)은 데이터베이스에서 각각의 SQL 문을 구별하기 위한 표준이다.

### 중요한 SQL 명령

- **SELECT** - 데이터베이스에서 데이터를 추출
- **UPDATE** - 데이터베이스에서 데이터를 갱신
- **DELETE** - 데이터베이스에서 데이터를 삭제
- **INSERT INTO** - 데이터베이스에서 새로운 데이터를 삽입
- **CREATE DATABASE** - 새로운 데이터베이스 생성
- **ALTER DATABASE** - 데이터베이스 수정
- **CREATE TABLE** - 새로운 테이블 생성
- **ALTER TABLE** - 테이블 수정
- **DROP TABLE** - 데이블 삭제
- **CREATE INDEX** - 인덱스 생성(search key)
- **DROP INDEX** - 인덱스 삭제
