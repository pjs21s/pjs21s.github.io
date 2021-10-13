---
layout: post
title: "Jekyll(지킬) 설치시 발생하는 ruby make 에러"
tags: Jekyll
comments: true
---
Jekyll 블로그를 생성하기 위해 ruby를 다운 받아 gem 명령어를 입력해서 설치를 하려하니

``` 'make'는 내부 또는 외부 명령, 실행할 수 있는 프로그램, 또는 배치 파일이 아닙니다 ```

이런 오류가 뜬다. 한국어로 검색을 하면 나오는 자료는 리눅스 명령어를 칠 수 있게 해주는 프로그램을 다운하라고 하는데
그래도 해결이 안돼서 영어로 찾아보니 페이지가 하나 나왔다.

<https://github.com/jekyll/jekyll/issues/3208>

그래서 쭉 댓글을 보면 관리자 권한으로 실행을 하라고 하지만 이것도 해결책이 아니다.
문제는 바로 ruby는 64bit인데 Jekyll은 32bit라서 발생하는 에러였다.
ruby를 삭제하고 32bit로 다운받아서 다시 gem 명령어로 Jekyll을 다운하니 깔끔하게 해결됐다.