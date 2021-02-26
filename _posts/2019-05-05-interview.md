---
layout: post
title: "개발 면접 기초, django 보안 "
tag: Interview
comments: true
---

개발 면접에 필요한 기초적인 지식을 한번 정리해봤다.



Serialization : 객체 또는 데이터를 외부에서도 사용가능하게 byte 형태로 변환하는 것

Overloading : 메소드의 이름은 하나이지만 매개변수의 타입이나 이름을 다르게 사용하는 것

Overriding : 부모 클래스로부터 상속받아 메소드를 재정의해서 사용하는 것, 조금만 메소드를 수정해서 사용하고 싶을 때 유용하다.



**Object Oriented Programming**

Polymorphism : 다형성, 하나의 클래스나 메소드가 다양한 방법으로 동작하는 것(ex. overloading, overriding)

Inheritance : 중복을 최소화하기 위해 공통적으로 필요한 기능을 부모로 만들어 상속받아 사용하는 것

Encapsulation : 중요한 정보를 함부로 조작하지 못하게 하기 위해 묶는 것



Stack : Last In First Out, 후입선출

Queue : First In First Out, 선입선출



Model-View-Controller Pattern : 쉽게 고칠 수 있는 어플리케이션을 개발 가능케 한다. 설계를 위한 시간이 많이 필요하다. View-Model이 서로 의존적이다.

Controller : 모델에 명령을 보내 모델의 상태를 변경한다. 사용자로부터 입력을 받는다.

Model : 상태 변화가 있을 때 Controller와 View에 통보한다. 실제 데이터를 조작하고 처리한다.

View : 사용자가 볼 결과물을 생성한다.

<img src="/images/mvc.png">

Model-View-Presenter Pattern

Presenter : View에서 요청한 정보를 Model로부터 가공해서 View로 전달해준다.

사용자 입력은 View에서 받는다.

<img src="/images/mvp.png">

Model-View-ViewModel Pattern

ViewModel : View를 표현하기 위해 만들어진 View를 위한 Model

<img src="/images/mvvm.png">



DeadLock - 교착상태 : 일련의 프로세스들이 서로가 가진 자원을 요청하고 기다리며 blocked 된 상태

해결책 : 프로세스가 자원을 받기위해 대기하는 법이 있으나 어떤 프로세스가 얼마나 자원을 할당받아 사용할지 알수없으므로 비효율적이다. 그래서 Deadlock Ignorance로 대처한다.

Deadlock Ignorance : 데드락이 일어나지 않는 것을 가정, 발생 시 사용자가 프로세스를 죽이게 한다.

데드락 자체는 드문 현상이므로 가장 나은 방법



**Security in django**

<https://docs.djangoproject.com/en/2.2/topics/security/#security-in-django>

## Cross site scripting (XSS) protection

XSS 공격은 다른 사용자의 브라우저를 통해 클라이언트 사이드 스크립트를 주입할 수 있게 한다.

(Client side scripts 언어 - JavaScripts, Server side scripts 언어 - Python, php, jsp etc)

이는 보통 다른 사용자에게 보여주고 조회하는 DB에 있는 강한 악성 스크립트로 인해 달성된다. 혹은 사용자가 본인의 브라우저에 의해 실행될 공격자의 자바스크립트가 포함된 링크를 클릭해 발생하게 된다. 그러나 XSS 공격은 특정 페이지에 데이터가 제대로 관리되고 있지 않다면 웹 서비스나 쿠키 같은 신뢰할 수 없는 아무 데이터 소스에서 발생할 수 있다.

Django 템플릿을 사용하면 주요 XSS 공격으로부터 보호된다. 그러나 어떤 보호를 제공하는지와 그에 대한 한계를 아는 것은 무척이나 중요하다. 

HTML에서 특정 escape characters를 사용하는 건 특히 위험하다. 대부분의 악의적인 입력을 보호해주지만 이건 foolproof에 어긋난다.

(**foolproof** : 사람이 기계 등 서비스를 부적절한 사용을 하더라도 보호기능이 작동해야 하는 것)

예를 들어 아래와 같은 예시는 보호받을 수 없다.

`<style class={{ var }}>...</style>`

만약 var 값이 `'class1 onmouseover=javascript:func()'` 설정 되어 있다면 브라우저에 따라 인증되지 않은 자바스크립트 실행이 결과로 나올 수 있다. 그렇기에 is_safe, safe, mark_safe 템플릿 태그를 사용하거나 autoescape가 꺼져있다면 주의해야 한다.

HTML을 DB에 저장할때 그리고 HTML을 불러오고 보여줄때 더욱 조심해야 한다. 



## Cross site request forgery (CSRF) protection

CSRF 공격을 통해 악의적인 사용자는 유저의 지식이나 동의 없이 다른 유저의 자격을 사용해 작업을 한다.

적절하게 제공되는 기능을 사용하면 Django는 대부분의 CSRF 공격에 대해 보호를 해준다. 그러나 보호를 약화시키는 기법 때문에 한계는 있다. 예를 들어 CSRF 모듈이 특정 뷰나 전체적으로 비활성화 될 수 있다. 자신이 하는 일이 무엇인지 정확히 알고 있을 때만 이렇게 해야 한다. 만약 사이트가 통제를 벗어난 서브도메인을 가지고 있다면 그것도 취약점이 될 수 있다.

CSRF 보호는 각 POST 요청을 비밀을 위해 확인함으로써 작동한다. 이는 악의적인 사용자가 단순히 웹 사이트를 향해 form POST를 replay 못하게 하며 다른 로그인한 유저가 우연히 form을 제출하게 한다. 여기서 악의적인 사용자는 쿠키를 이용해 사용자의 특정한 비밀을 알고 있을 것이다.

HTTPS와 함께 배포될 때 **CsrfViewMiddleware**는 HTTP referrer header가 서브도메인과 포트를 포함한 같은 곳에 URL이 설정되어 있는지 체크할 것이다. 왜냐하면 HTTPS는 추가적인 보안을 제공하기에 안전하지 않은 연결 요청이나 지원되는 브라우저에 HSTS를 사용해야 접속이 가능한 곳에서는 HTTPS를 사용하여 연결시켜야만 하기 때문이다.

(**HTTP referrer**는 웹 브라우저로 월드 와이드 웹을 서핑할 때, 하이퍼링크를 통해서 각각의 사이트로 방문시 남는 흔적을 말한다.)

**csrf_exempt**를 사용하여 주의하도록 합시다.



다음에 추가로 읽으면서 번역해봐야겠다...
