# [File Uploads](https://docs.djangoproject.com/en/1.10/topics/http/file-uploads/)


## Basic file uploads

- view에서 파일 데이터는 `request.FILES`에서 처리할 수 있다.
- `request.FILES`는 폼의 각 **FileField** (or **ImageField**, or other FileField subclass)에 대한 키를 포함하는 딕셔너리 타입이다.
- **주의** :  요청 메소드가 **POST**이고, ` <form>`이 enctype="multipart/form-data" 속성을 갖는 경우에만 데이터를 포함한다. 그렇지 않으면 `request.FILES`가 비어있다.

```python
def post_add(request):
    if request.method == 'POST':
        # 폼의 생성자에 request.FILES 전달
        form = PostForm(request.POST, request.FILES)
        if form.is_valid():
            post = Post(
                author=request.user,
                content=form.cleaned_data['content'],
                # 파일은 cleaned_data에 들어가지 않음
                photo=request.FILES['photo']
            )
            post.save()
            return redirect('post:list')
    else:
        form = PostForm()
    return render(request, 'post/post_add.html', {'form': form})
```

- `request.FILES`를 폼의 생성자에 전달해야 한다. 이것은 파일 데이터가 폼에 바인딩되는 방법이다.
- 파일을 **UploadedFile** 감싸서 장고가 알아서 chunks 하는 기능을 제공한다.
- **FileField**가 있는 모델에 파일을 저장하는 경우 **ModelForm**을 사용하면이 프 훨씬 쉽게 사용할 수 있다. 파일 객체는 form.save ()를 호출 할 때 해당 FileField의 upload_to 인수로 지정된 위치에 저장됩니다.

```python
from django.http import HttpResponseRedirect
from django.shortcuts import render
from .forms import ModelFormWithFileField

def upload_file(request):
    if request.method == 'POST':
        form = ModelFormWithFileField(request.POST, request.FILES)
        if form.is_valid():
            # 간단하게 파일을 저장할 수 있다.
            form.save()
            return HttpResponseRedirect('/success/url/')
    else:
        form = ModelFormWithFileField()
    return render(request, 'upload.html', {'form': form})
```

- 여러개의 파일을 업로드할 수 있다. 결과가 리스트로 반환되므로, 반복문을 통해 꺼내 쓰면된다.

### Uploading multiple files
필드 위젯 속성에 `multiple`을 설정한다.

```python
# post/forms.py
class PostForm(forms.Form):
    content = forms.CharField(required=False)
    photo = forms.ImageField(
        widget=forms.ClearableFileInput(
            attrs={
                'multiple': True
            }
        )
    )
```

```python
# post/views/post.py
def post_add(request):
    if request.method == 'POST':
        form = PostForm(request.POST, request.FILES)
        if form.is_valid():
            files = request.FILES.getlist('photo')

            for file in files:
                create_post_comment(file, comment_content)

            return redirect('post:list')
```


## Upload Handlers

-  사용자가 파일을 업로드하면,  필드 데이터를 **upload handler**에게 전달한다.
- **upload handler**는 설정의  `FILE_UPLOAD_HANDLERS` 에 정의되며, 기본값은 다음과 같다.

```python
[
	# 파일 크기(2.5 MB 미만)가 작을 때,
    'django.core.files.uploadhandler.MemoryFileUploadHandler',

    # 파일 크기가 클 때, 임시로 파일을 디스크에서 쓰다가 사용하지 않으면 지운다.
    'django.core.files.uploadhandler.TemporaryFileUploadHandler',
]
```
