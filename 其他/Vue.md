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

#### 2.1、Vue概述

- Vue是一套用于构建用户界面的渐进式JavaScript框架(渐进性：逐步实现新特性)
- Vue被设计为可以自底向上逐层应用
- Vue的核心库只关注视图层(HTML+CSS+JS)，方便与第三方库或既有项目整合
- MVVM+虚拟Dom
- 网络通信：axios
- 页面跳转：vue-router
- 项目管理：vuex

#### 2.2、CSS预处理器

- 用一种专门的编程语言，进行Web页面样式设计，再通过编译器转化为正常的CSS文件，以供项目使用
- 常见：LESS(基于Node.js)

#### 2.3、前端MVVM

- M：模型
- V：视图
- VM(C)：视图模型，前后双向绑定

#### 2.4、UI框架

- Ant-Design基于React的UI框架
- **Element**、iview、ice
- Bootstrap
- AmazeUI

#### 2.5、JavaScript

- Babel：JS编译工具
- **WebPack**：模块打包器

#### 2.6、Node.js

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

- 把数据从(使用**{{}}**)div标签中取出(**数据双向绑定**)

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

## 4、Vue学习

#### 4.1、Vue语法

我们看到的v-bind等被称为指令，前缀带有v-

数组：[]

对象：{}

##### 4.1.1、v-if，v-else-if,  v-else

```html
<!--View层：模板-->
<div id="app">
    <h1 v-if="type === 'A'">A</h1>
    <h1 v-else-if="type === 'B'">B</h1>
    <h1 v-else-if="type === 'C'">C</h1>
    <h1 v-else>D</h1>
</div>

<!--1.导入vue.js-->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script>
    var vm = new Vue({
        el:"#app",
        data: {
            type: 'A'
        }
    });

</script>
```

##### 4.1.2、v-for

```html
<!--View层：模板-->
<div id="app">
    <li v-for="item in items">
        {{item.message}}
    </li>
</div>


<!--1.导入vue.js-->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script>
    var vm = new Vue({
        el:"#app",
        data:{
            items: [
                {message: 'ferron1'},
                {message: 'ferron2'}
            ]
        }
    });

</script>
```

#### 4.2、事件绑定

- 使用v-on绑定事件
- 事件定义在methods中

```html
<!--View层：模板-->
<div id="app">
    <button v-on:click="sayHi">click me</button>
</div>

<!--1.导入vue.js-->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script>
    var vm = new Vue({
        el:"#app",
        data: {
            message: "ferron"
        },
        //方法必须定义在Vue的methods对象中
        methods: {
            sayHi: function () {
                alert(this.message);
            }
        }
    });

</script>
```

#### 4.3、双向绑定

- 使用v-model指定

- 双向绑定不仅仅是后端传来的数据可以直接得到，前端在input框输入的数据也可以通过v-model来绑定到一个数据中

文本：

```html
<!--View层：模板-->
<div id="app">
    输入的文本：<input type="text" v-model="message">
    {{message}}
</div>


<!--1.导入vue.js-->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script>
    var vm = new Vue({
        el:"#app",
        data: {
            message: '',
        }
    });
</script>
```

单选框：

```html
<!--View层：模板-->
<div id="app">
    性别：
    <input type="radio" name="sex" value="男" v-model="message"> 男
    <input type="radio" name="sex" value="女" v-model="message"> 女

    <p>
        选中了：{{message}}
    </p>

</div>


<!--1.导入vue.js-->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script>
    var vm = new Vue({
        el:"#app",
        data: {
            message: '',
            checked: false
        }
    });
</script>

</body>
</html>
```

下拉框：

```html
<!--View层：模板-->
<div id="app">
    下拉框：
    <select v-model="message">
        <option disabled value="">请选择</option>
        <option>A</option>
        <option>B</option>
        <option>C</option>
    </select>

    <span>{{message}}</span>

</div>


<!--1.导入vue.js-->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script>
    var vm = new Vue({
        el:"#app",
        data: {
            message: ''
        }
    });
</script>
```

注意：如果v-model表达式的初始值未能匹配到任何选项，在ios中可能存在bug

#### 4.4、组件

组件是Vue实例，是一组可复用的模板，通常一个应用会以一颗嵌套的组件树的形式来组织

