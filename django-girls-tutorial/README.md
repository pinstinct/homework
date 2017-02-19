# Django란?

Django (*/ˈdʒæŋɡoʊ/ jang-goh/쟁고/장고*)는 파이썬으로 만들어진 무료 오픈소스 웹 어플리케이션 프레임워크(web application framework) 입니다. 쉽고 빠르게 웹사이트를 개발할 수 있도록 돕는 구성요소로 이루어진 웹 프레임워크이랍니다.

## 장고 웹 사이트 통신

편지(request, 요청) 이 도착했는지 확인해주는 메일박스(port, 포트) 가 있다고 상상해보세요. 이 것은 웹 서버가 해주는 일이에요. 웹 서버는 받은 편지를 읽고 웹 페이지와 함께 답장을 준답니다. 그런데 무언가를 주고 싶을 때는 그 안에 내용이 있어야하죠. 장고는 그 특정 컨텐츠를 만들 수 있는 역할을 합니다.

**웹 서버에 요청이 오면 장고로 전달**됩니다. 그러면 장고는 실제로 어떤 요청이 들어왔는지 확인합니다. 첫 번째로 **웹 페이지의 주소를 가져와 무엇을 할지 확인**합니다. 이 것이 바로 장고의 **urlresolver**가 하는 역할이에요. (웹 사이트 주소는 URL - Uniform Resource Locator을 말합니다. 그래서 *urlresolver*가 이해하게 됩니다.) **패턴 목록을 가져와 URL과 맞는지 하나 씩 대조**해보는 것은 그리 똑똑한 방법이 아니죠. 그러나 장고는 위에서 부터 아래로 그 패턴을 확인해봅니다. 만약 **일치하는 게 있다고 하면, 장고는 그 요청을 관련된 함수(*view*)** 에 넘겨줍니다.

# 프로젝트 생성, 관리

```shell
$ mkdir django_girls_hw
$ cd django_girls_hw

# 가상환경 생성
$ pyenv virtualenv 3.4.3 django_girls

# 가상환경 적용
$ pyenv local django_girls

# Django 설치
$ pip list
$ pip install django

# Django Project 생성
$ django-admin startproject django_girls

# Django Project 이름 변경
$ mv django_girls django_app

# 프로젝트 구성 확인
$ tree-python
.
├── django_app
│   ├── db.sqlite3
│   ├── django_girls
│   │   ├── __init__.py
│   │   ├── settings.py
│   │   ├── urls.py
│   │   └── wsgi.py
│   └── manage.py
└── requirements.txt
```



# 버전관리

```shell
$ git init
$ vi .gitignore
$ pip freeze > requirements.txt

# pycharm 실행
# Project Interpreter 설정
$ py .

$ git status
$ git add -A
$ git commit -m "Project init"
```



> 프로젝트 확인: https://github.com/pinstinct/django-girls-tutorial-hw
>
> 
>
> 히스토리 확인: https://github.com/pinstinct/django-girls-tutorial-hw/commits/master