# [Authentication: Overview ](https://docs.djangoproject.com/en/1.10/topics/auth/)


cookie-based user sessions (쿠키기반 사용자 세션)

- 유저가 새로고침을 할 때, 연결의 연속성이 없는데,
- 그것을 연결해주는 것이 세션
- 세션을 유지하게 해주는 것이 쿠키
- 유저는 고유한 쿠키 값을 가진다.
- 쿠키를 가지고 서버는 세션을 유지한다.

## Overview

인증(authentication)과 권한(authorization)

auth system

- Users
- Permissions
- Groups
- A configurable password hashing system (패스워드 해싱시스템) : 해시를 했을때, 다시 평문으로 복원되면 안된다.
- Forms and view tools for logging in users, or restricting content
- pluggable backend system : 3rd 로그인방식, id/pwd 방식과 같이 여러가지 방식을 갈아끼워 사용할 수 있다.

third-party package(로 제공)

- Password strength checking
- Throttling of login attempts : 로그인 시도 제한
- Authentication against third-parties (OAuth, for example)

## Installation
MIDDLEWARE : request와 response사이에서 일어나는 과정을 미들웨어라고 한다. 모든 request에 대한 로그를 남고기 싶을 때, 미들웨어를 사용하면 된다.
