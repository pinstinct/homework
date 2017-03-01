# [contrib packages: The Django admin site](https://docs.djangoproject.com/en/1.10/ref/contrib/admin/)

- 관리자 사이트는 유저 그룹별로 권한을 달리 할 수 있다.
- 하지만 내부에서 사용하길 추천한다.
- end user가 사용해서는 안된다.
- 커스텀하여 사용할 수 있는 기능이 많지만, 너무 많은 기능을 커스텀하려면 새로 만드는 것이 좋다.

## Overview

```python
# Settings.py
# admin 사이트를 이용하기 위한 필수 설정
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',

    # 정의한 모든 모델을 추적
    'django.contrib.contenttypes',
    'django.contrib.sessions',

    # 웹 어플리케이션에서 사용자 입력을 처리 후,
    # 사용자에게 일회성 알림 메시지(aka "플래시 메시지")를 표시
    'django.contrib.messages',
]
```

## ModelAdmin objects
`ModelAdmin` 클래스는 admin 인터페이스의 모델을 나타낸다.

```python
# admin.py
from django.contrib import admin
from myproject.myapp.models import Author

class AuthorAdmin(admin.ModelAdmin):
    pass
admin.site.register(Author, AuthorAdmin)
```
위의 `ModelAdmin` 클래스는 아직 사용자 정의 값을 정의하지 않았다. 결과적으로 기본 관리 인터페이스가 제공된다.

### ModelAdmin options
`ModelAdmin`은 매우 유연하다. 사용자 정의 인터페이스를 위한 여러 가지 옵션이 있다. 모든 옵션은 `ModelAdmin` 하위 클래스에서 정의한다.

```python
from django.contrib import admin

class PostAdmin(admin.ModelAdmin):
    list_display = ('id', 'author', 'is_visible')

admin.site.register(Post, PostAdmin)
```

- ModelAdmin.list_display : admin 페이지의 변경 목록에 표시될 필드
