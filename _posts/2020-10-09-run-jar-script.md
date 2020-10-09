---
layout: post
title: "리눅스 jar 실행 스크립트 예시"
tags: linux
comments: true
---

리눅스에서 간단하게 jar를 실행하는 방법은 `java -jar test.jar` 명령어이다.
근데 이 명령어를 쓰면 자주 끄고 켜줘야하는 파일이거나 테스트해야 할 때는 일일이 `ps -ef | grep test.jar`로 찾아서
프로세스를 죽여야하는 반복 노동이 발생한다.

그래서 `run.sh`와 같이 스크립트 파일을 하나 만들어 놓고 관련 옵션이나 자주 사용하는 명령어를 미리 정의해두면 편하다.
아래 코드는 간단하게 `.jar`를 멈추고 시작하고 재시작하는 명령어를 포함한 .sh 파일 예시이다.
기본 적으로 nohup(백그라운드)으로 실행되기 때문에 그게 싫다면 명령어를 수정해서 사용하면 된다.

```perl 
#!/bin/sh
SERVICE_NAME=demo
PATH_TO_JAR=/usr/local/server/demo.jar
PID_PATH_NAME=./demo.pid
JAVA_OPT="-Xms2048m -Xmx2048m -XX:PermSize=256m -XX:MaxPermSize=512m"

case $1 in
    start)
        if [ -f $PID_PATH_NAME ]; then
            PID=$(cat $PID_PATH_NAME);
            kill $PID;
            rm $PID_PATH_NAME
        fi

        echo "$SERVICE_NAME starting ..."
        nohup java -jar $JAVA_OPT $PATH_TO_JAR >> /dev/null &
        echo $! > $PID_PATH_NAME
        echo "$SERVICE_NAME started ..."
    ;;
    stop)
        if [ -f $PID_PATH_NAME ]; then
            PID=$(cat $PID_PATH_NAME);
            echo "$SERVICE_NAME stopping ..."
            kill $PID;
            echo "$SERVICE_NAME stopped ..."
            rm $PID_PATH_NAME
        else
            echo "$SERVICE_NAME is not running ..."
        fi
    ;;
    restart)
        if [ -f $PID_PATH_NAME ]; then
            PID=$(cat $PID_PATH_NAME);
            echo "$SERVICE_NAME stopping ...";
            kill $PID;
            echo "$SERVICE_NAME stopped ...";
            rm $PID_PATH_NAME
            echo "$SERVICE_NAME starting ..."
            nohup java -jar $JAVA_OPT $PATH_TO_JAR >> /dev/null &
                        echo $! > $PID_PATH_NAME
            echo "$SERVICE_NAME started ..."
        else
            echo "$SERVICE_NAME is not running ..."
        fi
    ;;
esac

```