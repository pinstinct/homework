## 데이터베이스 설치

`mysite/settings.py` 파일을 열어보세요. 이 파일은 Django 설정을 모듈 변수로 표현한 보통의 Python 모듈입니다.

기본적으로는 SQLite 을 사용합니다. SQLite 는 Python 에서 기본으로 제공되기 때문에 별도로 설치할 필요가 없습니다. SQLite 를 사용한다면, 아무것도 미리 생성할 필요가 없습니다. 데이터베이스 파일은 필요할 때마다 자동으로 생성됩니다.

```python
# mysite/settings.py
# 시간대 설정
TIME_ZONE = 'Asia/Seoul'

# 기본적으로 INSTALLED_APPS의 어플리케이션들을 포함
INSTALLED_APPS = [
    'django.contrib.admin',  
    'django.contrib.auth',  
    'django.contrib.contenttypes',  
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

기본 어플리케이션들 중 몇몇은 최소한 하나 이상의 데이터베이스 테이블을 사용하는데, 그러기 위해서는 데이터베이스에서 테이블을 미리 만들 필요가 있습니다. 이를 위해, 다음의 명령을 실행해봅시다.

```shell
$ python manage.py migrate
```

 기본으로 제공되는 어플리케이션은 일반적인 상황을 염두에 두었으나, 모두에게 필요한것은 아닙니다. 만약 이것들이 필요 없다고 생각되면, `migrate`를 실행하기 전에 `INSTALLED_APPS`에서 제거할 어플리케이션들을 그냥 주석처리(comment-out) 하거나 삭제하면 됩니다. `migrate`명령은 `INSTALLED_APPS`에 등록된 어플리케이션에 한하여 실행합니다.

## 모델 만들기

이제, 모델을 정의해 보겠습니다. 본질적으로, 모델이란 부가적인 메타데이터를 가진 데이터베이스의 구조(layout)를 말합니다. 모델(model)은 **데이터**에 관한 단 하나의, 가장 확실한 source입니다. 이것은 당신이 저장하는 데이터의 필수적인 필드들과 동작들을 포함하고 있습니다.

우리가 만드는 단순한 설문조사(poll) 앱을 위해 `Question` 과 `Choice` 라는 두개의 모델을 만들어 보겠습니다. `Question` 은 질문(question) 과 발행일(publication date) 을 위한 두개의 필드를 가집니다. `Choice` 는 선택지(choice) 와 표(vote) 계산을 위한 두개의 필드를 가집니다. 각 `Choice` 모델은 `Question` 모델과 연관(associated) 됩니다. 이런 개념은 간단한 Python **클래스로 표현**할 수 있습니다. 

```python
# polls/models.py
from django.db import models

class Question(models.Model):
    # 기계가 읽기 좋은 형식
    # CharField는 max_length가 필수 값
    question_text = models.CharField(max_length=200)
    # 사람이 읽기 좋은 형식의 데이터베이스 필드 이름
    # 데이터베이스에서는 컬럼명으로 사용
    pub_date = models.DateTimeField('date published')

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

이 정보로 Django 는 다음을 할 수 있습니다.

- 이 app 에 대하여 데이터베이스 스키마 생성 (`CREATE TABLE` statements)
- `Question` 과 `Choice` 객체에 접근하기 위한 Python 데이터베이스 접근 API 를 생성

```
├── django.db.models.Model (클래스 = 모델)
    ├── Question (서브클래스)
	│   ├── question_text (변수 = 모델의 데이터베이스 '필드')
    │   └── pub_date
    └── Choice (서브클래스)
        ├── question 
        ├── choice_text
        └── votes
```



## 모델 활성화

먼저 현재 project 에게 `polls` app 이 설치되어 있다는 것을 알려야 합니다. app 을 현재의 project 에 포함시키기 위해서는, app 의 구성 클래스에 대한 참조를 `INSTALLED_APPS`설정에 추가시켜야 합니다. `PollsConfig` 클래스는 `polls/apps.py` 파일 내에 존재합니다. 

```python
# polls/apps.py
from django.apps import AppConfig

class PollsConfig(AppConfig):
    name = 'polls'
```

따라서, 점으로 구분된 경로는 `'polls.apps.PollsConfig'` 가 됩니다. 이 점으로 구분된 경로를, `mysite/settings.py` 파일을 편집하여 `INSTALLED_APPS` 설정에 추가하면 됩니다. 

