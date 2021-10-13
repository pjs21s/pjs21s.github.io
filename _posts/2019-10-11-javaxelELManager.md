---
layout: post
title: "AWS 배포 오류 javax/el/ELManager"
tags: Error
comments: true
---

Tomcat7을 사용하고 있고 AWS에 Maven통해서 배포를 하고 있다.
오늘 validator 잘 사용해서 기분이 좋았는데 배포를 하고 사이트를 접속해보니 
`java.lang.NoClassDefFoundError: javax/el/ELManager` 이런 오류가 났다.
검색해보니 Tomcat7에 있는 버전이랑 뭐랑 호환이 안되서 충돌이 난다 어쩐다 말이 많다.
첫번째 해결방법은 만약 추가가 안되어있다면 아래 dependency를 추가하는 것이다.
근데 나는 이걸해도 안됐다.

```xml
<dependency>
    <groupId>javax.el</groupId>
    <artifactId>javax.el-api</artifactId>
    <version>3.0.0</version>
</dependency>
```

성공한 방법은 Hibernate Validator의 버전을 낮추는 것이다.

원래는 6버전을 사용하고 있었는데 이걸 낮추니까 바로 사이트 접속도 되고 회원가입에서 검증하는 기능도 잘 작동했다.

```xml
<!-- Hibernate Validator -->
<dependency>
	<groupId>org.hibernate</groupId>
	<artifactId>hibernate-validator</artifactId>
	<version>5.4.3.Final</version>
</dependency>
```
참고: https://stackoverflow.com/questions/45841464/java-lang-noclassdeffounderror-javax-el-elmanager

