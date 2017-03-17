# Responses
REST 프레임워크는 `Response` 클래스를 제공합니다.

`Response` 객체는 **native Python** 요소로 구성된 데이터로 초기화됩니다. 그런 다음 REST 프레임워크는 표준 HTTP 내용 협상을 사용하여 최종 응답 내용을 렌더링하는 방법을 결정합니다.

## Attributes
### .data
`Request` 객체의 렌더링되지 않은 content

### .status_code
HTTP 응답의 숫자 상태 코드입니다.

### .content
응답의 렌더링 된 내용입니다. `.content`에 액세스하려면 먼저 `.render()` 메서드를 호출해야합니다.
