---
layout: post
title: "DBeaver mysql dump 안될때 - IO Error: Utility 'mysqldump.exe' not found in client home"
tags: DBeaver
comments: true
---

DBeaver를 통해 mysql DB data를 dump하려는데 계속 에러가 발생했다.
`IO Error: Utility 'mysqldump.exe' not found in client home`

이건 DBeaver에서 제대로 mysql이 설치된 디렉토리를 찾지 못하고 있기때문에
발생하는 오류이다.
그러므로 해당 DB 우클릭 후 `Edit Connection -> Local Client`에 올바르게 설치된
DB 디렉토리를 설정해주면 된다. 아래 이미지를 참고하시면 됩니다.

<img src="/images/dump1.png">

<img src="/images/dump2.png">