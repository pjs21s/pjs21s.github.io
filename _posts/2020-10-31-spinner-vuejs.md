---
layout: post
title: "Vuex를 사용하여 Spinner 로딩 이펙트 적용하기"
tags: Vue
comments: true
---

로딩 이펙트를 적용하는 예제가 찾아봐도 쉽게 잘 안나와 있는 듯하여 저도 본 예제를 활용한 코드를 적어둡니다.
Vuex에 대한 기본 이해가 있어야 합니다.
우선 Spinner 컴포넌트를 만듭니다.
v-if를 통해 true면 화면에서 보이고 false면 보이지 않게 합니다.
즉 로딩 중에 true로 바꿔주고 로딩이 끝나는 시점에 false로 바꿔주면 됩니다.

```vue
<!-- Spinner.vue -->
<template>
    <div class="lds-facebook" v-if="loading">
        <div>
        </div>
        <div>
        </div>
        <div>
        </div>
    </div>
</template>
<script>
export default {
    props: {
        loading: {
            type: Boolean,
            required: true
        }
    }
}
</script>
<style>
.lds-facebook {
    display: inline-block;
    position: absolute;
    width: 64px;
    height: 64px;
    top: 47%;
    left: 47%
}
.lds-facebook div {
    display: inline-block;
    position: absolute;
    left: 6px;
    width: 13px;
    background: #42b883;
    animation: lds-facebook 1.2s cubic-bezier(0, 0.5, 0.5, 1) infinite;
}
.lds-facebook div:nth-child(1) {
    left: 6px;
    animation-delay: -0.24s;
}
.lds-facebook div:nth-child(2) {
    left: 26px;
    animation-delay: -0.12s;
}
.lds-facebook div:nth-child(3) {
    left: 45px;
    animation-delay: 0;
}
@keyframes lds-facebook {
    0% {
        top: 6px;
        height: 51px;
    }
    50%, 100% {
        top: 19px;
        height: 25px;
    }
}
</style>
```

제 프로젝트는 시작하면 App.vue를 통해 렌더링되기 때문에
App.vue에서 Spinner 컴포너트를 사용했습니다.
실제로 loading props에 true를 넣어보시면 어느 화면에서나 Spinner를 볼 수 있습니다.
Spinner의 상태 값은 Vuex state에서 가져옵니다.

```vue
<!--App.vue-->
<template>
    <div id="app">
        <router-view></router-view>
		<spinner :loading="$store.state.LoadingStatus"/>
    </div>
</template>
<script>
import Spinner from '@/components/Spinner'
export default {
	components: {
		Spinner
	}
}
</script>
```

이제 Vuex state에 LoadingStatus를 정의합니다.
mutations, actions 등을 같은 파일에 정의할 필요 없이 분리하고 아래처럼 가져다 사용하시면 됩니다.

```javascript
// store/index.js
import Vue from 'vue'
import Vuex from 'vuex'
import actions from './actions'
import getters from './getters'
import mutations from './mutations'

Vue.use(Vuex)

const state = {
    LoadingStatus: false
}

export default new Vuex.Store({
    state,
    mutations,
    actions,
    getters
})
```

이제 LoadingStatus를 변화시켜 줄 mutations를 정의합니다.

```javascript
// store/mutations.js
export default {
    startSpinner(state){
        state.LoadingStatus = true;
    },
    endSpinner(state){
        state.LoadingStatus = false;
    }
}
```

그리고 전역 router 파일을 생성하고 정의합니다.

```javascript
// router/index.js
import Vue from 'vue'
import VueRouter from 'vue-router'
import AllFigure from '@/views/AllFigure';
import store from '../store/index'

Vue.use(VueRouter)

const router = new VueRouter({
    mode: 'history',
    routes: [
        {
            path: '/',
            component: Dashboard,
            children: [
                {
                    path: '/all-figure',
                    component: AllFigure,
                    meta: { description: '전체 현황' }
                },
            ],
        },
    ]
});

router.beforeEach((to, from, next) => {
    store.commit('startSpinner');
    setTimeout(() => {
        next();
    }, 1);
})

router.afterEach((to, from) => {
    store.commit('endSpinner');
})

export default router
```

setTimeout을 주지 않으면 Spinner 효과가 안나타나납니다.
router.boforeEach에는 라우터 이동 전에 수행 할 코드가 들어갑니다.
router.afterEach는 이동이 끝난 후에 수행 할 코드가 들어갑니다.

위와 같이 정의하면 라우터를 이동하기 전에 startSpinner를 커밋하면서 loadingStatus가 true로 변경되고 화면에 Spinner가 나타납니다.
이동이 끝난 후에는 endSpinner를 커밋하면서 loadingStatus가 false로 변경되고 화면에서 Spinner가 사라집니다.

아래와 같이 axios 전역 파일을 생성하여 axios 요청시에도 비슷한 원리로 사용할 수 있습니다.

```javascript
// util/axios.js
import axios from 'axios'
import store from '../store/index'

const axiosInstance = axios.create({
})

axiosInstance.interceptors.request.use(
    config => {
        store.commit('startSpinner');
        return config;
    },
    error => {
        alert('데이터 요청 실패');
        return Promise.reject(error);
    }
);

axiosInstance.interceptors.response.use(
    response => {
        store.commit('endSpinner');
        return response;
    },
    error => {
        alert('데이터 응답 실패');
        return Promise.reject(error);
    }
)

export default axiosInstance
```