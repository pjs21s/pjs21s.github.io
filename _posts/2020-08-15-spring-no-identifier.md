---
layout: post
title: "no-identifier-specified-for-entity 스프링부트 에러 해결"
tags: Spring
comments: true
---

참고 : https://stackoverflow.com/questions/15320408/org-hibernate-annotationexception-no-identifier-specified-for-entity-even-whe

스프링부트에서 Entity 관련 파일을 코드를 생성할 때 @Id라는 어노테이션을 사용한다.
이때 `import org.springframework.data.annotation.Id;` 이게 아니라 `mport javax.persistence.Id;` 이걸 임포트해서 사용해야 오류가 발생하지 않는다. 스프링부트가 다 편한데 이런 어노테이션이 너무 많아서 가끔 시간을 버린다.
