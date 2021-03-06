---
layout: post
title: 多进程与多线程的乱七八糟的事情
description: 多进程与多线程的一些概念，以及Java与Python中关于此的一点细节问题描述。
keywords: 多进程, 多线程
---




<img src="/images/Seattle.png" alt="Seattle" class="img-center" width="700px" />

####1.进程与线程

在比较早期的资料中进程(process)和任务(task)是指相同的概念。进程这个概念至今还没有公认的统一的定义，这里引用《操作系统教程(第3版)》(陆松年)一书中的定义：进程是程序处于一个执行环境中在一个数据集上的运行过程，它是系统进行资源分配和调度的一个可并发执行的独立单位。进程有两个基本的特征：（1）资源拥有单位（2）调度单位。在早期的计算机发展中，这两个特征是同时体现在进程上的，但随着操作系统的发展，对这两个特征进行了区别对待，因而出现了新的调度和执行的实体单元--线程(thread)，而资源拥有单位仍然是进程。即进程是资源分配的最小单位，线程是CPU调度的最小单位。线程和进程的区别在于，子进程和父进程有不同的代码和数据空间，而多个线程则共享数据空间，每个线程有自己的执行堆栈和程序计数器为其执行上下文。

####2.多进程与多线程

多进程的出现与CPU的核心没有太大关系。早期在单核CPU时代，操作系统也就出现了多用户多任务（多进程），任何时候我们都不希望操作系统仅仅在处理一个任务。那时候多任务采用的解决策略便是CPU时间片的方式让CPU在不同任务之间轮转。近几年，预测每18个月处理器运算速度增加一倍的摩尔定律已经走到了瓶颈时代，可是，人们将摩尔定律衍伸到处理器的核心数量上，预测在未来，硬件性能的提高或许是处理器核心数要明显胜过频率的提升。此时，为了利用多核心的硬件资源，多进程和多线程变得更有意义。在某种程度上可以做到在真正意义上的并行处理。

根据第一部分进程与线程的定义与区别，可以总结多进程与多线程之间的对比：


<table style="border:2px solid #888">
  <tr style="border:2px solid;background:#ddd;text-align:center">
    <td  style="border:2px solid #888">对比项</td>
     <td  style="border:2px solid #888">多进程</td>
     <td  style="border:2px solid #888">多线程</td>
     <td  style="border:2px solid #888">优势对比</td>
  </tr>
  <tr style="border:2px solid #888 ">
     <td  style="border:2px solid #888">数据共享，同步</td>
     <td  style="border:2px solid #888">数据共享复杂，需要IPC；数据是分开的，同步简单　　</td>
     <td  style="border:2px solid #888">共享进程数据，数据共享简单，但是同步复杂　　</td>
     <td  style="border:2px solid #888">－－</td>
  </tr>
  <tr style="border:2px solid #888">
     <td  style="border:2px solid #888">内存，CPU</td>
     <td  style="border:2px solid #888">占内存多，切换复杂，CPU利用率低</td>
     <td  style="border:2px solid #888">占内存少，切换简单，CPU利用率高</td>
     <td  style="border:2px solid #888">线程优势</td>
  </tr>
  <tr style="border:2px solid #888">
     <td  style="border:2px solid #888">创建，销毁，切换　　</td>
     <td  style="border:2px solid #888">复杂，速度慢</td>
     <td  style="border:2px solid #888">简单，速度快</td>
     <td  style="border:2px solid #888">线程优势</td>
  </tr>
  <tr style="border:2px solid #888">
     <td  style="border:2px solid #888">编程，调试</td>
     <td  style="border:2px solid #888">简单</td>
     <td  style="border:2px solid #888">复杂</td>
     <td  style="border:2px solid #888">进程优势</td>
  </tr>
  <tr style="border:2px solid #888">
     <td  style="border:2px solid #888">可靠性</td>
     <td  style="border:2px solid #888">进程间不会相互影响</td>
     <td  style="border:2px solid #888">一个线程崩溃很容易引起整个进程崩溃</td>
     <td  style="border:2px solid #888">进程优势</td>
  </tr>
  <tr style="border:2px solid #888">
     <td  style="border:2px solid #888">分布式</td>
     <td  style="border:2px solid #888">适于多核，多机分布式，可以扩展到多台机器</td>
     <td  style="border:2px solid #888">适于多核</td>
     <td  style="border:2px solid #888">－－</td>
  </tr>
</table>



