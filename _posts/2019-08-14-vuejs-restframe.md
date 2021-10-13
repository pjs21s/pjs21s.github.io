---
layout: post
title: "Vue.js Django CRUD REST 해보기"
tags: Vue
comments: true
---

출처: https://medium.com/quick-code/crud-app-using-vue-js-and-django-516edf4e4217

Vue.js를 배우고 있으니 Django와 연동해서 만들 수 있으면 좋겠다는 생각을 했다.
근데 찾아봐도 한국 자료는 없는거 같아서... 내가 이해를 못하는건지 하여튼
찾아보니 Medium에 튜토리얼이 있어서 따라해봤다.
그리고 Vue resource를 알아보기 위해 검색하니 또 axios를 쓰는게 인증된 방식이라고 한다...
그래서 axios로 수정한 코드도 마지막에 넣었다.


여기에 포스팅을 하는 내용은 제 주관적인 내용이 더 들어가 있기에 원문을 보실 수 있다면 보시는게 더 좋습니다.
필자는 Django에 대한 기본 이해가 있는 상태이므로 Django에 대한 설명은 본문에 비해 생략이 많이 됩니다.

* Django 설치
* Django-Project와 app 생성
* 모델을 만들고 마이그레이션
* Django-rest-framework 설치
* Serializer, Viewset 그리고 Routers를 생성
* Vue.js와 Django를 연결


# Django를 설치하자

빈 폴더를 하나 만들고 `python -m venv myvenv`로 가상환경을 만듭니다.
`myvenv\Scripts\activate`로 가상환경을 실행합니다.
가상환경은 독립된 환경이므로 Django와 같은 것들을 다시 설치해야합니다.
`pip install django`

# Django-proejct와 app 만들기

Django까지 설치를 했다면 가상환경을 실행한 상태에서 `django-admin startproject myproject`를 만듭니다.
`cd myproject`로 프로젝트 안으로 이동한후에 `python manage.py startapp article`을 생성합니다.
만약 이 명령어가 작동하지 않는다면 `manage.py`가 없는 곳에서 명령어를 실행했을 가능성이 높기때문에 확인을 다시 한 번 하시는게 좋습니다.
`settings.py`에 가서 아래와 같이 추가합니다.
`article` 사용하기 위해 등록하는 것입니다.

```python
INSTALLED_APPS = [
    ...
    'article',
]
```

저는 템플릿 경로도 기존에 사용하던대로 추가해주었습니다.

```python
TEMPLATE_DIR = os.path.join(BASE_DIR, 'templates')
...
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [TEMPLATE_DIR,],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

# 모델을 만들고 마이그레이션

`article` 폴더 안에 있는 `models.py`에 가셔서 `Article`모델을 생성합니다.

```python
from django.db import models

class Article(models.Model):
    article_id = models.AutoField(primary_key=True)
    article_heading = models.CharField(max_length=250)
    article_body = models.TextField()
```

모델을 생성했으니 마이그레이션을 해서 DB와 연결해줍니다.

`python manage.py makemigrations`

`python manage.py migrate`

# Django-rest-framework 설치

REST에 대한 전반적인 설명은 아래 링크를 보시면 너무나도 잘 정리되어 있습니다.

[[Network\] REST란? REST API란? RESTful이란? - Heee's Development Blog](https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html)

django rest framework를 설치합니다.

`pip install djangorestframework`

`settings.py`에도 추가해줍니다.

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```

이제 70% 정도를 완료했습니다!



# Serializer, Viewset & Routers를 만들자

이제 `serializers.py`를 `article`안에 만들어줍니다.
디렉토리 구조가 헷갈리실 수 있으니 구조도 올립니다.

```
article
│--  admin.py
│--  apps.py
│--  models.py
│--  serializers.py
│--  tests.py
│--  views.py
│--  viewsets.py
│--  __init__.py
```

Serializers는 queryset, 모델 인스턴스와 같은 복잡한 데이터를 조금 더 쉽게 `JSON`, `XML` 혹은 다른 타입으로 변경하게 해줍니다. 아마 형태에 얽매이지 않고 데이터를 사용하기 위해 사용하는게 아닌가 싶습니다.



