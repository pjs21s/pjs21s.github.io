---
layout: post
title: "서버에서 구동하고 있는 자바 프로그램이 이유 없이 작동 중지할 때"
tags: Java
comments: true
---

서버에서 항시 구동시켜놓은 스케줄러가 이유 없이 멈춘 날이 있었습니다.
전부터 이상하게 서버에 올려서 구동하는 자바 관련 프로그램이 가끔 제대로 돌아가지 않는다는 인상을 받았습니다.

이대로 두면 불안해서 자바 메모리 문제라고 추정하고 Centos에서 프로그램이 자바 메모리 사용량을 알 수 있는 방법을 찾았습니다. APM Tool이나 다른 메모리 추적하는 툴을 사용할 줄 알면 좋겠지만 현실이 그렇지 않아 명령어도 대강 알 수 있는 방법이 있나 찾아보니 `jstat`이라는 명령어를 통해 간단히 알 수 있었습니다.

한데 메모리 문제면 기본적으로 메모리 설정부터 들여다보게 됩니다.
`JAVA_OPTS="-Dfile.encoding=UTF-8 -Xms1024m -Xmx1024m -XX:PermSize=512m -XX:MaxPermSize=512m"`
보통 이런 느낌으로 설정을 하는데요. 여기서 의구심을 느껴 찾아보니 JDK8부터는 PermSize 관련 옵션이 사라졌다는 사실을 알게 됩니다. JDK7까지만 Permenent 영역이 존재했고 JDK8부터는 Metaspace라는 영역으로 바뀌었습니다. 그래서 Native 영역으로 오게 되었고 OS가 자동으로 사용량만큼 설정한다고 합니다. 그래서 `-XX:PermSize=512m -XX:MaxPermSize=512m` 옵션을 제거하고 프로그램을 작동시키니 정상적으로 잘 동작합니다. jstat로 측정한 메모리 사용량도 이전보다 더 사용하고 있는 것을 보면 혹시 메모리가 제한되어 있어서 프로그램이 자꾸 멈춘 것은 아니었나 하는 생각이 듭니다.

그러면 간단하게 jstat에 대해 알아봅시다.

`jstat --option $PID $MILLISECOND_INTERVAL $COUNT`

$PID : JVM 프로세스 아이디
$MILLISECOND_INTERVAL : 추적하고 싶은 단위 시간을 밀리세컨드로 환산
$COUNT : 횟수

근데 급하게 사용해야해서 문법을 익히기 힘들다면 아래 형식을 이용하면 샤용량과 총량을 함께 볼 수 있다.

`jstat -gc PID값 1000 3| tail -n 3 | awk '{split($0,a," "); total=a[1]+a[2]+a[5]+a[7]; sum=a[3]+a[4]+a[6]+a[8]; print "used: " sum ", total: " total}`

더 제대로 정리된 문서를 보고 싶다면 다른 블로그나 오라클 공식문서를 참고하면 좋다.

참고 : <https://gem1n1.tistory.com/89>, <https://johngrib.github.io/wiki/java8-why-permgen-removed/>