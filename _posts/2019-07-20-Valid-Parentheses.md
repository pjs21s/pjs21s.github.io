---
layout: post
title: "Codewars Valid Parentheses 알고리즘"
tags: Algorithm
comments: true
---

Valid Parentheses는 이름에서 알 수 있듯이 괄호가 올바르게 닫혀있는지를 검증하는 알고리즘이다.

# 예시

```python
"()"              =>  true
")(()))"          =>  false
"("               =>  false
"(())((()())())"  =>  true
```

간단해 보이지만 실제로 풀때는 그렇지 않았다 ㅠ

# 풀이

```python
def valid_parentheses(string):
    stack = []
    for s in string:
        if (s == '('):
            stack.append(s)
        elif(s == ')'):
            try:
                stack.pop()
            except:
                return False
                
    if(len(stack) == 0):
        return True
    else:
        return False
```

우선 처음에는 개수로만 검증하는 알고리즘을 만들었다.

그렇다보니 ')(())(' 이런식으로 닫혀있으면 검증을 할 수 없었다.

괄호 개수는 맞지만 올바르게 닫혀있지 않아도 `False`를 출력해야 하기 때문이다.


조건을 만들어 보면 앞 괄호가 '(' 나오면 반드시 ')' 뒷 괄호가 존재해야 한다.

앞 괄호없이 뒷 괄호만 나오면 오류다.

찾아보니 스택을 이용해서 푸는게 많았다.


조건식을 이용해서 만약에 '('라면 스택에 추가한다.

그리고 ')'가 나오면 스택에 있는 '('를 삭제하면 된다.

왜냐하면 ')'가 나온다는 의미는 결국 이전에 '('가 있었다는 의미이기 때문이다.


올바른 괄호는 무조건 앞 괄호 뒤에 뒷 괄호가 나온다.

그렇기 때문에 앞 괄호를 스택에 저장하고 뒷 괄호가 나오면 앞 괄호를 삭제해서 스택에 아무것도 남아있지 않으면

`True`가 된다.

필자도 이게 글로만 봐서는 무슨 말인지 이해가 안되서 그리면서 이해했다 ㅠ


그래서 `for`문을 끝내고 스택의 길이가 0이면 `True`아니면 `False`를 출력했다.

```python
elif(s == ')'):
	try:
    	stack.pop()
    except:
        return False
```


이 부분은 ')'면 스택에 있는 '('를 `pop`하는 수식이다.

그런데 `except:`일때는 `return False`를 한다.

이 때는 스택에 아무것도 없는 경우를 의미한다.

즉 ')'인데 스택에 저장된 '('가 없다는 의미이므로 ')'가 먼저 나왔다는 것이다.

그러므로 올바르게 괄호가 닫혀 있지 않기 때문에 `False`다.