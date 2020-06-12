---
layout: post
title: "Quartz crontrigger 중복 실행 방지 어노테이션"
tags: quartz
comments: true
---

Java에서 스케줄러 기능을 사용할 때 quartz를 사용하는데
동작하고 있는 스케줄러의 작업이 설정해 놓은 주기보다 길어지면
중지가 되는게 아니고 그냥 주기에 맞춰서 스케줄러가 새로 생성된다.

1시간 주기가 있고 이미 돌고 있는 스케줄러가 1시간을 초과하는 경우
기존 스케줄러 + 새로 생성된 스케줄러 이렇게 총 2개가 동작하게 된다.
그래서 문제가 발생하는 경우가 생긴다.

이런 경우 아래 처럼 Job을 implements하고 있는 클래스 위에
어노테이션을 넣어주면 동시성 방지가 되어 문제 없이 동작하게 된다.
```java
@DisallowConcurrentExecution
public class MySchedulerOnlySeaice implements Job{
```