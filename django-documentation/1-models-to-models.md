
# [Models: Introduction to models](https://docs.djangoproject.com/en/1.10/topics/db/models/)

모델은 데이터 정보의 소스이다. 일반적으로 각 모델은 하나의 데이터베이스 테이블과 연결한다.

기본 사항:

- 각 모델은 `django.db.models.Model`의 서브클래스인 파이썬 클래스이다.
- 모델의 각 **속성은 데이터베이스 필드**로 표현한다.
- 장고는 자동으로 생성한 데이터베이스 접근 API(ORM)를 제공한다. 쿼리를 만들어 볼 수 있다.

## Quick example

모델 예는 `first_name`과 `last_name`을 가지는 `Person`을 정의한다.

```python
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
```

`first_name`과 `last_name`은 **모델의 필드**이다. 각 필드는 클래스 속성으로 지정하고, 각 속성은 데이터베이스 column과 연결된다.

위의 `Person`모델은 아래와 같은 데이터베이스를 만든다.

```sql
CREATE TABLE myapp_person (
    "id" serial NOT NULL PRIMARY KEY,
    "first_name" varchar(30) NOT NULL,
    "last_name" varchar(30) NOT NULL
);
```



- 테이블 이름

  - 장고는 자동적으로 '모델 클래스의 이름'과 '앱 이름'에서 데이터베이스 테이블 이름을 얻는다.
  - 위의 예에서 테이블 이름은 `myapp_person`이다.

- id 필드는 자동으로 추가된다. (override 불가)

  ​

## Using models

모델을 정의하자마자 그 모델을 *사용*할 것이라고 장고에게 알려야 한다. 장고에게 알리는 것은 설정 파일  `INSTALLED_APPS`에 추가하면 된다.

```python
INSTALLED_APPS = [
    #...
    'myapp',
    #...
]
```

`INSTALLED_APPS`에 새로운 앱을 추가한 후, `manage.py migrate`를 실행해야 한다.

## Fields

모델의 유일한 필수 부분이자, 가장 중요한 부분은 필드가 정의한 데이터베이스 리스트이다. 필드는 클래스 속성에 의해 지정된다.  clean, save, delete 같은 `models API`와 충돌하는 필드 이름을 고르지 않도록 주의하자.

### Field types

모델의 각 필드는 적절한 `Field` 클래스의 하나의 인스턴스여야 한다. 장고는 필드 클래스 유형을 사용하여 몇 가지를 결정한다.

-  column type : 데이터베이스에 저장할 데이터 종류 (예: `INTEGER` , `VARCHAR` , `TEXT` )
-  **폼 필드**를 렌더링할 때, 사용할 기본 HTML `widget` (widget = 장고 내의 HTML input element, 예: choice 선택박스를 만들어줌)
-  장고 관리자 안에서 요구된 최소한의 검증

