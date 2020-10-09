---
layout: post
title: "Docker image 로컬에 저장하고 불러오기"
tags: Docker
comments: true
---

도커 이미지를 tar로 저장하기
`docker save -o c:\images\testImage.tar sourceBlah/testImage`

`-o`는 경로를 지정하는 명령어이다.
`sourceBlah/testImage` 저장할 이미지 이름이다.

저장 한 도커 이미지를 불러오기
`docker load –i c:\temp\testImage.tar`

`-i`는 불러올 tar가 있는 경로를 지정하는 명령어이다.
`c:\temp\testImage.tar` tar가 있는 전체 경로이다.

반드시 save 명령어로 저장한 tar는 load로만 불러올 수 있다.
특정 저장 명령어를 사용했다면 그 명령어와 맞는 load 명령어로만 불러올 수 있다.

원본 : [https://www.assistanz.com/import-and-export-docker-images/](https://www.assistanz.com/import-and-export-docker-images/)