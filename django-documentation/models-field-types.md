# [Models: Field types](https://docs.djangoproject.com/en/1.10/ref/models/fields/)

## Model field reference

이 문서는 Django가 제공하는 field options과 field types을 포함한 **Field**의 모든 API 참조를 포함합니다.  

기술적으로,이 모델들은 `django.db.models.fields`에 정의되어 있습니다. 하지만, 편의를 위해 `django.db.models`로 가져옵니다. 표준 규칙은 `from django.db import models`를 사용하고 `models.<Foo>Field`로 필드를 참조합니다. 

### Field options

다음 인자들은 모두 필드 타입으로 사용할 수 있습니다. 모두 선택 사항입니다.

- null (`Field. null`)
  - 기본값은 **False** 입니다.
  - **True** 일 때, Django는 빈 값을 **NULL**로 데이터베이스에 저장합니다. 
  - **null** 파라미터는 데이터베이스 저장소에만 영향을줍니다.
  - `CharField`와 `TextField` 같은 문자열 필드에 null 사용하지 마십시오.
    - Django 규칙은 **Null**이 아니라 빈 문자열을 사용하는 것입니다.
    - 폼에서 빈 값을 허용한다면, 문자열 그리고 비문자열 필드 모두 `blank=True`로 설정해야 합니다.
- blank (`Field.blank`)
  - 기본값은 **False** 입니다.
  - **True** 일 때, 필드는 공백을 허용합니다.
  - **주의** : **null**과는 다릅니다.
    - **null**은 순전히 데이터 베이스와 관련이 있습니다.
    - **blank**는 유효성 검사와 관련이 있습니다.
- choices (`Field.choices`)


choices
db_column
db_index
db_tablespace
default
editable
error_messages
help_text
primary_key
unique
unique_for_date
unique_for_month
unique_for_year
verbose_name
validators
​	Registering and fetching lookups