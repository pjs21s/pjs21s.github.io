---
layout: post
title: "'vue'은(는) 내부 또는 외부 명령, 실행할 수 있는 프로그램 해결 방안"
tags: vue
comments: true
---

`'vue'은(는) 내부 또는 외부 명령, 실행할 수 있는 프로그램, 또는배치 파일이 아닙니다.`
`vue command not found`

vue 명령어를 사용하려는데 에러가 위와 같이 발생한다.

1. npm install -g vue-cli 하고 환경 변수 제대로 다시 설정

    일단 vue 환경 변수를 설정해도 해당 경로에 알맞는 파일이 없으면 명령어가 동작하지 않는다. 그래서 전역으로 vue-cli를 설치해준다.

2. 설정한 환경변수 경로에 vue-cli가 있음에도 명령어 실행시 에러가 발생하면 권한 문제일 수도 있다.
    Powershelll에 다음에 나오는 명령어를 실행해준다. `Set-ExecutionPolicy -ExecutionPolicy Unrestricted (-Scope CurrentUser)` 괄호는 생략하거나 알맞게 유저 범위를 설정해준다.