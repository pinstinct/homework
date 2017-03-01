# [django.conf.urls utility functions](https://docs.djangoproject.com/en/1.10/ref/urls/)

## static()
**디버그 모드**에서 파일을 제공하기위한 `URL pattern`을 반환

```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # ...
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

## url()
`urlpatterns`는 `url()` 인스턴스의 목록

```python
from django.conf.urls import include, url

urlpatterns = [
    url(r'^index/$', index_view, name='main-view'),
    url(r'^weblog/', include('blog.urls')),
    ...
]
```
`url(regex, view, kwargs=None, name=None)`

- regex : 정규 표현식
- view : view 함수 / `as_view()`의 결과 / `include()`
- kwargs : view 함수에 추가 인자를 전달
- name : URL 역참조를 수행하기 위해 사용

## include()
다른 `URLconf` 모듈로 경로를 가져 오는 함수
