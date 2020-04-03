---
layout: post
title: "TooManyResultsException 원인 정리, MySQL timezone error"
tags: Error
comments: true
---

```java
Expected one result (or null) to be returned by selectOne(), but found: 2]을(를) 발생시켰습니다.
org.apache.ibatis.exceptions.TooManyResultsException: Expected one result (or null) to be returned by selectOne(), but found: 2
```

중복검사 기능 만들 때 만난 오류
에러를 차분히 읽어보면 알 수 있다.
하나의 결과를 기대했는데 2개를 찾아서 오류가 발생한 것이다.
그러면 왜 하나의 결과를 기대하고 있는 것일까 바로 중복된 값은 들어가지 않게 설정해놨기 때문이다.

근데 중복검사 기능이 구현된 이후로는 중복된 데이터가 들어가지 않기 때문에 유니크한 값(1개)을 가져오는 걸로 기대하고 있는데 2개의 값이 나오니 발생하는 오류이다.

그러므로 DB에 있는 데이터를 지워주고 다시 테스트를 해보면 문제없이 작동하는 것을 볼 수 있다.

# the server time zone value 'KST' is unrecognized

DBeaver에서 mysql을 설정하는데 이상한 오류가 발생했다.
사진처럼 DB 이름 뒤에 `?serverTimeZone=Asia/Seoul` 붙여주면 된다.
발생하는 이유는 모르겠지만 이런걸로 시간 낭비하면 아까우니 적어둔다.

<img src="/images/timezone1.png">