```python
# mysite/settings.py
INSTALLED_APPS = [
    'polls.apps.PollsConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

이제, Django 는 `polls` app 이 포함된 것을 알게 되었습니다. 다른 명령을 내려봅시다.

```shell
$ python manage.py makemigrations polls
Migrations for 'polls':
  polls/migrations/0001_initial.py:
    - Create model Choice
    - Create model Question
    - Add field question to choice
```

`makemigrations` 을 실행시킴으로서, 당신이 모델을 변경시킨 사실과(이 경우에는 새로운 모델을 만들었습니다) 이 변경사항을 *migration* 으로 저장시키고 싶다는 것을 Django 에게 알려줍니다.

migration 은 Django가 모델(즉, 데이터베이스 스키마를 포함한)의 변경사항을 저장하는 방법으로써, 디스크상의 파일로 존재합니다. 원한다면, `polls/migrations/0001_initial.py` 파일로 저장된 새 모델에 대한 migration 을 읽어볼 수 있습니다. 

당신을 위해 migration 들을 실행시켜주고, 자동으로 데이터베이스 스키마를 관리해주는 `migrate`라는 명령어가 존재합니다. 이 명령을 알아보기 전에 migration 이 내부적으로 어떤 SQL 문장을 실행하는지 살펴봅시다. `sqlmigrate` 명령은 migration 이름을 인수로 받아, 실행하는 SQL을 반환합니다.

```shell
$ python manage.py sqlmigrate polls 0001
BEGIN;
--
-- Create model Choice
--
# 테이블 이름은 app이름_모델이름(소문자)이 조합되어 자동 생성
CREATE TABLE "polls_choice" (
	# 기본키(PRIMARY KEY)는 자동 추가
	# 자동 증가 integer primary key autoincrement (SQLite)
	"id" integer NOT NULL PRIMARY KEY AUTOINCREMENT,
	"choice_text" varchar(200) NOT NULL, 
	"votes" integer NOT NULL
);
--
-- Create model Question
--
CREATE TABLE "polls_question" (
	"id" integer NOT NULL PRIMARY KEY AUTOINCREMENT,
	"question_text" varchar(200) NOT NULL, 
	"pub_date" datetime NOT NULL
);
--
-- Add field question to choice
--
ALTER TABLE "polls_choice" RENAME TO "polls_choice__old";
CREATE TABLE "polls_choice" (
	"id" integer NOT NULL PRIMARY KEY AUTOINCREMENT,
	"choice_text" varchar(200) NOT NULL, 
	"votes" integer NOT NULL, 
	# 왜래 키(foreign key) 필드명에 "_id" 이름을 자동으로 추가
	"question_id" integer NOT NULL REFERENCES
	"polls_question" ("id"));
INSERT INTO "polls_choice" ("id", "question_id", "choice_text", "votes") SELECT "id", NULL, "choice_text", "votes" FROM "polls_choice__old";
DROP TABLE "polls_choice__old";
CREATE INDEX "polls_choice_7aa0f6ee" ON "polls_choice" ("question_id");

COMMIT;
```

`sqlmigrate` 명령은 실제로 데이터베이스의 migration 을 실행하지는 않습니다. 이 명령은 단순히 결과만 출력할 뿐이며, Django 가 필요로 하는 SQL 이 무엇인지 확인할 수 있습니다. 이 결과를 이용하여 Django 가 무엇을 할 것인지 미리 확인하거나, 데이터베이스 관리자에게 필요한 SQL 스크립트를 요청할 수도 있습니다.

이제, `migrate`를 실행시켜 데이터베이스에 모델과 관련된 테이블을 생성해봅시다.

```shell
$ python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, polls, sessions
Running migrations:
  Applying polls.0001_initial... OK
