## Serializer fields
### Core arguments
- `read_only`
- `write_only`
- `required` : Defaults to `True`
- `allow_null`
- `default`
- `source` : Defaults to the **name of the field**
- `validators`
- `error_messages`
- `label`
- `help_text`
- `initial`
- `style`

## Boolean fields
- BooleanField
- NullBooleanField

## String fields
- CharField
- EmailField
- RegexField
- SlugField : 패턴 `[a-zA-Z0-9 _-]+`에 대한 입력의 유효성을 검사하는 `RegexField`입니다.
- URLField
- UUIDField
- FilePathField
- IPAddressField

## Numeric fields
- IntegerField
- FloatField
- DecimalField

## Date and time fields
- DateTimeField
- DateField
- TimeField
- DurationField

## Choice selection fields
- ChoiceField
- MultipleChoiceField

## File upload fields
- FileField
- ImageField

## Composite fields
- ListField
- DictField
- JSONField

## Miscellaneous fields
- ReadOnlyField
- HiddenField : 사용자 입력에 따라 값을 사용하지 않고 대신 기본값 또는 호출 가능 값에서 값을 가져 오는 필드 클래스입니다.
- ModelField
- SerializerMethodField
