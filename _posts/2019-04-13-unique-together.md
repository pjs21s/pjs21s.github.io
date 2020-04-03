---
layout: post
title: "190413 django unique_together 정리"
tags: django
comments: true

---

CKEditor를 적용하고 글을 쓰려고 하니 자꾸 `UNIQUE constraint failed, IntegrityError`에러가 발생했다.
아무리 검색해도 나오지를 않아서 에러 페이지를 보고 자체적으로 코드를 따라가면서 봤더니
Post에 `unique_together`가 있는데 거기에 user가 들어있어서 포스트를 한 사람이 여러개를 작성할 수 없게되는 그런 오류였다.
문서를 보니 tuple 형태로 database level에서 제약을 걸어버리는 듯 했다.
어쩐지 Admin에서 글을 쓰려고 해도 유저가 중복됐다고 하면서 오류가 나더라

또한 `ManyToManyField`에는 `unique_together`가 적용되지 않는다고 한다.
그래서 사용하고 싶으면 through model을 써보라고 한다.

<https://docs.djangoproject.com/ko/2.2/ref/models/options/#unique-together>


