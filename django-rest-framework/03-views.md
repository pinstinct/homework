## Class-based Views

REST 프레임워크는 Django의 `View`의 subclass인 `APIView` 클래스를 제공합니다.

`APIView` 클래스를 사용하는 것은 일반 `View` 클래스를 사용하는 것과 거의 같습니다.

### API policy attributes
- `.renderer_classes`
- `.parser_classes`
- `.authentication_classes`
- `.throttle_classes`
- `.permission_classes`
- `.content_negotiation_class`

### Dispatch methods
뷰의 `.dispatch()` 메소드에 의해 직접 호출됩니다. 이들은 `get()`, `post()`, `put()`, `patch()` 및 `delete()`와 같은 핸들러 메소드를 호출하기 전후에 수행되어야하는 모든 조치를 수행합니다.


## Function Based Views
함수 기반의 뷰를 래핑하는 간단한 **데코레이터** 세트를 제공하며, 요청이 처리됩니다.

### @api_view()
뷰가 응답해야하는 **HTTP 메소드**의 목록을 취하는 `api_view` 데코레이터

```python
@api_view(['GET', 'POST'])
def hello_world(request):
    if request.method == 'POST':
        return Response({"message": "Got some data!", "data": request.data})
    return Response({"message": "Hello, world!"})
```
