# 템플릿의 동적 데이터

블로그 글이 각각 다른 장소에 조각조각 나눠져있어요. `Post` 모델은 `models.py`파일에 정의되어 있고, `post_list` 모델은 `views.py` 파일에 정의되어 있습니다. 템플릿도 추가해야해요. 하지만 실제로 HTML 템플릿에서 글들이 어떻게 보여질까요? **보여주고자 하는 컨텐츠(데이터베이스 안에 저장되어 있는 모델들) 를 가져와서 우리의 템플릿에 넣어서 멋있게 보여줄 것입니다.**

```python
# blog/views.py
from .models import Post
```

`from` 다음에 있는 마침표(.)는 *현재 디렉토리* 또는* 현재 어플리케이션*을 의미합니다. `views.py`와 `models.py` 파일들이 같은 디렉토리에 있기 때문에 우리는 `.`와 (`.py` 확장자를 붙이지 않아도) 파일의 이름만으로도 쉽게 사용할 수 있습니다. 자 다음은 모델(`Post`)의 이름을 불러옵니다.).

그럼 그 다음은요? `Post` 모델에서 실제 블로그 글들을 가져와야 하는데 이때 필요한 특별한 것이 바로 `QuerySet`입니다.

## 쿼리셋 (QuerySet) 추가

```python
from django.shortcuts import render
from django.utils import timezone
from .models import Post


def post_list(request):
    # 쿼리셋
    posts = Post.objects.filter(published_date__lte=timezone.now()).order_by('published_date')

    return render(request, 'blog/post_list.html', {'posts': posts})

```



## Django 템플릿

이제 데이터를 보여줄 차례에요! 이를 위해 장고는 내장된 **template tags** 라는 유용한 기능을 제공합니다. 

HTML에 여러분은 파이썬 코드를 바로 넣을 수 없어요. 브라우져는 파이썬 코드를 이해할 수 없기 때문이에요요 브라우저는 HTML만을 알고 있어요. 알다시피 HTML는 정적이지만, 파이썬은 동적입니다.

**장고 템플릿 태그(Django template tags)** 는 파이썬을 HTML로 바꿔주어, 빠르고 쉽게 동적인 웹사이트를 만들 수 있게 도와주어요. 

장고 템플릿 안에 있는 값을 출력하려면, 변수 이름안에 중괄호를 넣어 표시해야합니다. `django_app/templates/blog/post_list.html`으로 이동해 작성해봅시다.

```html
<html>
<head>
    <title>Django Girls blog</title>
</head>
<body>
<div>
    <h1><a href="/">Django Girls Blog</a></h1>
</div>

{% for post in posts %}
<div>
    <p>published: {{ post.published_date }}</p>
    <h1><a href="">{{ post.title }}</a></h1>
    <p>{{ post.text|linebreaksbr }}</p>
</div>
{% endfor %}
</body>
</html>
```

`{{ post.title }}`라던가 `{{ post.text }}` 같이 이전과 다른 표기법을 사용한 것을 눈치 채셨나요? `Post` 모델에서 정의한 각 필드의 데이터에 접근하기 위해 이 표기법을 사용합니다. 또한 `|linebreaksbr` 같이 파이프 문자(|)도 사용해요. 이건 블로그 글 텍스트에서 행이 바뀌면 문단으로 변환하도록 하라는 의미입니다. 행바뀜을 문단으로 변환하는 필터를 적용한다는 표현을 쓰기도 합니다.