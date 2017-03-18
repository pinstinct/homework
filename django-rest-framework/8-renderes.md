## Renderers
TemplateResponse 인스턴스가 클라이언트에 반환되기 전에 렌더링되어야합니다. 렌더링 프로세스는 템플릿과 컨텍스트의 중간 표현을 가져 와서 클라이언트에 제공 할 수있는 **바이트 스트림**으로 바꿉니다.

### How the renderer is determined
컨텐츠 협상(content negotiation)의 기본 프로세스는 요청의 `Accept` 헤더를 조사하여 응답에서 예상하는 미디어 유형을 판별하는 것입니다.

### Setting the renderers

#### 1. 전역으로 설정하는 법
```python
REST_FRAMEWORK = {
    'DEFAULT_RENDERER_CLASSES': (
        'rest_framework.renderers.JSONRenderer',
        'rest_framework.renderers.BrowsableAPIRenderer',
    )
}
```

#### 2. 각 view 또는 viewset에 설정하는 법
```python
from django.contrib.auth.models import User
from rest_framework.renderers import JSONRenderer
from rest_framework.response import Response
from rest_framework.views import APIView

class UserCountView(APIView):
    """
    A view that returns the count of active users in JSON.
    """
    renderer_classes = (JSONRenderer, )

    def get(self, request, format=None):
        user_count = User.objects.filter(active=True).count()
        content = {'user_count': user_count}
        return Response(content)
```

#### 3. function based view에 @api_view 데코레이터를 사용하는 법

```python
@api_view(['GET'])
@renderer_classes((JSONRenderer,))
def user_count_view(request, format=None):
    """
    A view that returns the count of active users in JSON.
    """
    user_count = User.objects.filter(active=True).count()
    content = {'user_count': user_count}
    return Response(content)
```

### Ordering of renderer classes
클라이언트가 `Accept` 헤더를 포함하지 않거나 `Accept : */*`같이 명시하지 않는다면, REST 프레임워크는 목록에서 응답에 사용할 첫 번째 렌더러를 선택합니다.

## API Reference
- JSONRenderer
    - .media_type: `application/json`
    - .format: `'.json'`
- TemplateHTMLRenderer
    - .media_type: `text/html`
    - .format: `'.html'`
- StaticHTMLRenderer
    - .media_type: `text/html`
    - .format: `'.html'`
- BrowsableAPIRenderer
    - .media_type: `text/html`
    - .format: `'.api'`
- AdminRenderer
    - .media_type: `text/html`
    - .format: `'.admin'`
- HTMLFormRenderer
    - .media_type: `text/html`
    - .format: `'.form'`
- MultiPartRenderer
    - .media_type: `multipart/form-data; boundary=BoUnDaRyStRiNg`
    - .format: `'.multipart'`
