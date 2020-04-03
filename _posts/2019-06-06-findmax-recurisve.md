---
layout: post
title: "재귀 함수로 최대값 구하기 python"
comments: true
tag: Algorithm
---

```python
def find_max(a):
    n=len(a)
    if n==1:
        return a
    elif a[n-1]>=a[n-2]:
        del a[n-2]
    elif a[n-1]<=a[n-2]:
        del a[n-1]
    return find_max(a)
```



당연히 for문으로 찾아야 한다고 생각했었다.

if를 쓸수는 있지만 뭔가 if를 많이 쓰는건 이상하다고 느꼈고 그래서 하다가 안되서 찾아보니

stackoverflow에 해답이 있어서 보고 이해했다.

단지 의문은 왜 `a[n]>=a[n-1]`이게 아니고 `a[n-1]>=a[n-2]`이거 일까 싶어서 테스트를 해보니 바로 알았다.

`n=len(a)` 리스트 길이를 받아오는데 리스트의 인덱스는 길이에서 하나를 뺀 값 `n-1`이 최대값이기 때문이다.

그래서 `a[n]>=a[n-1]`로 하면 인덱스 범위가 넘어가서 에러가 발생한다.

역시 아직 연습이 한참 필요...ㅠ

