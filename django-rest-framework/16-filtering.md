## Filtering
REST 프레임워크의 generic list views의 기본 동작은 모델 manager에 대한 전체 쿼리 세트를 반환하는 것입니다. 때때로 당신은 API가 queryset에 의해 반환 된 항목을 제한하기를 원할 것입니다.

`GenericAPIView`를 하위 클래스로하는보기의 쿼리 세트를 필터링하는 가장 간단한 방법은 `get_queryset()` 메서드를 재정의하는 것입니다.

### Filtering against the current user
쿼리를 필터링하여 요청한 현재 인증 사용자와 관련된 결과만 반환할 수 있습니다. `request.user`의 값에 따라 필터링하면 됩니다.

```python
from myapp.models import Purchase
from myapp.serializers import PurchaseSerializer
from rest_framework import generics

class PurchaseList(generics.ListAPIView):
    serializer_class = PurchaseSerializer

    def get_queryset(self):
        """
        This view should return a list of all the purchases
        for the currently authenticated user.
        """
        user = self.request.user
        return Purchase.objects.filter(purchaser=user)
```

### Filtering against the URL
필터링의 또 다른 스타일은 URL 일부를 기반으로 쿼리 세트를 제한하는 것입니다.

```python
url('^purchases/(?P<username>.+)/$', PurchaseList.as_view()),
```
URL의 `username` 부분으로 필터링 된 쿼리 세트를 반환합니다.
```python
class PurchaseList(generics.ListAPIView):
    serializer_class = PurchaseSerializer

    def get_queryset(self):
        """
        This view should return a list of all the purchases for
        the user as determined by the username portion of the URL.
        """
        username = self.kwargs['username']
        return Purchase.objects.filter(purchaser__username=username)
```

### Filtering against query parameters
`http://example.com/api/purchases?username=denvercoder9`를 입력하고 username 매개 변수가 URL에 포함되어있는 경우, 쿼리 세트를 필터링하는 방법이다.
```python
class PurchaseList(generics.ListAPIView):
    serializer_class = PurchaseSerializer

    def get_queryset(self):
        """
        Optionally restricts the returned purchases to a given user,
        by filtering against a `username` query parameter in the URL.
        """
        queryset = Purchase.objects.all()
        username = self.request.query_params.get('username', None)
        if username is not None:
            queryset = queryset.filter(purchaser__username=username)
        return queryset
```

## Generic Filtering
복잡한 검색 및 필터를 쉽게 구성 할 수있는 generic filtering 백엔드를 지원합니다.

### Setting filter backends
#### 1. 전역으로 설정하는 법
```python
REST_FRAMEWORK = {
    'DEFAULT_FILTER_BACKENDS': ('django_filters.rest_framework.DjangoFilterBackend',)
}
```

#### 2. 각 view 또는 viewset에 설정하는 법
```python
import django_filters.rest_framework
from django.contrib.auth.models import User
from myapp.serializers import UserSerializer
from rest_framework import generics

class UserListView(generics.ListAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    filter_backends = (django_filters.rest_framework.DjangoFilterBackend,)
```

## API Guide

### DjangoFilterBackend
`django-filter` 라이브러리에는 REST 프레임워크에 대해 고도로 사용자 정의 가능한 필드 필터링을 지원하는 `DjangoFilterBackend` 클래스가 포함되어 있습니다.

```shell
pip install django-filter
```
설정하는 방법은 settings.py에 추가하거나
```python
REST_FRAMEWORK = {
    'DEFAULT_FILTER_BACKENDS': ('django_filters.rest_framework.DjangoFilterBackend',)
}
```
view 또는 viewset에 설정하는 방법이 있습니다.
```python
from django_filters.rest_framework import DjangoFilterBackend

class UserListView(generics.ListAPIView):
    ...
    filter_backends = (DjangoFilterBackend,)
```

#### Specifying filter fields
필터링할 필드를 view 또는 viewset에 `filter_fields`에 설정합니다.
```python
class ProductList(generics.ListAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    filter_backends = (filters.DjangoFilterBackend,)
    filter_fields = ('category', 'in_stock')
```
이렇게하면 주어진 필드에 대한 `FilterSet` 클래스가 자동으로 생성되며 다음과 같은 요청을 할 수 있습니다.
```
http://example.com/api/products?category=clothing&in_stock=True
```

#### Specifying a FilterSet
고급 필터링 요구 사항의 경우 뷰에서 사용해야하는 `FilterSet` 클래스를 지정할 수 있습니다.
```python
import django_filters
from myapp.models import Product
from myapp.serializers import ProductSerializer
from rest_framework import generics

class ProductFilter(django_filters.rest_framework.FilterSet):
    min_price = django_filters.NumberFilter(name="price", lookup_expr='gte')
    max_price = django_filters.NumberFilter(name="price", lookup_expr='lte')
    class Meta:
        model = Product
        fields = ['category', 'in_stock', 'min_price', 'max_price']

class ProductList(generics.ListAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    filter_backends = (django_filters.rest_framework.DjangoFilterBackend,)
    filter_class = ProductFilter
```

```
http://example.com/api/products?category=clothing&max_price=10.00
```
django-filter를 사용하여 관계를 확장 할 수도 있습니다. 각 제품에는 제조업체 모델에 대한 **외래 키**가 있다고 가정하여 제조업체 이름을 사용하여 필터링하는 필터를 만듭니다.
```python
import django_filters
from myapp.models import Product
from myapp.serializers import ProductSerializer
from rest_framework import generics

class ProductFilter(django_filters.rest_framework.FilterSet):
    # manufacturer__name 대신 이름 지정
    manufacturer = django_filters.CharFilter(name="manufacturer__name")
    class Meta:
        model = Product
        fields = [
        'category',
        'in_stock',
        'manufacturer__name',
        'manufacturer',
        ]
```

```
http://example.com/api/products?manufacturer__name=foo
http://example.com/api/products?manufacturer=foo
```

### SearchFilter
SearchFilter 클래스는 간단한 단일 쿼리 매개 변수 기반 검색을 지원하며 장고 관리자의 검색 기능을 기반으로합니다.

### OrderingFilter
OrderingFilter 클래스는 쿼리 매개 변수로 제어되는 간단한 결과 순서를 지원합니다.

기본적으로 쿼리 매개 변수의 이름은 'ordering'이지만, ORDERING_PARAM 설정으로 재정의 할 수 있습니다.

```
http://example.com/api/users?ordering=username

# reverse
http://example.com/api/users?ordering=-username

# multiple ordering
http://example.com/api/users?ordering=account,username
```

#### Specifying which fields may be ordered against
API가 정렬 필터에 허용해야하는 필드를 명시 적으로 지정하는 것이 좋습니다. 다음과 같이보기에서 ordering_fields 속성을 설정하여이를 수행 할 수 있습니다.

```python
class UserListView(generics.ListAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    filter_backends = (filters.OrderingFilter,)
    ordering_fields = ('username', 'email')
    # Specifying a default ordering
    ordering = ('username',)
```
이렇게하면 사용자가 암호 해시 필드 또는 기타 중요한 데이터에 대해 요청할 수있게하는 등 예기치 않은 데이터 유출을 방지 할 수 있습니다.

### DjangoObjectPermissionsFilter
