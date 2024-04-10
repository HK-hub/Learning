# Java 程序问题排查 | JDK 常用工具详解

[toc]

> https://blog.csdn.net/wangzhongshun/article/details/112537453
>
> https://zhuanlan.zhihu.com/p/642932325

JDK 提供了一系列排查问题的工具，本文主要对 jps、jinfo、jstack、jstat、jmap、jhat 做介绍。



## jps

jps 全称为 Java Virtual Machine Process Status Tool，是 JDK 提供的一个查看当前用户有权访问的主机上正在运行的 Java 进程信息的命令行工具，通过读取特定目录下的相关文件来获取信息。

### 语法

```text
jps [options] [hostid]
```

- options：命令参数，可以控制输出格式。
- hostid：指定特定主机，可以是 ip 地址和域名， 也可以指定具体协议和端口。

参数说明：

- -q：只输出 PID。
- -m：输出传递给 main 方法的参数。
- -l：输出应用程序主类的完整包名或应用程序 JAR 文件的完整路径名。
- -v：输出传递给 JVM 的参数。
- -V：通过 flags 文件（.hotspotrc 文件或 -XX:Flags=< filename > 参数指定的文件）输出传递给 JVM 的参数。本参数不常用。

### 示例

```text
// 不带参数，默认列出 PID 和简单的主类名或 jar 名称
jps
```

