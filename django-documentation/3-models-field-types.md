# [Models: Field types](https://docs.djangoproject.com/en/1.10/ref/models/fields/)

## Model field reference

이 문서는 Django가 제공하는 field options과 field types을 포함한 **Field**의 모든 API 참조를 포함합니다.  

기술적으로,이 모델들은 `django.db.models.fields`에 정의되어 있습니다. 하지만, 편의를 위해 `django.db.models`로 가져옵니다. 표준 규칙은 `from django.db import models`를 사용하고 `models.<Foo>Field`로 필드를 참조합니다. 

### Field options

다음 인자들은 모두 필드 타입으로 사용할 수 있습니다. 모두 선택 사항입니다.

- `null`
  - 기본값은 **False** 입니다.
  - **True** 일 때, Django는 빈 값을 **NULL**로 데이터베이스에 저장합니다. 
  - **null** 파라미터는 데이터베이스 저장소에만 영향을줍니다.
  - `CharField`와 `TextField` 같은 문자열 필드에 null 사용하지 마십시오.
    - Django 규칙은 **Null**이 아니라 빈 문자열을 사용하는 것입니다.
    - 폼에서 빈 값을 허용한다면, 문자열 그리고 비문자열 필드 모두 `blank=True`로 설정해야 합니다.
- `blank`
  - 기본값은 **False** 입니다.
  - **True** 일 때, 필드는 공백을 허용합니다.
  - **주의** : **null**과는 다릅니다.
    - **null**은 순전히 데이터 베이스와 관련이 있습니다.
    - **blank**는 유효성 검사와 관련이 있습니다.
- `choices`
  - 두 항목(예 : `[(A, B), (A, B)...]`)의 **반복가능한 객체**로 구성됩니다.
    - 각 튜플의 첫 번째 요소는 모델에 설정한 실제 값입니다.
    - 두 번째 요소는 사람이 읽을 수 있는 이릅입니다.
    - `get_FOO_display()` : 사람이 읽을 수 있는 이름을 검색하는 메서드
  - 기본 폼 위젯은 텍스트 필드 대신에 선택박스가 됩니다.
  - 선택 항목 그룹의 이름을 사용할 수 있습니다.
    -  선택 상자의 "————" 라벨을 무시하려면, choices에 `(None, 'Your String For Display')` 튜플을 추가합니다.
- `db_column`
  - 필드에 적용할 데이터베이스 column 이름
  - 주어지지 않으면, 장고는 필드의 이름을 사용합니다.
- `db_index`
  - **True** 일 때, 필드를 위한 데이터베이스 인덱스를 생성합니다.
- `db_tablespace`
  - 필드의 인덱스가 작성된 경우, 필드의 인덱스를 사용하기 위한 **database_tablespace**
- `default`
  - 필드의 기본값
  - 값 또는 호출 가능한 객체를 사용할 수 있습니다.
- `editable`
  - 기본값은 **True** 입니다.
  - **False** 일 때, 필드는 admin이나 다른 모델 폼에 표시되지 않습니다.
- `error_message`
  - 필드에서 발생하는 기본 메시지를 재정의(override)할 수 있습니다.
  - 에러 메시지 키는  `null`, `blank`, `invalid`, `invalid_choice`, `unique`, `unique_for_date`를 포함합니다.
- `help_text`
  - 폼 위젯에 표시되는 추가 "도움말"
  - 폼을 사용하지 않는 필드라도 문서화에 유용합니다.
- `primary_key`
  - **True** 일 때, 필드는 모델의 기본 키 입니다.
  - 모델의 어떤 필드에도 `primary_key=True`를 명시하지 않으면, 장고는 자동으로 기본 키를 가지는 **AutoField**를 추가합니다.
  - 기본 키 필드는 읽기만 가능합니다. 만약 기존 객체의 기본 키 값을 변경후 저장하면, 기존 객체는 그대로 둔채 새로운 객체를 생성합니다.
