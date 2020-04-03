---
layout: post
title: "이상한 문자 만들기 Python 프로그래머스"
tags: Algorithm
comments: true
---

# 예시

"try hello world" -> "TrY HeLlO WoRlD"

문자열 전체의 인덱스가 아니라 공백을 기준으로 나눈 단어별로 인덱스를 판단해야 한다.

짝수는 대문자로 홀수는 소문자로 바꾸어 출력한다.

```python
def solution(s):
    new_s = s.split(' ')
    answer = ''
    for word in new_s:
        for i, spell in enumerate(word):
            answer += spell.upper() if i % 2 == 0 else spell.lower()
        answer += ' '
    return answer[:-1]
```

처음에는 문자 전체를 하는 거로 풀었는데 아니어서 다시...

공백을 기준으로 한다는 의미는 공백을 기준으로 문자열을 나누면 된다는 것이다.

`s.split(' ')`를 통해 공백을 기준으로 문자열을 나눈다.

`split`는 ()안에 들어가는 것을 기준으로 문자열을 나누어 준다.

['try', 'hello', 'world'] 이렇게 나뉘어 있을 것이다.



`for word in new_s:`를 통해 한 단어씩 `word`로 받는다.

인덱스가 필요하기 때문에 `enumerate`를 사용하면 편하다.

그러면 인덱스도 자동으로 뽑아낼 수 있다.



받은 `word`를 `spell`로 나누어 한 글자씩 인덱스마다 받는다.

그래서 짝수면 `.upper()`를 이용해 대문자로 만들고 그렇지 않으면 `.lower()`를 통해 소문자로 만든다.

그걸 비어있는 `answer`에 더한다.



그리고 한 `word`의 변환이 끝나면 `' '`공백을 붙여 띄어준다.

이렇게 되면 마지막에도 공백이 생겨 오류가 발생한다.

그러므로 return을 할 때는 `answer[:-1]`로 -1까지 즉 마지막 공백을 제외하고 모든 문자를 출력하게 하면 된다.



원래는 if문을 전부 썼는데 Vue.js에서도 그렇고 한줄에 if, for문을 쓰는게 생각보다 보기에도 괜찮은 듯 해서 일단 if문만 한줄에 넣어봤다.

처음에는 `answer += spell.upper() if i % 2 == 0 else answer += spell.lower()`

그대로 옮기다 보니 위와 같이 작성했다.

그러니 `answer += spell.lower()`에서 오류가 발생했는데 다른 코드를 보니 중복되는 부분은 빼는듯 했다.

`answer += spell.upper() if i % 2 == 0 else spell.lower()` 제외 하니 오류 없이 잘 완성이 됐다.



참 일때 수행문 **if** 조건문 **else** 거짓일때 수행문