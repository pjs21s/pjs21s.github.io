---
layout: post
title: "프로그래머스 JAVA 모의고사 알고리즘"
tags: Algorithm
comments: true
---

이 알고리즘은 결국 혼자 힘으로 풀지 못하고 다른 분들이 풀어놓은 것들을 보고 풀었다. 근데 이 알고리즘을 풀면서 하나 깨달은 게 있어서 정리하려고 한다. 알고리즘을 만들 때 가장 먼 저해야 할 것은 패턴을 찾는 것이다. 패턴을 찾아내야 공식을 만들어 낼 수 있기 때문이다.

예를 들어 `person1[i%person1.length] == answers[i]`를 직접 풀어가면서 패턴을 찾아보자
`int[] person1 = {1,2,3,4,5};`이 정의되어 있고 `int[] answers = {1,2,3,4,5,1,2,3,4,5,1};`
이라고 가정한다.
그렇다면 하나씩 비교하기 위해 식은 이렇게 전개될 것이다.

```java
person1[0] == answers[0],
person1[1] == answers[1],
person1[2] == answers[2],
person1[3] == answers[3],
person1[4] == answers[4],
// 좌측은 인덱스 0부터 다시
person1[0] == answers[5],
person1[1] == answers[6],
person1[2] == answers[7],
person1[3] == answers[8],
person1[4] == answers[9],
// 좌측은 인덱스 0부터 다시
person1[0] == answers[10]
```

우항은 꾸준히 증가하고 있기에 비교를 위해서는 for문을 사용해야 할 것이다. 문제는 좌항을 어떻게 반복시킬 것인가.
`// 좌측은 인덱스 0부터 다시`부터 보면 좌항의 인덱스는 `0`이고 우항의 인덱스는 `5`이다.
그다음은 `1`이고 `6`이며 이와 같은 규칙으로 증가한다.
그러니 좌항 인덱스에 `+5`를 하면 우항 인덱스와 같으니 이런 식으로 공식을 만들면 되지 않을까? 하고 보니 좀 더 반복시킬 때 좌항 인덱스는 `0`이고 우항 인덱스는 `10`이 나온다. `+5`는 반복되는 패턴이 아니다.

여기서 알아야 할 법칙 중 하나는 이것이다.
`0(좌항) + 5(person1.length) = 5(우항)` 이렇게 규칙이 나온다는 의미는 곧
$$
\frac{5}{5}
$$
이런식으로 나눠서 나오는 나머지가 `0`이라는 의미와 같다.
`1(좌항) + 5(person1.length) = 6(우항)`은 곧
$$
\frac{6}{5}
$$
을 나눠서 나오는 나머지 `1`이라는 의미와 같다.

즉 덧셈이 아닌 나머지를 구하는 식으로 공식화하면 `좌항 인덱스 = i % person1.length`가 된다.

그래서 `person1[i%person1.length] == answers[i]`같은
공식이 나온다.

이해가 안되면 역시 펜을 들고 공책에 일일이 풀어보는 방법밖에 없다.

```java
import java.util.ArrayList;
class Solution {
    public int[] solution(int[] answers) {
        int[] answer = {};
        int[] person1 = {1,2,3,4,5};
        int[] person2 = {2,1,2,3,2,4,2,5};
        int[] person3 = {3,3,1,1,2,2,4,4,5,5};
        int cnt1 = 0, cnt2 = 0, cnt3 = 0, middle = 0, max = 0;

        for(int i = 0; i<answers.length; i++){
            if(person1[i%person1.length] == answers[i]) cnt1++;
            if(person2[i%person2.length] == answers[i]) cnt2++;
            if(person3[i%person3.length] == answers[i]) cnt3++;
        }
        
        middle = (cnt1 > cnt2) ? cnt1 : cnt2;
        max = (middle > cnt3) ? middle : cnt3;
        
        ArrayList<Integer> intList = new ArrayList<Integer>();
        
        if(max == cnt1) intList.add(1);
        if(max == cnt2) intList.add(2);
        if(max == cnt3) intList.add(3);
        
        answer = new int[intList.size()];
        
        for(int i = 0; i<answer.length; i++){
            answer[i] = intList.get(i);
        }
        return answer;
    }
}
```