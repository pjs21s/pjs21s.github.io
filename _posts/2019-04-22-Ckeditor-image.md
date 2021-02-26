---
layout: post
title: "CKEditor 이미지 업로드 문제 해결"
tags: Django Ckeditor
comments: true

---



CKEditor에 있는 이미지 업로드 기능을 사용하려니 생각보다 설정하고 수정해야할게 많았다.

그리고 한국어로 된 자료는 거의 나오지도 않아서 영어 자료를 일일히 찾아봐야 했다.

한번 정리를 해보자



우선 **static**과 **media**의 개념부터 잡자

* static : 개발에서 사용하는 정적인 파일을 의미한다.

* media : 유저가 업로드하는 정적인 파일을 의미한다.



CKEditor에서 이미지 업로드를 수행하기 위한 기본 설정은 검색하거나 공식 문서를 보고 하면 된다.

공식문서 : <https://github.com/django-ckeditor/django-ckeditor>



하지만 참고하실분들을 위해 제 소스도 올리겠습니다.

```python
#settings.py
STATIC_URL = '/static/'
CKEDITOR_UPLOAD_PATH = "uploads/"
CKEDITOR_BASEPATH = "/static/ckeditor/ckeditor/"
CKEDITOR_IMAGE_BACKEND = "pillow"
CKEDITOR_JQUERY_URL = 'https://ajax.googleapis.com/ajax/libs/jquery/2.2.4/jquery.min.js'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media/')
MEDIA_URL = '/media/'
```



```python
#models.py
from ckeditor_uploader.fields import RichTextUploadingField
...
class Post(models.Model, HitCountMixin):
    ...
	text = RichTextUploadingField()
```

디렉토리 구조도 참고하세요.

<img src="/images/ckeditor_image.png">

문제는 여기까지 했는데도 로컬에서 테스트를 하려니 자꾸

 `Not Found: /media/uploads/2019/04/22/test.jpg` 

혹은 `AttributeError: 'NoneType' object has no attribute 'split'`

이런 식으로 에러가 발생합니다.

그래서 찾아보니 개발환경에서는 media 파일을 다루려면 따로 설정 값을 넣어줘야 한다고 합니다.

```python
from django.conf import settings
from django.conf.urls.static import static
...
urlpatterns = [
    # 본인의 나머지 URL, path 값
	...
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

이렇게 하고 나니 제 컴퓨터에서는 이미지 업로드가 잘 작동했습니다.

그래서 `pythonanywhere`에 배포를 했는데 파일을 업로드 하니까

`Incorrect server response`오류가 발생합니다.

그리고 서버를 클릭하니까 갑자기 django admin 페이지가 올라옵니다.



찾아보니 ckeditor_uploader의 urls.py의 원본 소스를 보라는 말이 나옵니다.

필요한 부분만 가져왔습니다.

```python
#original code urls.py
from django.contrib.admin.views.decorators import staff_member_required
from django.views.decorators.cache import never_cache
...

url(r'^upload/', staff_member_required(views.upload), name='ckeditor_upload'),
url(r'^browse/', never_cache(staff_member_required(views.browse)), name='ckeditor_browse')
```

기능을 사용하려는데 admin 페이지가 뜬 이유는 바로 `staff_member_required`라는 `decorator` 때문입니다.

이 `decorator`는 유저가 관리자로 로그인이 되어있는지 체크를 해줍니다.

즉 ckeditor_uploader의 기본값은 관리자만 사용할 수 있게 되어있습니다.

저는 login한 사용자라면 모두가 사용가능하게 할것이기 때문에 아래와 같이 바꾸었습니다.

pythonanywhere에서도 디렉토리를 찾아서 코드를 변경할 수 있기 때문에 사이트 내에서 파일을 찾아서 변경하시면 됩니다.

```python
from django.contrib.auth.decorators import login_required
from django.views.decorators.cache import never_cache
...
url(r'^upload/', login_required(views.upload), name='ckeditor_upload'),
url(r'^browse/', login_required(never_cache(views.browse)), name='ckeditor_browse'),
```

그러면 아래와 같이 잘 작동합니다.

<img src="/images/ckeditor_image2.png">
