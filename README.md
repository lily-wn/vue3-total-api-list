# vue3-total-api-list
Vue3.0 Composition API和Hooks使用学习文档
2022-04-27 994举报

简介： Vue3.0 Composition API和Hooks使用学习文档
vite脚手架创建项目
1、全局安装vite脚手架

npm install -g create-vite-app
2、使用脚手架创建项目

create-vite-app projectName
3、进入项目文件夹

cd projectName
4、安装依赖

npm install
5、启动vue3.0项目

npm run dev
升级Vue-cli
npm update -g @vue/cli
# OR
yarn global upgrade --latest @vue/cli
如果已经全局安装了旧版本的vue-cli(1.x或者2.x), 你需要先通过

npm uninstall vue-cli -g
//或
yarn global remove vue-cli

//卸载它，然后再使用
npm install -g @vue/cli
//或
yarn global add @vue/cli

//安装新的包
创建项目
npm install -g @vue/cli
vue create 项目名
cd 项目名
vue add vue-next
npm run serv
开始
在vue-cli3.0下安装 composition-api

npm install @vue/composition-api --save
# OR
yarn add @vue/composition-api
在使用任何 @vue/composition-api 提供的能力前，必须先通过 Vue.use() 进行安装

import Vue from 'vue'
import VueCompositionApi from '@vue/composition-api'
Vue.use(VueCompositionApi)
mian.js
import { createApp } from 'vue';
import App from './App.vue';
import router from './router';
import store from './store';
import './App.scss'

const app = createApp(App)
app.use(router).use(store).mount('#app');
Vue-router
import router from './router';
import store from './store';

createApp(App).use(router).use(store).mount('#app');
import { createRouter, createWebHashHistory } from 'vue-router';
import Home from '../views/Home/index.vue';
const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home,
  },
  {
    path: '/home',
    name: 'Home',
    component: Home,
  },
  {
    path: '/login',
    name: 'Login',
    component: () => import('../views/Login/index.vue'),
  },
  {
    path: '/user',
    name: 'User',
    component: () => import('../views/User/index.vue'),
  },
  {
    path: '/information',
    name: 'Information',
    component: () => import('../views/Information/index.vue'),
  },
  {
    path: '/discover',
    name: 'Discover',
    component: () => import('../views/Discover/index.vue'),
  }
];

const router = createRouter({
  history: createWebHashHistory(),
  routes,
});
router.beforeEach((to, from, next) => {
   if(to.path === '/') {
     next({ path: '/home' })
   } else {
      next()
   }
})
export default router;
Vuex
import Vuex from 'vuex';

export default Vuex.createStore({
  state: {
    userInfo: {}
  },
  mutations: {
    setUserInfo(state, info) {
      state.userInfo = info
    }
  }, 
  actions: {
  },
});
页面 template
vue2.0里 template模板里只能有一个根标签, 而3.0可以使用多个根标签

createApp
返回一个提供应用上下文的应用实例。应用实例挂载的整个组件树共享同一个上下文。可以在 createApp 之后链式调用其它方法。该函数第一个参数接收一个根组件选项的对象，第二个参数可以将 props 传递给应用程序.

createApp(
  App,
  {
    username: 'alex cheng' // 提供 props，App 组件内部通过 props: ['username'] 接收
  }
).mount('#app')
Composition API
数据声明
声明单一基础数据类型时使用;定义的变量，函数都要 return 出来;
setup函数，是在beforecreate钩子之前完成的。所以无法使用data跟methods。 另外要注意的是setup是同步的，不是异步的。

//引入方式1
 import { setup， ref } from 'vue'
 //引入方式2
 import { reactive } from '@vue/composition-api'
 export default {
    setup(props, context) {
        let count = ref(0)
        console.log(count.value) //0 ，  内部获取时需要用.value来获取
        console.log(isRef(count)) //true   //isRef() 用来判断是不是ref对象
     return {
         count  // ref返回的是一个响应式的对象
     }
    }
 }
Reactive 声明单一对象时使用
import { setup, Reactive } from 'vue'
export default {
// 倒计时逻辑的Composition Function
  const useCountdown = (initialCount) => {
    const count = ref(initialCount)
    const state = ref(false)
    const start = (initCount) => {
      state.value = true;
      if (initCount > 0) {
        count.value = initCount
      } 
      if (!count.value) {
        count.value = initialCount
      }
      const interval = setInterval(() => {
        if (count.value === 0) {
          clearInterval(interval)
          state.value = false
        } else {
          count.value--
        }
      }, 1000)
    }
    return {
      count,
      start,
      state
    }
  }
   setup(props, context) {
        const obj = reacttive({
        data1: '123',
        data2: {}
    })
    const state = reactive({count: 0}) // 创建数据
    // 直接使用倒计时逻辑
     const {count, start, state} = useCountdown(10)
     const onClick = () => {
        start()
     }
    return {
        ...torefs(obj)// 把obj转为响应式的ref，不然template里获取不到,
        state，
        count, 
        onClick, 
        state
    }
   }
}
watchEffect() 监听props
import { Reactive } from 'vue'
export default {
  props: ['val'], //这里获取props和 2.0 一样,也可以用对象形式
  setup(props, context) {
     watchEffect(() => {  //首次和props改变才会执行这里面的代码
         console.log(props.val)
     })
  }
}
ref
ref()函数用来根据给定值创建一个响应式的数据对象，ref()函数的调用返回值是一个对象，这个对象上只包含一个value属性。
ref函数只能监听简单类型的变化，不能监听复杂类型的变化
导入相关ref相关函数

import { ref } from '@vue/composition-api'
创建响应式对象

setup() {
    const count = ref(0)
        return {
            count,
            name: ref('vue')
        }
}
在template中访问响应式数据

<template>
    <span>{{count}}--- {{name}}</span>
</template>
isRef
isRef的使用
，isRef()函数主要用来判断某个值是否为ref()创建出来的对象；应用场景：当需要展开某个可能为ref()创建出来的值得时候，例如：

import { isRef } from '@vue/composition-api'
const fooData = isRef(foo) ? foo.value : foo
toRefs
toRefs的使用，toRefs()函数可以将reactive()创建出来的响应式对象，转为普通对象，只不过这个对象上的属性节点都是以ref()类型的像是数据， 最常见应用场景

import { toRefs, reactive } from '@vue/composition-api'

setup() {
    // 定义响应式数据对象
    const state = reactive({
      count: 0
    })
    // 定义页面上可用的事件处理函数
    const increment = () => {
      state.count++
    }
    
    // 在 setup 中返回一个对象供页面使用
    // 这个对象中可以包含响应式的数据，也可以包含事件处理函数
    return {
      // 将 state 上的每个属性，都转化为 ref 形式的响应式数据
      ...toRefs(state),
      // 自增的事件处理函数
      increment
    }
}
在template中就直接可以使用count属性和相对应的increment方法了，如果没有使用roRefs直接返回state那么就得通过state.xx来访问数据

<template>
  <div>
    <span>当前的count值为：{{count}}</span>
    <button @click="increment">add</button>
  </div>
</template>
shallowReactive
听这个API的名称就知道，这是一个浅层的 reactive，难道意思就是原本的 reactive 是深层的呗，没错，这是一个用于性能优化的API
其实将 obj 作为参数传递给 reactive 生成响应式数据对象时，若 obj 的层级不止一层，那么会将每一层都用 Proxy 包装一次，我们来验证一下

