# Requests
REST 프레임워크의 `Request` 클래스는 표준 `HttpRequest`를 확장하여 유연한 request parsing 및 request authentication을 지원합니다.

## Request parsing
일반적으로 폼 데이터를 처리하는 것과 같은 방식으로 **JSON 데이터** 또는 **다른 미디어 유형의 데이터** 요청을 처리 할 수 ​​있습니다.

### .data
`request.data`는 request body의 parsed content를 반환합니다. 표준 `request.POST` 및 `request.FILES` 속성과 유사합니다.

### .query_params
`request.query_params`는 `request.GET`과 유사하다.

### .parsers
`parser_classes` 또는 `DEFAULT_PARSER_CLASSES` 설정을 기반으로 `Parser` 인스턴스 목록에 자동으로 설정되도록합니다.

**주의** : 기본적으로 REST 프레임워크의 `APIView` 클래스 또는 `@api_view` 데코레이터는 오류를 catch하고 `400 Bad Request` 응답을 반환합니다.


## Authentication
- API의 다른 부분에 대해 서로 다른 인증 정책을 사용
- 다중 인증 정책의 사용을 지원
- 들어오는 요청과 관련된 사용자 및 토큰 정보를 제공

### .user
`request.user`는 일반적으로 `django.contrib.auth.models.User`의 인스턴스를 반환하지만 동작은 사용되는 인증 정책에 따라 다릅니다.

요청이 인증되지 않은 경우, `request.user`의 기본값은 `django.contrib.auth.models.AnonymousUser`의 인스턴스입니다.

### .auth
`request.auth`는 추가 인증 컨텍스트를 리턴합니다. `request.auth`의 정확한 작동은 사용되는 인증 정책에 따라 다르지만 대개 요청이 **인증 된 토큰**의 인스턴스 일 수 있습니다.

요청이 인증되지 않았거나 추가 컨텍스트가없는 경우, `request.auth`의 기본값은 `None`입니다.

### .authenticators
`APIView` 클래스 또는 `@api_view` 데코레이터는 이 속성이 자동으로  `Authentication` 인스턴스 목록으로 설정되도록합니다.


## Browser enhancements
### .method
`request.method`는 요청의 HTTP 메소드를 **대문자**로 된 문자열 표현으로 리턴합니다.

브라우저 기반 `PUT`, `PATCH` 및 `DELETE` 폼을 지원합니다.

### .content_type
`request.content_type`, HTTP request body의 미디어 유형을 나타내는 문자열 객체를 반환하거나 미디어 유형이 제공되지 않은 경우 빈 문자열을 반환합니다.

### .stream
`request.stream`은 reqest body의 내용을 나타내는 스트림을 반환합니다.
