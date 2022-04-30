---
title: Vue学习笔记(三)
tags: Vue
categories: 前端
description: Vue第三部分学习笔记，主要介绍了在实际项目中怎么使用Vue进行开发
---

## 5、Vue开发

### 5.1、Vue-cli安装

#### 5.1.1、功能

- 统一目录结构
- 本地调试
- 热部署
- 单元测试
- 集成打包上线

#### 5.1.2、环境搭建

- 下载node.js
  - `node -v`确定是否安装成功
  - `npm -v`确定是否安装成功
  - `npm install cnpm -g`安装node.js淘宝镜像加速器
- 安装vue-cli
  - `cnpm install vue-cli -g`安装
  - `vue list`测试是否安装成功

#### 5.1.3、第一个vue-cli程序

- 进入到一个对应目录，无需新建文件夹
- 使用`vue init webpack myvue`初始化一个项目，无需选太多东西
- 使用`cd myvue`进入文件夹
- 使用`npm install`安装项目所需依赖
- 使用`npm run dev`启动当前项目
- 注：使用管理员打开idea，可以在idea终端运行上面的命令

### 5.2、webpack

#### 5.2.1、概述

- 现代化的JavaScript应用程序的静态模块打包器

#### 5.2.2、CommonsJS

- 运行模块通过`require`方法来同步加载所需依赖的其他模块
- 通过`exports`或`module.exports`来导出需要暴露的接口

#### 5.2.3、安装webpack

- 使用`npm install webpack -g `安装
- 使用`npm install webpack-cli -g `安装客户端

#### 5.2.4、使用webpack

- 创建一个项目

- 创建一个`modules`的文件夹，存放js模块等资源文件

- 在`modules`下创建模块文件，比如`hello.js`，用于编写js模块相关代码

  hello.js：

  ```javascript
  //暴露一个方法
  exports.sayHi = function () {
      document.write("<h1>ferron</h1>")
  };
  
  ```

  main.js：

  ```javascript
  var hello = require("./hello");
  
  hello.sayHi();
  ```

- 编写webpack配置文件`webpack.config.js`

  ```javascript
  module.exports = {
      entry: './modules/main.js',
      output: {
          filename: "./js/bundle.js"
      }
  };
  ```

- 使用命令`webpack`进行打包

- 打包成功后，成功将ES6规范的代码打包成ES5规范浏览器可识别的代码，在html页面中引入即可

### 5.3、Vue-router路由

#### 5.3.1、功能

- 嵌套的路由/视图表
- 模块化的、基于组件的路由配置
- 路由参数、查询、通配符
- 基于Vue.js过渡系统的视图过渡效果
- 细粒度的导航控制
- 带有自动激活的CSS class的链接
- HTML5历史模式或hash模式，在IE9中自动降级
- 自定义的滚动条行为

#### 5.3.2、使用

- 使用`npm install vue-router --save-dev`安装vue-router并且加入到库中

- 在`main.js`中导入vue-router

  ```javascript
  import VueRouter from 'vue-router'
  ```

- 删除一些没有用的东西，变成一个干净的项目

- 定义两个组件

  Content.vue

  ```vue
  <template>
    <h1>内容页</h1>
  </template>
  
  <script>
      export default {
          name: "content"
      }
  </script>
  
  <style scoped>
  </style>
  ```

  Main.vue

  ```vue
  <template>
      <h1>首页</h1>
  </template>
  <script>
      export default {
          name: "Main"
      }
  </script>
  
  <style scoped>
  
  </style>
  ```

- 安装路由，在src目录下新建一个router文件夹，在文件夹下新建一个index.js配置路由信息

  ```javascript
  import Vue from 'vue'
  import VueRouter from "vue-router";
  
  import Content from '../components/Content'
  import Main from "../components/Main";
  
  
  //安装路由
  Vue.use(VueRouter)
  
  //配置导出路由
  export default new VueRouter({
    routes: [
      {
        //路由路径
        path: '/main',
        name: 'Main',
        //跳转的组件
        component: Main
      },
      {
        //路由路径
        path: '/content',
        name: 'Content',
        //跳转的组件
        component: Content
      }
    ]
  })
  ```

- 在`main.js`中配置路由

  ```javascript
  import Vue from 'vue'
  import App from './App'
  import router from './router'  //自动扫描路由配置
  
  Vue.config.productionTip = false;
  
  new Vue({
    el: '#app',
    //配置路由
    router,
    components: { App },
    template: '<App/>'
  })
  ```

- 在`App.vue`中测试路由

  ```vue
  <template>
    <div id="app">
      <router-link to="/main">首页</router-link>
      <router-link to="/content">内容页</router-link>
      <!--展示视图-->
      <router-view></router-view>
    </div>
  </template>
  ```