![img](https://pic3.zhimg.com/80/v2-ee1393e54efc9622060d6e56e433a566_720w.webp)

```text
// 只输出 PID
jps -q
```

![img](https://pic2.zhimg.com/80/v2-3cbf38a8ea41caa5e826f711314425d5_720w.webp)

```text
// 输出传递给 main 方法的参数
jps -m
```

![img](https://pic4.zhimg.com/80/v2-5ce659bca9bf500b6502cd0310147b9b_720w.webp)

```text
// 输出应用程序主类的完整包名或应用程序 JAR 文件的完整路径名
jps -l
```

![img](https://pic3.zhimg.com/80/v2-6ba464c8bbec173bf4ad3b669f289c2a_720w.webp)

```text
// 输出传递给 JVM 的参数
jps -v
```

![img](https://pic3.zhimg.com/80/v2-ac46268cab39e77b1d77ee25ae18f40a_720w.webp)

```text
// 通过 flags 文件（.hotspotrc 文件或 -XX:Flags=< filename > 参数指定的文件）输出传递给 JVM 的参数。本参数不常用。
jps -V
```

![img](https://pic4.zhimg.com/80/v2-30275e516cff63f04049bce17511ff9f_720w.webp)

## jinfo

jinfo 是 JDK 自带的一个命令行工具，用于查看和调整正在运行的 Java 进程的各种参数和选项。

### 语法

jinfo -help：

```text
Usage:
    jinfo [option] <pid>
        (to connect to running process)
    jinfo [option] <executable <core>
        (to connect to a core file)
    jinfo [option] [server_id@]<remote server IP or hostname>
        (to connect to remote debug server)

where <option> is one of:
    -flag <name>         to print the value of the named VM flag
    -flag [+|-]<name>    to enable or disable the named VM flag
    -flag <name>=<value> to set the named VM flag to the given value
    -flags               to print VM flags
    -sysprops            to print Java system properties
    <no option>          to print both of the above
    -h | -help           to print this help message
```

- no option：输出全部的参数和系统属性
- -flag name：输出对应名称的参数
- -flag [+|-]name：开启或者关闭对应名称的参数
- -flag name=value：动态修改对应名称的参数（并非所有 JVM 参数都支持动态修改）

![img](https://pic1.zhimg.com/80/v2-7fd0f53791b1a24be83d960936387480_720w.webp)

- -flags：输出全部的参数

![img](https://pic2.zhimg.com/80/v2-99264f192ea801a09dad4b921fcdd751_720w.webp)

- -sysprops：输出系统属性

支持动态修改的 JVM 参数：

![img](https://pic2.zhimg.com/80/v2-c4cc2b3b497daa5670bd899db621a9fd_720w.webp)

## jstack

jstack 是 JDK 自带的一种堆栈跟踪工具，可用于生成 JVM 当前时刻的线程快照。线程快照是当前 JVM 内每一条线程正在执行的方法堆栈的集合，生成线程快照的主要目的是定位线程出现长时间停顿的原因，如线程间死锁、死循环、请求外部资源导致的长时间等待等。 线程出现停顿的时候通过 jstack 来查看各个线程的调用堆栈，就可以知道没有响应的线程到底在后台做什么事情，或者等待什么资源。 如果 java 程序崩溃生成 core 文件，jstack 工具可以用来获得 core 文件的 java stack 和 native stack 的信息，从而可以知道 java 程序是如何崩溃和在程序何处发生问题。另外，jstack 工具还可以 attach 到正在运行的 java 程序中，看到当时运行的 java 程序的 java stack 和 native stack的信息, 如果现在运行的 java 程序呈现 hung 的状态，jstack 是非常有用的。

简而言之，jstack 主要用来查看 Java 线程的调用堆栈，可以用来分析线程问题（如死锁、死循环、CPU 占用过高）。

### 语法

最常见用法

```text
jstack [options] <pid>
```

![img](https://pic1.zhimg.com/80/v2-25c139f9dd1bc01ece18665010e2254c_720w.webp)

- -F：当正常输出的请求不被响应时，强制输出线程堆栈
- -m：如果调用到本地方法的话，加上此参数可以显示本地方法的堆栈
- **-l：最常用的一个参数，除堆栈外，显示关于锁的附加信息，在发生死锁时可以用 jstack -l pid 来观察锁持有情况**

把 jstack 的输出重定向到文件中，就可以分析了。

```text
// 比如
jstack -l <pid> >> thread.log
```

### 示例

### 分析死锁

### 线程状态

使用 jstack 查看线程堆栈信息时可能会看到的**线程的几种状态**：

- New：创建后尚未启动的线程处于这种状态，不会出现在Dump中。
- RUNNABLE：包括Running和Ready。线程开启start（）方法，会进入该状态，在虚拟机内执行的。
- Waiting：无限的等待另一个线程的特定操作。
- Timed Waiting：有时限的等待另一个线程的特定操作。
- Blocked：在程序等待进入同步区域的时候，线程将进入这种状态，在等待监视器锁。
- Terminated：已终止线程的线程状态，线程已经结束执行。

**Dump 文件的线程状态一般其实就以下3种：**

- RUNNABLE，线程处于执行中
- BLOCKED，线程被阻塞
- WAITING，线程正在等待

### Monitor

**Monitor 是 Java 中用以实现线程之间的互斥与协作的主要手段**，它可以看成是对象或者 Class 的锁。每一个对象都有，也仅有一个 monitor。下图描述了线程和 Monitor 之间的关系，以及线程的状态转换图：

![img](https://pic4.zhimg.com/80/v2-bfd15ade157fa7f134251599c542b273_720w.webp)

- **进入区(Entry Set)**：表示线程通过 synchronized 要求获取对象的锁。如果对象未被锁住，则变为拥有者，否则在进入区等待。一旦对象锁被其他线程释放，立即参与竞争。
- **拥有者(The Owner)**：表示某一线程成功竞争到对象锁。
- **等待区(Wait Set)**：表示线程通过对象的 wait 方法，释放对象的锁，并在等待区等待被唤醒。

从图中可以看出，一个 Monitor 在某个时刻，只能被一个线程拥有，该线程就是 Active Thread，而其它线程都是 Waiting Thread，分别在两个队列 Entry Set 和 Wait Set 里面等候。在 Entry Set 中等待的线程状态是 Waiting for monitor entry，而在 Wait Set 中等待的线程状态是 “in Object.wait()”。

### 常见 dump 内容

- locked <地址> 目标：申请对象锁成功，监视器的拥有者。

```text
at oracle.jdbc.driver.PhysicalConnection.prepareStatement
- locked <0x00002aab63bf7f58> (a oracle.jdbc.driver.T4CConnection)
at oracle.jdbc.driver.PhysicalConnection.prepareStatement
- locked <0x00002aab63bf7f58> (a oracle.jdbc.driver.T4CConnection)
at com.jiuqi.dna.core.internal.db.datasource.PooledConnection.prepareStatement
```

- waiting to lock <地址> 目标：申请对象锁未成功，在 Entry Set 等待。

```text
// 一个线程锁住某对象，大量其他线程在该对象上等待：
"blocker" runnable
java.lang.Thread.State: RUNNABLE
at com.jiuqi.hcl.javadump.Blocker$1.run(Blocker.java:23)
- locked <0x00000000eb8eff68> (a java.lang.Object)
"blockee-11" waiting for monitor entry
java.lang.Thread.State: BLOCKED (on object monitor)
at com.jiuqi.hcl.javadump.Blocker$2.run(Blocker.java:41)
- waiting to lock <0x00000000eb8eff68> (a java.lang.Object)
"blockee-86" waiting for monitor entry
java.lang.Thread.State: BLOCKED (on object monitor)
at com.jiuqi.hcl.javadump.Blocker$2.run(Blocker.java:41)
- waiting to lock <0x00000000eb8eff68> (a java.lang.Object)
```

- waiting on <地址> 目标：释放对象锁，在等待区等待被唤醒。

```text
at java.lang.Object.wait(Native Method)
- waiting on <0x00000000da2defb0> (a WorkingThread)
at com.jiuqi.dna.core.impl.WorkingManager.getWorkToDo
- locked <0x00000000da2defb0> (a WorkingThread)
at com.jiuqi.dna.core.impl.WorkingThread.run
```

### 分析流程

使用 jstack 分析死锁步骤非常简单：

1. jps 获取 pid
2. jstack -l pid 打印堆栈信息
3. 分析堆栈信息，一般来说 Java-level 的死锁，jstack 能自动检测出来。

比如下面这段 jstack 打印的堆栈信息，就是 jstack 自动检测出了一个 Java-level 死锁：

- Thread-0 锁住了<0x00000007d6aa2c98>，尝试获取 <0x00000007d6aa2ca8> 的锁
- Thread-1 锁住了<0x00000007d6aa2ca8>，尝试获取 <0x00000007d6aa2c98> 的锁

```text
Found one Java-level deadlock:
=============================
"Thread-1":
  waiting to lock monitor 0x00007f0134003ae8 (object 0x00000007d6aa2c98, a java.lang.Object),
  which is held by "Thread-0"
"Thread-0":
  waiting to lock monitor 0x00007f0134006168 (object 0x00000007d6aa2ca8, a java.lang.Object),
  which is held by "Thread-1"

Java stack information for the threads listed above:
===================================================
"Thread-1":
    at javaCommand.DeadLockclass.run(JStackDemo.java:40)
    - waiting to lock <0x00000007d6aa2c98> (a java.lang.Object)
    - locked <0x00000007d6aa2ca8> (a java.lang.Object)
    at java.lang.Thread.run(Thread.java:745)
"Thread-0":
    at javaCommand.DeadLockclass.run(JStackDemo.java:27)
    - waiting to lock <0x00000007d6aa2ca8> (a java.lang.Object)
    - locked <0x00000007d6aa2c98> (a java.lang.Object)
    at java.lang.Thread.run(Thread.java:745)

Found 1 deadlock.
```

**需要注意的是，jstack 只能自动检测 Java 线程间的死锁，不能检测到其它类型的线程状态，比如本地线程或者操作系统线程的状态。**

如果要分析本地线程或者操作系统线程是否出现了死锁，可以在使用 jstack 时加上 -m 参数，把本地线程堆栈一起打印起来进行分析。

### 分析 CPU 占用

整体步骤：

- top 查看占用 CPU 较高的进程，可以发现 pid 为 21340 的进程 CPU 占用较高。

![img](https://pic4.zhimg.com/80/v2-37e71260a4c14c3be7f0387441c5b8d3_720w.webp)

- top -Hp pid 可以查看该进程下各个线程的 CPU 使用情况，可以发现线程21350的 CPU 占用较高

![img](https://pic3.zhimg.com/80/v2-ef31e61fa5b5ca9b7fc5515d9148442a_720w.webp)

img

- jstack -l pid >> dump.log 将线程堆栈信息保存下来
- 分析堆栈信息，thread dump 中，每个线程对应一个十六进制的 nid（native thread id），将 21350 转成十六进制 5366 然后 grep 查看相关信息即可：

![img](https://pic2.zhimg.com/80/v2-c2de6653fb934983444c688d23aede6d_720w.webp)

### 参考链接

- [https://www.cnblogs.com/myseries/p/12050083.html](https://link.zhihu.com/?target=https%3A//www.cnblogs.com/myseries/p/12050083.html)
- [https://juejin.cn/post/6844904152850497543](https://link.zhihu.com/?target=https%3A//juejin.cn/post/6844904152850497543)
- https://zhuanlan.zhihu.com/p/475571849

## jstat

jstat 是 JDK 自带的一个命令行工具，全称为 Java Virtual Machine statistics monitoring tool，可以用来监视和分析 Java 应用程序的内存使用和性能情况。jstat 命令可以显示有关 Java 堆和非堆内存使用情况、类加载、垃圾回收、线程和编译器等方面的信息。

### 语法

```text
jstat [ generalOption | outputOptions vmid [ interval[s|ms] [count] ] ]
```

参数说明：

- generalOption：一般选项，用于设置 jstat 命令的全局行为。可以是以下选项之一：
- -class：显示类加载情况。
- -compiler：显示JIT编译器统计信息。
- -gc：显示垃圾回收统计信息。
- -gccapacity：显示垃圾回收堆内存使用情况。
- -gcmetacapacity：显示垃圾回收非堆内存使用情况。
- -gcnew：显示新生代垃圾回收统计信息。
- -gcnewcapacity：显示新生代垃圾回收堆内存使用情况。
- -gcold：显示老年代垃圾回收统计信息。
- -gcoldcapacity：显示老年代垃圾回收堆内存使用情况。
- -gcutil：显示垃圾回收堆内存使用情况总览。
- -printcompilation：显示 JIT 编译器编译情况。
- outputOptions：输出选项，用于设置 jstat 命令的输出格式。可以是以下选项之一：
- -t：输出时间戳。
- -h：输出帮助信息。
- -J：输出完整的 JVM 内部信息。
- vmid：Java 虚拟机进程 ID 或进程名。
- interval：指定采样间隔时间，默认单位是毫秒。可以使用 s 或 ms 后缀指定单位，例如 10s 或 500ms。
- count：指定采样次数，默认是无限次。

### 示例

### jstat -class pid

显示加载class的数量，及所占空间等信息:

![img](https://pic1.zhimg.com/80/v2-fa998aab357f515d099d891e00f14144_720w.webp)

| 显示列名 | 具体描述                 |
| -------- | ------------------------ |
| Loaded   | 装载的类的数量           |
| Bytes    | 装载类所占用的字节数     |
| Unloaded | 卸载类的数量             |
| Bytes    | 卸载类的字节数           |
| Time     | 装载和卸载类所花费的时间 |

### jstat -compiler pid

显示VM实时编译的数量等信息：

![img](https://pic4.zhimg.com/80/v2-82179777e555660a2cd4d572ffd592ff_720w.webp)

| 显示列名     | 具体描述                           |
| ------------ | ---------------------------------- |
| Compiled     | 编译任务执行数量                   |
| Failed       | 编译任务执行失败数量               |
| Invalid      | 编译任务执行失效数量               |
| Time         | 编译任务消耗时间                   |
| FailedType   | 最后一个编译失败任务的类型         |
| FailedMethod | 最后一个编译失败任务所在的类及方法 |

### jstat -gc pid

可以显示gc的信息，查看gc的次数，及时间：

![img](https://pic2.zhimg.com/80/v2-2d815212ac033ad2fb265528f368b925_720w.webp)

| 显示列名 | 具体描述                                              |
| -------- | ----------------------------------------------------- |
| S0C      | 年轻代中第一个survivor（幸存区）的容量 (字节)         |
| S1C      | 年轻代中第二个survivor（幸存区）的容量 (字节)         |
| S0U      | 年轻代中第一个survivor（幸存区）目前已使用空间 (字节) |
| S1U      | 年轻代中第二个survivor（幸存区）目前已使用空间 (字节) |
| EC       | 年轻代中 Eden（伊甸园）的容量 (字节)                  |
| EU       | 年轻代中 Eden（伊甸园）目前已使用空间 (字节)          |
| OC       | 老年代的容量 (字节)                                   |
| OU       | 老年代目前已使用空间 (字节)                           |
| PC       | Perm(持久代)的容量 (字节)                             |
| PU       | Perm(持久代)目前已使用空间 (字节)                     |
| YGC      | 从应用程序启动到采样时年轻代中 gc 次数                |
| YGCT     | 从应用程序启动到采样时年轻代中 gc 所用时间(s)         |
| FGC      | 从应用程序启动到采样时老年代(full gc) gc 次数         |
| FGCT     | 从应用程序启动到采样时老年代(full gc) gc 所用时间(s)  |
| GCT      | 从应用程序启动到采样时 gc 用的总时间(s)               |

### jstat -gccapacity pid

显示 VM 内存中三代（young, old, perm）对象的使用和占用大小：

| 显示列名 | 具体描述                                       |
| -------- | ---------------------------------------------- |
| NGCMN    | 年轻代(young)中初始化(最小)的大小(字节)        |
| NGCMX    | 年轻代(young)的最大容量 (字节)                 |
| NGC      | 年轻代(young)中当前的容量 (字节)               |
| S0C      | 年轻代中第一个 survivor（幸存区）的容量 (字节) |
| S1C      | 年轻代中第二个 survivor（幸存区）的容量 (字节) |
| EC       | 年轻代中 Eden（伊甸园）的容量 (字节)           |
| OGCMN    | 老年代中初始化(最小)的大小 (字节)              |
| OGCMX    | 老年代的最大容量(字节)                         |
| OGC      | 老年代当前新生成的容量 (字节)                  |
| OC       | 老年代的容量 (字节)                            |
| PGCMN    | perm 代中初始化(最小)的大小 (字节)             |
| PGCMX    | perm 代的最大容量 (字节)                       |
| PGC      | perm 代当前新生成的容量 (字节)                 |
| PC       | perm(持久代)的容量 (字节)                      |
| YGC      | 从应用程序启动到采样时年轻代中 gc 次数         |
| FGC      | 从应用程序启动到采样时老年代(full gc) gc 次数  |

### jstat -gcutil pid

统计 gc 信息：

![img](https://pic4.zhimg.com/80/v2-a3e3c04554de2a396c63b638ca132ac7_720w.webp)

| 显示列名 | 具体描述                                                  |
| -------- | --------------------------------------------------------- |
| S0       | 年轻代中第一个 survivor（幸存区）已使用的占当前容量百分比 |
| S1       | 年轻代中第二个 survivor（幸存区）已使用的占当前容量百分比 |
| E        | 年轻代中 Eden（伊甸园）已使用的占当前容量百分比           |
| O        | 老年代已使用的占当前容量百分比                            |
| P        | perm 代已使用的占当前容量百分比                           |
| YGC      | 从应用程序启动到采样时年轻代中 gc 次数                    |
| YGCT     | 从应用程序启动到采样时年轻代中 gc 所用时间(s)             |
| FGC      | 从应用程序启动到采样时老年代(full gc) gc 次数             |
| FGCT     | 从应用程序启动到采样时老年代(full gc) gc 所用时间(s)      |
| GCT      | 从应用程序启动到采样时 gc 用的总时间(s)                   |

### 参考链接

1. [https://blog.csdn.net/zhaozheng7758/article/details/8623549](https://link.zhihu.com/?target=https%3A//blog.csdn.net/zhaozheng7758/article/details/8623549)
2. 
   [https://www.linuxprobe.com/detailed-jstat-command.html](https://link.zhihu.com/?target=https%3A//www.linuxprobe.com/detailed-jstat-command.html)

## jmap

jmap 是 JDK 自带的一个命令行工具，可以用于生成 Java Heap Dump 文件，以及查看 Java 进程中的内存使用情况。

本文内容来自一篇整理得非常详细的文档： [https://juejin.cn/post/6844904062526160904](https://link.zhihu.com/?target=https%3A//juejin.cn/post/6844904062526160904)

### 语法

```text
jmap [option]  <pid>
jmap [option] <executable  (to connect to a core file)
jmap [option] [server_id@] (to connect to remote debug server)
```

option：命令选项，常用选项如下：

- -heap：打印 Java 堆概要信息，包括使用的 GC 算法、堆配置参数和各代中堆内存使用情况；
- -histo[:live]： 打印 Java 堆中对象直方图，通过该图可以获取每个 class 的对象数目，占用内存大小和类全名信息，带上 :live，则只统计活着的对象；
- -permstat 打印永久代统计信息；
- -finalizerinfo 打印等待回收的对象信息
- -dump: 以 hprof 二进制格式将 Java 堆信息输出到文件内，该文件可以用 JProfiler、VisualVM 或 jhat 等工具查看；

dump-options 选项:

- live 只输出活着的对象，不指定则输出堆中所有对象
- format=b 指定输出格式为二进制
- file= 指定文件名及文件存储位置，例如：jmap -dump:live,format=b,file=D:\heap.bin
- -F 与-dump: 或 -histo 一起使用，当没有响应时，强制执行；注意：不支持live子选项
- pid：进程id

### 示例

### jmap -heap pid

```text
> jmap -heap 10352

jmap -heap 10352
Attaching to process ID 10352, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.201-b09

using thread-local object allocation.
Parallel GC with 4 thread(s)

Heap Configuration:
   //对应jvm启动参数-XX:MinHeapFreeRatio设置JVM堆最小空闲比率(defalut 40)
   MinHeapFreeRatio         = 0 
   //对应jvm启动参数 -XX:MaxHeapFreeRatio设置JVM堆最大空闲比率(default 70)
   MaxHeapFreeRatio         = 100
   //对应jvm启动参数-XX:MaxHeapSize=设置JVM堆的最大大小
   MaxHeapSize              = 4280287232 (4082.0MB)
   //对应jvm启动参数-XX:NewSize=设置JVM堆的‘新生代’的默认大小
   NewSize                  = 89128960 (85.0MB)
   //对应jvm启动参数-XX:MaxNewSize=设置JVM堆的‘新生代’的最大大小
   MaxNewSize               = 1426587648 (1360.5MB)
   //对应jvm启动参数-XX:OldSize=<value>:设置JVM堆的‘老年代’的大小
   OldSize                  = 179306496 (171.0MB)
   //对应jvm启动参数-XX:NewRatio=:‘新生代’和‘老生代’的大小比率
   NewRatio                 = 2
   //对应jvm启动参数-XX:SurvivorRatio=设置年轻代中Eden区与Survivor区的大小比值
   SurvivorRatio            = 8
   //对应jvm启动参数-XX:MetaspaceSize=<value>:设置JVM堆的‘元空间’的初始大小
   // jdk1.8 永久代已经被元空间所取代
   MetaspaceSize            = 21807104 (20.796875MB)
   CompressedClassSpaceSize = 1073741824 (1024.0MB)
   //对应jvm启动参数-XX:MaxMetaspaceSize= :设置JVM堆的‘元空间’的最大大小
   MaxMetaspaceSize         = 17592186044415 MB
   G1HeapRegionSize         = 0 (0.0MB)
//堆内存分布
Heap Usage:
//新生代的内存分布
PS Young Generation
//Eden区内存分布
Eden Space:
   //Eden区总容量
   capacity = 1425539072 (1359.5MB)
   //Eden区已使用
   used     = 28510792 (27.19001007080078MB)
   //Eden区剩余容量
   free     = 1397028280 (1332.3099899291992MB)
   //Eden区使用比率
   2.0000007407724003% used
From Space:
   capacity = 524288 (0.5MB)
   used     = 65536 (0.0625MB)
   free     = 458752 (0.4375MB)
   12.5% used
To Space:
   capacity = 524288 (0.5MB)
   used     = 0 (0.0MB)
   free     = 524288 (0.5MB)
   0.0% used
PS Old Generation
   capacity = 128974848 (123.0MB)
   used     = 24006808 (22.894676208496094MB)
   free     = 104968040 (100.1053237915039MB)
   18.613557893086256% used

13410 interned Strings occupying 1194568 bytes.
```

### jmap -histo pid

```text
>jmap -histo 10352

 num     #instances         #bytes  class name
----------------------------------------------
   1:      27073797      433180752  java.lang.Integer
   2:          5442       16503144  [I
   3:          5005        6143944  [B
   4:         33835        3446608  [C
   5:          9721         855448  java.lang.reflect.Method
   6:         33751         810024  java.lang.String
   7:          6494         717416  java.lang.Class
   8:         14303         457696  java.util.concurrent.ConcurrentHashMap$Node
   ...
```

### jmap -histo:live pid

统计 heap 中所有生存的对象的情况, 这个命令会先触发 gc 再统计：

```text
> jmap -histo:live 10352

 num     #instances         #bytes  class name
----------------------------------------------
   1:         27483        2670608  [C
   2:          6453         713152  java.lang.Class
   3:         27300         655200  java.lang.String
   4:         12861         411552  java.util.concurrent.ConcurrentHashMap$Node
   5:          6364         327688  [Ljava.lang.Object;
   6:          2496         219648  java.lang.reflect.Method
   7:           874         178440  [B
   ...
```

### jmap -dump:live,format=b,file=a.log pid

**执行这个命令，JVM 会将整个 heap 的信息 dump 到一个文件，heap 如果比较大的话会导致这个过程比较耗时，并且执行的过程中为了保证 dump 的信息是可靠的会暂停应用。**

该命令通常用来分析内存泄漏 OOM，通常做法是：

- 首先配置 JVM 启动参数，让 JVM 在遇到 OutOfMemoryError 时自动生成 Dump 文件

```text
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/path
```

- 使用命令 Dump Heap 信息

```text
jmap -dump:format=b,file=/path/heap.bin pid
```

- 使用 MAT 分析工具，如 jhat 命令分析 hprof 文件

### 常用命令

- 查看大对象：

```text
jmap -histo <pid>|less
```

- 查看对象数最多的对象，并按降序排序输出：

```text
jmap -histo <pid>|grep alibaba|sort -k 2 -g -r|less
```

- 查看占用内存最多的对象，并按降序排序输出：

```perl
jmap -histo <pid>|grep alibaba|sort -k 3 -g -r|less
```

## jhat

jhat 全称为 Java Virtual Machine Heap Analysis Tool，即虚拟机堆转储快照分析工具。jhat 用于分析 heapdump 文件，它会建立一个HTTP/HTML 服务器，让用户可以在浏览器上查看分析结果。 jhat 一般与 jmap 搭配使用，用于分析 jmap 生成的堆转储快照。jhat 是一个命令行工具，使用起来比较简便，但功能也相对简陋。如果条件允许的话，建议使用 JProfiler 或者 IBM HeapAnalyzer 等功能更强大的工具来分析 heapdump 文件。

### 语法

```text
jhat [options] heap-dump-file
```

option 具体选项及作用如下:

- -J< flag >：因为 jhat 命令实际上会启动一个 JVM 来执行，通过 -J 可以在启动 JVM 时传入一些启动参数。例如，-J-Xmx512m 指定运行 jhat 的 JVM 使用的最大堆内存为 512 MB。 如果需要使用多个 JVM 启动参数，则传入多个 -Jxxxxxx。
- -stack false|true：关闭跟踪对象分配调用堆栈。如果分配位置信息在堆转储中不可用，则必须将此标志设置为 false。默认值为 true。
- -refs false|true：关闭对象引用跟踪。默认情况下，返回的指针是指向其他特定对象的对象，如反向链接或输入引用(referrers or incoming references),，会统计/计算堆中的所有对象。
- -port port-number：设置 jhat HTTP server 的端口号，默认值 7000。
- -exclude exclude-file：指定对象查询时需要排除的数据成员列表文件。 例如，如果文件列出了 java.lang.String.value，那么当从某个特定对象 Object o 计算可达的对象列表时，引用路径涉及 java.lang.String.value 的都会被排除。
- -baseline exclude-file：指定一个基准堆转储(baseline heap dump)。 在两个 heap dump 文件中有相同 object ID 的对象会被标记为不是新的(marked as not being new)，其他对象被标记为新的(new)。**在比较两个不同的堆转储时很有用。**
- -debug int：设置 debug 级别，0 表示不输出调试信息。 值越大则表示输出更详细的 debug 信息。
- -version：启动后只显示版本信息就退出。

### 示例

使用 jhat 分析 jmap 生成的 hprof 文件：

![img](https://pic2.zhimg.com/80/v2-2a0769d26e575e0877783f262951652d_720w.webp)

![img](https://pic3.zhimg.com/80/v2-104bd8f621e89157b7a72b3c7a52a02e_720w.webp)

localhost:7000 页面的最底部给出了可查看信息的列表：

分别为：

- All classes including platform：所有在堆中创建对象的类，包括 JDK 中定义的类。
- Show all members of the rootset：从根集能引用到的对象。
- Show instance counts for all classes (including platform)：所有类(包括 JDK 中定义的类)的实例数量。
- Show instance counts for all classes (excluding platform)：所有类(不包括 JDK 中定义的类)的实例数量。
- Show heap histogram：堆实例的分布表。

![img](https://pic3.zhimg.com/80/v2-62892ee5832710f013e3fa03d30b9f82_720w.webp)

- Show finalizer summary：将要结束的对象的概括信息。
- Execute Object Query Language (OQL) query：执行对象查询语句，可以查看 OQL help，也可以参考博客 [https://blog.csdn.net/a772304419/article/details/104022356](https://link.zhihu.com/?target=https%3A//blog.csdn.net/a772304419/article/details/104022356) 中的例子。

### 参考链接

1. [https://xixincan.github.io/2020/05/21/Java/JVM/jhat%E5%B7%A5%E5%85%B7%E8%AF%A6%E8%A7%A3/](https://link.zhihu.com/?target=https%3A//xixincan.github.io/2020/05/21/Java/JVM/jhat%E5%B7%A5%E5%85%B7%E8%AF%A6%E8%A7%A3/)
2. https://zhuanlan.zhihu.com/p/476375727
3. [https://blog.csdn.net/a77230441](https://link.zhihu.com/?target=https%3A//blog.csdn.net/a772304419/article/details/104022356)