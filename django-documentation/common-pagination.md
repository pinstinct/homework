# [Pagination](https://docs.djangoproject.com/en/1.10/topics/pagination/)

장고에서는 "Previous"/"Next" 링크를 제공하는 pagination 기능을 제공한다. 이 클래스는 `django/core/paginator.py`에 있다.

```shell
# 2는 한 페이지에 나타낼 개수
>>> p = Paginator(objects, 2)
```

```python
from django.core.paginator import Paginator, EmptyPage, PageNotAnInteger
from django.shortcuts import render

def listing(request):
    contact_list = Contacts.objects.all()
    # 한 페이지에 25개 노출
    paginator = Paginator(contact_list, 25)

    page = request.GET.get('page')
    try:
        # 현재 페이지 number와 앞,뒤 페이지 정보를 가지고 있다.
        contacts = paginator.page(page)
    except PageNotAnInteger:
        # page가 integer가 아니거나 없을 경우에는 첫 번째 페이지로 이동
        contacts = paginator.page(1)
    except EmptyPage:
        # 범위를 넘는 큰 수를 입력 할 경우(예: 9999), 마지막 페이지로 이동
        contacts = paginator.page(paginator.num_pages)

    return render(request, 'list.html', {'contacts': contacts})
```