- 使用`Vue.component`定义一个组件

  ```html
  Vue.component("ferron",{
          template:   '<li>Hello</li>'
      });
  ```

- 拿出组件

  ```html
  <ferron></ferron>
  ```

**涉及props组件之间传参：**

- 使用`Vue.component`定义一个组件

  ```html
  Vue.component("ferron",{
          template:   '<li></li>'
      });
  ```

- 拿出组件

  ```html
  <ferron></ferron>
  ```

- 从`items`遍历取出`item`，然后将取出的`item`绑定到`item`

  ```html
  <ferron v-for="item in items" v-bind:item="item"></ferron>
  ```

- 在组件中使用`props`参数接收`item`，并打印

  ```html
  Vue.component("ferron",{
          props: ['item'],
          template:   '<li>{{item}}</li>'
      });
  ```

#### 4.5、网络通信：axios

##### 4.5.1、特点

- 实现AJAX异步通信
- 从浏览器创建`XMLHttpRequests`
- 从node.js创建http请求
- 支持Promise API【JS中链式编程】
- 拦截请求和响应
- 转换请求数据和相应数据
- 取消请求
- 自动转换json数据
- 客户端支持防御XSRF【跨站请求伪造】

##### 4.5.2、第一个Axios应用程序

- 导入js文件

  ```html
  <script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
  <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
  ```

- 设置好其他模板

  ```html
  <div id="vue">
  
  </div>
  
  <script type="text/javascript">
      var vm = new Vue({
          el:"#vue"
  
  </script>
  
  ```

- 使用钩子函数，来获得数据

  ```html
  mounted(){ //钩子函数  链式编程
              axios
                  .get('../data.json') //得到数据
                  .then(response => (this.info=response.data));  //操作
          },
  ```

- 使用数据返回函数，来返回数据

  ```html
  //data是vm中的属性，data()是方法，返回数据
          data(){
              return{
                  //请求的返回参数格式，必须和json字符串一样
                  info:{
                      name: null,
                      address: {
                          country: null,
                          city: null,
                          street: null
                      },
                      url: null
                  }
              }
          },
  ```

- 前端加载

  ```html
  <div id="vue">
      <div>{{info.name}}</div>
      <div>{{info.address.country}}</div>
      <a v-bind:href="info.url">点击</a>
  </div>
  ```

#### 4.6、计算属性

- 重点在`属性`上
- 能将计算结果缓存起来的属性

- 好处：内存中运行
- 计算属性跟方法的区别
  - 方法调用必须得加上()，而计算属性不需要
  - 方法会时时变化，而计算属性计算之后不会再变，类似缓存
  - 特点：一些重复的计算不需要再进行，避免开销

```html
<!--View层：模板-->
<div id="app">
    <!--调用方法得在后面加(),调用计算属性中不能加(),因为他是一个属性，并且计算出来之后结果不变-->
    <p>currentTime1:{{currentTime1()}}</p>
    <p>currentTime2:{{currentTime2}}</p>
</div>


<!--1.导入vue.js-->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script>
    var vm = new Vue({
        el:"#app",
        data:{
            message: "hello"
        },
        methods:{
            currentTime1: function () {
                return Date.now(); //返回一个时间戳
            }
        },
        computed: {//计算属性:methods和computed中的方法名尽量不要重名，重名只会调用方法
            currentTime2: function () {
                return Date.now(); //返回一个时间戳
            }
        }


    });

</script>
```

#### 4.7、插槽slot

使用`<slot>`元素作为承载分发内容的出口，可以应用在组合组件的场景中

- 定义被插槽插的组件

  ```javascript
  Vue.component("todo",{
          template:
              '<div>'+
                  '<slot></slot>'+
                  '<ul>'+
                      '<slot></slot>'+
                  '</ul>'+
              '</div>'
      })
  ```

- 定义两个插槽

  ```javascript
   Vue.component("todo-title",{
          props: ['title'],
          template: '<div>{{title}}</div>'
      })
  
      Vue.component("todo-items",{
          props: ['item'],
          template: '<li>{{item}}</li>'
      })
  ```

- 在被插槽的组件中绑定每个插槽的名字

  ```javascript
  Vue.component("todo",{
          template:
              '<div>'+
                  '<slot name="todo-title"></slot>'+
                  '<ul>'+
                      '<slot name="todo-items"></slot>'+
                  '</ul>'+
              '</div>'
      })
  ```

