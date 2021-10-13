---
layout: post
title: "Spring Validator 사용해보기"
tags: Spring
comments: true
---

단순히 HTML에서 `required`와 같은 방법으로 빈 값이 넘어가지 못하게 하는 방법도 있지만 이는 결국 적절치 못하다. 그렇기 때문에 서버에서 값을 검증하는 코드를 넣어보자
우선 `pom.xml`에 dependency를 추가해준다.
최신버전은 Maven 사이트에서 검색하면 나온다.

```xml
<!-- Hibernate Validator -->
		<dependency>
			<groupId>org.hibernate.validator</groupId>
			<artifactId>hibernate-validator</artifactId>
			<version>6.0.7.Final</version>
		</dependency>
<!-- https://mvnrepository.com/artifact/javax.validation/validation-api -->
		<dependency>
    		<groupId>javax.validation</groupId>
    		<artifactId>validation-api</artifactId>
    		<version>2.0.1.Final</version>
		</dependency>
```
그리고 사용하고자 하는 domain 코드에 넣어준다.

필자는 회원가입할때 검증을 시행할 것이므로 `UserVO`에 넣어줬다.

어노테이션 이름은 직관적이라 따로 설명은 안한다.

단지 필자가 본 예제에서는 `@NotBlank`나 `@NotEmpty`를 사용했는데 이게 만료가 된건지 분리가 된건지 작동을 안해서 `@NotNull`을 사용했다. `@Email`도 다른걸 import하면 작동을 안한다.

필자가 import한건 `import javax.validation.constraints.Email;`

물론 시간이 지나면 또 바뀔지 모르지만...

```java
public class UserVO {
	
	@NotNull
	@Email
	private String email;
	
	@NotNull
	@Length(min=3)
	private String name;
	
	@NotNull
	@Length(min=6)
	private String password;
}
```

`servlet-context.xml`에 오류일때 출력할 메세지를 한국어로 변경하기 위한 설정을 해야한다.

```xml
<beans:bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
		<beans:property name="defaultEncoding">
			<beans:value>utf-8</beans:value>
		</beans:property>
		<beans:property name="basenames">
			<beans:list>
				<beans:value>messages/messages_ko</beans:value>
			</beans:list>
		</beans:property>
</beans:bean>
```

이렇게 하고 나서 `src/main/resources`에 `messages`라는 폴더를 만들고 `messages_ko.property`를 만들어 한국어로 메세지가 출력되게 한다. 한국어로 원하는 메세지를 입력하면 저 형식으로 알아서 변환해준다.

`NotNull.userVO.name = \uC774\uB984\uC774 \uBE44\uC5B4 \uC788\uC2B5\uB2C8\uB2E4.`

그래서 아래와 같이 `어노테이션이름.domain이름.변수이름` 형식으로 작성해서 출력하고 싶은 메세지를 넣으면 된다.

```properties
NotEmpty.userVO.name = \uC774\uB984\uC774 \uBE44\uC5B4 \uC788\uC2B5\uB2C8\uB2E4.
Length.userVO.name = \uC774\uB984\uC740 \uCD5C\uC18C 3\uAE00\uC790 \uC774\uC0C1\uC774\uC5B4\uC57C \uD569\uB2C8\uB2E4.

NotNull.userVO.email = \uC774\uBA54\uC77C\uC774 \uBE44\uC5B4\uC788\uC2B5\uB2C8\uB2E4.
Email.userVO.email  = \uC774\uBA54\uC77C \uC591\uC2DD\uC774 \uC77C\uCE58\uD558\uC9C0 \uC54A\uC2B5\uB2C8\uB2E4.

NotNull.userVO.password = \uBE44\uBC00\uBC88\uD638\uAC00 \uBE44\uC5B4\uC788\uC2B5\uB2C8\uB2E4.
Length.userVO.password = \uBE44\uBC00\uBC88\uD638\uB294 \uCD5C\uC18C 6\uC790\uB9AC \uC774\uC0C1\uC774\uC5B4\uC57C \uD569\uB2C8\uB2E4.
```
그리고 나서 컨트롤러에 `@Valid`와 `BindingResult result`를 추가해서 여기서 사용한다는걸 알려주고 오류를 잡아온다. 그래서 if문을 통해 오류가 있으면 회원가입 창에 그대로 머물게 한다.

```java
@RequestMapping(value = "/register", method = RequestMethod.GET)
	public void RegisterGET(@ModelAttribute UserVO uservo, Model model) throws Exception {
	}
	
	@RequestMapping(value="/register", method=RequestMethod.POST)
	public String RegisterPost(@Valid UserVO uservo, BindingResult result, RedirectAttributes rttr) throws Exception{
		
		if(result.hasErrors()) {
			System.out.println("has errors");
			return "/user/register";
		}
		
		String hashedPw = BCrypt.hashpw(uservo.getPassword(), BCrypt.gensalt());
		uservo.setPassword(hashedPw);
		userservice.insertUser(uservo);
		rttr.addFlashAttribute("msg", "Registered");
	
		return "redirect:/user/login";
	}
```

그리고 jsp 파일 상단에 `<form:form>`을 사용하기 위한 태그를 추가해준다.

`modelAttribute`에는 domain의 이름을 적는데 `UserVO`가 아닌 `userVO`로 적어야 한다고 한다.

그리고 기존 `input`을 없애고 `<form:input>`으로 값을 받고 `<form:errors>`로 자동으로 에러가 있을때 메세지를 출력한다. `path`에는 VO에 있던 변수를 넣으면 된다.


```html
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<form:form modelAttribute="userVO" method="post">
	<div class="form-group row">
		<div class="col-md-6 offset-md-4">
		<form:input path="email" class="form-control" name="email" placeholder="이메일"/>
		<form:errors path="email"/>
		</div> 
	</div>

	<div class="form-group row">
		<div class="col-md-6 offset-md-4">
		<form:input path="password" type="password" id="password" class="form-control" name="password" placeholder="비밀번호"/>
		<form:errors path="password"/>
		</div>
	</div>
							
	<div class="form-group row">
		<div class="col-md-6 offset-md-4">
		<form:input path="name" type="text" id="name" class="form-control" name="name" placeholder="이름"/>
		<form:errors path="name"/>
		</div>
	</div>

	<div class="col-md-6 offset-md-4">
		<button type="submit" class="btn btn-primary">회원가입</button>
	</div>
</form:form>
```

이제 실행해보면 회원가입 창에서 적절한 값을 넣지 않았을 경우 메세지가 출력됨을 확인할 수 있다.