---
title: Vue学习笔记-2
date: 2025-12-11 21:01:41
tags:
    - 网站开发
    - 前端
categories:
    - 前端
    - Vue
---

图灵学院 Vue 学习笔记
<!-- more -->

<div class="catalog">
    <h3>目录</h3>
    <a href="#chapter1">1. 整体认识 Vue3</a><br>
    <a href="#chapter1.1" style="margin-left: 2em;">1.1 创建工程</a><br>
    <a href="#chapter1.2" style="margin-left: 2em;">1.2 项目结构</a><br>
    <a href="#chapter2">2. 理解：数据的双向绑定</a><br>
    <a href="#chapter3">3. OptionsAPI 和 CompositionAPI</a><br>
    <a href="#chapter4">4. Vue3 的数据双向绑定</a><br>
    <a href="#chapter5">5. 自定义组件</a><br>
    <a href="#chapter5.1" style="margin-left: 2em;">5.1 定义子组件</a><br>
    <a href="#chapter5.2" style="margin-left: 2em;">5.2 将子组件的对象暴露给父组件</a><br>
    <a href="#chapter5.3" style="margin-left: 2em;">5.3 将父组件的对象暴露给子组件</a><br>
    <a href="#chapter6">6. 组件的生命周期</a><br>
    <a href="#chapter7">7. Vue-Router 组件路由管理机制</a><br>
    
</div>

<h2 id="chapter1">1. 整体认识 Vue3</h2>

<h3 id="chapter1.1">1.1 创建工程</h3>

```bash
# 创建工程
npm create vue@latest 
# 启动项目
npm install
npm run dev
```

<h3 id="chapter1.2">1.2 项目结构</h3>

1. `node_modules`目录：存放依赖

2. `public`目录：静态资源，如图片视频等

3. `src`目录：放源代码

4. `index.html`：Vue 项目访问页面的入口

5. `package.json`文件：devDependencies ——依赖，scripts —— 启动脚本等

启动项目：`npm run dev`
打包项目：`npm run build`

<strong>小结：</strong>

1. 典型的 Vue 项目都只在 index 这一个页面里进行交互，即 SPA

2. Vue3 的核心是通过 createApp 创建一个应用实例，在实例中构建各种应用

3. 每一个 .vue 文件都是一个组件，组件可以互相嵌套

4. 每个 vue 文件都包括 script, template, style

<h2 id="chapter2">2. 理解：数据的双向绑定</h2>

<strong>理解：</strong>把 template 里的页面数据和 script 里的数据建立绑定关系

<strong>v-model 绑定数据</strong>
<strong>v-on 绑定方法</strong><br>

Vue 单文件组件（SFC）的 `<template>` 要求只能有一个根节点 `<div>`

示例代码，注意 data 和 methods 的写法：

```JavaScript
<template>
  <div>
    姓名：<input v-model="userName">{{ userName }}<br><br>
    薪水：<input type="number" v-model="salary">{{ salary }}<br><br>
    <button @click="addSalary">加薪</button>
  </div>
</template>

{/* lang="ts" 声明该脚本块使用TypeScript编写 */}
<script lang="ts">
  export default{
    data(){
      return {
        userName: "roy",
        salary: 1800
      }
    },
    methods:{
      addSalary(){
        this.salary += 1000
      }
    }
  }
</script>

<style scoped>

</style>
```

style 中的 scope 表示样式只在当前文件中生效

<strong>小结：</strong>

1. 常用的双向绑定：

- v-if: 控制元素显示（直接摧毁）
- v-show：控制元素显示（通过 display）
- v-for: 循环显示元素
- @方法: 将事件和方法绑定

2. 在写 methods 时不要忘了 this 关键字

<h2 id="chapter3">3. OptionsAPI 和 CompositionAPI</h2>

OptionsAPI：配置式 API，用一个统一的<strong>对象</strong>封装所有代码逻辑

OptionsAPI 代码示例：

