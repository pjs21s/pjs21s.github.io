---
layout: post
title: "Docker Oracle 데이터 날아가지 않게"
tags: Docker
comments: true
---

Docker에 Oracle을 설치하는건 어려운 일이 아니다.
기분탓인지는 모르겠으나 윈도우에서 Docker 사용한것보다 더 쉽게 느껴진다.
그 사이에 AWS 해보느라 명령어 치는게 익숙해져서 그럴지도 모르겠다.

그런데 사용하고 나서 다음날 보니 데이터도 다 사라지고 작동이 안됐다.
알고보니 Docker는 휘발성이므로 기본적으로 보관을 안한다고 한다.
그래서 이번 포스팅에서는 종료 후에도 데이터가 남아있게 해보자

```
docker run --name oracle11g -d -p 8080:8080 -p 1521:1521 -v ~/documents/oracle11g-data jaspeen/oracle-xe-11g;
```

위와 같이 run을 할때 이름과 디렉토리를 지정하면 나중에 다시 접속했을때도 데이터가 사라지지 않게 된다.
이름: `oracle11g`, 디렉토리: `~/documents/oracle11g-data`

사용하려면 아래와 같이 하면 된다.

`docker login`

`docker start oracle11g`

`docker exec -it oracle11g sqlplus`

Enter user-name : system, Enter password : oracle

이렇게 하면 연결해둔 SQLDeveloper에서 다시 그대로 사용가능하다.
oracle11g는 필자가 설정한 이름이니 본인에 맞게 바꾸어 사용해도 무방하다.

삭제시 `docker rm <이름>`
정지시 `docker stop <이름>`
Docker 정보 확인시 `docker ps`