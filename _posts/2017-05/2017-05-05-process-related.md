---
layout: post
title: "进程 线程 协程 管程 纤程 概念对比理解"
categories: [Web开发,前端开发]
tags: [进程,线程,协程,管程,纤程]
---

### 引言
不知道是不是我自己本身就有那么一丝丝的密集恐惧，把这么一大堆看起来很相似很相关的概念放在一起，开起来是有点麻，捋一捋感觉舒服多了。


### 相关概念

#### 任务、作业（Job，Task，Schedule）

在进程的概念出现之前，进程有着这样的称谓。



#### 进程

为了使多个程序能够并发（同一时刻只有一个在运行，但感觉起来像多个同时运行；并行（同一时刻真的多个在运行，不是感觉像多个））的执行，操作系统需要一个结构来抽象和表示这个程序的运行。

+ 进程是操作系统对一个正在运行的程序的一种抽象结构。
+ 进程是指在操作系统中能独立运行并作为资源分配的基本单位，由一组机器指令、数据和堆栈等组成的能独立运行的活动实体。
+ 操作系统可以同时运行多个进程，多个进程直接可以并发执行和交换信息。
+ 进程在运行是需要一定的资源，如CPU、存储空间和I/O设备等。



#### 线程

进程是资源分配的基本单位，进程的调度涉及到的内容比较多（存储空间，CPU，I/O资源等，进程现场保护），调度开销较大，在并发的切换过程效率较低。为了更高效的进行调度，提出了比进程更轻量的独立运行和调度的基本单位。

+ 线程比进程更轻量
+ 线程能独立运行，独立调度，拥有资源（一般是CPU资源，程序计数器等）
+ 线程调度能大幅度减小调度的成本（相对于进程来说），线程的切换不会引起进程的切换
+ 线程的引入进一步提高了操作系统的并发性，线程能并发执行
+ 同一个进程的多个线程共享进程的资源（省去了资源调度现场保护的很多工作）



#### 协程、共行程序、Coroutine

+ 协程是用户模式下的轻量级线程，操作系统内核对协程一无所知
+ 协程的调度完全有应用程序来控制，操作系统不管这部分的调度
+ 一个线程可以包含一个或多个协程
+ 协程拥有自己的寄存器上下文和栈，协程调度切换时，将寄存器上细纹和栈保存起来，在切换回来时恢复先前保运的寄存上下文和栈
+ 协程能保留上一次调用时的状态，看到这里各种生成器（生成器是被阉割的协程）的概念浮现出来了。。
+ Windows下的实现叫纤程



####  纤程

代码易移植性一直是平台间交互考虑的重点，在将引用程序从Unix移植到Windows的过程中，会存在一些类似于线程栈管理的不一致、结构和异常处理等问题，增加移植难度和成本。

为了帮助各公司更快、更正确地将他们的代码移植到Windows，Microsoft 在操作系统中增加了纤程（Fiber）。纤程与纤程对比，有如下的特性：

+ 线程是在Windows内核中实现的，操作系统会根据系统的调度算法对纤程程进行调度。
+ 纤程是在用户模式下实现的，内核对纤程一无所知。
+ 纤程是更轻量级的线程，一个线程可以包含一个或多个纤程
+ 内核会对纤程进行抢占式调度，线程一次只能执行一个纤程的代码（具体执行哪一个纤程由用户调度算法决定）
+ 纤程的调度与线程的调度没有直接关系，操作系统随时可能会夺取纤程所在线程的运行权
+ 除非正在运行的纤程显式的切换到另一个纤程，否则其他纤程将无法运行
+ Windows有一套API来讲线程转换为纤程或者在同一个线程里面创建多个纤程

---

#### 管程

把管程放最后还加了一道分割线原因是管程跟上面的几个概念不是同一类东东，虽然长得很像，就像Car和Bar一样。

临界资源的概念：

+ 一次只允许一个进程访问的资源
+ 多个进程只能互斥访问的资源

临界资源的访问需要同步操作，比如信号量就是一种方便有效的进程同步机制。但信号量的方式要求每个访问临界资源的进程都具有wait和signal操作。这样使大量的同步操作分散在各个进程中，不仅给系统管理带来了麻烦，而且会因同步操作的使用不当导致死锁。管程就是为了解决这样的问题而产生的。

操作系统中管理的各种软件和硬件资源，均可用数据结构抽象地描述其资源特性，即用少量信息和对该资源所执行的操作来表征该资源，而忽略它们的内部结构和实现细节。利用共享数据结构抽象地表示系统中的共享资源。而把对该共享数据结构实施的操作定义为一组过程，如资源的请求和释放过程request和release。进程对共享资源的申请、释放和其他操作，都是通过这组过程对共享数据结构的操作来实现的，这组过程还可以根据资源的情况接受或阻塞进程的访问，确保每次仅有一个进程使用该共享资源，这样就可以统一管理对共享资源的所有访问，实现临界资源互斥访问。

管程就是代表共享资源的数据结构以及由对该共享数据结构实施操作的一组过程所组成的资源管理程序共同构成的一个操作系统的资源管理模块。管程被请求和释放临界资源的进程所调用。

管程定义了一个数据结构和能为并发进程所执行（在该数据结构上）的一组操作，这组操作能同步进程和改变管程中的数据。

#### 超线程

这个也是一个跟上面的概念不是一类事的概念，超线程是Intel CPU设计上的一种技术。

一个CPU物理核在同一时间只能执行一个线程，而线程的切换会消耗掉上万的始终周期，效率还不够高。超线程就是在实现同一个CPU物理核在同一时间能几乎执行两个线程的技术。这就是我们在Intel CPU的机子上的任务管理器中经常看到double的CPU物理核心的缘由。



### 参考资料

1. 《深入理解计算机系统》 第二版
2. 《Windows 核心编程》 第五版
3. 《计算机操作系统》 第三版
4. 《操作系统概念》 第七版
5. 《计算机程序设计艺术》 卷一
6.  Coroutine 维基百科