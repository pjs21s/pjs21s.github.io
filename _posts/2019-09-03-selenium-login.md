---
layout: post
title: "Selenium 인스타그램 로그인"
tags: Selenium
comments: true
---

구글은 패스워드칸을 인식을 못해서 실패... 네이버는 캡챠 실패

성공 못시키면 너무 억울할거 같아서 가장 쉬운 인스타라도 해봤다.

다행히 인스타는 쉽게 성공

조금 뒤에 다른걸 더 해봐야겠다.

```python
from selenium import webdriver
from selenium.webdriver.firefox.firefox_binary import FirefoxBinary
from time import sleep

binary = FirefoxBinary('C:\Program Files (x86)\Mozilla Firefox\\firefox.exe')
browser = webdriver.Firefox(firefox_binary=binary)

browser.implicitly_wait(3)

browser.get('https://www.instagram.com/accounts/login/?source=auth_switcher')

browser.find_element_by_name('username').send_keys('아이디')

browser.find_element_by_name('password').send_keys('패스워드')

browser.find_element_by_xpath("//button[@type='submit']").click()

sleep(10)

browser.quit()
```

