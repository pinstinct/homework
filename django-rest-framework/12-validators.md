## Validators

### Validation in REST framework
`ModelForm`을 사용하면 유효성 검사가 부분적으로 폼에서 수행되고 부분적으로 모델 인스턴스에서 수행됩니다. REST 프레임워크를 사용하면 유효성 검사는 전체적으로 `serializer` 클래스에서 수행됩니다.

UniqueValidator
: 이 유효성 검사기를 사용하여 모델 필드에 `unique=True` 제약 조건을 적용 할 수 있습니다.

UniqueTogetherValidator
: 이 유효성 검사기를 사용하여 모델 인스턴스에 `unique_together` 제약 조건을 적용 할 수 있습니다.

UniqueForDateValidator, UniqueForMonthValidator, UniqueForYearValidator
: 이 유효성 검사기는 model 인스턴스에 대해 `unique_for_date`, `unique_for_month` 및 `unique_for_year` 제약 조건을 적용하는 데 사용할 수 있습니다.

## Advanced field defaults
API 클라이언트가 제공해서는 안되지만 유효성 검사가 필요할 수 있습니다.

이러한 유형의 유효성 검사에 사용할 수있는 두 가지 패턴은 다음과 같습니다.
- `HiddenField` 사용. 이 필드는 `validated_data`에 있지만 serializer 출력 표현에서는 사용되지 않습니다.
- `read_only=True`와 함께 표준 필드를 사용하지만, `default=<value>` 인수도 포함합니다. 이 필드는 시리얼 라이저 출력 표현에 사용되지만 사용자가 직접 설정할 수는 없습니다.
