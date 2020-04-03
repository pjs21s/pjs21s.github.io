---
layout: post
title: "Putty 세션(inactive) 자동 종료 방지 등 오류 정리"
tags: Error
comments: true
---

# Putty 세션(inactive) 자동 종료 방지
원문: <https://poppy-leni.tistory.com/entry/Putty-ssh-%EC%A0%91%EC%86%8D-%EC%9C%A0%EC%A7%80-%EC%8B%9C%ED%82%A4%EA%B8%B0>

Second between keepalives 부분이 0으로 되어있는데 10이나 혹은 본인이 원하는 값을 넣어주면 설정한 초마다
null packets을 보내 세션을 살아있게 한다.
근데 Windows를 기준으로 컴퓨터가 절전모드에 들어가면 이런 설정도 의미가 없기때문에 장시간 Putty Session을 유지시켜야한다면
절전모드부터 해제해야한다.

# java.io.IOException: No space left on device
`To prevent a memory leak, the JDBC Driver has been forcibly unregistered`
War방식으로 배포를 하는데 JDBC 드라이버 오류부터 너무 다양하게 오류들이 올라왔다.
그래서 제대로 못잡고 있다가 아래 명령어로 계속 보니까 어느새 공간이 부족하다고 올라왔다.
`tail -f /특정경로파일/logs/catalina.out`
`df -k` 용량 확인하는 명령어를 사용하니 Use% -> 100%가 떠서 공간 정리 후 배포하니 성공했다.

# psycopg2.OperationalError: could not connect to server: Connection refused (0x0000274D/10061)

Is the server running on host "192.168.0.11" and accepting
TCP/IP connections on port 15432?

이런 경우에는 15432가 맞는 포트인지를 물어보는것이므로 포트번호가 올바른지 확인하면 된다.
