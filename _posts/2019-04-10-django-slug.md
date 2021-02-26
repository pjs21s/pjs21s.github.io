---
layout: post
title: "190407 django Slug 메모"
tags: Django
comments: true
---



유효한 URL을 만들 때 사용하는 방법이다. 예를 들어 기사의 제목을 URL로 만들려 할 때 slug를 사용하는 걸 추천한다.

수동으로 값을 입력해놓는 것보다 추천되는 방식이다.


※ 예시
```python
<title> The 46 Year Old Virgin </title>
<content> A silly comedy movie </content>
<slug> the-46-year-old-virgin </slug>
```
위와 같이 만들려면 Django 모델을 아래와 같이 작성해야 할 것이다.

```python
class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField(max_length=1000)
    slug = models.SlugField(max_length=40)
```

어떻게 해야 의미 있는 이름을 가진 URL을 생성할 수 있을까? 보통은 Article.id를 사용할 건데 그러면 아래와 같이 만들어진다.

`www.example.com/article/23`

혹은, 이렇게 될 것이다.

`www.example.com/article/The 46 Year Old Virgin`

문제는 URLs에서 Space는 유효하지 않기 때문에 전부 %20으로 Space를 채워버린다. 그러면 아래와 같은 URL이 만들어진다.

`www.example.com/article/The%2046%20Year%20Old%20Virgin`

위와 같은 URL은 그다지 의미 있는 URL이라고 할 수 없다. 아래와 같이 만들어지는 것이 보다 의미 있다.

`www.example.com/article/the-46-year-old-virgin`

the-46-year-old-virgin 이것이 slug다. 모든 문자는 소문자로 변하고 Space는 -(hyphens)로 채워진다.

※ Stack Overflow의 주소도 뒤를 보면 이와 같은 형식으로 되어있다.

---

```python
class Article(models.Model):

    def save(self, *args, **kwargs):
        self.slug = slugify(self.title) # set the slug explicitly
        super(Article, self).save(*args, **kwargs) # call Django's save()
```

id 대신에 slug를 사용하면 같은 slug와 title을 가진 2개의 Article을 동시에 존재할 수 없다는 것을 의미한다.
※ 같은 이름의 URL이 존재할 수 없으므로 같은 title도 존재할 수 없다는 의미인 거 같다.
그래서 unique=True를 모델에 넣어 제약을 걸 수 있다.

`slug = models.SlugField(allow_unicode=True, unique=True)`

※ 다른 예시
```python
from django.utils.text import slugify
class Article(models.Model):
    headline = models.CharField(max_length=100)
    . . .
    slug = models.SlugField(unique=True)

    def save(self, *args, **kwargs):
        self.slug = slugify(self.headline)
        super(Article, self).save(*args, **kwargs)
    . . .

>>> u1 = User.objects.get(id=1)
>>> from datetime import date
>>> a1 = Article.objects.create(headline="todays market report", pub_date=date(2018, 3, 6), reporter=u1)
>>> a1.save()
// slug here is auto-generated, we haven't created it in the above create method.
>>> a1.slug
'todays-market-report'
```

※ save()에 대해 조금 더 공부를 해야 확실하게 이해할 수 있을 것 같다.

※ 참고
<https://stackoverflow.com/questions/34465180/how-do-i-make-the-slugs-work-in-django>
<https://stackoverflow.com/questions/427102/what-is-a-slug-in-django>
<http://books.agiliq.com/projects/django-orm-cookbook/en/latest/slugfield.html>
