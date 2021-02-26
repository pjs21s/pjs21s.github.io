---
layout: post
title: "190405 django Setting.py 수정 메모"
tags: Django
comments: true
---

캡스톤 프로젝트로 하고 있는 Django의 Setting.py를 조금 수정했다.
```python
TEMPLATE_DIR = os.path.join(BASE_DIR, 'templates')
```
TEMPLATE_DIR을 위와 같이 정의 해둔 후에 INSTALLED_APPS에는 사용하고 있는 프로그램(폴더 이름)을 추가시켰다.
```python
TEMPLATE =[
    {
        'DIRS' : [TEMPLATE_DIR, ],
...
]
```
TEMPLATE 내부에 DIRS에도 잊지 않고 추가시켜주어야 한다.

urls.py에 추가해야 할 코드가 늘어나다 보니 SLASH 관련 에러가 나서 검색해보니 아래와 같이 하라고 해서 추가했다.
```python
APPEND_SLASH = False
```
APPEND_SLASH는 기본 True이고 자동으로 URL 끝에 '/'를 붙여 주는 Django의 옵션이다.
브라우저에서 요청한 URL이 `urls.py` 안에 정의된 패턴과 일치하지 않을 때만 자동으로 '/'를 붙여 검사를 한다.
그래서 `APPEND_SLASH = False`로 값을 지정해줘서 자동으로 '/'가 붙지 않게 해주면 오류가 해결됐다.

<https://docs.djangoproject.com/en/2.1/ref/settings/>

Django 공식 문서에 의하면 `contrib.auth.login`를 사용했을 때의 Default 값은 '/accounts/profile/'이다.
실제로 기본으로 지원해주는 LoginView를 사용해서 로그인을 하면 바로 profile 페이지로 넘어가게 된다.
그러면 사용자는 다시 서비스를 이용하기 위해 메인 페이지로 와야 하기 때문에 번거로움이 있을 수 있다.
그래서 `Setting.py` 하단에 `LOGIN_REDIRECT_URL = 'index'`라고 정의를 별도로 해서 메인 페이지로 이동할 수 있게 한다.
아마 'index'라고만 작성을 해도 인식이 가능한 이유는 이미 위에서 templates에 대한 경로를 지정을 해놨기 때문에 따로 경로를 자세히 적지 않아도 오류 없이 인식을 하는 것 같다.

<img src = "/images/190405_settings.png">

LOGOUT_REDIRECT_URL 또한 같은 방식이다.
LogoutView를 이용하면 로그아웃 시 Django Admin 페이지로 이동하기 때문에 `LOGOUT_REDIRECT_URL = 'index'`를 정의해준다.
이렇게 되면 로그아웃 시에도 Main 페이지로 넘어가게 된다.

마지막은 Class 이름을 조금 수정했는데 강의를 듣다 보니 Class의 이름은 첫 글자를 모두 대문자로 하고 있다는 걸 발견했다.
원래 알고 있었지만 프로젝트 파일을 열어보니 소문자로 되어있는 Class가 몇 개 있어서 대문자로 변경해두었다.
