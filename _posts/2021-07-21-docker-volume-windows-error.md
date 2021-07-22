---
layout: post
title: "도커 볼륨 권한 설정 문제, chmod not working"
tags: docker
comments: true
---

윈도우 환경에서 도커를 사용할 때 데이터 보존을 위해 `-v`옵션을 사용한다.
이렇게 하면 윈도우에서도 접근이 가능해지고 편리하다.
동시에 이렇게 연결 된 디렉토리에 관한 권한 설정은 이제 불가능하다.
`chmod`를 몇 번을 눌러봐도 에러는 발생하지 않는데 권한 설정도 안된다.

https://forums.docker.com/t/not-able-to-change-permissions-for-files-and-folders-created-on-mounted-volumes/45769/2

알고 보니 윈도우 환경에서 도커를 사용할 때 버그가 있고 아마 상위 폴더 하위 폴더 관련한 권한 버그가 아닌가 싶다.
그렇기 때문에 어지간하면 서버 용 컴퓨터는 그냥 처음부터 리눅스인게 좋다.