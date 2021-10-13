---
layout: post
title: "Django REST와 JWT 사용해보기"
tags: Django
comments: true
---

원문 : https://simpleisbetterthancomplex.com/tutorial/2018/12/19/how-to-use-jwt-authentication-with-django-rest-framework.html#obtain-token

JWT는 JSON Web Token을 줄인 말로 인증 시스템입니다.

JWT를 사용함으로 서버는 유저의 세션을 유지할 필요가 없습니다. 그래서 서버 자원을 아낄 수 있게 되기 때문에 사용하면 좋습니다.

자세한 설명 https://velopert.com/2389



# 필요 사항

- Python (3.6, 3.7)
- Django (2.0, 2.1, 2.2)
- Django REST Framework (3.8, 3.9, 3.10)

Django Rest API를 다뤄보고 이 예제를 진행하는게 더 이해를 하기 쉽습니다.

저도 그렇게 했기 때문에 REST Framework에 대한 설명은 아마 적을 듯 합니다.

JWT는 **access token**과 **refresh token**을 가지고 있습니다.

**access token**은 잠시동안만 살아있습니다. 기본값은 5분이고 커스터마이징이 가능하다.

**refresh token**은 조금 더 긴 24시간동안 살아있고 역시 커스터마이징이 가능하다.

토큰이 만료가 되면 당연히 다시 로그인을 해야한다.

왜냐하면 중요한 정보를 담고 있기 때문이다.

```bash
xxxxx.yyyyy.zzzzz
```

JWT는 세 부분으로 구성되어 있는데

```bash
header.payload.signature
```

**header**

```python
{
  "typ": "JWT",
  "alg": "HS256"
}
```

**payload**

```python
{
  "token_type": "access",
  "exp": 1543828431,
  "jti": "7f5997b7150d46579dc2b49167097e7b",
  "user_id": 1
}
```

**signature**

signature에는 header(base64) + payload(base64) + `SECRET_KEY`로 구성되어 있다. `SECRET_KEY`를 배포 시에 드러나지 않게 해야하는 이유가 하나 더 생겼다. signature는 이러한 정보다 맞는지 검증하는데 사용된다.



# 설치

우선 빈 폴더 안에 프로젝트를 생성한다.

`django-admin startproject jwtproject`

그리고 `cd jwtproject` 후에 `python manage.py startapp apiviews`를 만든다.



```bash
pip install djangorestframework_simplejwt
```

당연히 django rest framework도 설치되어 있어야 한다.

**settings.py**에 아래의 값을 넣어준다.

```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}
```

jwtproject안에 있는 **urls.py**에 아래의 값을 넣어준다.

```python
from django.contrib import admin
from django.urls import path, include
from rest_framework_simplejwt import views as jwt_views
from apiviews import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/token/', jwt_views.TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/token/refresh/', jwt_views.TokenRefreshView.as_view(), name='token_refresh'),
    path('hello/', include('apiviews.urls')),
]

```

apiviews에 있는 **views.py**에 아래의 코드를 넣어준다.

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticated

class HelloView(APIView):
    permission_classes = (IsAuthenticated,)
    
    def get(self, request):
        content = {'message': 'Hello, World!'}
        return Response(content)

```

apiviews에 **urls.py**를 만들고 아래의 코드를 작성한다.

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.HelloView.as_view(), name="hello"),
]
```

여기까지 했으면 아래와 같은 화면을 볼 수 있다.

혹시 안되면 필자는 settings.py에 APPEND_SLASH = False를 넣었으니 설정하고 다시 들어가보자

http://localhost:8000/hello/

<img src="/images/jwt1.png">

# 사용해보기

사용을 해보려면 HTTPie라는 도구가 필요하다.

curl을 사용하라고도 하는데 curl보다 훨씬 편하니까 HTTPie를 추천한다.

홈페이지에서 `pip install httpie`로 설치하라고 한다.

혹은 다른 방법도 있으니 공식 홈페이지를 참고

https://httpie.org/

그리고 토큰을 보려면 인증된 사용자가 필요하기 때문에 유저를 생성해준다.

```python
python manage.py createsuperuser --username vitor --email vitor@email.com
```

Password : 123으로 하고 

`Bypass password validation and create user anyway? [y/N]: y`

하면 패스워드를 8자 이상을 하지 않아도 된다.

만약에 인증된 사용자를 만들지 않고 토큰 값을 얻어오려고 하면 이런 에러가 발생한다.

```python
AttributeError: 'NoneType' object has no attribute 'split'
----------------------------------------
Unauthorized: /api/token/
```

# 토큰 얻어오기

일단 인증된 사용자로 토큰을 얻어오자

`http post http://127.0.0.1:8000/api/token/ username=vitor password=123`

<img src="/images/jwt2.png">

정상적으로 접근했다면 토큰 값이 보여야하고 그렇지 않은 경우 아래와 같은 값이 나올 수 있다.

`"detail": "No active account found with the given credentials"`

그 후 **access token**을 넣고 해보면 메세지를 볼 수 있다.

`http http://127.0.0.1:8000/hello/ "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNTY2MjkwODQ2LCJqdGkiOiI4MDU1Yzk4ZTJkYzE0YjlhYmFiNWRjZmM1ODI2NTY5MyIsInVzZXJfaWQiOjF9.1UnTrEFsCBHnfRqK__pd04rBVMX129OOrNjoFTzd8Iw"`

**Bearer** 이후에 본인의 access token 값을 넣으면 된다.

만약 만료된 후에 저 명령을 싱행하면 

`Token is invalid or expired`라는 메세지를 볼 수 있다.

이번엔 **refresh token**을 넣어보자

`http post http://127.0.0.1:8000/api/token/refresh/ refresh=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoicmVmcmVzaCIsImV4cCI6MTU2NjM3Njk0NiwianRpIjoiZjhmN2MyZTFhNzY1NDBmZDkxNmYwNzMwYjNiY2Q1NGIiLCJ1c2VyX2lkIjoxfQ.TcslE3gQ5uFsYD6LLSIWzgQrjlq1X5kVWThovac7-J0`

그러면 access token을 볼 수 있다. 근데 이 access token은 새로운 것이기 때문에 다음 요청을 할 때는 이 access token을 사용해야 한다.

문제가 생긴다면 원문을 참고하시는게 제일 좋습니다. 혹은 스택오버플로우...