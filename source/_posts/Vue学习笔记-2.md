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

<h2>1. 整体认识 Vue3</h2>

<h3>1.1 创建工程</h3>

```bash
# 创建工程
npm create vue@latest 
# 启动项目
npm install
npm run dev
```

<h3>1.2 项目结构</h3>

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

<h2>2. 理解：数据的双向绑定</h2>

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

<h2>3. OptionsAPI 和 CompositionAPI</h2>

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

<h2>4. Vue3 的数据双向绑定</h2>

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