```JavaScript
<script>
  export default{
    data(){
      return{
        userInfo: {
          name: "",
          age: 0,
          gender: 1,
          job: "",
          skills: ["Java", "Python", "Vue"]
        },
        newSkill: "",
        showUserInfo: false
      }
    },
    methods: {
      learnNewSkill(){
        if(this.newSkill){
          this.userInfo.skills.push(this.newSkill)
        }
      },
      changeShwoInfo(){
        this.showUserInfo = !this.showUserInfo
      }
    }
  }
</script>
```

CompositionAPI：组合式 API，把数据和方法写在一起

CompositionAPI 代码示例：

```JavaScript
<script lang="ts">
import { ref } from 'vue';

  export default{
    setup(){ // 在进入页面时就调用这个函数
      let userName = ref("joy") // ref 实现双向绑定
      let salary = ref(1500)
      function addSalary(){
        salary.value += 1000
      }
      return {userName, salary, addSalary} // 把需要用的数据和方法返回
    }
  }
</script>
```

可以通过 setup 的语法糖简化代码，在 script 中写 setup，就无需在代码中 setup 并 return 了

```JavaScript
<script setup lang="ts">
  import { ref } from 'vue';
  let userName = ref("joy")
  let salary = ref(1500)
  function addSalary(){
    salary.value += 1000
  }
</script>
```

组合式 API 的另一个好处是可以在另一个文件里写逻辑，在 .vue 中直接调用即可

例如，在 components 文件夹下新建一个 MySalary.ts，内容如下：

```TypeScript
import { ref } from "vue";

export default function(){ // 导出一个函数，执行并返回动态的值；而不是导出对象
    let userName = ref("roy")
    let salary = ref(1500);
    function addSalary(){
        salary.value += 1000
    }
    return {userName, salary, addSalary}
}
```

在 App.vue 中直接调用：

```Vue
<script setup lang="ts">
  import MySalary from "@/components/MySalary" // @等价于 src 文件夹；自定义导出的匿名函数的名称为 Salary
  let {userName, salary, addSalary} = MySalary() // 解包对象
</script>
```

<h2 id="chapter4">4. Vue3 的数据双向绑定</h2>

不要操作 ref 对象本身，而是操作它的 value 属性

让对象具备响应式的能力：reactive

```Vue
<script setup lang="ts">
import { reactive } from 'vue';
  let userInfo = reactive({userName: "roy", salary: 15000})
  function addSalary(){
    userInfo.salary += 1000
  }
</script>
```

注意：ref 包裹的数据需要通过 value 获取值，而 reactive 不需要

toRef() 可以把对象转成 ref 对象，使其具备响应式能力

toRefs(对象)：把这个对象的所有属性都转成 ref 对象

<h2 id="chapter5">5. 自定义组件</h2>

<h3 id="chapter5.1">5.1 定义子组件</h3>

在 components 文件夹下新建组件 SalaryInfo.vue，内容如下：

```Vue
<template>
    姓名：<input type="text" v-model="UserName"/><br />
    工资：<input type="number" v-model="UserSalary"/> <br />
    加薪：<input type="button" @click="AddSalary" value="点击">
</template>

<!-- 这部分是为了暴露组件，自定义名称为"SalaryInfo" -->
<script lang="ts"> 
    export default{
        name: "SalaryInfo"
    }
</script>

<script setup lang="ts">
    import { ref } from 'vue';
    let UserName = ref("roy")
    let UserSalary = ref(15000)
    function AddSalary(){
        UserSalary.value += 1000
    }   
</script>

<style>

</style>
```

定义好之后，在 App.vue 中引入组件：

```Vue
<template>
<div>
  <!-- 在这里引入组件 -->
  <SalaryInfo></SalaryInfo>
</div>
</template>

<script setup lang="ts">
import SalaryInfo from './components/SalaryInfo.vue';

</script>

<style scoped>

</style>
```

<h3 id="chapter5.2">5.2 将子组件的对象暴露给父组件</h3>

