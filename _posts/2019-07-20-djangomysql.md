---
layout: post
title: "Django MySQL 연동하기"
tags: Algorithm
comments: true
---

참고: <https://myjamong.tistory.com/102>

```python
ValueError: Related model 'accounts.Account' cannot be resolved
ValueError: The field admin.LogEntry.user was declared with a lazy reference to 'accounts.account', but app 'accounts' isn't installed.
```

가상환경에 Mysql을 연결하려고 하면 자꾸 위와 같은 에러가 발생할때가 있는데

필자는 그냥 가상환경 포함 디렉토리를 다 삭제하고 다시 만들어서 진행했다.

그리고 `accounts`로 안쓰고 그냥 `users`로 `startapp` 했다.

어차피 나중에 OAuth 연결하려고 `allauth`를 쓰면 `accounts` 이름이 중복되서 기능 못쓰는 것도 생긴다.

그래서 미리 이름을 안겹치게 다른 걸로 지정했다.

일단 `startproejct`, `startapp`으로 프로젝트와 어플리케이션을 하나 만들어줍니다.

그리고 `settings.py`의 값을 변경해줍니다.

```python
INSTALLED_APPS = [
    ...
    'users',
]

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'django_test',
        'USER': 'root',
        'PASSWORD': '비밀번호',
        'HOST': 'localhost',
        'PORT': '3306',
        'OPTIONS': {
            'init_command': 'SET sql_mode="STRICT_TRANS_TABLES"'
        }
    }
}
```

`USER`에는 본인이 Mysql에서 사용하고 있는 계정을 적으시면 됩니다.

`NAME`에는 아래 사진에 있는 DB의 이름을 적으면 됩니다.

<img src="/images/djangomysql.png">

`pip install mysqlclient`도 설치해줍니다.



# models.py

```python
from django.db import models

# Create your models here.
class Furits(models.Model):
    name = models.CharField(max_length=50)
    descript = models.TextField()
    price = models.FloatField()
    quantity = models.IntegerField()
    cdate = models.DateTimeField(auto_now_add=True)
    
    def __str__(self):
        return self.name
```

# admin.py

```python
from django.contrib import admin
from users.models import *
# Register your models here.

admin.site.register(Furits)
```

위와 같이 모델을 정의한 후 admin에서 테스트 할 수 있도록 추가해줍니다.



```python
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

그리고 마이그레이션 해주고 슈퍼유저를 생성 그리고 실행해봅니다.

admin 페이지에 가서 데이터를 하나 추가합니다.

그러면 아래와 같이 Mysql에도 데이터가 잘 들어가 있는 것을 확인할 수 있습니다.

<img src="/images/djangomysql2.PNG">