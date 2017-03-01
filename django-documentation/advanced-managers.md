# [Advanced: Managers](https://docs.djangoproject.com/en/1.10/topics/db/managers/)

- Manager는 Django 모델에 데이터베이스 쿼리작업을 제공
- Django 응용프로그램은 최소한 하나 이상의 Manager 존재
- 기본적으로 Django는 모든 Django 모델 클래스에 objects라는 이름의 Manager를 추가

#### Custom Managers

커스텀 Manager를 사용하려는 두 가지 이유가 있습니다.

- Mansager 메서드 추가
- Manager가 반환하는 초기(initial) **QuerySet**을 수정
  - Manager의 기본 QuerySet은 모든 객체(all objects)를 반환 (예: `Post.objects.all()`)
  - `Manager.get_queryset()` 메서드를 재정의하여 기본 QuerySet 변경 가능
