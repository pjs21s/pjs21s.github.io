---
title: "django-allauth OAuth google 사용하기"
layout: post
tag: Django
comments: true
---

<https://django-allauth.readthedocs.io/en/latest/installation.html>

google OAuth를 사용해보자

`pip install django-allauth`로 설치한다.

`TEMPLATES`설정은 이미 했다고 가정한다. 만약 `settings.py`에 `TEMPLATES`관련 설정을 하지 않았다면 공식문서를 참고하자.

```python
AUTHENTICATION_BACKENDS = (
    # Needed to login by username in Django admin, regardless of `allauth`
    'django.contrib.auth.backends.ModelBackend',
    # `allauth` specific authentication methods, such as login by e-mail
    'allauth.account.auth_backends.AuthenticationBackend',
)
INSTALLED_APPS = [
 ...
    'django.contrib.auth',
    'django.contrib.messages',
    'django.contrib.sites',
    'allauth',
    'allauth.account',
    'allauth.socialaccount',
    'allauth.socialaccount.providers.google',
    #본인이 사용할 서비스의 providers를 공식문서를 보고 추가해준다.
]

SITE_ID = 1
```


```python
#urls.py
path('accounts/', include('allauth.urls')),
```

만약 본인이 이미 `accounts/`를 url로 사용하고 있다면 임의로 변경해줘도 문제는 없다.

필자는 `accounts/`를 사용하고 있어서 아래와 같이 변경했다.

`path('authaccounts/', include('allauth.urls')),`

`python manage.py migrate`를 해준다.


# Google OAuth API 설정

그리고 django admin에 가서 설정을 하기 전에 구글 API 값을 받아야 한다.

[https://console.developers.google.com](https://console.developers.google.com/)

왼쪽 탭에 사용자 인증 정보를 클릭하고 OAuth 동의 화면에 들어간다.

아래와 같이 본인의 APP 이름 혹은 웹 사이트 이름과 본인 이메일을 적어줍니다.

승인된 도메인은 적지 않고 그대로 저장합니다.

<img src="/images/oauth.png">



사용자 인증 정보 만들기를 클릭 후 OAuth 클라이언트 ID를 클릭해 아래와 같이 적어줍니다.

승인된 자바스크립트 원본에는 기본 url을 적고 승인된 리디렉션 URI에는 `http://localhost:8000/accounts/google/login/callback/` 뒤에 `/`까지 포함해서 적어줍니다.

만약 위에 `path`설정에서 `path('authaccounts/', include('allauth.urls')),` 이와 같이 하셨다면

이미지에 있는 것처럼 적어주시면 됩니다.

<img src="/images/oauth2.png">

저장하면 클라이언트 ID와 클라이언트 보안 비밀 Key가 생성됩니다.


# django admin 설정

이제 admin에 들어가면 Social Application이라는 항목이 있을겁니다.

추가를 누르셔서 방금 생성된 클라이언트 아이디와 비밀 키를 넣어주시고 Sites도 아래 이미지와 같이 추가해줍시다. 여기서 `example.com`이 필요없어 보인다고 지우시면 오류가 발생하기 때문에 삭제하시면 안됩니다.

<img src="/images/oauth3.PNG">

만약에 삭제하셨다면 <https://stackoverflow.com/questions/11476210/getting-site-matching-query-does-not-exist-error-after-creating-django-admin> 참고하세요.

이제 템플릿에서 아래의 코드를 넣어서 버튼을 누르면 작동합니다.

{% raw %}

```html
{% load socialaccount %}
<a href="{% provider_login_url 'google' %}">Google</a>
```
{% endraw %}

저는 버튼을 조금이나마 예쁘게 넣고 싶어서 아래와 같이 했습니다.

{% raw %}
```html
<div class="btn white">
    <a href="{% provider_login_url 'google' %}" style="text-transform:none">
      <div class="left">
        <img width="20px" alt="Google &quot;G&quot; Logo" src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/53/Google_%22G%22_Logo.svg/512px-Google_%22G%22_Logo.svg.png"/>
      </div>   
    </a>
  </div>
```
{% endraw %}


그 외에 OAuth 연결을 하면 추가 정보를 입력해야만 가입 완료가 가능케 하는 기능도 구현할 수 있습니다.

공식문서나 추가자료를 참고하시면 가능합니다.

저는 accounts/를 사용하고 있어 충돌이 나는건지 잘 안됐지만요 ㅠㅠ
