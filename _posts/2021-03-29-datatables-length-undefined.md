---
layout: post
title: "datatables Cannot read property 'length' of undefined 원인 및 해결"
tags: Datatables
comments: true
---

```
datatables Cannot read property 'length' of undefined
```
Spring boot, Thymeleaf 환경에서 Datatables를 사용할 때 위와 같은 에러를 만날 수 있다.

위 경우는 Datatables를 사용할 때 ajax를 설정하는 코드에서 `dataSrc`에 대한 설정이 맞지 않아 데이터를 읽어들이지 못해
length라는 요소가 없어 발생하는 오류이다.

백엔드 쪽에서 이런 식으로 `ResponseEntity`를 리턴한다.
<img src="/images/datatableslengthundefined2.png">

해당 `ResponseEntity`를 직접 `Console.log`로 받아서 찍어보면 왜 `dataSrc`를 `""`으로 받아야 하는지 알 수 있다.
<img src="/images/datatableslengthundefined1.png">

Datatables 기본 설정과 관련되어 있는 것이므로 맞춰서 사용해야 할 것 같다.