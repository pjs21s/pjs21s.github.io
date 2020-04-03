---
title: "django category 모델 만들기와 recursive 관계"
layout: post
comments: true
tags: django recursive
---

* 참고
  * <https://stackoverflow.com/questions/44798651/why-to-use-self-foreignkey-in-django-models>



django는 java에 비하면 한국 자료가 정말 없기에 자신과 타인을 위해서 정보를 기록해둔다.

글을 분류해서 보여주기 위해 카테고리 모델을 만들어본다.



```python
#models.py
class Category(models.Model):
    category_name = models.CharField(max_length=30)
    parent = models.ForeignKey('self', on_delete=models.CASCADE, blank=True, null=True)

    class Meta:
        unique_together = ["category_name"]
    
    def __str__(self):
        return self.category_name
    
class Post(models.Model, HitCountMixin):
    ...
    category = models.ForeignKey(Category, on_delete=models.CASCADE, null=True)
```



`Category`모델을 만들다 보면 누구나 의문이 생긴다.

`Post`에서 사용하기 위해 `Category`를 먼저 위에 정의해주면 `Category`에서는 `Post`에 연결을 못한다.

`Post`모델이 `Category`모델 보다 아래에 있어서 인식을 못한다;



그래서 `Category`는 `Post`와 직접적으로 연결을 하지 않고 recursive relationship

즉 재귀적 관계를 이용해서 생성만 해주고 그걸 `Post`에서 가져다 사용한다.

parent의 역할은 아래 이미지에서 더 쉽게 이해할 수 있습니다.



```python
#admin.py
admin.site.register(Category)
```

fomrs.py에도 category 필드를 각자에 맞게 추가해줍니다.

이렇게 다 하고 나면 admin에서 아래처럼 카테고리를 추가해줄 수 있습니다.



<img src="/images/category1.png">

parent의 역할은 카테고리를 하나 만든 후 상위 카테고리로 사용할 수 있게 해준다는 점입니다.

아직 직접 사용은 안 해봐서 정확히는 알 수 없지만 자신이 자신을 참조하는 재귀적 관계니 가능한 것 같습니다.

<img src="/images/category2.png">



카테고리를 만들었으면 Post에서 카테고리 설정을 해보세요.
그리고 글을 카테고리에 맞게 분류해주어야 합니다.

template은 기존에 있는 list template을 사용합니다.

url만 다르게 해줍니다.

`path('translation',views.TransPostList.as_view(),name="translation"),`

```python
class TransPostList(generic.ListView):
    model = Post
    template_name = "posts/post_list.html"
    queryset = Post.objects.filter(category_id=1) #번역
```

queryset에서 filter를 사용할 때도 무식하게 "번역"이라고 값을 넣었다가 자꾸 안돼서 그냥 id로 설정하니 바로 됐습니다...

그리고 template에도 버튼을 만들어서 해당 게시판으로 들어올 수 있게 해줍니다.

{% raw %}

```html
<a href="{% url 'posts:translation' %}"><button class="btn btn-link" type="button">
<span class="font-weight-bolder align-middle white-text">번역 게시판</span></button></a>
```

{% endraw %}

드롭다운 형식으로 한 페이지에서 카테고리 분류를 하면 더 편할 텐데 거기까지는 아직 못하겠네요 ㅠ
