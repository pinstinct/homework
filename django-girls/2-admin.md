# Django 관리자

```python
from django.contrib import admin
from .models import Post

# 관리자 페이지에서 만든 모델을 보기위해 등록
admin.site.register(Post)
```

````shell
$ ./manage.py runserver
````

로그인을 하기 위해서는, 모든 권한을 가지는 *슈퍼유저(superuser)*를 생성해야해요. 

```shell
$ ./manage.py createsuperuser
```

브라우저를 열고 주소 창에 [http://127.0.0.1:8000/admin/](http://127.0.0.1:8000/admin/) 입력하여 슈퍼유저로 로그인한 후 대시보드를 확인하세요.

