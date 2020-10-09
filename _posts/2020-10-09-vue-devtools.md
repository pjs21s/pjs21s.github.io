---
layout: post
title: "vue-devtools 로컬에서 동작하지 않을 때"
tags: vue
comments: true
---


Vue.config.devtools = true 동작하지 않을때 로컬에서, html 파일로 간단하게 코드를 짤 때 vue-devtools를 사용하고 싶은데 동작하지 않는 경우가 있다.

[https://github.com/vuejs/vue-devtools](https://github.com/vuejs/vue-devtools)

To make it work for pages opened via file:// protocol, you need to check "Allow access to file URLs" for this extension in Chrome's extension management panel.

공식문서에 위와 같이 나와있듯이 로컬에서 사용하기 위해서는 크롬 확장 프로그램 관리에서 별도로 설정을 해야한다.

`오른쪽 상단에서 더보기 더보기 다음 도구 더보기 다음 확장 프로그램을 클릭`

<img src="/images/vuedevtools.png">