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
  - 두 항목(예 : `[(A, B), (A, B)...]`)의 **반복가능한 객체**로 구성됩니다.
    - 각 튜플의 첫 번째 요소는 모델에 설정한 실제 값입니다.
    - 두 번째 요소는 사람이 읽을 수 있는 이릅입니다.
    - `get_FOO_display()` : 사람이 읽을 수 있는 이름을 검색하는 메서드
  - 기본 폼 위젯은 텍스트 필드 대신에 선택박스가 됩니다.
  - 선택 항목 그룹의 이름을 사용할 수 있습니다.
    -  선택 상자의 "————" 라벨을 무시하려면, choices에 `(None, 'Your String For Display')` 튜플을 추가합니다.
- db_column (`Field.db_column`)
  - 필드에 적용할 데이터베이스 column 이름
  - 주어지지 않으면, 장고는 필드의 이름을 사용합니다.
- db_index (`Field.db_index`)
  - **True** 일 때, 필드를 위한 데이터베이스 인덱스를 생성합니다.
- db_tablespace (`Field.db_tablespace`)
  - 필드의 인덱스가 작성된 경우, 필드의 인덱스를 사용하기 위한 **database_tablespace**
- default (`Field.default`)
  - 필드의 기본값
  - 값 또는 호출 가능한 객체를 사용할 수 있습니다.
- editable(`Field.editable`)
  - 기본값은 **True** 입니다.
  - **False** 일 때, 필드는 admin이나 다른 모델 폼에 표시되지 않습니다.
- error_messages (`Field.error_message`)
  - 필드에서 발생하는 기본 메시지를 재정의(override)할 수 있습니다.
  - 에러 메시지 키는  `null`, `blank`, `invalid`, `invalid_choice`, `unique`, `unique_for_date`를 포함합니다.
- help_text (`Field.help_text`)
  - 폼 위젯에 표시되는 추가 "도움말"
  - 폼을 사용하지 않는 필드라도 문서화에 유용합니다.
- primary_key (`Field.primary_key`)
  - **True** 일 때, 필드는 모델의 기본 키 입니다.
  - 모델의 어떤 필드에도 `primary_key=True`를 명시하지 않으면, 장고는 자동으로 기본 키를 가지는 **AutoField**를 추가합니다.
  - 기본 키 필드는 읽기만 가능합니다. 만약 기존 객체의 기본 키 값을 변경후 저장하면, 기존 객체는 그대로 둔채 새로운 객체를 생성합니다.
- unique (`Field.unique`)
  - **True** 일 때, 필드는 테이블 전체에서 고유해야 합니다.
  - 중복 값을 unique 필드에 저장하려고 하면, `django.db.IntegrityError`가 발생합니다.
  - **주의** : `unique=True`일 때, 인덱스 생성을 의미하므로 **db_index**를 명시할 필요가 없습니다.
- unique_for_date (`Field.unique_for_date`)
  - **DateField**, **DateTimeField**로 설정한 필드는 date 필드의 값에 대해 고유해야 합니다.
  - **주의** : **DateTimeField**를 설정하면, 오직 날짜 부분만 고려합니다.
- unique_for_month (`Field_for_month`)
  - **unique_for_date**와 비슷하지만, 월을 기준으로 고유해야 합니다.
- unique_for_year (`Field_for_year`)
  - unique_for_month와 unique_for_date와 비슷합니다.
- verbose_name (`Field_verbose_name`)
  - 사람이 읽을 수있는 필드 이름입니다. 
  - 주어지지 않으면, 장고는 필드의 속성 이름을 사용하여 밑줄은 공백으로 변환하여 자동으로 생성합니다.
- validators (`Field_validators`)
  - 필드에 대해 실행하는 유효성 검사 목록입니다.
  - Registering and fetching lookups (검색 등록 및 가져오기)
    - 필드는 를 구현합니다.
    - API는 필드 클래스에 어느 것이 조회 가능한지, 필드에서 어떻게 조회해 가져오는지 사용자화 할 수 있습니다.