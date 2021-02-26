---
layout: post
title: "rbenv shims in path not found 해결 방안"
tags: Ruby
comments: true
---

`curl -fsSL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-doctor | bash`
루비를 설치하려는데 위의 명령을 실행했을때 아래와 같이 경로를 못찾는 경우가 있다.
`rbenv shims in path not found`

그러면 터미널의 아래의 명령들을 차례로 쳐주면 된다.
`echo 'eval "$(rbenv init -)"' >> ~/.zshrc`
`source ~/.zshrc`

만약에 zsh를 사용하지 않는다면 아마 .bash_profile과 같은 설정파일을 사용할텐데
그 경우에는 본인에 맞게 바꿔서 명령어를 사용하도록 한다.