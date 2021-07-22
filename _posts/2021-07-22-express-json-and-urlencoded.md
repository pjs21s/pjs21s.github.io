---
layout: post
title: "express.json과 express.urlencoded"
tags: express
comments: true
---

이전에 express를 다루면서 조금 해맸던 기억이 나서 명확하게 알아보고자 찾아보다가 괜찮은 글이 있어서 번역해두었습니다.

1. 미들웨어는 어플리케이션 메소드에서 요청에 대한 처리와 응답에 대한 송신 `사이동안` 호출되는 메소드/함수이다.
2. `express.json()`, `express.urlencoded()`에 대해 생각할 때 POST 요청과 PUT 요청에 대해 제대로 생각해보자.
3. GET, DELETE 요청을 수행하는 데에는 `express.json()`, `express.urlencoded()`가 필요 없다.
4. POST, PUT 요청을 하기 위해서는 `express.json()`, `express.urlencoded()`가 필요하다. 왜냐하면 POST, PUT 요청 둘 다 서버에 객체 형식의 데이터를 **보내는 요청**을 수행하기 때문이다. 그리고 서버에 해당 요청(POST 혹은 PUT)의 본문 (`req.body`같은)에 포함 된 데이터(객체)를 수락하거나 저장하도록 요청한다.
5. Express는 request의 body에 있는 데이터(객체)를 다룰 수 있는 미들웨어를 제공한다.
a. `express.json()`는 들어오는 요청 객체를 **JSON 객체**로 인식하게 해주는 메소드다. `app.use(express.json());`과 같은 형태로 호출하여 미들웨어로 사용할 수 있다.
b. `express.urlencoded()`는 들어오는 요청 객체를 **strings 나 arrays**로 인식하게 해주는 메소드다. `app.use(express.urlencoded());`와 같은 형태로 호출하여 미들웨어로 사용할 수 있다.

원문 : https://stackoverflow.com/questions/23259168/what-are-express-json-and-express-urlencoded