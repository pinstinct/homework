# [Handling HTTP requests: Writing views](https://docs.djangoproject.com/en/1.10/topics/http/views/)

## Returning errors

## Customizing error views
Django의 기본 오류 views는 대부분의 웹 응용 프로그램에서 충분하지만, 필요하면 쉽게 재정의 할 수 있습니다. 아래에 표시된 것처럼 URLconf에서 핸들러를 지정하기만 하면됩니다.

```python
# 이렇게 지정해주면 해당 뷰로 이동
handler404 = 'mysite.views.my_custom_page_not_found_view'
```
- 404 : 페이지를 찾을 수 없다.
- 500 : 서버에 오류가 있다.
- 400 : 잘못된 요청을 보냈다.
