---
layout: post
title: "Vue.js Udemy 강의 세번째 과제"
tags: Vue
comments: true
---

`watch`는 값이 변경될 때 함수를 동작하게 만들고 싶은 경우에 사용한다.

```html
<script src="https://npmcdn.com/vue/dist/vue.js"></script>

<div id="exercise">
    <div>
        <p>Current Value: {{ value }}</p>
        <button @click="value += 5">Add 5</button>
        <button @click="value += 1">Add 1</button>
        <p>{{ result }}</p>
    </div>
    <div>
        <input type="text">
        <p>{{ value }}</p>
    </div>
</div>
```

```javascript
new Vue ({
	el: "#exercise",
  data: {
    value: 0
  },
  watch: {
  	value: function() {
    	var vm = this
    	setTimeout(() => {
      	vm.value = 0
      },5000)
    }
  },
  computed: {
  	result: function() {
   		return this.value == 37 ? 'done' : 'not there yet'
    }
  }
})
```

`Vue.js`에서는 생각보다 `this.value == 37 ? 'done' : 'not there yet'` 이 형식을 많이 쓴다.

삼항연산자라고 하는데 익숙해져야겠다.

```html
<div id="app">
  <p v-if="show">You can see me</p>
  <p>Do you also see me</p>
  <button @click="show = !show">Switch</button>
</div>
```

```javascript
new Vue ({
	el: '#app',
  data: {
    show: true
  },
})
```

`show = !show`는 버튼을 눌렀을때 show와 !show를 왔다 갔다 한다는 뜻이다.

여기서 중요한 점은 버튼을 눌러서 You can see me를 숨기고 콘솔 창에서 보면 단순히 숨기거나 투명하게 하는게 아니라는 점이다. 그냥 완벽하게 없앤다.