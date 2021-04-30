---
layout: post
title: "Docker Jenkins + GitLab Webhooks + 원격서버 배포까지"
tags: Jenkins
comments: true
---

이번에 사내에 젠킨스 사용 예정이 되면서 직접 전체적으로 해보게 되었습니다.
하지만 기존에 참고하던 자료들이 설명이 생략되어 있거나 조금 시간이 지나 지금과 안맞는 것들이 있는거 같아
적어두려고 합니다.

환경 Docker Centos 7(배포서버), Docker Jenkins, GitLab 등 입니다.
Gradle, Jar로 배포하는 내용을 포함합니다.

배포용 서버를 세팅하는 내용까지 포함하면 너무 길어지기때문에 배포서버는 이미 설정이 되어 있다고 가정합니다.
기본적인 도커 사용법은 알고있다고 가정합니다.

도커에 Jenkins 이미지를 다운받아 설치하여 실행합니다. 

`docker run -p 8080:8080 -p 50000:50000 --name jenkins jenkins/jenkins`로 포트를 열어주면서 설치합니다.
만약 본인의 도커 젠킨스의 설정이 재부팅할때마다 증발한다고 하면 아래처럼 `-v` 옵션을 사용해 본인 로컬에 연결하여 실행해야 합니다.

`docker run -v C:\Users\Jin\Documents:/home/workspace:Z -p 8080:8080 -p 50000:50000 --name jenkins jenkins/jenkins `

모든 사람이 재부팅한다고 설정이 사라지는게 아니기때문에 도커를 이미 다뤄보시거나 그런 증상을 걱정하지 않으셔도 된다면 굳이 하실 필요는 없습니다.
설치가 정상적으로 완료되면 화면이 이렇게 되고 비밀번호가 적혀있습니다.

<img src="/images/jenkinswebhooks.png">

localhost:8080으로 접속해서 아래 스크린샷처럼 비밀번호를 넣고 진행합니다.

<img src="/images/jenkinswebhooks2.png">

기본값인 Install suggested plugins를 선택합니다.
플러그인을 자동으로 설치합니다. 만약 여기서 플러그인이 제대로 설치되지 않는다면 아래 링크의 Jenkins 이미지를 다운받아서 설치합니다.

https://hub.docker.com/r/jenkins/jenkins/

`jenkins/jenkins` 이미지가 아닌 그냥 `jenkins` 이미지를 다운받아 사용하면 에러가 발생합니다.

<img src="/images/jenkinswebhooks3.png">

플러그인이 다 설치되면 Admin을 설정해야 합니다. 간단하게 적고 진행합니다.
Instance Configuration도 기본 값인 localhost:8080으로 진행합니다.
여기서 포트를 변경해야 하시는 분들은 도커 생성할 때 포트포워딩도 그에 맞추어 해주셔야 합니다.


이제 Jenkins에 플러그인을 다운하겠습니다.
`Jenkins 관리 -> 플러그인 관리`

<img src="/images/jenkinswebhooks4.png">

설치가능 탭을 누르고 Gitlab을 검색합니다. 저는 이렇게 필요해보이는 것들을 다 설치하겠습니다.
그리고 밑에 지금 다운로드하고 재시작 후 설치하기를 누릅니다.

<img src="/images/jenkinswebhooks5.png">

다운로드 화면으로 넘어가면 `설치가 끝나고 실행중인 작업이 없으면 Jenkins 재시작.` 옵션도 눌러줍니다.
적당히 설치된거 같으면 메인페이지로 돌아가기를 눌러 다시 로그인합니다.

이제 프로젝트를 만들건데 그 전에 Credential을 설정해야 합니다.
`Jenkins 관리 -> Manage Credential`
<img src="/images/jenkinswebhooks7.png">

global을 눌러 등록합니다.
<img src="/images/jenkinswebhooks8.png">

Jenkins 서버에서 공개키, 개인키 쌍을 생성합니다.
Jenkins 컨테이너에 접속해야 합니다.
근데 그냥 접속하면 root 권한이 없으므로 아래와 같이 `-u 0` 키워드를 추가하여 root로 로그인합니다.
`docker exec -u 0 -it jenkins bash`

