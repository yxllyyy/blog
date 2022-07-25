# Pinia

## 一、什么是Pinia呢？

Pinia（发音为/piːnjʌ/，如英语中的“peenya”）是最接近piña（西班牙语中的菠萝）的词

- Pinia开始于大概2019年，最初是作为一个实验为Vue重新设计状态管理，让它用起来像组合式API（Composition API）
- 从那时到现在，最初的设计原则依然是相同的，并且目前同时兼容Vue2、Vue3，也并不要求你使用Composition API
- Pinia本质上依然是一个状态管理的库，用于跨组件、页面进行状态共享（这点和Vuex、Redux一样）

![https://cdn.jsdelivr.net/gh/likesandy/blog@main/img/pinia.png](https://cdn.jsdelivr.net/gh/likesandy/blog@main/img/pinia.png)

## 二、Pinia和Vuex的区别

那么我们不是已经有Vuex了吗？为什么还要用Pinia呢？

- Pinia 最初是为了探索 Vuex 的下一次迭代会是什么样子，结合了 Vuex 5 核心团队讨论中的许多想法
- 最终，团队意识到Pinia已经实现了Vuex5中大部分内容，所以最终决定用Pinia来替代Vuex
- 与 Vuex 相比，Pinia 提供了一个更简单的 API，具有更少的仪式，提供了 Composition-API 风格的 API
- 最重要的是，在与 TypeScript 一起使用时具有可靠的类型推断支持

和Vuex相比，Pinia有很多的优势：

-  比如mutations 不再存在：
  - 他们经常被认为是 非常 冗长
  - 他们最初带来了 devtools 集成，但这不再是问题
- 更友好的TypeScript支持，Vuex之前对TS的支持很不友好
- 不再有modules的嵌套结构：
  - 你可以灵活使用每一个store，它们是通过扁平化的方式来相互使用的
- 也不再有命名空间的概念，不需要记住它们的复杂关系

![https://cdn.jsdelivr.net/gh/likesandy/blog@main/img/vuex和pinia对比.png](img/vuex和pinia对比.png)

## 二、pinia的基本使用

安装：

```sh
pnpm add pinia
```

创建一个`pinia`并且将其传递给应用程序：

```js
import { createPinia } from 'pinia'

const pinia = createPinia()

createApp(App)
  .use(pinia)
  .mount('#app')
```

## 三、认识Store

什么是Store？

- 一个 Store （如 Pinia）是一个实体，它会持有为绑定到你组件树的状态和业务逻辑，也就是保存了全局的状态
- 它有点像始终存在，并且每个人都可以读取和写入的组件
- 你可以在你的应用程序中定义任意数量的Store来管理你的状态

Store有三个核心概念：

- `state`、`getters`、`actions`

- 等同于vue2中的`data`、`computed`、`methods`
-  一旦 store 被实例化，你就可以直接在 store 上访问 state、getters 和 actions 中定义的任何属性

## 四、定义一个Store

定义一个Store：

- 我们需要知道 Store 是使用 `defineStore() `定义的
- 并且它需要一个唯一名称，作为第一个参数传递
- 这个 name，也称为 id，是必要的，Pinia 使用它来将 store 连接到 `devtools`
- 返回的函数推荐使用useX作为命名方案，这是约定的规范

```js
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  state: () => (
    { count: 0, }
  ),
})
```

##  五、使用定义的Store

在需要使用store的地方进行导入，并且调用封装的store函数，函数会返回一个对象:

```vue
<script setup>
import { useCounterStore } from "./stores/couter";
const counterStore = useCounterStore();
</script>

<template>
  <div>
    <h2>{{ counterStore.count }}</h2>
  </div>
</template>

```

请注意，store是一个用reactive包装的对象，这意味着不需要在getters之后写.value，但是，像setup中的props一样，我们不能对它进行解构

- 如果我们就是想要解构的话,你需要使用官方提供的`storeToRefs()`或者Composition API中的`toRefs()`

```vue
<script setup>
import { toRefs } from "vue";
import { storeToRefs } from "pinia";
import { useCounterStore } from "./stores/couter";

// const { count } = toRefs(useCounterStore());
const { count } = storeToRefs(useCounterStore());
</script>

<template>
  <div>
    <h2>{{ count }}</h2>
  </div>
</template>

```

## 六、认识和定义State

state 是 store 的核心部分，因为store是用来帮助我们管理状态的

- 在 Pinia 中，状态被定义为返回初始状态的函数

- state是一个需要返回对象的函数

```js
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  state: () => (
    { count: 0, }
  ),
})
```

## 七、操作State

读取和写入 state：

```vue
<script setup>
import { storeToRefs } from "pinia";
import { useCounterStore } from "./stores/couter";

const counterStore = useCounterStore();
const add = () => {
  counterStore.count++;
};
</script>

<template>
  <div>
    <h2>{{ counterStore.count }}</h2>
    <button @click="add">+1</button>
  </div>
</template>
```

调用 `$patch()`一次性修改多个状态:

```js
const add = () => {
  counterStore.$patch({
    count: counterStore.count + 1,
  });
};
```

你可以通过调用 store 上的 `$reset()` 将状态 重置 到其初始值:

```js
const reset = () => {
  counterStore.$reset();
};
```

您可以通过将其 `$state` 设置为新对象来替换 Store 的整个状态：

```js
const replace = () => {
  counterStore.$state = {
    count: 2,
    age: 18,
  };
};
```

## 八、认识和定义Getters

Getters相当于Store的计算属性：

- 它们可以用 defineStore() 中的 getters 属性定义
- getters中可以定义接受一个state作为参数的函数

```js
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  state: () => (
    { count: 0, }
  ),
  getters: {
    doubleCount: (state) => state.count * 2,
  }
})
```

## 九、访问Getters

访问当前store的Getters：

```vue
<h2>{{ counterStore.doubleCount }}</h2>
```

Getters中访问自己的其他Getters：

```js
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  state: () => (
    { count: 0, }
  ),
  getters: {
    doubleCount: (state) => state.count * 2,
    countAdd() { return this.doubleCount + 1 }
  }
})
```

 Getters也可以返回一个函数，这样就可以接受参数：

```js
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  state: () => (
    { count: 0, }
  ),
  getters: {
    getUserId: (state) => userId => state.count == userId
  }
})
```

```vue
<h2>{{ counterStore.getUserId(0) }}</h2>  <!--true-->
<h2>{{ counterStore.getUserId(1) }}</h2>  <!--false-->
```

访问其他store的Getters：

```js
import { useUserStore } from './user' 

getUesrId: (state) => {
   const userStore = useUserStore()
   return state.count + userStore.doubleId
}	
```

## 十、认识和定义Actions

Actions 相当于组件中的 methods

- 可以使用 defineStore() 中的 actions 属性定义，并且它们非常适合定义业务逻辑

```js
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  state: () => (
    { count: 0, }
  ),
  actions: {
    increment() {
      this.count++
    }
  }
})
```

你可以像函数一样调用action:

```js
const add = () => {
  counterStore.increment();
};
```

和getter一样,你可以在在action中使用另一个store里的action

```js
getUserId() {
  const userStore = useUserStore()
  userStore.setId()
}
```

