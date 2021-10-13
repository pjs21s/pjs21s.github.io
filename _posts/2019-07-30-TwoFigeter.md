---
layout: post
title: "Codewars Two fighters, one winner 알고리즘"
tags: Algorithm
comments: true
---

게임 알고리즘을 몇개 풀어보기로 했다.
그냥 알고리즘 푸는건 슬슬 재미가 없어졌다.
그리고 게임 알고리즘이 은근히 어렵다 종합적으로 생각해야하기 때문에
그래서 뭔가 새로운 방식으로 생각할 수 있지 않을까 싶었다.


<https://www.codewars.com/kata/two-fighters-one-winner/train/python>

# 풀이

```python
import math
def declare_winner(fighter1, fighter2, first_attacker):
    fighter1_round = math.ceil(fighter1.health/fighter2.damage_per_attack)
    fighter2_round = math.ceil(fighter2.health/fighter1.damage_per_attack)
    
    if(fighter1_round > fighter2_round):
        return fighter1.name
    elif(fighter1_round == fighter2_round):
        return first_attacker
    return fighter2.name
```

기본 논리는 어렵지 않다.
체력을 서로 비교해서 더 많이 남아있으면 승자가 된다.
근데 아무리 비교를 해도 또 몇군데서 오류가 났다.
찾아보니 다들 `ceil`을 쓰고 있었다.


`ceil`은 올림을 해주는 수학 함수다.

즉 위에서 계산하는 값이 거의 소수로 떨어지기 때문에 비교하기 애매한 부분이 있었을 것이다.
그걸 `ceil`을 이용해 올려서 깔끔하게 만들고 비교하는 것이다.
어차피 이 게임에선 `first_attacker`가 누구인가가 더 중요하다.
왜냐하면 거의 체력이랑 데미지가 비슷하게 설정이 되어 있기 때문이다.