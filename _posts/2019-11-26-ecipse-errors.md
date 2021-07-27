---
layout: post
title: "Eclipse - SunTlsRsaPremasterSecret외 에러 정리"
tags: Eclipse
comments: true
---

# SunTlsRsaPremasterSecret KeyGenerator not available

원본: <https://stackoverflow.com/questions/2872520/suntlsrsapremastersecret-keygenerator-not-available>

Windows -> Preferences -> Java -> Installed JREs -> Edit에서 JRE system libraries에 있는
파일들의 디렉토리를 확인하면 된다.
`%JAVA_HOME%/jre/lib/ext` 이런 형식이면 된다.
근데 형식이 맞아도 해결이 안될수있는데 그런 경우에는 `JRE home`탭에는 `jre1.8.0`으로 되어있는게
JRE system libraries에는 `jdk1.8.0`으로 되어있던가 하면 서로 안맞아서 에러가 나는것이니 맞춰주면 된다.

# tomcat eclipse The requested resource is not available.

Server 탭에서 현재 프로젝트 삭제 후 다시 실행하면 잘 된다.
그냥 이유없이 발생할때가 종종 있음

# The code of method _jspService(HttpServletRequest, HttpServletResponse) is exceeding the 65535 bytes limit

Servers -> web.xml에 아래코드 추가

```xml
<servlet>
<servlet-name>jsp</servlet-name>
<servlet-class>org.apache.jasper.servlet.JspServlet</servlet-class>
<init-param>
<param-name>mappedfile</param-name>
<param-value>false</param-value>
</init-param>
</servlet>
```
