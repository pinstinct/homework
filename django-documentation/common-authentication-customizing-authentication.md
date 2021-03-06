# [Authentication: Customizing authentication](https://docs.djangoproject.com/en/1.10/topics/auth/customizing/)


## Other authentication sources
username, password 방식이 아닌 다른 방식으로 사용할 때, 확장하거나 대체할 수 있는 `Authentication backends` 제공한다.

### Specifying authentication backends

`django.contrib.auth.authenticate()`가 호출될 때
- 인증 시도
  - 먼저 기본(유전네임과 패스워드)사항을 확인
  - 그 다음 확장한게 있으면 그것까지 시도
- 인증에 실패하면 로그인 실패

기본적으로 `AUTHENTICATION_BACKENDS`는 다음과 같이 설정되있다.

```python
['django.contrib.auth.backends.ModelBackend']
```
순서가 중요하므로 처리하고싶은 순서대로 해둬라.

### Writing an authentication backend
인증 백엔드는 `get_user(user_id)`와 `authenticate(** credentials)`의 두 가지 필수 메소드가 있다.

- `get_user` : user_id를 취하고 사용자 객체를 반환
- `authenticate` : 자격 증명(credentials)을 키워드 인자로 사용

```python
class MyBackend(object):
    def authenticate(self, token=None):
        # Check the token and return a user.
        # 알아서 처리 후
        # 유효하면 반드시 사용자 객체를 반환하고
        # 아니면 None을 반환
```
`authenticate()`는 자격 증명이 유효한지 확인한 다음 자격 증명이 유효하면 해당 자격 증명과 일치하는 사용자 객체를 반환해야합니다. 유효하지 않으면 없음을 반환해야합니다.

## Extending the existing `User` model
기본 사용자 모델을 확장하는데 두 가지 방법이 있다.

- 프록시 모델 : 테이블은 그대로이고 동작만 새로 만들 때 (메서드가 필요할 때)
- OneToOneField : OneToOneField 필드를 사용해 추가 정보 모델을 만드는 법 (필드가 필요할 때)

## Substituting a custom `User` model
(사용자 모델을 대체하는 방법을 실습할 것)

세팅파일에 기본 유저 모델을 재정의해야 한다.
```python
AUTH_USER_MODEL = 'myapp.MyUser'
```

### Using a custom user model when starting a project
시작할 때 부터 하는 것을 강력히 추천한다.
```python
from django.contrib.auth.models import AbstractUser

class User(AbstractUser):
    pass
```
`AUTH_USER_MODEL`과 위의 설정을하고 마이그레이션을 진행한다.

### Referencing the `User` model
`AUTH_USER_MODEL` 설정이 변경된 경우, **User**를 직접 참조하면(예: 외래 키로 참조하는 경우) 코드가 작동하지 않는다.

- `get_user_model()` : User를 직접 참조하는 대신, ` django.contrib.auth.get_user_model()`을 사용한다. 이 메서드는 현재 활성화된 유저 모델을 리턴한다.

### Specifying a custom user model
장고는 커스텀 유저 모델이 최소한 몇 가지를 충족하기를 기대한다.

1. identification 목적을 위한 unique 필드 (기본값은 username)
2. 사용자를 "short"형식과 "long"형식으로 다룰수 있게 한다.

사용자 커스텀 모델을 생성하는 가장 쉬운 방법은 `AbstractBaseUser`를 상속하는 것이다.

#### class models.CustomUser
- USERNAME_FIELD
- REQUIRED_FIELDS
- is_active : 유저가 활성화되었는지 확인
    - 기본값은 True
    - 메일을 인증 후 가입하는 경우 사용
- get_full_name()
- get_short_name()

#### class models.AbstractBaseUser
- get_username()
- clean()
- is_authenticated : 사용자가 인증되었는지 여부
    - 항상 True인 읽기 전용 특성
    - AnonymousUser.is_authenticated는 항상 False
- is_anonymous
- set_password(raw_password)
- check_password(raw_password)
- set_unusable_password()
- has_usable_password()
- get_session_auth_hash()

커스텀 유저 모델을 위한 `UserManager`를 정의해야 한다. `BaseUserManager`를 확장해서 사용한다.

장고의 기본 유저모델을 가져와서 커스텀 유저 모델을 만들면 매니저를 상속받거나 오버라이드해서 사용할 수 있다.

#### class models.CustomUserManager

- `create_user(*username_field*, password=None, **other_fields)`
- `create_superuser(*username_field*, password, **other_fields)`

#### class models.BaseUserManager
- `classmethod normalize_email(email)`
- `get_by_natural_key(username)`
- `make_random_password(length=10, allowed_chars='abcdefghjkmnpqrstuvwxyzABCDEFGHJKLMNPQRSTUVWXYZ23456789')` : 패스워드 입력이 필요없는 서비스를 제공할 때 사용
