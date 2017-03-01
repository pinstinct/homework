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


  ```python
  # post/models/post.py
  # 커스텀 Manager를 설정하는 방법에 두 가지 방식이 있다.

  # 1. manager에 메서드 추가
  class PostManager(models.Manager):
      def visible(self):
          return super().get_queryset().filter(is_visible=True)

  # 2. get_queryset을 오버라이드
  class PostUserVisibleManager(models.Manager):
      def get_queryset(self):
          return super().get_queryset().filter(is_visible=True)

  class Post(models.Model):
      # Default 모델 Manager 교체
      objects = PostManager()
      # Post.obejcts.all() : 모든 객체 반환
      # Post.objecst.visible() : 필터된 객체 반환

      # 커스텀 모델 매니저 추가
      visible = PostUserVisibleManager()
      # Post.visibles.all()
  ```

  ```python
  # 뷰에서 사용할 때
  # post/views/post.py

  def post_list(request):
      # posts = Post.objects.filter(is_visible=True)
      posts = Post.visible.all()
      context = {
          'posts': posts
      }
      return render(request, 'post/post_list.html', context)
  ```