- `unique`
  - **True** 일 때, 필드는 테이블 전체에서 고유해야 합니다.
  - 중복 값을 unique 필드에 저장하려고 하면, `django.db.IntegrityError`가 발생합니다.
  - **주의** : `unique=True`일 때, 인덱스 생성을 의미하므로 **db_index**를 명시할 필요가 없습니다.
- `unique_for_date`
  - **DateField**, **DateTimeField**로 설정한 필드는 date 필드의 값에 대해 고유해야 합니다.
  - **주의** : **DateTimeField**를 설정하면, 오직 날짜 부분만 고려합니다.
- `unique_for_month` 
  - **unique_for_date**와 비슷하지만, 월을 기준으로 고유해야 합니다.
- `unique_for_year`
  - unique_for_month와 unique_for_date와 비슷합니다.
- `verbose_name`
  - 사람이 읽을 수있는 필드 이름입니다. 
  - 주어지지 않으면, 장고는 필드의 속성 이름을 사용하여 밑줄은 공백으로 변환하여 자동으로 생성합니다.
- `validators`
  - 필드에 대해 실행하는 유효성 검사 목록입니다.
  - Registering and fetching lookups (검색 등록 및 가져오기)
    - 필드는 를 구현합니다.
    - API는 필드 클래스에 어느 것이 조회 가능한지, 필드에서 어떻게 조회해 가져오는지 사용자화 할 수 있습니다.

### Field types

- AutoField `class AutoField(**options)`
  - 사용 가능한 ID에 따라 자동으로 증가하는 **IntegerField**
  - 별도로 지정하지 않으면, 기본 키 필드가 자동으로 모델에 추가합니다.
- IntegerField `class IntegerField(**options)`
  -  **-2147483648**에서 **2147483647**까지의 정수
  - 기본 폼 위젯은 **localize**가 **False** 일 때 **NumberInput**이고, 그렇지 않으면 **TextInput**입니다.
- BigAutoField `class BigAutoField(**options)`
  - **AutoField**와 매우 유사한 64 비트 정수
  - **1**에서 **9223372036854775807**까지의 숫자들을 보장합니다.
- BigIntegerField `class BigIntegerField(**options)`
  - **IntegerField**와 매우 흡사 한 64 비트 정수
  - **-9223372036854775808**에서 **9223372036854775807**까지의 숫자들을 보장합니다.
  - 기본 폼 위젯은 **TextInput** 입니다.
- PositiveIntegerField `class PositiveIntegerField(**options)`
  - **IntegerField**와 같지만 양수 또는 **0**이어야합니다. **0**에서 **2147483647**까지의 숫자들을 보장합니다.
- PositiveSmallIntegerField `class PositiveSmallIntegerField(**options)`
  - **PositiveIntegerField**와 같지만 특정 (데이터베이스 종속적인) 지점에서만 값을 허용합니다. **0**에서 **32767** 까지의 숫자들을 보장합니다.
- SmallIntegerField `class SmallIntegerField(**options)`
  - **IntegerField**와 같지만 특정 (데이터베이스 종속적인) 지점에서만 값을 허용합니다. **-32768**에서 **32767**  까지의 숫자들을 보장합니다.
- BinaryField `class BinaryField(**options)`
  - 원시 이진 데이터를 저장하는 필드입니다. 
  - 바이트 할당만 지원합니다.
  - 99% 경우에 잘못된 디자인이므로, 사용하지 않는 것이 좋습니다.
- BooleanField `class BooleanField(**options)`
  - True/False 필드입니다.
  - 기본 폼 위젯은 **CheckboxInput**입니다. 
  - null 값을 허용한다면, **NullBooleanField**를 사용하십시오. 
- NullBooleanField `class NullBooleanField(**options)`
  - **BooleanField**와 같으나 옵션 중 하나로 **NULL**을 허용합니다. 기본 폼 위젯은 **NullBooleanSelect**입니다.
