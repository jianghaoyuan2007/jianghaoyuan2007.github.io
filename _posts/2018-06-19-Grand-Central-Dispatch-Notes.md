---
layout: post
title: Grand Central Dispatch (GCD) 学习笔记
---

本文主要是对阅读《Objective-C高级编程:iOS与OS X多线程和内存管理》过程中一些问题的整理。

## Grand Central Dispatch (GCD) 概要

--- 

### 什么是 GCD ？

* GCD 是异步执行任务的技术之一

* 一般将应用程序中记述的线程管理用的代码在系统级中实现

* 开发者只需要定义想执行的任务并追加到适当的 Dispatch Queue 中，GCD 就能生成必要的线程并计划执行任务。

### GCD 的优点是什么？

* 高效

* 简洁

### 什么是线程？

1 个 CPU 执行的 CPU 命令列为一条无分叉路径

### 什么是多线程？

* 无分叉路径不止 1 条，存在多条。

* 在多线程中，1 个 CPU 核执行多条不同路径上的不同命令。

### 什么是上下文切换？

* OSX 和 iOS 的核心 XNU 内核在发生操作系统事件时会切换执行路径。

* 执行路径中的状态，例如 CPU 的寄存器等信息会保存到各自路径专用的内存块中，从切换目标路径专用的内存块中，复原 CPU 寄存器等信息，继续执行切换路径的 CPU 命令列。

### 多线程编程存在哪些问题？

* 数据竞争（多个线程更新相同的资源会导致数据不一致）

* 死锁（停止等待事件的线程会导致多个线程相互持续等待）

* 内存消耗（使用太多线程会消耗大量的内存）


### 多线程编程的优点是什么？

* 保证应用程序的响应性能


## GCD 的 API

--- 

### Dispatch Queue 是什么？

* Dispatch Queue 是执行处理的等待队列

* 通过 dispatch_async 等函数 API，在 Block 中记述想执行的处理并将其追加到 Dispatch Queue 中

* Dispatch Queue 按照追加的顺序（First-In-First-Out）执行处理

### Dispatch Queue 有哪些种类？

* Serial Dispatch Queue
	等待现在执行中的处理结束（固定的执行顺序）

* Concurrent Dispatch Queue
	不等待现在执行中的处理结束（执行处理顺序会根据处理内容和系统状态发生变化）

### Serial Dispatch Queue、Concurrent Dispatch Queue 和 线程的关系

* Serial Dispatch Queue 使用一个线程

* Concurrent Dispatch Queue 使用多个线程
	
	* 并行执行的处理数量取决于当前系统的状态（Dispatch Queue 的处理数、CPU 核数和 CPU 负荷）

	* XNU 内核决定应用使用的线程数，并只生成所需的线程执行处理

	* 当处理结束，应当执行的处理数量减少时，XNU 内核会结束不再需要的线程

### 什么是并行执行？

使用多个线程同时执行多个处理。

### 如何创建一个 Dispatch Queue ?

* Serial Dispatch Queue

```objective-c
// 使用默认的优先级
dispatch_queue_t mySerialDispatchQueue = dispatch_queue_create("com.example.gcd.MySerialDispatchQueue", NULL);
```

* Concurrent Dispatch Queue

```objective-c
// 使用默认的优先级
dispatch_queue_t myConcurrentDispatchQueue = dispatch_queue_create("com.example.gcd.MyConcurrentDispatchQueue", DISPATCH_QUEUE_CONCURRENT);
```

### Serial Dispatch Queue 注意事项

* 创建多个 Serial Dispatch Queue 时，各个 Serial Dispatch Queue 将并行执行。

* 系统对于一个 Serial Dispatch Queue 就只生成并使用一个线程。如果生成了 2000 个 Serial Dispatch Queue，那么久生成了 2000 个线程。

* 如果过多使用线程，就会消耗大量内存，引起大量的上下文切换，大幅度降低系统的响应性能。

### Serial Dispatch Queue 的优点

