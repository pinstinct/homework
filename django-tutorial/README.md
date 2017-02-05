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
$ pip install django

# 가상환경에서 설치
$ pyenv virtualenv 3.4.3 django-tutorial
$ cd your/dir/path
$ pyenv local django-tutorial
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

