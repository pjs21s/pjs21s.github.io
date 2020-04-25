---
layout: post
title: "Git cannot overwrite multiple values with a single value 해결 방안"
tags: Git
comments: true
---

처음에 git config를 잘못사용해서 나중에 user.name이나 user.email을 수정하려고하면

`cannot overwrite multiple values with a single value`라는 오류와 함께
수정이 되지 않는다.

이런 경우에는 아래와 같은 형식으로 명령어를 쳐주면 전부 해당값으로 바꿔준다.
user.name을 바꾸는 경우에도 동일한 형식으로 사용하면 된다.

`git config --global --replace-all user.email "new@mail.com"`

참고 :<https://stackoverflow.com/questions/4310974/more-than-one-value-for-the-key-user-name-git>