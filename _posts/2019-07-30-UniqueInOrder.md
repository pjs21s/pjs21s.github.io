---
layout: post
title: "Codewars Unique In Order 알고리즘"
tags: Algorithm
comments: true
---

# 예시

```python
unique_in_order('AAAABBBCCDAABBB') == ['A', 'B', 'C', 'D', 'A', 'B']
unique_in_order('ABBCcAD')         == ['A', 'B', 'C', 'c', 'A', 'D']
unique_in_order([1,2,2,3,3])       == [1,2,3]
```

즉 중복을 제외하여 리턴을 하면 되는 알고리즘이다.

물론 완벽하게 중복을 없앤다기보다는 연속적으로 나오는 같은 문자만 없애면 된다.



# 풀이

```python
def unique_in_order(iterable):
    results = []
    for item in iterable:
        if len(results) < 1 or item != results[len(results)-1]:
            results.append(item)
    return results
```

처음에는 중복이 되는 문자열을 다 체크해서 없앨려고 했는데 그게 아니었다.

핵심은 다음에 나올 문자가 전에 나온 문자와 다르면 `results`에 추가하는 것이다.



`iterable`에서 `item`에 하나씩 넣어주면서 만약 `len(results)`가 1미만의 길이면 일단 넣어준다.

`results`의 길이가 1미만이라는 의미는 즉 `results`에 아무것도 추가가 되어있지 않다는 것이다.

그렇기 때문에 첫번째 문자는 조건과 상관없이 무조건 추가한다.



그리고 현재 `item`이 이미 `results`에 있는 값과 다르면 추가해준다.

`len(results)`에서 1을 빼주는 이유는 리스트의 길이는 0부터 시작하기 때문에 맞춰줘야 한다.