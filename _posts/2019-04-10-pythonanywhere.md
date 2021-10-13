---
layout: post
title: "django Pythonanywhere 설정법과 에러 정리"
tags: Pythonanywhere
comments: true
---
<h6>이 포스트까지 네이버에서 옮긴 글</h6>

1. 우측 상단에 있는 signup을 눌러 회원가입을 한다.
2. 요금을 지불할 게 아니라면 Create a Beginner account를 클릭한다.

회원가입이 완료 후 Consoles의 Bash를 누른다.
이런 페이지가 안 보이면 우측 상단에 DashBoard를 누르면 된다.

<img src="/images/anywhere1.png">
<img src="/images/anywhere2.png">

github에서 클론을 다운로드해 배포하는 게 나중에 수정사항이 생겨 업데이트를 할 때도 편한 거 같아
github에 먼저 올린 후 git 주소를 가져와 아래와 같이 적는다.

```$ git clone https://github.com/pjs21s/Third.git // 본인이 사용하고자 하는 git 주소```

가상환경 설정을 위해 git에서 가져온 프로젝트가 있는 폴더로 이동해서 아래와 같이 작업을 시작한다.

```
$ cd <프로젝트 폴더 이름> // 프로젝트로 이동하기 위함
// 뒤로 가려면 cd - 명령어를 사용

$ virtualenv --python=python3.7 myvenv // python은 본인이 사용하고자 하는 버전에 맞게 고치면 된다.
// myvenv는 가상환경 폴더의 이름이므로 본인이 변경하고 싶으면 다른 이름으로 생성해도 된다.

$ source myvenv/bin/activate // myvenv 가상환경 활성화

(myvenv) $ pip install django // (가상환경 이름) $ -> 이와 같은 형식이면 활성화 된 것
// 비활성화는 $ (myvenv) deactive
// 새로운 환경이므로 기존에 pip로 설치했던게 있다면 여기서 전부 다시 설치해야 한다.

// 필자는 pip install django-bootstrap4, pip install pillow, pip install misaka, pip install django-braces 전부 설치했다.
// 190410 이후로는 requirement.txt로 한번에 설치
```

pip 설치는 조금 느릴 수 있는 여유롭게 기다리면서 해야 한다.
데이터베이스를 생성하자

```
(myvenv) $ python manage.py migrate
(myvenv) $ python manage.py makemigrations
(myvenv) $ python manage.py createsuperuser
//환경이 바뀌어서 admin과 db에 대한 명령도 다시 해줘야한다.
```
<img src="/images/anywhere3.png">

createsuperuser까지 마쳤다면 Web 탭으로 가서 아래와 같은 버튼을 클릭한다.

<img src="/images/anywhere4.png">

그러면 프레임워크를 선택해야 하는데 Manual configuration을 선택한다.
python 3.7을 선택한다. (본인 버전에 맞는 python 선택)

가상환경을 연결하기 위해 본인이 만들었던 myvenv(가상환경 폴더)의 경로를 적어준다.
Bash에서 폴더 있는 곳에 가서 pwd를 입력하면 현재 경로를 알려준다.
경로가 다르면 에러가 발생하니 경로를 제대로 입력해주자

<img src="/images/anywhere5.png">

그 후 django 프로젝트 내의 settings.py을 열어 ALLOWED_HOSTS를 설정해준다.
pythonanywhere 사이트 안에서도 에디터로 수정할 수 있으니 외부 프로그램은 사용하지 않아도 된다.

<img src="/images/anywhere6.png">

```ALLOWED_HOSTS = ['Example.pythonanywhere.com'] // 본인에게 할당되는 주소```

마지막으로 WSGI configuration file을 수정해줘야 한다.
django 프로젝트 내부에 있는 게 아니라 pythonanywhere에 있는 wsgi를 수정해야 한다.

Virtualenv가 있는 곳 바로 위에 WSGI configuration file 디렉토리가 있고 그걸 누르면 바로 WSGI 파일로 이동할 수 있다.
내용을 전부 지우고 아래의 내용을 복사 후 붙여 넣고 본인에 맞는 path와 os.environ 값을 넣는다.

```python
import os
import sys

path = '/home/pjs21s/Third/Project/versio' // 본인 django 프로젝트 디렉토리
if path not in sys.path:
    sys.path.append(path)

os.environ['DJANGO_SETTINGS_MODULE'] = 'versio.settings' // settings.py을 가지고 있는 django 프로젝트 디렉토리의 이름

from django.core.wsgi import get_wsgi_application
from django.contrib.staticfiles.handlers import StaticFilesHandler
application = StaticFilesHandler(get_wsgi_application())
```

전부 끝났으면 상단에 있는 Reload를 누른 후 본인에게 부여된 주소로 들어가서 작동이 정상적인지 테스트를 하면 된다.


**DisallowedHost at** 에러는 ALLOWED_HOSTS에 옳지 않은 값이 들어가 있다면 발생하므로 알맞게 수정해준다.
업데이트한다고 git pull 한 후 settings.py에 없어서 오류가 나기도 한다.


**git pull** 에러는 Branch를 다 Merge 해버리고 삭제 후 git reset --hard 그리고 다시 git pull을 하니 어떻게 해결이 됐다.


**수정사항 배포**
virtualenv 실행 확인, 프로젝트 상위 폴더에서 $ git pull origin master
