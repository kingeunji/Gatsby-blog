---
title: Vue 3 으로 만드는 TODO 애플리케이션
date: 2020-08-08 08:08:62
category: 'vue.js'
draft: false
---

Vue.js 3.0 버전의 공식 릴리즈가 얼마 안남았습니다!  
또 [오피셜 Vue.js 3.0 문서](https://v3.vuejs.org/)도 출시 되었습니다!!

그래서 Vue.js 3.0 버전에서는 뭐가 달라졌는지 TODO 앱을 만들어보면서 알아보겠습니다

### 1. vue cli 를 사용한 프로젝트 생성

먼저 `@vue/cli`의 버전을 v4.5.0 로 업데이트를 해줘야합니다.

```shell
npm i -g @vue/cli@4.5.0
```

그 후 기존과 동일하게 `vue create [APP NAME]` 으로 프로젝트를 생성합니다.

<img width="554" alt="image" src="https://user-images.githubusercontent.com/44806627/
89696698-0013cf80-d954-11ea-99af-b3e18b9deb75.png">

프로젝트가 만들어지면 제일 눈에 보이는 차이점은 기존 main.js 파일입니다.

```javascript
import { createApp } from 'vue'
import App from './App.vue'

createApp(App).mount('#app')
```

`createApp` 이라는 생소한 개념이 나옵니다. 흐음...?  
[#공식 문서](https://v3.vuejs.org/guide/instance.html#creating-an-instance)를 보면 이제 모든 Vue 애플리케이션은 `createApp` 함수로 새 애플리케이션 인스턴스를 만드는 것으로 시작합니다.

### 2. vue-router 설정

Vue 3 Preset 은 아직 `vue-router` 및 `vuex` 를 자동으로 설치해주지 않습니다.  
`vue-router@4.0.0-beta.5` 패키지를 설치해줍니다.

```shell
npm install vue-router@4.0.0-beta.5
```

설치 후 라우터 파일을 만들어줍시다!  
`src/router/index.js`

```javascript
import { createRouter, createWebHistory } from 'vue-router'

const routes = [{
  ...
}]

const router = createRouter({
  history: createWebHistory(process.env.BASE_URL),
  routes,
})

export default router
```

아직 만들어진 화면이 없으니 빈 routes 를 작성하고,
`createRouter` 함수로 라우터를 선언합니다.

### 3. TODO 화면 컴포넌트 작성

이제 메인 화면 컴포넌트를 만들 차례입니다. 우선은 비어있는 페이지로 작성합니다.  
`src/views/Home.vue`

```javascript
<template>
  <div>
    홈 컴포넌트
  </div>
</template>

<script>
export default {
  name: 'Home'
}
</script>

<style>

</style>
```

그 후 `Home.vue`를 라우터에 추가합니다  
`src/router/index.js`

```javascript
import Home from '@/views/Home.vue'

const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home,
  },
]
```

`main.js`에 `vue-router` 를 추가하고, `App.vue`에 `router-view` 를 추가합니다.

`main.js`

```javascript
import { createApp } from 'vue'
import router from './router'
import App from './App.vue'

createApp(App)
  .use(router)
  .mount('#app')
```

`App.vue`에도 다른 코드를 다 지우고 `router-view` 만 작성해주세요.

```javascript
<template>
  <div id="app">
    <router-view />
  </div>
</template>

<script>
export default {
  name: 'App',
  components: {

  }
}
</script>
```

그리고 서버를 작성해서 확인하면 아래처럼 라우터가 제대로 동작하는 것을 확인 할 수 있습니다
<img width="1552" alt="image" src="https://user-images.githubusercontent.com/44806627/89698584-fe023e80-d95c-11ea-92bf-a2e2cc020194.png">

이제 `Home.vue` 컴포넌트에 Input과 Button 컴포넌트를 추가로 작성합니다.

```html
<template>
  <div class="todo-wrapper">
    <form>
      <div class="todo-input">
        <input v-model="state.content" type="text" required />
        <button type="submit">추가!</button>
      </div>

      {{ state.content }}
    </form>
  </div>
</template>

<script>
  import { reactive } from 'vue'
  export default {
    name: 'Home',

    setup() {
      const state = reactive({
        content: '',
        todoList: [],
      })

      return {
        state,
      }
    },
  }
</script>

<style lang="scss" scoped>
  ...
</style>
```

`vue 3.0`에 가장 크게 바뀐 `Composition API`를 위 코드에서 확인할 수 있습니다.

기존은 `data`, `methods`, `computed` 등 여러군데 나눠서 작성하던 코드의 문제점을 이제는 `setup()` 함수 안에 작성 할수 있어 해결할 수 있습니다.

그 안에서 `reactive` 를 통해 생성된 객체는 모두 깊은(Deep) 감지를 수행하기 때문에
객체가 중첩된 상황에서도 **반응형 데이터**를 쉽게 조작하고 처리할 수 있습니다.  
이렇게 `reactive` 를 통해 선언한 데이터들을 `state.content` 이런 식으로 접근해 사용할 수 있습니다.

위에 코드를 실행하면 아래와 같은 화면을 볼 수 있습니다!

<img width="1552" alt="image" src="https://user-images.githubusercontent.com/44806627/89699713-a74c3300-d963-11ea-8aad-f07d5d89ed14.png">

이제 **추가 버튼을 눌렀을 때 이벤트(onSubmit)** 를 만들 차례입니다.  
추가를 하면 `todoList[]` 에 `push(state.content)` 를 시키고,  
**삭제 버튼을 클릭하면 삭제되는 이벤트(removeTodoItem)** 도 만들어줍니다.

```html
<form @submit.prevent="onSubmit">
  <div class="todo-input">
    <input
      v-model="state.content"
      type="text"
      @keypress.enter="$refs.$submitButton.click()"
      required
    />
    <button ref="$submitButton" type="submit">추가!</button>
  </div>
</form>

<div class="todo-list">
  <div v-for="(item, i) in state.todoList" :key="i" class="todo-item">
    <div class="todo">{{ item.content }}</div>
    <div class="remove">
      <button @click="removeTodoItem(i)">X</button>
    </div>
  </div>
</div>
```

```javascript
setup() {
  const state = reactive({
    content: '',
    todoList: []
  })

  const onSubmit = () => {
    state.todoList.push({ content: state.content})
    state.content = ''
  }

  const removeTodoItem = index => {
    state.todoList.splice(index, 1)
  }

  return {
    state,
    onSubmit,
    removeTodoItem
  }
}
```

그 후 간단하게 `scss`로 작업을 해주고 서버를 켜보면
아래와 같이 그럴싸한 TODO 앱이 만들어집니다~!!

![todo-app](https://user-images.githubusercontent.com/44806627/89704502-1e4af100-d98f-11ea-8df1-0fa5e1e91a1a.gif)

### 4. todoList 를 store 에서 관리하기

지금 `Home.vue`에서만 사용하고 있는 todoList 를 store 에서 관리를 하려고 합니다.

기존에 사용하던 `vuex` 는 Vue3 부터는 설치하지 않고 vue 패키지에서 제공하는 `reactive` 를 사용하여 해결할 수 있습니다. 그리고 기존의 `vuex` 패키지도 설치가 가능합니다. (`vuex@4.0`)

먼저 `store/todo.js` 파일을 만들고 아래 코드를 작성해줍니다.

```javascript
import { reactive, readonly } from 'vue'

const createStore = () => {
  const state = reactive({
    list: [],
  })

  const push = content => {
    state.list.push({
      content,
    })
  }

  const remove = index => {
    state.list.splice(index, 1)
  }

  return {
    state: readonly(state),
    push,
    remove,
  }
}

export default createStore
```

기존 컴포넌트에 있는 `todoList`를 `state`에 작성해주고, `push, remove` 함수도 작성해줍니다.

`readonly(state)` 키워드를 통해 외부에서 state를 변경하지 못하게 막아줍니다!

그리고 `main.js`에서 스토어를 전역으로 설정해줍니다.

```javascript
import { createApp } from 'vue'
import router from './router'
import App from './App.vue'
import createTodoStore from './store/todo'

createApp(App)
  .use(router)
  .provide('todo', createTodoStore())
  .mount('#app')
```

그리고 기존에 `Home.vue` 에서도 코드를 수정합니다.

```javascript
import { reactive, inject } from 'vue'
export default {
  name: 'Home',

  setup() {
    const todo = inject('todo')
    const state = reactive({
      content: '',
      todoList: todo.state.list,
    })

    const onSubmit = () => {
      todo.push(state.content)
      state.content = ''
    }

    const removeTodoItem = index => {
      todo.remove(index)
    }

    return {
      state,
      onSubmit,
      removeTodoItem,
    }
  },
}
```

`inject`를 추가해서 `todo`로 등록한 스토어를 가져옵니다.
그리고 다시 실행해보면 똑같이 동작하는 것을 확인할 수 있습니다.

## 마무리..

vue 3 공식 릴리즈를 앞두고 간단하게 TODO 어플리케이션을 만들어봤습니다.

`setup()` 함수안에서 `this` 키워드를 안써도 되는 점이 편리하고,  
`vuex` 없이도 전역 상태관리를 할 수 있는 점이 가장 큰 변화 인 것 같아요

만들어본 예제는 [깃허브]()에서 확인해 볼 수 있습니다.

다음 포스팅은 `Composition API` 란 무엇인지에 대해 작성해보겠습니다!
