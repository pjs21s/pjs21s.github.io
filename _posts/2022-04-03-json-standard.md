---
layout: post
title: "JSON 표준 문법과 자바스크립트"
tags: Javascript
comments: true
---

Unexpected token ' in JSON at position 1과 같이 에러가 발생할 때가 있다.  

```JSON
"{'test-history': 111, 'test-success-rate': 222, 'test-user-build-api': 333, 'cards': 444}"
```

위와 같이 데이터를 API에서 보낸 적이 있었는데 처음에는 무엇이 문제일까 하다가
에러를 봐서는 문법 관련인거 같아서 찾아봤는데 JSON 표준 문법과 관련이 있었다.  

보통 쌍따옴표로 제일 바깥 문자를 감싸다 보니 내부에 따옴표를 사용하게 되는데  
JSON은 키 값이 쌍따옴표 ""로 감싸져야 한다고 하여 가장 바깥쪽을 따옴표를 사용하고  
내부 키값을 쌍따옴표로 감싸서 수정하였다.  

이래서 자바스크립트 관련 VSCODE Extension이 자꾸 따옴표로 바꾸는 것이었다.  
역시 하나를 이해하더라도 왜 그런지 조금이라도 더 깊이 파는 습관을 만들어야 한다.  
