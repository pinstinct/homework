# Parsers
REST 프레임워크는 Parser 클래스가 내장되어있어 다양한 미디어 유형으로 요청을 수락 할 수 있습니다.

## How the parser is determined
`request.data`에 액세스할 때, REST 프레임워크는 들어오는 요청의 `Content-Type` 헤더를 검사하고 요청 내용을 구문 분석하는 데 사용할 구문 분석기(parser)를 결정합니다.

**주의** : 항상 HTTP 요청으로 데이터를 보낼 때 `Content-Type` 헤더를 설정해야합니다.

`Content-Type`을 설정하지 않으면 대부분의 클라이언트는 `'application / x-www-form-urlencoded'`을 기본값으로 사용합니다.

## Setting the parsers

### 1. 전역으로 설정하는 법
```python
REST_FRAMEWORK = {
    'DEFAULT_PARSER_CLASSES': (
        'rest_framework.parsers.JSONParser',
    )
}
```

### 2. 각 view 또는 viewset에 설정하는 법
```python
from rest_framework.parsers import JSONParser
from rest_framework.response import Response
from rest_framework.views import APIView

class ExampleView(APIView):
    """
    A view that can accept POST requests with JSON content.
    """
    parser_classes = (JSONParser,)

    def post(self, request, format=None):
        return Response({'received data': request.data})
```
### 3. function based view에 `@api_view` 데코레이터를 사용하는 법
```python
from rest_framework.decorators import api_view
from rest_framework.decorators import parser_classes

@api_view(['POST'])
@parser_classes((JSONParser,))
def example_view(request, format=None):
    """
    A view that can accept POST requests with JSON content.
    """
    return Response({'received data': request.data})
```
