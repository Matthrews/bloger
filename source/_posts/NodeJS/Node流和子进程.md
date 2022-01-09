---
date: 2021-02-10

title: Node流和子进程

author: matthew

category: NodeJS

keywords: 流 子进程
---

### 核心一：流 Stream

####流

- 非阻塞式的数据处理方式可提升效率
- chunk 数据分块可以节省内存
- 管道可提高扩展性，方便组合

#### 管道

- pipe
- 两种方式实现 pipe

#### Stream 对象的原型链

- fs.createReadStream
- 自身属性 由 fs.ReadStream 构造
- 原型：stream.Readable.prototype
- 二级原型：stream.Stream.prototype
- 三级原型：events.EventEmitter.prototype
- 四级原型：Object.prototype
- 结论：Stream 对象都继承了 EventEmitter

#### Stream 分类

- Readable Stream
- Writable Stream
- Duplex Stream
- Transform Stream

#### 如何自定义 Stream

### 核心二：子进程 child_process

#### 什么是进程

- 进程是程序的执行实例
- 程序在 CPU 上执行时的活动叫做进程
- 一个进程可以创建另一个进程（父进程与子进程）
- 通过任务管理器可以看到进程

#### 了解 CPU

- 特点：一个单核 CPU，在一个时刻，只能做一件事
- 那如何让他用户用时看电影，听音乐，写代码呢？答案是在不同的进程中快速切换
- 【多程序并发执行】是指多个程序在宏观上并行，微观上串行
- 每个进程会出现【执行-暂停-执行】的规律，多个进程之间会出现抢资源的现象

#### 什么是阻塞进程

- 等待执行的进程队列中，都是非运行态的，一些在等待 CPU 资源，另一些 B 在等待 I/O 完成，
  此时把 CPU 分配给 B 进程，B 还是在等 I/O，我们把这个 B 叫做阻塞进程，
  因此，分派程序只会把 CPU 分配给非阻塞进程
- [进程状态](https://blog.csdn.net/Shangxingya/article/details/113799269)

#### 什么是线程

##### 线程引入原因

- 进程是执行的基本实体，也是资源分配的基本实体
- 导致进程的创建，切换，和销毁太耗时间
- 于是引入线程，线程作为执行的基本实体
- 而进程只作为资源分配的基本实体

##### 线程 Thread

- CPU 调度和执行的最小单元
- 一个进程中至少有一个线程，可以有多个线程，同时一个线程只能属于一个进程
- 一个进程中的所有线程共享该进程的所有资源
- 进程的第一个线程叫做初始化线程
- 线程的调度可以由操作系统负责，也可以由用户自己负责
- 举例：浏览器进程里面有渲染引擎，V8 引擎，存储模块，网络模块，用户界面模块等，每一个模块都可以放在一个线程里面

#### Node.js 的进程控制

- 使用目的：子进程运行结果存储在系统缓存中（最大 200KB），等到子进程运行结束后，主进程再用回调函数读取子进程运行结果
- API
  - exec
  - 流式
  - Promise
  - 为防止 cmd 注入，推荐使用 execFile
  - execFile
  - spawn
    - 用法和 execFile 类似
    - 没有回调函数，只能通过留流事件获取结果
    - 没有最大 200KB 限制，因为是流式的
  - fork
    - 创建一个子进程，执行 Node 脚本
    - fork('./child.js')相当于 spawn('node', ['./child.js'])
    - 特点 1：会多出一个 message 事件，用于父子通信
    - 特点 2：会多出一个 send 方法

#### Node.js 的线程控制

- 加入较迟，效率不如内置 I/O 操作
- API
  - isMainThread
  - new Worker(filename)
  - parentPort
  - postMessage
  - message
  - exit