`serializers.py`에 아래와 같이 코드를 작성합니다.

```python
from rest_framework import serializers
from .models import Article

class ArticleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = '__all__'
```

처음에 serializers를 import하고 모델을 import 해야합니다.

그 후 같은 폴더에 `viewsets.py`를 생성합니다.

```python
from rest_framework import viewsets
from .models import Article
from .serializers import ArticleSerializer

class ArticleViewSet(viewsets.ModelViewSet):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
```

viewsets은 django의 view와 같은 보여주기 위한 로직을 담당하는 부분이 아닌가 싶습니다.

`settings.py`이 있는 `myproject`폴더에 `routers.py`를 생성합니다.

주소와 보여질 데이터를 정의해놓은 `ArticleViewSet`과 연결합니다.

```python
from rest_framework import routers
from article.viewsets import ArticleViewSet

router = routers.DefaultRouter()

router.register(r'article', ArticleViewSet)
```

이로 인해 이제 주소는 localhost:8000/article 이렇게 시작합니다.

`urls.py`에 api 주소를 정의합니다.

```python
from django.contrib import admin
from django.urls import path, include
from .routers import router

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include(router.urls)),
]

```

이렇게 되면 /api/article로 접속했을때 api - article이 잘 작동하는지 테스트해볼 수 있습니다.

<img src = "/images/rest1.png">



**GET**: /api/article/

모든 기사를 가져옵니다.

**POST**: /api/article

새로운 기사를 추가합니다.

**DELETE**: /api/article/{article_id}/

기사를 삭제합니다.

**GET**: /api/article/{article_id}/

특정 기사를 가져옵니다.

**PUT**: /api/article/{article_id}/

특정 기사를 수정합니다.

PATCH는 모르겠...

# Vue.js와 Django를 연결

`article`폴더 내에 `templates`을 만들고 그 안에 `article`폴더를 다시 만들고 `index.html`을 생성합니다.

article - templates - article - index.html

```html
<html>
    <head>
        <meta charset="utf-8">
        <title>Vue-js | Django</title>
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <!--Bootstrap-->
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
    </head>
    <body>
    <!-- bootrtap js files -->
  	<script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
  	<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
  	<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>

  <!-- vue.js files -->
  <script src="https://cdn.jsdelivr.net/npm/vue@2.5.13/dist/vue.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/vue-resource@1.3.5"></script>
    </body>
</html>
```

Bootstrap과 Vue.js의 CDN을 넣어줍니다.
Vue-resource도 REST API를 불러오기 위해 사용합니다.
앞에서도 말씀드렸듯이 이제는 axios를 사용해야 합니다...
하지만 일단 REST API에 익숙해지기 위해 이걸로 기초를 쌓는다고 생각하겠습니다.

`urls.py`을 업데이트 합니다.

```python
from django.contrib import admin
from django.urls import path, include
from .routers import router
from django.views.generic import TemplateView

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include(router.urls)),
    path('article', TemplateView.as_view(template_name='article/index.html')),
]
```

```javascript
<script type="text/javascript">
new Vue({
      el: '#starting',
      delimiters: ['${','}'],
      data: {
        articles: [],
        loading: true,
        currentArticle: {},
        message: null,
        newArticle: { 'article_heading': null, 'article_body': null },
      },
    mounted: function() {
        
    },
    methods; {
    
	},
 })
</script>
```

Vue.js에서 `el`은 html에서의 `id`를 의미한다.
data에는 사용할 변수들을 정의해준다.
delimiters는 vue.js data를 사용할때 사용할 태그를 정의한 것이다.
기본값은 `["{{", "}}"]` 이것인데 Django와 겹치기 때문에 위와 같이 다르게 정의한다.
mounted는 화면을 렌더링 한 후에 작동하게 하기위해 사용한다.

