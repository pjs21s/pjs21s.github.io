---
layout: post
title: "Mac os ventura 업데이트 후 파이썬 관련 라이브러리 실행못하는 원인"
tags: Python
comments: true
---

ventura 업데이트 후 문제없이 사용하던 파이썬 프로그램이 라이브러리를 찾지 못해 실행하지 못하는 현상이 발생했다.

```shell
honggildong@honggildong-MacBookPro ~ % ./honggildong_call.sh
default
Traceback (most recent call last):
  File "/Users/honggildong/./honggildong_call.py", line 9, in <module>
    import boto3
ModuleNotFoundError: No module named 'boto3'
```

문제 없이 사용하고 있고 pip3 freeze로 확인하여도 있는데 실행이 안되는 이유가 무엇일지 찾아보니
해결 방법이 아래와 같이 나왔다.

제가 사용하고 있는 파이썬 프로그램 상단에는 파이썬 경로가 기록되어 있었는데 그 디렉토리 주소가 맞지 않다보니
발생하는 문제였다.

그래서 아래와 같이 조치하였다.

1. zsh shell로 실행하였는지 확인
2. python3 -> import sys -> sys.executable 명령어로 현재 파이썬 위치 기록
	2.1. 예시: opt/homebrew/opt/python@3.9/bin/python3.9
3. honggildong_call.py, honggildong_call.py 파일 상단에 적혀있는 파이썬 위치를 본인 파이썬 위치에 맞게 수정
	3.1. #!/usr/bin/python3 -> #!/opt/homebrew/opt/python@3.9/bin/python3.9