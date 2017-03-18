## Authentication
인증은 수신 요청을 요청한 사용자 또는 서명 된 토큰과 같은 **자격 증명**과 연결하는 메커니즘입니다. 그런 다음 권한(permission) 및 제한(throttling) 정책은 이러한 자격 증명을 사용하여 요청을 허용해야하는지 결정할 수 있습니다.

인증은 항상 view의 맨 처음, 권한 및 제한 검사가 수행되기 전에 그리고 다른 코드가 진행되기 전에 실행됩니다.

`request.user` 속성은 일반적으로 `contrib.auth` 패키지의 User 클래스 인스턴스로 설정됩니다.

`request.auth` 속성은 추가 인증 정보에 사용됩니다. 예를 들어, 서명과 함께 요청된 인증 토큰을 나타는데 사용될 수 있습니다.

**주의** : 인증 자체만으로는 들어오는 요청을 허용하거나 거부하지 않으며, 단순히 요청이 작성된 자격 증명을 식별한다는 것을 잊지 마십시오.

### How authentication is determined
인증 체계는 항상 클래스 리스트로 정의됩니다. REST 프레임워크는 리스트 안에 각 클래스에 대해 인증을 시도하고 성공적으로 인증 한 첫 번째 클래스의 반환 값을 사용하여 `request.user` 및 `request.auth`를 설정합니다.

인증되지 않으면 `request.user`는 `django.contrib.auth.models.AnonymousUser`의 인스턴스로 설정되고 `request.auth`는 `None으로` 설정됩니다.

### Setting the authentication scheme
#### 1. 전역으로 설정하는 법

```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework.authentication.SessionAuthentication',
    )
}
```

#### 2. 각 view 또는 viewset에 설정하는 법

```python
from rest_framework.authentication import SessionAuthentication, BasicAuthentication
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response
from rest_framework.views import APIView

class ExampleView(APIView):
    authentication_classes = (SessionAuthentication, BasicAuthentication)
    permission_classes = (IsAuthenticated,)

    def get(self, request, format=None):
        content = {
            'user': unicode(request.user),  # `django.contrib.auth.User` instance.
            'auth': unicode(request.auth),  # None
        }
        return Response(content)
```

#### 3. function based view에 @api_view 데코레이터를 사용하는 법

```python
@api_view(['GET'])
@authentication_classes((SessionAuthentication, BasicAuthentication))
@permission_classes((IsAuthenticated,))
def example_view(request, format=None):
    content = {
        'user': unicode(request.user),  # `django.contrib.auth.User` instance.
        'auth': unicode(request.auth),  # None
    }
    return Response(content)
```

### Unauthorized and Forbidden responses
- HTTP 401 Unauthorized
- HTTP 403 Permission Denied

HTTP 401 응답에는 항상 클라이언트에 인증 방법을 지시하는 `WWW-Authenticate` 헤더가 포함되어야합니다. HTTP 403 응답에는 `WWW-Authenticate` 헤더가 포함되지 않습니다.

사용되는 응답의 종류는 인증 체계에 따라 다릅니다. 여러 인증 스키마가 사용 중일 수 있지만 응답 유형을 결정하는 데 하나의 스키마 만 사용할 수 있습니다. 뷰에 설정된 **첫 번째 인증 클래스는 응답 유형을 결정할 때 사용**됩니다.

요청이 성공적으로 인증(authentication)될 수 있지만, 여전히 요청을 수행 할 권한(permission)이 거부 된 경우 인증 스키마에 관계없이 `403 Permission Denied` 응답이 항상 사용됩니다.

## API Reference
### BasicAuthentication
이 인증 체계는 사용자의 사용자 이름과 암호에 대해 서명된 HTTP 기본 인증을 사용합니다. 기본 인증은 일반적으로 **테스트에만 적합**합니다.

성공적으로 인증되면 BasicAuthentication은 다음 자격 증명을 제공합니다.

- `request.user` : Django `User` instance.
- `request.auth` : `None`

**주의** : 프로덕션 환경에서 `BasicAuthentication`을 사용하는 경우 **https**를 통해서만 API를 사용할 수 있어야합니다. 또한 API 클라이언트가 로그인 할 때 항상 사용자 이름과 비밀번호를 다시 요청하고 해당 세부 정보를 영구 저장소에 저장하지 않도록해야합니다.

### TokenAuthentication
이 인증 체계는 간단한 토큰 기반 HTTP 인증 체계를 사용합니다. 토큰 인증은 네이티브 데스크톱 및 모바일 클라이언트와 같은 **클라이언트-서버** 설정에 적합합니다.

```python
INSTALLED_APPS = [
    # ...
    'rest_framework.authtoken'
]
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework.authentication.SessionAuthentication',
    )
}
```
클라이언트가 인증하려면 토큰 키가 `Authorization` HTTP 헤더에 포함되어야합니다. 키에는 두 문자열을 공백으로 구분하여 문자열 리터럴 "Token"을 접두어로 사용해야합니다.

```
Authorization: Token 9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b
```
성공적으로 인증되면 `TokenAuthentication`은 다음 자격 증명을 제공합니다.
- `request.user` : Django `User` 인스턴스
- `request.auth` : `rest_framework.authtoken.models.Token` 인스턴스가됩니다.

### SessionAuthentication
이 인증 방식은 Django의 기본 세션 백엔드를 인증에 사용합니다. 세션 인증은 웹 사이트와 동일한 세션 컨텍스트에서 실행되는 AJAX 클라이언트에 적합합니다.

성공적으로 인증되면 `SessionAuthentication`은 다음 자격 증명을 제공합니다.
- `request.user` : Django `User` 인스턴스
- `request.auth` : `None`