通过以上表格，我们可以根据业务场景做出多进程与多线程的合理选择。(1)需要频繁创建销毁的优先用线程(2)需要进行大量计算的优先使用线程(3)强相关的处理用线程，弱相关的处理用进程(4)可能扩展到多机分布的用进程，多核分布的用线程。我们可以看几个例子，比如迅雷等多线程下载工具就是典型的多线程。一个下载任务进来，迅雷把文件平均分成10份，然后开10个线程分别下载。这时主界面是一个单独的线程，并不会因为下载文件而卡死，这里这10个线程是共享内存和其他资源的，所以他们可以同时对迅雷打开的这个文件进行读写。要是10个进程就不行了，因为进程之间的数据是相互独立的。再比如，Chrome浏览器就是一个典型的多进程程序，里面的每个标签页，扩展，插件都是单独的进程，各自独占资源，相互隔离，一个进程出错崩溃只会影响一个页面或者插件，再也不会出现Flash插件出错导致整个浏览器崩溃的情况了。此外，需要注意的是，平均来说，切换进程的开销要比切换线程大的多，但是，在不同的操作系统中，同样是进程切换，开销也不一样，比如在Linux系统中进程切换的开销就远小于Windows下进程的切换。

####3.Python与Java中的多进程与多线程

这里我们并不想用代码展示这两种语言中是如何使用多进程与多线程的。我们想要说明的是，Python和Java中的多进程与多线程在概念上的一些特点。首先，我们要明白的一点是，进程与线程是操作系统级别上的一个概念，无论各种高级语言中怎么包装这两个概念，最终他们是需要调用操作系统提供的接口的。

首先说一下Python，关于Python中多线程与多进程的coding可以看这篇文章中提到的[http://python.jobbole.com/81255/]，里面详细介绍了Python的多进程和多线程，还提到了协程(coroutine)这个概念。提到Python的并发(concurrency)编程，GIL(Global Interpreter Lock)是永远被首先想到的，因为它，Python中的并发被人所诟病。无论什么时候，在多线程编程中，关于安全性永远首先要考虑的是数据的同步性问题，Python对于此给出的解决方案便是GIL(存在CPython的实现中，但是比如Jython中则没有GIL)。所以，由于GIL的存在，任一时刻无论CPU有几个核心，Python解释器都只能运行一个线程，所以Python并不能做到真正意义上的并行，它知识通过切换上下文来模拟多线程。所以在Python中如果真的想让程序并行，应该使用多进程。接下来你肯定会这么想，为何不去掉GIL呢？其实，很多人真的去实现过试图去掉Python(Python 1.5)中的GIL，但是发现当去掉GIL后，Python的单线程程序性能急剧下降，所以，没办法还是要保留GIL的，并且在可预见的未来，GIL都不会从Python中删除。

那么Python中的多线程还有存在的必要吗？Sure！计算机通常处理的任务主要有两种类型：计算密集型(cpu-bound)和I/O密集型(I/O-bound)。对于计算密集型的任务，简单理解就是需要大量的CPU运算，数据都要经过CPU的处理，比如加密/解密，视频解码等一些操作，而I/O密集型的则不需要CPU过多参与，其数据流也不会经过CPU，大部分时间处在I/O阻塞和等待，比如Web应用和数据库读写等。由于I/O密集型任务不需要CPU过多的参与，如果I/O阻塞的时间CPU处于闲置状态，则是资源上的一种浪费。此时，可以利用I/O等待的时间CPU切换到其它任务进行处理。因此，对于I/O密集型任务，完全可以使用Python中的多线程。此时，CPU的占用将大部分用于在I/O等待的时间进行线程切换，不会浪费计算资源，从而达到处理多个任务的目的。

如果我们单纯从进程与线程的特点来看，由于进程切换的代价远大于线程，所以，对于计算密集型任务，应该选用多线程而不是多进程。但是在Python中，由于GIL的存在导致Python多线程并不能利用多核的优势，并且线程之间是互斥的。所以在Python中对于计算密集型任务首要选择是多进程的方式。


在Java中，并没有GIL这个机制。Java实现的是一种多线程的机制，即JVM 实现的是多线程的机制。所以，在Java中，如果你的程序是多线程的，则实际上是JVM负责调用操作系统的多线程接口来实现多线程，即一个JVM实例中运行着多个线程 。而Java的多进程则需要运行多个JVM，每开启一个子进程则需要新的JVM实例来运行，这样如果有一个进程发生异常，并不影响其它的子进程。

####４．进程间的通信

由于不同的进程拥有不同的地址空间，进程间的数据是相互独立的，在确保数据安全的同时，也为数据的共享带来了一定的苦难。为了让不同的进程间可以交换数据，所以提出了IPC这个概念，即进程间通信。常用的进程间的通信方式有以下几种：

+ (1)管道(pipe)和named pipe(FIFO)
+ (2)信号(signal)
+ (3)消息队列
+ (4)共享内存(shared memory)
+ (5)信号量(semaphore)
+ (6)套接字(socket)





