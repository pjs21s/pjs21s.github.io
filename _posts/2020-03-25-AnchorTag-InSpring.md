---
layout: post
title: "Spring 스프링 컨트롤러에서 #링크 Anchor Tag로 리다이렉트 시키기, window.location.hash"
tags: Spring
comments: true
---

`#`를 사용하면 같은 페이지 내에서도 링크를 구분하여 사용할 수 있다.
예를 들면 고객관리 탭과 글쓰기 탭이 한 페이지에 있다고 하면
`test.do#usersetting`, `test.do#write` 이렇게 나눠서 탭 기능을 만들 수 있다.

근데 탭 기능을 사용한다는 건 서로 같은 `.jsp`이지만 다른 페이지처럼 사용한다는 것이므로
컨트롤러에서 특정 작업 후 리다이렉트를 시킬 때도 서로 다르게 리다이렉트 되야할 것이다.

사용자가 고객관리 탭에서 작업 후 글쓰기 탭으로 리다이렉트가 되면 이상하지 않은가?
즉 한 페이지이지만 탭을 서로 다른 링크로 나눠서 개발을 해야 한다.

한 컨트롤러가 `return "redirect:/test.do#usersetting"` 이처럼 리다이렉트를 시킨다고 하자
하지만 아무 설정을 하지 않는다면 절대 저 탭으로 리다이렉트 되지 않는다.

그렇기 때문에 아래 코드와 같이 리다이렉트로 받은 주소에서 `#usersetting`만 받아 hash라는 변수에
넣고 그 변수와 같은 이름을 가진 `href`태그를 클릭하게 해주어야 한다.

```javascript
<script>
$(document).ready(function(){
var hash = $.trim( window.location.hash );

if (hash) $('.your-css-selector a[href$="'+hash+'"]').trigger('click');
});
</script>
```

이렇게 하면 같은 이름을 가진 태그를 찾아서 페이지가 로딩될 때 클릭해줄 것이다.

출처 : <https://stackoverflow.com/questions/49729210/how-to-set-response-sendredirect-to-a-particular-tab-on-a-jsp-page>