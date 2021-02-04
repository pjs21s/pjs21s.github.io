---
layout: post
title: "Java Resources 밑에 있는 파일, 경로 가져오기"
tags: Java
comments: true
---

<img src="/images/resource1.png">

이번에 엑셀 기능을 개발하면서 경로를 설정하는 법에 대해 조금 힘들었었다.
파이썬처럼 통일이 되어있지 않고 이 방법 저 방법 다양하게 쓰라고 하는데 되는 건 하나도 없고
몇 일을 찾아보다가 겨우 하나 성공해서 잊어버릴까봐 기록한다.

예를 들어 `...resources/static/result.xlsx` 파일을 읽어서 작업한다고 가정한다.
만약 이걸 절대 경로로 적으면 다른 컴퓨터로 이 프로젝트를 이동했을때 다시 또 그 컴퓨터에 맞게
절대 경로를 지정해주어야 하는 번거로움이 생긴다.

`ResourceUtils.getFile('classpath:static/result.xlsx')`라고 하면 File 형식으로 받아서 사용할 수 있게 된다.
이미지와 같이 파일을 이용한 기능이 필요할 때 유용하게 사용할 수 있다.

경로만 필요하면 `ResourceUtils.getPath...` 같은 옵션이 있으니 찾아서 사용하면 된다.