---
layout: post
title: "javac command not found 바로 해결하기"
tags: Java
comments: true
---

Java, Openjdk가 설치되어 있어도 `javac command not found`라는 에러가 발생할 수 있다.
이 경우 JAVA_HOME 설정이 번거로워지는데 `yum list java*jdk-devel` 명령어로 설치 가능한 리스트를 확인하여 버전에 맞게 `openjdk-devel`을 설치하면 `javac -version` 명령어도 사용가능하고 `which javac`로 경로를 찾아 JAVA_HOME을 설정하는데도 부담이 줄어든다. devel 버전을 설치하지 않아도 작동에 이상은 없지만 패키지를 빌드하거나 개발과 관련된 작업을 하려면 필요한 패키지다.
