---
layout: post
title: "이클립스(Eclipse) 톰캣(Tomcat) Timeout 에러"
tags: Eclipse
comments: true
---
"Starting Tomcat v9.0 Server at localhost' has encountered a problem.

Server Tomcat v9.0 Server at localhost was unable to start within 45 seconds. If the server requires more time, try increasing the timeout in the server editor.

* 이클립스 서버 탭에 있는 서버를 더블 클릭-> Tomacat v9.0 Server at localhost [Stopped]

* 열리는 설정 창에 Timeouts 탭을 클릭 -> Start(in secondes)의 기본 설정값은 45초이므로 서버 구동시 45초를 넘기면 위와 같은 에러가 발생

* Start(in secondes) 여유롭게 100초 정도로 바꿔주거나 각자의 상황에 따라 변경하면 해결 완료
