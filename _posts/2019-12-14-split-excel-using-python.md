---
layout: post
title: "pandas, numpy로 큰 엑셀 파일을 여러개로 나누기"
tags: Python
comments: true
---

오늘은 파이썬으로 큰 엑셀을 여러 파일로 나눠보겠습니다.
저는 기능 테스트를 위해 사용 되는 자료가 너무 커서 나누는데 사용했습니다.
대략적인 코드는 검색을 통해 찾으시면 비슷비슷합니다.

근데 예전 코드다 보니 오류가 있고 조금 수정을 했습니다.
`link[1:]`이 있어야 파일명 앞에 붙는 `u202a`와 같은 이상한 문자열을 제외할 수 있습니다.
`chunksize`에 넣는 수 만큼 나누겠다는 의미입니다.
총 40000열이 있고 chunksizerk 5000이면 대체적으로 5000에 맞게 잘라서 총 8개 파일이 생성됩니다.
사용하시고 안맞는 부분은 수정하셔서 사용하시면 됩니다.

```python
import pandas as pd
import numpy as np

chunksize = 5000
i = 0
link = '‪2019.xlsx'
link = link[1:]
df = pd.read_excel(link)
for chunk in np.array_split(df, len(df) // chunksize):
    chunk.to_excel('2019_file_{:02d}.xlsx'.format(i), index=False)
    i = i + 1

```