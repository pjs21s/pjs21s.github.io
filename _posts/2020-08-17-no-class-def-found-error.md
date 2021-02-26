---
layout: post
title: "NoClassDefFoundError 해결 방안"
tags: Gradle
comments: true
---

NoClassDefFoundError에서 알 수 있듯이 클래스를 못찾아서 발생하는 에러다

필자의 경우에는 기본 `.gitignore`에 `.classpath`가 포함되어 있어서 필자가 Pull 받은 프로젝트에 `.classpath`가 없어서 발생한 오류 였다.

`./gradlew clean build` 명령어를 사용했지만 해결이 되지 않아서 build.gradle에 오른쪽 클릭을 한 후 `Update project configuration`을 눌러서 수정이 반영되면 정상적으로 classpath가 생성된다.

에러 메세지 : Exception in thread "main" java.lang.NoClassDefFoundError: org/springframework/boot/SpringApplication

<img src="/imgaes/noclassdeffound.png">