- CharField `class CharField(max_length=None, **options)`
  - 작은 크기부터 큰 크기의 문자열을 나태는 문자열 필드입니다.
    - 많은 약의 텍스트는 **TextField**를 사용하십시오. 
  - 이 필드의 기본 폼 위젯은 **TextInput**입니다. 
  - 필수 : CharField.max_length
    - 필드의 최대 길이 (문자 수)입니다.
- TextField `class TextField(**options)`
  - 큰 텍스트 필드
  - 기본 폼 위젯은 **Textarea**입니다.
- CommaSeparatedIntegerField `class CommaSeparatedIntegerField(max_length=None, **options)`
  - 1.9버전 부터 사용하지 않습니다.
  - 쉼표로 구분 된 정수 필드
- DateField `class DateField(auto_now=False, auto_now_add=False, **options)`
  - Python에서 datetime.date 인스턴스로 표현하는 날짜(date)입니다. 
  - 옵션 : DateField.auto_now
    - 객체가 저장 될 때마다 필드를 지금(now)으로 자동 설정합니다. 
    - "마지막으로 수정한" 타임 스탬프에 유용합니다. 
    - `Model.save()`를 호출 할 때, 자동으로 업데이트됩니다. 
  - 옵션 : DateField.auto_now_add
    - 객체가 처음 생성 될 때 자동으로 필드를 지금(now)으로 설정합니다. 
    - 타임 스탬프 생성에 유용합니다. 
    - 객체를 만들 때 값을 설정하더라도 무시됩니다. 이 필드를 수정하게 하려면 `auto_now_add = True` 대신 다음을 설정하십시오. 
      - DateField : default = **date.today** - from **datetime.date.today()** 
      - DateTimeField : default = **timezone.now** - from **django.utils.timezone.now()**
  - 기본 폼 위젯은 **TextInput**입니다.
  - `auto_now_add`, `auto_now`, `default` 옵션은 상호 배타적입니다. 이러한 옵션들을 조합하면 오류가 발생합니다.
  - `auto_now` 또는 `auto_now_add`를 **True**로 설정하면, 해당 필드는 `editable = False` 및 `blank = True`로 설정됩니다.
- DateTimeField `class DateTimeField(auto_now=False, auto_now_add=False, **options)`
  - Python에서 **datetime.datetime** 인스턴스로 표현하는 날짜(date)와 시간(time)
  - **DateField**와 동일한 추가 인자를 사용합니다. 
  - 기본 폼 위젯은 단일 **TextInput**입니다. 
- TimeField `class TimeField(auto_now=False, auto_now_add=False, **options)`
  - Python에서 **datetime.time** 인스턴스로 표현하는 시간(time)입니다. 
  - **DateField**와 동일한 자동 채우기 옵션이 있습니다.
  - 기본 폼 위젯은 **TextInput**입니다. 
- DecimalField `class DecimalField(max_digits=None, decimal_places=None, **options)`
  - Python에서 **Decimal** 인스턴스로 표현하는 고정 소수점 숫자
  - 필수 : DecimalField.max_digits
    - 숫자에 허용되는 최대 자릿수입니다. 
    - **주의** : 이 수는 **decimal_places**보다 크거나 같아야합니다.
  - 필수 : DecimalField.decimal_places
    - 숫자와 함께 저장할 소수점 자릿수입니다.
  - 기본 폼 위젯은 localize가 False 일 때 **NumberInput**이고 그렇지 않으면 **TextInput**입니다.
- FloatField `class FloatField(**options)`
  - Python에서 **float** 인스턴스로 표현한 부동 소수점 숫자
  - 기본 폼 위젯은 localize가 False 일 때 **NumberInput**이고 그렇지 않으면 **TextInput**입니다.
  - FloatField vs DecimalField 
    - 둘 다 실수를 나타내지만 그 수를 다르게 나타냅니다. 
    - FloatField는 내부적으로 Python의 float 유형을 사용하고, DecimalField는 Python의 Decimal 유형을 사용합니다.
- DurationField `class DurationField(**options)`
  - Python에서 **Timedelta**가 모델링한 기간을 저장하는 필드입니다. 
