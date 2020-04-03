---
layout: post
title: "맥북(in mac) Sourcetree Permission denied (publickey)"
tags: sourcetree
comments: true
---

`cd ~/.ssh`

그리고 이곳에 뭐가 있는지 확인하기 위해 `ls`
그러면 아무것도 없을수도 있고 `.pub`,`.pem` 같은걸 사용했다면 있을 수도 있다.

만약에 `.pub`을 사용한적이 없는데 있다면 지워주자
`rm id_rsa.pub` 이런식으로 파일명을 적어서 지워주면 된다.

`ssh-keygen`을 하면 새로 만들 수 있다.
이름을 정하지 않고 그냥 엔터를 치면 `id_rsa`라는 이름으로 생성된다.
사용할 비밀번호도 입력해준다.

이제 생성한 키 내용을 복사 해준다.
다른 명령어도 있지만 필자는 그냥 파일 열어서 복사했다.
`vi id_rsa.pub`

복사 후 github -> setting -> SSH and GPG keys라는 곳에 가서 New SSH key를 눌러
내용에 넣어주고 저장하면 된다.

본인의 로컬에서도 등록을 해놔야 사용할 수 있으므로
`ssh-add -l`을 해서 `The agent has no identities.`라고 뜨면
`ssh-add -K ~/.ssh/id_rsa` 명령어로 등록하면 된다.
