# Django templates

## HTML시작하기

템플릿이란 서로 다른 정보를 일정한 형태로 표시하기 위해 **재사용 가능한 파일**이예요. 예를 들면 편지를 쓸때도 템플릿을 사용할 수 있어요. 편지의 내용이나 수신인 주소는 달라져도 동일한 디자인, 레이아웃을 사용하는 경우가 있으니까요. **장고의 템플릿 양식은 HTML을 사용**합니다.

## 템플릿 만들기

manage.py가 있는 경로에 tempates 폴더를 생성합니다.

```shell
$ l
total 280
drwxr-xr-x   7 limhm  staff   238B  2  7 06:20 .
drwxr-xr-x   8 limhm  staff   272B  2  6 23:12 ..
drwxr-xr-x  11 limhm  staff   374B  2  7 06:13 blog
-rw-r--r--   1 limhm  staff   136K  2  7 05:40 db.sqlite3
drwxr-xr-x   7 limhm  staff   238B  2  7 05:57 django_girls
-rwxr-xr-x   1 limhm  staff   810B  2  6 23:08 manage.py
drwxr-xr-x   2 limhm  staff    68B  2  7 06:20 templates

# tempates 폴더 안에 blog 폴더 생성
# blog 폴더 안에 post_list.html 생성
$ tree-python
.
├── blog
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── migrations
│   │   ├── 0001_initial.py
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   ├── urls.py
│   └── views.py
├── db.sqlite3
├── django_girls
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── manage.py
└── templates
    └── blog
        └── post_list.html
```

기본적으로 장고는 blog/templates/blog/를 찾습니다. 그렇기 때문에 설정파일에 추가해 장고에게 알려줘야 합니다. 

```python
# mysite/settings.py
TEMPLATES_DIR = os.path.join(BASE_DIR, 'templates')

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            TEMPLATES_DIR,
        ],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

웹사이트를 확인해보세요.  [http://127.0.0.1:8000/](http://127.0.0.1:8000/)

하지만 웹사이트가 비어있어 아무 내용도 보여지지 않네요. 이제 고쳐나가 봅시다. 템플릿 파일 post_list.html에 아래 내용을 넣어주세요. 

```html
<html>
    <p>Hi there!</p>
    <p>It works!</p>
</html>
```