-  EmailField `class EmailField(max_length=254, **options)`
  - 값이 유효한 전자 메일 주소인지 확인하는 **CharField**입니다. **EmailValidator**를 사용하여 입력의 유효성을 검사합니다.
- FileField `class FileField(upload_to=None, max_length=100, **options)`
  - 파일 업로드 필드입니다.
  - **주의** : **primary_key**와 **unique** 인자는 지원되지 않으며, 사용할 경우 **TypeError**를 발생합니다.
  - 필수 : FileField.upload_to
    - 업로드 디렉토리와 파일이름을 설정하는 방법을 제공하며, 두 가지 방법으로 설정할 수 있습니다.
    - 두 가지 방법 모두 값은 `Storage.save()` 메서드에 전달됩니다.
    - 문자열 값을 지정하려면,  `strftime()` 포맷을 사용합니다. (예 : %Y/%m/%d)
  - 필수 : FileField.storage
    - 파일의 저장 및 검색을 처리하는 저장 객체입니다. 
    - 기본 폼 위젯은 **ClearableFileInput**입니다. 
    - 모델에 **FileField** 또는 **ImageField**를 사용하면 몇 단계를 수행합니다.
      - 설정 파일에 Django가 업로드 된 파일을 저장할 디렉토리의 전체 경로로 **MEDIA_ROOT**를 정의해야합니다. 
      - 모델에 **FileField** 또는 **ImageField**를 추가하고, 업로드 된 파일에 사용할 **MEDIA_ROOT**의 하위 디렉토리를 **upload_to** 옵션을 정의하여 지정합니다.
      - 데이터베이스에 저장되는 것은 모두 파일에 대한 경로(MEDIA_ROOT에 상대경로)입니다. Django가 제공하는 편의 url 속성을 사용하고 싶을 것입니다.
    - **주의** : 업로드 된 파일을 다룰 때마다 업로드 할 파일과 파일의 유형에주의를 기울여 보안상의 위험을 피하십시오. 업로드 된 모든 파일의 유효성을 검사하여 파일이 자신이 생각하는 것임을 확신합니다.
    - **FileField** 인스턴스는 기본 최대 길이가 100자인 **varchar** 열로 데이터베이스에 만들어집니다. 다른 필드와 마찬가지로 **max_length** 인자를 사용하여 최대 길이를 변경할 수 있습니다.
  - FileField and FieldFile `class FieldFile`
    - 모델에서 **FileField**에 접근할 때, 기본 파일 접근을 위해 FieldFile 인스턴스에 프록시가 주어집니다.
    - `read()`, `write ()`와 같이 **File**에서 상속 된 API 외에도 **FieldFile**에는 기본 파일과 상호 작용하는 데 사용할 수있는 몇 가지 메서드가 포함되어 있습니다.
    - **주의** : `save()`와 `delete()` 메서드는 기본적으로 FieldFile에 연관된 모델 객체를 데이터베이스에 저장합니다.
    - `FieldFile.name` : 상대 경로를 포함한 파일 이름
    - `FieldFile.size` : `Storage.size()` 메서드의 결과
    - `FieldFile.url` : 파일의 상대 URL에 접근하는 읽기 전용 속성
    - `FieldFile.open(mode='rb')` : 지정된 **모드**에서 인스턴스와 관련된 파일을 엽니다.
    - `FieldFile.close()` : 인스턴스와 관련된 파일을 닫습니다.
    - `FieldFile.save(name, content, save=True)` : 파일 이름과 파일 내용을 가져 와서 저장소 클래스 필드에 전달한 다음 저장된 파일을 모델 필드와 연결합니다. 
    - `FieldFile.delete(save=True)` : 인스턴스와 관련된 파일을 삭제하고 필드의 속성을 지웁니다.
