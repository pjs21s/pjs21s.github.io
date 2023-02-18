---
layout: post
title: "파이썬에서 메소드와 함수 차이"
tags: Python
comments: true
---

파이썬에서 함수와 메소드가 어떤 차이가 있는지 알아본다.

- Methods, 메소드
  - 클래스와 관련 있음
  - self가 필요

```python
class User(object):
    def eat(self):
        print("EAT")

    def __init__(self, name, gender, age):
        self.name = name
        self.gender = gender
        self.age = age

minsu = User()
minsu.eat()
```

- Functions, 함수
  - 독립적
  - self 필요하지 않음

```python
def get_user_name(user):
    return user.name

name = get_user_name("minsu")
print(name)
```

결국 주요한 차이점은 클래스와 같은 object와 연관이 있냐 없냐 유무

<img src="/images/method_and_function.png">