* 可以解决多个线程更新相同资源导致数据竞争的问题

### Concurrent Dispatch Queue 的优点

* 无论创建多少个 Concurrent Dispatch Queue，XNU 内核能有效管理线程

### dispatch_queue_create 内存管理

```objective-c
dispatch_queue_t myConcurrentDispatchQueue = dispatch_queue_create("com.example.gcd.MyConcurrentDispatchQueue", DISPATCH_QUEUE_CONCURRENT);
dispatch_async(myConcurrentDispatchQueue, ^{ NSLog(@"block on myConcurrentDispatchQueue."); });
dispatch_release(myConcurrentDispatchQueue);
```

### Main Dispatch Queue 是什么？

* 在主线程中执行的 Dispatch Queue

* 该 Dispatch Queue 属于 Serial Dispatch Queue

* 追加到 Main Dispatch Queue 中的处理在主线程的 Run Loop 中执行

* 由于在主线程中执行，因此要将用户界面的界面更新等一些必须在主线程中执行的处理追加到 Main Dispatch Queue 使用

* 与 NSObject 类的 performSelectorOnMainThread 实例方法相同

### Global Dispatch Queue 是什么？

* Global Dispatch Queue 是所有应用程序都能使用的 Concurrent Dispatch Queue

### Global Dispatch Queue 有哪些优先级？

* 高优先级（High Priority）

* 默认优先级（Default Priority）

* 低优先级（Low Priority）

* 后台优先级（Background Priority）

### 如何获取系统标准提供的 Dispatch Queue?

* Main Dispatch Queue

```objective-c
dispatch_queue_t mainDispatchQueue = dispatch_get_main_queue();
```

* Global Dispatch Queue（High Priority）

```objective-c
dispatch_queue_t globalDispatchQueueHigh = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_HIGH, 0);
```

* Global Dispatch Queue（Default Priority）

```objective-c
dispatch_queue_t globalDispatchQueueDefault = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
```

* Global Dispatch Queue（Low Priority）

```objective-c
dispatch_queue_t globalDispatchQueueLow = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_LOW, 0);
```

* Global Dispatch Queue（Background Priority）

```objective-c
dispatch_queue_t globalDispatchQueueBackground = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_BACKGROUND, 0);
```

### Main Dispatch Queue/Global Dispatch Queue 内存管理

* dispatch_retain/dispatch_release 无效


### 如何变更 Dispatch Queue （dispatch_queue_create 函数创建的） 的优先级？

使用 dispatch_set_target_queue 函数。
第一个参数是要变更优先级的 Dispatch Queue，该参数不允许是 Main Dispatch Queue 或 Global Dispatch Queue。
第二个参数是与要使用的执行优先级相同的 Global Dispatch Queue

示例代码如下：

```objective-c
dispatch_queue_t mySerialDispatchQueue = dispatch_queue_create("com.example.gcd.MySerialDispatchQueue", NULL);
dispatch_queue_t globalDispatchQueueBackground = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_BACKGROUND, 0);
dispatch_set_target_queue(mySerialDispatchQueue, globalDispatchQueueBackground);
```

### 如何组织 Dispatch Queue 的执行阶层？

#### Queue1 & Queue2 & Queue3 依赖 Queue0

```objective-c
dispatch_queue_t dispatchQueue0 = dispatch_queue_create("dispatchQueue0", NULL);
    
dispatch_queue_t dispatchQueue1 = dispatch_queue_create("dispatchQueue1", NULL);
dispatch_queue_t dispatchQueue2 = dispatch_queue_create("dispatchQueue2", NULL);
dispatch_queue_t dispatchQueue3 = dispatch_queue_create("dispatchQueue3", NULL);
    
dispatch_set_target_queue(dispatchQueue1, dispatchQueue0);
dispatch_set_target_queue(dispatchQueue2, dispatchQueue0);
dispatch_set_target_queue(dispatchQueue3, dispatchQueue0);
    
for (int i = 0; i < 10; i ++) {
    dispatch_async(dispatchQueue2, ^{ NSLog(@"dispatchQueue2 do block %d", i); });
}
    
for (int i = 0; i < 10; i ++) {
    dispatch_async(dispatchQueue3, ^{ NSLog(@"dispatchQueue3 do block %d", i); });
}
    
for (int i = 0; i < 10; i ++) {
    dispatch_async(dispatchQueue1, ^{ NSLog(@"dispatchQueue1 do block %d", i); });
}
```

