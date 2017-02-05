## 관리자 폼 커스터마이징

Question 모델을 admin.site.register(Question)에 등록함으로써, Django는 디폴트 폼 표현을 구성 할 수있었습니다. 관리 폼이 보이고 작동하는 방법을 커스터마이징하려는 경우가 있습니다. 객체를 등록 할 때 Django에 원하는 옵션을 알려주면 커스터마이징 할 수 있습니다.

수정 폼의 필드를 재정렬하여 이것이 작동하는법을 보겠습니다. admin.site.register(Question) 줄을 다음과 같이 바꾸세요.

```python
# polls/admin.py
from django.contrib import admin

from .models import Question


class QuestionAdmin(admin.ModelAdmin):
    fields = ['pub_date', 'question_text']

admin.site.register(Question, QuestionAdmin)
```

모델의 관리자 옵션을 변경해야 할 때마다 이 패턴 – 모델 어드민 클래스를 만든 다음, `admin.site.register()`에 두 번째 인수로 전달합니다 – 을 따라하면 됩니다.

브라우저에서 http://127.0.0.1:8000/admin/polls/question/1/change/ 확인하면, Date published 필드와 Question text필드의 위치가 바뀌었습니다.

수십 개의 필드가 있는 폼에 관해서는 폼을 fieldset으로 분할하는 것이 좋습니다.

```python
# polls/admin.py
from django.contrib import admin

from .models import Question


class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date']}),
    ]

admin.site.register(Question, QuestionAdmin)
```



## 관련된 객체 추가

Question 관리자 페이지를 가지고 있습니다. 그러나, `Question`은 여러 개의 `Choice`들을 가지고 있음에도, admin 페이지는 Choice를 표시하지 않습니다.

이 문제를 해결하는 데는 두 가지 방법이 있습니다. 첫 번째는 `Question`에서 했던 것처럼 관리자에 `Choice`를 등록하는 것입니다. 

```python
# polls/admin.py
from django.contrib import admin

from .models import Choice, Question
# ...
admin.site.register(Choice)
```

브라우저에서 http://127.0.0.1:8000/admin/polls/choice/3/change/ 확인합니다. 이 양식에서 “Question” 필드는 데이터베이스의 모든 질문을 포함하는 select box입니다. Django는 [`ForeignKey`](http://django-document-korean.readthedocs.io/ko/latest/ref/models/fields.html#django.db.models.ForeignKey)가 admin에서 `<select>`로 표현되어야 함을 알고 있습니다. 또한 “Question” 옆의 “Add Another” 링크를 주목하세요. `ForeignKey` 관계를 가진 모든 객체는 저 링크가 붙습니다. 

그러나 실제로 이것은 `Choice` 객체를 시스템에 추가하는 비효율적 인 방법입니다. `Question` 객체를 생성 할 때 여러개의 Choices를 직접 추가 할 수 있다면 더 좋을 것입니다. 그것을 만들어 봅시다.

`Choice` 모델에 대한 `register()` 호출을 제거하십시오. 그런 다음 `Question` 등록 코드를 다음과 같이 편집하십시오.

```python
# polls/admin.py
from django.contrib import admin

from .models import Choice, Question


class ChoiceInline(admin.StackedInline):
    model = Choice
    extra = 3


class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
    ]
    inlines = [ChoiceInline]

admin.site.register(Question, QuestionAdmin)
```

위 소소는 Django에게 "Choice 객체는 Question 관리자 페이지에서 편집된다. 기본으로 3가지 선택 항목을 제공함."이라고 알려줍니다. 브라우저에서 http://127.0.0.1:8000/admin/polls/question/add/ 접속해 확인합니다.

하나의 작은 문제가 있습니다. 관련 `Choice` 객체를 입력하기위한 모든 필드를 표시하는 데는 많은 화면 공간이 필요합니다. 이런 이유로 Django는 인라인 관련 객체를 표시하는 표 형식의 방법을 제공합니다. 다음과 같이 `ChoiceInline` 선언을 변경하면됩니다.

```python
# polls/admin.py
class ChoiceInline(admin.TabularInline):
    #...
```

`StackedInline` 대신에 `TabularInline`을 사용하면, 관련된 객체는 좀 더 조밀하고 테이블 기반 형식으로 표시됩니다. 



## 관리자 변경 목록(change list) 커스터마이징

이제 Question 관리 페이지가 괜찮아 보이므로, 시스템의 모든 질문을 표시하는 “변경 목록” 페이지를 약간 조정하십시오. 기본적으로 Django는 각 객체의 `str()`을 표시합니다. `list_display` admin 옵션을 사용해 객체의 "변경 목록" 페이지에서 튜플 필드이름을 열로 표시합니다.

