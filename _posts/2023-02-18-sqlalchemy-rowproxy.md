---
layout: post
title: "SqlAlchemy 결과 값이 키를 가지고 있는 이유"
tags: Python Sqlalchemy
comments: true
---

어느 날 Sqlalchemy를 사용하면서 궁금증이 생겼다.

예를 들어 아래와 같이 User 정보를 가져오는 orm이 있다고 가정한다.

```python
users = session.query(User).select_from(User).order_by(User.email).all()
```

users 결과를 보면 아래와 같이 나올 것이다.

```python
[
    <sqlalchemy.engine.result.RowProxy object at 0x7f9811b92e20>,
    <sqlalchemy.engine.result.RowProxy object at 0x7f9811b92eb0>,
    <sqlalchemy.engine.result.RowProxy object at 0x7f9811b92f40>,
    ...
]
```

그래서 결과를 명확하게 보여주기 위해 아래 함수를 사용하게 된다.

```python
user_dicts = [user._asdict() for user in users]
```

여기서 드는 의문은 RowProxy라는게 무엇이길래 _asdict()를 사용하면 `key`, `value`로 나올 수 있는걸까?

RowProxy에 대한 공식문서 원문은 아래와 같다.

https://docs.sqlalchemy.org/en/13/core/connections.html#sqlalchemy.engine.RowProxy

```
Represent a single result row.

The RowProxy object is retrieved from a database result, from the ResultProxy object using methods like ResultProxy.fetchall().

The RowProxy object seeks to act mostly like a Python named tuple, but also provides some Python dictionary behaviors at the same time.
```

여기서 핵심 문구는 아래와 같다.

```
The RowProxy object seeks to act mostly like a Python named tuple, but also provides some Python dictionary behaviors at the same time.
```

번역을 하면 아래와 같다.

```
RowProxy는 대개 Python tuple과 같으나 동시에 Python dictionary처럼 동작하기도 한다.
```

우리가 알고 있는 Python tuple은 아래와 같고 key가 존재하지 않는다.

```
t1 = (5, 6, 'c')
```

하지만 sqlalchemy를 통해 얻어온 결과 값은 변환을 하지 않아도 아래와 같이 값을 가져올 수 있다.

```python
users = session.query(User).select_from(User).order_by(User.email).all()

user_name = users.name
user_id = users.id
```

바로 결과 타입이 RowProxy이기 때문에 가능한 것이다.

그리고 asdict은 이 결과 값을 dictionary로 바꿔주는 역할을 담당한다.

SQL 결과 값이니 당연히 키를 가지고 있어야 하겠지만 정확히 왜 그런것이지 파악을 하니
더이상 헷갈리지 않아서 좋았고 값을 가져오지 못하는 에러를 보더라도
당황하지 않고 해결할 수 있게 되었다.