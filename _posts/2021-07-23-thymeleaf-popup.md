---
layout: post
title: "thymeleaf, 타임리프 window.open으로 팝업"
tags: thymeleaf
comments: true
---

타임리프에서 html을 생성하여 팝업 형식으로 띄우는 방법 기록

```java
@GetMapping("/popup")
public String makePopup() {
    return "make_popup";
}
```

```javascript
window.open("/popup", "popup", "width=300, height=300");
```

직접 html을 불러서 팝업으로 띄우지 않고 스프링에서 선언을 하여 해당 컨트롤러에 요청하여 사용해야한다.