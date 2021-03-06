---
layout: post
title: "디버깅(Debugging) 기초 실제로 해보기"
tags: Java
comments: true
---

오늘은 기초적인 디버그 방법을 포스팅 하겠습니다.
개인적으로 작게 프로젝트를 할 때는 디버깅을 할 필요를 느끼기 힘듭니다.
하지만 실제로 일을 하게 되면 디버깅을 잘 하는 게 얼마나 필요한 능력인지 깨닫게 됩니다.

그래서 실제로 어떻게 사용하는지에 집중해서 포스팅을 하겠습니다.
제가 예전에 디버깅을 해보고 싶어 찾아봐도 잘 없었던 것 같아서 정리합니다.

크롬 개발자 도구를 가지고 하는 법과 이클립스에서 디버그 모드를 사용하는 법을 적겠습니다.

f12를 눌러 개발자 도구를 열면 아래 사진과 같이 마우스 모양의 버튼이 있습니다.
지금까지 저는 보고 싶은 요소가 있으면 `마우스 오른쪽 클릭 -> 검사`를 통해서 소스를 봤는데요.
그럴 필요 없이 아래 사진에 있는 버튼을 누른 후 요소를 클릭하면 바로 소스를 볼 수 있습니다.

<img src="/images/howtodebug1.png">

개발자 도구를 가지고 디버깅을 하는 경우는 자바스크립트 기능이 왜 동작하지 않는지 혹은 잘 작동하는지를 보기 위해서입니다. 이클립스에서는 순수 자바 오류만 확인할 수 있기 때문입니다.
아래와 같이 작동하는지 보고 싶은 코드 옆에 있는 번호에 더블 클릭을 하면 breakpoint(중단점)을 찍을 수 있습니다.

<img src="/images/howtodebug2.png">

중단점을 찍으면 코드를 한 줄씩 돌려보면서 어디서 오류가 발생하는지 정확히 확인할 수 있습니다.
F10을 누르거나 화면 왼쪽에 있는 `Paused in debugger` 정지 화살표 옆에 있는 진행 버튼을 누르면 한 줄 씩 진행합니다. 제 코드는 그냥 ajax 정의 자체가 잘못되어서 작동을 안 하는 것이므로 굳이 중단점을 찍지 않아도 문제가 있다고 알려줍니다.

더 간단한 에러라면 console에서 미리 알려주기도 합니다.

<img src="/images/howtodebug3.png">

만약 이런 도구를 사용하지 않는다면 Ajax나 다른 javascript 기능을 구현할 때 왜 동작하지 않는지 정확히 원인을 파악하기 쉽지 않습니다. 그렇기에 도구를 잘 사용하는 것도 능력 중 하나입니다. 학생 때 이걸 알았더라면 Ajax를 더 재밌게 공부했을 겁니다... ㅠㅠ

Javascript 경우에는 수정해도 캐시에 남아있을 수 있으므로 캐시 비우기 및 강력 새로고침을 사용해서 다시 테스트 하는 것이 좋다. (개발자 도구 실행 중에만 사용 가능)

# 이클립스 디버깅 모드 사용하기

이번엔 이클립스에서 디버깅 모드를 사용해봅니다.
이클립스에서 디버깅 모드를 사용하는 이유는 스프링에서 `ServiceImpl`이나 `Controller`에서 정의한 코드가 잘 동작하는지를 관찰하기 위해서 입니다.

실행 방법은 비슷합니다. 프로젝트 우클릭 후 `Debug as -> Debug on Server`로 실행하면 됩니다.
그리고 개발자 도구에서 했던것처럼 관찰하고 싶은 코드 옆에 번호에 중단점을 찍습니다.
그리고 홈페이지에서 그 기능을 실행하면 아래와 같이 알림이 뜨는데 Switch를 눌러서 변경해줍니다.

<img src="/images/howtodebug4.png">

그러면 다른 화면으로 바뀌는데 개발자 도구에서 했던 것처럼 중단점부터 한줄씩 실행해볼 수 있다.
편하게 진행하기 위해 단축키를 사용하는데 이건 설명보다 직접 사용해보면서 익히는게 낫다.

<img src="/images/howtodebug5.png">

# 이클립스 디버깅 단축키

F5 - Step Into : 해당 라인을 실행한다. 단 현재 라인에 함수가 포함되어 있다면 그 함수 속으로 들어간다. 앞의 예에서 F6 대신 F5를 눌렀다면 cout 이 포함된 iostream의 소스 코드가 표시될 것이다.
F6 - Step Over : 해당 라인을 실행한다. 함수가 포함되어 있어도 함수 속으로 들어가지 않고 함수 실행 결과를 가지고 현재 라인 끝까지 실행한다.
F7 - Step Return : 현재 함수의 끝까지 실행하여 현재 함수를 벗어난다.
F8 - 다음 break point 까지 한번에 실행한다. break point가 없다면 프로그램 종료시까지 멈춤없이 실행한다.

단축키 출처 : [https://baljuhee.tistory.com/161]

디버깅은 여러 사람이 한 프로젝트에서 코드를 만들거나 유지보수를 위해 내가 모르는 코드를 보고 이해할때 사용하면 좋다. 그리고 DB를 결국은 많이 사용하기 때문에 특정 기능을 사용할 때 관련 Query가 이클립스 콘솔에 표시되게 하면 더욱 도움이 된다. 이와 관련한 자료는 많은거 같으니 일단 패스