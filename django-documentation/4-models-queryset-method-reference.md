# [QuerySets: QuerySet method reference](https://docs.djangoproject.com/en/1.10/ref/models/querysets/)

## QuerySet API reference

**QuerySet** API의 세부 사항을 기술한다.

### When QuerySets are evaluated

**QuerySet**를 평가할 때까지 실제로 데이터베이스 활동이 발생하지 않습니다. 다음과 같은 방법으로 QuerySet을 평가할 수 있습니다.

- Iteration : **QuerySet**은 반복 가능하며 처음 반복 할 때 **QuerySet**을 실행합니다. 최소한 하나의 결과가 존재하는지 확인(`exists ()`)해 사용하는 것이 더 효율적입니다.
- Slicing : **QuerySet**은 Python의 배열 슬라이싱 구문을 사용하여 슬라이스 할 수 있습니다. 슬라이스 구문의 "**step**"매개 변수를 사용하면 데이터베이스 쿼리를 실행하고 목록을 반환합니다.
- Pickling/Caching : 결과가 데이터베이스에서 읽혀집니다.
- repr() : **QuerySet**은 `repr()`을 호출 할 때 평가됩니다.
- len() : **QuerySet**은 `len()`을 호출 할 때 평가됩니다. 목록의 길이를 반환합니다. 실제 객체가 아닌 집합의 레코드 수가 필요하다면, SQL의 `SELECT COUNT(*)` 를 사용하여 데이터베이스 수준에서 카운트를 처리하는 것이 훨씬 효율적입니다. Django는 `count()` 메소드를 제공합니다.
- list() : `list()`를 호출하여 QuerySet 을 강제 평가합니다.
- bool() : `bool()`, `or`, `and`, `if문` 같이 QuerySet의 boolean context를 검사하는 것은 쿼리를 실행하게 됩니다.

#### Pickling QuerySets

QuerySet을 **pickle**하면, pickling 전에 결과를 메모리에 강제로 로드합니다. pickling은 보통 캐싱하기 위한 선구자로 사용됩니다. 캐시 된 쿼리 세트가 다시 로드되면 결과가 이미 있고 사용할 준비가 되기를 원합니다. 즉, QuerySet을 unpickle 할 때는 (현재 데이터베이스에 있는 결과 보다) 그 순간 pickle한 결과를 포함합니다.

QuerySet객체를 저장할 수 없으니, 피클링해서 파일에 저장해두고 언피클링해서 사용한다. (원래 객체는 파일에 저장할 수 없다.)


### QuerySet API

`class QuerySet(model=None, query=None, using=None)`

일반적으로 QuerySet과 상호 작용할 때 필터를 연결하여 사용합니다. QuerySet 클래스에는 두 개의 공용 속성이 있습니다.

- ordered : **True**일 때, QuerySet이 정렬된 것입니다.
- db : 쿼리가 지금 실행될 경우 사용할 데이터베이스입니다.

#### Methods that return new QuerySets