原本该并行执行的多个 Serial Dispatch Queue，在目标 Serial Dispatch Queue 上只能同时执行一个处理。

在必须将不可并行执行的处理追加到多个 Serial Dispatch Queue 中时，如果使用 dispatch_target_queue 函数将目标指定为某一个 Serial Dispatch Queue，即可防止并行处理。

#### 串行 Queue 1 & 并行 Queue 2 依赖 Serial Queue

```objective-c
dispatch_queue_t serialQueue = dispatch_queue_create("com.fyf.serialqueue", DISPATCH_QUEUE_SERIAL);
    
dispatch_queue_t queue1 = dispatch_queue_create("com.fyf.firstqueue", DISPATCH_QUEUE_SERIAL);
    
dispatch_queue_t queue2 = dispatch_queue_create("com.fyf.secondqueue", DISPATCH_QUEUE_CONCURRENT);
    
dispatch_set_target_queue(queue1, serialQueue);
    
dispatch_set_target_queue(queue2, serialQueue);
    
dispatch_async(serialQueue, ^{ NSLog(@"serialQueue 线程为：%@", [NSThread currentThread]); });
dispatch_async(queue1, ^{
    NSLog(@"开始第 1 个任务。");
    [NSThread sleepForTimeInterval:3.f];
    NSLog(@"结束第 1 个任务。");
});
dispatch_async(queue2, ^{
    NSLog(@"开始第 2 个任务。");
    [NSThread sleepForTimeInterval:2.f];
    NSLog(@"结束第 2 个任务。");
});
dispatch_async(queue2, ^{
    NSLog(@"开始第 3 个任务。");
    [NSThread sleepForTimeInterval:1.f];
    NSLog(@"结束第 3 个任务。");
});
```

### dispatch_after 函数的功能是什么？

在指定的时间后执行处理的情况。

### dispatch_after 函数的注意事项

* dispatch_after 函数并不是在指定时间后执行，而只是在指定时间追加处理到 Dispatch Queue

* 然而，即使加入到了对应的 Dispatch Queue 中，也不会立即执行。因为队列中可能有其它的处理要执行

### 如何创建一个 dispatch_time_t 的值？

#### 使用 dispatch_time 函数

* 通常用于计算相对的时间

* `dispatch_time_t time = dispatch_time(DISPATCH_TIME_NOW, 1ull * NSEC_PER_SEC);`

* 参数说明

	* when 

	The dispatch_function_t value to use as the basis for a new value. Pass DISPATCH_TIME_NOW to create a new time value relative to now.

	* delta

	The number of nanoseconds to add to the time in the when parameter. 

* 相关名词解释

	* SEC：秒(s)

	* MSEC：毫秒(ms)

	* USEC：微妙(μs)

	* NSEC：纳秒(ns)

* 相关宏定义

	* `#define NSEC_PER_SEC  1000000000ull // 1s = 1000000000ns`

	* `#define NSEC_PER_MSEC 1000000ull // 1ms = 1000000ns`

	* `#define NSEC_PER_USEC 1000ull // 1μs = 1000ns`

	* `#define USEC_PER_SEC  1000000ull // 1s = 1000000μs`

#### 使用 dispatch_walltime 函数

* 由 POSIX 中使用的 struct timespec 类型的时间得到 dispatch_time_t 类型

* 用于计算绝对的时间

示例代码如下：

