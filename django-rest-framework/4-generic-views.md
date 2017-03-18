## Generic views
**클래스 기반 뷰**의 주요 이점 중 하나는 재사용 가능한 동작을 구성하는 것입니다. REST 프레임워크는 일반적으로 사용되는 패턴을 제공하는 pre-built views를 제공합니다.

generic views가 API의 요구 사항에 맞지 않으면 `mixins`을 재사용하여 고유한 generic views를 구성할 수 있다.


## API Reference
### GenericAPIView
REST 프레임워크의 `APIView` 클래스를 확장한 클래스이다.

#### Attributes
Basic settings
: control the basic view behavior

- `queryset` : queryset
- `serializer_class` : 직렬화 클래스
- `lookup_field` : 모델 인스턴스 검색을 위해 사용할 모델 필드 (기본 값 `pk`)
- `lookup_url_kwarg` - URL 키워드 인자 (기본 값 `lookup_field`와 동일)

Pagination
: control pagination

- `pagination_class`

## Mixins
mixin 클래스는 행동(action)을 제공합니다.

- ListModelMixin
- CreateModelMixin
- RetrieveModelMixin
- UpdateModelMixin
- DestroyModelMixin


## Concrete View Classes
구체적인 generic views입니다. 커스터마이징 된 동작이 필요하지 않으면 사용할 수준입니다.

- CreateAPIView
  - create-only
  - `post` method handler
  - Extends: `GenericAPIView`, `CreateModelMixin`
- ListAPIView
  - read-only
  - `get` method handler
  - Extends: `GenericAPIView`, `ListModelMixin`
- RetrieveAPIView
  - read-only
  - `get` method handler
  - Extends: `GenericAPIView`, `RetrieveModelMixin`
- DestroyAPIView
  - delete-only
  - `delete` method handler
  - Extends: `GenericAPIView`, `DestroyModelMixin`
- UpdateAPIView
  - update-only
  - `put` and `patch` method handlers
  - Extends: `GenericAPIView`, `UpdateModelMixin`
- ListCreateAPIView
  - read-write
  - `get` and `post` method handlers
  - Extends: `GenericAPIView`, `ListModelMixin`, `CreateModelMixin`
- RetrieveUpdateAPIView
  - read or update
  - `get`, `put` and `patch` method handlers
  - Extends: `GenericAPIView`, `RetrieveModelMixin`, `UpdateModelMixin`
- RetrieveDestroyAPIView
  - `read` or `delete`
  - `get` and `delete` method handlers
  - Extends: `GenericAPIView`, `RetrieveModelMixin`, `DestroyModelMixin`
- RetrieveUpdateDestroyAPIView
  - read-write-delete
  - `get`, `put`, `patch` and `delete` method handlers
  - Extends: `GenericAPIView`, `RetrieveModelMixin`, `UpdateModelMixin`, `DestroyModelMixin`

## Customizing the generic views

### Creating custom mixins
사용자 지정 동작이있는 경우 믹스 인을 사용하는 것이 좋습니다.
```python
class MultipleFieldLookupMixin(object):
    """
    Apply this mixin to any view or viewset to get multiple field filtering
    based on a `lookup_fields` attribute, instead of the default single field filtering.
    """
    def get_object(self):
        queryset = self.get_queryset()             # Get the base queryset
        queryset = self.filter_queryset(queryset)  # Apply any filter backends
        filter = {}
        for field in self.lookup_fields:
            if self.kwargs[field]: # Ignore empty fields.
                filter[field] = self.kwargs[field]
        return get_object_or_404(queryset, **filter)  # Lookup the object
```

### Creating custom base classes
프로젝트 전반에 걸쳐 많은 수의 뷰에서 일관되게 반복해야하는 사용자 정의 행동이 있는 경우 사용하는 것이 좋습니다.
```python
class BaseRetrieveView(MultipleFieldLookupMixin,
                       generics.RetrieveAPIView):
    pass

class BaseRetrieveUpdateDestroyView(MultipleFieldLookupMixin,
                                    generics.RetrieveUpdateDestroyAPIView):
    pass
```
