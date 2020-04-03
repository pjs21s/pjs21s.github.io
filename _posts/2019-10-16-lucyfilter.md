---
layout: post
title: "Spring lucy xss servlet filter와 Apache에 간단하게 보안 적용 "
tags: Spring
comments: true
---

[깃허브 원본 주소](https://github.com/naver/lucy-xss-servlet-filter)

원본에 설명이 잘되어 있는데 그럼에도 개인적으로는 조금 헷갈렸기에 저같은 분들을 위해 정리합니다.

`pom.xml`에 dependency를 추가합니다.

```xml
<dependency>
	<groupId>com.navercorp.lucy</groupId>
	<artifactId>lucy-xss-servlet</artifactId>
	<version>2.0.0</version>
</dependency>
```

`web.xml`에 필터를 추가합니다. encodingFilter는 예시로 올려놓은 것이니 있으시면 추가안하셔도 됩니다.

순서가 XSS가 뒤에 와야합니다.

```xml
<!-- xssEscapeServletFilter는 CharacterEncodingFilter 뒤에 위치해야 한다. -->
<filter>
	<filter-name>encodingFilter</filter-name>
	<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
	<init-param>
		<param-name>encoding</param-name>
		<param-value>UTF-8</param-value>
	</init-param>
</filter>
<filter-mapping>
	<filter-name>encodingFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>

<!-- XSS 필터 코드 -->
<filter>
	<filter-name>xssEscapeServletFilter</filter-name>
	<filter-class>com.navercorp.lucy.security.xss.servletfilter.XssEscapeServletFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>xssEscapeServletFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```
`lucy-xss-servlet-filter-rule.xml`을 생성합니다.

`src/main/resources` 안에 생성해야합니다. 다른 `resources`에 만들면 이 파일을 못찾아서 실행이 안됩니다.

parse 못한다고 에러가 발생합니다.

<img src="/images/lucyxss.png">

공식문서에 있는걸 사용하셔도 되고 저는 더 간단하게 만든 버전을 사용했습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<config xmlns="http://www.navercorp.com/lucy-xss-servlet">
	<defenders>
		<!-- XssPreventer 등록 -->
		<defender>
			<name>xssPreventerDefender</name>
			<class>com.navercorp.lucy.security.xss.servletfilter.defender.XssPreventerDefender
			</class>
		</defender>

		<!-- XssSaxFilter 등록 -->
		<defender>
			<name>xssSaxFilterDefender</name>
			<class>com.navercorp.lucy.security.xss.servletfilter.defender.XssSaxFilterDefender
			</class>
			<init-param>
				<param-value>lucy-xss-sax.xml</param-value>   <!-- lucy-xss-filter의 sax용 설정파일 -->
				<param-value>false</param-value>        <!-- 필터링된 코멘트를 남길지 여부, 성능 효율상 false 추천 -->
			</init-param>
		</defender>

		<!-- XssFilter 등록 -->
		<defender>
			<name>xssFilterDefender</name>
			<class>com.navercorp.lucy.security.xss.servletfilter.defender.XssFilterDefender
			</class>
			<init-param>
				<param-value>lucy-xss.xml</param-value>    <!-- lucy-xss-filter의 dom용 설정파일 -->
				<param-value>false</param-value>         <!-- 필터링된 코멘트를 남길지 여부, 성능 효율상 false 추천 -->
			</init-param>
		</defender>
	</defenders>

	<!-- default defender 선언, 별다른 defender 선언이 없으면 default defender를 사용해 필터링 
		한다. -->
	<default>
		<defender>xssPreventerDefender</defender>
	</default>

	<!-- global 필터링 룰 선언 -->
	<global>
		<!-- 모든 url에서 들어오는 globalParameter 파라메터는 필터링 되지 않으며 또한 globalPrefixParameter로 
			시작하는 파라메터도 필터링 되지 않는다. -->
		<params>
			<param name="globalParameter" useDefender="false" />
			<param name="globalPrefixParameter" usePrefix="true"
				useDefender="false" />
		</params>
	</global>

	<!-- url 별 필터링 룰 선언 -->
	<url-rule-set>

	</url-rule-set>
</config>
```

# Apache

근데 위와 같이해도 기능은 동작하지만 https://observatory.mozilla.org/

에서 배포해놓은 사이트를 넣고 검사를 하면 기능이 적용되어 있지 않다고 뜬다.

여기서 검사하는건 서버에 설정이 어떻게 되어 있냐를 검사하는 듯 하다.

그래서 EC2에 Apache + Tomcat으로 배포를 한 나는 `httpd.conf` 파일에 코드를 넣어줘야 보안 설정을 할 수 있었다. `etc/httpd/conf`에 있는 `httpd.conf`를 열어서 아래의 코드를 넣어준다.

근데 `Content-Security-Policy`는 안넣는게 좋을 수도 있다.

이걸 하면 `css`와 같은 것들이 작동을 안해서 완전 원본 상태의 페이지가 뜬다.

물론 수정을 하면 사용 가능하겠지만 개발 초보는 그냥 `X-Content-Type-Options`과 `X-XSS-Protection`만 넣어줘도 된다.

```
<IfModule mod_headers.c>
    Header set Content-Security-Policy "script-src 'self' ; img-src 'self'; style-src 'self' 'unsafe-inline';connect-src http:; child-src 'unsafe-inline'"
    Header set X-Content-Type-Options nosniff
    Header set X-XSS-Protection "1;mode=block"
</IfModule>
```

코드를 넣고 저장 후 `restart`를 하고 검사를 다시 하면 적용된 것을 확인할 수 있다.

참조: https://coding-start.tistory.com/65