```objective-c
NSDate *date = [NSDate date];
NSTimeInterval interval = [date timeIntervalSince1970];
double second;
double subsecond = modf(interval, &second);
struct timespec time;
time.tv_sec = second;
time.tv_nsec = subsecond * NSEC_PER_SEC;
dispatch_time_t milestone = dispatch_walltime(&time, 0);
```
### 如何实现追加到 Dispatch Queue 中的多个处理全部结束之后，执行某个处理？

#### 使用 Serial Dispatch Queue 

由于 Serial Dispatch Queue 本身就是一个串行队列，队列中的处理按照先进先出（FIFO）的原则来处理。只要在最后处理的后面再添加一个处理即可。

#### 使用 Concurrent Dispatch Queue

示例代码如下：

```objective-c
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
dispatch_group_t group = dispatch_group_create();
    
dispatch_group_async(group, queue, ^{ NSLog(@"Block 0."); });
dispatch_group_async(group, queue, ^{ NSLog(@"Block 1."); });
dispatch_group_async(group, queue, ^{ NSLog(@"Block 2."); });
    
dispatch_group_notify(group, dispatch_get_main_queue(), ^{ NSLog(@"Done."); });
    
dispatch_release(group);    // Not available in ARC mode.
```

### dispatch_group_wait 函数说明

* 等待全部处理执行结束，或等待超时

* 返回值

	* 0：表示 Dispatch Group 的全部处理执行结束

	* 1：表示 Dispatch Group 的某一个处理还在执行中

* 超时参数

	* dispatch_time_t 类型

	* 在 DISPATCH_TIME_FOREVER 的情况下，如果该函数返回了，那么必定是 0（即全部处理结束）

* “等待”的含义

	* 该函数处于调用的状态而不返回

	* 当前线程处于停止的状态

### dispatch_barrier_async 函数说明

* 等待追加到 Concurrent Dispatch Queue 上的并行的处理全部处理结束之后，再将指定的处理追加到该 Concurrent Dispatch Queue 中。然后在 dispatch_barrier_async 函数追加的处理完成之后，Concurrent Dispatch Queue 才恢复一般的动作，追加到该 Concurrent Dispatch Queue 的处理又开始并行执行。

* 使用 Concurrent Dispatch Queue 和 dispatch_barrier_async 函数可实现高效的数据库访问和文件访问

示例代码如下：

```objective-c
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    
dispatch_async(queue, ^{ NSLog(@"Reading 0."); });
dispatch_async(queue, ^{ NSLog(@"Reading 1.");});
dispatch_async(queue, ^{ NSLog(@"Reading 2."); });
dispatch_barrier_async(queue, ^{ NSLog(@"Writing 0"); });
dispatch_async(queue, ^{ NSLog(@"Reading 3."); });
dispatch_async(queue, ^{ NSLog(@"Reading 4."); });
dispatch_barrier_async(queue, ^{ NSLog(@"Writing 1"); });
dispatch_async(queue, ^{ NSLog(@"Reading 5."); });
```
### dispatch_sync 函数说明

将指定的 Block 同步地追加到指定的 Dispatch Queue 中。在追加处理执行结束之前，dispatch_sync 函数会一直等待（当前线程处于停止状态）。

### 使用 dispatch_sync 函数的线程死锁

示例代码如下：

```objective-c
// Running in main thread.
dispatch_queue_t queue = dispatch_get_main_queue();
dispatch_sync(queue, ^{ NSLog(@"Reading 0."); });
```	

* 主线程是一个串行队列。dispatch_sync 函数会等待一直到被添加的处理完成。

* 然而 dispatch_sync 向主线程的队列当中添加的这个 Block 需要等待在它之前的任务完成之后才能被处理，因为队列是先进先出（FIFO）的数据结构。

* 这就导致主线程在由于 dispatch_sync 函数一直阻塞不能返回，被添加到主线程队列里面的这个 Block 也无法执行。造成了线程的死锁。串行队列都会存在这个问题。

### dispatch_apply 函数说明

* dispatch_sync 函数和 Dispatch Group 组合的 API

