---
layout: post
title: "WeightForWeight 알고리즘을 풀며... 느낀점"
tags: Algorithm
comments: true
---

codewars에서 풀었던 알고리즘을 이해하기 위해 발버둥 친 기록입니다.

처음에 생각했던 풀이는 이렇습니다.
1. HashMap Key에는 몸무게를 넣고 Value에는 가중치를 넣는다.
2. Value를 기준으로 정렬한 후 Key를 출력한다.
3. 출력된 값을 StringBuilder에서 합쳐 리턴한다.

하지만 위를 수행해보니 발생한 문제는 아래와 같습니다.

1. HashMap이라서 중복되는 값이 있으면 자동으로 삭제된다.
2. Weight의 가중치가 같은 경우에는 몸무게 값을 String으로 비교해야 하는데 비교를 하지 않았다.

그래서 풀이 자체가 잘못되었다고 판단하고 다른 사람들의 풀이를 찾아봤습니다.

```java
import java.util.Arrays;
import java.util.stream.Collectors;

public class WeightForWeight {

    public static String orderWeight(String string) {
        return Arrays.stream(string.split(" ")).sorted((String a, String b) -> {

            int asum = Arrays.stream(a.split("")).mapToInt(Integer::parseInt).sum();
            int bsum = Arrays.stream(b.split("")).mapToInt(Integer::parseInt).sum();

            if (asum == bsum) {
                return a.compareTo(b);
            } else {
                return Integer.compare(asum, bsum);
            }
        }).collect(Collectors.joining(" "));
    }

    public static void main(String[] args) {
        String params = "2000 10003 1234000 44444444 9999 11 11 22 123";
        System.out.println(orderWeight(params));
    }
  
}

```

코드 자체가 익숙하지 않은 코드라서 하나하나씩 짚어 가봤습니다.
우선 아래와 같이 파라미터가 오면 숫자를 비교하기 위해서는 분리해야 합니다.
`"2000 10003 1234000 44444444 9999 11 11 22 123"`

그래서 `string.split(" ")`는 자연스럽게 생각할 수 있습니다.

`string.split(" ")`는 배열입니다.
배열이면 `Arrays.stream`에 넘겨서 사용할 수 있습니다.
어떻게 사람들은 `Arrays.stream`을 사용할 생각을 할 수 있는걸까?라는 의문이 들어 생각을 해봤습니다.

문제를 읽어 알 수 있는 점을 먼저 정리해봅니다.
1. 정렬을 해야 한다. 오름, 내림차순이 아닌 임의로 만든 기준을 사용한다.
2. 배열을 사용 한다.

잘 생각해보면 1번에서 저희는 `Comparator`를 사용해야 한다는 점을 알 수 있습니다.
`Comparator`를 사용한다는 것은 정렬을 해야한다는 것이고 2번에서 배열을 사용한다고 했으니
`Arrays.stream`에서 `sorted`를 사용하면 되지 않을까?

라는 식으로 생각이 이루어지지 않을까 싶습니다.
값 사이마다 공백을 넣는 것도 `StringBuilder`가 아닌 `joining`을 사용하는 이유도
처음에 `stream`을 사용했으니 자연스럽게 연결되지 않을까 싶습니다.

물론 추측입니다 ㄷㄷ
이렇게 알 수 있도록 문제를 계속해서 풀어봐야 하는게 아닌가 싶습니다.