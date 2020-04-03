---
layout: post
title: "Codewars Scramblies 알고리즘"
tags: Algorithm
comments: true
---

Scramblies 알고리즘 문제는 `s1`과 `s2`를 인자로 받아 `s1`에 있는 문자들로 `s2`의 단어를 만들 수 있으면 `True`아니면 `False`를 출력하는 문제다.

그래서 아래와 같은 방법으로 풀어봤는데 왠지 무작위 검사에서 자꾸 두문제씩 틀렸다.

방법을 바꿔봐도 안되길래 찾아보니 다른 답이 있었는데 사용한 기능이 많아 정리하려한다.

# 시도했던 풀이

```python
set_s2 = set(s2)
    set_s1 = set(s1)
    c = {}
    
    c = set_s1 & set_s2
    
    if set_s2 in c:
        return True
    else:
        return False
```

```python
def scramble(s1, s2):
    coll = set(s2)
    
    for i in s2:
        if i not in s1:
            return False
        else:
            coll.discard(i)
        
    if len(coll) <= 0:
        return True
    else:
        return False
```

# 정답

```python
def scramble(s1, s2):
    return all( s1.count(x) >= s2.count(x) for x in set(s2))
```

`set`은 집합을 만들어 주는데 특징으로는 순서, 중복 그리고 Key가 없다.

`dict`와 동일하게 중괄호를 사용하므로 중괄호만으로는 생성이 불가하다.

`s=set()`

`count(x)`는 x가 몇 개인지 조사하여 개수를 반환한다.

`all(x)`는 iterable 자료형 x를 인수로 받아 x가 모두 참이면 `True` 하나라도 거짓이면 `False`를 반환한다.



즉 `for x in set(s2)`를 통해 `s2`에서 문자열 하나씩 x에 넣어준다.

그리고 x라는 문자열이 `s1`과 `s2`에 있는지 체크한다.

`s1`에 들어있는 문자열의 개수가 `s2`와 같거나 더 많으면 `s1`의 문자열로 `s2`의 문자를 생성 가능하다는 의미이므로 `True`이다. 이 작업을 반복한다. 즉 `s2`를 만드는데 필요한 문자열이 `s1`에 더 많거나 같게 있으면 참이고 하나라도 없으면 거짓이다.
