# [Authentication: Using the authentication system](https://docs.djangoproject.com/en/1.10/topics/auth/default/)



## User objects
기본 사용자의 primary 속성

- username : 장고와 장고관련 3rd party 툴에서도 기본적으로 사용하기 때문에 변경하지 않는 것이 좋다.
- password
- email
- first_name
- last_name


### Creating users
```shell
In [2]: from django.contrib.auth.models import User
In [3]: user = User.objects.create_user('jhon', 'test@test.com', 'johnpassword')


In [4]: user.username
Out[4]: 'jhon'

In [5]: user.password
Out[5]: '*****hased_password*****'

# 아래와 같이 해도 비밀번호가 변경되지만,
# 해시한 값이랑 다르기 때문에 나중에 로그인 할 수 없다.
user.password = '1234'
user.save()

# 패스워드 변경을 위해 set_password 메서드를 사용한다.
user.set_password('123')
```


## Authentication in Web requests
Django는 **request 객체를 이용해** 세션과 미들웨어를 사용하여 인증 시스템을 연결합니다.
