---
layout: post
title: "Vue.js Udemy 강의 첫 과제"
tags: Vue
comments: true
---

듣고 있는 강의: <https://www.udemy.com/vuejs-2-the-complete-guide/>

```html
<script src="https://unpkg.com/vue/dist/vue.js"></script>

<div id="exercise">
    <p>{{name}}({{age}})</p>
    <p>{{age*3}}</p>
    <p>{{random_float()}}</p>
    <div>
        <img v-bind:src=img_src style="width:100px;height:100px">
    </div>
    <div>
        <input type="text" :value="name">
    </div>
</div>
```



```javascript
new Vue ({
	el: "#exercise",
  data: {
  	name: "Jin",
    age: "25",
    img_src: "https://upload.wikimedia.org/wikipedia/commons/thumb/7/77/Google_Images_2015_logo.svg/1200px-Google_Images_2015_logo.svg.png"
  },
  methods: {
  	random_float: function(){
    	return Math.random()
    }
  }
})
```



`el`은 `html`에서 `id`와 같아야 한다.

`Vue`의 `data`는 `String` 값을 가진다.

`html`에서 사용할 때는 `{{}}`를 사용한다. 흔히 말하는 콧수염 템플릿

함수를 쓰고 싶을 때는 `methods`를 만들어서 사용한다.

간단한 기능의 경우는 return만 해서 가져다 쓸 수 있다.

`v-bind`는 뭔가 가져올때 사용하는것 같다. `v-bind`는 `:`로 대체가능하다.

이벤트가 일어날때?



`v-text`를 사용하면 XSS 차단에도 좋다.

`v-html`은 XSS의 가능성이 있어서 조심히 사용해야 한다.

`v-once`는 초기에 한번만 렌더링한다. 정적인 부분을 나타낼때 사용하면 성능 최적화에 도움이 된다.



`v-on:이벤트이름 = "메소드이름"` `v-on`은 `@`로 대체가능하다.

`@:이벤트이름 = "메소드이름"`