# 설정

## 기본 설정

```python
# django_girls/settings.py

# 시간대 설정
TIME_ZONE = 'Asia/Seoul'

# 정적파일 경로 추가
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```



## 데이터베이스 설정

```shell
$ ./manage.py migrate
```



# 모델

## 객체 (Object)

"블로그 글"이라는 객체를 모델링해봅시다.

```
Post
--------
title
text
author
created_date
published_date
```



## 장고 모델

이번에는 블로그 글을 위한 장고 모델을 만들어봅시다. **장고 안의 모델은 객체의 특별한 종류**입니다. 이 모델을 저장하면 그 내용이 `데이터베이스`에 저장되는 것이 특별한 점이죠. 

쉽게 말해 데이터베이스안의 모델이란 엑셀 스프레드시트와 같다고 말할 수 있어요. 엑셀 스프레드시트를 보면 열(필드) 와 행(데이터) 로 구성되어 있죠? 모델도 마찬가지입니다.

## 어플리케이션 제작

```shell
$ ./manage.py startapp blog
$ tree-python
.
├── django_app
│   ├── blog
│   │   ├── __init__.py
│   │   ├── admin.py
│   │   ├── apps.py
│   │   ├── migrations
│   │   │   └── __init__.py
│   │   ├── models.py
│   │   ├── tests.py
│   │   └── views.py
│   ├── db.sqlite3
│   ├── django_girls
│   │   ├── __init__.py
│   │   ├── settings.py
│   │   ├── urls.py
│   │   └── wsgi.py
│   └── manage.py
└── requirements.txt
```

어플리케이션을 생성한 후 장고에게 사용해야한다고 알려줘야 합니다. 이 역할을 하는 파일이 `mysite/settings.py`입니다. 

```python
# django_girls/settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    
    'blog.apps.BlogConfig'
]
```

## 블로그 글 모델 만들기

모든 `Model` 객체는 `blog/models.py` 파일에 선언하여 모델을 만듭니다. 

```python
# blog/models.py
from django.db import models
from django.utils import timezone

# 모델을 정의 (모델은 객체의 종류)
# models.Model은 Post가 장고 모델임을 의미
# 장고는 Post가 데이터베이스에 저장되어야 하는걸 알게 됨
class Post(models.Model):
    author = models.ForeignKey('auth.User')
    title = models.CharField(max_length=200)
    text = models.TextField()
    created_date = models.DateTimeField(
            default=timezone.now)
    published_date = models.DateTimeField(
            blank=True, null=True)

    # 메서드
    def publish(self):
        self.published_date = timezone.now()
        self.save()

    def __str__(self):
        return self.title
```

- models.CharField - 글자 수가 제한된 텍스트를 정의할 때 사용합니다. 글 제목같이 대부분의 짧은 문자열 정보를 저장할 때 사용합니다.
- models.TextField - 글자 수에 제한이 없는 긴 텍스트를 위한 속성입니다. 블로그 콘텐츠를 담기 좋겠죠.
- models.DateTimeField - 이것은 날짜와 시간을 의미합니다.
- models.ForeignKey - 다른 모델이 대한 링크를 의미합니다.

## 데이터베이스 생성

장고 모델에 몇 가지 변화가 생겼다는 걸 알게 해줘야 합니다. 

```shell
$ ./manage.py makemigrations blog
Migrations for 'blog':
  blog/migrations/0001_initial.py:
    - Create model Post
```

장고는 데이터베이스에 지금 반영할 수 있도록 마이그레이션 파일(migration file)이라는 것을 준비해 두었답니다. 이제 실제 데이터베이스에 모델 추가를 반영하겠습니다. 

```shell
$ ./manage.py migrate blog
Operations to perform:
  Apply all migrations: blog
Running migrations:
  Applying blog.0001_initial... OK
```

