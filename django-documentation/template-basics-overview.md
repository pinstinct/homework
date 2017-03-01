# [Basics: Overview](https://docs.djangoproject.com/en/1.10/topics/templates/)

- HTML을 동적으로 생성하기 위해 템플릿을 사용한다.
- 장고는 DTL이라고 부르는 장고 템플릿 시스템을 사용한다.
- Rendering은 `render()`를 사용하여 템플릿에 context data를 삽입한다.

## Support for template engines

### Configuration

템플릿 파일을 로드 설정에는 두 가지 방법이 있다.

- `DIRS` : 데릭토리를 정의 (더 많이 사용하는 방법)
- `APP_DIRS` : 설치된 app 내의 템플릿을 명시 (잘 사용하지 않음)

## The Django template language (중요)

### Syntax

렌더링은 변수들을 컨텍스트에 있는 값들로 바꾸고 태그를 실행한다.

#### Variables

- 변수는 context에서 값을 출력한다.
- 변수는 `{{ }}`로 둘러싸여 있다.
- 딕셔너리 검색, 속성 검색, 리스트 인덱스 검색은 dot(.) 표기법을 사용한다.

```html
{{ my_dict.key }}
{{ my_object.attribute }}
{{ my_list.0 }}
```

#### Tags

- 태그는 제어문의 역할을 해, content를 출력할 수 있다. (예: if, for)
- 태그는 `{% %}`로 둘러싸여 있다.
- 대부분 태그는 인자를 허용한다.
- 일부 태그는 시작과 종료 태그가 필요하다.

```html
{# cycle은 odd와 even을 반복 #}
{% cycle 'odd' 'even' %}
{% if user.is_authenticated %}Hello, {{ user.username }}.{% endif %}
```

#### Filters

```html
{# 데이트타임 객체가 왔을 때, 타임존 설정에 따라 변환되서 출력 #}
{{ my_date|date:"Y-m-d" }}
```

#### Comments

```html
{# 주석은 html로 변환되지 않는다. #}
{# this won't be rendered #}
```

### Components

#### Context

- Context는 현재 HttpRequest를 저장하고, 템플릿 Context Processors를 실행한다.
- Context 데이터는 일반 dict로 전달되고, 필요한 경우 현재 HttpRequest가 개별적으로 전달된다.
- `Render()`할 때, 전달해주는 객체

#### Context processors

- Context Processors는 현재 HttpRequest를 인자로 받아,
- rendering context에 추가할 데이터 dict을 반환하는 함수다.
- 주된 용도는 모든 템플릿에 코드를 반복하지 않고, 공통 데이터를 컨텍스트에 추가하는 것이다.
