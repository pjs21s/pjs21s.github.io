---
layout: post
title: "mapstruct cannot find implementation for 에러 해결"
tags: spring
comments: true
---

공식문서에 써있는 사용법은 아래와 같다.
```gradle
dependencies {
    ...
    implementation 'org.mapstruct:mapstruct:1.4.2.Final'
    annotationProcessor 'org.mapstruct:mapstruct-processor:1.4.2.Final'
}
```

근데 이렇게 하니 계속해서 에러가 발생했다.
정확히는 콘솔에 결과는 나오는데 테스트가 성공하지 못했다.
`cannot find implementation for`
몇 시간을 낭비하고 해결했는데 방법은 임포트 순서를 변경한다.

```gradle
    ...
	annotationProcessor 'org.mapstruct:mapstruct-processor:1.4.2.Final'
	implementation 'org.mapstruct:mapstruct:1.4.2.Final'
```

Update Project 후 테스트하니 바로 성공했다.