---
layout: post
title: "Spring MVC 기본 crud 코드 정리"
tags: Spring
comments: true
---

제가 나름대로 공부한 내용을 정리해보겠습니다.

Mybatis + MySQL + Eclipse + Spring MVC 기반입니다.

연동과정에 대해서는 적어놓지 않았습니다.

`popit`이나 여러 블로그들을 보고 공부했습니다.

전체코드는 [깃허브](https://github.com/pjs21s/Spring_Capstone)에서 보실 수 있습니다. 



# 레이아웃 JSP

```html
<a href="/regist" class="btn btn-primary">글쓰기</a>
```

버튼을 만들고 같은 폴더에 `regist.jsp`를 생성합니다.

Bootstrap의 기능을 사용하고 있기때문에 CDN을 넣지 않으시면 제대로 동작하지 않으니 참고하세요.

```html
<!--regist.jsp-->
<body>
<form method="post">
	<div class="container form-group">
		<div class="row">
			<div class="col-lg-8">
			<input class="form-control" type="text" name ="title" placeholder="제목">
			<input class="form-control" type="text" name ="writer" placeholder="작성자">
			<textarea class="form-control" name="content"></textarea>
			<button type ="submit" class="btn btn-primary">등록</button>
			</div>
		</div>
	</div>
</form>
</body>
```

이번엔 작성한 글이 올라오는 `list.jsp`를 생성합니다.

```html
<a class="nav-link" href="list">글목록</a>
```



```html
<!--list.jsp-->
<body>
	<c:forEach items="${list}" var="boardVO">
	<div class="box card bg-white rounded">
		<div class="card-body">
			<p class="font-weight-bold"><a href='/read?bno=${boardVO.bno}'>${boardVO.title}</a></p>
            <div class="h5 m-0">${boardVO.writer}</div>
			<p class="font-weight-bold">${boardVO.content}</p>
			<p class="text"><fmt:formatDate pattern="yyyy-MM-dd HH:mm" value="${boardVO.regdate}"/></p>
			<p>${boardVO.viewcnt}</p>
		</div>
	</div>
</form>
</body>
```

게시물을 누르면 읽을 수 있게 `read.jsp`를 만듭니다.



```html
<!--read.jsp-->
<body>
<form>
<div class="form-group">
<input class="form-control" type="text" name ="bno" value ="${boardVO.bno}" readonly="readonly">
<h1 class="mt-4">${boardVO.title}</h1>
<p class="lead">${boardVO.writer}</p>
</div>
<textarea class="form-control" name="content" readonly="readonly">${boardVO.content}</textarea>

<button type="submit" class="btn btn-warning" formaction="modify" formmethod="get">수정</button>
<button type="submit" class="btn btn-danger" formaction="remove" formmethod="post">삭제</button>
<button type="submit" class="btn btn-primary" formaction="listAll" formmethod="get">목록</button>
</form>
</body>
```

삭제는 Url로 요청만 하면 되니 수정 페이지만 만들어줍니다.



```html
<!--modify.jsp-->
<!DOCTYPE html>
<html>
<body>
<form action="modify" method="post">
<div class="form-group">
글번호<input class="form-control" type="text" name ="bno" value ="${boardVO.bno}" readonly>
제목<input class="form-control" type="text" name ="title" value ="${boardVO.title}" >
작성자<input class="form-control" type="text" name="writer" value = "${boardVO.writer}">
<textarea class="form-control" name=content>${boardVO.content}</textarea>
<br>
<button type ="submit" class="btn btn-primary">완료</button>
</div>
</form>
</body>
</html>
```

UI는 완성했으니 이제 DB에 테이블을 만들어야 합니다.



MySQL에 이렇게 기본적인 내용만 만들어 주었습니다.

```
create table tbl_board(
bno INT NOT NULL AUTO_INCREMENT,
title VARCHAR(200) NOT NULL,
content VARCHAR(2000) NOT NULL,
writer VARCHAR(100) NOT NULL,
regdate TIMESTAMP NOT NULL default NOW(),
viewcnt INT DEFAULT 0,
PRIMARY KEY(bno)
);
```

# Package, BoardVO

그리고 이제 스프링에 패키지를 만들고 하나씩 필요한 것들을 만들어봅시다.

<img src="/images/springcrud1.png">

메인 폴더 오른쪽 클릭하여 패키지를 아래 사진의 이름과 같이 만들어줍니다.

<img src="/images/springcrud2.png">



그리고 `com.popit.domain`을 오른쪽 클릭 후 New -> Class 클릭하여 `BoardVO`라는 이름으로 `.java`파일을 하나 생성합니다.

그리고 나서 아래와 같이 코드를 작성합니다.

```java
public class BoardVO {
	private Integer bno;
	private String title;
	private String content;
	private String writer;
	private Date regdate;
	private int viewcnt;
}
```

작성 후 코드가 있는 화면에 대고 오른쪽 클릭을 하여 Source -> Generate getters and setters...를 클릭합니다.

<img src="/images/springcrud3.png">

<img src="/images/springcrud4.png">

Select All 후 아래에 Generate로 생성합니다.

마찬가지로 Source -> Generate toString()...을 눌러 생성해주면 아래와 같은 코드가 완성됩니다.

참고로 import 코드는 올리지 않았습니다.

`Ctrl + Shift + O`를 하거나 오류가 나는 곳에 마우스를 올리시면 쉽게 import 가능합니다.

```java
public class BoardVO {
	private Integer bno;
	private String title;
	private String content;
	private String writer;
	private Date regdate;
	private int viewcnt;
	
	public Integer getBno() {
		return bno;
	}
	public void setBno(Integer bno) {
		this.bno = bno;
	}
	public String getTitle() {
		return title;
	}
	public void setTitle(String title) {
		this.title = title;
	}
	public String getContent() {
		return content;
	}
	public void setContent(String content) {
		this.content = content;
	}
	public String getWriter() {
		return writer;
	}
	public void setWriter(String writer) {
		this.writer = writer;
	}
	public Date getRegdate() {
		return regdate;
	}
	public void setRegdate(Date regdate) {
		this.regdate = regdate;
	}
	public int getViewcnt() {
		return viewcnt;
	}
	public void setViewcnt(int viewcnt) {
		this.viewcnt = viewcnt;
	}
	@Override
	public String toString() {
		return "BoardVO [bno=" + bno + ", title=" + title + ", content=" + content + ", writer=" + writer + ", regdate="
				+ regdate + ", viewcnt=" + viewcnt + "]";
	}

}
```

# DTO(=VO)와 DAO의 차이

DTO(=VO)는 `getter`와 `setter`를 가진 클래스를 의미한다.

하지만 VO는 DTO와 다르게 readonly 속성을 가진다.

DAO는 데이터베이스에 접근하기 위해 만드는 클래스이다.

# BoardDAO, Mapper

이번에는 `com.popit.persistence`라는 패키지를 생성 후 Class가 아닌 Interface로 `BoardDAO.java`를 생성한다. 생성부터 만들거지만 나중에 추가하기 귀찮으니까 아래와 같이 미리 CRUD 코드를 다 추가해놓는다.

```java
public interface BoardDAO {
	public void create(BoardVO boardvo) throws Exception;
	
	public BoardVO read(Integer bno) throws Exception;
	
	public void update(BoardVO boardvo) throws Exception;
	
	void updateViewCnt(Integer bno) throws Exception;
	
	public void delete(Integer bno) throws Exception;
	
	public List<BoardVO> listAll() throws Exception;
}

```

그런 다음 `BoardDAOImple.java`를 만들어야 하는데 New -> Class를 누른 후 Interface 탭에서 Add를 눌러 방금 생성한 `BoardDAO`를 추가한다.

<img src="/images/springcrud5.png">

그러면 비어있는 함수를 다 만들어준다.

그 안에 로직을 구현할 코드만 넣어주면 된다.

`@Repository`는 데이터베이스와 관련한 클래스에 넣어준다.

```java
@Repository
public class BoardDAOImpl implements BoardDAO {
	
	@Inject
	private SqlSession session;
	
	private static String namespace = "com.popit.mapper.BoardMapper";
	
	@Override
	public void create(BoardVO boardvo) throws Exception {
		session.insert(namespace+".create", boardvo);
	}

	@Override
	public BoardVO read(Integer bno) throws Exception {
		return session.selectOne(namespace + ".read", bno);
	}

	@Override
	public void update(BoardVO boaradvo) throws Exception {
		session.update(namespace+".update", boaradvo);
	}
	
	@Override
	public void updateViewCnt(Integer bno) throws Exception{
		session.update(namespace + ".updateViewCnt", bno);
	}

	@Override
	public void delete(Integer bno) throws Exception {
		session.delete(namespace+".delete", bno);
	}

	@Override
	public List<BoardVO> listAll() throws Exception {
		return session.selectList(namespace + ".listAll");
	}
}
```

<img src="/images/springcrud6.png">

Mapper를 작성할때 `#{}`안에 있는 값은 본인이 선언한 변수를 의미한다.

그냥 써있는건 SQL에서 정의한 이름이다.

`title`은 SQL에서 정의한거 `#{title}`은 VO에서 선언한 값

그리고 `#{}`은 SQL Injection을 예방할 수 있다고 한다.

Mybatis를 통해 조금 더 편하게 DB를 다룰 수 있다.

물론 최근에는 JPA를 더 사용하는 것 같다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.popit.mapper.BoardMapper">

<insert id="create">
insert into tbl_board (title, content, writer)
values(#{title}, #{content}, #{writer})
</insert>

<select id="read" resultType="com.popit.domain.BoardVO">
select bno, title, content, writer, regdate, viewcnt
from tbl_board
where bno = #{bno}
</select>

<update id="update">
update tbl_board set title = #{title}, content = #{content}
where bno = #{bno}
</update>

<update id="updateViewCnt">
update tbl_board
set viewcnt = viewcnt + 1
where bno = #{bno}
</update>

<delete id="delete">
delete from tbl_board where bno = #{bno}
</delete>

<select id="listAll" resultType="com.popit.domain.BoardVO">
<![CDATA[
select bno, title, content, writer, regdate, viewcnt
from tbl_board
where bno > 0 order by bno desc, regdate desc
]]>
</select>
</mapper>
```



# BoardService

지금까지와 동일하게 `com.popit.service`라는 패키지를 만들고 `BoardService`라는 이름의 인터페이스를 생성합니다.

```java
public interface BoardService {
	public void regist(BoardVO boardvo) throws Exception;

	public BoardVO read(Integer bno) throws Exception;

	public void modify(BoardVO boardvo) throws Exception;

	public void remove(Integer bno) throws Exception;

	public List<BoardVO> listAll() throws Exception;
}

```

그리고 `BoardServiceImpl`이라는 클래스를 만들고 생성할때 `BoardService`를 Add해서 자동으로 생성해줍니다.

`@Service`는 비즈니스 로직과 관련한 곳에 넣어준다.

```java
@Service
public class BoardServiceImpl implements BoardService {
	
	@Inject
	private BoardDAO boarddao;

	@Override
	public void regist(BoardVO boardvo) throws Exception {
		boarddao.create(boardvo);
	}

	@Override
	public BoardVO read(Integer bno) throws Exception {
		boarddao.updateViewCnt(bno);
		return boarddao.read(bno);
	}

	@Override
	public void modify(BoardVO boardvo) throws Exception {
		boarddao.update(boardvo);
	}
	
	@Override
	public void remove(Integer bno) throws Exception {
		boarddao.delete(bno);
	}

	@Override
	public List<BoardVO> listAll() throws Exception {
		return boarddao.listAll();
	}
}
```



# BoardController

이제 Controller를 만들면 끝난다.

`@Controller`로 `BoardController`를 웹 컨트롤러로 사용하겠다고 선언한다.

`@RequestMapping`은 URL을 라우팅 해준다.

`@Inject`는 의존성 주입을 위해 사용하는데 `@Autowired`와 다른점은 자바에서 지원하는지 혹은 스프링에서 추가된 어노테이션이냐의 차이점 뿐이다.

`@RequestParam`을 통해 url에 받아올 값을 넣어서 사용할 수 있다.

`@pathvariable`라는 것도 있다. 한 어노테이션이 우위에 있다기 보다는 같이 사용한다고 한다.

`model.addAttribute("list",service.listAll());`의 원형은 아래와 같다.

`model.addAttribute(String name, Object value`) 그러면 저 안에 있는 "list"는 어디서 사용하는 걸까

`list.jsp`에 가보면 이런 코드가 있다. `items="${list}"` 이 코드는 반복을 하면서 리스트에 있는 값들을 가져온다. 즉 `addAttribute`에서 `name`을 정의하면 그걸 `jsp`에서 가져다 사용할 수 있게 되는 것이다.

```java
@Controller
@RequestMapping(value = "/")
public class BoardController {	

	@Inject
	private BoardService service;

	@RequestMapping(value= "/listAll", method = RequestMethod.GET)
	public void listAll(Model model) throws Exception {
		model.addAttribute("list",service.listAll());
	}

	@RequestMapping(value = "/regist", method = RequestMethod.GET)
	  public void registerGET(BoardVO boardvo, Model model) throws Exception {
	}

	@RequestMapping(value = "/regist", method = RequestMethod.POST)
	  public String registPOST(BoardVO boardvo, RedirectAttributes rttr) throws Exception {
		service.regist(boardvo);
	    return "redirect:/listAll"; 
	}

	@RequestMapping(value = "/read", method = RequestMethod.GET)
	  public void read(@RequestParam("bno") int bno, Model model) throws Exception{
		model.addAttribute(service.read(bno));
	  }

	  @RequestMapping(value = "/modify", method = RequestMethod.GET)
	  public void modifyGET(int bno, Model model) throws Exception {
	    model.addAttribute(service.read(bno));
	  }

	  @RequestMapping(value = "/modify", method = RequestMethod.POST)
	  public String modifyPOST(BoardVO boardvo, RedirectAttributes rttr) throws Exception {
	    service.modify(boardvo);
	    return "redirect:/listAll";
	  }

	  @RequestMapping(value = "/remove", method = RequestMethod.POST)
	  public String removePOST(@RequestParam("bno") int bno, RedirectAttributes rttr) throws Exception{
		  service.remove(bno);
		  return "redirect:/listAll";
	  }
}
```



# 참고 스프링의 기본 구조

<img src="/images/springcrud7.png">

