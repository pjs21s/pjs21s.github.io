---
layout: post
title: "Postgresql 에러"
tags: Postgresql
comments: true
---

잘 되던 postgresql이 갑자기 작동이 중단됐다.
`dbeaver connection refused connect` 처음에는 이런 단순한 오류였다.
그런데 계속 하다보니 오류 유형이 다 나오기 시작했다.

```
HINT:  Is another postmaster already running on port 5432? If not, wait a few seconds and retry.
psql: error: could not connect to server: could not connect to server:

No such file or directory /tmp/.s.PGSQL.5432

"root" execution of the PostgreSQL server is not permitted.

postgres: could not access the server configuration file
"/usr/local/bin/postgres/postgresql.conf": Not a directory
```

근본적인 해결책이 없는거 같아서 그냥 `brew uninstall postgres`하고 `/usr/local/var`에 있는
postgres 디렉토리까지 `rm -r postgres`로 지우고 다시 설치했다.
그랬더니 저번과는 다르게 `brew services start postgresql` 명령어로 실행하라고 해서 실행 후
권한 주고 데이터베이스 생성도 되고 DBeaver connection도 성공했다.