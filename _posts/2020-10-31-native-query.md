---
layout: post
title: "QueryDSL Union을 Stream.concat으로 대체하고 정렬기능 사용해보기"
tags: Jenkins
comments: true
---

QueryDSL은 BooleanExpression까지 지원해 주고 너무 편하지만 from subquery와 union 절은 사용할 수 없습니다.
물론 사용하지 않고 쿼리를 만들 수 있다면 좋지만 현실은 항상 그렇지 않습니다.
그래서 Stream.concat을 사용하여 Union을 대체하고 Stream.concat으로 만들어진 데이터를 Sort 하는 법 까지 기록합니다.

Native Query를 사용해도 되지만 업무 상 이유로 저는 Stream.concat을 사용했습니다.
단순히 Stream.concat을 사용하는 법은 아래와 같습니다.
두 컬렉션이 있고 stream 형식으로 변환하여 합치면 끝입니다.

```java
public Collection<studentDTO> getAllStudent(CommonDTO search, String sort) {
    Collection<studentDTO> grade = studentRepo.getStuentByGrade(search, sort);
    Collection<studentDTO> dream = studentRepo.getStudentByDream(search, sort);

    return Stream.concat(grade.stream(), dream.stream())
            .collect(Collectors.toList());
}
```

하지만 이렇게 하면 테이블에 데이터를 넣어서 정렬 기능을 넣어야 할 때 데이터가 올바르게 정렬되지 않습니다.
그 이유는 이미 정렬된 grade + 정렬된 dream 이렇게 합치기 때문에 각자 기준대로 정렬이 된 후 그 테이블끼리 합쳤을 뿐입니다.

예를 들면, studentAge라는 항목을 기준으로 정렬할 때 위와 같은 코드를 사용하면 아래와 같이 출력됩니다.

```sql
studentAge
9
13
15
16
8
10
11
12
```

각 컬렉션마다 studentAge 기준으로 정렬을 하고 더했기 때문에 전체 테이블의 데이터는 정렬이 되지 않습니다.
즉 DB에서 정렬한 것처럼 하려면 Stream.concat을 한 데이터에 대해 정렬을 수행해야 합니다.
그렇게 하면 아래와 같이 데이터가 정렬될 것입니다.

```sql
studentAge
8
9
10
11
12
13
15
16
```

이렇게 정렬하기 위해서 Comparator를 사용합니다.
아래와 같이 sorted()에 정렬하고 싶은 기준을 넣으시면 concat을 한 데이터에 정렬을 하여 리턴합니다.
저는 @Getter로 인해 자동으로 DTO에 대한 getter가 있어서 그걸 기준으로 정렬했습니다.
근데 이렇게 하면 또 동적으로는 정렬을 못하니 미리 선언을 해두고 변수에 따라 정렬을 하도록 수정해봅시다.

```java
return Stream.concat(grade.stream(), dream.stream())
.sorted(Comparator.comparing(studentDTO::getStudentAge))
.collect(Collectors.toList())
```

동적으로 사용하기 위해 Map 형식으로 아래와 같이 선언해 줍니다.
이러면 프론트에서 정렬을 위해 특정 컬럼을 눌렀을 때 해당 변수와 함께 API 요청만 해주면
그 변수를 받아서 Comparator를 사용할 수 있게 됩니다.

```java
private static Map<String, Comparator<studentDTO>> comparatorMap = Map.of(
    "age", Comparator.comparing(studentDTO::getStudentAge),
    "name", Comparator.comparing(studentDTO::getStudentName),
    "grade", Comparator.comparing(studentDTO::getStudentGrade),
    "dream", Comparator.comparing(studentDTO::getStudentDream),
    //"dream", Comparator.comparing(studentDTO::getStudentDream).reversed() -> 역순 정렬
);
```

주석처럼 파라미터를 받게 되면 아래와 같이 조건에 맞춰 동적 정렬을 수행할 수 있게 됩니다.

```java
// String sort = "age,ASC";
return sort.split(",")[1].equals("ASC")
? Stream.concat(grade.stream(), dream.stream())
.sorted(comparatorMap.get(sort.split(",")[0]))
.collect(Collectors.toList())
: Stream.concat(grade.stream(), dream.stream())
.sorted(comparatorReverseMap.get(sort.split(",")[0]))
.collect(Collectors.toList());
```

물론 이 방법은 그렇게 추천되는 방법도 아니라고 알고 있고 당연히 DB에서 정렬을 해서 가져와야 합니다.
하지만 현장에서는 다양한 이슈가 있고 어른들의 사정이라는 것으로 맞춰서 개발해야 할 때도 있습니다.
그런 경우 도움이 되시면 좋겠습니다.