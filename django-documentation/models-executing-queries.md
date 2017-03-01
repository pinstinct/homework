
# [QuerySets: Making queries](https://docs.djangoproject.com/en/1.10/topics/db/queries/)

## Creating objects
- 객체를 생성 후 `save()`를 호출해야한다. 그래야 데이터베이스에 저장된다.
- `create()` 메서드 는 한번에 객체를 생성하고 저장한다.


### Saving ForeignKey and ManyToManyField fields
- ForeignKey 필드는 일반 필드와 동일한 방법으로 저장한다.
- **ManyToManyField**는 다르게 동작한다.
    - `add()` 메서드를 사용해 관계에 레코드를 추가한다.
    - `add()` 사용하면 `save()`는 따로 하지 않아도 된다.

```shell

In [1]: from blog.models import Blog, Author, Post

# Blog 인스턴스 생성
In [2]: b = Blog.objects.create(name='Beatles Blog', tagline='All the latest Beatles news')

In [3]: author = ['John', 'Paul', 'George', 'Ringo']

# Author 인스턴스 생성
# python dynamic variable name으로 검색
# globals() 전역 변수들을 dictionary로 반환
# globals() dic에 key로 name.lower() 정의
In [7]: for name in author:
   ...:     globals()[name.lower()] = Author.objects.create(name=name)
   ...:

# 확인
In [8]: john
Out[8]: <Author: John>

# Post 인스턴스 생성
In [10]: p = Post.objects.create(blog=b, title='Fisrt Post')

# add() 메서드에 여러개 인자를 포함해 호출하는 법
In [12]: p.authors.add(john, paul, george, ringo)

# 여러개 인자가 저장된 것 확인
In [13]: p.authors.all()
Out[13]: <QuerySet [<Author: John>, <Author: Paul>, <Author: George>, <Author: Ringo>]>

```

## Retrieving objects
- 데이터베이스에서 객체를 검색하기 위해 **QuerySet**을 구성한다.
- QuerySet은 모델 **클래스**의 **Manager**을 통해 구성한다.
    - 각 모델은 `objects`라고 부르는 적어도 하나의 Manager를 가진다.
- QuerySet은 SQL문에서 `SELECT`문과 동급이다.
    - 하나 이상의 많은 *filter*를 가지고 있다.
    - filter는 SQL문의 `WHERE`, `LIMIT`와 같은 제한절과 동급이다.

```shell
# 클래스에서만 접근 가능
In [14]: Blog.objects
Out[14]: <django.db.models.manager.Manager at 0x10f33d2b0>

# 인스턴스에서는 불가
In [16]: b.objects
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-16-c375a53b1b40> in <module>()
----> 1 b.objects

/usr/local/var/pyenv/versions/documents/lib/python3.4/site-packages/django/db/models/manager.py in __get__(self, instance, cls)
    184     def __get__(self, instance, cls=None):
    185         if instance is not None:
--> 186             raise AttributeError("Manager isn't accessible via %s instances" % cls.__name__)
    187
    188         if cls._meta.abstract:

AttributeError: Manager isn't accessible via Blog instances
```

### Retrieving specific objects with filters

- `filter(**kwargs)` : 조건과 일치하는 객체를 포함하는 QuerySet을 반환
- `exclude(**kwargs)` : 조건이과 일치하지 않는 객체를 포함하는 QuerySet을 반환


```shell
# 둘은 같은 결과
>>> Entry.objects.filter(pub_date__year=2006)
>>> Entry.objects.all().filter(pub_date__year=2006)
```

#### QuerySets are lazy
QuerySet을 만드는 것이 데이터베이스 활동을 포함하지는 않는다.
QuerySet이 평가될 때 까지 아무일도 일어나지 않는다.

```shell
>>> q = Entry.objects.filter(headline__startswith="What")
>>> q = q.filter(pub_date__lte=datetime.date.today())
>>> q = q.exclude(body_text__icontains="food")
# 이때 작동
>>> print(q)
```
위의 예를 보면 데이터베이스에 3번 질의할 것 같지만, 사실은 마지막 라인 `print(q)`에서 딱 한번 질의한다.

일반적으로 QuerySet의 결과는 데이터베이스로부터 fetch되지 않는다. QuerySet을 "ask"할 때 까지 fecht하지 않는다.

### Retrieving a single object with get()
- 한개를 가져올 때 사용
- 0도 안되고, 2개도 안되고 무조건 1개만 가져옴


### Limiting QuerySets
- 네거티브는 지원하지 않는다.

