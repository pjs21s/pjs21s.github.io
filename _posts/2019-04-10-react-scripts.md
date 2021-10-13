---
layout: post
title: "'react-scripts'은(는) 내부 또는 외부 명령, 실행할 수 있는 프로그램, 또는 배치 파일이 아닙니다."
tags: React
comments: true
---

에러
```
'react-scripts'은(는) 내부 또는 외부 명령, 실행할 수 있는 프로그램, 또는 배치 파일이 아닙니다.
```
 해결
```
npm update 후 npm start
```
```
npm update
npm start
```
 *추가

EPERM 에러 났을때도 npm update로 해결했음
```
EPERM: operation not permitted, 
```
