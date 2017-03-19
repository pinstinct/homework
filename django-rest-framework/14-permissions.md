## Permissions

인증(authentication) 및 제한(throttling)과 함께 사용 권한(permissions)은 요청(request)에 액세스를 허용할지 또는 거부 할지를 결정합니다.

권한(permissions) 검사는 다른 코드가 진행되기 전에 view의 맨 처음에 항상 실행됩니다. 권한 검사는 일반적으로 들어오는 요청을 허용해야 하는지를 결정하기 위해 `request.user` 및 `request.auth` 인증(authentication) 정보를 사용합니다.

**권한(Permissions)**은 다른 클래스의 사용자가 API의 다른 부분에 액세스하는 것을 허용하거나 거부하는 데 사용됩니다.

가장 간단한 사용 권한 스타일은 인증 된 사용자에게 액세스를 허용하고 인증되지 않은 모든 사용자에 대한 액세스를 거부하는 것입니다. 이것은 REST 프레임워크의 `IsAuthenticated` 클래스에 해당합니다.

약간 덜 엄격한 권한 스타일은 인증 된 사용자에게 모든 권한을 허용하지만 인증되지 않은 사용자에게는 읽기 전용 권한을 허용하는 것입니다. 이것은 REST 프레임 워크의 `IsAuthenticatedOrReadOnly` 클래스에 해당합니다.

### How permissions are determined
REST 프레임워크의 권한(permission)은 항상 권한 클래스 리스트로 정의됩니다.

뷰의 본문을 실행하기 전에 목록의 각 권한이 검사됩니다. 권한 확인에 실패하면 `exceptions.PermissionDenied` 또는 `exceptions.NotAuthenticated` 예외가 발생하고보기 본문이 실행되지 않습니다.

권한 검사가 실패하면 다음 규칙에 따라 "403 Forbidden"또는 "401 Unauthorized"응답이 반환됩니다.
- HTTP 403 Forbidden : 요청이 성공적으로 인증되었지만 권한이 거부되었습니다.
- HTTP 403 Forbidden : 요청이 성공적으로 인증되지 않았고 최상위 우선 순위 인증(authentication) 클래스는 `WWW-Authenticate` 헤더를 사용하지 않습니다.
- HTTP 401 Unauthorized (with 적절한 `WWW-Authenticate` 헤더) : 요청이 성공적으로 인증되지 않았고 최상위 우선 순위 인증 클래스가 `WWW-Authenticate` 헤더를 사용합니다.

### Object level permissions
REST 프레임워크 권한(permission)은 오브젝트 레벨 권한 부여를 지원합니다. 개체 수준 권한은 사용자가 특정 개체(일반적으로 모델 인스턴스)에 대한 작업을 허용해야하는지 여부를 결정하는 데 사용됩니다.

### Setting the permission policy
#### 1. 전역으로 설정하는 법
```python
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': (
        # 기본 권한 정책
        'rest_framework.permissions.IsAuthenticated',
        # 지정하지 않으면, 기본적으로 무제한 액세스를 허용
        #'rest_framework.permissions.AllowAny',
    )
}
```

#### 2. 각 view 또는 viewset에 설정하는 법

```python
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response
from rest_framework.views import APIView

class ExampleView(APIView):
    permission_classes = (IsAuthenticated,)

    def get(self, request, format=None):
        content = {
            'status': 'request was permitted'
        }
        return Response(content)
```

#### 3. function based view에 @api_view 데코레이터를 사용하는 법

```python
from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response

@api_view(['GET'])
@permission_classes((IsAuthenticated, ))
def example_view(request, format=None):
    content = {
        'status': 'request was permitted'
    }
    return Response(content)
```
**참고** : 클래스 속성이나 데코레이터를 통해 새 권한 클래스를 설정하면 `settings.py` 파일을 통해 설정된 기본 목록을 무시하도록 뷰에 지시합니다.


## API Reference
- AllowAny : 인증(authentication) 여부에 관계없이 제한되지 않은 액세스를 허용합니다.
- IsAuthenticated : 인증되지 않은 사용자에게 권한(permission)을 거부하고 그렇지 않은 경우에는 권한을 허용합니다. 이 권한은 등록 된 사용자 만 API에 액세스 할 수있게하려는 경우 적합합니다.
- IsAdminUser : `user.is_staff`가 `True`인 경우를 제외하고 모든 사용자에 대한 사용 권한을 거부합니다. 이 권한은 신뢰할 수 있는 관리자의 하위 집합에서만 API에 액세스 할 수있게하려는 경우 적합합니다.
- IsAuthenticatedOrReadOnly :
인증 된 사용자가 모든 요청을 수행 할 수 있습니다. 권한이없는 사용자에 대한 요청은 요청 방법이 `GET`, `HEAD` 또는 `OPTIONS` 중 하나 일 경우에만 허용됩니다. 이 권한은 API에서 익명 사용자에게 읽기 권한을 허용하고 인증 된 사용자에게만 쓰기 권한을 허용하려는 경우에 적합합니다.
- DjangoModelPermissions : 이 권한은 `queryset` 속성이 설정된 view에만 적용해야합니다. 권한 부여는 사용자가 인증되고 관련 모델 권한이 할당 된 경우에만 부여됩니다.
- DjangoModelPermissionsOrAnonReadOnly : `DjangoModelPermissions`와 유사하지만 인증되지 않은 사용자는 API에 대한 읽기 전용 액세스만 허용합니다.
- DjangoObjectPermissions