`ssh-keygen -t rsa -b 4096`
명령어로 키를 생성합니다. 생성한 키는 ~/.ssh에 보관됩니다.
<img src="/images/jenkinswebhooks10.png">

`cd ~/.ssh`으로 이동합니다.
`cat id_rsa`으로 텍스트를 출력하고 복사하여 Username에는 본인이 식별할만한 키 이름을 넣어주시고
private key에는 방금 생성한 개인키를 적어주시면 됩니다.

<img src="/images/jenkinswebhooks9.png">

gitlab으로 가서 Jenkins 서버에서 생성한 공개키(id_rsa.pub)를 설정에 적어줍니다.
`Setting -> SSH Keys`
<img src="/images/jenkinswebhooks11.png">

이제 Jenkins 프로젝트를 생성하겠습니다. 왼쪽 메뉴에서 새로운 Item -> Freestyle project를 눌러줍니다.

<img src="/images/jenkinswebhooks6.png">

그리고 스크롤을 내려서 소스 코드 관리로 와서 gitlab 프로젝트의 주소를 적어주고 Credential에는 방금 추가했던 키를
선택해줍니다. 기본적으로 Branch는 마스터지만 혹시 변경해야한다면 `*/feature/test`와 같은 형식으로 변경하시면 됩니다.
여기서는 프로젝트 주소가 `https~` 형식이 아니라 `git~` 형식을 사용했습니다.
<img src="/images/jenkinswebhooks12.png">

이제 밑에 내려오시면 빌드 유발이라는 탭이 있습니다.
`Build when a change is pushed to GitLab`을 눌러주시고 다른 부분은 그대로 두시고 고급을 눌러서 `Secret token`에서
Generate해서 Webhooks에 사용할 토큰을 생성합니다.

그리고 `http://localhost:8080/project/vue_spring` Webhooks을 위한 주소가 `Build when a change is pushed to GitLab` 옆에 있으니
잘 적어두시면 됩니다.

<img src="/images/jenkinswebhooks13.png">

Secret token을 가지고 `gitlab 본인 프로젝트 -> Settings -> Webhooks` 메뉴를 클릭합니다.
URL에 `http://localhost:8080/project/vue_spring`을 넣으면 되는데요. 그냥 넣으면 내부 주소는 사용할 수 없다고
에러가 발생합니다. 물론 내부 IP 허용을 해서 사용할 수도 있지만 본인이 회사에서 관리자가 아니라서 허용을 못하는 상황도
있기때문에 저희는 주소를 생성해서 Webhook을 등록하겠습니다.

https://ngrok.com/에서 ngrok 프로그램을 다운해서 저장해서 압축을 풀어 실행합니다.
프로그램에서 `ngrok http http://localhost:8080`와 같은 명령어로 주소를 생성합니다.

`http://bd5b27b14ef1.ngrok.io` 저는 이 주소가 나왔고 `http://bd5b27b14ef1.ngrok.io/project/vue_spring` 원래 Webhook 주소에 맞게
수정해서 적어주겠습니다.

<img src="/images/jenkinswebhooks14.png">

다른 설정을 손대지 않고 아래로 내려서 Add webhookk합니다.

<img src="/images/jenkinswebhooks15.png">

Push Events로 테스트합니다. 200번이 나오면 성공입니다.

이제 Jenkins 페이지로 다시 돌아와서 빌드 설정을 하기전에 지금까지 작성한 내용을 저장합니다.
그리고 도커 Jenkins 컨테이너로 돌아가서 root 계정 상태에서 `apt-get update`를 해줍니다.
Jenkins 사용해서 자동 빌드를 시키려면 Gradle, Java 등이 Jenkins 서버에도 설치되어 있어야 하기때문에
설치를 하겠습니다.

`apt-get install gradle`로 설치합니다. 근데 아마 6.6 같은 최신 버전이 아니라 3.2 정도 버전까지만 설치될것입니다.
```
sudo add-apt-repository ppa:cwchien/gradle
sudo apt-get update
sudo apt upgrade gradle
```

명령어를 통해 최신버전 gradle로 업그레이드 합니다. 이런 느낌으로 java나 각자 빌드에 필요한 프로그램들을 미리 설치해줍니다.
sudo는 없으면 생략해도 무방하거나 sudo를 설치하고 명령어를 사용하면 됩니다.

