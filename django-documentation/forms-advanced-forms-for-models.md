# [Advanced: Forms for models](https://docs.djangoproject.com/en/1.10/topics/forms/modelforms/)

# ModelForm
데이터베이스 기반 앱을 만드는 경우 Django 모델과 밀접하게 매핑되는 폼을 사용할 수 있습니다.

```python
>>> from django.forms import ModelForm
>>> from myapp.models import Article

# Create the form class.
>>> class ArticleForm(ModelForm):
...     class Meta:
...         model = Article
...         fields = ['pub_date', 'headline', 'content', 'reporter']
```

### Field types
각 모델 필드에는 해당 기본 폼 필드가 있습니다.

`ForeignKey` 및 `ManyToManyField` 모델 필드 유형은 특별한 경우입니다.

- ForeignKey는 `django.forms.ModelChoiceField`로 표현
- ManyToManyField는 `django.forms.ModelMultipleChoiceField`로 표현
- 모델 필드가 `blank = True`면, 폼 필드에서 `required = False`로 설정
- 필드의 **label**은 `verbose_name`으로 설정

### Validation on a ModelForm
- 유효성 검사도 폼 안에서 할 수 있다.
- `clean_<fieldname>()` 메서드는 폼의 서브 클래스이다.

```python
# member/forms.py
class SignupForm(forms.Form):
    username = forms.CharField(max_length=30)
    password1 = forms.CharField(widget=forms.PasswordInput)
    password2 = forms.CharField(widget=forms.PasswordInput)
    # ...

    def clean_username(self):
        username = self.cleaned_data['username']
        if MyUser.objects.filter(username=username).exists():
            raise forms.ValidationError('username already exists!')
        return username
```

### The save() method

- 폼에 바인딩 된 데이터로 부터 데이터베이스 객체를 만들고 저장한다.
- **ModelForm**의 subclass는 모델의 인스턴스를 `instance` 키워드 인자로 받을 수 있다.
  - `instance`가 있으면, **save()**는 인스턴스를 update
  - `instance`가 없으면, **save()**는 인스턴스 create

```shell
# Create a form instance with POST data.
>>> f = AuthorForm(request.POST)

# Create, but don't save the new author instance.
>>> new_author = f.save(commit=False)

# Modify the author in some way.
>>> new_author.some_field = 'some_value'

# 객체 생성
# Save the new instance.
>>> new_author.save()

# Now, save the many-to-many data for the form.
>>> f.save_m2m()

# Create a form instance with POST data.
>>> a = Author()

# instance는 기존데이터, request.POST는 새로운 데이터
>>> f = AuthorForm(request.POST, instance=a)

# Create and save the new author instance. There's no need to do anything else.
>>> new_author = f.save()
```



```shell
# Post.objetcts.create()
# p = Post()
# p.save()
# 위의 두 가지  방법과 비슷한 개념

# Create a form instance with POST data.
>>> f = AuthorForm(request.POST)

# 객체가 만들어져서 메모리상에 있는 상태
# Create, but don't save the new author instance.
>>> new_author = f.save(commit=False)

# Modify the author in some way.
>>> new_author.some_field = 'some_value'

# Save the new instance.
>>> new_author.save()

# Now, save the many-to-many data for the form.
>>> f.save_m2m()
```

Model fromsets은 잘 안쓴다. formsets을 쓸 정도가 되면 fontend에서 처리한다.