### 5.4、Vue+ElementUI

- 使用创建一个新项目，创建完之后删除掉一些不需要的文件

  ```xshell
  #新建工程
  vue init webpack hello-vue
  #进入工程目录
  cd hello-vue
  #安装vue-router
  npm install vue-router --save-dev
  #安装element-ui
  npm i element-ui -S
  #安装依赖
  npm install
  #安装SASS加载器
  cnpm install sass-loader node-sass --save-dev
  #启动测试
  npm run dev
  ```

  - `npm install moduleName`：安装模块到项目目录下
  - `npm install -g moduleName`：安装模块到全局，具体安装到哪在`npm config prefix`中查看
  - `npm install -save moduleName`：--save的意思是将模块安装到项目目录下，并在package文件的dependencies节点写入依赖，-S为该命令的缩写
  - `npm install -save-dev moduleName`：-save-dev的意思是将模块安装到项目目录下，并在package文件的dependencies节点写入依赖，-D为该命令的缩写

- 完善目录结构

  - views：存放视图型组件，components则用来存在功能型组件
  - router

- 编写一个Main.vue和Login.vue

  Main.vue

  ```vue
  <template>
      <h1>首页</h1>
  </template>
  
  <script>
      export default {
          name: "Main"
      }
  </script>
  
  <style scoped>
  
  </style>
  ```

  Login.vue

  ```vue
  <template>
    <div>
      <el-card class="box-card">
        <el-form ref="loginForm" :model="form" :rules="rules" label-width="80px" class="login-box">
          <h3 class="login-title">欢迎登录</h3>
          <el-form-item label="账号" prop="username">
            <el-input type="text" placeholder="请输入账号" v-model="form.username"/>
          </el-form-item>
          <el-form-item label="密码" prop="password">
            <el-input type="password" placeholder="请输入密码" v-model="form.password"/>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" v-on:click="onSubmit('loginForm')">登录</el-button>
          </el-form-item>
        </el-form>
      </el-card>
  
  
      <el-dialog
        title="温馨提示"
        :visible.sync="dialogVisible"
        width="30%"
        :before-close="handleClose">
        <span>请输入账号和密码</span>
        <span slot="footer" class="dialog-footer">
          <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
        </span>
      </el-dialog>
    </div>
  </template>
  
  <script>
    export  default {
      name:"Login",
      data(){
        return {
          form:{
            username: '',
            password: ''
          },
          //表单验证，需要再el-form-item 元素中增加prop属性
          rules:{
            username:[
              {required:true,message:'账号不能为空',trigger:'blur'}
            ],
        password:[
        {required: true,message: '密码不能为空',trigger:'blur'}
            ]
      },
        //对话框显示和隐藏
        dialogVisible:false
      }
      },
      methods:{
        onSubmit(formName) {
          //为表单绑定验证功能
          this.$refs[formName].validate((valid) =>{
            if (valid){
              //使用 vue-router路由到指定页面，该方式称之为编程式导航
              this.$router.push("/main");
            } else {
              this.dialogVisible = true;
              return false;
            }
          });
        },
        handleClose: function () {
          console.log("Handle Close， 空函数")
        }
      }
    }
  </script>
  
  <style lang="scss" scoped>
    .box-card{
      width:480px;
      margin:auto;
    }
    .login-box{
      border: 1px solid #DCDFE6;
      width: 350px;
      margin:180px auto;
      padding:35px 35px 15px 35px;
      border-radius: 5px;
      -webkit-border-radius: 5px;
      -moz-border-radius: 5px;
      box-shadow:0 0 25px #909399;
    }
  
    .login-title{
      text-align:center;
      margin:0 auto 40px auto;
      color:#303133;
    }
  </style>
  ```

- 编写路由配置`index.js`

  ```javascript
  import Vue from 'vue'
  import Router from 'vue-router'
  
  Vue.use(Router)
  
  import Main from "../views/Main";
  import Login from "../views/Login";
  
  export default new Router({
    routes: [
      {
        path:'/main',
        component: Main
      },{
        path:'/login',
        component: Login
      }
    ]
    
  })
  ```

- 编写主配置`main.js`

  ```javascript
  import Vue from 'vue'
  import App from './App'
  import router from './router';
  
  //导入element-ui
  import Element from 'element-ui'
  import 'element-ui/lib/theme-chalk/index.css';
  
  
  //使用路由和Elemnet
  Vue.use(router)
  Vue.use(Element)
  
  Vue.config.productionTip = false
  
  new Vue({
    el: '#app',
    //绑定
    router,
    render: h => h(App)
  })
  
  ```

### 5.5、嵌套路由