<img src="/images/jenkinswebhooks16.png">

`Jenkins 관리 -> Global Tool Configuration`
Jenkins 서버에 프로그램 설치가 완료됐으면 Jenkins 페이지로 돌아와서 설정을 이어서합니다.

<img src="/images/jenkinswebhooks17.png">

JDK 경로부터 설정합니다. Install automaticall를 해제하면 스크린샷처럼 직접 적을 수 있습니다.

<img src="/images/jenkinswebhooks18.png">

Gradle도 똑같이 설정합니다. 단지 Gradle을 구버전을 설치후 최신 버전을 설치하면 `which gradle`로 나온 경로로는
Jenkins에 설정할 수 없습니다. 그래서 `find / -name "gradle" 2> /dev/null` 명령어를 사용해서 최신버전의 경로를 찾아
등록하면 됩니다.
만약 Build step `'Invoke Gradle script' marked build as failure` 에러가 빌드 중에 발생한다면 Jenkins에서 Gradle 설정을
잘못한 경우이니 확인하시면 됩니다. 저의 경우 이름을 gradle6으로 했다고 에러가 발생했으니 어지간하면 그냥 gradle로 하시는게 좋습니다.

저장을 하고 기존 Freestyle 프로젝트 설정 페이지로 와서 `Build -> Invoke Gradle script`를 선택합니다.
여전히 다른건 손대지 않고 스크린샷처럼 Gradle Version, Tasks, Build File만 적어줍니다.
<img src="/images/jenkinswebhooks19.png">

그리고 저는 vue + spring boot 배포이다 보니 bootJar를 하기 전에 npm install, npm run build를 미리 실행해야 하기 때문에 이렇게 적어주겠습니다.

<img src="/images/jenkinswebhooks24.png">

만약에 Jenkins 서버에 npm이 최신버전이 다운되지 않는다면 아래 링크를 참고하시면 됩니다.

https://stackoverflow.com/questions/41195952/updating-nodejs-on-ubuntu-16-04

이제 gitlab에 push를 하면 변경을 감지해서 빌드를 자동으로 해주는지 테스트해보겠습니다.
그리고 성공하면 원격 서버로 배포해보겠습니다.

수정을 하고 푸쉬를 하면 빌드를 하고 있는 모습을 보실 수 있습니다.
정상적으로 성공했다면 파란색 아이콘이 떠야합니다.
<img src="/images/jenkinswebhooks20.png">

이렇게 성공 로그가 찍히고요. Jenkins 서버에 가서 확인을 하시면 .jar 파일도 정상적으로 존재할겁니다.
<img src="/images/jenkinswebhooks21.png">

`/var/jenkins_home/workspace/vue_spring/build/libs` 저는 여기에 `demo.jar`라는 이름으로 잘 생성되었습니다.
왜냐하면 `build.gralde`에 아래 내용을 넣어주었기 때문에 이름이 저렇게 생성되었습니다.

```groovy
bootJar {
	archiveFileName = 'demo.jar'
}
```

마지막으로 생성된 .jar 파일을 배포 서버에 보내주고 거기서 .jar를 실행하도록 하겠습니다.
Jenkins 설정 플러그인 관리에 가셔서 `Publish Over SSH`를 설치해줍니다.

그리고 Jenkins 서버에서 아래 명령어로 공개키를 배포서버로 보냅니다.
`ssh-copy-id -i ~/.ssh/id_rsa.pub root(유저이름)@배포서버 IP`

설치가 완료되면 환경설정 -> 시스템 설정으로 들어갑니다.
Publish over SSH 설정을 스크린샷처럼 해줍니다.
<img src="/images/jenkinswebhooks22.png">

**Passphrase** - 접속할 배포 서버의 암호  
**Key** - Jenkins 서버에서 생성한 개인키

그리고 ssh servers 추가를 눌러서 추가로 적습니다.

**Name** - 설정할 아무 이름  
**Hostname** - 배포서버 IP  
**Username** - 배포서버 접속할 유저 이름  
**Remote Directory** - Jenkins 서버에서 보낼 파일을 저장 할 배포서버의 상위 디렉토리  
(/usr/local/server에 저장하고 싶으면 /usr만 적는다.)

