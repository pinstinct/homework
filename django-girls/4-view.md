# Django 뷰 만들기

뷰(*view*) 는 어플리케이션의 **"로직"을 넣는 곳**이에요. 뷰는 앞 챕터에서 만들었던 **`모델`에게서 필요한 정보를 받아와서 `템플릿`에 전달하는 역할을 합니다.** 

```python
# blog/views.py
from django.shortcuts import render


def post_list(request):
    # request를 넘겨받아 render 메서드 호출
    # render 메서드는 넘겨진 요청(request)과 blog/post_list.html 템플릿 받아 
    # 리턴된 내용을 브라우저에 보여줌
    return render(request, 'blog/post_list.html', {})
```

