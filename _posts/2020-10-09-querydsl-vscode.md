---
layout: post
title: "QueryDSL Vscode에서 설정하기"
tags: Querydsl
comments: true
---

Gradle 버전 5이상 기준입니다.
이렇게 적고 빌드를 하면 src/main/generated 폴더가 생기고 Q로 시작하는 QueryDSL용 엔티티가 생성됩니다.

```groovy
plugins {
	id "com.ewerk.gradle.plugins.querydsl" version "1.0.10"
}
configurations {
	querydsl.extendsFrom compileClasspath
}
dependencies {
	//querydsl-jpa
	compile group: 'com.querydsl', name: 'querydsl-jpa', version: '4.3.1'
	// https://mvnrepository.com/artifact/com.querydsl/querydsl-apt
	compile group: 'com.querydsl', name: 'querydsl-apt', version: '4.3.1'
	// https://mvnrepository.com/artifact/com.querydsl/querydsl-core
	compile group: 'com.querydsl', name: 'querydsl-core', version: '4.3.1'
}
// querydsl 적용
apply plugin: "com.ewerk.gradle.plugins.querydsl"
def querydslSrcDir = 'src/main/generated'

querydsl {
    library = "com.querydsl:querydsl-apt"
    jpa = true
    querydslSourcesDir = querydslSrcDir
}

sourceSets {
    main {
        java {
            srcDirs 'src/main/java', querydslSrcDir
        }
    }
}

compileQuerydsl{
    options.annotationProcessorPath = configurations.querydsl
}
```

그리고 나서 이런 형태로 import해서 사용하는데 이상하게 컨트롤러 테스트를 하면 오류가 발생합니다.

```java
import xx.xxxx.xxxxxx.entity.QSubject;
import xx.xxxx.xxxxxx.entity.Subject;

@RequiredArgsConstructor
@Repository
public class CommonQueryRepository {
    private final JPAQueryFactory queryFactory;

    public Collection<Subject> findByName() {
        
        return queryFactory
                .selectFrom(QSubject.subject)
                .where(QSubject.subject.name.eq("Y"))
                .fetch();
    }
}
```
심지어 결과가 잘 오는데도 이런경우가 있습니다.
`java.lang.ClassNotFoundException: xx.xxxx.xxxxxx.entity.QSubject`
ClassNotFoundException QClass, QObject라는 형식으로 계속 에러가 발생합니다.

이 경우 Vscode는 에디터 자체에 문제가 있는게 아니라 익스텐션으로 사용하는 자바 프로그램과의 충돌로 인해 문제가 발생하는 경우일 가능성이 높습니다. 그래서 사용하고 있는 익스텐션을 다운그레이드 합니다.

<img src="/images/querydsl1.png">

<img src="/images/querydsl2.png">

이런 식으로 익스텐션에 있는 설정 버튼을 누르시면 다른 버전으로 쉽게 재설치할 수 있습니다.
`Java Test Runner : 0.22.4, Debugger for Java : 0.25.1` 버전으로 다운그레이드 합니다.
`gradle clean`을 해주시고 그래도 안되시면 Ctrl + Shift + P를 누른 후 `clear the java language server workspace`해서 다시 전체적으로 설정잡아주시면 잘 동작합니다.