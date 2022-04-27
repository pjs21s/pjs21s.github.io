---
layout: post
title: "Python pandas apply, lambda 코드 개선 및 성능 기록"
tags: Python
comments: true
---

회사에서 `pandas` 코드 작성을 도와드리다가 개인적으로 제대로 모르고 있다는 생각도 들었고 `apply`,`lambda`를 사용하면 코드는 깔끔하게 되는데 성능도 나아질까라는 궁금증이 생겨 시도해본 흔적을 기록합니다.

성능 측정은 간단하게 수행시간을 기록하는 방식을 사용했고 데코레이터를 정의하여 사용했습니다.
각 함수를 10번 돌린 후 소요시간 평균값을 내서 결과에 작성했습니다.
`jupyter`에서 사용하는 방법은 찾아보면 쉽게 나옵니다.

일단 코드를 쭉 나열 후 마지막에 결과에 대해 작성했습니다.

```python
import time
import contextlib


class Timer(contextlib.ContextDecorator):
    def __init__(self, name):
        self.name = name

    def __enter__(self):
        self.start = time.time()

    def __exit__(self, ex_type, ex_value, ex_traceback):
        self.end = time.time()
        operation_time = self.end - self.start
        print(f"{self.name}의 수행시간 : {operation_time}")
```

1. 아무것도 사용하지 않은 기본 코드  

```python
df = pd.read_csv('test_date.csv')

@Timer("basic_csv()")
def basic_csv(df):
    for date in df['date']:

        defined_compile = re.compile('오전|오후')
        index = defined_compile.search(date)
        i = index.start()
        ampm = date[i:]
        
for _ in range(10):
    
    basic_csv(df)
```

2. 정규식만 통합  

```python
df = pd.read_csv('test_date.csv')

@Timer("integrate_only_regex()")
def integrate_only_regex(df):
    for date in df['date']:

        index = re.search('오전|오후', date)
        i = index.start()
        ampm = date[i:]

for _ in range(10):
    integrate_only_regex(df)
```

3. 함수 사용하지 않고 apply, lambda 사용  

```python
df = pd.read_csv('test_date.csv')

@Timer("not_use_func()")
def not_use_func(df):

    defined_compile = re.compile('오전|오후')
    df['date2'] = df['date'].apply(lambda x: x[defined_compile.search(x).start():])

for _ in range(10):
    not_use_func(df)
```

4. 함수 정의하여 apply, lambda 사용  

```python
def check_ampm(stred_date):
    index = re.search('오전|오후', stred_date)
    i = index.start()
    payment_hms = stred_date[i:]
    
    return payment_hms

df = pd.read_csv('test_date.csv')

@Timer("apply_lambda()")
def apply_lambda(df):
    df['date2'] = df['date'].apply(lambda x: check_ampm(x))
    
    
for _ in range(10):
    apply_lambda(df)
```

5. 위에서 작성한 4가지 함수 성능 비교

- `10개 기준, for loop`

```python
# basic_csv()

avg : 5.307197570800781e-05

# integrate_only_regex()

avg : 8.704662322998047e-05
```


- `10개 기준, apply lambda`

```python
# not_use_func()

avg : 0.0007233142852783204

# apply_lambda()

avg : 0.0012561321258544923
```
        
- `1000개 기준, for loop`

```python
# basic_csv()

avg : 0.0011383056640625

# integrate_only_regex()

avg : 0.0016978025436401368
```

- `1000개 기준, apply lambda`

```python
# not_use_func()

avg : 0.0013390541076660155

# apply_lambda()

avg : 0.0016229391098022462
```

결론 : `apply`, `lambda`를 사용하는건 성능상의 이점보다는 코드를 파이썬스럽게 작성할 수 있다는 점이 더 장점이지 않나 싶습니다. 테스트 자체가 잘못되었거나 1000개밖에 돌려보지 않아서 그럴 수도 있고요. 테스트 결과 보다는 코드를 이렇게 저렇게 작성할 수 있는 점을 깨달았다는것에 더 의의를 두고 싶습니다.