- FilePathField `class FilePathField(path=None, match=None, recursive=False, max_length=100, **options)`
  - 파일 시스템의 특정 디렉토리의 파일이름으로 선택이 제한된 CharField 입니다.
  - `FilePathField.path` : 필수, **FilePathField**부터 디렉토리까지 파일시스템 절대 경로
  - `FilePathField.match` : 옵션, 파일 이름을 필터링하는 데 사용할 정규 표현식입니다. 정규 표현식은 전체 경로가 아닌 기본 파일 이름에 적용됩니다.
  - `FilePathField.recursive` : 옵션, 기본값은 False입니다. **path**의 모든 하위디렉토리가 포함되느지 여부를 지정
  - `FilePathField.allow_files` : 옵션, 기본값은 True입니다.  지정된 위치의 파일을 포함할지 여부를 지정
  - `FilePathField.allow_folders` : 옵션, 기본값은 False입니다. 지정된 위치의 폴더를 포함할지 여부를 지정
  - FilePathField 인스턴스는 기본 최대 길이가 100자인 **varchar** 열로 데이터베이스에 만들어집니다. 다른 필드와 마찬가지로 **max_length** 인자를 사용하여 최대 길이를 변경할 수 있습니다.
- ImageField `class ImageField(upload_to=None, height_field=None, width_field=None, max_length=100, **options)`
  - **FileField**의 모든 특성과 메서드를 상속하지만, 업로드 된 개체가 유효한 이미지인지 확인합니다.
  - **ImageField**에는 **height**, **width** 속성이 있습니다.
  - `ImageField.height_field` : 옵션, 모델 인스턴스가 저장 될 때마다 자동으로 채워지는 이미지 높이
  - `ImageField.width_field` : 옵션, 모델 인스턴스가 저장 될 때마다 자동으로 채워지는 이미지 너비
  - 기본 폼 위젯은 **ClearableFileInput**입니다. 
  - ImageField 인스턴스는 기본 최대 길이가 100자인 **varchar** 열로 데이터베이스에 만들어집니다. 다른 필드와 마찬가지로 **max_length** 인수를 사용하여 최대 길이를 변경할 수 있습니다. 
- GenericIPAddressField `class GenericIPAddressField(protocol='both', unpack_ipv4=False, **options)`
  - 문자열 형식의 IPv4 또는 IPv6 주소 (예 : 192.0.2.30 또는 2a02 : 42fe :: 4) 
  - 기본 폼 위젯은 **TextInput**입니다.
  - `GenericIPAddressField.protocol` : 지정된 프로토콜에 대한 입력을 제한합니다. 허용되는 값은 '**both**'(기본값), '**IPv4**'또는 '**IPv6**'입니다. 일치는 대소 문자를 구분하지 않습니다.
  - `GenericIPAddressField.unpack_ipv4` : IPv4 매핑 된 주소의 압축을 풉니 다. 기본값은 사용하지 않습니다. `protocol = 'both'`로 설정된 경우에만 사용할 수 있습니다.
- SlugField `class SlugField(max_length=50, **options)`
  - 슬러그는 글자, 숫자, 밑줄 또는 하이픈만 포함하는 짧은 레이블입니다. 
  - 일반적으로 URL에 사용합니다.
  -  **max_length** 기본 값은 50 입니다.
  - `SlugField.allow_unicode` : **True** 일 때, 필드에 ASCII 문자 이외의 유니 코드 문자도 사용할 수 있습니다.
- URLField `class URLField(max_length=200, **options)`
  - URL을 위한 **CharField**입니다.
  - 기본 폼 위젯은 **TextInput**입니다.
- UUIDField `class UUIDField(**options)`
  - 보편적으로 유일한 식별자를 저장하기위한 필드
  - Python의 UUID 클래스를 사용합니다. 
  - UUID는 primary_key를 위한 **AutoField** 대신 사용할 수있는 좋은 방법입니다.



### Relationship fields

관계를 표현하는 필드 세트를 정의합니다.

#### ForeignKey 

`class ForeignKey(othermodel, on_delete, **options)`

