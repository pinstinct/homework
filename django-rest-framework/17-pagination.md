## Pagination
pagination API 다음 중 하나를 지원할 수 있습니다.
- 응답 내용의 일부로 제공되는 Pagination 링크
- `Content-Range` 또는 `Link`와 같은 **응답 헤더**에 포함 된 Pagination 링크

Pagination은 generic views 또는 viewsets을 사용하는 경우에만 자동으로 수행됩니다.

### Setting the pagination style
기본 pagination 스타일은 전역 적으로 설정할 수 있습니다. API 전체에서 동일한 pagination 스타일을 사용하게 됩니다.
```python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',
    'PAGE_SIZE': 100
}
```

### Modifying the pagination style
pagination 스타일의 특정 측면을 수정하려면 pagination 클래스 중 하나를 재정의하고 변경하려는 속성을 설정해야합니다.
```python
class LargeResultsSetPagination(PageNumberPagination):
    page_size = 1000
    page_size_query_param = 'page_size'
    max_page_size = 10000

class StandardResultsSetPagination(PageNumberPagination):
    page_size = 100
    page_size_query_param = 'page_size'
    max_page_size = 1000
```
그런 다음 `pagination_class` 속성을 사용하여 뷰에 새 스타일을 적용 할 수 있습니다.
```python
class BillingRecordsView(generics.ListAPIView):
    queryset = Billing.objects.all()
    serializer_class = BillingRecordsSerializer
    pagination_class = LargeResultsSetPagination
```
또는 DEFAULT_PAGINATION_CLASS 설정 키를 사용하여 스타일을 전역 적으로 적용할 수 있습니다.
```python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'apps.core.pagination.StandardResultsSetPagination'
}
```

## API Reference

### PageNumberPagination
매개변수에 single number를 `page` 번호로 받습니다.

```
GET https://api.example.org/accounts/?page=4
```

`GenericAPIView` 서브 클래스에서 `pagination_class` 속성을 사용하여 **각 뷰**별로 `PageNumberPagination`을 설정할 수도 있습니다.


### LimitOffsetPagination
여러 데이터베이스 레코드를 검색할 때 사용한다. 이 방법은 `limit`과 `offset` 파라미터를 받는다.

- limit : 반환할 최대 항목 수
- offset : 시작 위치

```
GET https://api.example.org/accounts/?limit=100&offset=400
```

`GenericAPIView` 서브 클래스에서 `pagination_class` 속성을 사용하여 **각 뷰**별로 `LimitOffsetPagination`을 설정할 수도 있습니다.

### CursorPagination
정방향 및 역방향 컨트롤 만 제공하며, 임의의 위치로 이동할 수 없다. 하지만, 다음과 같은 이점이 있다.

- 새로운 항목이 추가되도 정상적으로 동작한다.
- 큰 데이터를 사용도 지원한다.
- 속도도 느려지지 않는다.

`GenericAPIView` 서브 클래스에서 `pagination_class` 속성을 사용하여 **각 뷰**별로 `CursorPagination`을 설정할 수도 있습니다.
