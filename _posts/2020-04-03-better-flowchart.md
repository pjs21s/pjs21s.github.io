---
layout: post
title: "더 나은 순서도를 위한 다섯가지 팁"
tags: Flowchart
comments: true
---

1. 일관된 디자인 요소를 사용한다.
2. 한 페이지 안에 그린다.
3. 순서도의 흐름은 왼쪽에서 오른쪽으로 진행되게 한다.
4. 기존의 분기 아이콘이 아니라 split Path를 사용한다.
5. 리턴 라인은 순서도의 아래에 위치한다.

<img src="/images/flowchart1.png">

<img src="/images/flowchart2.jpg">

일관된 디자인을 사용한다는 건 일정한 규칙이 있다는 의미이다.
순서도를 그릴 때 필자의 경우 부정문으로 분기될 때 빨간색으로 표시를 해준다. 그리고 순서도가 길어질 경우 연관된 순서도라면 비슷한 색으로 시작하는 쪽을 표시해 준다.
일관된 디자인을 사용하지 않는다는 것은 사진으로 그린 순서도와 같다.
이미지로 순서도를 나타내면 언뜻 직관적일 것 같지만 오히려 이미지 하나하나를 파악하는데
시간이 더 걸리고 일정한 규칙이 없으므로 직관성이 떨어져 가독성도 떨어진다.

개인적으로 색을 잘 사용하면서도 난잡하지 않게 잘 만들었다고 생각하는 Immigration Roadmap

<img src="/images/flowchart3.png">

한 페이지 안에 그린다는 건 최대한 압축을 해서 그리라는 것이다.
물론 툴이 좋다면 그냥 순서도의 크기를 전체적으로 줄여서 한 페이지 안에 넣을 수도 있지만 그렇지 못한 경우라면 가로, 세로 면적을 잘 이용해서 그려야 한다.

<img src="/images/flowchart4.png">

오른쪽처럼 그린다면 화면 전체를 사용하지 않고 밑으로만 길어질 것이다.
하지만 왼쪽처럼 그린다면 최대한 면적을 활용하면서 그릴 수 있게 된다.

<img src="/images/flowchart5.png">

순서도의 흐름은 왼쪽에서 오른쪽으로 흘러가게 통일해야 한다.
선을 길게 빼기 싫다고 아래와 같이 하면 처음 순서도를 보는 사람은 헷갈릴 수 밖에 없게 된다.

<img src="/images/flowchart6.png">

기존의 분기 아이콘이 아니라 일반적인 처리 아이콘을 사용해서 분기한다.
첫번째로 기존의 분기 아이콘을 사용하면 흐름이 아래와 위로 나뉘면서 진행된다. 이 경우도 마찬가지로 순서도가 길어질 경우 흐름을 따라가는데 여러움이 생길 수 있다. 그렇기 때문에 일반적은 처리 아이콘을 사용하여 최대한 같은 방향을 보고 분기할 수 있도록 한다.
두번째로 순서도는 개발자뿐만 아니라 프로젝트와 관련한 직원들이 모두 보게 된다. 이 경우 분기 아이콘에 대해 알지못하면 오히려 직관성이 떨어지는 효과가 나타날 수 있기에 최대한 새로운 형태의 도형은 자제하는 것이 좋다.

<img src="/images/flowchart7.png">

리턴을 해야하는 경우 선은 겹치지 않고 아래로 그리도록한다. 즉 기존 로직 흐름과 겹치지 않는 쪽으로 선을 그려야한다. 지금 순서도 예시의 경우 국밥이라는 분기가 위로 그려져 있기 때문에 리턴 라인을 위로 그리면 복잡해질 것이다. 그러므로 아래로 그린다.

출처 : <https://www.smartdraw.com/flowchart/flowchart-tips.html>