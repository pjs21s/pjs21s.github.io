---
layout: post
title: "윈도우 geoserver installer(설치파일) 만들기"
tags: geoserver
comments: true
---

http://geoserver.org/release/stable/

geoserver는 더 이상 윈도우용 설치파일을 별도로 제공해주고 있지 않습니다.
그래서 `Platform Independent Binary`를 다운받으면 얻는 .zip파일을 기반으로 설치파일을 만드는 법을 안내하고 있습니다.
그래서 오늘은 윈도우용 geoserver installer를 만드는 법을 따라해보겠습니다.

설치 안내 문서 : https://docs.geoserver.org/latest/en/developer/win-installer.html

우선 Stable 버전의 GeoServer를 다운로드 합니다.

<img src="/images/geoserverinstall.png">

저 링크를 들어가시면 SourceForge로 이동되는데 기다리면 자동으로 다운로드 됩니다.

<img src="/images/geoserverinstall2.png">

그리고 문서에 안내되어 있는 것처럼 NSIS를 다운로드합니다.
다운이 완료되면 설치합니다. 설치는 특별한게 없기에 생략합니다.

<img src="/images/geoserverinstall3.png">

그다음 NSIS Access Control plugin을 다운받아서 압축을 풉니다.


`C:\Users\Jin\Downloads\AccessControl\Plugins\AccessControl.dll -> C:\Program Files (x86)\NSIS\Plugins\x86-ansi`

이렇게 각각 Plugins에 있는 AccessControl.dll을 옮겨주시면 됩니다. 다른 파일들은 옮기지 않으셔도 됩니다.

`C:\Users\Jin\Downloads\AccessControl\Unicode\Plugins\AccessControl.dll -> C:\Program Files (x86)\NSIS\Plugins\x86-unicode`

여기까지 하시면 공식 설치 안내 문서에 있는 3번까지는 마친겁니다.

4번 과정 진행을 위해 github에서 소스를 다운합니다.

https://github.com/geoserver/geoserver

코드를 zip 파일로 다운로드 하시면 됩니다. 다운로드가 완료되면 압축을 푸시고 아래 적힌 파일들을 geoserver_stable 다운받았던 폴더에 넣으면 됩니다.

```
LICENSE.txt, src/release/GPL.txt, src/release/installer/win
```

그러면 이런식으로 파일이 모이게 됩니다.

<img src="/images/geoserverinstall4.png">

그리고 나서 `GeoServerEXE.nsi` 오른쪽 클릭하여 Compile NSIS Script를 눌러 설치파일을 생성합니다.

<img src="/images/geoserverinstall5.png">

설치파일이 정상적으로 생성되면 위와 같은 비슷한 화면을 보실 수 있고 Test Installer를 눌러도 되고 설치파일을 직접 실행하셔도 됩니다.

Geoserver 설치도 어렵지 않으니 쭉 진행하시면 됩니다. 설치가 완료되면 Geoserver가 잘 작동하는지 확인해야 합니다.

<img src="/images/geoserverinstall6.png">

Start Geoserver를 검색해서 작동시켜 봅니다. 그리고 아래와 같이 본인이 설정한 포트를 입력하고 접속해서 켜지면 성공입니다. 고생하셨습니다.
http://localhost:8080/geoserver/web/