Django에는 수십 가지 내장 필드 타입이 있다. [model field reference](https://docs.djangoproject.com/en/1.10/ref/models/fields/#model-field-types)에서 전체 목록을 찾을 수 있다. 또한, 커스텀 모델 필드를 쉽게 만들수 있다.

### Field options

각 필드는 필드 별 인자 세트를 갖는다. 예를 들면, `CharField`는 `VARCHAR`의 사이즈를 지정하는 `max_length` 인자가 필요하다.

또한 모든 필드 타입으로 사용할 수 있는 공통 인자 세트가 있다. 모든 것은 옵션이다. 가장 자주 사용하는것만 요약해보자.

#### null

`True`면, 데이터베이스는 `NULL`이라는 빈 값을 데이터베이스에 저장할 것 이다. (Default `False`)

#### blank

`True` 면, 필드는 공백을 허용한다. (Default `False`)

**주의** : `null`과는 다르다. `null`은 순전히 데이터베이스 관련인 반면, `blank`는 유효성과 관련이다.

#### choices

필드의 choices로 사용할 2차원 튜플을 반복 가능한 (예 : list 또는 tuple) 객체에 담아 주면, 기본 폼 위젯은 문자 필드 대신 선택 상자(select box)가되어 주어진 선택 사항으로 선택 항목을 제한한다. (admin 페이지에서 확인 할 수 있다.)

```python
from django.db import models

class Person(models.Model):
    SHIRT_SIZES = (
        ('S', 'Small'),
        ('M', 'Medium'),
        ('L', 'Large'),
    )
    name = models.CharField(max_length=60)
    shirt_size = models.CharField(max_length=1, choices=SHIRT_SIZES)
```

각 튜플의 첫 번째 요소는 데이터베이스에 저장될 값이다. 두 번째 요소는 폼 위젯 또는 `ModelChoiceField` 에 의해 표시된다. 모델 인스턴스가 주어지면, choices 필드를 위한 표시 값은 `get_FOO_display()` 메서드를 사용해 choices 값에 접근할 수 있다.

```shell
>>> p = Person(name="Fred Flintstone", shirt_size="L")
>>> p.save()
>>> p.shirt_size
'L'
>>> p.get_shirt_size_display()
'Large'
```



#### default

기본 값을 위한 필드이다. 값 또는 호출가능한 객체면 가능하다. 만약 호출하는 경우라면, 새로운 객체가 생성될 때 마다 호출된다.

```python
from django.db import models


class Person(models.Model):
    SHIRT_SIZES = [
        ('S', 'Small'),
        ('M', 'Medium'),
        ('L', 'Large'),
    ]
    name = models.CharField(max_length=60)
    shirt_size = models.CharField(max_length=1, choices=SHIRT_SIZES, default=SHIRT_SIZES[1][0])
    nationality = models.CharField(max_length=100, default='South Korea')
```

```shell
In [1]: from person.models import Person

# creat하면 save까지 모두 완료
In [2]: p = Person.objects.create(name='Tom')

In [4]: p.nationality
Out[4]: 'South Korea'

In [5]: p.name
Out[5]: 'Tom'
```



#### help_text

위젯과 함께 표시될 추가 "help" 텍스트이다. 필드를 폼에 사용하지 않을지라도 문서화에 유용하다.

```python
from django.db import models


class Person(models.Model):
    name = models.CharField(max_length=60, help_text='성과 이름을 붙여 적으세요.')
```



#### primary_key

`True`면, 이 필드는 모델의 기본 키이다.

모델의 어떤 필드에도 `primary_key=True` 를 지정하지 않으면, 장고가 기본 키를 보유하는 `IntegerField` 를 자동으로 추가한다. 그래서 디폴트 기본키 동작을 재정의하려는 경우가 아니면, 어떤 필드에도 `primary_key=True`를 설정할 필요가 없다.

기본 키 필드는 **읽기 전용**입니다. 기존 객체의 기본 키 값을 변경하고 저장하면, 이전 객체와 함께 새로운 객체가 생성된다.

```shell
In [1]: from person.models import Person

In [2]: person = Person.objects.all()

In [3]: person
Out[3]: <QuerySet [<Person: Fred>, <Person: Tom>]>

In [4]: p1 = Person.objects.get(id=1)

# 기본 키 변경
In [7]: p1.id = 5

In [8]: p1.save()

In [9]: p1.id
Out[9]: 5

# 이전 객체
In [10]: Person.objects.get(id=1)
Out[10]: <Person: Fred>

# 생성된 객체
In [11]: Person.objects.get(id=5)
Out[11]: <Person: Fred>
```



#### unique

`True`면, 이 필드는 테이블 전체에서 유일해야 한다.

### Automatic primary key fields

기본적으로 장고는 각 모델에 다음의 필드를 제공한다.

```python
id = models.AutoField(primary_key=True)
```

자동 증가 기본 키이다. 만약 커스텀 기본 키를 지정하고 싶다면, 필드 중 하나에 `primary_key=True` 를 지정한다. 장고가 `Field.primary_key`를 명시적으로 설정한 것을 알면, 자동 증가 기본키를 추가하지 않는다.

각 모델은 `primary_key=True` (명시적으로 선언되거나 자동으로 추가됨)를 갖기 위해 정확히 하나의 필드가 필요하다.

### Verbose field names

`ForeignKey`, `ManyToManyField`, `OneToOneField`를 제외한 각 필드 타입은 선택적인(optional) **첫번째 위치 인자**로 verbose name을 취한다. 만약 verbose 이름이 주어지지 않으면, 장고는 필드의 속성 이름을 사용해 자동으로 생성한다. 이때 underscore(_)는 spaces(공백)으로 변환한다.

```python
# verbose name: person's first name
# 변수명 대신 verbose name으로 관리자페이지 필드명이 출력 됨
first_name = models.CharField("person's first name", max_length=30)

# ForeignKey 일 때는 키워드 인자로 넘겨줌
poll = models.ForeignKey(
    Poll,
    on_delete=models.CASCADE,
    verbose_name="the related poll",
)
```

관례상 `verbose_name`의 첫 글자를 대문자로 사용하지 않습니다. 장고는 필요할 때 자동으로 첫 글자를 대문자로 변환한다.



### Relationships

분명히 관계형 데이터베이스의 힘은 테이블을 서로 연관시키는 데 있다. 장고는 데이터베이스 관계의 가장 일반적인 세 가지 유형을 정의하여 제공한다.  many-to-one, many-to-many and one-to-one.

#### Many-to-one relationships

다대일 관계를 정의하려면 `django.db.models.ForeignKey`를 사용한다 . 다른 `Field` 타입과 마찬가지로 모델의 클래스 속성으로 포함하여 사용한다. `ForeignKey` 모델과 관련된 **하나의 클래스 위치 인자**가 필요하다.

예를 들어 `Car` 모델이 `Manufacturer`를 가진다면 다음 정의를 사용한다. 즉, `Manufacturer` 가 여러 자동차를 생산하지만 각 `Car`는 오직 하나의 `Manufacturer`를 가진다.

```python
from django.db import models


class Manufacturer(models.Model):
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name


class Car(models.Model):
    manufacturer = models.ForeignKey(Manufacturer)
    title = models.CharField(max_length=100)

    def __str__(self):
        return '{} {}'.format(self.manufacturer, self.title)
```

`재귀 관계`(그 자체와 다 대일 관계가 있는 객체)와 `아직 정의되지 않은 모델과 관계`를 만들 수 있다. 재귀 관계를 만들려면 `models.ForeignKey('self', on_delete = models.CASCADE)`를 사용한다. 아직 정의되지 않은 모델과 관계를 작성해야하는 경우 **모델 오브젝트 자체가 아닌 모델 이름을 사용**할 수 있다.

(아직 정의되지 않은 모델과 관계 예)

```python
from django.db import models


class Car(models.Model):
    '''
    Car와 Manufacturer의 위치를 변경하면,
    아직 정의되지 않은 모델을 참조하게 되어 아래의 에러가 발생한다.
    manufacturer = models.ForeignKey(Manufacturer)
    NameError: name 'Manufacturer' is not defined
    '''
    manufacturer = models.ForeignKey('Manufacturer')
    title = models.CharField(max_length=100)

    def __str__(self):
        return '{} {}'.format(self.manufacturer, self.title)


class Manufacturer(models.Model):
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name

```



(다대일 재귀 관계 예: 강사와 학생관계)

```python
from django.db import models


class Person(models.Model):
    SHIRT_SIZES = [
        ('S', 'Small'),
        ('M', 'Medium'),
        ('L', 'Large'),
    ]

    """
    일대다 재귀 관계 실습
    """
    instructor = models.ForeignKey(
        'self',  # 외래키를 자기 자신의 클래스에 대해 갖을 때
        verbose_name='담당 강사',
        null=True,
        blank=True,
        on_delete=models.SET_NULL,  # 외래키로 참조된 객제가 삭제될 때, 장고가 실행할 행동 지정
        related_name='student_set'  # 역참조 할 때, 사용할 이름 지정
    )

    """
    related_name 사용 이유 확인
    owner와 mentor 작성 후 makemigrations를 진행하면 아래와 같은 오류가 발생한다.
    person.Person.mentor: Reverse accessor for 'Person.mentor' clashes with reverse accessor for 'Person.owner'.
    HINT: Add or change a related_name argument to the definition for 'Person.mentor' or 'Person.owner'.
    그렇기 때문에 related_name을 작성해주는 것이 좋다.
    """
    owner = models.ForeignKey(
        'self',
        verbose_name='사장님',
        null=True,
        blank=True,
        on_delete=models.SET_NULL,
        related_name='employee_set',
    )
    mentor = models.ForeignKey(
        'self',
        verbose_name='멘토',
        null=True,
        blank=True,
        on_delete=models.SET_NULL,
        related_name='mentee_set'
    )

    name = models.CharField('이름', max_length=60, help_text='성과 이름을 붙여 적으세요.')
    shirt_size = models.CharField('셔츠 사이즈', max_length=1, choices=SHIRT_SIZES, default=SHIRT_SIZES[1][0])
    nationality = models.CharField('국적', max_length=100, default='South Korea')

    def __str__(self):
        return self.name
```

```shell

In [1]: from person.models import Person

In [3]: Person.objects.create(name='lhy')
Out[3]: <Person: lhy>

In [5]: instructor = Person.objects.get(name='lhy')

In [7]: p1 = Person.objects.get(id=1)
In [8]: p2 = Person.objects.get(id=2)

In [11]: p1.instructor = instructor
In [12]: p1.save()

In [13]: p2.instructor = instructor
In [14]: p2.save()

In [15]: p1.instructor
Out[15]: <Person: lhy>

In [16]: p2.instructor
Out[16]: <Person: lhy>

# 자동으로 생성되는 역참조 이름이 person_set이라 명확하게 하기 위해,
# related_name을 지정한다.
In [20]: instructor.person_set.all()
Out[20]: <QuerySet [<Person: Fred>, <Person: Tom>]>


In [1]: from person.models import Person

In [2]: instructor = Person.objects.get(name='lhy')

In [3]: instructor.student_set.all()
Out[3]: <QuerySet [<Person: Fred>, <Person: Tom>]>
```



#### Many-to-many relationships

다대다 관계를 정의하려면 `ManyToManyField`를 사용한다. 다른 `Field` 타입과 마찬가지로 모델의 클래스 속성으로 포함하여 사용한다. `ManyToManyField` 모델과 관련된 클래스 위치 인자가 필요하다.

예를 들어, `Pizza`에 여러 개의 `Topping` 객체가있는 경우 다음과 같이 표현할 수 있다. (`Topping` 이 여러 피자에있을 수 있으며, 각 `Pizza` 에 여러 토핑이있을 수 있다.)

```python
from django.db import models


class Topping(models.Model):
    title = models.CharField(max_length=30)

    def __str__(self):
        return self.title


class Pizza(models.Model):
    title = models.CharField(max_length=100)

    # Pizza와 Topping은 서로 ManyToMany 관계
    # 포함관계에서 상위요소에 해당하는 클래스에 ManyToMany를 선언
    toppings = models.ManyToManyField(Topping)

    def __str__(self):
        return '{} {}'.format(
            self.title,
            ', '.join(self.toppings.value_list('title', flat=True))
        )
```

```shell
In [1]: from pizza.models import Pizza, Topping

In [2]: bp = Pizza.objects.create(title='불고기 피자')

In [3]: cp = Pizza.objects.create(title='치즈 피자')

In [4]: sp = Pizza.objects.create(title='슈퍼슈프림 피자')

In [5]: t1 = Topping.objects.create(title='치즈')

In [6]: t2 = Topping.objects.create(title='햄')

In [7]: t3 = Topping.objects.create(title='불고기')

In [8]: t4 = Topping.objects.create(title='피망')

In [9]: sp.toppings.add(t1, t2, t4)

In [10]: sp
Out[10]: <Pizza: 슈퍼슈프림 피자, Toppings : 치즈, 햄, 피망>

In [13]: cp.toppings = t3, t4

In [14]: cp
Out[14]: <Pizza: 치즈 피자 불고기, 피망>

In [18]: Pizza.objects.all()
Out[18]: <QuerySet [<Pizza: 불고기 피자 >, <Pizza: 치즈 피자 불고기, 피망>, <Pizza: 슈퍼슈프림 피자 치즈, 햄, 피망>]>

In [19]: Pizza.objects.values_list()
Out[19]: <QuerySet [(1, '불고기 피자'), (2, '치즈 피자'), (3, '슈퍼슈프림 피자')]>

In [21]: Pizza.objects.values_list('title')
Out[21]: <QuerySet [('불고기 피자',), ('치즈 피자',), ('슈퍼슈프림 피자',)]>

In [22]: Pizza.objects.values_list('title', flat=True)
Out[22]: <QuerySet ['불고기 피자', '치즈 피자', '슈퍼슈프림 피자']>

In [23]: ', '.join(Pizza.objects.values_list('title', flat=True))
Out[23]: '불고기 피자, 치즈 피자, 슈퍼슈프림 피자'

In [25]: Pizza.objects.get(id=1)
Out[25]: <Pizza: 불고기 피자 >

In [26]: Pizza.objects.get(id=1).toppings
Out[26]: <django.db.models.fields.related_descriptors.create_forward_many_to_many_manager.<locals>.ManyRelatedManager at 0x10ba430b8>
```

migrations 파일을 보면 아래와 같은 구조로 되어 있다.

- pizza_pizza : 따로 연결된 키 없음
  - id
  - title
- pizza_topping :  따로 연결된 키 없음
  - id
  - title
- pizza_pizza_toppings : 서로 연결하는 foreign key 존재 (REFERENCES)
  - id
  - pizza_id
  - topping_id

```
BEGIN;
--
-- Create model Pizza
--
CREATE TABLE "pizza_pizza" (
	"id" integer NOT NULL PRIMARY KEY AUTOINCREMENT,
	"title" varchar(100) NOT NULL
	);
--
-- Create model Topping
--
CREATE TABLE "pizza_topping" (
  "id" integer NOT NULL PRIMARY KEY AUTOINCREMENT,
  "title" varchar(30) NOT NULL
);
--
-- Add field toppings to pizza
--
CREATE TABLE "pizza_pizza_toppings" (
  "id" integer NOT NULL PRIMARY KEY AUTOINCREMENT,
  "pizza_id" integer NOT NULL REFERENCES "pizza_pizza" ("id"),
  "topping_id" integer NOT NULL REFERENCES "pizza_topping" ("id"));

CREATE UNIQUE INDEX "pizza_pizza_toppings_pizza_id_5ff9fcdb_uniq" ON "pizza_pizza_toppings" ("pizza_id", "topping_id");
CREATE INDEX "pizza_pizza_toppings_c3c2621e" ON "pizza_pizza_toppings" ("pizza_id");
CREATE INDEX "pizza_pizza_toppings_3a22b0a0" ON "pizza_pizza_toppings" ("topping_id");

COMMIT;
```

`ForeignKey`와 마찬가지로 `재귀 관계`(그 자체와 다 대다 관계가 있는 객체)와 `아직 정의되지 않은 모델과의 관계`를 만들 수 있다. 재귀 관계를 만들려면 `models.ForeignKey('self', on_delete = models.CASCADE)`를 사용한다. 아직 정의되지 않은 모델과 관계를 작성해야하는 경우 **모델 오브젝트 자체가 아닌 모델 이름을 사용**할 수 있다.

(다대다 재귀함수 예: 친구관계, 트위터 팔로잉/팔로우 관계)

```python
class User(models.Model):
    name = models.CharField(max_length=50)
    friends = models.ManyToManyField('self')

    def __str__(self):
        return self.name
```

```shell
In [1]: from person.models import User

In [3]: u1 = User.objects.create(name='lhy')

In [4]: u2 = User.objects.create(name='na')

In [5]: u3 = User.objects.create(name='hml')

# 연결되어 있는거를 확인할 수 있음
In [6]: u1.friends
Out[6]: <django.db.models.fields.related_descriptors.create_forward_many_to_many_manager.<locals>.ManyRelatedManager at 0x10e51ebe0>

In [7]: u1.friends.add(u2)

In [8]: u1.friends.all()
Out[8]: <QuerySet [<User: na>]>

In [9]: u2.friends.all()
Out[9]: <QuerySet [<User: lhy>]>

# 자기 자신 참조라 user_set이 없음
In [10]: u1.user_set.all()
--------------------------------------------------------
AttributeError: 'User' object has no attribute 'user_set'
```

장고는 위의 모델(`ManyToManyFields`로 자기자신을 가질 경우)을 처리할 때, User 클래스에 user_set 속성을 추가하지 않도록 정의한다. 대신에 대칭적(symmetrical)이라고 가정한다. 즉, 내가 너의 친구이면 너도 나의 친구라고 가정한다.

만약 **재귀 다대다 관계**`(ManyToManyField('self'))`에서 대칭구조를 원하지 않으면, `symmetrical=False`로 설정한다.

```python
class User(models.Model):
    # 팔로우, 팔로잉을 나타내는 경우
    # ManyToManyField에 symmetrical 옵션으로 설정 가능
    following = models.ManyToManyField(
        'self',
        related_name='follower_set',
        symmetrical=False,
    )
    friends = models.ManyToManyField('self')
    name = models.CharField(max_length=50)

    def __str__(self):
        return self.name
```

```shell

In [1]: from person.models import User

In [2]: u1 = User.objects.get(id=1)

In [3]: u2 = User.objects.get(id=2)

In [6]: u1.following.add(u2)

In [7]: u1.following.all()
Out[7]: <QuerySet [<User: na>]>

In [10]: u2.following.all()
Out[10]: <QuerySet []>

In [6]: u1.follower_set.all()
Out[6]: <QuerySet []>

In [7]: u2.follower_set.all()
Out[7]: <QuerySet [<User: lhy>]>
```

필수는 아니지만, `ManyToManyField`의 이름(위의 예에서의 toppings)은 관련된 모델 객체 세트를 설명하는 복수형으로 사용할 것을 권장한다.

어떤 모델이 `ManyToManyField`를 가지고 있는지 중요하지 않지만, 두 모델 중 **하나에만** 넣어야 한다.

일반적으로 `ManyToManyField` 인스턴스는 폼에서 편집할 객체 안에 있어야 한다. 위의 예에서 (Topping이 pizzas의 ManyToManyField를 갖는것보다) toppings는 Pizza 안에 있다. pizza가 topings를 가지는게 topping이 여러개의 피자를 가지는거보다  더 자연스러운 생각이기 때문이다. 위에 설정된 방식대로 `Pizza` 양식을 사용하면 사용자가 토핑을 선택할 수 있다.



#### Extra fields on many-to-many relationships

피자와 토핑을 믹싱하고 매칭하는 것과 같은 단순한 다대다 관계 만 처리 할 때는 `ManyToManyField`만 있으면된다. 그러나 때로는 두 모델 간의 관계에 데이터를 연결해야 할 수도 있다.

예를 들어, 뮤지션들이 속한 그룹을 추적하는 어플리케이션의 경우를 생각해보자. person과 멤버가 속한 group 사이에 다대다 관계가 있다. 이 관계를 표현하기 위해 `ManyToManyField`를 사용할 수 있다. 그러나 그룹에 가입한 날짜와 같이 수집하려는 멈버십 정보의 세부 정보가 많이 있다.

이러한 상황에서 장고는 다대다 관계를 관리하는 데 사용될 모델을 지정할 수 있다. **중간자(intermediate) 모델**에 별도의 필드를 추가 할 수 있다. 중간자 모델은 `ManyToManyField`에 중개자로 행동할 모델을 가르키는 `through`인자를 사용해  연관된다.

```
아이돌
	레이나
	나나
	리지
	유이
	가희

아이돌 그룹
	에프터스쿨
	오렌지캬라멜

멤버쉽
	가입 날짜
```

```python
# 다대다 모델 + 추가적 필드 : ManyToManyField + 중간자 모델
class Idol(models.Model):
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name


class Group(models.Model):
    # Group과 Idol은 서로 ManyToMany 관계
    # 포함관계에서 상위요소에 해당하는 클래스에 ManyToMany를 선언
    name = models.CharField(max_length=100)
    members = models.ManyToManyField(
        'Idol',
        # 별도의 필드를 추가해 두 관계를 정의하는 중간자 모델
        # MemberShip이 정의되기 전이므로 String으로 작성
        through='MemberShip',
    )

    def __str__(self):
        return self.name


class Membership(models.Model):
    group = models.ForeignKey(Group)
    Idol = models.ForeignKey(Idol)
    date_join = models.DateTimeField()
```

중간자 모델을 설정할 때,  다대다 관계에 관여된 모델에 **외래 키(foreign key)를 명시적으로 지정**한다. 이 명시적 선언은 두 모델이 어떻게 관련이 있는지 정의한다.

중간 모델에는 몇 가지 제한 사항이 있다.

- 중간자 모델은 소스(source) 모델(예: `Group`)에 대한 **단 하나의 외래 키**를 가져야만 한다. 또는 장고가 `ManyToManyField.through_fields`로 관계를 사용할 수 있게 외래키를 명시적으로 지정해야만 한다. 만약 하나 이상의 외래 키를 가지고 `through_fields`도 선언되지 않았다면, 유효성 검증 에러가 발생한다. 유사한 제한이 대상(target) 모델(예: `Person`)에 대한 외래 키에도 적용된다.
- 중간자 모델을 통해 자신을 다대다 관계로 가지는 모델의 경우 같은 모델에서 두 개의 외래 키가 허용되지만, 다대다 관계의 다른 두개의 측면으로 처리됩니다. 만약 두개 이상의 외래 키를 가진다면, 위의 항목처럼 `through_fields`를 선언해야만 한다. 그렇지 않으면 유효성 검증 에러가 발생한다.
- 중간자 모델을 사용하여 자신의 모델로부터 다대다 관계를 정의할 때, 반드시 `symmetrical=False`를 사용해야 한다.

이제 중개자 모델(예: `Membership`)을 사용하기위해 `ManyToManyField`를 설정 했으므로 이제 다대다 관계를 만들 준비가 된 것이다. 중간자 모델의 인스턴스를 생성해서 수행한다.

(1번 제약 사향 예)

```python
# 다대다 모델 + 추가적 필드 : ManyToManyField + 중간자 모델
class Idol(models.Model):
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name


class Group(models.Model):
    # Group과 Idol은 서로 ManyToMany 관계
    # 포함관계에서 상위요소에 해당하는 클래스에 ManyToMany를 선언
    name = models.CharField(max_length=100)
    members = models.ManyToManyField(
        'Idol',
        # 별도의 필드를 추가해 두 관계를 정의하는 중간자 모델
        # MemberShip이 정의되기 전이므로 String으로 작성
        through='MemberShip',
        # 제약사항 1번
        # 관계를 형성하는 두 클래스 중 한 클래스라도 한 번을 초과하여 필드로 사용할 경우
        # 기본 값 설정
        through_fields=('group', 'idol'),
    )

    def __str__(self):
        return self.name


class Membership(models.Model):
    group = models.ForeignKey(Group)
    Idol = models.ForeignKey(Idol)
    date_join = models.DateTimeField()
    # 제약사항 1번
    # 추천인이라는 Target 모델에 대한 추가 필드가 있을 경우,
    # through_fields를 정의해야 한다.
    recommneder = models.ForeignKey(
        Idol,
        null=True,
        blank=True,
        related_name='recommender_membership_set'
    )
```

(2번 제약 사항 예)

```
사람
	친구관계
	(기본적으로 생성되는 정보)
	사람1
	사람2
	...

친구관계에 대한 정보
	사람1(from_user)
	사람2(to_user)
	...
	누가 소개 시켜줬느냐 (사람3, recommender)
	소개해준날
```

```python

# 다대다 모델
class User(models.Model):
    '''
    다대다 재귀 관계 실습
    '''
    # 팔로우, 팔로잉을 나타내는 경우
    # ManyToManyField에 symmetrical 옵션으로 설정 가능
    following = models.ManyToManyField(
        'self',
        related_name='follower_set',
        symmetrical=False,
    )
    # symmetrical 기본 값은 True > 한 객체에서 '친구' 설정 시, 반대쪽도 자동으로 '친구' 설정
    # 중간자 모델을 사용하려면, symmetrical이 반드시 False여야 한다.
    # symmetrical=True 설정을 유지해 동시에 친구 관계를 맺으려면,
    # 두 관계를 한 번에 생성해주는 메서드를 만들어 사용해야 한다.
    friends = models.ManyToManyField('self')
    name = models.CharField(max_length=50)

    def __str__(self):
        return self.name


class UserFriendsInfo(models.Model):
    from_user = models.ForeignKey(
        User,
        related_name='+',
    )
    to_user = models.ForeignKey(
        User,
        related_name='+',
    )
    # 중개인이라는 User ForeignKey가 추가로 존재할 경우,
    # 중간자 모델을 사용하는 곳에서 through_fields를 정의해야 함
    recommender = models.ForeignKey(
        User,
        null=True,
        blank=True,
        related_name='recommender_userfriendsinfo_set'
    )
    when = models.DateTimeField(auto_now_add=True)
```



```shell
In [2]: from person.models import Idol, Group, MemberShip

# 객체 생성
In [3]: yui = Idol.objects.create(name='유이')

In [4]: reina = Idol.objects.create(name='레이나')

In [5]: nana = Idol.objects.create(name= '나나')

In [6]: lizzy = Idol.objects.create(name='리지')

In [7]: eyoung = Idol.objects.create(name='이영')

In [8]: kaeun = Idol.objects.create(name='가은')

In [10]: Idol.objects.values_list('name', flat=True)
Out[10]: <QuerySet ['레이나', '나나', '유이', '레이나', '나나', '리지', '이영', '가은']>

In [11]: afterschool = Group.objects.create(name='애프터스쿨')

In [12]: orangecaramel = Group.objects.create(name='오렌지 캬라멜')

In [16]: from datetime import date, datetime, time, timedelta

In [17]: m1 = MemberShip(
    ...:     idol=reina,
    ...:     group=orangecaramel,
    ...:     date_joined=date(2010, 6, 17))

In [18]: m1.save()


In [21]: MemberShip.objects.create(
    ...: idol=nana,
    ...: group=orangecaramel,
    ...: date_joined=date(2010, 6, 17))
Out[21]: <MemberShip: MemberShip object>


In [22]: MemberShip.objects.create(
    ...: idol=lizzy,
    ...: group=orangecaramel,
    ...: date_joined=date(2010, 6, 17))
Out[22]: <MemberShip: MemberShip object>


# 확인
In [23]: MemberShip.objects.all()
Out[23]: <QuerySet [<MemberShip: MemberShip object>, <MemberShip: MemberShip object>, <MemberShip: MemberShip object>]>

In [24]: MemberShip.objects.count()
Out[24]: 3

In [26]: MemberShip.objects.values_list('idol__name', flat=True)
Out[26]: <QuerySet ['레이나', '나나', '리지']>

In [27]: orangecaramel.members.all()
Out[27]: <QuerySet [<Idol: 레이나>, <Idol: 나나>, <Idol: 리지>]>

# 삭제할 때는 이런식으로 조회해 삭제
In [31]: MemberShip.objects.values_list('id', 'idol__name')
Out[31]: <QuerySet [(1, '레이나'), (2, '나나'), (3, '리지')]>

In [32]: nana.group_set.all()
Out[32]: <QuerySet [<Group: 오렌지 캬라멜>]>

In [33]: nana.membership_set.all()
Out[33]: <QuerySet [<MemberShip: MemberShip object>]>

In [34]: nana.membership_set.values_list('group__name', 'date_joined')
Out[34]: <QuerySet [('오렌지 캬라멜', datetime.datetime(2010, 6, 17, 0, 0, tzinfo=<UTC>))]>

#  전체 멤버 멤버쉽 추가
In [36]: for idol in Idol.objects.all():
    ...:     MemberShip.objects.create(
    ...:         idol=idol,
    ...:         group=afterschool,
    ...:         date_joined=date(2009, 1, 15)
    ...:     )
    ...:

# 입력되어있던 멤버가 있다.
# 지우고 다시해야되는데 그냥 진행했다.
In [37]: afterschool.members.values_list('name')
Out[37]: <QuerySet [('레이나',), ('나나',), ('유이',), ('레이나',), ('나나',), ('리지',), ('이영',), ('가은',)]>

In [38]: orangecaramel.members.all()
Out[38]: <QuerySet [<Idol: 레이나>, <Idol: 나나>, <Idol: 리지>]>


In [39]: lizzy.group_set.all()
Out[39]: <QuerySet [<Group: 오렌지 캬라멜>, <Group: 애프터스쿨>]>

In [41]: lizzy.membership_set.values_list('group__name', 'date_joined')
Out[41]: <QuerySet [('오렌지 캬라멜', datetime.datetime(2010, 6, 17, 0, 0, tzinfo=<UTC>)), ('애프터스쿨', datetime.datetime(2009, 1, 15, 0, 0, tzinfo=<UTC>))]>



In [1]: from person.models import Group, Idol, MemberShip

In [2]: MemberShip.objects.all()
Out[2]: <QuerySet [<MemberShip: 오렌지 캬라멜 (레이나)>, <MemberShip: 오렌지 캬라멜 (나나)>, <MemberShip: 오렌지 캬라멜 (리지)>, <MemberShip: 애프터스쿨 (레이나)>, <MemberShip: 애프터스쿨 (나나)>, <MemberShip: 애프터스쿨 (유이)>, <MemberShip: 애프터스쿨 (레이나)>, <MemberShip: 애프터스쿨 (나나)>, <MemberShip: 애프터스쿨 (리지)>, <MemberShip: 애프터스쿨 (이영)>, <MemberShip: 애프터스쿨 (가은)>]>


# 중간자모델은 추가한 필드 때문에 crete를 사용할 수 없다.
# 추가하고 싶으면 무조건 Membership 객체로만 정의 가능하다.

# clear 삭제라기보다는 연결관계가 사라진다.
# 중간자 모델에 있는 것만 사라진다.


In [22]: lizzy = Idol.objects.get(name='리지')


In [24]: lizzy.membership_set.get(group__name='애프터스쿨')
Out[24]: <MemberShip: 애프터스쿨 (리지)>
```


#### One-to-one relationships
- 기본키를 확장할 때 사용한다.
- 예를 들면 유저와 유저 프로필 관계와 같다.
- One-to-One 관계는 한쪽 테이블만 많이 쓰는 경우이다.

### Models across files
- import 해서 사용하면 된다.

### Field name restrictions
- 파이썬 예약어 사용불가
- underscore(_) 두개 이상 사용 불가
- sql문은 자동으로 escape 처리하므로 사용 가능

### Custom field types
- 예를 들어 핸드폰 번호 필드를 커스텀해서 사용할 수 있다.
- 유효성 검사까지 가능하다.
- 디스플레이 방식도 정의 할 수 있다.

## Meta options
- 클래스에 대한 정보
- 모델 메타데이터는 "필드를 제외한 모든 것"

```python
from django.db import models

class Ox(models.Model):
    horn_length = models.IntegerField()

    class Meta:
        ordering = ["horn_length"]
        verbose_name_plural = "oxen"
```


## Model attributes
### objcects
- 모델의 가장 중요한 속성은 **Manager**
- Custom Manager가 정의되어 있지 않는 한, 기본 이름은 **objects**
- **Manager**는 모델 클래스를 통해서만 접근 가능 (모델 인스턴스는 불가)

## Model methods
- **Manager** 메서드는 "테이블 전체" 영역에 대해 작업을 수행
- **모델 메서드**는 특정 모델 인스턴스에 작동
- 모델에 자동으로 부여되는 메서드 (오버라이드 가능)
    - `__str__()` : 모든 객체의 유니코드 "표현"을 반환하는 Python 메서드
    - `get_absolute_url()`


### Overriding predefined model methods
또 다른 **모델 메서드**이다. 커스텀하려는 데이터베이스 동작을 캡슐화한다.
- `save()`
- `delete()`

예를 들면 글이 몇 개인지 필드에 미리 저장해두고, 글이 추가되거나 삭제될 때 그 값을 증감한다.

```python
from django.db import models

class Blog(models.Model):
    name = models.CharField(max_length=100)
    tagline = models.TextField()

    def save(self, *args, **kwargs):
        do_something
        # Call the "real" save() method
        super(Blog, self).save()
        do_something_else()
```
super 클래스 메서드를 호출해야 한다. 그래야 데이터베이스에 저장하는 것을 보장한다.


## Model inheritance
장고의 모델상속 파이썬의 상속과 유사하다. 장고는 세가지 상속 스타일이 있다.

1. Abstract base classes : 많이 사용하는 방식
2. Multi-table inheritance : 편리하지만 느림, 사용하지 않는것을 권장
3. Proxy models : 특이한 모델로 거의 사용하지 않음


### Abstract base classes (aka. abc)
- 공통된 정보를 여러 다른 모델에 사용하려고 할 때 유용하다.
- 추상 클래스는 테이블로 생성되지 않는다.
- 상속받은 클래스에서 그 속성들만 만들어 진다.

```python
# 정보 주는데만 사용
# 테이블이 생성되지 않으므로,
# Shop.objects.* 도 안 됨
class Shop(models.Model):
    name = models.TextField()
    address = models.TextField()

    class Meta:
        abstract = True

# 테이블 생성
class Restaurant(Shop):
    pass #만 해도 naame, address 생성


class PC(Shop):
    pass #만 해도 naame, address 생성
```


#### Meta inheritance

메타도 추상화 가능하다

```python
from django.db import models

class CommonInfo(models.Model):
    # ...
    class Meta:
        abstract = True
        ordering = ['name']

class Student(CommonInfo):
    # ...
    class Meta(CommonInfo.Meta):
        # 상송받은 클래스는 자동으로
        # abstract=false로 설정

        # 몇 가지 속성은 Meta에 포함하지 않는 것이 좋음
        # 자식 클래스의 db_table이 모두 같아지기 때문이
        db_table = 'student_info'
```


#### Be careful with related_name and related_query_name
- related_name : 관련된 객체 역참조 할 때, 사용하는 이름
- related_query_name : 타겟 모델로부터 역방향 필터를 할 때, 사용하는 이름

```python
# Declare the ForeignKey with related_query_name
class Tag(models.Model):
    article = models.ForeignKey(
        Article,
        on_delete=models.CASCADE,
        # 기본값은 tag_set
        related_name="tags",
        related_query_name="tag",
    )
    name = models.CharField(max_length=255)

# reverse filter
Article.objects.filter(tag__name="important")
```

````shell
# 다 주석 처리
In [1]: from person.models import Article, Tag

In [2]: Article.objects.filter(tag__name='Tag1')
Out[2]: <QuerySet []>

In [3]: a.tag_set.all()

# related name만 활성화
# related_query_name이 정의되지 않을 경우,
# related name을 기본 값으로 사용
In [2]: Article.objects.filter(tags__name='Tag1')

# related_query_name까지 활성화
In [2]: Article.objects.filter(tag__name='Tag1')
````
추상 클래스가 related_name과 related_query_name 속성을 가지면, 상속받은 자식 클래스도 같은 이름을 갖게 된다. 그럴때는 아래와 같이 한다.

```python
class Base(models.Model):
    m2m = models.ManyToManyField(
        OtherModel,
        related_name="%(app_label)s_%(class)s_related",
        related_query_name="%(app_label)s_%(class)ss",
    )
```


### Multi-table inheritance
```python
from django.db import models

class Place(models.Model):
    name = models.CharField(max_length=50)
    address = models.CharField(max_length=80)

class Restaurant(Place):
    serves_hot_dogs = models.BooleanField(default=False)
    serves_pizza = models.BooleanField(default=False)
```
위의 코드를 도식화해보면, 다음과 같다.
```
Place
 - id
 - name
 - address
Restaruant(Place)
 - p_id (place 상속), One-To-One으로 연결
 - place_name (place 상속)
 - place_address (place 상속)
 - serv_hot_dog (고유 속성)
 - serv_pizza (고유 속성)
```
실제로 Restaruant 테이블은 Place, Restaruant 필드를 모두 가지고 있다. 하지만 Place는 id, name, address 필드만 있다.

**Abstract base classes**라면, Restaruant 테이블은 모든 필드를 가지지만, Place는 테이블이 없다. **Multi-table**은 두 개의 테이블이 분리되어 있고 One-To-One 연결되는 방식이다.


### Proxy models

멀티 테이블 상속은 부모와 자식 모두 만들지만, 프록시 모델은 부모 테이블만 만든다. 자식 테이블은 생기지 않으므로, 부모 모델만 정의 할 수 있다.

```python
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)

class MyPerson(Person):
    class Meta:
        proxy = True

    def do_something(self):
        # ...
        pass
```

Person으로 접근할 수 있고, MyPerson으로도 접근할 수 있다. 둘다 같은 결과를 반환한다.

```shell
>>> p = Person.objects.create(first_name="foobar")
>>> MyPerson.objects.get(first_name="foobar")
<MyPerson: foobar>
```
같은 객체에서 다른 동작을 하고 싶을 경우에 사용한다. (예: 사용자별로 알림을 보낼 때)

```python
class OrderedPerson(Person):
    class Meta:
        ordering = ["last_name"]
        proxy = True
```
`Person.objects.order_by()`하면 그냥 오름차순으로 나오지만,
`OrderedPerson.objects.order_by()`하면 lst_name을 기준으로 오름차순하여 나온다.
