---
title: Vue学习笔记(一)
tags: Vue
categories: 前端
description: Vue第一部分学习笔记，主要介绍了Vue的核心要素和内容
---

## 1、核心要素

1. 逻辑
   - 判断
   - 循环
2. 事件
   - 浏览器事件：window  document
   - Dom事件：增删改遍历
   - 框架：jQuery、Vue.js...
3. 视图
   - html
   - css：难点
   - 框架：bootStrap...
4. 通信
   - xhr
   - ajax
   - axios(Vue)

全栈工程师：

- 后台开发：
- 前端：html、css、js、jQuery；
- 运维：项目发布；服务器运行项目？Linux；

## 2、Vue详解

### 2.1、Vue概述

- Vue是一套用于构建用户界面的渐进式JavaScript框架(渐进性：逐步实现新特性)
- Vue被设计为可以自底向上逐层应用
- Vue的核心库只关注视图层(HTML+CSS+JS)，方便与第三方库或既有项目整合
- MVVM+虚拟Dom
- 网络通信：axios
- 页面跳转：vue-router
- 项目管理：vuex

### 2.2、CSS预处理器

- 用一种专门的编程语言，进行Web页面样式设计，再通过编译器转化为正常的CSS文件，以供项目使用
- 常见：LESS(基于Node.js)

### 2.3、前端MVVM

- M：模型
- V：视图
- VM(C)：视图模型，前后双向绑定

### 2.4、UI框架

- Ant-Design基于React的UI框架
- **Element**、iview、ice
- Bootstrap
- AmazeUI

### 2.5、JavaScript

- Babel：JS编译工具
- **WebPack**：模块打包器

### 2.6、Node.js

- Express：Node.js框架
- NPM：项目综合管理工具，类似于Maven
- YARN:NPM的替代方案

## 3、第一个Vue程序

注意：使用IDEA开发

- 新建一个空项目，在setting的插件下载安装vue.js

- 导入vue.js

  ```html
  <script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
  ```

- 新建一个div标签

  ```html
  <!--View层：模板-->
  <div id="app">
  
  </div>
  ```

- 新建一个vm对象

  ```html
  <script>
      var vm = new Vue({
        	//绑定的id
          el:"#app",
          //Model：数据
          data:{
              message: "hello,vue!"
          }
      });
  
  </script>
  ```

- 把数据从(使用双括号)div标签中取出(**数据双向绑定**)

  ```html
  <div id="app">
      {{message}}
  </div>
  ```

为什么要使用MVVM：

- 低耦合：View可以独立于Model变化和修改，一个ViewModel可以绑定到不同的View上，当View变化的时候Model可以不变，当Model变化的时候View也可以不变
- 可复用：可以把一个视图逻辑放到一个ViewModel里面，让很多View复用
- 独立开发：专注于ViewModel和页面的分开开发
- 可测试