在父组件中我们可以获取子组件的一些属性。在此之前，需要让子组件先暴露给父组件：在 SalaryInfo.vue 中作出如下修改：

```Vue
<script setup lang="ts">
    import { ref } from 'vue';
    let userName = ref("roy")
    let userSalary = ref(15000)
    function AddSalary(){
        userSalary.value += 1000
    }  
    // 添加以下代码，暴露出父组件需要的部分
    defineExpose({userName, userSalary, AddSalary}) 
</script>
```

App.vue 修改如下：

```Vue
<template>
<div>
  <SalaryInfo ref="salaryInfo"></SalaryInfo>
  <button @click="showRes">查看薪水信息</button>
</div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import SalaryInfo from './components/SalaryInfo.vue';
let salaryInfo = ref();
function showRes(){
  console.log(salaryInfo)
}
</script>

<style scoped>

</style>
```

`<SalaryInfo ref="salaryInfo"></SalaryInfo>`：ref 在 Vue 中用来<strong>获取 DOM 元素或子组件实例的引用</strong>。Vue 会自动将该子组件的实例对象挂载到 `<script setup>` 中同名的 ref 变量上。在这里是`let salaryInfo = ref();`。此处已经获取到子组件，通过 console.log 在控制台打印信息

<h3 id="chapter5.3">5.3 将父组件的对象暴露给子组件</h3>

修改后的代码如下，下面将作详细说明：

`SalaryInfo.vue`

```Vue
<template>
    姓名：<input type="text" v-model="salaryInfo.userName"/><br />
    工资：<input type="number" v-model="salaryInfo.userSalary"/> <br />
    <!-- 加薪：<input type="button" @click="AddSalary" value="点击"> -->
</template>

<script lang="ts">
    export default{
        name: "SalaryInfo"
    }
</script>

<script setup lang="ts">
    defineProps([
        "salaryInfo"
    ])
</script>

<style>

</style>
```

`App.vue`

```Vue
<template>
<div>
  <SalaryInfo :salary-info="salaryInfo"></SalaryInfo>
  <button @click="showRes">查看薪水信息</button>
</div>
</template>

<script setup lang="ts">
import { reactive } from 'vue'
import SalaryInfo from './components/SalaryInfo.vue';
let salaryInfo = reactive({
  userName: "roy", userSalary: 15000
})

function showRes(){
  salaryInfo.userSalary += 1000
}
</script>

<style scoped>

</style>
```

在 SalaryInfo.vue 中，通过`defineProps`接收来自父组件暴露的数据，接受的是一个列表，列表中的元素是暴露的数据的<strong>名字</strong>，即salaryInfo。`<input type="text" v-model="salaryInfo.userName"/>`中获取的 userName 就来源于它（这里并没有对获取的数据做校验，也就是说不能保证获取到的 salaryInfo 对象一定有 userName 属性，这里可能会报错）。

在 App.vue 中，`<SalaryInfo :salary-info="salaryInfo"></SalaryInfo>`中的`:`是 v-bind 的简写，用于双向绑定动态资源；`:salary-info`中的 salary-info 是子组件接收的 Prop 名称，也就是父组件向外暴露的名称。在 Vue 的处理过程中，会把 salary-info 转为 salaryInfo，也就是子组件中要接收的数据的名字。最后的`salaryInfo`是数据源的名称，对应着

```JavaScript
let salaryInfo = reactive({
  userName: "roy", userSalary: 15000
})
```

<h2 id="chapter6">6. 组件的生命周期</h2>

生命周期分为四个阶段：创建 挂载 更新 销毁（卸载）

CompositionAPI 的生命周期阶段：

- 创建阶段：setup
- 挂载阶段：onBeforeMount onMounted
- 更新阶段：onBeforeUpdate onUpdated
- 卸载阶段：onBeforeUnmount onUnmounted

用法如下：

