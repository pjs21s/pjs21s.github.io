---
layout: post
title: "맥, 윈도우 putty 서버에서 로컬로 파일 복사하기"
tags: scp
comments: true
---

키파일을 사용해야 하는 경우
`scp -i [키파일] <remote user>@<remote machine>:<서버의 파일 경로> <내 컴퓨터의 파일 경로>`

개별 파일
`scp root@45.32.23.160:~/websaver/latest.txt ~/Desktop/test`

폴더 전체는 -r Recursive 옵션 사용
`scp -r root@45.32.23.160:~/websaver ~/Desktop/test/`

putty pscp
pscp [서버접속계정]@[서버접속 아이피]:[다운로드 받을 서버 디렉토리] [다운받을 윈도우 디렉토리]
pscp root@123.45.67.890:/usr/var/* c:\temp

많은 양을 옮겨야 할때는 파일질라 같은 GUI 프로그램을 사용하기 보다 이런 방식으로 옮기는게
더 안정적이고 빠르다.

출처 : <https://macinjune.com/all-posts/mac/terminal/맥-터미널-유닉스-scp-ftp-파일-공유/>