---
layout: post
title: "Spring 게시판 URL 이해하기"
tags: Spring
comments: true
---

게시판을 만들면서 모든 게시물의 리스트를 보여주는 `list.jsp`를 만들었다.

여기서 `title`을 클릭하면 해당 게시물로 이동할 수 있게 아래의 코드가 있다.

```java
<a href='/read${pageMaker.makeSearch(pageMaker.cri.page)}&bno=${boardVO.bno}'>
${boardVO.title}</a>
```

검색기능을 만들면서 수정하다 보니 본문으로 이동하는 링크가 꽤 복잡해졌다.
이 자체로는 문제가 없지만 필자는 `mainlist.jsp`라는 걸 만들고 싶었다.
조회수 기준으로 인기가 있는 게시물을 메인 페이지에 출력해주는 jsp 파일이다.
문제는 저 링크를 그대로 가져다 사용하면 게시물로 이동을 못한다.
그러면 다른 jsp에서는 어떻게 게시물 본문으로 이동가능하게 만들까?

결론부터 이야기 하자면 `<a class="card-link" href='/read?bno=${boardVO.bno}'></a>` 이렇게 하면 된다.  어떻게 저 패턴인지를 알았냐면 Controller에 정의된 항목을 보면 알 수 있다.

```java
@RequestMapping(value = "/read", method = RequestMethod.GET)
	  public void read(@RequestParam("bno") int bno, Model model) throws Exception
```

`read`라는 기능을 담당하는 Controller에는 위와 같이 정의되어 있다.
저 말은 즉 `/read`후에 `RequestParam`을 통해 `bno`를 가져온다는 것이다.
그러므로 `bno`만 있으면 `read`기능을 사용할 수 있다는 것이다.
그래서 저렇게 만들어 주면 다른 페이지에서도 `read`기능을 사용해 게시글 본문으로 갈 수 있다.

# ${pageContext.request.contextPath}

그 다음 문제는 jsp끼리 이동할때 발생하는 문제다.

`header.jsp`에 원래는 이렇게 정의를 해두었다.

그러면 리스트를 눌렀을때는 `/list`이렇게 갈것이고 회원가입을 누르면 `/user/register`이렇게 URL을 찾아갈 것이다. 근데 문제는 회원가입을 누른 후 리스트를 누르면 주소가 `/user/list`로 이상하게 꼬여서 원래 리스트 페이지를 찾아가지 못한다. 이걸로 몇일을 고민하다가 영어로 그냥 검색하니 바로 해결책이 나왔다.

```html
<a class="nav-link" href="list">리스트</a>
<a class="nav-link" href="/user/register">회원가입</a>
```
`${pageContext.request.contextPath}` 이걸 사용하면 문제는 해결된다.

위 코드의 의미는 항상 Application root를 반환한다.

즉 회원가입 페이지에서 리스트 페이지를 눌러도 이제는 `/user`가 앞에 나오지 않고 루트 `/`가 나와서 주소가 꼬이지 않고 `/list`로 이동시켜준다.

```html
<a class="nav-link" href="${pageContext.request.contextPath}/list">리스트</a>
<a class="nav-link" href="/user/register">회원가입</a>
```