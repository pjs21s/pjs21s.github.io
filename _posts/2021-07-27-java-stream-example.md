---
layout: post
title: "java stream, map 간단 실제 예제 및 테스트 방법 기록"
tags: Java
comments: true
---

아직 익숙하지가 않아서 자꾸 잊어서 기록합니다.
자바에서 스트림을 사용하여 값을 출력할 때 예전 작성 방법과 새로운 작성 방법 그리고
`JUnit` 테스트를 할 때 어떻게 테스트를 해야할지 기록합니다.

일단 기존 람다 스트림에서 각 요소를 가져오고 싶을때 아래와 같이 작성하였습니다.

```java
    testDTOs.stream().map(v -> v.getName()).forEach(item -> System.out.println(item));
```

아래와 같이 작성하면 화살표를 한번만 사용해도 되고 `TestDTO::getName` 덕분에 의미가 더 명확합니다.
`TestDTO`는 `Collection<TestDTO> testDTOs = testServiceImpl.findAll();`와 같이 변수가 아니라 미리 정의해놓은 DTO를 의미합니다.

```java
    testDTOs.stream().map(TestDTO::getName).forEach(item -> System.out.println(item));
```

스트림으로 받은 데이터끼리 테스트를 하고 싶다면 리스트로 바꾸면 편합니다.
리스트끼리 `assertEquals`에 넣으면 알아서 비교해줍니다.
순서가 애매하면 리스트 2개 전부 오름, 내림차순으로 정렬 후 비교하면 더 정확할 것입니다.

```java

@Test
public void findAllTest() throws Exception {
    Collection<TestDTO> testDTOs = testServiceImple.findAll();

    List<String> testCodes = Arrays.asList("ABC", "SDF", "EWT", "RYDR", "FHCV", "SDF");
    List<String> testCodesDTO = testDTOs.stream().map(TestDTO::getName)
            .collect(Collectors.toList());

    assertEquals(testCodesDTO, testCodes);
}

```