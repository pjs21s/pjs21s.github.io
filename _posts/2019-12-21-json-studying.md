---
layout: post
title: "Python 엑셀을 Json으로 변환 그리고 시행착오 정리"
tags: Json
comments: true
---

오늘은 Python을 이용해 엑셀인 `.xlsx`을 `json`으로 변환하는 코드를 포스팅합니다.
그리고 제가 겪었던 Json과 관련한 시행착오를 정리하려고 합니다.

우선 xlxs를 json으로 변환하는 코드는 아래와 같습니다.
기본적인 설명을 하면 `excel_path`에는 `JSON`으로 변환하고 싶은 데이터가 있는
엑셀파일의 경로를 입력합니다. 저는 Mac OS 기준으로 해놨기에 Windows를 사용하시면
주석에 적혀있는 형식처럼 입력하시면 됩니다.
`excel_path[1:]`은 경로 앞에 붙는 이상한 문자를 없애주기 위해 적어놓은 코드입니다.
경로 앞에 그런 문자가 붙지 않는다면 제외하셔도 됩니다.

`data['column'] = row_values[0]` 형식으로 적으면 JSON에 들어가는 첫 번째 데이터의 이름이
"column" : "This is Test" 형식이 됩니다. 전체 데이터는 Python 코드 밑에 첨부해두었습니다.

`with open('/Users/vors/Downloads/data.json', 'w+')` 코드가 새로 data.json이라는 파일을
만들어줍니다. 경로는 변경하셔서 사용하시면 됩니다.

```python
import xlrd
from collections import OrderedDict
import json

# Windows용 excel_path = '‪C:\\Users\\user\\Desktop\\rpa\\json_to_excel.xlsx'

excel_path = '‪/Users/vors/Downloads/json_to_excel.xlsx'
excel_path = excel_path[1:]
wb = xlrd.open_workbook(excel_path)
sh = wb.sheet_by_index(0)

data_list = []

for rownum in range(1, sh.nrows):
    data = OrderedDict()
    row_values = sh.row_values(rownum)
    data['column'] = row_values[0]
    data['url'] = row_values[1]
    data['top'] = row_values[2]
    data['left'] = row_values[3]
    data['width'] = row_values[4]
    data['height'] = row_values[5]
    data_list.append(data)

j = json.dumps(data_list, ensure_ascii=False)

with open('/Users/vors/Downloads/data.json', 'w+') as f:
    f.write(j)
```

<img src="/images/jsonstudying1.png">

```json
[
{"column": "This. Is Test", "url": "/Users/vors/Downloads/test1.png",
"top": 22.0, "left": 298.0, "width": 101.0, "height": 50.0},
{"column": "I wanna go to seoul!", "url": "/Users/vors/Downloads/test2.png",
"top": 298.0, "left": 22.0, "width": 50.0, "height": 101.0},
{"column": "Do u like Kimchi", "url": "/Users/vors/Downloads/test3.png",
"top": 50.0, "left": 298.0, "width": 22.0, "height": 101.0},
{"column": "No I don't like Kimchi", "url": "/Users/vors/Downloads/test4.png",
"top": 51.0, "left": 299.0, "width": 22.0, "height": 101.0},
{"column": "I don't wanna live in korea", "url": "/Users/vors/Downloads/test5.png",
"top": 52.0, "left": 300.0, "width": 22.0, "height": 101.0},
{"column": "This is My Life", "url": "/Users/vors/Downloads/test6.png",
"top": 53.0, "left": 301.0, "width": 22.0, "height": 101.0}
]
```

그런데 문제는 JSON 데이터를 엑셀로 뽑게 되면 처음 모습은 이렇게 잘 정렬된 모습이 아닙니다.
한 줄로 쭉 지저분하게 나옵니다. 그럼 이걸 보기 좋게 정렬을 해야 차후 조금 수정을 하던지
확인 작업을 할 수 있을 겁니다. 그래서 JSON 정렬 사이트와 Validator 사이트를 이용하면 됩니다.

정렬: <http://json.parser.online.fr>
Validator:  <https://jsonlint.com>

JSON 데이터를 만들었으니 이걸 Ajax로 받아서 출력하시는 분들이 있을 것입니다
근데 `Uncaught TypeError: Cannot read property 'data' of null` 이런 오류가 간혹 뜨는 경우도 있습니다.
필자의 경우 버튼을 누르면 JSON에서 데이터를 읽어와 출력하는 함수였는데
이상하게 빨리 누르면 Data를 못 읽어오고 천천히 하는 경우 정상작동 됐습니다.
그래서 데이터를 콘솔로 찍어보고 내린 결론은 Ajax는 비동기이기 때문에 데이터를 다 읽지 않았음에도
성공으로 처리하여 발생하는 문제라고 판단했습니다.
그래서 `async:false`로 동기식으로 작동하게 만들면 데이터를 전부 다 읽고 출력하는 기능이 작동하기에
문제없이 동작하게 됩니다. 문제는 이러면 왜 Ajax 코드를 쓰냐는 것...

그리고 JSON을 로컬에서 읽어서 사용하다 보면 JSON 파일 자체는 읽어오는데
데이터를 전부 못 읽어올 때가 있습니다. 이건 Cache 관련한 문제로 브라우저의 Cache를 지워도 소용이 없고
그냥 `cache:false`로 설정해 놓으면 문제 없이 데이터를 잘 가져옵니다.
혹은 json url 뒤에 `?version=1.0`과 같은 쿼리 스트링 형식을 붙여주면 캐시 상관하지 않고 데이터를 가져옵니다.
변경사항이 있을때 마다 버전 뒤에 숫자만 변경해주면 됩니다.
이것 때문에 몇 시간을 날렸는지...

그리고 JSON 안에는 주석 형태의 데이터를 놔두면 안 되고 쉼표나 따옴표 같은 미세한 문자가 잘못들어가면
전부 못 읽어오기 때문에 기능이 논리적으로 맞는다면 JSON에 들어간 데이터가 잘 맞는지 검토할 필요가 있습니다.
