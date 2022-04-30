---
title: Vue学习笔记(二)
tags: Vue
categories: 前端
description: Vue第二部分学习笔记，主要介绍了Vue的学习过程，包括Vue语法、事件绑定、组件等内容
---

## 4、Vue学习

### 4.1、Vue语法

我们看到的v-bind等被称为指令，前缀带有v-

数组：[]

对象：{}

#### 4.1.1、v-if，v-else-if,  v-else

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

#### 4.1.2、v-for

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

### 4.2、事件绑定

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

### 4.3、双向绑定

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

### 4.4、组件

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

### 4.5、网络通信：axios

#### 4.5.1、特点

- 实现AJAX异步通信
- 从浏览器创建`XMLHttpRequests`
- 从node.js创建http请求
- 支持Promise API【JS中链式编程】
- 拦截请求和响应
- 转换请求数据和相应数据
- 取消请求
- 自动转换json数据
- 客户端支持防御XSRF【跨站请求伪造】

#### 4.5.2、第一个Axios应用程序

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

### 4.6、计算属性

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

### 4.7、插槽slot

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

### 4.8、自定义事件内容分发

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

![image-20210215170132050](http://fl.ljuuu.com/image-20210215170132050.png)

