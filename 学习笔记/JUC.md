## JUC

- java.util.concurrent
- java.util.concurrent.atomic
- java.util.concurrent.locaks

### 1、回顾

- 进程与线程
- Java真的可以开线程嘛？Java线程的`start()`方法默认调用一个`start0()`方法，该方法是一个被`native`关键字修饰的方法，它会调用本地用C++的方法开启线程
- 并发和并行
  - 并发：多个线程操作同一个资源
  - 并行：CPU多核，多条线程可以一起执行，使用线程池
  - 获取CPU的核数`Runtime.getRuntime().availableProcessors()`
  - 并发编程的本质：**充分利用CPU的资源**
- 线程状态：
  - 创建
  - 运行
  - 阻塞
  - 等待
  - 超时等待
  - 终止
- wait/slepp区别
  - wait是来自Object类，sleep来自thread类，一般休眠使用`java.util.concurrent.TimeUnit`实现
  - 锁的释放：wait会释放锁，sleep会抱着锁睡觉
  - 使用范围不同：wait必须在同步代码块中，sleep可以在任何地方使用
  - 是否需要捕获异常：wait不需要捕获异常(**除了中断异常**)，sleep需要捕获异常
  - 

### 2、Lock锁

- Synchronized：本质是排列和锁，加在方法前，可以保证同步
- Lock锁：
  - 使用Lock锁：new锁，在业务代码中加锁，解锁
  - 公平锁和非公平锁：默认使用非公平锁，3h 3s
- 区别：
  - Synchronized是关键字，Lock是一个类
  - Synchronized无法判断获取锁的状态，Lock可以判断是否获取了锁
  - Synchronized会自动释放锁，Lock必须手动释放锁，否则会出现**死锁**
  - Synchronized当线程1阻塞时，线程2会一直等待，而Lock不一定会一直等待
  - Synchronized可重入锁，不可以中断，非公平；Lock是一个类，可以自己设置
  - Synchronized适合锁少量的代码同步问题，Lock适合锁大量的同步代码
- 