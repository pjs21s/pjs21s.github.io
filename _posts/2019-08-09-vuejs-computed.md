---
layout: post
title: "Vue.js Computed 정리"
tags: Vue.js
comments: true
---



```html
<script src="https://unpkg.com/vue/dist/vue.js"></script>

<div id="app">
  <button @click="counter++">Increase</button>
  <button @click="counter--">Decrease</button>
  <button @click="secondCounter++">Increase Second</button>
  <p>Counter: {{ counter }} | {{ secondCounter }}</p>
  <p>Result: {{ result() }} | {{ output }}</p>
</div>
```



```javascript
new Vue ({
	el: "#app",
  data: {
  	counter: 0,
    secondCounter: 0
  },
  computed: {
  	output: function() {
    	console.log('Computed')
    	return this.counter > 5 ? 'Greater 5' : 'Smaller 5'
    }
  },
  methods: {
  	result: function() {
    	console.log('methods')
    	return this.counter > 5 ? 'Greater 5' : 'Smaller 5'
    }
  }
})
```

`computed`와 `methods`는 겉으로 보기에는 하는 역할이 같다.

위에 코드는 `counter` 버튼을 클릭해 5를 초과하면 `Greater 5`가 출력되고 그렇지 않으면 `Smaller 5`가 출력되는 기능이다.

그리고 그냥 단독으로 쓰이는 `secondCounter`를 추가했다.

`counter` 버튼을 누를때마다 아래 이미지에 있는 것처럼 `Computed`와 `methods`가 콘솔에 찍힌다.

그런데 `secondCounter` 버튼을 누를 때는 `methods`만 출력되는 것을 알 수 있다.

그래서 `secondCounter`를 세번 눌렀기 때문에 `methods`도 세번 출력되어 있다.

<img src="/images/computed.png">

근데 `secondCounter`는 `methods`에서도 사용하고 있지 않는 무관한 기능이다.

그런데 왜 `methods`에 있는 콘솔 출력 기능이 작동하고 있는걸까

`computed`는 출력을 하지 않고 있는데...



그 이유는 바로 `computed`는 종속성이라는 특징으로 인해 참고하고 있는 `counter`가 변경될때만 작동을 하는 것이고 `methods`는 상관없이 항상 작동하는 것이다.

즉 `computed`는 다시 계산할 필요가 없는 항목을 출력할 때 사용하면 좋은 것이다.

`methods`로 계산하지 않아도 되는 부분까지 다시 재실행하는 것은 비효율적이기 때문이다.



살짝 복잡한 개념이지만 계속 사용하다보면 익숙해질것같다.

