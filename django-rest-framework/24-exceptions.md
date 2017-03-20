## Exceptions
### Exception handling in REST framework views
REST 프레임워크의 뷰는 다양한 예외를 처리하고 적절한 오류 응답을 반환합니다. 처리되는 예외는 다음과 같습니다.
- REST 프레임워크 내에서 발생하는 `APIException`의 서브 클래스
- 장고의 `Http404` 예외
- 장고의 `PermissionDenied` 예외

각각의 경우에 REST 프레임워크는 적절한 `status code` 및 `content-type`이 포함 된 응답을 반환합니다. 응답 body에는 오류의 성격과 관련된 추가 세부 정보가 포함됩니다.

유효성(Validation) 검사 오류는 약간 다르게 처리되며 필드 이름을 응답의 키로 포함합니다. 유효성 검사 오류가 특정 필드에만 해당되지 않으면 "non_field_errors"키를 사용하거나 NON_FIELD_ERRORS_KEY 설정에 대해 설정된 문자열 값을 사용합니다.

### Custom exception handling
함수는 인수 쌍을 취해야하며
- 첫 번째는 처리 할 예외
- 두 번째는 현재 처리중인 뷰와 같은 추가 컨텍스트를 포함하는 딕셔너리

예외 핸들러 함수는 `Response` 객체를 반환하거나 예외를 처리 할 수없는 경우 `None`을 반환해야합니다. 만약 `None`을 반환하면 예외가 다시 발생하고 Django는 표준 **HTTP 500 'server error'** 응답을 반환합니다.

```python
from rest_framework.views import exception_handler

def custom_exception_handler(exc, context):
    # Call REST framework's default exception handler first,
    # to get the standard error response.
    response = exception_handler(exc, context)

    # Now add the HTTP status code to the response.
    if response is not None:
        response.data['status_code'] = response.status_code

    return response
```
`EXCEPTION_HANDLER`를 사용하여 settings.py에서 예외 처리기를 설정해야합니다.

```python
REST_FRAMEWORK = {
    'EXCEPTION_HANDLER': 'my_project.my_app.utils.custom_exception_handler'
}
```

## API Reference
- APIException : `APIView` 클래스 또는 `@api_view` 내부에서 발생한 모든 예외에 대한 기본 클래스
- ParseError : request.data에 액세스 할 때 요청에 잘못된 데이터가 포함 된 경우 발생
- AuthenticationFailed : 들어오는 요청에 잘못된 인증이 포함될 때 발생
- NotAuthenticated : 인증되지 않은 요청이 권한 검사에 실패하면 발생
- PermissionDenied : 인증 된 요청이 권한 검사에 실패하면 발생
- NotFound : 주어진 URL에 자원이 없을 때 발생
- MethodNotAllowed : 뷰의 핸들러 메서드에 매핑되지 않는 들어오는 요청이 발생하면 발생
- NotAcceptable : 사용 가능한 렌더러에 없는 `Accept` 헤더로 요청이 들어올 때 발생
- UnsupportedMediaType : `request.data`에 액세스 할 때 요청 데이터의 내용 유형을 처리 할 수있는 파서가 없는 경우 발생합
- Throttled : 들어오는 요청이 스로틀 검사에 실패하면 발생
- ValidationError : `ValidationError` 예외는 다른 `APIException` 클래스와 약간 다릅니다.