그리고 Test Configuration을 눌러서 Success가 뜨면 여기까지는 설정이 잘 완료된겁니다.

이제 jenkins 프로젝트 설정으로 들어가서 `빌드 후 조치 -> Send build artifacts over SSH`을 선택합니다.

<img src="/images/jenkinswebhooks23.png">

시스템 설정에서 Publish over SSH를 정상적으로 설정하셨다면 Name은 자동으로 선택됩니다.

**Source files** - 보낼 파일의 경로입니다.  
**Remove prefix** - 보낼 파일 이외의 제외처리를 할 경로입니다.  
build/libs/demo.jar라고 적었어도 Jenkins는 이 경로에 있는 모든 파일을 보내기때문에
.jar 이외의 폴더나 파일이 필요없으시다면 build/libs/로 제외하셔야합니다.  
**Remote directory** - 파일을 보낼 배포서버의 경로입니다.  
시스템 설정에서 상위디렉토리를 이미 /usr이라고 설정했기때문에 여기서는 local/server/만 적어줍니다.
그러면 /usr/local/server/로 demo.jar가
송신됩니다.  
**Exec command** - 파일을 보내고 실행 할 명령어를 적습니다.  
이상하게 java -jar demo.jar 명령어가 작동하지 않아서 스크립트를 .jar 실행하는 스크립트를 만들어 명령어를 넣었습니다. `> /dev/null 2>&1` 를 넣어주지 않으면 에러가 발생하기때문에 무시처리하겠습니다.  

참고로 스크립트 파일을 자동을 실행하게 만들려면 미리 권한이 조정되어 있어야 합니다.
`chmod 755 run.sh`로 실행할 수 있도록 만듭니다.

이제 푸쉬해서 홈페이지까지 뜨는지 확인해봅니다.
저는 아래 로그처럼 나오고 배포까지 완료되었습니다.

```log
Started by GitLab push by JinSang Park
Running as SYSTEM
Building in workspace /var/jenkins_home/workspace/vue_spring
using credential 11a082b2-5d2a-49e6-9699-048b70087d72
 > git rev-parse --is-inside-work-tree # timeout=10
Fetching changes from the remote Git repository
 > git config remote.origin.url git@gitlab.com:pjs21s/vue-spring.git # timeout=10
Fetching upstream changes from git@gitlab.com:pjs21s/vue-spring.git
 > git --version # timeout=10
 > git --version # 'git version 2.11.0'
using GIT_SSH to set credentials 
 > git fetch --tags --progress -- git@gitlab.com:pjs21s/vue-spring.git +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse remotes/origin/master^{commit} # timeout=10
 > git branch -a -v --no-abbrev --contains 4b594cdc470fdc5eadae7ae2f58fc56ce69c7631 # timeout=10
Checking out Revision 4b594cdc470fdc5eadae7ae2f58fc56ce69c7631 (origin/master)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f 4b594cdc470fdc5eadae7ae2f58fc56ce69c7631 # timeout=10
Commit message: "static 폴더 포함"
 > git rev-list --no-walk f6812bbb1e04a2fa0f8f09a406bec60b1444232b # timeout=10
[Gradle] - Launching build.
[vue_spring] $ gradle bootJar -b build.gradle
Starting a Gradle Daemon (subsequent builds will be faster)
> Task :compileJava UP-TO-DATE
> Task :processResources
> Task :classes
> Task :bootJar

Deprecated Gradle features were used in this build, making it incompatible with Gradle 7.0.
Use '--warning-mode all' to show the individual deprecation warnings.
See https://docs.gradle.org/6.6/userguide/command_line_interface.html#sec:command_line_warnings

BUILD SUCCESSFUL in 6s
3 actionable tasks: 2 executed, 1 up-to-date
Build step 'Invoke Gradle script' changed build result to SUCCESS
SSH: Connecting from host [35eee1387dba]
SSH: Connecting with configuration [vue-ssh] ...
SSH: EXEC: STDOUT/STDERR from command [cd /usr/local/server
./run.sh start > /dev/null 2>&1] ...
SSH: EXEC: completed after 200 ms
SSH: Disconnecting configuration [vue-ssh] ...
SSH: Transferred 1 file(s)
Finished: SUCCESS
```