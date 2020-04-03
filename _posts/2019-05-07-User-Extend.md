---
title: "OneToOne을 이용해 User 모델 확장 Profile 생성"
layout: post
tag: django
comments: true
---


# Profile 모델을 우선 만든다.

여기서 `auth.models.User`부분은 개인에 따라 바뀔 수 있다. 보통은 위에 선언을 다 해놓고 사용하기 때문에 `User`로 사용하는게 대부분이다. 핵심은 `User`라는 클래스명을 가져오면 안되고 django에서 제공하는 `User`를 가져와야 한다. 그러므로 `auth.models.User`라는 값을 `Profile user`에 넣어준다.

```python
class User(auth.models.User, auth.models.PermissionsMixin):
    ...
```

`Profile`에는 자기소개 필드를 추가로 넣었다.

```python
#models.py
class Profile(models.Model):
    user = models.OneToOneField(auth.models.User, on_delete=models.CASCADE, related_name='profile')
    bio = models.TextField(max_length=300, blank=True)
```

이어서 아래의 코드를 적어준다.

여기서 `@receiver`는 `signal`을 `parameter`로 받는다.

`post_save`와 `sender`인데 `post_save`는 모델이 만들어지고 난 후에 저장해주는 기능이다.

`sender`는 사용할 모델인 `auth.models.User`를 넣어준다.

`instance`는 실제 지금 저장된 값을 말한다. 여기서는 생성된 `User` 모델을 `Profile`의 `user`필드에 넣어준다고 이해하면 될것같다. `created`는 새로운 record가 만들어졌으면 True를 반환한다.

그러므로 `User`가 만들어졌을때 `Profile`을 생성하는 기능이 `create_user_profile`이다.

`save_user_profile`는 그걸 저장해준다.

<https://docs.djangoproject.com/en/2.2/ref/signals/>

```python
#models.py
from django.dispatch import receiver
from django.db.models.signals import post_save
@receiver(post_save, sender=auth.models.User)
def create_user_profile(sender, instance, created, **kwargs):
    if created:
        Profile.objects.create(user=instance)

@receiver(post_save, sender=auth.models.User)
def save_user_profile(sender, instance, **kwargs):
    instance.profile.save()
```

# View 작성

회원가입할 때 프로필을 작성하는 것이 아니라 회원가입 후 본인의 프로필에서 추가로 작성하도록 합니다.


`@transaction.atomic`은 공식문서에 따르면 block의 코드가 성공적으로 수행되면 변화를 DB에 커밋하고 예외가 있으면 롤백한다고 합니다. 확실하게 저장하고 싶을때 사용하는걸까요(?)

`user_form`과 `profile_form`을 선언하고 사용할 Form을 넣어줍니다.

`is_valid()`로 검사하고 성공하면 `save()`를 해줍니다.

return 할 때 'user_form', 'profile_form'에 위에서 Form을 넣어줬던 `user_form`과 `profile_form`을 연결해줍니다. 이걸 템플릿에서 출력할 때 사용하면 됩니다.

```python
from django.contrib.auth.decorators import login_required
from django.db import transaction
#views.py
@login_required
@transaction.atomic
def update_profile(request):
    if request.method == 'POST':
        user_form = UserCreateForm(request.POST, instance=request.user)
        profile_form = ProfileForm(request.POST, instance=request.user.profile)
        if user_form.is_valid() and profile_form.is_valid():
            user_form.save()
            profile_form.save()
            messages.success(request, 'Your profile was successfully updated!')
            return redirect('accounts:login')
        else:
            messages.error(request, 'Please correct the error below.')
    else:
        user_form = UserCreateForm(instance=request.user)
        profile_form = ProfileForm(instance=request.user.profile)
    return render(request, 'accounts/update_profile.html', {
        'user_form': user_form,
        'profile_form': profile_form
    })
```

`urls.py`에 정의해줍니다.

```python
path("profile/update_profile", views.update_profile, name="update_profile"),
```

`admin`에서도 관리할거라면 정의해줍니다.

```python
from .models import Profile

admin.site.register(Profile)
```

저는 아래와 같이 `profile.html`을 만든 후 프로필 수정 버튼을 만들어서 `update_profile.html`로 넘어가게 했습니다.

참고하셔서 본인에 맞게 수정하시면 됩니다.

{% raw %}
```html
<!--profile.html--> 
<a class="dropdown-item" href="{% url 'accounts:update_profile' %}">프로필 수정</a>

<!--update_profile.html--> 
<form method="post">
    {% csrf_token %}
    {% bootstrap_form user_form %} 
    {% bootstrap_form profile_form %} 
    <button type="submit" class="btn btn-primary">저장</button>
</form>
```
{% endraw %}



**참고**

<https://simpleisbetterthancomplex.com/tutorial/2016/07/22/how-to-extend-django-user-model.html>
