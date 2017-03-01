# [Reference: Request and response objects](https://docs.djangoproject.com/en/1.10/ref/request-response/)

## Quick overview
페이지가 요청될 때,
- Django는 `HttpRequest` 객체를 생성
  - `HttpRequest` 객체는 요청에 대한 메타 데이터를 포함
- Django는 적절한 뷰를 로드
- 뷰 함수의 첫 번째 인자로 `HttpRequest`를 전달
  - 각 뷰는 `HttpResponse` 객체를 반환합니다.

## HttpRequest objects
### Attributes
모든 속성은 다른 언급이 없으면, **읽기 전용**으로 간주

- HttpRequest.scheme : 요청의 체계(scheme)를 표현하는 문자열
- HttpRequest.body : **바이트 형식**의 raw HTTP 요청 본문 (기존의 form 데이터를 처리하려면, `HttpRequest.POST` 사용)
- HttpRequest.path_info : 웹 서버 설정과 상관없이 항상 경로 정보 부분을 포함
- HttpRequest.method : 요청에 사용된 HTTP 메서드를 나타내는 문자열 (**대문자**로 사용)
- HttpRequest.encoding : form 제출 데이터를 디코딩하는데 사용한 인코딩 방식의 문자열
  - `DEFAULT_CHARSET` 설정 사용 시, 인코딩 방식의 문자열 대신 **None**
- HttpRequest.content_type : 리소스 타입, request에 넘어온 데이터 형식 (Json인지, 자바스크립트인지, ...)
- HttpRequest.content_params : CONTENT_TYPE 헤더에 포함 된 키-값 파라미터 사전형
- HttpRequest.GET : 주어진 **HTTP GET**의 모든 파라미터를 포함하는 사전형 객체
- HttpRequest.POST : 주어진 **HTTP POST**의 모든 파라미터를 포함하는 사전형 객체 (단, 요청에 form 데이터가 들어있는 경우 제공)
  - raw 또는 non-form 데이터에 접근해야하는 경우, `HttpRequest.body` 속성을 통해 접근해야 함
  - 빈 POST dictionary가 올 수 있으므로, `request.method == "POST"`를 사용해 확인해야 함
  - **POST**는 파일 업로드 정보는 포함하지 않음
- HttpRequest.COOKIES : 키-값 문자열로 이루어진 사전형 쿠키 정보
- HttpRequest.FILES : 업로드 된 모든 파일을 포함하는 사전형 객체
  - FILES의 각 키는 `<input type = "file" name = ""/>`의 **name**
  - FILES는 `<form>` **메소드가 POST**이고,
  - `<form>`에 `enctype="multipart/form-data"`가있는 경우에만 데이터를 포함
  - 그렇지 않으면 FILES는 빈 사전형 객체
- HttpRequest.META :사용 가능한 모든 **HTTP 헤더**를 포함하는 사전형 객체
  - CONTENT_LENGTH, CONTENT_TYPE, HTTP_ACCEPT_LANGUAGE, HTTP_REFERER, HTTP_USER_AGENT, QUERY_STRING, REMOTE_ADDR, EQUEST_METHOD, SERVER_NAME, ...
- HttpRequest.resolver_match

### Attributes set by application code
- HttpRequest.current_app : `url` 템플릿 태그 ` reverse()`의 인자로 current_app의 값을 사용
- HttpRequest.urlconf : `ROOT_URLCONF` 설정을 오버라이드하고, 현재 요청의 루트 URLconf로 사용


### Attributes set by middleware
Django의 `contrib` 앱에 포함 된 미들웨어 중 일부는 요청에 대한 속성을 설정한다.

- HttpRequest.session
- HttpRequest.site
- HttpRequest.user
