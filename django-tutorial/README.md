# 소개
[장고 튜토리얼](http://django-document-korean.readthedocs.io/ko/latest/intro/index.html)을 보고 정리한 문서입니다.

## 프로젝트
[https://github.com/pinstinct/django_tutorial](https://github.com/pinstinct/django_tutorial)


# 설치

## 파이썬 설치

`python` 명령어를 통해 설치되어있는지 확인합니다.

```shell
$ python
Python 3.4.3 (default, Jan 25 2017, 11:10:47)
[GCC 4.2.1 Compatible Apple LLVM 8.0.0 (clang-800.0.42.1)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
```

## 장고 설치

```shell
# 가상환경에서 설치
$ pyenv virtualenv 3.4.3 django-tutorial
$ cd your/dir/path
$ pyenv local django-tutorial
$ pip install django
```

## 장고 설치 확인

버전이 정상적으로 출력되는지 확인합니다.

```shell
$ python
>>> import django
>>> print(django.get_version())
1.10.5

# 또는 아래의 방법으로도 확인할 수 있다.
$ python -m django --version
1.10.5
```
