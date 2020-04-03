---
layout: post
title: "Python으로 엑셀에서 원하는 칼럼들의 데이터만 추출하기"
tags: Python
comments: true
---

코드 자체는 간단해서 따로 설명은 적지 않겠습니다.

```python
import pandas as pd
import glob, os

parent_dir = ("C:\\Users\\user\\Desktop\\rpa\\")
for f in glob.glob(os.path.join(parent_dir,'*.csv')): # 폴더 안에 있는 모든 csv 가져옴
    csv_data = pd.read_csv(f)
    new_csv = csv_data[['id', 'first_name', 'last_name', 'email', 'gender', 'ip_address', 'have_money', 'alive_dead']]
    # 써놓은 헤더들의 데이터만 살리고 나머지는 삭제
    print(f)
    new_csv.to_csv('{}'.format(f), index=False)
```