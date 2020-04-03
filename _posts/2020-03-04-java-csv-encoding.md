---
layout: post
title: "Java, Tomcat CSV Encoding 정리"
tags: Java
comments: true
---

이번에 일본어가 포함된 CSV 다운로드 기능을 보완하면서 깨닫게 된 점들이 있어서
정리합니다.

결론부터 쓰자면 프로젝트가 UTF-8 설정이 잘 되어 있으면 더 이상 따로 `pom.xml` 같은 곳에
추가해야 할 코드는 없습니다.

`Dfile.encoding=UTF-8` 설정과 Windows 10 기준으로 프로젝트를 올려놓은 서버의 언어 설정만
일본어로 되어 있으면 CSV는 깨지지 않고 올바르게 작동합니다.
물론 중간에 다른 인코딩 방식이 설정되어 있다면 파일의 내용은 깨질 것입니다.

```java
...
response.setHeader("Content-Disposition", "attachment;filename=\"" + fileName + "\"");
response.setContentType("text/csv");
response.setCharacterEncoding("UTF-8");
response.setHeader("Content-Transfer-Encoding", "binary;");

servletOutputStream.write(0xEF);
servletOutputStream.write(0xBB);
servletOutputStream.write(0xBF);
...
```

Controller에 위와 같이 BOM 처리와 UTF-8로 인코딩 설정을 해줍니다.
나머지 코드는 파일 다운로드를 위한 코드가 될 것입니다.

그러고 나서 프로젝트의 설정은 따로 건드리지 않고 Tomcat 원래 폴더의 bin으로 가면
`catalina.bat, startup.bat`과 같은 파일들이 있을 겁니다.

여기서 `catalina.bat`을 열어서 아래와 같이 되어 있는 부분을 찾아서 `-Dfile.encoding=UTF-8`을
추가해 줍니다.

```
rem Register custom URL handlers
rem Do this here so custom URL handles (specifically 'war:...') can be used in the security policy
set "JAVA_OPTS=%JAVA_OPTS% -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dfile.encoding=UTF-8"
```

그리고 저장 후 `startup.bat`으로 서버를 실행시키면 `catalina.bat`에서 설정한 옵션을 따라가게 되어
UTF-8 적용이 잘 될것입니다.

<img src="/images/encoding2.png">

만약에 `tomcat8.exe`로 서버를 실행시킨다면 `tomcat8w.exe`의 설정을 변경해야 합니다.

<img src="/images/encoding1.png">

아래와 같이 JAVA 탭에 있는 곳에 있는 Java Options에 정의해주시면 됩니다.

<img src="/images/encoding3.png">

그리고 CSV에 일본어가 포함되어 있다면 해당 서버의 Windows 표시 언어를 일본어로 변경해야 합니다.
한국어만 포함되어 있다면 그러지 않아도 됩니다.
이렇게 언어를 맞춰서 변경하는 이유는 파일 다운로드를 할 때 시스템 언어를 따라가는 부분이
있기 때문입니다. 그래서 서비스할 파일의 언어와 맞춰주어야 문제없이 다운로드가 됩니다.

만약 설정이 제대로 되지 않았다면 파일의 인코딩은 `UTF-8 BOM`이라 할지라도 내용이 깨져서
보일 것입니다. 그 이유는 중간에 `ANSI`라는 인코딩 형식으로 파일이 한번 저장되고 나서
최종적으로 사용자에게 가기 때문입니다.

`SHIFT-JIS`도 사용해보았지만 몇 한자가 깨져서 나오는 바람에 사용하지 않았습니다.
