# [Basics: Overview](https://docs.djangoproject.com/en/1.10/topics/forms/)


일방적으로 보여주고 끝나는 페이지가 아니라면 폼을 이해하고 사용해야 한다.

## HTML forms
HTML에서 form은 `<form> ... </ form>` 내에 있는 요소 모음이다. 사용자는 폼 안에 있는 텍스트 입력, 옵션 선택 등의 동작을 수행하고, 그 데이터들은 서버로 다시 보내진다.

몇 가지 폼 요소 인터페이스는 자바스크립트와 CSS로 구현해두었다.

폼에는 다음의 두 가지를 반드시 지정해야 한다.
- where : 사용자가 입력한 데이터가 반환될 **URL**
- how : **HTTP 메소드**

폼 데이터는 `<forms>`의 `action` 속성에 지정된 URL로 보내진다.


### GET and POST

폼에서 다룰수 있는 메소드는 `GET`과 `POST` 뿐이다.

`POST` 메서드는 브라우저가 인코딩한 폼 데이터를 묶어서 서버로 전송한다.

`GET` 메서드는 데이터를 문자열로 묶어 **URL**에 구성하는데 사용한다. **URL**에는 데이터를 전송해야하는 주소는 물론 **데이터 키와 값이 포함**됩니다.

시스템의 상태를 변경하는 데 사용할 수있는 요청(예: 데이터베이스 변경 요청)은 `POST`를 사용한다. `GET`은 시스템 상태에 영향을주지 않는 요청에만 사용한다.
- `GET` : 조회(읽기전용)
- `POST` : 삭제, 수정, 생성



## Django’s role in forms
굉장히 복잡한 작업이다. 그렇지만 우리가 이걸 만들었다.


## Forms in Django
웹 어플리케이션의 context에서 'form'은
- `HTML <form>`이거나
- 장고의 `Form` 클래스이거나
- 데이터구조에서 제출된 것일 수도있고
- 전송되는 모든 과정을 의미 할 수 있다.


### The Django `Form`
장고의 모델과 폼 클래스는 긴밀하게 연결되어 작업을 수행한다.

- 폼 클래스의 **필드**는 HTML 폼 `<input>` 요소에 매핑
- 폼의 필드 자체는 **클래스** (DateField, FileField)
- 폼이 제출 될 때, 폼 데이터를 관리하고 유효성 검사를 수행
- widget : 체크박스, 라디오버튼, 텍스트 필드, ...



## Building a form

### Building a form in Django

#### The `Form` class

```python
from django import forms

class NameForm(forms.Form):
    your_name = forms.CharField(label='Your name', max_length=100)
```
`Form` 인스턴스에는 모든 필드에 대한 유효성 검사 루틴을 실행하는 `is_valid()` 메서드가 있다. 모든 필드에 유효한 데이터(검증된 데이터)가 포함되어 있으면 다음을 수행한다.

- return **True**
- 폼의 데이터를 `cleaned_data` 속성에 저장


#### The template

```html
<form action="/your-name/" method="post">
    {% csrf_token %}
    {{ form }}
    <input type="submit" value="Submit" />
</form>
```


#### The view
```python
from django.shortcuts import render
from django.http import HttpResponseRedirect

from .forms import NameForm

def get_name(request):
    if request.method == 'POST':
        # 폼 인스턴스 생성
        # request의 데이터로 채움 (바인딩)
        # request.POST안에서 your_name을 찾음
        form = NameForm(request.POST)
        # check whether it's valid:
        if form.is_valid():
            # ...
            # redirect to a new URL:
            return HttpResponseRedirect('/thanks/')

    # 빈 폼 생성
    else:
        form = NameForm()

    return render(request, 'name.html', {'form': form})
```



## More about Django `Form` classes
모든 폼 클래스는 `django.forms.Form`의 서브 클래스이다. (`ModelForm` 포함)


### Bound and unbound form instances
폼 인스턴스에 연결된 데이터가 있는지 없는지 구별하는 것

### More on fields

#### Widgets
각각의 폼 필드에 해당하는 **Widget** 클래스가 있다.

#### Field data
폼과 함께 제출 된 데이터가 무엇이든간에 `is_valid()`를 호출하여 유효성을 검사하고 유효성이 검사 된 폼 데이터는 `form.cleaned_data` **딕셔너리**에 저장된다.

**주의** : `request.POST` 객체를 사용할 수도 있지만, 검증된 데이터(`cleaned_data`)를 사용하는 것이 좋다.

## Working with form templates

### Form rendering options
폼의 결과는 `<form>` 태그로 둘러쌓여있지 않다. 또한 폼의 submit 버튼도 없다. 작성하는 것을 잊지 마라.


각 폼 필드에는 `id_<field-name>`으로 설정된 ID 속성이 있다. 함께 제공되는 `label` 태그에 의해 참조됩니다.


### Rendering fields manually
원한다면 수동으로 처리 할 수 ​​있다. 각 필드는 `{{form.name_of_field}}`를 사용하여 폼의 속성으로 사용할 수 있다. (예: 부트스트랩 사용할 때)


### Looping over the form’s fields
loop를 사용할 수 있다.
