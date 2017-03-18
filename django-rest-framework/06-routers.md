## Routers

REST 프레임워크는 Django에 대한 자동 URL 라우팅 지원하고, 뷰 로직을 URLs 세트에 간단하고 빠르고 일관되게 연결하는 방법을 제공합니다.

### Usage
```python
from rest_framework import routers

router = routers.SimpleRouter()
router.register(r'users', UserViewSet)
router.register(r'accounts', AccountViewSet)
urlpatterns = router.urls
```

`register()` 메소드에는 두 가지 필수 인수가 있습니다.
- `prefix` : URL prefix
- `viewset` : viewset class

선택적으로 추가 인수를 지정할 수도 있습니다.
- `base_name` : URL name에 사용할 base이다. 설정되지 않은 경우 viewset의 `queryset` 속성(있는 경우)을 기반으로 자동 생성됩니다. viewset에 `queryset` 속성이 포함되어 있지 않으면 `base_name`을 설정해야합니다.

위의 예는 다음 URL 패턴을 생성합니다.
- URL pattern: `^users/$` Name: `'user-list'`
- URL pattern: `^users/{pk}/$` Name: `'user-detail'`
- URL pattern: `^accounts/$` Name: `'account-list'`
- URL pattern: `^accounts/{pk}/$` Name: `'account-detail'`

#### Using include with routers
라우터 URL 패턴도 namespace를 가질수 있습니다.
```python
urlpatterns = [
    url(r'^forgot-password/$', ForgotPasswordFormView.as_view()),
    url(r'^api/', include(router.urls, namespace='api')),
]
```

## API Guide
- SimpleRouter
    - `trailing_slash` : URL 뒤에는 슬래시(`/`)가 추가
    - `lookup_value_regex` : 보다 제한적인 (또는 관대한) 검색 패턴의 경우, viewset에 `lookup_value_regex` 속성을 설정
- DefaultRouter : SimpleRouter와 비슷하지만, API root view와 모든 list view에 대한 하이퍼 링크가 포함된 응답을 반환한다.