- 前端展示

  ```html
  <!--View层：模板-->
  <div id="app">
  
      <todo>
          <todo-title slot="todo-title" :title="title"></todo-title>
          <todo-items slot="todo-items" v-for="item in items" :item="item"></todo-items>
      </todo>
  
  </div>
  ```

#### 4.8、自定义事件内容分发

需求：在上面的例子中，每个图书后面加一个删除按钮，点击按钮，就删除

- 首先加上删除按钮，并且在该组件中加入一个remove方法，测试该方法

  ```javascript
  Vue.component("todo-items",{
          template: '<li>{{item}} <button @click="remove">删除</button></li>',
          methods: {
              remove: function () {
                  alert("111")
              }
          }
      })
  ```

- 在vm中定义一个接收index，删除item的方法

  ```javascript
  methods: {
              removeItems: function (index) {
                  this.items.splice(index,1) //一次只删除一个元素
              }
          }
  ```

- 在for循环中同时遍历出每个item的索引

  ```html
  v-for="(item,index) in items"
  ```

- 同时在该标签中自定义事件，绑定vm中的事件名及参数，同时新定义一个事件名

  ```html
  v-on:remove="removeItems(index)"
  ```

- 在子组件中接收参数，并且使用该自定义事件

  ```javascript
  Vue.component("todo-items",{
          props: ['item','index'],
          template: '<li>{{item}} <button @click="remove">删除</button></li>',
          methods: {
              remove: function (index) {
                  //自定义事件分发(事件名，参数)
                  this.$emit('remove',index);
              }
          }
      })
  ```

![image-20210215170132050](D:\Application\Typora\Typora\image\image-20210215170132050.png)

## 5、Vue开发

#### 5.1、Vue-cli安装

##### 5.1.1、功能

- 统一目录结构
- 本地调试
- 热部署
- 单元测试
- 集成打包上线

##### 5.1.2、环境搭建

- 下载node.js
  - `node -v`确定是否安装成功
  - `npm -v`确定是否安装成功
  - `npm install cnpm -g`安装node.js淘宝镜像加速器
- 安装vue-cli
  - `cnpm install vue-cli -g`安装
  - `vue list`测试是否安装成功

##### 5.1.3、第一个vue-cli程序

- 进入到一个对应目录，无需新建文件夹
- 使用`vue init webpack myvue`初始化一个项目，无需选太多东西
- 使用`cd myvue`进入文件夹
- 使用`npm install`安装项目所需依赖
- 使用`npm run dev`启动当前项目
- 注：使用管理员打开idea，可以在idea终端运行上面的命令

#### 5.2、webpack

##### 5.2.1、概述

- 现代化的JavaScript应用程序的静态模块打包器

##### 5.2.2、CommonsJS

- 运行模块通过`require`方法来同步加载所需依赖的其他模块
- 通过`exports`或`module.exports`来导出需要暴露的接口

##### 5.2.3、安装webpack

- 使用`npm install webpack -g `安装
- 使用`npm install webpack-cli -g `安装客户端

##### 5.2.4、使用webpack

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

#### 5.3、Vue-router路由

##### 5.3.1、功能

- 嵌套的路由/视图表
- 模块化的、基于组件的路由配置
- 路由参数、查询、通配符
- 基于Vue.js过渡系统的视图过渡效果
- 细粒度的导航控制
- 带有自动激活的CSS class的链接
- HTML5历史模式或hash模式，在IE9中自动降级
- 自定义的滚动条行为

##### 5.3.2、使用

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

#### 5.4、Vue+ElementUI

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

#### 5.5、嵌套路由

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

#### 5.6、参数传递及重定向

##### 5.6.1、参数传递

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

##### 5.6.2、转发及重定向

转发：`<router-link></router-link>`

重定向：重新定义一个路由`{path: '/goHome',redirect: '/main'}`，转发`/goHome`便会重定向到`/main`

#### 5.7、路由模式和404

##### 5.7.1、路由模式

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

##### 5.7.2、404

- 编写一个404组件

- 将这个组件注册到路由中

  ```javascript
  {
      path: '*',
     	component: NotFound
  },
  ```

#### 5.8、路由钩子

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

  



