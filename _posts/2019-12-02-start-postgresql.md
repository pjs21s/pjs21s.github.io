---
layout: post
title: "Postgresql 설치, 연결해서 사용 설정 DBeaver Script"
tags: Postgresql
comments: true
---

`brew install postgresql`

설치 후 나오는 글을 천천히 보면 설치된 경로를 아래와 같이 알려준다.
경로를 넣고 `postgres -D`를 하면 실행된다.

`postgres -D /usr/local/var/postgres`

실행을 하면 아래와 같은 경고가 나올 수 있는데 그러면 유저를 생성하면 된다.

`FATAL:  role "postgres" does not exist`

`psql postgres`

'postgres'라는 이름을 가진 유저를 생성

`create user postgres password 'postgres';`

'post_prac'라는 이름을 가진 DB를 생성

`create database post_prac encoding 'utf-8';`

'postgres' 유저에게 'post_prac' DB의 주인으로 설정

`alter database post_prac owner to postgres;`

'post_prac' DB에 관한 권한을 'postgres'에게 부여

`grant all on database post_prac to postgres with grant option;`

DBeaver 연결해서 사용하기
<img src="/images/postgres1.png">

Script에서 Query Execution을 하려면 활성화된 DB에 연결을 해야합니다.
<img src="/images/postgres2.png">
