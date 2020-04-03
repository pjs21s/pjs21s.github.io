---
layout: post
title: "Update Your Amazon RDS SSL/TLS Certificates AWS 인증서 교체"
tags: AWS
comments: true
---

어느 날 메일이 와있었다.

`Update Your Amazon RDS SSL/TLS Certificates by October 31, 2019`

보통 영어 메일이면 무시를 하는데 AWS 메일은 무시하다가 피 보는 경우도 많은거 같고 또 기한이 적혀 있으니 불안해서 놔뒀다가 오늘 읽었다.

그냥 차분히 읽어보니 인증서가 새로나와서 교체를 안하면 SSL/TLS 적용이 안될거라는 말이었다.

근데 AWS EC2나 RDS를 블로그를 보고 설정한 필자이기에 괜히 쫄아서는 혼자서 설정을 할 수 있을까 싶었다.

https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/UsingWithRDS.SSL-certificate-rotation.html

위 링크에 들어가면 메뉴얼이 있는데 결론은 너무 쉽다.

그냥 RDS에서 수정을 누르고 인증서를 2019년 버전으로 교체만 해주고 즉시 적용을 하면 재부팅 후에 잘 동작하는걸 확인할 수 있다.

역시 시작도 전에 쫄지말자~

<img src="/images/awscachange.png">