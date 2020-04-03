---
layout: post
title: "IntelliJ 인텔리제이 테스트 에러와 H2 DB 테이블 없음 해결"
tags: IntelliJ
comments: true
---

# IntelliJ Test Error
no tests found for given includes xxxx.someThingTest
자바 코드에는 문제가 없는데 테스트가 진행되지 않는다면 설정을 변경해야한다.
`Settings(Preference) > Build,Execution,Deployment > Build Tools > Gradle > Runner > "Run tests using"` 부분을 `Gradle`로 변경해주면 된다.

출처 : <https://stackoverflow.com/questions/55405441/intelij-2019-1-update-breaks-junit-tests>

# 빈 폴더 접히는 옵션 끄기

인텔리제이로 프로젝트를 처음 만들어서 사용하다보면 비어있는 폴더가 자동으로 접히는 경우를 볼 수 있다.
그래서 폴더 중간에 파일을 만들고 싶어도 할 수가 없게 된다.
이것 또한 설정을 변경하면 된다.

<img src="/images/intellij1.png">

출처 : <https://stackoverflow.com/questions/22001171/how-to-expand-folded-package-chain-in-intellij-idea>

# H2 DB 기본

인텔리제이 H2 DB 테스트를 처음할 때 이상하게 데이터가 나오지 않는 경우가 있다.
Select를 했는데도 아무것도 없다고 한다.
이 경우에는 아래 이미지와 같이 `JDBC URL`을 바꾸어주면 된다.

<img src="/images/intellij2.png">

출처 : <https://stackoverflow.com/questions/24116962/h2-console-cant-see-tables-created-by-java>