-  다대일 관계입니다. 관련된 모델의 클래스를 인자로 필요합니다.
- 재귀 관계(자체와 다대일 관계가있는 객체)를 만들려면 `models.ForeignKey( 'self', on_delete = models.CASCADE)`를 사용합니다.
- 아직 정의되지 않은 모델에서 관계를 작성해야하는 경우, 모델 객체가 아닌 모델 이름을 사용할 수 있습니다.
- 다른 응용 프로그램(app)에 정의 된 모델을 참조하려면 전체 응용 프로그램 레이블로 모델을 명시적으로 지정할 수 있습니다.

##### Database Representation

Django는 **"_id"**를 필드 이름에 추가하여 데이터베이스 열 이름을 만듭니다. 

##### Arguments

- `ForeignKey.on_delete` : ForeignKey가 참조하는 객체가 삭제되면, Django는 on_delete 지정한 SQL 제약 조건의 동작을 에뮬레이션합니다. 
  - CASCADE : 계단식 삭제. Django는 ON DELETE CASCADE SQL 제약 조건의 동작을 에뮬레이션하고 ForeignKey가 포함된 객체도 삭제합니다.
  - PROTECT : **django.db.IntegrityError**의 하위 클래스인 **ProtectedError**를 발생시켜 참조된 객체의 삭제를 방지합니다.
  - SET_NULL : ForeignKey null을 설정합니다. (`null = True` 경우에만 가능)
  - SET_DEFAULT : ForeignKey의 기본값을 설정합니다.
  - SET() : ForeignKey를 **SET()**에 전달 된 값으로 설정하거나, 호출 가능 객체가 호출 한 결과를 설정합니다.
  - DO_NOTHING : 아무런 동작도 하지 않습니다.
- `ForeignKey.limit_choices_to` : ModelForm 또는 admin을 사용하여 필드를 렌더링 할 때, 필드에 사용할 수있는 선택 항목에 대한 제한을 설정합니다(기본적으로 쿼리 세트의 모든 객체를 선택할 수 있음). 
- `ForeignKey.related_name` : 관련 객체에서 이 객체에 대한 관계에 사용할 이름입니다. 또한 **related_query_name** (대상 모델의 역 필터 이름에 사용할 이름)의 기본값입니다. Django가 역방향 관계를 생성하지 않기를 원한다면, **related_name**을 '**+**'로 설정합니다.
- `ForeignKey.related_query_name` : target 모델의 역방향 필터에 사용할 이름입니다. 설정하지 않을 경우, **default_related_name**(`<ModelName>_set`) 또는 **related_name** 값이 기본 값입니다.
- `ForeignKey.to_field` : 관계가 있는 관련 객체의 필드입니다. 기본적으로 관련 객체의 기본 키를 사용합니다. 다른 필드를 참조할 경우, 해당 필드는 `unique=True`설정을 해야 합니다.
- `ForeignKey.db_constraint` : 외래 키에 대해 데이터베이스에 제약 조건을 만들지 여부를 제어합니다. 기본값은 **True**입니다.
- `ForeignKey.swappable` : ForeignKey가 스왑 가능 모델을 가리키는 경우, 마이그레이션 프레임 워크의 반응을 제어합니다. 

#### ManyToManyField

`class ManyToManyField(othermodel, **options)`

- 다대다 관계입니다. 관련된 모델의 클래스를 인자로 필요합니다.
- 재귀 및 지연 관계를 포함하여 **ForeignKey**와 똑같이 작동합니다.

##### Database Representation

장고는 다대다 관계를 표현하기 위해 중개자 조인 테이블을 생성합니다. 

##### Arguments

