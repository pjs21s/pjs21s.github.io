---
layout: post
title: "간단하게 실행 중인 docker container에 포트포워딩 다시 설정하기"
tags: Docker
comments: true
---

도커에 센토스(컨테이너)를 올려서 배포하고 접근하려면 처음부터 도커를 run할때 포트포워딩을 해야합니다.
도커 네트워크 특성때문입니다. 그래서 처음 사용할 때 모르고 컨테이너를 만들었다가 접근해야 할 일이 생기면 혹시 다시 만들어야 하는게 아닌지 걱정이 듭니다.

그래서 간단하게 실행 중인 도커 컨테이너에 포트포워딩을 하는 방법을 적겠습니다.

```
docker stop testContainer
docker commit testContainer testForPort
docker run -p 6060:6060 -d testForPort
```

이런식으로 하면 실행 중인 컨테이너를 멈추고 새로운 이미지로 커밋하여 그걸 기준으로 포트포워딩을 하게 됩니다.