* 该函数按指定的次数将指定的 Block 追加到指定的 Dispatch Queue 中，并等待全部执行结束。

* 推荐在 dispatch_async 函数中非同步地执行 dispatch_apply 函数

示例代码如下：

```objective-c
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
dispatch_apply(3, queue, ^(size_t index) { NSLog(@"index = %zu", index); });
NSLog(@"Done.");
```

### dispatch_suspend / dispatch_resume 函数说明

* dispatch_suspend 函数

	* 挂起指定的 Dispatch Queue

	* 挂起后，追加到 Dispatch Queue 中，但尚未执行的处理在此之后停止执行。

* dispatch_resume 函数

	* 恢复指定的 Dispatch Queue

	* 恢复使得被挂起尚未执行的处理能够继续执行

* 应用案例

	* 当追加大量的处理到 Dispatch Queue 时，在追加处理的过程中，有时希望不执行已追加的处理。

### Dispatch Semaphore

#### Dispatch Semaphore 说明

* 持有计数的信号，该信号是多线程编程中的计数类型信号。

* 所谓信号，类似于过马路时常用的手旗，可以通过时举起手旗，不可以通过时方向手旗。

* 在 Dispatch Semaphore 中，使用计数来实现该功能。计数为0时等待，计数为1或者大于1时，减去1而不等待。

#### dispatch_semaphore_create 函数说明

* 用于创建 Dispatch Semaphore

* `dispatch_semaphore_t semaphore =  dispatch_semaphore_create(1);`

#### dispatch_semaphore_wait 函数说明

* 等待 Dispatch Semaphore 的计数值大于或等于1。

* 当计数值大于等于1时，或者在待机中计数值大于等于1时，对该计数进行减法并从 dispatch_semaphore_wait 函数返回。

* `dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);`

#### dispatch_semaphore_signal 函数说明

将 Dispatch Semaphore 的计数值加1

示例代码如下：

```objective-c
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
dispatch_semaphore_t semaphore =  dispatch_semaphore_create(1);
NSMutableArray *array = [NSMutableArray array];
    
for (int i = 0; i < 10000; i ++) {
    dispatch_async(queue, ^{
        dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
        [array addObject:[NSNumber numberWithInt:i]];
        dispatch_semaphore_signal(semaphore);
    });
}
```

### dispatch_once 函数说明

* 保证在应用程序执行中指执行一次指定处理的 API

* 该函数在多线程的环境下依然安全

示例代码如下：

```objective-c
static dispatch_once_t onceToken;
dispatch_once(&onceToken, ^{
    // 初始化操作
});
```

### Dispatch I/O 说明

如果想提高文件的读取速度，可以尝试使用 Dispatch I/O。

#### dispatch_io_create 函数

生成 Dispatch I/O，并指定发生错误时用来执行处理的 Block，以及执行该 Block 的 Dispatch Queue。

#### dispatch_io_set_low_water 函数

设定一次读取的大小（分割大小）

#### dispatch_io_read 函数

使用 Global Dispatch Queue 开始并行读取。每当各个分割的文件块读取结束时，将含有文件块数据的 Dispatch Data 传给 dispatch_io_read 函数指定的读取结束时回调用的 Block。回调用的 Block 分析传递过来的 Dispatch Data 并进行结合处理。

## GCD 实现

--- 

### Dispatch Queue

#### Dispatch Queue 的队列

* 使用结构体和链表实现 FIFO 队列

* 通过 dispatch_async 等函数对处理进行入队操作

* 待处理的 Block 被封装成 Dispatch Continuation 才能入队

* Dispatch Continuation 用于保存 Block 所属的 Dispatch Group 和其他一些信息，相当于执行的上下文

#### Global Dispatch Queue 的种类与 pthread_workqueue、workqueue 的组成和关系

* Global Dispatch Queue (High Priority) --> 对应一个 pthread_workqueue --> 对应 WORKQUEUE_HIGH_PRIOQUEUE

* Global Dispatch Queue (Default Priority) --> 对应一个 pthread_workqueue --> 对应 WORKQUEUE_DEFAULT_PRIOQUEUE