```javascript
 mounted: function() {
        this.getArticles();
      },
      methods: {
        getArticles: function() {
          this.loading = true;
          this.$http.get('/api/article/')
              .then((response) => {
                this.articles = response.data;
                this.loading = false;
              })
              .catch((err) => {
                this.loading = false;
                console.log(err);
              })
        },
        getArticle: function(id) {
          this.loading = true;
          this.$http.get(`/api/article/${id}/`)
              .then((response) => {
                this.currentArticle = response.data;
                $("#editArticleModal").modal('show');
                this.loading = false;
              })
              .catch((err) => {
                this.loading = false;
                console.log(err);
              })
        },
        addArticle: function() {
          this.loading = true;
          this.$http.post('/api/article/',this.newArticle)
              .then((response) => {
                this.loading = true;
                this.getArticles();
              })
              .catch((err) => {
                this.loading = true;
                console.log(err);
              })
        },
        updateArticle: function() {
          this.loading = true;
          this.$http.put(`/api/article/${this.currentArticle.article_id}/`, this.currentArticle)
              .then((response) => {
                this.loading = false;
                this.currentArticle = response.data;
                this.getArticles();
              })
              .catch((err) => {
                this.loading = false;
                console.log(err);
              })
        },
        deleteArticle: function(id) {
          this.loading = true;
          this.$http.delete(`/api/article/${id}/`)
              .then((response) => {
                this.loading = false;
                this.getArticles();
              })
              .catch((err) => {
                this.loading = false;
                console.log(err);
              })
        }
```



기본적으로 아래와 같은 구조를 가진다.

.then과 같은 문법을 promise라 하고 => 화살표 문법이라고 한다.

화살표 문법을 사용할때는 this에 대해서 잘 알때만 사용해야한다.

```javascript
this.$http.request_type(‘api_url’,payload)
    .then((response) => {
      // code if the api worked successfully
    })
    .catch((err) => {
      // code if the api show some error
    })
```

```html
<div id="starting">
      <div class="container">
        <div class="row">
          <h1>List of Articles
          <button  type="button" class="btn btn-primary" data-toggle="modal" data-target="#addArticleModal">ADD ARTICLE</button>
          </h1>
          &emsp;
          <div class="form-inline my-2 my-lg-0" style="margin-left: 40%;">
            <input class="form-control mr-sm-2" type="text" placeholder="Search" v-model="search_term" aria-label="Search">
            <button class="btn btn-outline-success my-2 my-sm-0" @click.prevent="getArticles()">Search</button>
          </div>
          <table class="table">
            <thead>
              <tr>
                <th scope="col">#</th>
                <th scope="col">Heading</th>
                <th scope="col">Action</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="article in articles">
                <th scope="row">${article.article_id}</th>
                <td>${article.article_heading}</td>
                <td>
                  <button class="btn btn-info" @click="getArticle(article.article_id)">Edit</button>
                  <button class="btn btn-danger" @click="deleteArticle(article.article_id)">Delete</button>
                </td>
              </tr>
            </tbody>
          </table>
          </div>
       </div>
      <div class="loading" v-if="loading===true">Loading&#8230;</div>
    </div>
```

위와 같이 html을 추가하고

index.html에 있는 modal 버튼 코드를 수정한다.

```html
<button  type="button" class="btn btn-primary" data-toggle="modal" data-target="#addArticleModal">ADD ARTICLE</button>
```

table 태그 밑에 아래와 같이 Add Article Modal을 추가한다.

```html
<!-- Add Article Modal -->
          <div class="modal fade" id="addArticleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLongTitle" aria-hidden="true">
            <div class="modal-dialog" role="document">
              <div class="modal-content">
                <div class="modal-header">
                  <h5 class="modal-title" id="exampleModalLongTitle">ADD ARTICLE</h5>
                  <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                  </button>
                </div>
                <form v-on:submit.prevent="addArticle()">
                <div class="modal-body">
                    <div class="form-group">
                      <label for="article_heading">Article Heading</label>
                      <input
                        type="text"
                        class="form-control"
                        id="article_heading"
                        placeholder="Enter Article Heading"
                        v-model="newArticle.article_heading"
                        required="required" >
                    </div>
                    <div class="form-group">
                      <label for="article_body">Article Body</label>
                      <textarea
                        class="form-control"
                        id="article_body"
                        placeholder="Enter Article Body"
                        v-model="newArticle.article_body"
                        required="required"
                        rows="3"></textarea>
                    </div>
                </div>
                <div class="modal-footer">
                  <button type="button" class="btn btn-secondary m-progress" data-dismiss="modal">Close</button>
                  <button type="submit" class="btn btn-primary">Save changes</button>
                </div>
                </form>
              </div>
            </div>
            <div class="loading" v-if="loading===true">Loading&#8230;</div>
          </div>
          <!-- End of add article modal -->
```