### Field lookups
- double-underscore(__)로 접근

대표적인 필드 lookup을 살펴본다.
 - exact
 - iexact : 대소문자 상관없이 검색
 - contains : 대소문자 구분하는 포함
 - icontains
 - startswith : ~로 시작하는
 - istartwith : 대소문자 상관없이 ~로 시작하는
 - endswith : ~로 끝나는
 - iendswith


### Lookups that span relationships
- 장고는 SQL JOIN을 다루는 강력하고 직관적인 검색을 제공한다.
- 관련된 모델의 필드이름에 `__`를 사용해 작성한다.


```shell
In [24]: Post.objects.filter(blog__name='Beatles Blog')
Out[24]: <QuerySet [<Post: Fisrt Post>]>

In [25]: Post.objects.get(blog__name='Beatles Blog')
Out[25]: <Post: Fisrt Post>

In [26]: Post.objects.get(blog__name__contains='Beatles')
Out[26]: <Post: Fisrt Post>


In [28]: Post.objects.get(blog__name__contains='Beatles', id=1)
Out[28]: <Post: Fisrt Post>
```

`filter` 조건에 맞는 값이 중간자 모델과 다중관계에 없을 경우에도 장고는 모든 값에 `NULL`값이 있다고 취급한다. 즉, 객체가 있고 유효하기 때문에 에러는 발생하지 않는다.


```shell
# Post 인스턴스 생성
In [30]: p2 = Post.objects.create(blog=b, title='Second Post')

n [33]: p.authors.all()
Out[33]: <QuerySet [<Author: John>, <Author: Paul>, <Author: George>, <Author: Ringo>]>

# Post 인스턴스 p2에는 author가 없다.
In [34]: p2.authors.all()
Out[34]: <QuerySet []>

# MTM 은 까다로움, 제약조건
# MTM필드가 빈 경우를 검색

# 제대로된 결과
In [31]: Blog.objects.filter(post__authors__isnull=True)
Out[31]: <QuerySet [<Blog: Beatles Blog>]>

# name이 null인 경우가 없지만 결과가 나옴
# name과 author를 모두 검색하기 때문
In [32]: Blog.objects.filter(post__authors__name__isnull=True)
Out[32]: <QuerySet [<Blog: Beatles Blog>]>

# 그럴땐,
# author와 name 모두 둘다 확인
In [33]: Blog.objects.filter(post__authors__isnull=False, post__authors__name__i
    ...: snull=True)
Out[33]: <QuerySet []>

```

#### Spanning multi-valued relationships

- 장고는 단일 `filter()` 호출 내부의 모든 항목이 동시에 적용되어 모든 요구 사항과 일치하는 항목을 필터링한다.
- 연속적인 `filter()` 호출은 객체 세트를 추가로 제한한다.
- **주의** : 다중 값 관계는 이전 `filter()` 호출로 선택된 객체가 아닌 기본 모델에 링크 된 모든 객체에 적용된다.

````shell
# 연결관계에 따라 결과가 다르게 나온다
# filter()내의 , : like and 연산
>>> Blog.objects.filter(entry__headline__contains='Lennon', entry__pub_date__year=2008)

# 첫번째 filter() 결과
# + 두번째 filter() 결과 : like or 연산
>>> Blog.objects.filter(entry__headline__contains='Lennon').filter(entry__pub_date__year=2008)
````



### Filters can reference fields on the model
- 지금까지는 모델 필드의 값과 상수를 비교했다.
- 모델 필드의 값과 같은 모델의 다른 필드와 비교하려면 **F 표현식**을 사용한다. (같은 레벨에서는 `__`로 타고 들어가 검색이 불가능하기 때문에)
- `F()`의 인스턴스는 모델 필드의 레퍼런스 처럼 행동한다.

```shell
from django.db.models import F
p1 = Post.objects.get(title='First Post')

p1.comments_count = 10
p1.pinbacks_count = 5

p2.comments_count = 10
p2.pinbacks_count = 15
p1.save()
p2.save()

# Post.objects.filter(comments_count > pingbacks_count)를
# 검색하기 위해 F표현식을 사용
Post.objects.filter(commnets_count__gt=F('ping_backs_count')

# Post.objects.filter(comments_count > pingbacks_count)
Post.objects.filter(commnets_count__lt=F('ping_backs_count')
```

### Aggregate() expressions
- 쿼리문 안쪽에서 변수하나를 할당해 변수를 비교 값으로 사용할 수 있게 하는것




