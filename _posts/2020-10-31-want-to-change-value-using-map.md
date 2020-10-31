---
layout: post
title: "Javascript Map으로 원하는 Object의 값을 조건에 따라 변경하기"
tags: Javascript
comments: true
---

자바스크립트의 Map, Filter 그리고 Reduce로는 많은 걸 할 수 있다.
그 중에서 Object 형식의 값을 조건에 따라 변경하는 코드 예시를 기록해두려고 한다.
삼항 연산자를 사용 했고 보기 좋게 줄바꿈도 했다.

Object 형식이라는건 아래와 같다.
아마 자바에서 보낼때는 Collection으로 보내면 이런 형식이 나온다.
DB에서 값을 가져오면 이런 형식으로 JS에서는 받는다.

```js
{
    studentName: 'Kim',
    studentAge: 10,
    studentClass: 'A',
    studentDream: 'Police'
}
```

이런 상황에서 studentName의 길이가 4이상인 경우 truncate를 하고 싶을 때
아래와 같은 코드를 사용하면 `...data`로 원본을 복사하면서 `studentName` 항목에만
값을 수정해서 넣게 된다.


```javascript
datas.map(data =>
data.studentName.length > 4
? { ...data, studentName: data.studentName.substring(0, 4) + "..." }
: {...data, studentName: data.studentName });
```