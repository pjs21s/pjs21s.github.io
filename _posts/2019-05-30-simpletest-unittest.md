---
layout: post
title: "단위 테스트로 간단한 홈페이지 테스트"
comments: true
tag: TDD
---

기능 테스트는 사용자 관점에서 애플리케이션 외부를 테스트하는 것 -> 제대로 기능성을 갖추게 함
단위 테스트는 프로그래머 관점에서 내부를 테스트하는것 -> 깔끔하고 버그없는 코드를 위함

# Traceback 읽기

```
======================================================================
ERROR: test_root_url_resolves_to_home_page_view (lists.tests.HomePageTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "C:\Users\pjs21\Desktop\TDD\superlists\lists\tests.py", line 7, in test_root_url_resolves_to_home_page_view
    found = resolve('/')
  File "C:\Users\pjs21\AppData\Local\Programs\Python\Python37-32\lib\site-packages\django\urls\base.py", line 24, in resolve
    return get_resolver(urlconf).resolve(path)
  File "C:\Users\pjs21\AppData\Local\Programs\Python\Python37-32\lib\site-packages\django\urls\resolvers.py", line 566, in resolve
    raise Resolver404({'tried': tried, 'path': new_path})
django.urls.exceptions.Resolver404: {'tried': [[<URLResolver <URLPattern list> (admin:admin) 'admin/'>]], 'path': ''}
```

마지막 줄에 있는게 `에러`다. 간단한 경우는 이곳만 봐도 해결이 가능하다. 하지만 파악이 불가능 할 경우 다른 부분을 더 확인해야한다.

첫째 줄에 있는 `ERROR`부분이 어떤 테스트가 실패하고 있는지 말해주는 부분이다.

이후 읽어가면서 `ERROR`에서 발생한 부분과 관련한 부분을 찾는다. 여기서는 test 함수의 문제니까 관련한걸 찾아보면 line 7에서 발생한 `found = resolve('/')`부분이다. 

책에서는 말하길 '/'를 찾으려고 했으나 없어서 오류가 난것이라고 하였다.



# 단위테스트- 코드 주기

1. 터미널에서 단위 테스트를 실행해서 어떻게 실패하는지 확인
2. 현재 실패 테스트를 수정하기 위한 최소한의 코드를 변경

자신 있는 부분이라도 작은 단위로 나누어 코드를 변경해야 코드 품질이 높아진다.

```python
#views.py
from django.shortcuts import render
from django.http import HttpResponse

def home_page(request):
    return HttpResponse('<html><title>To-Do lists</title></html>')
```

```python
#urls.py
from django.contrib import admin
from django.urls import path
from lists import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.home_page, name='home'),
]
```

```python
#lists/tests.py
from django.test import TestCase
from django.urls import resolve
from lists.views import home_page
from django.http import HttpRequest

class HomePageTest(TestCase):
    def test_root_url_resolves_to_home_page_view(self):
        found = resolve('/')
        self.assertEqual(found.func, home_page)

    def test_home_page_returns_correct_html(self):
        request = HttpRequest()
        response = home_page(request)
        self.assertTrue(response.content.startswith(b'<html>'))
        self.assertIn(b'<title>To-Do lists</title>', response.content)
        self.assertTrue(response.content.endswith(b'</html>'))
```