import {reactive} from 'vue'
export default {
    setup() {
        const obj = {
            a: 1,
            first: {
                b: 2,
                second: {
                    c: 3
                }
            }
        }
        const state = reactive(obj)
        console.log(state)
        console.log(state.first)
        console.log(state.first.second)
    }
}
来看一下打印结果：

设想一下如果一个对象层级比较深，那么每一层都用 Proxy 包装后，对于性能是非常不友好的
接下来我们再来看看 shallowReactive

import {shallowReactive} from 'vue'
export default {
    setup() {
        const obj = {
            a: 1,
            first: {
                b: 2,
                second: {
                    c: 3
                }
            }
        }
        const state = shallowReactive(obj)
        console.log(state)
        console.log(state.first)
        console.log(state.first.second)
    }
}
来看一下打印结果：

结果非常的明了了，只有第一层被 Proxy 处理了，也就是说只有修改第一层的值时，才会响应式更新，代码如下：

<template>
    <p>{{ state.a }}</p>
    <p>{{ state.first.b }}</p>
    <p>{{ state.first.second.c }}</p>
    <button @click="change1">改变1</button>
    <button @click="change2">改变2</button>
</template>
<script>
import {shallowReactive} from 'vue'
export default {
    setup() {
        const obj = {
            a: 1,
            first: {
                b: 2,
                second: {
                    c: 3
                }
            }
        }
        const state = shallowReactive(obj)
        function change1() {
            state.a = 7
        }
        function change2() {
            state.first.b = 8
            state.first.second.c = 9
            console.log(state);
        }
        return {state}
    }
}
来看一下具体过程：

首先我们点击了第二个按钮，改变了第二层的 b 和第三层的 c，虽然值发生了改变，但是视图却没有进行更新；
当我们点击了第一个按钮，改变了第一层的 a 时，整个视图进行了更新；
由此可说明，shallowReactive 监听了第一层属性的值，一旦发生改变，则更新视图。

shallowRef
这是一个浅层的 ref，与 shallowReactive 一样是拿来做性能优化的
shallowReactive 是监听对象第一层的数据变化用于驱动视图更新，那么 shallowRef 则是监听 .value 的值的变化来更新视图的
我们来看一下具体代码

<template>
    <p>{{ state.a }}</p>
    <p>{{ state.first.b }}</p>
    <p>{{ state.first.second.c }}</p>
    <button @click="change1">改变1</button>
    <button @click="change2">改变2</button>
</template>

<script>
import {shallowRef} from 'vue'
export default {
    setup() {
        const obj = {
            a: 1,
            first: {
                b: 2,
                second: {
                    c: 3
                }
            }
        }
        const state = shallowRef(obj)
        console.log(state);
        function change1() {
            // 直接将state.value重新赋值
            state.value = {
                a: 7,
                first: {
                    b: 8,
                    second: {
                        c: 9
                    }
                }
            }
        }
        function change2() {
            state.value.first.b = 8
            state.value.first.second.c = 9
            console.log(state);
        }
        return {state, change1, change2}
    }
}
</script>
首先看一下被 shallowRef 包装过后是怎样的结构

我们先点击了第二个按钮，发现数据确实被改变了，但是视图并没随之更新；
于是点击了第一个按钮，即将整个 .value 重新赋值了，视图就立马更新了
这么一看，未免也太过麻烦了，改个数据还要重新赋值，不要担心，此时我们可以用到另一个API，叫做 triggerRef ，调用它就可以立马更新视图，其接收一个参数 state ，即需要更新的 ref 对象
我们来使用一下

triggerRef
<template>
    <p>{{ state.a }}</p>
    <p>{{ state.first.b }}</p>
    <p>{{ state.first.second.c }}</p>
    <button @click="change">改变</button>
</template>

<script>
import {shallowRef, triggerRef} from 'vue'
export default {
    setup() {
        const obj = {
            a: 1,
            first: {
                b: 2,
                second: {
                    c: 3
                }
            }
        }
        const state = shallowRef(obj)
        console.log(state);
        function change() {
            state.value.first.b = 8
            state.value.first.second.c = 9
            // 修改值后立即驱动视图更新
            triggerRef(state)
            console.log(state);
        }
        return {state, change}
    }
}
</script>
我们来看一下具体过程

可以看到，我们没有给 .value 重新赋值，只是在修改值后，调用了 triggerRef 就实现了视图的更新

toRaw
toRaw 方法是用于获取 ref 或 reactive 对象的原始数据的
先来看一段代码

<template>
    <p>{{ state.name }}</p>
    <p>{{ state.age }}</p>
    <button @click="change">改变</button>
</template>
<script>
import {reactive} from 'vue'
export default {
    setup() {
        const obj = {
            name: '前端印象',
            age: 22
        }
        const state = reactive(obj)    
        function change() {
            state.age = 90
            console.log(obj); // 打印原始数据obj
            console.log(state);  // 打印 reactive对象
        }
        return {state, change}
    }
}
</script>
来看看具体过程

我们改变了 reactive 对象中的数据，于是看到原始数据 obj 和被 reactive 包装过的对象的值都发生了变化，由此我们可以看出，这两者是一个引用关系
那么此时我们就想了，那如果直接改变原始数据 obj 的值，会怎么样呢？答案是： reactive 的值也会跟着改变，但是视图不会更新
由此可见，当我们想修改数据，但不想让视图更新时，可以选择直接修改原始数据上的值，因此需要先获取到原始数据，我们可以使用 Vue3 提供的 toRaw 方法
toRaw 接收一个参数，即 ref 对象或 reactive 对象

<script>
import {reactive, toRaw} from 'vue'
export default {
    setup() {
        const obj = {
            name: '前端印象',
            age: 22
        }
        const state = reactive(obj)    
        const raw = toRaw(state)

        console.log(obj === raw)   // true
    }
}
</script>
上述代码就证明了 toRaw 方法从 reactive 对象中获取到的是原始数据，因此我们就可以很方便的通过修改原始数据的值而不更新视图来做一些性能优化了

注意： 补充一句，当 toRaw 方法接收的参数是 ref 对象时，需要加上 .value 才能获取到原始数据对象

markRaw
markRaw 方法可以将原始数据标记为非响应式的，即使用 ref 或 reactive 将其包装，仍无法实现数据响应式，其接收一个参数，即原始数据，并返回被标记后的数据
我们来看一下代码

<template>
    <p>{{ state.name }}</p>
    <p>{{ state.age }}</p>
    <button @click="change">改变</button>
</template>

<script>
import {reactive, markRaw} from 'vue'
export default {
    setup() {
        const obj = {
            name: '前端印象',
            age: 22
        }
        // 通过markRaw标记原始数据obj, 使其数据更新不再被追踪
        const raw = markRaw(obj)   
        // 试图用reactive包装raw, 使其变成响应式数据
        const state = reactive(raw)    

        function change() {
            state.age = 90
            console.log(state);
        }

        return {state, change}
    }
}
</script>
我们来看一下在被 markRaw 方法处理过后的数据是否还能被 reactive 包装成响应式数据

从图中可以看到，即使我们修改了值也不会更新视图了，即没有实现数据响应式

provide && inject
与 Vue2中的 provide 和 inject 作用相同，只不过在Vue3中需要手动从 vue 中导入
这里简单说明一下这两个方法的作用：

