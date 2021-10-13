---
layout: post
title: "Codewars Persistent Bugger 알고리즘"
tags: Algorithm
comments: true
---

Persistent Bugger 알고리즘은 한자리 수가 나올때까지 계속해서 수끼리 곱해나가는 알고리즘이다.

예시를 보면 이해가 쉽다.

# 예시

```python
persistence(39) => 3  # Because 3*9 = 27, 2*7 = 14, 1*4=4
                       # and 4 has only one digit.
persistence(999) => 4 # Because 9*9*9 = 729, 7*2*9 = 126,
                       # 1*2*6 = 12, and finally 1*2 = 2.
persistence(4) => 0   # Because 4 is already a one-digit number.
```

```python
persistence(39) # returns 3, because 3*9=27, 2*7=14, 1*4=4
                 # and 4 has only one digit
persistence(999) # returns 4, because 9*9*9=729, 7*2*9=126,
                  # 1*2*6=12, and finally 1*2=2
persistence(4) # returns 0, because 4 is already a one-digit number
```

39라는 수가 있으면 처음에 3과 9를 곱한다. 3 * 9 = 27

나온 결과값인 27은 한자리 수가 아니기에 한번 더 곱한다.

2 * 7 = 14가 나왔고 한번 더 곱한다.

1 * 4 = 4 그래서 총 곱한 횟수를 결과값으로 출력하는 함수를 만들면 된다.

여기서는 총 3번을 곱했기에 3이 나와야한다.


# 풀이

```python
def persistence(n):
    if n < 10:
        return 0
    num_str = str(n)
    result = 1
    for i in num_str:
        result = result * int(i)
    return 1 + persistence(result)
```

이 함수는 계속 곱해지다가 끝나기 때문에 재귀 함수를 사용해야한다.

재귀 함수는 종료 조건이 있어야 하고 계속 반복되야 한다.


n이 한자리 수인 경우 계산을 멈추기 때문에 `if n < 10`라는 조건일 때 `return 0`을 함으로써 종료 조건을 만든다. 실제로 한자리 수를 넣으면 곱셈을 하지 않기에 계산 횟수는 0회를 출력해야한다.


39와 같은 두자릿수 숫자를 각각 곱하려면 한자리씩 분리해야 하는데 숫자 상태에서는 이게 불가능하다.

그래서 `str(n)`으로 우선 문자로 만들어 `num_str`에 넣는다.

곱셈을 해야하기 때문에 `result`는 0이 아닌 1로 초기화를 한다.

처음에 0을 넣었다가 왜 계산이 안되지 이러고 있었다...


`for i in num_str:`을 통해 39와 같은 두자릿수 이상의 수가 입력됐을 때 한자리씩 나누어 i에 넣어준다.

i = 3이 들어가면 `result = result * 3`이렇게 곱해져야 할 것이다.

그런데 지금 i는 문자이기 때문에 다시 한 번 `int(i)`를 통해 숫자로 변경해줘야 한다.

그러면 i = 3, i = 9일때 곱하면서 27이라는 결과를 만든다.

그리고 그 결과 값을 바로 `persistence(result)`로 해서 다시 곱할수있게 만든다.

앞에 1을 더하는 이유는 곱셈 횟수를 누적시키기 위해서다.


이렇게 계속 곱하다 보면 마지막에 `result`에는 4라는 값이 들어간다.

`return 1 + persistence(4)`재귀 함수니까 다시 함수를 시작해 `if n <10: return 0`에 의해서 종료된다.

근데 살짝 의문인점은 n = 4니까 return 0이 나와야 하는거 아닌가 싶지만

아마 앞에 쌓여있던 3이라는 return 값을 먼저 반환해주고 return 0으로 종료 하니까

상관없는게 아닌가 싶다.

추측이지만... 순서가 중요하니까...