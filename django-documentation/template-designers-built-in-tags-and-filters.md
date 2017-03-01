# [Designers: Built-in tags and filters](https://docs.djangoproject.com/en/1.10/ref/templates/builtins/)

## Built-in tag reference

### autoescape

block안에서 자동 escape 적용되는지 여부를 결정한다.

```html
{% autoescape on %}
    {{ body }}
{% endautoescape %}
```

### block

자식 템플릿이 재정의 할 수 있는 block을 정의한다.

### url
주어진 뷰 및 매개 변수와 일치하는 절대 경로 참조를 반환

```html
<!-- URL을 따옴표로 묶지 않으면 컨텍스트 변수로 해석하므로 반드시 따옴표를 사용 -->
{% url 'some-url-name' v1 v2 %}
<!-- 네임스페이스 URL을 검색할 때 -->
{% url 'myapp:view-name' %}
```
역참조하는 URL이 없으면 **NoReverseMatch** 예외가 발생한다.

## Built-in filter reference

### Date
주어진 포맷에 따라 날짜 형식을 지정한다.

```html{%raw%}
{{ value|date:"D d M Y" }}
```