```Vue
<script setup lang="ts">
import { onMounted } from 'vue'
onMounted(()=>{
  console.log("挂载后")
})
</script>
```

<h2 id="chapter7">7. Vue-Router 组件路由管理机制</h2>

路由：实现在页面上点击跳转

安装 Vue-Router：`npm install vue-router@4`（也可以在创建项目时就把 vue-router 选上）

App.vue 内容如下，目标是通过点击“首页”、“关于”、“新闻”实现跳转：

```Vue
<script setup lang="ts"></script>

<template>
  <div id="app">
    <h1>Hello App!</h1>
    <p>
      <a href="">首页</a>
      <a href="">关于</a>
      <a href="">新闻</a>
    </p>
    <div class="container"></div>

  </div>
</template>

<style scoped>
  a {
    margin-right: 10px;
    color: green;
  }
  .container {
    background-color: yellowgreen;
    widows: 10%;
    height: 400px;
  }
</style>

```

首先需要在 src/pages 文件夹下创建三个页面对应的组件：HomePage.vue, AboutPage.vue, NewsPage.vue

在 main.ts 中实现路由逻辑。一共分为三步：配置路由规则、创建路由器、加载路由器

```JavaScript
import { createApp } from 'vue'
import App from './App.vue'
import HomePage from './pages/HomePage.vue'
import AboutPage from './pages/AboutPage.vue'
import NewsPage from './pages/NewsPage.vue'
import {createRouter, createWebHistory} from 'vue-router'

// 1. 配置路由规则
// 创建路由数组 path: 路径 component: 需要跳转到的页面组件 name：路由的别名
const routes = [
    {path: "/home", component: HomePage, name: "home"},
    {path: "/about", component: AboutPage, name: "about"},
    {path: "/news", component: NewsPage, name: "news"}
]

// 2. 创建路由器
const router = createRouter({
    history: createWebHistory(), // 路由工作模式
    routes: routes // 路由规则
})

// 3. 加载路由器
const app = createApp(App)
app.use(router)
app.mount('#app')
```

完成路由配置后，我们回头修改 App.vue 组件。实现路由跳转需要两个标签：`<RouterLink>` 和 `<RouterView>`

其中 `<RouterLink>` 描述跳转到哪个页面，`<RouterView>` 确定路由的出口，即跳转的内容渲染到哪里

App.vue 如下：

```Vue
<script setup lang="ts"></script>

<template>
  <div id="app">
    <h1>Hello App!</h1>
    <p>
      <RouterLink to="/home">首页</RouterLink> <!--字符串跳转-->
      <RouterLink :to="{path: '/about'}">关于</RouterLink> <!--对象跳转-->
      <RouterLink :to="{name: 'news'}">新闻</RouterLink> <!--具名跳转-->
    </p>
    <div class="container">
      <RouterView /> <!--路由出口-->
    </div>
  </div>
</template>

<style scoped>
  a {
    margin-right: 10px;
    color: green;
  }
  .container {
    background-color: yellowgreen;
    widows: 10%;
    height: 400px;
  }
</style>
```

上面代码中的 RouterLink 分别通过三种跳转方法实现路由跳转，其中对象跳转和具名跳转通过 json 形式包裹对象

RouterLink 中有一个属性 replace，使用方法如下 `<RouterLink replace :to="{name: 'news'}">新闻</RouterLink>`

加上 replace 属性后，浏览器访问时不能回退

<!-- <h2>8. Pinia 集中式状态存储</h2>

作用：集中存储数据，让所有 Vue 界面共享

引入插件：`npm install pinia`

和 router 类似，使用方法如下:

```TypeScript
import { createPinia } from 'pinia'
const pinia = createPinia()
const app = createApp(App)
app.use(pinia)
app.mount('#app')
```

使用 pinia 需要创建 Store。Store 类似于 MySQL 里的库，用来保存数据。Store 有三个概念：state, getter, action

- state：数据
- getter：获取并返回数据
- action：组织业务逻辑 -->