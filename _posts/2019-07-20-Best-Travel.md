---
layout: post
title: "Codewars Best Travel 알고리즘"
tags: Algorithm
comments: true
---

문제: <https://www.codewars.com/kata/55e7280b40e1c4a06d0000aa>

문제가 너무 길어서 여기에는 적지 않습니다.

# 풀이

```python
from itertools import combinations as comb

def choose_best_sum(t, k, ls):
    bestsum = 0
    for ex in comb(ls, k):
        if sum(ex) <= t and sum(ex) > bestsum:
            bestsum = sum(ex)
            
    if bestsum:
        return bestsum
    return None
```

알고리즘 덕에 처음으로 `combinations`라는 기능을 사용했다.

처음에는 그냥 풀 수 있을줄 알고 고민해봤는데 아무리 중첩을 해도 수 많은 경우의 수를 리스트에서 뽑을 수 있나 싶어서 검색해보니 역시 제공해주는 기능을 사용해야 했다.

`combinations(ls, k)`는 `k`개의 수를 가지고 있는 조합을 `ls`에서 전부 뽑아준다.

하나의 리스트에서 모든 조합을 계산할 때 사용한다.

```python
item = [1, 2, 3, 4, 5]

from itertools import combinations

list(combinations(item, 2))
# [('1', '2'), ('1', '3'), ('1', '4'), ('1', '5'), ('2', '3'), ('2', '4'), ('2', '5'), ('3', '4'), ('3', '5'), ('4', '5')]
```

Best travel 알고리즘의 핵심은 경우의 수를 뽑아서 그 조합 내에 있는 수를 다 더해서 비교하는 것이다.

그렇기 때문에 `for ex in comb(ls, k):`로 각 조합을 하나씩 `ex`에 넣고 검증해야 한다.

`combinations`를 사용하는 것 말고는 크게 어려운 알고리즘은 아니었다.

```python
if sum(ex) <= t:
    sum(ex) > bestsum
    bestsum = sum(ex)
```

처음에는 if 문을 위와 같이 만들어서 자꾸 실패하는 게 몇개씩 생겼다.

큰 차이가 아니라고 무의식 중에 생각했었는데 

```python
if sum(ex) <= t and sum(ex) > bestsum:
	bestsum = sum(ex)
```

`and`로 묶고 다시 계산해보니 차이가 컸다...

미세한것 하나도 놓치지 말아야 겠다는 생각이 들었다.