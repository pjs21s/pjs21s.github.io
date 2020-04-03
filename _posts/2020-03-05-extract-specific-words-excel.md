---
layout: post
title: "특정 단어가 들어간 행만 추출하는 자동화 python"
tags: Python
comments: true
---
<img src="/images/extract1.png">

위와 같은 내용의 엑셀이 있다고 가정합니다.
저희는 여기서 `.do`가 포함된 행만 추출하려고 합니다.

추출에 성공하면 아래와 같은 결과가 나올 것입니다.

<img src="/images/extract2.png">

코드는 아래와 같습니다.
`errors="ignore"`의 역할은 python에서 발생하는 인코딩 오류를 잡기 위함입니다.
쓸데없이 발생하는 경우가 있기에 넣어주었습니다.

```python
import xlrd
sheet_data = []
wb = xlrd.open_workbook('/Users/vors/Desktop/TEST.xlsx')
p = wb.sheet_names()
for y in p:
   sh = wb.sheet_by_name(y)
   for rownum in range(sh.nrows):
      sheet_data.append((sh.row_values(rownum)))
found_list = []
rows_to_be_saved = []
for i in sheet_data:
  if '.do' in str(i[0]):
    print(i)
    found_list.append(i)
  else:
    rows_to_be_saved.append(i)

founded = ''.join(str(x) for x in found_list)
text_file = open("/Users/vors/Desktop/TEST.txt", "w",encoding='UTF-8', errors="ignore")
text_file.write(founded, )
text_file.close()
```