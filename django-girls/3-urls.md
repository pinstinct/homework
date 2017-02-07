# Django urls

## URL이란?

인터넷에 있는 모든 페이지들은 자신만의 URL을 가지고 있어야 해요. 이런 방식으로 어플리케이션은 URL을 입력한 사용자에게 어떤 내용을 보여줘야 할지 알게 됩니다. 장고는 `URLconf` (URL configuration)를 사용합니다. **URLconf는 장고에서 URL과 일치하는 뷰를 찾기 위한 패턴들의 집합**입니다.

## Django URL 작동법

```python
# mysite/urls.py
from django.conf.urls import url
from django.contrib import admin

urlpatterns = [    
    # admin/ 으로 시작하는 모든 URL을 view와 대조해 찾아냄
    url(r'^admin/', admin.site.urls),
]
```

## 정규표현식 (Regex)

장고가 URL을 뷰에 매칭시키는 방법이 궁금하죠? 이 부분은 조금 까다로울 수 있어요. 장고는 `regex`를 사용하는데, "정규표현식(regular expressions)"의 줄임말입니다.

패턴을 만드는 방법이 궁금하다면, 아래에 있는 표기법을 확인하세요. 우리는 패턴을 찾는데 필요한 몇 가지 규칙만 필요합니다. 

```
^ : 문자열이 시작할 때
$ : 문자열이 끝날 때
\d : 숫자
+ : 바로 앞에 나오는 항목이 계속 나올 때
() : 패턴의 부분을 저장할 때
```

예를 들어 `http://www.mysite.com/post/12345/` 여기에서 `12345`는 글 번호를 의미합니다.

뷰마다 모든 글 번호을 작성하는 것은 정말 힘든 일이 될 거에요. 정규 표현식으로 url과 매칭되는 글 번호를 뽑을 수 있는 패턴을 만들 수 있어요. `^post/(\d+)/$`. 어떤 뜻인지 하나씩 나누어 어떤 뜻인지 알아볼게요. 

- **^post/**는 장고에게 url 시작점에 (오른쪽부터) `post/`가 있다는 것을 말해 줍니다. 
- **(\d+)**는 숫자(한 개 또는 여러개) 가 있다는 뜻입니다. 내가 뽑아내고자 글 번호가 되겠지요.
- **/**는 장고에게 `/`뒤에 문자가 있음을 말해 줍니다.
- **$**는 URL의 끝이 방금 전에 있던 `/`로 끝나야 매칭될 수 있다는 것을 나타냅니다.

## Dajngo url 연결

```python
# mysite/urls.py
from django.conf.urls import url, include
from django.contrib import admin

urlpatterns = [
    url(r'^admin/', admin.site.urls),

    # http://127.0.0.1:8000/ 로 들어오는 모든 접속요청을 blog.urls로 전송
    url(r'', include('blog.urls')),
]

```

`blog/urls.py`이라는 새 파일을 생성하세요. 

```python
# blog/urls.py
from django.conf.urls import url
from . import views

urlpatterns = [
    # post_list 뷰를 ^$ URL에 할당
    # name='post_list는 URL에 이름 할당
    url(r'^$', views.post_list, name='post_list'),
]
```

패턴은 장고에게 누군가 웹사이트에 '[http://127.0.0.1:8000/](http://127.0.0.1:8000/)' 주소로 들어왔을 때`views.post_list`를 보여주라고 말할 거에요.

마지막 부분인 `name='post_list'` 는 URL에 이름을 붙인 것으로 **뷰를 식별**합니다. 이 부분은 뷰의 이름과 같을 수도 완전히 다를 수도 있습니다. 

모두 잘 되고 있나요? '[http://127.0.0.1:8000/](http://127.0.0.1:8000/)' 으로 접속해 결과를 확인해보세요.