```python
# polls/admin.py
class QuestionAdmin(admin.ModelAdmin):
    # ...
    list_display = ('question_text', 'pub_date')
```

`was_published_recently()`를 추가해봅시다.

```python
class QuestionAdmin(admin.ModelAdmin):
    # ...
    list_display = ('question_text', 'pub_date', 'was_published_recently')
```

`was_published_recently` 헤더의 경우를 제외하고 그 값으로 정렬하기 위해 열 머리글을 클릭 할 수 있습니다. 왜냐하면 임의의 메서드의 출력에 의한 정렬은 지원되지 않기 때문입니다. 또한 `was_published_recently`에 대한 열 머리글은 기본적으로 메서드 이름 (밑줄을 공백으로 대체)이며 각 줄에는 출력의 문자열 표현이 포함되어 있습니다.

다음과 같이 해당 메소드 (`polls/models.py`)에 몇 가지 속성을 부여하여 향상시킬 수 있습니다.

```python
# polls/models.py
class Question(models.Model):
    # ...
    def was_published_recently(self):
        now = timezone.now()
        return now - datetime.timedelta(days=1) <= self.pub_date <= now
    was_published_recently.admin_order_field = 'pub_date'
    was_published_recently.boolean = True
    was_published_recently.short_description = 'Published recently?'
```

`polls/admin.py` 파일을 열어 `QuestionAdmin`에 다음 줄을 추가하십시오.

```python
list_filter = ['pub_date']
```

http://127.0.0.1:8000/admin/polls/question/ 페이지에 "Filter" 사이드 바가 추가되었습니다. 표시되는 필터의 유형은 필터링중인 필드의 유형에 따라 다릅니다. `pub_date`는 [`DateTimeField`](http://django-document-korean.readthedocs.io/ko/latest/ref/models/fields.html#django.db.models.DateTimeField)이므로, Django는 “Any date”, “Today”, “Past 7 days”, “This month”, “This year” 등의 적절한 필터 옵션을 제공합니다.

이제 검색 기능을 추가해 보겠습니다.

```python
search_fields = ['question_text']
```

누군가가 검색어를 입력하면, 장고는 question_text 필드를 검색합니다. 당신이 원하는만큼의 필드를 사용할 수 있습니다 – 내부적으로 LIKE 쿼리를 사용하기 때문에 검색 필드의 수를 적당한 수로 제한하면 데이터베이스가 검색을 더 쉽게 할 수 있습니다.

이제 변경 목록이 자동 페이징 기능을 제공한다는 점을 기억하십시오. 기본값은 페이지 당 100 개의 항목을 표시하는 것입니다. 변경 목록 페이지내이션, 검색 상자, 필터, 날짜-계층구조, 그리고 컬럼-헤더-정렬 모두 함께 작동합니다.

## 관리자 커스터마이징

모든 관리자 페이지 상단에 "Django administration"이 있다는 것은 우스꽝스럽습니다. 이건 그저 자리를 채워넣기 위한 문자열입니다. Django의 템플릿 시스템을 사용하면 쉽게 변경할 수 있습니다. Django 관리자는 Django 자체에 의해 구동되며 인터페이스는 Django 자신의 템플릿 시스템을 사용합니다.

### 프로젝트 템플릿 커스터마이징

프로젝트 디렉토리 (`manage.py`를 포함하고있는)에 `templates` 디렉토리를 만듭니다. 템플릿은 장고가 액세스 할 수있는 파일 시스템 어디에서나 사용할 수 있습니다. 

설정 파일 (`mysite/settings.py`)을 열고 `DIRS` 옵션을 `TEMPLATES` 설정에 추가하십시오.

```python
# mysite/settings.py
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

`DIRS`는 Django 템플릿을 로드 할 때 검사 할 파일 시스템 디렉토리 목록입니다. 바로 검색 경로입니다.

#### 템플릿 구성

정적 파일과 마찬가지로, 하나의 커다란 템플릿 디렉토리에 모든 템플릿을 함께 넣을 수 있습니다. 그렇게해도 완벽하게 잘 작동할것입니다. 그러나 특정 애플리케이션에 속한 템플릿은 프로젝트(templates)가 아닌 해당 애플리케이션의 템플릿 디렉토리 (예: polls/templates)에 있어야합니다. 

이제 templates 디렉토리에 admin이라는 디렉토리를 만들고 장고 자체 소스 코드 (django/contrib/admin/templates)을 해당 디렉토리에 복사합니다.



