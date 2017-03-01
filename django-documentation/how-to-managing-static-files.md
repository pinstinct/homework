# [Managing static files (e.g. images, JavaScript, CSS)](https://docs.djangoproject.com/en/1.10/howto/static-files/)

## Serving files uploaded by a user during development

개발환경에서도 정상적으로 이미지 파일 경로가 동작하려면 설정이 필요하다.

```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # ... the rest of your URLconf goes here ...
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```
