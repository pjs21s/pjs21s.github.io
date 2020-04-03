---
layout: post
title: "Python으로 DB에 CSV COPY_EXPERT 자동화"
tags: Python
comments: true
---

이번에는 수많은 CSV 파일이 있을 때 이걸 일일이 Data Input을 하는 게 아니라 Python을 이용해서
반복하며 알아서 데이터를 넣게끔 만들어봅시다.
저는 postgresql을 사용하고 있기 때문에 그에 맞는 코드를 사용했습니다.
환경이 다르신 분들은 핵심코드만 참고하시면 될 것 같습니다.

코드는 특정 폴더 안에 있는 모든 CSV 파일들을 반복하며 넣습니다.
그리고 시간이 꽤 소요되므로 작업이 완료되면 텔레그램으로 메세지가 오게 하였습니다.

만약 psycopg2를 설치하시는데 아래와 같은 오류가 발생하신다면 그냥 이렇게 설치하면 됩니다.
그리고 사용하실 때는 똑같이 import 해서 사용하시면 됩니다.

```python
pip install psycopg2-binary
import psycopg2
```

```python
ERROR: Command errored out with exit status 1
library not found for -lssl
```

query를 실행하기 위해 connect에 있는 cursor()를 선언한다.
copy_sql 변수에는 postgresql에서 csv로 데이터를 넣을 때 사용하는 COPY 문구를 선언한다.
From에 있는 stdin은 파일을 읽어올때 사용하는 기본 값으로 추정된다.

glob.glob()은 여러 파일들을 묶어서 리스트로 반환해준다.
for문을 통해 하나씩 받아서 읽기 모드로 열어서 copy_expert()에 넣으면 동작한다.
이때 주의해야 할 점은 commit을 for문 안에 해줘야 한다는 점이다.
commit을 다른 곳에 해주면 명령 자체는 실행이 되는데 commit을 하지 않아서 저장이 안 된다.
그러므로 데이터를 조회해보면 아무 변화가 없을 것이다.
그리고 완료되면 각각 close() 닫아주고 텔레그램으로 완료됐다는 메세지를 전송한다.
f.close()도 해야 한다고 하는데 필자가 사용할 때는 안해서 정확히 어디에 넣어야 할지는 모르겠다.

```python
import psycopg2
import glob
import telegram

my_token = '1234567890:********' # 숫자와 긴 영문 숫자 조합으로 이루어진 토큰
bot = telegram.Bot(token = my_token)

conn = psycopg2.connect("host='111.111.111.111' port='5432' dbname='namedb' user='name' password='password'")
curs = conn.cursor()
copy_sql = """
           COPY human_info (weight, major, number, address, etc) FROM stdin DELIMITER ',' CSV HEADER
           """
# HEADER라는 옵션을 넣으면 HEADER를 데이터라고 인식하지 않게 된다.
# HEADER를 안넣고 Data input을 시작하면 HEADER를 데이터로 인식해 올바르지 않은 데이터 타입이라고 경고를 한다.

for f in glob.glob('C:\\Desktop\\*.csv'):
    with open(f, 'r') as f:
        curs.copy_expert(copy_sql, f)
        print(f) # 만약 파일의 이름만 출력하고 싶으면 f.name 사용

    conn.commit() 
    print("Commit")

curs.close()
conn.close()
print("Success")
bot.sendMessage(chat_id = 123456789, text="작업완료") #chat_id 본인에 맞는 chat_id
```
