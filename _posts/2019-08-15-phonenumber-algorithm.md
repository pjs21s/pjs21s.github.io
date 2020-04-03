---
layout: post
title: "Python 핸드폰 번호 가리기, 자연수 뒤집어 배열로 만들기"
tags: Algorithm
comments: true
---

전화번호의 뒷 4자리를 제외한 나머지 숫자 전부를 *로 가린 문자열을 리턴하는 함수를 만들면 된다.

처음에는 숫자를 `replace()` 같은 함수를 사용해서 *로 바꿔야하나 싶었는데

단순히 생각하니 뒷 4자리만 제외하고 그냥 앞을 전부 *로 바꾸면 되는거였다.

그래서 앞 번호 개수를 센 다음에 그 숫자만큼 *을 붙여서 뒷 4자리와 함께 리턴했다.

```python
def solution(phone_number):
    i = 0
    delete_s = phone_number[:-4]
    for w in delete_s:
        i += 1
    answer = '*'*i + phone_number[-4:]
    return answer
```

`phone_number[:-4]`를 하면 -4까지 가져온다는 의미이므로 뒷 4자리 이전까지의 번호를 가져온다.

그래서 `for` 문을 이용해 몇개인지 숫자를 세서 i에 넣고 i만큼 *을 곱해서 뒷 4자리와 더했다.



근데 다른 사람이 한걸 보니 그냥 `len()`을 이용해서 바로 알아냈다...

```python
def solution(phone_number):
    answer = '*'*len(phone_number[:-4]) + phone_number[-4:]
    return answer
```



# 문제

자연수 n을 뒤집어 각 자리 숫자를 원소로 가지는 배열 형태로 리턴해주세요. 예를들어 n이 12345이면 [5,4,3,2,1]을 리턴합니다.



```python
def solution(n):
    list = [int(i) for i in str(n)]
    list.reverse()
    return list
```



`int`형은 iterable 할 수 없어서 `for`문을 돌리려면 `str()`로 빼서 `int()`로 더해야한다.

그리고 나서 어떻게 뒤집어야 할지 몰랐었는데 찾아보니 `reverse()`가 있었다..

그대로 뒤집고 리턴하면 끝