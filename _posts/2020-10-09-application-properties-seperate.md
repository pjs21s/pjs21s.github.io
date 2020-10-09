---
layout: post
title: "간편하게 application.properties 개발 배포 모드 분리"
tags: Spring
comments: true
---

```java
//application.properties
spring.profiles.include=prod

//application-prod.properties 내용이 포함된다.
//server.port=8000
//server.compression.enabled=true
```

```java
//application-dev.properties
spring.profiles.active=dev

server.port=9000
spring.devtools.livereload.enabled=true
```


```java
//application-prod.properties
spring.profiles.active=prod

server.port=8000
server.compression.enabled=true
```

스프링부트는 기본적으로 application.properties를 찾아서 설정을 적용합니다.
이 때 기본 application.properties에 `spring.profiles.include=prod`로 사용 할 다른 properties를
가져오면 개발, 배포에 따라 다른 properties를 적용할 수 있습니다.
`application-***.properties` 파일 이름에 선언된 값과 파일안에 `spring.profiles.active=prod`로 선언 된 값을 가져와 포함시킵니다.

예를 들어 `application-test.properties`를 생성하고 `spring.profiles.active=test`로 선언해놓은 후에
기본 `application.properties`에서 `spring.profiles.include=test`로 가져다 사용하면
공통 `application.properties + application-test.properties`가 적용되는 것입니다.