**Edit Modal**

`<button class="btn btn-info" v-on:click="getArticle(article.article_id)">Edit</button>`

Script 코드는 이미 위에 추가를 해두었으니 html에만 추가하면 된다.

```html
<!-- Edit Article Modal -->
          <div class="modal fade" id="editArticleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLongTitle" aria-hidden="true">
            <div class="modal-dialog" role="document">
              <div class="modal-content">
                <div class="modal-header">
                  <h5 class="modal-title" id="exampleModalLongTitle">EDIT ARTICLE</h5>
                  <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                  </button>
                </div>
                <form v-on:submit.prevent="updateArticle()">
                <div class="modal-body">
                    <div class="form-group">
                      <label for="article_heading">Article Heading</label>
                      <input
                        type="text"
                        class="form-control"
                        id="article_heading"
                        placeholder="Enter Article Heading"
                        v-model="currentArticle.article_heading"
                        required="required" >
                    </div>
                    <div class="form-group">
                      <label for="article_body">Article Body</label>
                      <textarea
                        class="form-control"
                        id="article_body"
                        placeholder="Enter Article Body"
                        v-model="currentArticle.article_body"
                        required="required"
                        rows="3"></textarea>
                    </div>
                </div>
                <div class="modal-footer">
                  <button type="button" class="btn btn-secondary m-progress" data-dismiss="modal">Close</button>
                  <button type="submit" class="btn btn-primary">Save changes</button>
                </div>
                </form>
              </div>
            </div>
            <div class="loading" v-if="loading===true">Loading&#8230;</div>
          </div>
          <!-- End of edit article modal -->
```



**DELETE**

한줄만 추가하면 된다.

`<button class="btn btn-danger" v-on:click="deleteArticle(article.article_id)">Delete</button>`

`python manage.py runserver`

`http://localhost:8000/article`에 들어가서 테스트를 해보면 잘 작동한다.

혹시 작동을 하지 않으면 오타나 순서문제일 가능성이 있으니 검토하거나 그래도 작동하지 않으면 원문 하단에 github 주소가 있으니 거기서 코드를 보면 된다.

완벽하게 이해를 한건 아니지만 Vue.js 강의를 좀 더 듣고 axios를 다시 해보면 조금 더 이해할 수 있지 않을까 싶다.

그래서 몇 시간을 걸쳐서 고쳤다... 사실 수정하는건 별거 아닌데 코드 하나를 잘못건드려서 원인을 알아내느라 오래걸렸다.

우선 Script에 있는 vue-resource CDN을 지우고 axios CDN을 넣는다.

`<script src="https://unpkg.com/axios/dist/axios.min.js"></script>`



그 후 다른 것들은 건드리지 말고 `this.$http`를 `axios`로 고치면 된다.

```javascript
 methods: {
            getArticles: function() {
              this.loading = true;
              axios.get('/api/article/')
                  .then((response) => {
                    this.articles = response.data;
                    this.loading = false;
                  })
                  .catch((err) => {
                    this.loading = false;
                    console.log(err);
                  })
            },
```

이런 식으로 고치면 axios를 사용해서 crud를 그대로 구현할 수 있다.

만약 data가 제대로 표시가 안된다면 `delimiters` 오류일 수 있다.

delimiters가 data안에 정의가 되어있으면 문제가 생긴다...

```javascript
el: '#starting',
          delimiters: ['${','}'],
          data: {
            articles: [],
            loading: true,
            currentArticle: {},
            message: null,
            newArticle: { 'article_heading': null, 'article_body': null },
          },
```

위와 같이 delimiters는 항상 data 바깥에 독립적으로 선언해주자 ㅠ 안그러면 데이터를 템플릿에 전혀 출력을 못한다.