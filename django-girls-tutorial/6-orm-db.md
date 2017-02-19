# Django ORM과 QuerySets

이번 장에서는 장고를 데이터베이스에 연결, 데이터를 저장하는 방법에 대해서 알아볼 거에요. 

## 쿼리셋(QuerySet)이란?

쿼리셋은 전달받은 모델의 객체 목록입니다. 쿼리셋은 데이터베이스로부터 데이터를 읽고, 필터를 걸거나 정렬을 할 수 있습니다.

## 장고 shell

```shell
$ ./manage.py shell
(InteractiveConsole)
>>> from blog.models import Post

# Post 모델의 모든 객체 목록
>>> Post.objects.all()
<QuerySet [<Post: first post>]>

# User 모델
>>> from django.contrib.auth.models import User
>>> User.objects.all()
<QuerySet [<User: hm>]>

# User 모델의 인스턴스 가져오기
>>> me = User.objects.get(username='hm')

# 새로운 Post 객체 생성
Post.objects.create(author=me, title='Sample title', text='Test')
<Post: Sample title>

>>> Post.objects.all()
<QuerySet [<Post: first post>, <Post: Sample title>]>

# - 
# 필터링하기
>>> Post.objects.filter(author=me)
<QuerySet [<Post: first post>, <Post: Sample title>]>

#  밑줄(_)이 2개 : 장고 ORM은 필드 이름("title")과 연산자과 필터("contains")를 밑줄 2개를 사용해 구분
>>> Post.objects.filter(title__contains='title')
<QuerySet [<Post: Sample title>]>

>>> post = Post.objects.get(title='Sample title')
>>> post.publish()

# - 
# 정렬하기
>>> from django.utils import timezone

# 오름차순 정렬
>>> Post.objects.order_by('created_date')
<QuerySet [<Post: first post>, <Post: Sample title>]>

# 내림차순 정렬
>>> Post.objects.order_by('-created_date')
<QuerySet [<Post: Sample title>, <Post: first post>]>


# - 
# 쿼리셋 연결하기
>>> Post.objects.filter(published_date__lte=timezone.now()).order_by('published_date')
<QuerySet [<Post: first post>, <Post: Sample title>]>
```