provide ：向子组件以及子孙组件传递数据。接收两个参数，第一个参数是 key，即数据的名称；第二个参数为 value，即数据的值
inject ：接收父组件或祖先组件传递过来的数据。接收一个参数 key，即父组件或祖先组件传递的数据名称

假设这有三个组件，分别是 A.vue 、B.vue 、C.vue，其中 B.vue 是 A.vue 的子组件，C.vue 是 B.vue 的子组件

// A.vue
<script>
import {provide} from 'vue'
export default {
    setup() {
        const obj= {
            name: '前端印象',
            age: 22
        }
        // 向子组件以及子孙组件传递名为info的数据
        provide('info', obj)
    }
}
</script>
// B.vue
<script>
import {inject} from 'vue'
export default {
    setup() {    
        // 接收A.vue传递过来的数据
        inject('info')  // {name: '前端印象', age: 22}
    }
}
</script>
// C.vue
<script>
import {inject} from 'vue'
export default {
    setup() {    
        // 接收A.vue传递过来的数据
        inject('info')  // {name: '前端印象', age: 22}
    }
}
</script>
获取标签元素
最后再补充一个 ref 另外的作用，那就是可以获取到标签元素或组件
在Vue2中，我们获取元素都是通过给元素一个 ref 属性，然后通过 this.$refs.xx 来访问的，但这在Vue3中已经不再适用了
接下来看看Vue3中是如何获取元素的吧

<template>
  <div>
    <div ref="el">div元素</div>
  </div>
</template>
<script>
import { ref, onMounted } from 'vue'
export default {
  setup() {
      // 创建一个DOM引用，名称必须与元素的ref属性名相同
      const el = ref(null)

      // 在挂载后才能通过 el 获取到目标元素
      onMounted(() => {
        el.value.innerHTML = '内容被修改'
      })

      // 把创建的引用 return 出去
      return {el}
  }
}
</script>
获取元素的操作一共分为以下几个步骤：

先给目标元素的 ref 属性设置一个值，假设为 el
然后在 setup 函数中调用 ref 函数，值为 null，并赋值给变量 el，这里要注意，该变量名必须与我们给元素设置的 ref 属性名相同
把对元素的引用变量 el 返回（return）出去
补充：设置的元素引用变量只有在组件挂载后才能访问到，因此在挂载前对元素进行操作都是无效的

接收props数据
在props中定义当前组件允许外界传递过来的参数名称：

props: {
   name: String
}  
通过setup函数的第一个形参，接收 props 数据：

setup(props) {
    console.log(props.name)
}
context形参
setup函数的第二个形参是一个上下文对象，就是vue2.x中的this，在vue 3.x中，它们的访问方式如下

 setup(props, context) {
    context.slots
    context.emit
    context.refs
}
watchEffect
在追踪其依赖时立即运行一个函数，并在依赖发生变化时重新运行

const count = ref(0)

watchEffect(() => console.log(count.value))
// -> logs 0

setTimeout(() => {
  count.value++
  // -> logs 1
}, 100)
取消观察
接口会返回一个函数，该函数用以取消观察。

const stop = watchEffect(() => {
  /* ... */
})
// later
stop() // 取消之后对应的watchEffect不会再执行
清除副作用（side effect）
为什么需要清除副作用？有这样一种场景，在watch中执行异步操作时，在异步操作还没有执行完成，此时第二次watch被触发，这个时候需要清除掉上一次异步操作。

watch(onInvalidate => {
  const token = performAsyncOperation(id.value)
  onInvalidate(() => {
    // id has changed or watcher is stopped.
    // invalidate previously pending async operation
    token.cancel()
  })
})
watch提供了一个onInvalidate的副作用清除函数，该函数接收一个函数，在该函数中进行副作用清除。在以下情况中调用：

watch的callback即将被第二次执行时。
watch被停止时，即组件被卸载之后

接口还支持配置一些选项以改变默认行为，配置选项可通过watch的最后一个参数传入。

flush
当同一个tick中发生许多状态突变时，Vue会缓冲观察者回调并异步刷新它们，以避免不必要的重复调用。
默认的行为是：当调用观察者回调时，组件状态和DOM状态已经同步。
这种行为可以通过flush来进行配置，flush有三个值，分别是post(默认)、pre与sync。sync表示在状态更新时同步调用，pre则表示在组件更新之前调用。

watchEffect(
  () => {
    /* ... */
  },
  {
    flush: 'sync'
  }
)
onTrack与onTrigger
用于调试:
onTrack：在reactive属性或ref被追踪为依赖时调用。
onTrigger： 在watcher的回调因依赖改变而触发时调用。

watchEffect(
  () => {
    /* side effect */
  },
  {
    onTrigger(e) {
      debugger
    }
  }
)
onTrack与onTrigger仅适用于开发模式。

mixin
 import {ref} from 'vue'
 export const useMyMinxin = {
  const name = ref('qimukakax')
  const getToken = () => {
      const params = 'qimukakax'
      getToken()
  }
}
将对象混入当前的模版

<template>
    <div>components</div>
</template>

<script>
import useMyMinxin  from './minxin.js'
import { onMounted } from 'vue'
export defaut {
  setup() {
      const { name, getToken } = useMyMinxin()
      onMounted(() => console.log(name)) 
  }
}
</script>
readonly
readonly函数接收一个对象（普通对象或者reactive对象）或者ref，并返回一个只读的原始参数对象代理，在参数对象改变时，返回的代理对象也会相应改变。如果传入的是reactive或ref响应对象，那么返回的对象也是响应的。
简而言之就是给传入的对象新建一个只读的副本。

const original = reactive({ count: 0 })
const copy = readonly(original)
watchEffect(() => {
  // 原始对象的变化会出发副本的watch
  console.log(copy.count)
})
// 原始对象改变，副本的值也会改变
original.count++
// 副本不可更改
copy.count++ // warning!
watch监听器
监听单个数据

