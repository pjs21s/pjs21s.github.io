---
layout: post
title: "Vue.js Udemy 강의 두번째 과제"
tags: Vue.js
comments: true
---



```html
<script src="https://npmcdn.com/vue/dist/vue.js"></script>

<div id="exercise">
    <div>
        <button @click="show_alert">Show Alert</button>
    </div>
    <div>
        <input @keydown="value = $event.target.value" type="text">
        <p>{{ value }}</p>
    </div>
    <div>
        <input @keydown.enter="value = $event.target.value" type="text">
        <p>{{ value }}</p>
    </div>
</div>
```



```javascript
new Vue ({
	el: "#exercise",
  data: {
  	value:''
  },
  methods: {
  	show_alert: function() {
    	alert("!!")
    },
  }
})
```



`Vue`에 `methods`를 만들어도 되지만 간단한 경우에는 `"value = $event.target.value"`로 바로 값을 이벤트가 일어나는 걸 가져와서 변경할 수도 있다.



`v-model`은 양방향 바인딩을 지원한다.

원래는 `Vue`에 데이터를 정의해놓고 `html`로 뷰에 표시한다. (사용자가 보는 화면에)

그런데 `v-model`을 사용하면 클라이언트 화면에서 데이터를 바로 수정할 수 있다.

