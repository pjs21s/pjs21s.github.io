---
layout: post
title: "Spring Maven 배포와 오류 메모"
tags: Error
comments: true
---

원래 Eclipse에서 Maven 배포를 할 때 Goals에 `tomcat:undeploy tomcat:deploy`라고 적어줬다.

처음 본 자료에서 이렇게 해야 배포한걸 없애고 새로 배포한다고 했기 때문이다.

그런데 어제 `JUnit`으로 테스트를 하고 오늘 다시 배포를 하려니까 오류가 났다.

문제는 특정 오류를 나타내주는 문구는 하나도 없이 그냥 

`Cannot invoke Tomcat manager: FAIL - No context exists named`

이렇게만 뜨는 바람에 해결도 못하고 이제 망하는건가 싶었다.

그래서 이것저것 찾아보다가 속는셈 치고 `tomcat:redeploy`로 바꿨는데 바로 해결이 됐다...

이유는 모르겠지만 그리고 사람들이 어떻게 설정하는지 봤더니

`clean tomcat:redeploy -e` 이렇게 적는게 제일 좋아보였다.

`clean`으로 기존 target 폴더를 한번 지워주고 뒤에 `-e`를 붙여서 오류를 좀 더 상세하게 볼 수 있게 하였다.

그리고 `Skip Tests`에 체크를 해서 배포할때는 테스트를 가동하지 않았다.

아직 완벽하게 테스트 코드를 짜놓은게 아니기에 괜한 오류가 발생할것 같기 때문이다.

<img src="/images/mavenerror.png">