- `filter(**kwargs)` : 지정된 검색 매개 변수와 **일치하는** 객체가 포함 된 새 QuerySet을 반환합니다. 여러 매개 변수는 기본 SQL 문에서 **AND**를 통해 조인됩니다. 더 복잡한 쿼리 (예 : OR 문을 사용하는 쿼리)를 실행해야하는 경우 Q 개체를 사용할 수 있습니다.
- `exclude(**kwargs)` : 지정된 조회 매개 변수와 **일치하지 않는** 객체가 포함 된 새 QuerySet을 반환합니다. 여러 매개 변수는 기본 SQL 문에서 **AND**를 통해 조인된 후, **NOT()**으로 묶입니다.
- `annotate(*args, **kwargs)` : 제공된 쿼리 표현식 목록으로 QuerySet의 각 개체에 주석을 첨부합니다.
- `order_by(*fields)` : 기본적으로 QuerySet에 의해 반환된 결과는 모델의 **Meta** 정렬 옵션에 의해 정렬된 튜플에 의해 정렬됩니다. **order_by** 메서드를 사용하여 **QuerySet** 단위로 오버라이드 할 수 있습니다.
- `reverse()` : `reverse()` 메서드를 사용하여 쿼리 세트의 요소가 반환되는 순서를 바꿉니다. `reverse()`를 다시 호출하면 순서가 정상 방향으로 복원됩니다.
- `distinct(*fields)` : SQL 쿼리에서 **SELECT DISTINCT**를 사용하는 새 QuerySet을 반환합니다. 이렇게하면 조회 결과에서 중복 행이 제거됩니다. 기본적으로 QuerySet은 중복 행을 제거하지 않습니다. 
- `values(*fields)` : 모델 인스턴스가 아닌 사전형 QuerySet을 반환합니다.
- `values_list(*fields, flat=False)` : `values()`와 유사합니다. 사전형을 반환하는 대신 튜플을 반환합니다. 단일 필드만 통과하고 싶으면, **flat** 매개 변수를 사용합니다. **True** 일 때, 반환 된 결과가 튜플이 아닌 단일 값임을 의미합니다.
- `dates(field, kind, order='ASC')` : QuerySet의 내용 내에서 사용 가능한 특정 날짜를 나타내는 **datetime.date** 객체의 목록으로 평가되는 QuerySet을 반환합니다. **field**는 모델의 **DateField** 이름이어야합니다. 
- `datetimes(field_name, kind, order='ASC', tzinfo=None)` : QuerySet의 내용 내에서 사용 가능한 특정 날짜를 나타내는 **datetime.datetime** 객체의 목록으로 평가되는 QuerySet을 반환합니다. **field_name**은 모델의 **DateTimeField** 이름이어야합니다. 
- `none()` : `none()`을 호출하면 객체를 반환하지 않는 쿼리 세트가 만들어지며, 결과에 액세스 할 때 쿼리가 실행되지 않습니다.
- `all()` : 현재의 QuerySet(또는 QuerySet 서브 클래스)의 복사본을 반환합니다.
- `select_related(*fields)` : 외래 키 관계를 "따르는" QuerySet를 리턴 해, 그 쿼리를 실행할 때에 추가의 관련 객체 데이터를 선택합니다.
- `prefetch_related(*lookups)` : 자동으로 검색할 QuerySet을 반환합니다.
- `extra(select=None, where=None, params=None, tables=None, order_by=None, select_params=None)` : 때로는 Django 쿼리 문법만으로는 복잡한 **WHERE** 절을 쉽게 표현할 수 없습니다. 이러한 경우 Django는 QuerySet에 의해 생성 된 SQL에 특정 절을 삽입하기위한 **extra () QuerySet** 제공합니다. 최후의 수단으로이 방법을 사용합니다.
  - select : select 인수를 사용하면 SELECT 절에 추가 필드를 넣을 수 있습니다.
  - where / tables : **where**를 사용해 명시적 SQL **WHERE** 절을 정의 할 수 있습니다. **tables** 사용하여 SQL **FROM** 절에 테이블을 수동으로 추가 할 수 있습니다.
  - order_by : extra()를 통해 포함 된 새로운 필드나 테이블 중 일부를 사용하여 결과 쿼리 세트를 정렬해야하는 경우 order_by 매개 변수를 사용합니다.
  - params : Python 데이터베이스 문자열 자리 표시자 ( '**%s**')를 사용하여 매개 변수를 나타낼 수 있습니다.
- `defer(*fields)` :  데이터베이스에서 검색하지 않도록 할 수 있습니다.
- `only(*fields)` : `only()` 메소드는 `defer()`의 반대입니다. 모델을 검색 할 때 연기해서는 안되는 필드를 호출합니다.
- `using(alias)` : 이 방법은 둘 이상의 데이터베이스를 사용하는 경우 QuerySet이 평가 될 데이터베이스를 제어하는 데 사용됩니다.
- `select_for_update(nowait=False)` : 트랜잭션이 끝날 때까지 행을 잠그고 **SELECT ... FOR UPDATE** SQL 문을 생성하는 쿼리 집합을 반환합니다.
- `raw(raw_query, params=None, translations=None)` : 원시 SQL 쿼리를 가져 와서 실행하고 **django.db.models.query.RawQuerySet** 인스턴스를 반환합니다.

#### Methods that do not return QuerySets

- `get(**kwargs)` : 주어진 검색 매개 변수와 일치하는 객체를 반환합니다.
- `create(**kwargs)` : 객체를 작성해 한 번에 저장하는 메서드입니다.
- `get_or_create(defaults=None, **kwargs)` : 주어진 **kwargs**로 객체를 찾는 편리한 방법입니다. 필요한 경우 생성합니다.
- `update_or_create(defaults=None, **kwargs)` : 주어진 **kwargs**로 객체를 업데이트하고 필요한 경우, 새 객체를 생성하는 편리한 메소드입니다. **defaults**는 오브젝트 갱신에 사용되는 `{필드: 값}` 쌍의 사전형입니다.
- `bulk_create(objs, batch_size=None)` : 객체 목록을 데이터베이스에 삽입합니다. **batch_size** 매개 변수는 단일 쿼리에서 생성되는 객체 수를 제어합니다. 몇 가지 주의사항이 있습니다. save () 메소드는 호출되지 않습니다. 또한 다대다 관계에서 작동하지 않습니다.
- `count()` : QuerySet와 일치하는 데이터베이스의 객체 수를 나타내는 정수를 반환합니다. count () 메서드는 예외를 발생시키지 않습니다,
- `in_bulk(id_list=None)` : 기본 키 값의  리스트를 가져와 기본키와 매핑한 인스턴스를 사전형으로 반환합니다. 목록이 제공되지 않으면 쿼리 세트의 모든 오브젝트가 리턴됩니다.
- `iterator()` : 쿼리를 수행하여 QuerySet을 평가하고 결과에 대한 반복자를 반환합니다.
- `latest(field_name=None)` : 날짜 필드로 제공된 field_name을 사용하여 테이블의 최신 객체를 날짜순으로 반환합니다.
- `earliest(field_name=None)` : `latest()`와 반대 방향으로 작동합니다.
- `first()` : 쿼리 세트와 일치하는 첫 번째 객체를 반환하거나 일치하는 객체가없는 경우 None을 반환합니다.
- `last()` : first ()와 유사하지만 queryset의 마지막 객체를 반환합니다.
- `aggregate(*args, **kwargs)` : QuerySet을 통해 계산 된 집계 값 (평균, 합계 등)의 사전을 반환합니다. 
- `exists()` : QuerySet에 결과가 있으면 True를 반환하고 그렇지 않으면 False를 반환합니다. 일반적인 QuerySet 쿼리와 거의 같은 쿼리를 실행합니다.
- `update(**kwargs)` : 지정된 필드에 대해 SQL 업데이트 쿼리를 수행하고 일치하는 행 수를 반환합니다. update()는 SQL 레벨에서 업데이트를 수행하므로 모델의 `save()` 메소드를 호출하지 않으므로 호출해야 합니다.
- `delete()` : QuerySet의 모든 행에 대해 SQL 삭제 쿼리를 수행하고 삭제 된 개체 수와 개체 유형당 삭제 수가있는 사전형을 반환합니다.
- `classmethod as_manager()` : QuerySet 메서드의 복사본이있는 Manager 인스턴스를 반환하는 클래스 메서드입니다.

