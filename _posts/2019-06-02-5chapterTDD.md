---
layout: post
comments: true
title: "클린 코드를 위한 TDD 5장 기록"
tag: TDD
---

```python
def test_home_page_can_save_a_POST_request(self):
        request = HttpRequest()
        request.method = 'POST'
        request.POST['item_text'] = '신규 작업 아이템'

        response = home_page(request)

        self.assertIn('신규 작업 아이템', response.content.decode())
```

POST 요청 처리와 반환된 HTML이 신규 아이템 텍스트를 포함하고 있는지 확인하는 로직

# 테스트코드 줄 띄우기

처음 세 줄의 코드는 설정(Setup)을 위한 부분
가운데는 실제 함수를 호출하는 처리(Exercise) 부분
마지막은 Assert 코드
테스트 구조를 쉽게 파악할 수 있도록 만듦


`assertIn(a,b)`는 `a in b`의 의미로 b에 a가 있는지를 검사한다.

`test_`외의 이름을 사용해서 사용자 정의 메소드를 만들 수 있다.

`assertEqual(a,b)`은 `a == b` 인지 확인해서 true, false를 가린다.  

# 에러 수정

```selenium.common.exceptions.StaleElementReferenceException: Message: The element reference of <table id="id_list_table"> stale; either the element is no longer attached to the DOM, it is not in the current frame context, or the document has been refreshed```

<https://stackoverflow.com/questions/45178817/selenium-with-python-stale-element-reference-exception>

```python
inputbox.send_keys(Keys.ENTER)
self.check_for_row('1: 공작깃털 사기')
```

-> 변경

```python
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions

inputbox.send_keys(Keys.ENTER)

WebDriverWait(self.browser, 10).until(
            expected_conditions.text_to_be_present_in_element(
                (By.ID, 'id_list_table'), '공작깃털 사기'))

self.check_for_row('1: 공작깃털 사기')
```

`WebDriverWait`는 설정한 시간 즉 여기서는 10초 이내에 괄호안에 있는 명령이 실행이 되면 True를 반환한다.

`By`는 ID를 찾아올때 사용한다. 여기서는 `id_list_table`인곳을 찾는다.

이번에 처음 알았는데 `from selenium.webdriver.support import expected_conditions as EC`

import 할때 as 뒤에 단축어를 지정해서 사용할 수 있다.

`EC.text_to_be_present_in_element` 이렇게 바꾸어서 사용할 수도 있다.

# 각 테스트는 하나의 기능만 테스트 해야 한다

이를 통해 버그 추적이 더 용이해진다.


**추가 에러**

```
self.assertIn('itemey 1', response.content.decode())
AssertionError: 'itemey 1' not found in '<html>\n    <head>\n ...
```

참고: <https://lhy.kr/tdd-with-python>