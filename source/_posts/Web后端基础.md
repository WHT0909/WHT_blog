---
title: Web后端基础
date: 2025-12-10 11:32:31
tags:
    - 网站开发
    - 后端
categories:
    - 后端
    - Web基础
---

黑马程序员网课 Web 基础学习笔记
<!-- more -->

<h1>1. 资源和架构</h1>

静态资源：服务器上存储的，不会改变的资源

动态资源：随用户请求的变化而变化的资源

B/S 架构：浏览器/服务器架构，如京东、12306等，应用逻辑和数据存放在服务器

C/S 架构：客户端/服务器架构，如 QQ 等，应用逻辑和数据存放在本地

<h1>2. Spring 框架</h1>

Spring：最流行的 Java 框架，其项目涵盖了各个应用场景

SpringBoot：快速开发 Web 项目，简化开发，提高效率

<h1>3. SpringBoot 入门</h1>

<strong>需求：基于SpringBoot的方式开发一个web应用，浏览器发起请求/hello后，给浏览器返回字符串 "Hello xxx ~"</strong>

创建好的 SpringBoot 项目结构如下：

<img src="/img/SpringBoot项目结构.png" alt="SpringBoot项目结构" style="width:400px; margin: 0 auto; display: block">

这里已经删掉了不需要的项目结构，只保留了核心：src 文件夹和 pom.xml

`SpringbootWebQuickstartApplication`是项目的启动类 / 引导类，其内容如下：

```Java
package com.itheima;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication // 启动类的标识
public class SpringbootWebQuickstartApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootWebQuickstartApplication.class, args);
    }

}
```

里面是一个 main 函数，需要启动项目时直接运行这个 main 函数即可

resources/static 用于存放静态页面（html, css, JS）

application.properties 是项目的核心配置文件

接下来编写请求处理类 HelloController，请求处理类一般以 Controller 结尾：

```Java
package com.itheima;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController // 表示这是一个请求处理类
public class HelloController {
    @RequestMapping("/hello") // 指向请求路径
    public String hello(String name){
        System.out.println("name" + name);
        return "Hello" + name + "~";
    }
}
```

注解：
- `@RestController`：当使用RestController注解一个类时，Spring会将该类视为控制器（Controller），并处理传入的HTTP请求
- `@RequestMapping(value = "/{id}", method = RequestMethod.GET)`：将 HTTP 请求映射到控制器方法