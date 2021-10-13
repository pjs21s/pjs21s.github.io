---
layout: post
title: "Spring 회원생성, 수정, 탈퇴 그리고 로그인 코드 정리"
tags: Spring
comments: true
---

포스팅의 길이를 줄이기 위해 생략해도 무방한 코드는 지웠습니다.
그렇다보니 UI가 이상하게 나올수도 있고 그대로 복사 붙여넣기를 하시면 작동이 안될수도 있습니다.
가능한 직접 보고 타이핑하시는게 좋습니다.
전체코드는 [여기](https://github.com/pjs21s/Spring_Capstone)에 있습니다.
저번 기본 crud 포스팅에서 설명한 내용은 최대한 생략합니다.

# JSP

```html
<!--register.jsp-->
<html>
  <body>
    <div class="card">
      <div class="card-header border-0">회원가입</div>
        <div class="card-body">
       <form method="post">
        <div class="form-group row">
          <input type="text" id="email_address" class="form-control"
          name="email">
        </div>
         <div class="form-group row">
          <input type="password" id="password" class="form-control"
          name="password">
          </div>		
          <div class="form-group row">
            <input type="text" id="name" class="form-control"
            name="name">
          </div>
          <button type="submit" class="btn btn-primary">회원가입</button>
        </div>
      </form>
    </div>
  </body>
</html>
```



```html
<!--login.jsp-->
<html>
<body>
  <div class="card">
  <div class="card-header border-0">로그인</div>
    <div class="card-body">
      <form action="${path}/user/loginPost" method="post">
        <div class="form-group row">
          <input type="text" id="email_address" class="form-control"
          name="email">
        </div>
        <div class="form-group row">
          <input type="password" id="password" class="form-control"
          name="password">
        </div>
				<button type="submit" class="btn btn-primary">로그인</button>			
    </div>
      </form>
  </div>
</body>
</html>
```



loginPost는 로그인이 잘못되었을때만 alert를 통해 경고합니다.

```html
<!--loginPost.jsp-->
<html>
<body>
	<script>
		alert("아이디와 비밀번호를 확인");
		self.location ="/user/login";
	</script>
</body>
</html>
```



로그아웃은 세션만 끊어주면 되기에 따로 작성할 form은 없습니다.
로그아웃 시 메인으로 이동하게 script만 작성합니다.

```html
<!--logout.jsp-->
<body>
<script>
	self.location ="/"
</script>
</body>
```

수정과 탈퇴 버튼을 만듭니다.

```html
<li><a href="modify">회원정보 수정</a></li>
<li><a href="withdrawal">회원탈퇴</a></li>
```

버튼이 있는 jsp파일과 동일한 디렉토리에 `modify.jsp`와 `withdrawal.jsp`를 만듭니다.

혹은 다른 곳에 만들고 버튼에 연결되는 링크를 다르게 써주시면 됩니다.

```html
<!--modify.jsp-->
<html>
<body>
<form method="post">
<input type="text" name="email" value="${login.email}">
<input type="text" name="name" value="${login.name}">
<button type="submit">수정</button>
</form>
</body>
</html>

<!--withdrawal.jsp-->
<html>
<body>
<form method="post">
<input type="text" name="email" value="${login.email}">
<input type="text" name="password" value="${login.password}">
<button type="submit">탈퇴</button>
</form>
</body>
</html>
```

기본적인 UI는 완성되었습니다.



# VO, DTO, DAO

UserVO와 LoginDTO를 만듭니다.

```java
public class UserVO {
	private String email;
	private String name;
	private String password;
    
    /*getter, setter, toString()은 생략하지만 각자 만들어주세요.*/
}
```



```java
public class LoginDTO {
	private String email;
	private String password;
	private String name;
    
    /*getter, setter, toString()은 생략하지만 각자 만들어주세요.*/
}
```



```java
public interface UserDAO {
public void insertUser(UserVO uservo) throws Exception; /* 유저생성 */
    
public UserVO login(LoginDTO loginDTO) throws Exception; /* 로그인 */
	
public void modify(LoginDTO loginDTO) throws Exception; /* 회원수정 */
	
public void withdrawal(LoginDTO loginDTO) throws Exception; /* 회원탈퇴 */
}
```



```java
@Repository
public class UserDAOImpl implements UserDAO {

	@Inject
	private SqlSession session;
	
	private static String namespace = "com.popit.mapper.UserMapper";
	
	@Override
	public void insertUser(UserVO uservo) throws Exception {
		session.insert(namespace +".insertUser", uservo);
	}

	@Override
	public UserVO login(LoginDTO loginDTO) throws Exception {
		return session.selectOne(namespace +".login", loginDTO);
	}

	@Override
	public void modify(LoginDTO loginDTO) throws Exception {
		session.update(namespace +".modify", loginDTO);
	}

	@Override
	public void withdrawal(LoginDTO loginDTO) throws Exception {
		session.delete(namespace +".withdrawal", loginDTO);
	}	
}
```

여기서 `namespace`뒤에 붙는 `.withdrawl`과 같은 이름은 Mapper에서 정의한 id를 보고 적으면 된다.

# Mapper, MySQL

SQL에 테이블을 생성합니다.

```sql
create table USER(
user_email varchar(100) not null,
user_pw varchar(100) not null,
user_name varchar(50) not null,
primary key(user_email)
);
```



```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.popit.mapper.UserMapper">

<resultMap id="userVOResultMap" type="userVO">
	<id property="email" column="user_email"/>
	<result property="password" column="user_pw"/>
	<result property="name" column="user_name"/>
</resultMap>

<insert id="insertUser">
insert into USER (user_email, user_pw, user_name)
values(#{email}, #{password}, #{name})
</insert>

<select id="login" resultMap="userVOResultMap">
select * from USER where user_email = #{email}
</select>

<update id="modify">
update USER set user_name = #{name}
where user_email = #{email}
</update>

<delete id="withdrawal">
delete from USER
where user_email = #{email} and user_pw = #{password}
</delete>
</mapper>
```

SQL과 프로그래머가 지정한 변수의 명이 다를 경우 매칭시켜주기 위해 Mapper에 resultMap을 만듭니다.

`property`는 코딩할때 지정한 변수고 `column`은 SQL에 써놓은 이름입니다.

update와 delete는 resultMap을 적을 필요 없습니다.



# Service



```java
public interface UserService {
	public void insertUser(UserVO uservo) throws Exception;
	
	public UserVO login(LoginDTO loginDTO) throws Exception;
	
	public void modify(LoginDTO loginDTO) throws Exception;
	
	public void withdrawal(LoginDTO loginDTO) throws Exception;
	
}
```



```java
@Service
public class UserServiceImpl implements UserService {

	@Inject
	private UserDAO userdao;
	
	@Override
	public void insertUser(UserVO uservo) throws Exception {
		userdao.insertUser(uservo);
	}

	@Override
	public UserVO login(LoginDTO loginDTO) throws Exception {
		return userdao.login(loginDTO);
	}

	@Override
	public void modify(LoginDTO loginDTO) throws Exception {
		userdao.modify(loginDTO);
	}

	@Override
	public void withdrawal(LoginDTO loginDTO) throws Exception {
		userdao.withdrawal(loginDTO);
	}
}
```



# Interceptor

Interceptor는 Controller로 들어오는걸 가로채 특정 작업을 하기위해 만듭니다.

Interceptor를 사용하지 않으면 나중에 관리해야할 URL이 늘어날수록 중복코드가 늘어나 좋지 않습니다.



`postHandle`은 Controller의 method 처리가 끝나고 화면을 띄워주기 전에 실행됩니다.

```java
public class LoginInterceptor extends HandlerInterceptorAdapter {

	private static final String LOGIN = "login";

	@Override
	public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception{
		HttpSession httpsession = request.getSession();
		ModelMap modelMap = modelAndView.getModelMap();
		Object userVO = modelMap.get("userVO");

		if(userVO != null) {
			System.out.println("Success");
			httpSession.setAttribute(LOGIN, userVO);
			response.sendRedirect("/");
		}
	}

	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

		HttpSession httpsession = request.getSession();

		if(httpsession.getAttribute(LOGIN) !=null) {
			httpsession.removeAttribute(LOGIN);
		}
		return true;
	}
```

로그인에 성공했을때 콘솔에서 확인할 수 있게 "Success"를 출력하게 해놓았습니다.

만약 로그인을 했을때 "Success"가 콘솔에 출력되지만 로그인한 사용자 정보를 못끌어온다면 혹시 `.jsp` 파일에

`session = false` 비슷한게 써있지 않나 확인해야합니다.

`session = fasle`가 있다면 그 페이지에서는 세션정보를 못 끌어오게 되어 로그인을 했음에도 사용자 정보를 끌어올 수 없게 됩니다.



사용하기 위해서는 `servlet-context.xml`에도 정의해주어야 합니다.

`component-scan`을 사용해 어노테이션을 사용할 패키지를 지정해준다.

```xml
<!-- servlet-context.xml -->
<context:component-scan base-package="com.popit" />

<beans:bean id="loginInterceptor"
	class="com.popit.interceptor.LoginInterceptor" />
<mvc:interceptors>
	<mvc:interceptor>
		<mvc:mapping path="/user/loginPost" />
		<beans:ref bean="loginInterceptor" />
	</mvc:interceptor>
</mvc:interceptors>
```



그리고 특정 작업에 권한을 부여하려면 `AuthInterceptor`를 만들어야 합니다.

```java
@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception{

		HttpSession httpSession = request.getSession();

		if(httpSession.getAttribute("login") == null) {
			System.out.println("not logged");
			response.sendRedirect("/user/login");
			return false;
		}
		return true;
	}
```

마찬가지로 servlet-context.xml에도 적용해줍니다.

그리고 `mvc:mapping`에는 권한이 필요한 페이지의 url을 넣어주면 됩니다.

```xml
<!-- servlet-context.xml -->
<beans:bean id="authInterceptor"
		class="com.popit.interceptor.AuthInterceptor" />
	<mvc:interceptors>
		<mvc:interceptor>
			<mvc:mapping path="/regist" />
			<mvc:mapping path="/modify" />
			<mvc:mapping path="/remove" />
			<mvc:mapping path="/user/profile" />
			<mvc:mapping path="/reply/write" />
			<beans:ref bean="authInterceptor" />
		</mvc:interceptor>
	</mvc:interceptors>
```



# Controller

`Controller`는  코드가 길어질 수 있어서 로그인관련은 분리했습니다.

`RegisterPost`에는 `BCrypt`라는 새로운 기능이 있는데 이건 비밀번호를 암호화해서 저장해주는 기능입니다.

그걸 `hasedPw`라는 변수에 담아 `setPassword`를 해주고 유저를 생성합니다.

그리고 `RedirectAttributes`에는 `addFlashAttribute`라는 기능이 있는데 이건 리다이렉트 할 때 url뒤에 parameter를 붙이지 않고 정해놓은 값을 전달할 수 있는 기능입니다.



그래서 "msg"에 "Registered"라는 값을 받아 login 페이지로 리다이렉트 시킵니다.

그리고 `login.jsp` `<script>`부분에 아래와 같은 코드를 추가하여 만약 `Registered`라는 값이 넘어왔다면 성공한 것이므로 `alert`를 띄워 사용자가 회원가입이 완료된것을 알게합니다.

```javascript
var msg = "${msg}";
if (msg === "Registered") {
	alert("회원가입이 완료되었습니다. 로그인해주세요.")
}
```



`httpsession.invalidate`는 세션을 삭제하지는 않지만 무효화 해준다.

그래서 회원정보 수정, 탈퇴 그리고 로그아웃에는 `invalidate`가 들어간다.

```java
@Controller
@RequestMapping("/user/*")
public class UserController {
	
	@Inject
	private UserService userservice;
	
	@RequestMapping(value = "/register", method = RequestMethod.GET)
	public void RegisterGET(UserVO uservo, Model model) throws Exception {
	}
	
	@RequestMapping(value="/register", method=RequestMethod.POST)
	public String RegisterPost(UserVO uservo, RedirectAttributes rttr) throws Exception{
		
		String hashedPw = BCrypt.hashpw(uservo.getPassword(), BCrypt.gensalt());
		uservo.setPassword(hashedPw);
		userservice.insertUser(uservo);
		rttr.addFlashAttribute("msg", "Registered");
	
		return "redirect:/user/login";
	}
	
	@RequestMapping(value = "/profile", method = RequestMethod.GET)
	public void ProfileGET() throws Exception {
	}
	
	@RequestMapping(value = "/modify", method = RequestMethod.GET)
	public void ModifyGET() throws Exception {
	}
	
	@RequestMapping(value = "/modify", method = RequestMethod.POST)
	public String ModifyPOST(HttpSession httpsession, RedirectAttributes rttr, LoginDTO loginDTO) throws Exception {
		userservice.modify(loginDTO);
		httpsession.invalidate();
		
		return "redirect:/";
	}
	
	@RequestMapping(value = "/withdrawal", method = RequestMethod.GET)
	public void WithdrawalGET() throws Exception {
	}
	
	@RequestMapping(value = "/withdrawal", method = RequestMethod.POST)
	public String WithdrawalPOST(HttpSession httpsession, RedirectAttributes rttr, LoginDTO loginDTO) throws Exception {
		userservice.withdrawal(loginDTO);
		httpsession.invalidate();
		
		return "redirect:/";
	}
}
```



로그아웃을 할때 `removeAttribute`를 사용해 세션의 값들을 삭제합니다.

더이상 사용할 필요가 없으니까요.

```java
@Controller
@RequestMapping("/user/*")
public class UserLoginController {
	
	private final UserService userService;
	
	@Inject
	public UserLoginController(UserService userService) {
		this.userService = userService;
	}
	
	@RequestMapping(value="/login", method=RequestMethod.GET)
	public void LoginGET(@ModelAttribute("loginDTO") LoginDTO loginDTO) {
	}
	
	@RequestMapping(value="/loginPost", method=RequestMethod.POST)
	public void loginPOST(LoginDTO loginDTO, HttpSession httpsession, Model model) throws Exception{
		UserVO uservo = userService.login(loginDTO);
		
		if(uservo == null || !BCrypt.checkpw(loginDTO.getPassword(), uservo.getPassword())) {
			return;
		}
		model.addAttribute("userVO", uservo);
	}
    
	@RequestMapping(value="/logout", method=RequestMethod.GET)
	public String logout(HttpServletRequest request, HttpServletResponse response, HttpSession httpsession) throws Exception{
		Object object = httpsession.getAttribute("login");
		if(object != null) {
			httpsession.removeAttribute("login");
			httpsession.invalidate();
		}
		return "/user/logout";
	}
}
```

