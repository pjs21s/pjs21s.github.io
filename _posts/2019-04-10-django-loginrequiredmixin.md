---
layout: post
title: "190406 django LoginRequiredMixin, TabularInline"
tags: Django
comments: true
---


Class-based views를 사용할 때 `LoginRequiredMixin`을 쓰면 `login_required`와 같은 역할을 할 수 있다.
이 Mixin을 view에서 사용할 경우 인증되지 않은 사용자들의 모든 요청들은 login page로 넘어가거나 HTTP 403 Error를 보이게 된다.
`raise_exception`에 따라 달라질 수도 있다.

`raise_exception`의 default는 False로 익명 사용자 즉 인증되지 않은 사용자를 login page로 넘긴다.
만약에 값을 True로 변경하면 조건에 맞지 않는 경우 `PermissionDenied exception` 띄운다.
`PersmissionDenied`는 문자 그대로 특정 행동에 있어 허가받지 아니했을 경우 띄우는 exception이다.

※ 예시

```python
from django.contrib.auth.mixins import LoginRequiredMixin
from django.views import generic

class CreateGroup(LoginRequiredMixin, generic.CreateView):
    fields = ('name', 'description')
    model = Group
```
---

`TabularInline`은 테이블 형식으로 데이터를 표시해준다.
`admin.py`에서 아래와 같이 상속을 받아 정의를 해주면 `GroupMember`의 데이터는 `TabularInline`의 형식을 따를 것이다.

```python
class GroupMemberInline(admin.TabularInline):
    model = models.GroupMember

admin.site.register(models.Group)
```
```python
class GroupMember(models.Model):
    group = models.ForeignKey(Group, on_delete=models.CASCADE, related_name='memberships')
    user = models.ForeignKey(User,on_delete=models.CASCADE, related_name='user_groups')

    def __str__(self):
        return self.user.username

    class Meta:
        unique_together = ('group', 'user')
```

※ `TabularInline`은 admin 쪽을 더 공부를 해야 확실히 이해가 될 것 같다.

---
`admin.py`의 역할은 admin에서 관리하고 싶은 model을 `admin.site.register(모델명)`로 등록해서 사용하는 것
```python
from django.contrib import admin
from .models import Post

admin.site.register(Post)
```
※ 참조
<https://docs.djangoproject.com/en/2.2/topics/auth/default/>
<https://docs.djangoproject.com/en/2.2/ref/contrib/admin/>
