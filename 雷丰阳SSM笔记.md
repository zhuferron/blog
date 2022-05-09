## 1、JavaWeb复习

#### 1.1、三大组件：Servlet、Filter、Listener

Servlet：处理请求

Filter：过滤请求

Listener：监听请求

三大组件都需要在web.xml中进行注册，除了Listener中的两个(活化钝化监听器，绑定解绑监听器)需要javaBean实现，不注册外，剩下的都要注册。

#### 1.2、Filter

核心原理：实现Filter接口并且重写doFilter(ServletRequest request, ServletResponse response, FilterChain chain)方法

doFilter方法接收到request和response，并对其进行处理，然后放行。

多Filter执行过程：



![image-20220509084353248](D:%5CZFL%5C%E5%8D%9A%E5%AE%A2%5C%E5%9B%BE%E7%89%87%E6%95%B4%E7%90%86%5Cimage-20220509084353248.png)

#### 1.3、Listener

ServletContext：ServletContextListener、ServletContextAttributeListener

ServletRequest：ServletRequestListener、ServletRequestAttributeListener

Session：HttpSessionListener、HttpSessionAttributeListener

前面三个Listener是对三个域的开启和关闭进行监听，后面三个Listener是对这三个域中的属性修改、增加和删除进行监听。前面这六个监听器都要在web.xml中进行配置

HttpSession：HttpSessionBindingListener，这个是需要先创建一个bean对象类让对象类实现HttpSessionBindingListener接口中的绑定和解绑事件，每当session中存入该对象时候HttpSessionBindingListener的绑定事件就会运行，而每当session中的该对象移除时候解绑事件就会运行。

HttpSession：HttpSessionActivationListener，这个也是需要先创建一个bean对象类让对象类实现HttpSessionActivationListener接口中的钝化和活化这两个事件，在实现这个接口同时还得实现Serializable序列化这个接口，这样才能实现钝化后活化。（钝化：就是将session中已经存在的对象从session内存挪到磁盘中，活化：就是将磁盘中的session中的对象挪到内存中，钝化和活化可以运用到网站优化上，当用户长时间登陆网站不进行操作的时候就可以将这个用户的session对象进行钝化）

#### 1.4、JSON

JSON(javascript object notation)：一种轻量级(和xml相比)的数据交换格式

两个重要的方法：

- JSON.stringify():将js对象转为字符串
- JSON.parse()：把字符串转为js对象

#### 1.5、AJAX

AJAX：**A**synchronous **J**avascript **A**nd **X**ML，是一种无刷新的就可以得到服务器数据的技术。