* Global Dispatch Queue (Low Priority) --> 对应一个 pthread_workqueue --> 对应 WORKQUEUE_LOW_PRIOQUEUE

* Global Dispatch Queue (Background Priority) --> 对应一个 pthread_workqueue --> 对应 WORKQUEUE_BG_PRIOQUEUE

* Global Dispatch Queue (High Overcommit Priority) --> Serial Dispatch Queue --> 对应一个 pthread_workqueue --> 对应 WORKQUEUE_HIGH_PRIOQUEUE

* Global Dispatch Queue (Default Overcommit Priority) --> Serial Dispatch Queue --> 对应一个 pthread_workqueue --> 对应 WORKQUEUE_DEFAULT_PRIOQUEUE

* Global Dispatch Queue (Low Overcommit Priority) --> Serial Dispatch Queue --> 对应一个 pthread_workqueue --> 对应 WORKQUEUE_LOW_PRIOQUEUE

* Global Dispatch Queue (Background Overcommit Priority) --> Serial Dispatch Queue --> 对应一个 pthread_workqueue --> 对应 WORKQUEUE_BG_PRIOQUEUE

#### Dispatch Queue 的执行过程

* 当在 Global Dispatch Queue 中执行 Block 时，libdispatch 从 Global Dispatch Queue 自身的 FIFO 的队列中取出 Dispatch Continuation， 调用 pthread_workequeue_additem_np 函数。

* 将该 Global Dispatch Queue 自身、符合其优先级的 workqueue 信息以及为执行 Dispatch Continuation 的回调函数等传递给参数。

* pthread_workqueue_additem_np 函数使用 workq_kernreturn 系统调用，通知 workqueue 增加应当执行的项目。

* 根据该通知，XNU 内核基于系统状态判断是否需要生成线程。如果是 Overcommit 优先级的 Global Dispatch Queue，workqueue 则始终生成线程。

* 该线程虽然与 iOS 和 OS X 中通常使用的线程大致相同，但是有一部分 pthread API 不能使用。

* 因为 workqueue 生成的线程在实现用于 workqueue 的线程计划表中运行，所以与一般线程的上下文切换不同。

* workqueue 的线程执行 pthread_workqueue 函数。该函数调用 libdispatch 的回调函数。在回调函数中执行加入的 Dispatch Continuation 的 Block。

* Block 执行结束后，进行通知 Dispatch Group 结束，释放 Dispatch Continuation 等处理，开始准备执行加入到 Global Dispatch Queue 中的下一个 Block。

### Dispatch Source

#### Dispatch Source 说明

* Dispatch Source 是 BSD 系内核惯有功能 kqueue 的封装

* kqueue 是在 XNU 内核中发生各种事件时，在应用程序编程方法执行处理的技术。

* 其 CPU 负荷非常小，尽量不占用资源。kqueue 可以说是应用程序处理 XNU 内核中发生各种事件的方法中最优秀的一种。

#### Dispatch Source 的种类

* DISPATCH_SOURCE_TYPE_DATA_ADD 变量增加

* DISPATCH_SOURCE_TYPE_DATA_OR 变量 OR

* DISPATCH_SOURCE_TYPE_MACH_SEND MACH 端口发送

* DISPATCH_SOURCE_TYPE_MACH_RECV MACH 端口接收

* DISPATCH_SOURCE_TYPE_PROC 检测到与进程相关的事件

* DISPATCH_SOURCE_TYPE_READ 可读取文件映像

* DISPATCH_SOURCE_TYPE_SIGNAL 接收信号

* DISPATCH_SOURCE_TYPE_TIMER 定时器

* DISPATCH_SOURCE_TYPE_VNODE 文件系统有变更

* DISPATCH_SOURCE_TYPE_WRITE 可写入文件映像

#### Dispatch Source 优点

Dispatch Source 支持取消，而且取消时必须执行的处理可指定为回调用的 Block 形式。

