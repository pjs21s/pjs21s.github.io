---
layout: post
title: "vue.js router 전개연산자로 파일 분리하기"
tags: Vue
comments: true
---

vue.js 프로젝트를 만들다보면 규모가 커지게 된다. 그러다보면 router 파일이 지저분해지다 보니 분리를 하고 싶은 욕구가 생길 수 있다. 간단하게 분리하는 방법이다.
분리 한 `borad.js` 라우트 파일을 import하여 전개연산자로 펼쳐주면 알아서 `board.js`에서 정의한 내용이 `index.js`에 들어가게 된다.

```javascript
// index.js
import Vue from 'vue'
import VueRouter from 'vue-router'
import boardRoutes from './board'
Vue.use(VueRouter)
const router = new VueRouter({
mode: 'history',
routes: [
...boardRoutes
]
})
export default router
```

```javascript
// board.js
import Board from 'BOARD/List.vue'
import BoardDetail from 'BOARD/Input.vue'
import BoardUpdate from 'BOARD/Update.vue'
export default [
{ path : '/board', component: Board },
{ path : '/board/new', component: BoardDetail },
{ path : '/board/update', component: BoardUpdate }
]
```