- `ManyToManyField.related_name` : ForeignKey.related_name과 동일합니다.
- `ManyToManyField.related_query_name` : ForeignKey.related_query_name과 동일합니다.
- `ManyToManyField.limit_choices_to` : ForeignKey.limit_choices_to와 동일합니다. 
- `ManyToManyField.symmetrical` : 재귀 ManyToManyFields의 정의에만 사용합니다. ManyToManyField는 대칭이라고 가정합니다. 즉, 내가 당신의 친구라면, 당신도 내 친구입니다. 재귀 다대다 관계에서 대칭을 원하지 않으면 `symmetrical = False` 설정을 합니다.
- `ManyToManyField.through` : Django는 다대다 관계를 관리하기위한 테이블을 자동으로 생성합니다. 그러나 중개자 테이블을 수동으로 지정하려면 **through** 옵션을 사용하여 사용할 중간 테이블을 나타내는 모델을 지정할 수 있습니다. 이 옵션의 가장 일반적인 용도는 **추가 데이터를 다 대다 관계와 연관**시키려는 경우입니다.
- `ManyToManyField.through_fields` : 사용자 중간자 모델이 지정된 경우에만 사용합니다. 다대다 관계에 참여하는 모델 중 하나(또는 두 모델 모두)의 중간자 모델에 둘 이상의 외래 키가있는 경우에는 **through_fields**를 지정해야합니다. through_fields는 2-tuple **( 'field1', 'field2')**을 허용합니다. 재귀 다대다 관계일 때는 `symmetrical=False `로 정의되어 있어야 한다.
- `ManyToManyField.db_table` : 다대다 데이터를 저장하기 위해 작성할 테이블의 이름
- `ManyToManyField.db_constraint` : 중개 테이블의 외래 키에 대해 데이터베이스에 제약 조건을 만들어야하는지 여부를 제어합니다. 기본값은 True입니다.
- `ManyToManyField.swappable` :   ManyToManyField가 스왑 가능 모델을 가리키는 경우, 마이그레이션 프레임 워크의 반응을 제어합니다. 

#### OneToOneField

`class OneToOneField(othermodel, on_delete, parent_link=False, **options)`

- 일대일 관계입니다. 
- `unique=True`로 설정된 **ForeignKey**와 비슷합니다. 하지만 "역 방향" 관계는 하나의 객체를 직접 반환합니다.
- 재귀 및 지연 관계를 포함하여 **ForeignKey**와 똑같이 작동합니다.
- 어떤 식으로든 다른 모델을 "확장"하려는 모델의 기본키로 가장 유용합니다.

OneToOneField는 ForeignKey에서 허용하는 모든 인자와 하나의 추가 인자가 있습니다.

- `OneToOneField.parent_link`



## Field attribute reference

모든 **Field** 인스턴스에는 동작을 관할 수있는 여러 속성이 있습니다. 필드의 기능에 따라 코드를 작성할 때, **isinstance** 체크 대신 속성을 이용하세요.

### Attributes for fields

- `Field.auto_created` : 필드가 자동으로 만들어 졌는지 여부를 나타내는 Boolean 플래그

- `Field.concrete` : 필드에 연결된 데이터베이스 열이 있는지 여부를 나타내는 Boolean 플래그

- `Field.hidden` : 필드가 숨겨진 필드가 아닌 다른 기능을 백업하는 데 사용되는지 여부를 나타내는 Boolean 플래그

- `Field.is_relation` : 필드가 하나 이상의 다른 모델(예 : ForeignKey, ManyToManyField, OneToOneField)에 대한 참조가 포함되어 있는지 나타내는 Boolean 플래그입니다.

  ​

### Attributes for fields with relations

카디널리티 및 관계의 기타 세부 사항을 쿼리하는 데 사용합니다. 이 속성은 모든 필드에 있습니다. 그러나 `Field.is_relation = True`인 경우, Boolean 값만 있습니다.

- `Field.many_to_many` : 필드가 다대다 관계를 갖는 경우 **True**인 부울 플래그
- `Field.many_to_one` : 필드에 다대일 관계가있는 경우 Boolean 플래그 (예 : ForeignKey)
- `Field.one_to_many` : 필드에 일대다 관계가있는 경우 Boolean 플래그 (예 : GenericRelation 또는 ForeignKey의 역) 
- `Field.one_to_one` : 필드가 OneToOneField와 같이 일대일 관계를 갖는 경우  Boolean 플래그  
- `Field.related_model` : 필드가 관련된 모델을 가리킵니다. 