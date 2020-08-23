---
layout: post
title: "Mac os 디스크 유틸리티 포맷 불가 - 파티션 맵을 수정할 수 없습니다 해결 방안"
tags: Mac
comments: true
---

빅서 다운그레이드를 위해 클린 설치를 위한 USB 시동 디스크를 만들려는데 포맷이 불가능했다.

`파티션 맵을 수정할 수 없습니다.`
`MediaKit이 기기에 요청된 작업을 위한 충분한 공간이 없음을 보고합니다`

이런 오류가 발생했고 그래서 포맷을 터미널로 하는 방법을 적어두려고 한다.

터미널을 열어서 `diskutil list`로 포맷을 진행 할 디스크의 번호를 확인한다.
저는 `disk3`이고 `disk3`을 기준으로 포맷 진행하겠습니다.

`diskutil unmountDisk force disk3`

`sudo dd if=/dev/zero of=/dev/disk3 bs=1024 count=1024`

`diskutil partitionDisk disk3 GPT JHFS+ "My Volume" 0g`

여기서 모든 명령어의 `disk3`부분과 마지막 명령어의 `My Volume`은 본인이 포맷할 디스크에 맞게 변경하셔야 합니다. `My Volume`은 제 USB의 이름이었습니다. 이렇게 하면 디스크 유틸리티에서 되지 않는 디스크를 강제로 포맷할 수 있습니다.