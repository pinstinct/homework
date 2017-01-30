### SQL Injection

SQL Injection은 데이터베이스를 파괴 할 수 있습니다.

#### SQL in Web Pages

SQL을 사용하여 웹 페이지에 데이터를 표시 할 때, 웹 사용자가 자신의 검색 값을 입력하게하는 것이 일반적이다.

SQL 문은 텍스트 전용이므로 약간의 컴퓨터 코드로 SQL 문을 동적으로 변경하여 사용자에게 선택된 데이터를 제공하는 것이 쉽습니다.

```c#
// Server Code
// 사용자 입력을 txtUserId 변수에 저장
txtUserId = getRequestString("UserId");
txtSQL = "SELECT * FROM Users WHERE UserId = " + txtUserId;
```

SQL 문에 사용자 입력을 사용할 때는 잠재적 위험이 있다. 악의적인 사용자가 웹 페이지 입력을 통해 SQL 문에 SQL 명령을 주입 할 수있기 때문이다. 삽입 된 SQL 명령은 SQL 문을 변경하고 웹 응용 프로그램의 보안을 손상시킬 수 있다.

#### 1 = 1 항상 True

위 코드의 원래 목적은 사용자가 입력한 UserId를 가진 record를 추출하는 SQL 문을 만드는 것이다.

만약 사용자의 '잘못된' 입력을 방지하는 것이 없다면, 사용자는 다음과 같은 입력을 할 수 있다.

ID : `105 or 1=1`

```sql
-- Result
SELECT * FROM Users WHERE UserId = 105 or 1=1;
```

**WHERE 1 = 1** 은 항상 **True**이므로 테이블 Users의 모든 행을 반환한다.

해커는 `105 or 1 = 1`을 입력 상자에 삽입하기 만하면 데이터베이스의 모든 사용자 이름과 암호에 접근할 수 있다.

#### ""="" 항상 True

다음은 웹 사이트에서 사용자 로그인을 확인하는데 사용하는 일반적 구조이다.

ID : `John`, Password : `myPass`

```c#
// Server Code
uName = getRequestString("UserName");
uPass = getRequestString("UserPass");

sql = 'SELECT * FROM Users WHERE Name ="' + uName + '" AND Pass ="' + uPass + '"'
```

```sql
-- Result
SELECT * FROM Users WHERE Name ="John Doe" AND Pass ="myPass"
```

해커는 사용자 이름이나 암호 텍스트 상자에 `" or ""="`를 삽입하여 데이터베이스의 사용자 이름과 암호에 접근할 수 있습니다.

ID : `" or ""="`, Password : `" or ""="`

```sql
-- Result
SELECT * FROM Users WHERE Name ="" or ""="" AND Pass ="" or ""=""
```

**WHERE ""= ""** 는 항상 **True**이므로 테이블 Users의 모든 행을 반환한다.

#### Batched SQL 문

대부분의 데이터베이스는 일괄 처리(Batched) SQL 문을 세미콜론(;)으로 구분하여 지원한다.

```c#
// Server Code
txtUserId = getRequestString("UserId");
txtSQL = "SELECT * FROM Users WHERE UserId = " + txtUserId;
```

ID : `105; DROP TABLE Suppliers`

```sql
-- Result
SELECT * FROM Users WHERE UserId = 105; DROP TABLE Suppliers
```

#### 보호를 위한 Parameters

일부 웹 개발자는 SQL Injection 공격을 막기 위해 SQL 입력에서 검색 할 단어 나 문자의 "blacklist"를 사용한다.

이것은 로 좋은 생각이 아닙니다. 이러한 단어 (delete과 drop 같은)와 문자 (세미콜론 및 따옴표 같은)는 공통 언어로 사용되며 입력에서 허용해야 한다.

SQL Injection 공격으로부터 웹 사이트를 보호하는 유일한 방법은 SQL Parameters를 사용하는 것이다. SQL Parameters는 실행 될 때, 제어된 방식으로 SQL 쿼리에 추가되는 값입니다.

```c#
// Parameters는 SQL 문에서 @ 로 표시됩니다.
txtUserId = getRequestString("UserId");
txtSQL = "SELECT * FROM Users WHERE UserId = @0";
db.Execute(txtSQL,txtUserId);

txtNam = getRequestString("CustomerName");
txtAdd = getRequestString("Address");
txtCit = getRequestString("City");
txtSQL = "INSERT INTO Customers (CustomerName,Address,City) Values(@0,@1,@2)";
db.Execute(txtSQL,txtNam,txtAdd,txtCit);
```
