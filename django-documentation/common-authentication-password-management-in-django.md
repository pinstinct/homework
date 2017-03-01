# [Authentication: Password management in Django](https://docs.djangoproject.com/en/1.10/topics/auth/passwords/)

## Password validation
사용자는 종종 빈약 한 암호를 선택합니다. 이 문제를 완화하기 위해 Django는 플러그형 암호 검증을 제공합니다.

유효성 검사는 `AUTH_PASSWORD_VALIDATORS` 설정에 의해 제어됩니다.

기본적으로 유효성 검사기는 암호를 재설정하거나 변경하는 폼 및 `createsuperuser` 및 `changepassword` 커맨드에서 사용됩니다.

### Integrating validation

```python
AUTH_PASSWORD_VALIDATORS = [
    {
        # 다른 속성들과 동일한지 검사
        'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
    },
    {
        # 비밀번호 최소 길이를 충족하는지 검사
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
    },
    {
        # 흔한 암호목록을 사용하는지 검사
        'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
    },
    {
        # 숫자로만 이루어졌는지 검사
        'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
    },
]
```

### Integrating validation
`django.contrib.auth.password_validation`에는 생성한 폼이나 다른 코드에서 암호 검증을 연동하기 위한 몇 가지 기능이 있습니다.

- validate_password : 암호의 유효성을 검사
