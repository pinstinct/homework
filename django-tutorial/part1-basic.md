## 프로젝트 만들기

생성한 프로젝트는 DocumentRoot(/var/www) 의 **바깥에** 두는것을 권합니다.

```shell
# 현재 디렉토리에서 mysite라는 이름의 프로젝트 생성
$ django-admin startproject mysite
```

startproject subcommand를 통해 아래와 같이 생성됩니다.

```
── mysite
    ├── manage.py
    └── mysite
        ├── __init__.py
        ├── settings.py
        ├── urls.py
        └── wsgi.py
```

- `mysite/` 단순히 프로젝트를 담는 공간입니다. 이 이름은 Django 와 아무 상관이 없으니, 원하는 이름으로 변경할 수 있습니다.
- `manage.py`: Django 프로젝트와 다양한 방법으로 상호작용 하는 커맨드라인의 유틸리티
- `mysite/` 디렉토리 내부에는 project 를 위한 실제 Python 패키지들이 저장합니다. 이 디렉토리 내의 이름을 이용하여, (`mysite.urls` 와 같은 식으로) project 어디서나 Python 패키지들을 import 할 수 있습니다.
- `mysite/__init__.py`: Python 으로 하여금 이 디렉토리를 패키지 처럼 다루라고 알려주는 용도의 단순한 빈 파일
- `mysite/settings.py`: 현재 Django project 의 환경/구성을 저장
- `mysite/urls.py`: 현재 Django project 의 URL 선언을 저장한다. Django 로 작성된 사이트의 “목차” 라고 할 수 있습니다.
- `mysite/wsgi.py`: 현재 project 를 서비스 하기 위한 WSGI 호환 웹 서버의 진입점

## 개발 서버

서버를 동작시키면 웹 브라우져에서 [http://127.0.0.1:8000/](http://127.0.0.1:8000/) 를 통해 접속할 수있습니다.

```shell
$ cd mysite/ 
$ python manage.py runserver
$ python manage.py runserver
Performing system checks...

System check identified no issues (0 silenced).
February 04, 2017 - 04:31:51
Django version 1.10.5, using settings 'mysite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

`runserver`는 요청이 들어올 때마다 자동으로 Python 코드를 다시 불러옵니다. 코드의 변경사항을 반영하기 위해서 굳이 서버를 재기동 하지 않아도 됩니다. 그러나, 파일을 추가하는 등의 몇몇의 동작은 개발서버가 자동으로 인식하지 못하기 때문에, 이런 상황에서는 서버를 재기동 해야 적용됩니다.

## 앱 만들기

앱(app) 의 기본 디렉토리 구조를 자동으로 생성할 수 있는 도구를 제공합니다. 

### 프로젝트 vs 앱

app 

: (블로그나 공공 기록물을 위한 데이터베이스나, 간단한 설문조사 앱과 같은) 특정한 기능을 수행하는 웹 어플리케이션을 말합니다. 

project

: 이런 특정 웹 사이트를 위한 app 들과 각 설정들을 한데 묶어놓은 것 입니다. project 는 다수의 app 을 포함할 수 있고, app 은 다수의 project 에 포함될 수 있습니다.

```shell
$ cd mysite/
$ python manage.py startapp polls
```

startapp subcommand를 통해 polls 디렉토리가 생성됩니다.

```
└── polls
    ├── __init__.py
    ├── admin.py
    ├── apps.py
    ├── migrations
    │   └── __init__.py
    ├── models.py
    ├── tests.py
    └── views.py
```

### 뷰 작성하기

```python
# polls/views.py
from django.http import HttpResponse


def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

view 를 호출하려면 이와 연결된 URL 이 있어야 하는데, 이를 위해 URLconf를 사용합니다. URLconf 사용하기위해 polls 디렉토리에서  `urls.py`  파일을 생성합니다.

```python
# polls/urls.py
from django.conf.urls import url
from . import views

urlpatterns = [
    url(r'^$', views.index, name='index'),
]
```

project 최상단의 URLconf 에서 `polls`패키지의 `urls` 모듈을 바라보게 설정합니다. 

```python
# mysite/urls.py
from django.conf.urls import include, url	# include 추가
from django.contrib import admin

urlpatterns = [
    url(r'^polls/', include('polls.urls')),
    url(r'^admin/', admin.site.urls),
]
```

개발서버를 동작시킨 후, http://localhost:8000/polls/ 를 입력하면 `index` 뷰로 정의한 내용이 보입니다.

### url() 함수

4 개의 인자를 전달할 수 있습니다. 

- `regex` : 필수 인자, `정규표현식`으로 요청받은 url의 패턴을 찾는데 사용
- `view` : 필수 인자, `HttpRequest` 객체를 첫번째 인자로 하고, 일치하는 `정규표현식` 값을 나머지 인수로 하여 `view` 함수를 호출
- `kwargs` : 옵션, view 에 dictionary 타입으로 전달
- `name` : 옵션, URL 에 이름