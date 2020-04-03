---
layout: post
title: "django social share 공유 기능 사용하기"
tags: django
comments: true
---

django로 사이트를 만드는 중에 post를 SNS나 외부로 공유할 수 있는 기능이 있으면 좋겠다는 생각이 들었다.  
그래서 찾아보니 github에서 간단하게 가져다가 사용할 수 있어서 적어놓는다.  
링크 : <https://github.com/fcurella/django-social-share>  

# 설치
`$ pip install django-social-share`

언제나 그렇듯이 사용하려면 INSTALLED_APPS에 추가해줘야 한다.
`INSTALLED_APPS += ['django_social_share']`

templates를 위한 디렉토리 설정 등을 하라고 하지만 이미 했으므로 넘어간다.
```python
TEMPLATES=[
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            os.path.join(BASE_DIR, 'templates'),
        ],
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.request',
            ],
        }
    },
]
```
# Usage

{% raw %}
```python
{% post_to_facebook <object_or_url> <link_text> %}

{% post_to_gplus <object_or_url> <link_text> %}

{% post_to_twitter <text_to_post> <object_or_url> <link_text> %}

{% post_to_linkedin <subject> <object_or_url> <link_text> %}

{% post_to_mail <email_subject> <text_to_post> <object_or_url> <link_text> %}

{% send_email <subject> <text_to_post> <object_or_url> <link_text> %}

{% post_to_reddit <text_to_post> <object_or_url> <link_text> %}

{% post_to_telegram <text_to_post> <object_or_url> <link_text> %}
```


# 예시
꼭 상단에 `{% load social_share %}`를 해주자 안그러면 endblock 안닫았다고 에러난다.
`<object_or_url>`에는 `get_absolute_url`을 이용해주면 된다.

# 필자의 사용 예시

버튼을 만들고 그 안에 페이스북 공유를 사용하였다.
`<button type="button" class="btn btn-light">{% post_to_facebook post.get_absolute_url "Facebook" %}</button>`

<img src="/images/social_ex.PNG">


```python
{% load social_share %}

{% post_to_facebook object_or_url "Post to Facebook!" %}
{% post_to_twitter "New Song: {{object.title}}. Check it out!" object_or_url "Post to Twitter" %}
{% post_to_gplus object_or_url "Post to Google+!" %}
{% post_to_linkedin object.title object_or_url "Post to LinkedIn" %}
{% send_email object.title "New Song: {{object.title}}. Check it out!" object_or_url "Share via email" %}
{% post_to_reddit "New Song: {{object.title}}" <object_or_url> %}
{% post_to_telegram "New Song: {{object.title}}" <object_or_url> %}
```
{% endraw %}
