---
layout: post
title: "플러터 Flutter 환경 변수 MAC OS 설정하기"
tags: Flutter
comments: true
---

Flutter를 해보기 위해 환경 변수를 설정해야 했는데 Windows보다 어려웠다.
그래서 정리를 하려고 한다.

우선 핵심은 아래 사진처럼 실행파일이 있는 디렉토리까지 `cd` 명령어를 통해
들어가는 것이다.

<img src="/images/macospath1.png">

`cd /Users/vors/Documents/flutter/bin`

그리고 `./bash_profile`이라는 새로운 파일을 작성해야 한다.

`vi ~/.bash_profile`

원래는 아래와 같이 적어야 한다.

```
export PATH="$PATH:`pwd`/flutter/bin"
```

그런데 필자는 이미 실행파일이 있는 `/flutter/bin`에 직접 들어왔기 때문에

```
export PATH="$PATH:`pwd`"
```

이렇게 수정해서 `~/.bash_profile`에 적어준다.

`source ~/.bash_profile`로 적용하고
`flutter`라고 적었을 때 문제없이 로그 같은 게 나오면 성공이다.

근데 문제는 이렇게 설정하여도 맥을 끄고 나면 초기화되기에 계속 사용하려면
추후 다른 방법을 사용해야 한다.
