---
layout: post
title: "이클립스 war 추출 시 class, java 파일 포함되지 않는 현상"
tags: Eclipse
comments: true
---

Eclipse에서 종종 export war를 하면 class, java 파일이 포함되어 있지 않아서
배포를 하고 나서 오류가 나는 경우가 있습니다.

해결 방법에 대해 알아보겠습니다.

우선 해당 프로젝트를 우클릭하여 `Run As -> Run Configurations`를 클릭합니다.
<img src="/images/exportwar1.png">

좌측 `Maven Build`에 새로운 설정을 만드시고 사진처럼 `Goals`에 `Package`를 적어줍니다.
<img src="/images/exportwar2.png">

Build Success가 되면 target 폴더 안에 war 파일이 하나 생성됩니다.
<img src="/images/exportwar3.png">

export war를 해서 생성한 war 파일과 동일하므로 이걸 사용하시면 됩니다.
