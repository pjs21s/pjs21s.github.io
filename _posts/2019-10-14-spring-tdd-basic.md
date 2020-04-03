---
layout: post
title: "Spring JUnit TDD CRUD 기초"
tags: Spring
comments: true
---
참조: https://zorba91.tistory.com/106

```java
import javax.inject.Inject;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import com.popit.domain.BoardVO;
import com.popit.persistence.BoardDAO;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"file:src/main/webapp/WEB-INF/spring/root-context.xml",
		"file:src/main/webapp/WEB-INF/spring/appServlet/servlet-context.xml"})
public class BoardDAOTest {

	@Inject
	private BoardDAO boardDAO;
	
	@Test
	public void testCreate() throws Exception {
		for (int i = 1; i<= 10; i++) {
			BoardVO vo = new BoardVO();
			vo.setTitle(i+ "번째 제목");
			vo.setContent(i + "번째 본문");
			vo.setWriter("writer" + i);
			boardDAO.create(vo);
		}
	}

	@Test
	public void testRead() throws Exception {
		boardDAO.read(20).toString();
	}
	
	@Test
	public void testUpdate() throws Exception {
		BoardVO vo = new BoardVO();
		vo.setBno(20);
		vo.setTitle("제목");
		vo.setContent("내용123");
		boardDAO.update(vo);
	}
	
	@Test
	public void testDelete() throws Exception {
		boardDAO.delete(29);
	}
}
```



일단 TDD를 해보기 위해서는 Spring test라는 dependency를 추가해야한다.

근데 이상하게 오류가 발생하는데 그러면 `<scope>test</scope>`를 삭제해주면 된다.

이건 `test`할때만 동작하게 한다는 그런 의미인데 이게 있으니 이상하게 작동을 안한다.

그래서 아래와 같은 형식으로만 추가했다.

`junit`에 있는 `scope`는 그대로 test로 놔뒀다.

```xml
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-test</artifactId>
	<version>4.3.18.RELEASE</version>
</dependency>
```

`@ContextConfiguration` 이거 추가할때 `autowire`에 대한 오류가 발생한다.

근데 검색을 해도 너무 원인이 다양해서 해결이 안되다가 혹시나 해서 `.xml`을 `root-context.xml`, `servlet-context.xml` 둘다 참조 가능하게 했더니 해결됐다.



TDD 공부를 더하고 싶지만 일단은 기능 구현을 더 해야하므로 회원관련 테스트는 회원 생성만 해봤다.

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"file:src/main/webapp/WEB-INF/spring/root-context.xml",
		"file:src/main/webapp/WEB-INF/spring/appServlet/servlet-context.xml"})
public class UserDAOTest {
	
	@Inject
	private UserDAO userDAO;
	
	@Test
	public void testCreate() throws Exception{
		UserVO vo = new UserVO();
		vo.setPassword("j1234");
		String hashedPw = BCrypt.hashpw(vo.getPassword(), BCrypt.gensalt());
		
		vo.setEmail("j2u@ju.com");
		vo.setPassword(hashedPw);
		vo.setName("22it");
		userDAO.insertUser(vo);
	}
}
```

그냥 `setPassword`를 하면 암호화가 안되고 저장되니까 암호화 기능까지 넣어서 테스트 했다.

그리고 test라고 SQL에 값이 안들어가는게 아니고 실제로 다 들어가고 수정되고 삭제를 한다.

그래서 이 코드로 더미 데이터도 넣을 수 있다.



그리고 여담으로 `Controller`에서 `Service`를 선언할때 `final`을 같이 작성해주는데 이는 `Controller` 내부에서 다른요인으로 인해 수정이 불가능하게 만들어 준다.