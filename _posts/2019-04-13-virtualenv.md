---
title: "django Virtualenv, requirements 정리"
comments: true
layout: post
tags: Django
---

**Django Virtualenv 정리**

Python, Django는 이미 설치가 되어 있다는 가정하에 진행한다.

1. 프로젝트를 진행할 폴더로 이동한다.

2. pip install virtualenv 를 설치한다.

3. virtualenv [가상환경 이름] ex) virtualenv myvenv
4. myvenv\Scripts\activate로 실행한다.
5. 비활성화를 하고 싶다면 deactivate 명령어를 이용한다.
6. pip Package를 사용했던게 있다면 전부 다운로드 해주어야 한다.
  
    6.1. 미리 pip freeze >> requirements.txt를 이용해 Package를 txt로 만들어서 가지고 있을 수 있다.
  
    6.2. pip install -r requirements.txt로 한번에 Package를 설치할 수 있다.