- 在`views`文件夹下新建一个`user`文件夹，在`user`文件夹中创建两个组件

- 在路由配置中添加子路由配置

  ```javascript
  export default new Router({
    routes: [
      {
        path:'/main',
        component: Main,
        //嵌套路由
        children: [
          {path: '/user/profile',component: UserProfile},
          {path: '/user/list',component: UserList},
        ]
      },{
        path:'/login',
        component: Login,
      }
    ]
  })
  ```

- 编写`Main.vue`，将子路由展示出来

  ```vue
  <template>
    <div>
      <el-container>
        <el-aside width="200px">
          <el-menu :default-openeds="['1']">
            <el-submenu index="1">
              <template slot="title"><i class="el-icon-caret-right"></i>用户管理</template>
              <el-menu-item-group>
                <el-menu-item index="1-1">
                  <router-link to="/user/profile">个人信息</router-link>
                </el-menu-item>
                <el-menu-item index="1-2">
                  <router-link to="/user/list">用户列表</router-link>
                </el-menu-item>
              </el-menu-item-group>
            </el-submenu>
            <el-submenu index="2">
              <template slot="title"><i class="el-icon-caret-right"></i>内容管理</template>
              <e1-menu-item-group>
                <el-menu-item index="2-1">分类管理</el-menu-item>
                <el-menu-item index="2-2">内容列表</el-menu-item>
              </e1-menu-item-group>
            </el-submenu>
          </el-menu>
        </el-aside>
        <el-container>
          <el-header style="text-align: right; font-size: 12px">
            <el-dropdown>
              <i class="el-icon-setting" style="margin-right:15px"></i>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item>个人信息</el-dropdown-item>
                <el-dropdown-item>退出登录</el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </el-header>
  
          <el-main>
            <router-view/>
          </el-main>
  
        </el-container>
      </el-container>
    </div>
  </template>
  
  <script>
    export default {
      name: "Main"
    }
  </script>
  
  <style scoped lang="scss">
    .el-header {
      background-color: #048bd1;
      color: #333;
      line-height: 60px;
    }
  
    .el-aside {
      color: #333;
    }
  </style>
  ```

### 5.6、参数传递及重定向

#### 5.6.1、参数传递

- 在`router-link`节点中增加参数节点，注意要使用名字

  ```vue
  <router-link :to="{name: 'UserProfile',params:{id: 1}}">个人信息</router-link>
  ```

- 更改路由定义

  ```javascript
  {path: '/user/profile/:id',name:'UserProfile',component: UserProfile,props: true}
  ```

- 在该组件中增加`props`属性

  ```vue
  export default {
          props: ['id'],
          name: "Profile"
      }
  ```

- 前端直接取出即可

#### 5.6.2、转发及重定向

转发：`<router-link></router-link>`

重定向：重新定义一个路由`{path: '/goHome',redirect: '/main'}`，转发`/goHome`便会重定向到`/main`

### 5.7、路由模式和404

#### 5.7.1、路由模式

```javascript
export default new Router({
  //加上'history'便不会显示#
  mode: 'history',
  routes: [
    {
      path:'/main',
      component: Main,
      //嵌套路由
      children: [
        {path: '/user/profile/:id',name:'UserProfile',component: UserProfile,props: true},
        {path: '/user/list',component: UserList},
      ]
    },{
      path:'/login',
      component: Login,
    },{
      path: '/goHome',
      redirect: '/main',
    }
  ]

})
```

#### 5.7.2、404

- 编写一个404组件

- 将这个组件注册到路由中

  ```javascript
  {
      path: '*',
     	component: NotFound
  },
  ```

### 5.8、路由钩子

`beforeRouteEnter`：进入路由之前执行

`beforeRouteLeave`：进入路由之后执行

```vue
export default {
	props: ['id'],
	name: "Profile",
	beforeRouteEnter:(to,from,next)=>{
		console.log("进入路由之前")
		next();
	},
	beforeRouteLeave:(to,from,next)=>{
		console.log("进入路由之后")
		next();
	},
}
```

- 安装axios：

  ```xshell
  npm install axios -s
  npm install --save vue-axios
  ```

- 导入axios

- 使用

  ```javascript
  export default {
          props: ['id'],
          name: "Profile",
          beforeRouteEnter:(to,from,next)=>{
            console.log("进入路由之前")
            next(vm =>{
              vm.getData();  //进入路由之前执行getData
            });
          },
          beforeRouteLeave:(to,from,next)=>{
            console.log("进入路由之后")
            next();
          },
          methods: {
            getData: function () {
              this.axios({
                method: 'get',
                url: 'http://localhost:8080/static/mock/data.json'
              }).then(function (response) {
                  console.log(response)
              });
            }
          }
      }
  ```

  