```

[`migrate`](http://django-document-korean.readthedocs.io/ko/latest/ref/django-admin.html#django-admin-migrate) 명령은 아직 적용되지 않은 모든 migration 들을 수집하여 이를 실행합니다. 이 과정을 통해 모델에서의 변경 사항들과 데이터베이스의 스키마의 동기화가 이루어집니다.

### 정리

- (`models.py` 에서) 모델을 변경합니다.
- `python manage.py makemigrations`을 통해 이 변경사항에 대한 migration 을 만드세요.
- `python manage.py migrate` 명령을 통해 변경사항을 데이터베이스에 적용하세요.

migration 을 만드는 명령과, 적용하는 명령이 분리된 이유는 버전 관리 시스템에 migration 을 커밋할 수 있게 하여 app 과 함께 제공하기 위해서입니다. 이것은 단순히 당신의 개발을 쉽게 해주는 것 뿐 아니라, 다른 개발자들과 운영 환경에서도 유용하게 사용될 수 있습니다.



## API

대화식 Python 쉘에 뛰어들어 Django API 를 자유롭게 가지고 놀아봅시다. Python 쉘을 실행하려면 다음의 명령을 입력합니다.

```shell
$ python manage.py shell
Python 3.4.3 (default, Jan 25 2017, 11:10:47)
[GCC 4.2.1 Compatible Apple LLVM 8.0.0 (clang-800.0.42.1)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)

# 또는 아래의 방법으로 진행할 수 있다.
$ python
>>> import django
>>> django.setup()
```

쉘에 진입한 후, *database API*를 탐험해 보세요.

```shell
# 작성한 모델 클래스 import
>>> from polls.models import Question, Choice

# objects.all()은 데이터베이스에있는 모든 QuerySet 표시
>>> Question.objects.all()
<QuerySet []>

# Question 객체의 인스턴스 q생성
>>> from django.utils import timezone
>>> q = Question(question_text="What's new?", pub_date=timezone.now())
>>> q.save()

>>> q.id
1
>>> q.question_text
"What's new?"
>>> q.pub_date
datetime.datetime(2017, 2, 4, 10, 20, 26, 383472, tzinfo=<UTC>)

>>> q.question_text = "What's up?"
>>> q.save()
>>> Question.objects.all()
<QuerySet [<Question: Question object>]>
```

`<Question: Question object>`는 이 객체를 설명하는데에 도움이 안되네요. (polls/models.py 파일의) Question 모델을 수정하여 `__str__()` 메소드를 Question 과 Choice 에 추가해 봅시다.

```python
# polls/models.py
from django.db import models

class Question(models.Model):
    # ...
    def __str__(self):
        return self.question_text

class Choice(models.Model):
    # ...
    def __str__(self):
        return self.choice_text
