### SQL UCASE() Function
필드 값을 대문자로 변환한다.

```sql
SELECT UCASE(column_name) FROM table_name;

-- SQL Server
SELECT UPPER(column_name) FROM table_name;

-- Example
SELECT UCASE(CustomerName) AS Customer, City
FROM Customers;
```

### SQL LCASE() Function
필드 값을 소문자로 변환한다.

```sql
SELECT LCASE(column_name) FROM table_name;

-- SQL Server
SELECT LOWER(column_name) FROM table_name;

-- Example
SELECT LCASE(CustomerName) AS Customer, City
FROM Customers;
```
### SQL MID() Function
텍스트 필드에서 문자를 추출하는 데 사용한다.

```sql
-- column_name : 필수 파라미터
-- start : 필수 파라미터
-- length : 옵션 파라미터, 설정하지 않을 경우 나머지 부분을 모두 반환
SELECT MID(column_name,start,length)
AS some_name FROM table_name;

-- SQL Server
SELECT SUBSTRING(column_name,start,length) AS some_name FROM table_name;

-- Oracle
SELECT SUBSTR(column_name,start,length) AS some_name FROM table_name;

-- Example
SELECT MID(City,1,4) AS ShortCity
FROM Customers;
```

### SQL LEN() Function
텍스트 필드에 값의 길이를 반환한다.

```sql
SELECT LEN(column_name) FROM table_name;

-- Oracle
SELECT LENGTH(column_name) FROM table_name;

-- Example
SELECT CustomerName,LEN(Address) as LengthOfAddress
FROM Customers;
```

### SQL ROUND() Function
숫자 필드를 지정된 소수 자리로 반올림하는 데 사용한다.

**주의** : 많은 데이터베이스 시스템은 IEEE 754 산술 연산 표준을 채택했다. 이 표준에 따르면, .5는 가장 가까운 짝수 정수로 반올림된다. 예를 들면 11.5와 12.5 둘다 12로 반올림된다.

```sql
-- column_name : 필수 파라미터
-- decimals : 필수 파라미터,  소수의 개수 지정
SELECT ROUND(column_name,decimals) FROM table_name;

-- Example
SELECT ProductName, ROUND(Price,0) AS RoundedPrice
FROM Products;
```

### SQL NOW() Function
현재 시스템 날짜와 시간을 반환한다.

```sql
SELECT NOW() FROM table_name;

-- Example
SELECT ProductName, Price, Now() AS PerDate
FROM Products;
```


### SQL FORMAT() Function
필드 표시 방식을 만드는데 사용합니다.

```sql
-- column_name : 필수 파라미터
-- format : 필수 파라미터, 특정 format
SELECT FORMAT(column_name,format) FROM table_name;

-- Example
SELECT ProductName, Price, FORMAT(Now(),'YYYY-MM-DD') AS PerDate
FROM Products;
```
