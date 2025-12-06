---
title: Vue学习笔记
date: 2025-12-02 11:51:33
tags:
    - 网站开发
    - 前端
categories:
    - 前端
    - Vue
---

黑马程序员网课 Vue 学习笔记
<!-- more -->

<div class="catalog">
    <h3>目录</h3>
    <a href="#chapter1">1. 定义应用实例</a><br>
    <a href="#chapter2">2. Vue 常用指令</a><br>
    <a href="#chapter2.1" style="margin-left: 2em;">2.1 v-for</a><br>
    <a href="#chapter2.2" style="margin-left: 2em;">2.2 v-bind</a><br>
    <a href="#chapter2.3" style="margin-left: 2em;">2.3 v-if v-show</a><br>
    <a href="#chapter2.4" style="margin-left: 2em;">2.4 v-model</a><br>
    <a href="#chapter2.5" style="margin-left: 2em;">2.5 v-on</a><br>
</div>

<h1 id="chapter1">1. 定义应用实例</h1>

```html
<body>
    <div id="app">
        <h1>{{message}}</h1> // 插值表达式，访问数据对象中的变量
    </div>
    <script type="module"> // module表示模块化 JS 脚本，支持 import / export，这是能引入 Vue 库的前提
        import {createApp} from 'https://unpkg.com/vue@3/dist/vue.esm-browser.js' // 导入核心方法，详见 Vue 官方文档 https://cn.vuejs.org/guide/quick-start.html

        // 创建应用实例
        createApp({
            data(){ // 定义数据对象
                return {
                    message: "Hello Vue"
                };
            }
        }).mount("#app") // 将 Vue 应用实例挂载到指定的 DOM 元素上
    </script>
</body>
```

createApp 创建了一个应用实例，并通过 mount 方法将该实例挂在到 id="app" 的 DOM 元素上，在上面的代码中即为 `<div id="app">`

挂载后，#app 元素内部的内容会被 Vue 模板替换（如果有原始内容会被覆盖）

<h1 id="chapter2">2. Vue 常用指令</h1>

<h2 id="chapter2.1">2.1 v-for</h2>

用途：循环渲染，要渲染哪个标签就把 v-for 写在该标签里

```html
<table>
    <thead>
        ...
    </thead>
    <tbody>
        <tr v-for="(e, index) in empList" :key="e.id">
            <td>{{index + 1}}</td>
            <td>{{e.name}}</td>
            <td>{{e.gender == 1 ? '男' : '女'}}</td>
            <td>{{e.job}}</td>
            <td>{{e.entrydate}}</td>
            <td>{{e.updatetime}}</td>
            <td class="action-buttons">
                <button type="button">编辑</button>
                <button type="button">删除</button>
            </td>
        </tr>
    </tbody>
</table>
```

e 是列表 empList 里的元素，empList 定义如下：
```bash
createApp({
    data(){
        return{
            empList: [ // 这里仿照了 JSON 文件的写法，模拟浏览器的数据传输
                {
                    "id": 1,
                    "name": "谢逊",
                    "gender": 1,
                    "job": 1,
                    "entrydate": "2023-06-09",
                    "updatetime": "2025-12-01T14:59:38"
                },
                {
                    "id": 2,
                    "name": "谢逊",
                    "gender": 1,
                    "job": 1,
                    "entrydate": "2023-06-09",
                    "updatetime": "2025-12-01T14:59:38"
                },
                ...
            ]
        }
    }
}).mount("#container")
```
<strong>注意:</strong>

Vue 实例中 data 数据对象的定义方式为：`对象名: {..}`

如果数据对象是数组，定义方式为：`对象名: [{..}, {..}]`

:key 一般使用 id 作为唯一的键

<h2 id="chapter2.2">2.2 v-bind</h2>

用途：用于动态绑定资源

```html
<td><img v-bind:src="e.image" :alt="e.name" style="width: 150px;"></td>
```

`v-bind:src` 也可简写为 `:src`，在上述代码中用于动态绑定 image 资源（`:alt` 同理）

注意：在标签内部（如 img 标签`<img src="img1.png" alt="img1">`）使用插值表达式会报错

<h2 id="chapter2.3">2.3 v-if v-show</h2>

用途：都用于控制页面上元素显示与否；用`<span>`标签搭配控制元素

区别：

1. `v-if` 只有条件为 true 才会渲染节点，`v-show`无论条件真假都会渲染节点，但会通过 css 样式控制是否显示该元素

2. `v-show` 适用于频繁切换显示隐藏的场景

```html
<td>
    <!-- v-if 控制-->
    <span v-if="e.job == 1">班主任</span>
    <span v-else-if="e.job == 2">讲师</span>
    <span v-else-if="e.job == 3">学工主管</span>
    <span v-else-if="e.job == 4">教研主管</span>
    <span v-else-if="e.job == 5">咨询师</span>
    <span v-else>其他</span>

    <!-- v-show 控制 -->
    <span v-show="e.job == 1">班主任</span>
    <span v-show="e.job == 2">讲师</span>
    <span v-show="e.job == 3">学工主管</span>
    <span v-show="e.job == 4">教研主管</span>
    <span v-show="e.job == 5">咨询师</span>
</td>
```

<h2 id="chapter2.4">2.4 v-model</h2>

用途：双向绑定表单数据；在绑定前需要先在 data 里创建数据对象

```html
<form action="/search" method="post" class="search-form">
    <label for="name">姓名</label>
    <input type="text" id="name" v-model="searchForm.name" placeholder="请输入姓名"> <!-- 和 searchFrom对象中的 name 成员双向绑定 -->
</form>

createApp({
    data(){
        return {
            searchForm: {
                name: '',
                gender: '',
                job: ''
            }
        }
    }
})
```

<h2 id="chapter2.5">2.5 v-on</h2>

用途：绑定事件；语法为`v-on:事件="方法"`，也可以简写为`@事件="方法"`

```html
<div class="button-group">
    <button type="button" v-on:click="search">提交</button>
    <button type="reset" @click="clear">清除</button>
</div>

createApp({
    data(){
        ...
    }
    methods: {
        search(){
            console.log(this.searchForm) //这里必须用 this 指向当前实例的 data 对象
        }
        clear(){
            this.searchForm = {
                name: '',
                gender: '',
                job: ''
            }
        }
    }
})
```