```

```shell
# __str__() 추가가 잘되었는지 확인
>>> Question.objects.all()
<QuerySet [<Question: What's up?>]>
```

이것들은 모두 보통의 Python 메소드입니다. 예시를 위해 수정된 메소드를 추가해 보겠습니다.

```python
# polls/models.py
import datetime  # 파이썬 모듈

from django.db import models
from django.utils import timezone  # 장고의 시간대 관련 유틸리티

class Question(models.Model):
    # ...
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
```

변경된 사항을 저장하고, `python manage.py shell` 를 다시 실행해보세요.

```shell
>>> from polls.models import Question, Choice

>>> Question.objects.all()
<QuerySet [<Question: What's up?>]>

# Django는 완전히 구동되는 풍부한 데이터베이스 조회 API를 제공
# 키워드 인자
>>> Question.objects.filter(id=1)
<QuerySet [<Question: What's up?>]>
>>> Question.objects.filter(question_text__startswith='What')
<QuerySet [<Question: What's up?>]>

# 올해 출판한 질문 검색
>>> from django.utils import timezone
>>> current_year = timezone.now().year
>>> Question.objects.get(pub_date__year=current_year)
<Question: What's up?>

# 존재하지 않는 id를 요청하면 예외 발생
>>> Question.objects.get(id=2)
Traceback (most recent call last):
  ...
polls.models.DoesNotExist: Question matching query does not exist.

# 기본키 검색
>>> Question.objects.get(pk=1)
<Question: What's up?>

# 작성한 custom 메소드가 작동하는지 확인
>>> q = Question.objects.get(pk=1)
>>> q.was_published_recently()
True

# ForeignKey 관계에 접근할 수 있는 API를 통해,
# "상대방(예: Question's Choice)"을 보유하는 set을 만든다.
>>> q = Question.objects.get(pk=1)
>>> q.choice_set.all()
<QuerySet []>

# create는 Choice 생성
>>> q.choice_set.create(choice_text='Not much', votes=0)
<Choice: Not much>
>>> q.choice_set.create(choice_text='The sky', votes=0)
<Choice: The sky>
>>> c = q.choice_set.create(choice_text='Just hacking again', votes=0)

# Choice 객체는 Question 관련 객체에 접근하는 API가 있다.
>>> c.question
<Question: What's up?>

# 반대 경우 : Question 객체는 Choice 객체에 접근할 수 있다.
>>> q.choice_set.all()
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>
>>> q.choice_set.count()
3

# API는 필요한만큼 자동으로 관계를 추적합니다.
# 두 개의 밑줄(__)을 사용하여 관계를 분리하십시오.
# 이것은 원하는 수준만큼 작동합니다. 제한이 없습니다.
# 올해 pub_date의 질문에 대한 모든 일치 항목을 찾습니다.
>>> Choice.objects.filter(question__pub_date__year=current_year)
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>

# 선택 사항 중 하나를 삭제합시다. 그 것에는 delete ()를 사용하십시오.
>>> c = q.choice_set.filter(choice_text__startswith='Just haking')
>>> c.delete()
(0, {'polls.Choice': 0})
```



### ForeignKey relation

```python
# polls/models.py
# 각 클래스의 __str__ 메소드 리턴 값을 수정
class Question(models.Model):
    def __str__(self):
        return 'question_text: {}, pub_date: {}'.format(self.question_text, self.pub_date)

class Choice(models.Model):
    def __str__(self):
        return 'qestion: {}, choice_text: {}, votes: {}'.format(self.question, self.choice_text, self.votes)

```



```shell
>>> from polls.models import Question, Choice

>>> Question.objects.all()
<QuerySet [<Question: question_text: What's up?, pub_date: 2017-02-04 10:20:26.383472+00:00>]>
>>> Choice.objects.all()
<QuerySet [<Choice: qestion: question_text: What's up?, pub_date: 2017-02-04 10:20:26.383472+00:00, choice_text: Not much, votes: 0>, <Choice: qestion: question_text: What's up?, pub_date: 2017-02-04 10:20:26.383472+00:00, choice_text: The sky, votes: 0>, <Choice: qestion: question_text: What's up?, pub_date: 2017-02-04 10:20:26.383472+00:00, choice_text: Just hacking again, votes: 0>]>

>>> Question.objects.get(pk=1).was_published_recently()
True

>>> q = Question.objects.get(pk=1)
>>> q.choice_set.all()
<QuerySet [<Choice: qestion: question_text: What's up?, pub_date: 2017-02-04 10:20:26.383472+00:00, choice_text: Not much, votes: 0>, <Choice: qestion: question_text: What's up?, pub_date: 2017-02-04 10:20:26.383472+00:00, choice_text: The sky, votes: 0>, <Choice: qestion: question_text: What's up?, pub_date: 2017-02-04 10:20:26.383472+00:00, choice_text: Just hacking again, votes: 0>]>

>>> c = Choice.objects.get(pk=3)
>>> c.question_set.all()
Traceback (most recent call last):
  File "<console>", line 1, in <module>
AttributeError: 'Choice' object has no attribute 'question_set'
>>> c.question
<Question: question_text: What's up?, pub_date: 2017-02-04 10:20:26.383472+00:00>
>>> c.question_id
1
```



## Django Admin 모듈 소개

### 관리자 생성하기

관리 사이트에 로그인 할 수 있는 사용자를 생성해 봅시다. 다음과 같은 명령을 수행합니다.

```shell
$ python manage.py createsuperuser
Username : admin
Email address: admin@example.com
Password:
Password (again):
```

### 개발 서버 실행

```shell
python manage.py runserver
```

웹 브라우져를 열고 로컬 도메인의 “/admin/” 으로 이동합니다. 예를들면, http://127.0.0.1:8000/admin/ 으로 접근할 수 있습니다. 

### 관리 사이트에서 poll app 을 변경가능하도록 만들기

관리 사이트에 `Question` 객체가 관리 인터페이스를 가지고 있다는것을 알려주는 것입니다. 이것을 하기 위해서는, `polls/admin.py` 파일을 열어 다음과 같이 편집하면 됩니다.

```python
# polls/admin.py
from django.contrib import admin
from .models import Question

admin.site.register(Question)
```

웹 브라우저에서 http://127.0.0.1:8000/admin/polls/ 접속해 확인합니다.