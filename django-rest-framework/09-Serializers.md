## Serizlizers
Serializers는 querysets 및 모델 인스턴스와 같은 복잡한 데이터를 `JSON`, `XML` 또는 `native Python` 데이터 유형으로 변환 할 수 있도록합니다.

### Serializing objects
native Python 데이터 타입을 `Json` 데이터 타입으로 변환

```python
json = JSONRenderer().render(serializer.data)
json
```

### Deserializing objects
Json 데이터 타입을 `native Python` 데이터 타입으로 변환

```python
data = JSONParser().parse(stream)
```

### Saving instances
유효성이 검사 된 데이터를 기반으로 완전한 객체 인스턴스를 반환하려면 `create()` 및 `update()` 메소드 중 하나 또는 둘 모두를 구현해야합니다.


### Validation
데이터를 deserializing할 때, 유효성이 검사 된 데이터에 액세스하기 전에 항상 `is_valid()`를 호출하거나 객체 인스턴스를 저장해야합니다.

### Partial updates
기본적으로 serializer는 모든 필수 필드에 값을 전달해야하며 그렇지 않으면 유효성 검사 오류가 발생합니다. 부분 업데이트를 허용하기 위해 partial 인자를 사용할 수 있습니다.


### Dealing with nested objects
Serializer 클래스 자체는 `Field` 유형이며, 중첩되어있는 관계를 나타내는 데 사용할 수 있습니다.

### Dealing with multiple objects
단일 객체 인스턴스 대신 쿼리 세트 또는 객체 목록을 직렬화하려면 serializer를 인스턴스화 할 때 `many = True` 플래그를 전달해야합니다.

## ModelSerializer
종종 Django 모델 정의와 밀접하게 매핑되는 serializer 클래스가 필요할 것이다.

ModelSerializer 클래스는 모델 필드에 해당하는 필드가있는 Serializer 클래스를 자동으로 만들 수있는 바로 가기를 제공합니다.

ModelSerializer 클래스는 다음을 제외하고는 일반 Serializer 클래스와 동일합니다.

- 모델을 기반으로 일련의 필드가 자동으로 생성됩니다.
- unique_together validator와 같은 serializer에 대한 validator를 자동으로 생성합니다.
- `.create()` 및 `update()`의 간단한 기본 구현을 포함합니다.

기본적으로 클래스의 모든 모델 필드는 해당 serializer 필드에 매핑됩니다.

모델의 외래 키와 같은 관계는 `PrimaryKeyRelatedField`에 매핑됩니다. serializer relations 문서에 명시된대로 명시 적으로 포함되지 않으면 **역방향 관계가 기본적으로 포함되지 않습니다.**

### Specifying which fields to include

```python
class AccountSerializer(serializers.ModelSerializer):
    groups = serializers.PrimaryKeyRelatedField(many=True)

    class Meta:
        model = Account
        fields = ('id', 'account_name', 'users', 'created')
        # fields = '__all__'
        # exclude = ('users',)
```

### Specifying nested serialization
기본 `ModelSerializer는` 관계에 기본 키를 사용하지만, `depth` 옵션을 사용하여 중첩 된 표현을 쉽게 생성 할 수도 있습니다.

### Specifying read only fields
각 필드를 `read_only=True` 특성으로 명시 적으로 추가하는 대신, 바로 가기 메타 옵션 인 `read_only_fields`를 사용할 수 있습니다.

```python
class AccountSerializer(serializers.ModelSerializer):
    class Meta:
        model = Account
        fields = ('id', 'account_name', 'users', 'created')
        read_only_fields = ('account_name',)
```
`editable=False` 집합 및 `AutoField` 필드가있는 모델 필드는 기본적으로 읽기 전용으로 설정되며 read_only_fields 옵션에 추가 할 필요가 없습니다.

### Relational fields
ModelSerializer의 기본 표현은 관련 인스턴스의 기본 키(primary key)를 사용하는 것입니다.

## HyperlinkedModelSerializer
`HyperlinkedModelSerializer` 클래스는 기본 키(primary key)가 아닌 관계를 나타내기 위해 하이퍼링크를 사용한다는 점을 제외하고는 `ModelSerializer` 클래스와 유사합니다.

### How hyperlinked views are determined
기본적으로 하이퍼링크는 `'{model_name} -detail'` 스타일과 view 이름과 일치하기를 기대한다. 그리고 `pk` 키워드 인스턴스로 검색한다.

## ListSerializer
`ListSerializer` 클래스는 여러 개체를 한 번에 serialize하고 유효성을 검사하는 동작을 제공합니다. 일반적으로 `ListSerializer를` 직접 사용할 필요는 없지만 대신 serializer를 인스턴스화 할 때 `many=True`를 전달해야합니다.

## BaseSerializer
serialization 및 deserialization 스타일을 쉽게 지원하는 데 사용할 수있는 `BaseSerializer` 클래스입니다.


## Advanced serializer usage

### Overriding serialization and deserialization behavior
`.to_representation(self, obj)`
: 직렬화가 필요한 객체 인스턴스를 가져 와서 원시 표현을 반환해야합니다. 일반적으로 이것은 내장 파이썬 데이터 유형의 구조를 반환하는 것을 의미합니다. 처리 할 수있는 정확한 유형은 API에 대해 구성한 렌더링 클래스에 따라 다릅니다.

`.to_internal_value(self, data)`
: 검증되지 않은 들어오는 데이터를 입력 받아 serializer.validated_data로 사용할 수있는 유효성이 검사 된 데이터를 반환해야합니다. serializer 클래스에서 `save()`가 호출되면 반환 값도 `create()` 또는 `update()` 메서드에 전달됩니다.
