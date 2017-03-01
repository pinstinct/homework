# [Designers: Language overview ](https://docs.djangoproject.com/en/1.10/ref/templates/language/)

## Variables

- 변수 이름은 알파벳과 underscore(_)의 조합으로 구성
- dot(.)과 공백은 변수이름으로 사용할 수 없다.
- 존재하지 않는 변수를 사용할 때, 템플릿 시스템은 `string_if_invalid` 옵션 값을 삽입한다.
- `string_if_invalid` 기본값은 빈 값('')이다.

## Filters

- 필터를 사용해 표시할 변수를 수정할 수 있다.
- 필터를 적용하기 위해 파이프(|)를 사용한다.
- 필터는 "연결"될 수 있다. 하나의 필터가 적용된 결과로 다음 필터를 진행한다.
- 어떤 필터들은 인자를 취한다.

```
{{ name|lower }}

# linebreaks: \n을 <p> 태그로 변경
{{ text|escape|linebreaks }}

# truncatewords: 인자를 취함, 30단어만 보여줌
{{ bio|truncatewords:30 }}

{{ list|join:", " }}
```

장고는 60개의 내장 템플릿 필터를 제공한다. 일반적으로 많이 사용하는 필터는 다음과 같다.

- `default` : 변수가 False이거나 비어있으면, 주어진 default를 사용
- `length` : 값의 길이 반환
- `filesizeformat` : 값을 "사람이 읽을 수 있는" 파일사이즈로 포맷

## Tags

장고는 24개의 내장 템플릿 태그를 제공한다. 일반적으로 많이 사용하는 태그는 다음과 같다.

- `for`
- `if`, `elif`, `else` : `if` 태그 안에서 필터와 연산자(operator)를 사용할 수 있다.
- `block`, `extends` : 템플릿 상속 설정



## Template inheritance

템플릿 상속은 기본 "골격" 템플릿을 만드는 것이다. 기본 골격 템플릿은 사이트의 공통 요소를 포함하고, 자식 템플릿이 재정의 할 수 있는 `block`을 정의한다.

```html
<!-- 재정의하지 않으면, My amazing site가 기본 값 -->>
<title>{% block title %}My amazing site{% endblock %}</title>
```
- `{% extends %}` : 템플릿의 처 번째 태그여야만 함
- `{% block %} `
- `{{ block.super }}` : 상위요소의 내용을 가져옴


## Automatic HTML escaping
XSS(Cross Site Scripting) 공격을 피하기 위한 두 가지 방법이 있다.

- 잠재적으로 위험한 HTML 문자를 **escape** 필터하는 법 (Django 처음 몇 년 동안의 기본 솔루션)
- Django의 자동 HTML escaping을 사용하는 법

장고 템플릿 시스템을 사용한다면 기본적으로 두 번째 방법이 적용되어 있다.

### How to turn it off
자동 escaping을 사용하고 싶지 않다면, 여러가지 방법으로 끌 수 있다.

- For individual variables : `safe` 필터
- For template blocks : `autoescape` 태그

```html
<!-- For individual variables -->
This will be escaped: {{ data }}

<!-- For template blocks -->
{% autoescape off %}
    Hello {{ name }}
{% endautoescape %}
```


## Accessing method calls
템플릿은 뷰로부터 전달받은 클래스 속성(like 필드이름)과 변수에 접근 할 수 있다.

유사하게, **QuerySets**에서 제공하는 `count()` 메서드도 사용할 수 있다.

```html
<!-- 메서드에 괄호를 안 붙여도 됨 -->
{{ task.comment_set.all.count }}
```
템플릿 언어에서 사용할 수 있는 논리 처리량을 제한한다. 데이터는 뷰에서 계산 된 다음 표시를 위해 템플릿으로 전달한다.

## Custom tag and filter libraries
`INSTALLED_APPS`에 `django.contrib.humanize`를 추가한 후, 템플릿에 `load` 태그를 사용해 불러옵니다.

```html
{% load humanize %}

<!-- 숫자를 세자리로 끊어주는 기능 -->
{{ 45000|intcomma }}
```