import { Reactive } from 'vue'
export default {
  setup(props, context) {
   let count1 = ref(0)
   let state = reactive({
      count2: 0
   )
    //监听reactive类型
   watch(
   () => state.count2， //这里是你要监听的数据
   (count， preCount) => { //count新值， preCount旧值
       console.log('') //这里是监听数据变化后执行的函数
   }, {lazy： false}//在第一次创建不监听)
   //监听ref类型
     watch(count1，(count， preCount) => { //count新值， preCount旧值
       console.log('') //这里是监听数据变化后执行的函数
     })
   return {
       count
       ...toRefs(state)
   }
  }
}
监听多个数据
import { setup, Reactive } from 'vue'
export default {
  setup(props, context) {
   let count1 = ref(0)
    let name1 = ref(0)
   let state = reactive({
      count2: 0，
      name2： 'yangy'
   )
    //监听多个reactive类型
   watch(
       [() => state.count2, () => state.name2]
       ([count， name], [preCount， preName]) => { //count新值， preCount旧值
           console.log('') //这里是监听数据变化后执行的函数
       }, 
       {
          lazy： false
   })//在第一次创建不监听
   
   //监听ref类型
  watch(
       [count2, name2]
       ([count， name], [preCount， preName]) => { //count新值， preCount旧值
           console.log('') //这里是监听数据变化后执行的函数
       }, {lazy： false}//在第一次创建不监听)
   
   return {
       count，
       ...toRefs(state)
   }
  }
}
具体使用

使用前导入 import { watch } from '@vue/composition-api'
const count = ref(0)
// 定义watch只要count的值变化，就会触发watch的回调
// watch 会在创建的时候自动调用一次

watch(() => console.log(count.value))
setTimeout(() => {
    count.value++
}, 1000)
监听指定数据

// 定义reactive数据源
const state = reactive({ count: 0 })
// 监视 state.count 这个数据节点的变化
watch(() => state.count, (now, prev) => { 
    console.log(now, prev)
})
/ 定义ref数据源
const count = ref(0)
// 监视count这个数据的变化
watch(count, (now, prev) => { 
    console.log(now, prev)
})
computed
计算属性 可创建只读，和可读可写两种
computed() 用来创建计算属性，computed() 函数的返回值是一个 ref 的实例。使用 computed 之前需要按需导入：

import { computed } from '@vue/composition-api'
创建只读的计算属性，在调用computed()函数的时候，传入一个function函数，可以得到一个只读的计算属性。

// 创建一个响应式数据
const count = ref(1) 
// 根据count的值创建一个响应式的计算属性， 它会根据ref自动计算并且返回一个新的ref
const computedCount = computed(() => count.value + 1 ) 
console.log(computedCount.value) // 打印 2
computedCount.value++ //报错
创建可读可写的计算属性
在调用computed()函数的时候传入一个包含get和set的对象，就可以得到一个可读可写的计算属性了。

// 创建一个响应式数据
const count = ref(1) 
// 根据count的值创建一个响应式的计算属性， 它会根据ref自动计算并且返回一个新的ref
const computedCount = computed({
    get: () => count.value + 1,
    set: (val) => count.value = val - 1
} ) 

computedCount.count = 6
console.log(count.value) // 打印 5
import { setup, Reactive } from 'vue'
export default {
  setup(props, context) {
   let count = ref(0)
  setup () {
      const count = ref(0)
      const addCount = computed(() => count.value + 1) //只读 ，count.value变化后执行加1
      
      const addCount2 = computed({
          get:() => count.value + 1,
          set: (value) => count.value = value 
      })    
      // addCount2.value = 10   //赋值方法
     return {
         count,
         addCount,
         addCount2
     }
  }
  }
}
import { setup } from 'vue'
export default {
   setup(props, context) {
       //这里的props大家应该知道是啥吧，父组件传的值
       // context是什么？
       //在setup()里我们不能用this
       //所以vue2.0里的 this.$emit, this.$psrent, this.$refs在这里都不能用了。
       //context就是对这些参数的集合
       //context.attrs
       //context.slots
       //context.parent 相当于2.0里 this.$psrent
       //context.root 相当于2.0里 this
       //context.emit 相当于2.0里 this.$emit
       //context.refs 相当于2.0里 this.$refs
       ...
   }
}
provide / inject
provide 和 inject 启用依赖注入。只有在使用当前活动实例的 setup() 期间才能调用这两者。

// 父组件
setup() {
  const globalData = reactive({
    name: 'alex.cheng'
  })
  
  provide('globalData', globalData)
}

// 子/孙 组件
setup() {
  const globalData = inject('globalData')
}
注意: 可以在 setup 中获取，也可以在生命周期(onMounted/onBeforeMount)中获取。但是不能在方法中获取，比如点击事件在方法中打印，会是 undefined,并且Vue会给出警告。

function showInject () {
  // 获取顶层组件的数据
  const globalObj = inject('globalObj')
  console.log(globalObj)
}
defineComponent
顾名思义，这是一个定义组件的方法，传递一个包含组件选项的对象

import { defineComponent, h } from 'vue'
const DefineComp = defineComponent({
  data () {
    return {
      count: 11
    }
  },
  render () {
    return h(
      'h1',
      {
        class: 'define-comp'
      },
      `${this.count}`
    )
  }
})
或者是一个setup函数（函数名将作为组件名来使用）

import { defineComponent, ref } from 'vue'
const HelloWorld = defineComponent(function HelloWorld() {
  const count = ref(0)
  return { count }
})
getCurrentInstance
getCurrentInstance 支持访问内部组件实例，用于高阶用法或库的开发。只能在 setup 或生命周期钩子中调用。
注意: 如需在 setup 或生命周期钩子外使用，请先在 setup 中调用 getCurrentInstance() 获取该实例然后再使用。

const MyComponent = {
  setup() {
    const internalInstance = getCurrentInstance() // works
    const id = useComponentId() // works
    const handleClick = () => {
      getCurrentInstance() // doesn't work
      useComponentId() // doesn't work

      internalInstance // works
    }
    onMounted(() => {
      getCurrentInstance() // works
    })

    return () =>
      h(
        'button',
        {
          onClick: handleClick
        },
        `uid: ${id}`
      )
  }
}

// 在组合式函数中调用也可以正常执行
function useComponentId() {
  return getCurrentInstance().uid
}
LifeCycle Hooks(生命周期)


在新版中的生命周期需要按需导入，并且只能写setup()函数中。
使用onBeforeMount, onMounted, updated相关生命周期，使用前导入相关api方法

<template>
  <div id="app"></div>
</template>

<script>
// 1. 从 vue 中引入 多个生命周期函数
import {onBeforeMount, onMounted, onBeforeUpdate, onUpdated, onBeforeUnmount, unMounted} from 'vue'
export default {
  name: 'App',
  setup() {
      onBeforeMount(() => {
          // 在挂载前执行某些代码
      })
      onMounted(() => {
          // 在挂载后执行某些代码
      })
      onBeforeUpdate(() => {
          // 在更新前前执行某些代码
      })
      onUpdated(() => {
          // 在更新后执行某些代码
      })
      onBeforeUnmount(() => {
          // 在组件销毁前执行某些代码
      })
      unMounted(() => {
          // 在组件销毁后执行某些代码
      })
      return {}
  }
  
}
</script>
相关每个生命周期方法都是传入一个function函数。
vue2.x与新版Composition API之间的映射关系

beforeCreate -> setup
created -> setup
beforeMount -> onBeforeMount
mounted -> onMounted
beforeUpdate -> onBeforeUpdate
updated -> onUpdated
beforeDestroy -> onBeforeUnmount
destroyed -> onUnmounted
errorCaptured -> onErrorCaptured
provide & inject（数据共享）
provide()和 inject()可以实现嵌套组件之间的数据传递。这个两个函数只能在setup()函数中使用。父级组件中使用provide()函数可以使数据向下传递，子组件中使用inject()接收上层传过来的数据。
实现代码:
根组件（父组件）parent.vue

<template>
  <div>
    <child-one></child-one>
    <child-two></child-two>
  </div>
</template>

<script>
  import { provide } from '@vue/composition-api'
  import ChildOne from '@/components/Child'
  import ChildTwo from '@/components/Child'
  export default {
    components: {
       ChildOne,
       ChildTwo
    },
    setup() {
    // provide('要共享的数据名称', 被共享的数据)
      provide('globalName', 'vue') 
    }
  }
</script>
子组件1 ChildOne.vue

<template>
  <div>
    <!--页面展示数据globalName -->
    {{globalName}} 
  </div>
</template>

<script>
  import { inject } from '@vue/composition-api'
  export default {
    name: 'ChildOne',
    setup() {
      const globalName = inject('globalName') 调用 inject 函数时，通过指定的数据名称，获取到父级共享的数据
      return {
        globalName
      }
    }
  }
</script>
子组件2 ChildTwo.vue

<template>
  <div>
    <!--页面展示数据globalName -->
    {{globalName}} 
  </div>
</template>

<script>
  import { inject } from '@vue/composition-api'
  export default {
    name: 'ChildTwo',
    setup() {
      const globalName = inject('globalName') 调用 inject 函数时，通过指定的数据名称，获取到父级共享的数据
      return {
        globalName
      }
    }
  }
</script>
provide函数被共享的数据可以使ref和reactive定义的响应式数据，用法类似
template refs（元素组件）
我们知道在vue2.x中获取页面元素的DOM可以通过ref写在页面元素上然后在js中通过$refs.x来拿取当前元素的DOM元素信息，操作DOM,在composition-api中我们通过提供的ref方法传入一个null并且定义与页面元素上ref相对应。
代码实现：

<template>
  <div>
    <h1 ref="h1Ref">Hello Word</h1>
  </div>
</template>
<script>
import { ref, onMounted } from '@vue/composition-api'
export default {
  setup() {
    // 创建一个 DOM 引用
    const h1Ref = ref(null)
    // 在 DOM 首次加载完毕之后，才能获取到元素的引用
    onMounted(() => {
      // 为 dom 元素设置字体颜色
      // h1Ref.value 是原生DOM对象
      console.log(h1Ref.value)
    })
    // 把创建的引用 return 出去
    return {
      h1Ref
    }
  }
}
</script>
Suspense 组件
在开始介绍 Vue 的 Suspense 组件之前，我们有必要先了解一下 React 的 Suspense 组件，因为他们的功能类似。
React.lazy 接受一个函数，这个函数需要动态调用 import()。它必须返回一个 Promise，该 Promise 需要 resolve 一个 default export 的 React 组件。

import React, { Suspense } from 'react';
const myComponent = React.lazy(() => import('./Component'));
function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <myComponent />
      </Suspense>
    </div>
  );
}
Vue3 也新增了 React.lazy 类似功能的 defineAsyncComponent 函数，处理动态引入（的组件）。defineAsyncComponent可以接受返回承诺的工厂函数。当您从服务器检索到组件定义时，应该调用Promise的解析回调。您还可以调用reject(reason)来指示负载已经失败

import { defineAsyncComponent } from 'vue'
const AsyncComp = defineAsyncComponent(() =>
  import('./components/AsyncComponent.vue')
)
app.component('async-component', AsyncComp)
Vue3 也新增了 Suspense 组件:
<template>
  <Suspense>
    <template #default>
      <my-component />
    </template>
    <template #fallback>
      Loading ...
    </template>
  </Suspense>
</template>

<script lang='ts'>
 import { defineComponent, defineAsyncComponent } from "vue";
 const MyComponent = defineAsyncComponent(() => import('./Component'));

export default defineComponent({
   components: {
     MyComponent
   },
   setup() {
     return {}
   }
})
</script>
Application Config

errorHandler
顶层错误捕获

app.config.errorHandler = (err, vm, info) => {
    console.log(err)
}
warnHandler
顶层警告捕获

app.config.warnHandler = function(msg, vm, trace) {
   console.log(msg)
}
globalProperties
全局配置项，类似 Vue2 的 Vue.prototype.$http = $axios; 用法

app.config.globalProperties.foo = 'bar'
isCustomElement
这个 Api 的作用在于能够把第三方或者自定义而没有在 Vue 中注册标签使用时，忽略警告。

<template>
    <haha-Hello>123</haha-Hello>
</template>
export default {
    name: 'hello'
}
正常情况下是会报警告的，但这个 Api 就能配置忽略这个警告，标识这是我自定义的组件。
用法：
app.config.isCustomElement = tag => tag.startsWith('haha-')

注意：目前这个 Api 是有问题的，请看 girhub issues
这里提供了一些解决方案，Vue 作者尤雨溪也说明了，这个 Api 目前有点问题：

As pointed out, Vue 3 requires configuring custom elements via compiler options if pre-compiling templates.
如前所述，如果是预编译模板，则Vue 3需要通过编译器选项配置自定义元素。
This seems to be now a Vue CLI specific configuration problem so I'm closing it. But feel free to continue the discussion.
现在这似乎是Vue CLI特定的配置问题，因此我将其关闭。但是请随时继续讨论。

从中提到了，预编译模板(template)使用自定义标签，需要通过编译器选项配置自定义元素，从 girhub issues
中可以看到一个答案，在 vite 上的解决方案：

vite.config.js:
vueCompilerOptions: {
    isCustomElement: tag => {
      return /^x-/.test(tag)
    }
}
具体可以看 Vite 的 Api：github vite Api 中的 config 在配置项：config.ts 就可以找到 Vue 编译选项配置字段：vueCompilerOptions
这样配置后就可以忽略上诉例子的警告了：

vueCompilerOptions: {
    isCustomElement: tag => {
      return /^haha-/.test(tag)
    }
}
optionMergeStrategies
这个 Api 是只针对于 options Api 的，作用是对 mixin 的合并更改策略。

const app = Vue.createApp({
  custom: 'hello!'
})
app.config.optionMergeStrategies.custom = (parent, child) => {
  console.log(child, parent)
  // => "goodbye!", undefined
  // => "hello", "goodbye!"
  return child || parent
}
app.mixin({
  custom: 'goodbye!',
  created() {
    console.log(this.$options.custom) // => "hello!"
  }
})
这里可以看到，在 created 输出的时候，输出的是 hello，就是因为设置了合并策略，当组件和 mixin 存在相同属性的时候，会使用 child 的值，当不存在自定义属性重复的时候，当前组件输出的就是 child 因为这时候 parent 为 undefined
www.zhihu.com/question/40… 什么时候执行 render 函数

Directive
Vue2:

<div id="hook-arguments-example" v-demo:[foo].a.b="message"></div>
Vue.directive('demo', {
  bind: function (el, binding, vnode) {
    var s = JSON.stringify
    el.innerHTML =
      'name: '       + s(binding.name) + '<br>' +
      'value: '      + s(binding.value) + '<br>' +
      'expression: ' + s(binding.expression) + '<br>' +
      'argument: '   + s(binding.arg) + '<br>' +
      'modifiers: '  + s(binding.modifiers) + '<br>' +
      'vnode keys: ' + Object.keys(vnode).join(', ')
  }
})
new Vue({
  el: '#hook-arguments-example',
  data: {
    foo: 'HaHa'
    message: { color: 'white', text: 'hello!' }
  }
})
/*
 * name: "demo"
 * value: { color: 'white', text: 'hello!' }
 * expression: "message"
 * argument: "HaHa"
 * modifiers: {a: true, b: true}
 * name: "tag, data, children, text, elm, ns, context, fnContext, fnOptions, fnScopeId, key, componentOptions, componentInstance, parent, raw, isStatic, isRootInsert, isComment, isCloned, isOnce, asyncFactory, asyncMeta, isAsyncPlaceholder"
 **/
Vue3:
Vue3.x 和 Vue2.x 的指令在生命周期上有这明显差别，但使用是差不多的

import { createApp } from 'vue'
const app = createApp({})
// register
app.directive('my-directive', {
  // called before bound element's attributes or event listeners are applied
  created() {},
  // called before bound element's parent component is mounted
  beforeMount() {},
  // called when bound element's parent component is mounted
  mounted() {},
  // called before the containing component's VNode is updated
  beforeUpdate() {},
  // called after the containing component's VNode and the VNodes of its children // have updated
  updated() {},
  // called before the bound element's parent component is unmounted
  beforeUnmount() {},
  // called when the bound element's parent component is unmounted
  unmounted() {}
})

// register (function directive)
app.directive('my-directive', () => {
  // this will be called as `mounted` and `updated`
})
// getter, return the directive definition if registered
const myDirective = app.directive('my-directive')
instance: 使用指令的组件实例。
value: 传递给指令的值。例如，在v-my-directive =“ 1 + 1”中，该值为2。
oldValue: 旧的值，仅在 beforeUpdate 和更新时可用。值是否已更改都可用。
arg: 参数传递给指令（如果有）。例如，在 v-my-directive：foo 中，arg 为“ foo”。
modifiers: 包含修饰符（如果有）的对象。例如，在v-my-directive.foo.bar 中，修饰符对象为 {foo：true，bar：true}。
dir: 一个对象，在注册指令时作为参数传递。例如，在指令中
app.directive('focus', {
  mounted(el) {
    el.focus()
  }
})
dir就是：
{
  mounted(el) {
    el.focus()
  }
}
use 和 plug
如何制作插件和使用插件？
请看以下案例：

// 自定义 plug 插件
// myUi.js
import MyButton from './MyButton.vue';
import MyInput from './MyInput.vue';
const componentPool = [
    MyButton,
    MyInput
];
export default {
    install () {
        if (options.components) {
            option.components.map((compName) => {
                componentPool.map((comp) => {
                    if (compName === comp.name) {
                        app.component(comp.name, comp);
                    }
                })            
            })
        } else {
            componentPool.map(comp => {
                app.component(comp.name, comp);
            })
        }
    }
}
myUi 该插件，简单的实现了一下按需加载 UI 的方案

// main.js
import { createApp } from 'vue';
import App from './App.vue';
import MyUI from './libs/MyUI';
const app = createApp(App);
app.use(MyUI, {
    components: [
        'MyButton',
        'MyInput'
    ]
})
markRaw
import { reactive, markRaw } from 'vue';
export defalut {
    name: 'App',
    setup() {
        let obj = {name: 'Benson', age: 18};
        obj = markRow(obj);
        let state = reactive(obj);
        function fn() {
            state.name = 'zs';
        }
        return { state, fn }
    }
}
markRaw 的作用就是禁止源数据不能被用于监听，经过上述处理后，reactive 处理 obj 进行响应式数据的封装将不在其作用了。

customRef
创建一个自定义的 ref，并对其依赖项跟踪和更新触发进行显式控制。它需要一个工厂函数，该函数接收 track 和 trigger 函数作为参数，并应返回一个带有 get 和 set 的对象。
使用 v-model 使用自定义 ref 实现 debounce 的示例：
Html

<input v-model="text" />
function useDebouncedRef(value, delay = 200) {
  let timeout
  return customRef((track, trigger) => {
    return {
      get() {
        track()
        return value
      },
      set(newValue) {
        clearTimeout(timeout)
        timeout = setTimeout(() => {
          value = newValue
          trigger()
        }, delay)
      }
    }
  })
}
export default {
  setup() {
    return {
      text: useDebouncedRef('hello')
    }
  }
}
shallowReadonly
const state = shallowReadonly({
  foo: 1,
  nested: {
    bar: 2
  }
});
// 改变状态本身的 property 将失败
state.foo++;
// ...但适用于嵌套对象
isReadonly(state.nested); // false
state.nested.bar++; // 适用
只读效果，仅仅控制在 第一层，深层的属性还是可以变更新的
还有一个注意点，ES6 的 const 也能控制只读，但请看下面例子：

const argu_1 = {name: '小明', attrs: {sex: 18}};
const argu_2 = readonly({name: '小红'}, attrs: {sex: 16});
const argu_3 = shallowReadonly({name: '小夏'}, attrs: {sex: 17});
argu_1 控制这该变量不可变更，但是可以变更里面的属性
argu_2 控制着每一层属性都不可变更，但是 argu_2 可以重新赋值
argu_3 控制着第一层属性不可变更，深层的属性可以变更，比如 sex 是可以变更的，argu_3 可以重新赋值。
emits 定义自定义事件
这个语法类似于 vue 组件中使用 props 校验传入的参数。
例子：

app.component('custom-form', {
  // 数组方式, 只对自定义事件名称校验
  emits: ['click', 'submit'],
  // 对象方式
  emits: {
    // 没有验证
    click: null,
    // 验证submit 事件
    submit: ({ email, password }) => {
      if (email && password) {
        return true
      } else {
        console.warn('Invalid submit event payload!')
        return false
      }
    }
  },
  methods: {
    submitForm() {
      this.$emit('submit', { email, password })
    }
  }
})
script scoped 支持全局规则或只针对插槽内容的规则
deep 选择器

<style scoped>
    /* deep selectors */
    ::v-deep(.foo) {}
    /* shorthand */
    :deep(.foo) {}
</style>
最初，支持>>>组合器以使选择器为“ deep”。但是，某些CSS预处理器（例如SASS）在解析它时会遇到问题，因为这不是官方的CSS组合器。
后来切换到/ deep /，这曾经是CSS的实际建议添加（甚至是Chrome本身提供的），但后来删除了。这引起了一些用户的困惑，因为他们担心在Vue SFC中使用/ deep /会导致在删除该功能的浏览器中不支持其代码。但是，就像>>>一样，/ deep /仅由Vue的SFC编译器用作编译时提示以重写选择器，并在最终CSS中被删除。
为了避免丢失的/ deep /组合器造成混乱，引入了另一个自定义组合器:: v-deep，这次更加明确地表明这是Vue特定的扩展，并使用伪元素语法，以便任何-处理器应该能够解析它。
由于当前Vue 2 SFC编译器的兼容性原因，仍支持深度组合器的先前版本，这又可能使用户感到困惑。在v3中，我们不再支持>>>和/ deep /。
在研究适用于v3的新SFC编译器时，我们注意到CSS伪元素实际上在语义上不是组合符。伪元素改为接受参数，这与惯用CSS更加一致，因此，我们也使:: v-deep（）那样工作。如果您不关心显式的v-前缀，也可以使用更短的：deep（）变体，其工作原理完全相同。
当前仍支持:: v-deep作为组合器，但已将其弃用，并会发出警告。
插槽样式

<style scoped>
    /* targeting slot content */
    ::v-slotted(.foo) {}
    /* shorthand */
    :slotted(.foo) {}
</style>
当前，从父级传入的 slot 内容受父级的作用域样式和子级的作用域样式的影响。无法编写仅明确指定 slot 内容或不影响 slot 内容的规则。
在v3中，我们打算默认使子范围的样式不影响 slot 的内容。为了显式地指定插槽内容，可以使用:: v-slotted（）（简写为:: slotted（））伪元素。
scoped 情况下定义全局样式

<style scoped>
    /* one-off global rule */
    ::v-global(.foo) {}
    /* shorthand */
    :global(.foo) {}
</style>
当前，要添加全局CSS规则，我们需要使用单独的无作用域块。我们将引入一个新的:: v-global（）（简写为:: global（））伪元素，以用于一次性全局规则。
实验状态的特性

script setup
<template>
  <button @click="inc">{{ count }}</button>
</template>
<script setup="props, { emit }">
  import { ref, onMounted } from 'vue'
  export const count = ref(0)
  export const inc = () => count.value++
  onMounted(() => {
    emit('foo');
  )
</script>
script vars
支持将组件状态驱动的 CSS 变量注入到“单个文件组件”样式中。
案例：

<template>
  <div class="text">hello</div>
</template>
<script>
export default {
  data() {
    return {
      color: 'red'
    }
  }
}
</script>
<style vars="{ color }">
.text {
  color: var(--color);
}
</style>
注意的一点，目前 Vue SFC 样式提供了直接的 CS 配置和封装，但是它是纯静态的-这意味着到目前为止，尚无法根据组件的状态在运行时动态更新样式。
当样式存在 scoped 局部样式和又要使用全局 var 的情况：

<style scoped vars="{ color }">
h1 {
  color: var(--color);
  font-size: var(--global:fontSize);
}
</style>
这里的 fontSize 是全局的 var css 变量
经过 compiles 后：

h1 {
  color: var(--6b53742-color);
  font-size: var(--fontSize);
}
引入element组件库
element-plus组件库：https://element-plus.gitee.io/#/zh-CN
element-plus仓库地址：https://github.com/element-plus/element-plus
引入

import ElementPlus from 'element-plus'
import 'element-plus/lib/theme-chalk/index.css'
import { createApp } from 'vue'
import App from './App.vue'

createApp(App).use(ElementPlus).mount('#app')
vue打包优化
1. 配置 gzip 压缩，打出来一个待 gzip 后缀的文件
$ npm i compression-webpack-plugin -D
const CompressionWebpackPlugin = require('compression-webpack-plugin');
module.exports = {
    configureWebpack: config => {
        if (process.env.NODE_ENV === 'production') {
            config.plugins.push(
                ...[
                    new CompressionWebpackPlugin({
                        filename: '[path].gz[query]',
                        algorithm: 'gzip',
                        test: /\.(js|css|html|svg)$/i,
                        threshold: 2048,
                        minRatio: 0.8
                    })
                ]
            );
        }
    }
};
2. webpack-bundle-analyzer 分析包
$ npm i webpack-bundle-analyzer -D
module.exports = {
    chainWebpack: config => {
        if (process.env.NODE_ENV === 'production') {
            // 启动时动态创建一个html：http://localhost:8888/report.html
            // config.plugin('webpack-bundle-analyzer').use(require('webpack-bundle-analyzer').BundleAnalyzerPlugin);
            // 生成一个静态html，report.html
            config.plugin('webpack-report').use(require('webpack-bundle-analyzer').BundleAnalyzerPlugin, [
                {
                    analyzerMode: 'static'
                }
            ]);
        }
    }
};
3. webpack splitChunks， 提取出来的通用 'echarts', 'moment', 'element-ui', 'xlsx'等
// ['echarts', 'moment', 'element-ui', 'xlsx', 'chunk-vendors', 'chunk-common', 'index']
module.exports = {
    pages: {
        index: {
            // ...
            // 在这个页面中包含的块，默认情况下会包含
            // 提取出来的通用 chunk 和 vendor chunk。
            chunks: ['echarts', 'moment', 'element-ui', 'xlsx', 'chunk-vendors', 'chunk-common', 'index']
        },
        chainWebpack: config => {
            if (process.env.NODE_ENV === 'production') {
                // vue-cli3.x+默认配置
                // config.optimization.splitChunks({
                //     chunks: 'async',
                //     minSize: 30000,
                //     maxSize: 0,
                //     minChunks: 1,
                //     maxAsyncRequests: 6,
                //     maxInitialRequests: 4,
                //     automaticNameDelimiter: '~',
                //     cacheGroups: {
                //         vendors: {
                //             name: 'chunk-vendors',
                //             test: /[\\/]node_modules[\\/]/,
                //             priority: -10,
                //             chunks: 'initial'
                //         },
                //         common: {
                //             name: 'chunk-common',
                //             minChunks: 2,
                //             priority: -20,
                //             chunks: 'initial',
                //             reuseExistingChunk: true
                //         }
                //     }
                // });
                config.optimization.splitChunks({
                    chunks: 'async',
                    minSize: 1024 * 10, // 30000,
                    maxSize: 0,
                    minChunks: 1,
                    maxAsyncRequests: 6,
                    maxInitialRequests: 4,
                    automaticNameDelimiter: '~',
                    cacheGroups: {
                        // 链接：https://juejin.cn/post/6844904105555525640
                        echarts: {
                            name: 'echarts',
                            test: /[\\/]node_modules[\\/]echarts[\\/]/,
                            minSize: 0,
                            minChunks: 1,
                            reuseExistingChunk: true,
                            chunks: 'all'
                        },
                        moment: {
                            name: 'moment',
                            test: /[\\/]node_modules[\\/]moment[\\/]/,
                            minSize: 0,
                            minChunks: 1,
                            reuseExistingChunk: true,
                            chunks: 'all'
                        },
                        'element-ui': {
                            name: 'element-ui',
                            test: /[\\/]node_modules[\\/]element-ui[\\/]/,
                            minSize: 0,
                            minChunks: 1,
                            reuseExistingChunk: true,
                            chunks: 'all'
                        },
                        xlsx: {
                            name: 'xlsx',
                            test: /[\\/]node_modules[\\/]xlsx[\\/]/,
                            minSize: 0,
                            minChunks: 1,
                            reuseExistingChunk: true,
                            chunks: 'all'
                        },

                        vendors: {
                            name: 'chunk-vendors',
                            test: /[\\/]node_modules[\\/]/,
                            priority: -10,
                            chunks: 'initial'
                        },
                        common: {
                            name: 'chunk-common',
                            minChunks: 2,
                            priority: -20,
                            chunks: 'initial',
                            reuseExistingChunk: true
                        }
                    }
                });
            }
        }
    }
}
4. momentjs 优化
方案 1：只打包使用的文件（目前用的时这个）

vue.config.js
module.exports = {
    configureWebpack: config => {
        config.plugins.push(
            ...[
                // 链接：https://juejin.cn/post/6844904105555525640
                new webpack.ContextReplacementPlugin(
                    /moment[/\\]locale$/, // 这个参数表明了我们要改变的打包上下文
                    /zh-cn/ // 这个参数表示我们只想打包这个正则匹配的文件
                )
            ]
        );
    }
};
方案 2：使用 dayjs 替代 moment，代码不用变，把 lodash 配置别名为 dayjs
知乎上的文章： zhuanlan.zhihu.com/p/61031739?…

module.exports = {
    chainWebpack: config => {
        config.resolve.alias
        // set第一个参数：设置的别名，第二个参数：真实的名称（默认都是从node_modules中读取
        .set('moment','dayjs'));
        }
};
方案 3： ...
5. lodash 优化
$ npm i lodash-webpack-plugin babel-plugin-lodash -D
babel.config.js
module.exports = {
    presets: ['@vue/app'],
    plugins: [
        'lodash',
        [
            'component',
            {
                libraryName: 'element-ui',
                styleLibraryName: 'theme-chalk'
            }
        ]
    ]
};
vue.config.js
const LodashModuleReplacementPlugin = require('lodash-webpack-plugin');
module.exports = {
    configureWebpack: config => {
        config.plugins.push(
            ...[
                new LodashModuleReplacementPlugin(),
                // 链接：https://juejin.cn/post/6844904105555525640
                new webpack.ContextReplacementPlugin(
                    /moment[/\\]locale$/, // 这个参数表明了我们要改变的打包上下文
                    /zh-cn/ // 这个参数表示我们只想打包这个正则匹配的文件
                )
            ]
        );
    }
};
6. 小图片压缩成 base64 格式
module.exports = {
    chainWebpack: config => {
        // 10kb以内的图片会被打包成内联元素
        config.module
            .rule('images')
            .use('url-loader')
            .loader('url-loader')
            .tap(options => Object.assign(options, {limit: 10240}));
    }
};
7. xlsx 改为 xlsx.mini.min
把所有

import XLSX from 'xlsx';
改为以下的方式引入

import XLSX from 'xlsx/dist/xlsx.mini.min.js';
8. http1.1升级http2.0
注意的点：

nginx version 1.9.4 以上
当前配置http2基于https协议
nginx配置文件 nginx.conf
    # http转https
    server {
        listen 80;
        server_name yourdomain.com; #需要将yourdomain.com替换成证书绑定的域名。
        rewrite ^(.*) https://$server_name$1 permanent; #将所有HTTP请求通过rewrite指令重定向到HTTPS。
    }
    #以下属性中，以ssl开头的属性表示与证书配置有关。
    server {
        # 开启gzip
        gzip on;

        # 启用gzip压缩的最小文件，小于设置值的文件将不会压缩
        gzip_min_length 1k;

        # gzip 压缩级别，1-9，数字越大压缩的越好，也越占用CPU时间，后面会有详细说明
        gzip_comp_level 9;

        # 进行压缩的文件类型。javascript有多种形式。其中的值可以在 mime.types 文件中找到。
        gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png application/vnd.ms-fontobject font/ttf font/opentype font/x-woff image/svg+xml;

        # 是否在http header中添加Vary: Accept-Encoding，建议开启
        gzip_vary on;

        # 禁用IE 6 gzip
        gzip_disable "MSIE [1-6]\.";

        # 设置压缩所需要的缓冲区大小
        gzip_buffers 32 4k;

        # 设置gzip压缩针对的HTTP协议版本
        gzip_http_version 1.1;
        
        # 注意这里http2
        listen 443 ssl http2;
        #配置HTTPS的默认访问端口为443。
        #如果未在此处配置HTTPS的默认访问端口，可能会造成Nginx无法启动。
        #如果您使用Nginx 1.15.0及以上版本，请使用listen 443 ssl代替listen 443和ssl on。
        server_name yourdomain.com; #需要将yourdomain.com替换成证书绑定的域名。
        root html;
        index index.html index.htm;
        ssl_certificate cert/cert-file-name.pem;  #需要将cert-file-name.pem替换成已上传的证书文件的名称。
        ssl_certificate_key cert/cert-file-name.com.key; #需要将cert-file-name.key替换成已上传的证书密钥文件的名称。
        ssl_session_timeout 5m;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
        #表示使用的加密套件的类型。
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #表示使用的TLS协议的类型。
        ssl_prefer_server_ciphers on;
        # location / {
        #     root html;  #站点目录。
        #     index index.html index.htm;
        # }
        location / {
            # 反向代理
            proxy_pass http://127.0.0.1:8090;
        }
        error_page 404 /404.html;
            location = /40x.html {
        }
        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
http2.0 network 效果

9. SEO 优化
<meta name="description" content="vue,element-ui,nodejs,express,nginx全栈管理后台项目" />
vue-router(4.x)
import { createRouter } from "vue-router"
const router = createRouter({
    // options
    .....
})
路由模式
createWebHashHistory (hash)
createWebHashHistory (history)

import { createRouter,createWebHashHistory,createWebHashHistory} from 'vue-router'
const router = createRouter({
    history:createWebHashHistory() / createWebHashHistory()
})
重定向
{
    path: '/:pathMatch(.*)*', // 需要使用正则去匹配
    redirect: Home,
}
挂载方式

因为vue3的composition api，vue-router的挂载方式以插件来挂载
4.x的用法

import { createApp } from 'vue'
import router from './router.js'
import App from './App.vue'
createApp(App).use(router).mount('#app');
组件中的使用
因为setup中不能访 this,所以提供两个api来获取 router 和 route ， useRouter() 和 useRoute()
4.x的用法

   import { useRouter,useRoute } from "vue-router"
   export default({
      setup(){
        const router = useRouter();
        const route = useRoute();
        const linkToHome = () => {
            router.push({
                path:'/'
            })
        }
        return{
            linkToHome
        }
      }
   })
vue3.0 的生态
官网:https://v3.vuejs.org/
源码:https://github.com/vuejs/vue-next
vite构建器:https://github.com/vitejs/vite
脚手架:https://cli.vuejs.org/
vue-router-next:https://github.com/vuejs/vue-router-next
vuex4.0:https://github.com/vuejs/vuex/tree/4.0

UI 组件库
vant2.x:https://vant-contrib.gitee.io/vant/v3/#/zh-CN/
Ant Design of Vue 2.x:https://2x.antdv.com/docs/vue/introduce-cn/
element-plus:https://element-plus.org/#/zh-CN

参考文章
Vue3.0 尝鲜 ：https://juejin.cn/post/6847902215458258958
Vue3.0 && Vue3.0初体验 一 ：https://juejin.cn/post/6847902215458258958
Vue3.0尝试 https://juejin.cn/post/6844904084617560071
记Vue3.0 :https://juejin.cn/post/6906779250972590087
初识 Vue3.0 :https://juejin.cn/post/6903149708332498952#heading-3
Vue3.0尝鲜 ：https://juejin.cn/post/6844903986873499662
Vue3.0变动简介 :https://juejin.cn/post/6859541760797442062
Vue3.0学习笔记 :https://juejin.cn/post/6908185323801575432
Vue3.0系列——「vue3.0学习手册」第一期 :https://juejin.cn/post/6886844248126128135
Vue3.0实现todoList案例 :https://juejin.cn/post/6915018376851144718
Vue3.0 体验(API 及 TodoList Demo):https://juejin.cn/post/6923351431617445902
快速使用Vue3最新的15个常用API:https://juejin.cn/post/6897030228867022856#heading-15
2021新年 Vue3.0 + Element UI 尝鲜小记：https://juejin.cn/post/6914913275402518541
你30分钟快速掌握vue 3 :https://juejin.cn/post/6887359442354962445
Vue 3.0 新特性与使用 三 :https://juejin.cn/post/6916780404099792910#heading-6
Vue 3.0 新特性与使用 二 :https://juejin.cn/post/6902310695862435847#heading-15
vue-cli3.0 打包优化实践:https://juejin.cn/post/6913531130180272142
Vue3.0 && Vue3.0初体验 一：https://juejin.cn/post/6847902215458258958#heading-4
vue3.0 正式版体验 ：https://juejin.cn/post/6883380750683471879
学习笔记：vue-router(4.x) 与 vue-router(3.x)的区别:https://juejin.cn/post/6912683689725919239

