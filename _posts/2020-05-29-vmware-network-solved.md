---
layout: post
title: "VMWARE ifconfig 성공해도 인터넷 안되는 현상 해결"
tags: Vmware
comments: true
---

vmware에 centos를 설치하고 `network-scripts/ifcfg-`같은 기본 네트워크 설정을 했음에도 인터넷이 되지 않을때가 있다. 심지어 ifconfig를 봐도 이상이 없는데 말이다.

이런 경우에는 vmware 설정이 잘못되어 있을 가능성이 높다.
아래 사진처럼 `Manage → Virtual Machine Setting`에서 `Configure Adapters`를 눌러 실제 사용하는 인터넷과 관련없는 설정은 해제 해주어야 한다. 필자의 경우에는 도커를 삭제했기 때문에 Hyper-V 관련 옵션을 해제해주었다. 나머지는 인터넷을 잡는데 사용하는 도구라서 그대로 두었다.

자동으로 설정 전부를 잡으니까 안되는 경우가 있는것으로 파악된다.
<img src="/images/vmware2.png">

<img src="/images/vmware1.png">