#### Field lookups

필드 조회는 SQL **WHERE** 절의 핵심을 지정하는 방법입니다. QuerySet 메서드 filte (), exclude() 및 ge ()에 대한 키워드 인자로 지정됩니다.

- exact : 정확히 일치
- iexact : 대소 문자를 구분하지 않는 정확한 일치
- contains : 대소문자를 구분한 containment
- icontains : 대소 문자를 구분하지 않는 containment
- in : 주어진 목록에서
- gt : 보다 크다
- gte :  크거나 같다
- lt : 보다 작다
- lte : 작거나 같다
- startswith : 대소 문자를 구분하여 시작하는
- istartswith :  대소 문자를 구분하지 않고 시작하는
- endswith :  대소 문자를 구분하여 끝나는
- iendswith : 대소 문자를 구분하지 않고 끝나는
- range :  범위 테스트 (포함)
- date : datetime 필드의 경우 값을 날짜로 변환
- year : datetime, date 필드의 경우 정확히 일치하는 연도
- month : datetime, date 필드의 경우 정확히 일치하는 월
- day : datetime, date 필드의 경우 정확히 일치하는 날짜
- week_day : datetime, date 필드의 경우 '요일'과 일치
- hour : datetime, date 필드의 경우 정확히 일치하는 시간
- minute : datetime, date 필드의 경우 정확히 일치하는 분
- second : datetime, date 필드의 경우 정확히 일치하는 초
- isnull : IS NULL 및 IS NOT NULL의 SQL 쿼리에 해당하는 True 또는 False를 사용
- search : 버전 1.10 이후 사용되지 않음
- regex : 대소 문자를 구분하는 정규 표현식과 일치
- iregex : 대소 문자를 구분하지 않는 정규 표현식과 일치

#### Aggregation functions

Django는 **django.db.models** 모듈에서 다음과 같은 집계 함수를 제공합니다.

- expression : 모델의 필드 또는 쿼리 식을 참조하는 문자열입니다.
- output_field : 반환 값의 모델 필드를 나타내는 optional 인자
- **extra : 집계에 의해 생성 된 SQL에 대한 추가 컨텍스트를 제공 할 수있는 키워드 인자
- `class Avg(expression, output_field=FloatField(), **extra)` : 주어진 표현식의 평균값을 반환합니다.
- `class Count(expression, distinct=False, **extra)` : 제공된 표현식을 통해 관련된 객체 수를 반환합니다.
- `class Max(expression, output_field=None, **extra)` : 주어진 표현식의 최대 값을 리턴합니다.
- `class Min(expression, output_field=None, **extra)` : 주어진 표현식의 최소값을 리턴합니다.
- `class StdDev(expression, sample=False, **extra)` : 제공된 표현식에서 데이터의 표준 편차를 리턴합니다.
- `class Sum(expression, output_field=None, **extra)` : 주어진 표현식의 모든 값의 합을 계산합니다.
- `class Variance(expression, sample=False, **extra)` : 제공된 표현식에서 데이터의 분산을 반환합니다.



### Query-related tools

#### Q() objects

`class Q`

**Q()** 객체는 데이터베이스 관련 작업에 사용할 수있는 Python 객체를 SQL 표현식으로 캡슐화합니다. 일반적으로 Q() 객체를 사용하면 조건을 정의하고 다시 사용할 수 있습니다. 이렇게하면 `| (OR) 및 & (AND)` 연산자를 사용하여 복잡한 데이터베이스 조회를 구성 할 수 있습니다.

#### Prefetch() objects

`class Prefetch(lookup, queryset=None, to_attr=None)`

`Prefetch()` 객체는 `prefetch_related()`의 동작을 제어하는 데 사용될 수 있습니다.

#### prefetch_related_objects()

`prefetch_related_objects(model_instances, *related_lookups)`

주어진 모델 인스턴스의 반복 가능한 부분에 대해 미리보기를 프리페치합니다.
