---
layout: post
title: "python TDD 기초 selenium, binary"
comments: true
tag: TDD
---

클린 코드를 위한 테스트 주도 개발을 보며 공부를 시작했다.
해결한 문제들을 기록하려고 한다.   
기본 코드로 실행하니까 실행이 안되서 찾아가면서 이렇게 바꿔보았다.

```python
from selenium import webdriver
from selenium.webdriver.firefox.firefox_binary import FirefoxBinary

binary = FirefoxBinary('C:\Program Files (x86)\Mozilla Firefox\\firefox.exe')

browser = webdriver.Firefox(firefox_binary=binary)
browser.get('http://www.python.org')

assert 'django' in browser.title
```

binary non-text-file로 소스코드가 아닌 실행파일을 의미한다.
아마 firefox를 실행할 수 있게 Binary Path를 설정해줘야 하는 듯 하다.

# 190530 추가

브라우저는 실행이 되는데 빈화면이 뜨면 이건 작동이 제대로 안되고 있다는 증거다.

`AssertionError` 안뜨는 것도 문제임  

그래서 찾아보니까 `geckodriver`라는게 최근 `firefox`부터는 필요함

근데 이게 또 에러 메세지에 뜨면 아는데 안뜨면 `geckodriver` 가 필요한지 알 수도 없다.

`C:\Users\pjs21\AppData\Local\Programs\Python\Python37-32`

Python이 있는 기본 PATH에 `geckodriver.exe`를 다운받아서 넣어준다.

64bit여도 32bit로 다운받아야 한다.

왜냐면 제가 64bit로 했는데 오류가 나서...

`firefox`도 32bit로 다운합니다.

그리고 위에 코드를 실행시키면 작동!
