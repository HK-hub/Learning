# 方法的初始Frame

[toc]

## 1. Frame内存结构

JVM Architecture由Class Loader SubSystem、Runtime Data Areas和Execution Engine三个主要部分组成，如下图所示。其中，Runtime Data Areas包括Method Area、Heap Area、Stack Area、PC Registers和Native Method Stack等部分。

![JVM Architecture](https://lsieun.github.io/assets/images/java/jvm/jvm-architecture.png)

在程序运行的过程中，每一个线程（Thread）都对应一个属于自己的**JVM Stack**。当一个新线程（Thread）开始的时候，就会在内存上分配一个属于自己的JVM Stack；当该线程（Thread）执行结束的时候，相应的JVM Stack内存空间也就被回收了。

在JVM Stack当中，是栈的结构，里面存储的是frames；每一个frame空间可以称之为**Stack Frame**。当调用一个新方法的时候，就会在JVM Stack上分配一个frame空间（入栈操作）；当方法退出时，相应的frame空间也会JVM Stack上进行清除掉（出栈操作）。

在Stack Frame内存空间当中，有两个重要的结构，即**local variables**和**operand stack**。

![JVM Stack Frame](https://lsieun.github.io/assets/images/java/asm/frame-local-variables-operand-stack.png)

在Stack Frame当中，operand stack是一个栈的结构，遵循“后进先出”（LIFO）的规则，local variables则是一个数组，索引从`0`开始。

对于每一个方法来说，它都是在自己的Stack Frame上来运行的：

- 在编译的时候（compile time），local variables和operand stack的空间大小就确定下来了。比如说，一个`.java`文件经过编译之后，得到`.class`文件，对于其中的某一个方法来说，它的local variable占用10个slot空间，operand stack占用4个slot空间。
- 在运行的时候（run-time），在local variables和operand stack上存放的数据，会随着方法的执行，不断发生变化。

那么，在运行的时候（run-time），刚进入方法，但还没有执行任何指令（instruction），那么，此时、此刻，local variables和operand stack是一个什么样的状态呢？

**万物皆有始，天地亦有终，超脱轮回者，不在名利中。**

在Stack Frame空间当中，local variables和operand stack会有**一个开始的状态**和**一个结束的状态**。

## 2. 方法的初始Frame

在方法刚开始的时候，operand stack是空的，不需要存储任何的数据，而local variables的初始状态，则需要考虑两个因素：

- 是否需要存储

    ```plaintext
    this
    ```

    ？通过判断当前方法是否为static方法。

    - 如果当前方法是static方法，则不需要存储`this`。
    - 如果当前方法是non-static方法，则需要在local variables索引为`0`的位置存在一个`this`变量。

- 当前方法是否接收参数。方法接收的参数，会按照参数的声明顺序放到local variables当中。

    - 如果方法参数不是`long`和`double`类型，那么它在local variable当中占用1个位置。
    - 如果方法的参数是`long`或`double`类型，那么它在local variable当中占用2个位置。

### 2.1. static方法

假设`HelloWorld`当中有一个静态`add(int, int)`方法，如下所示：

```java
public class HelloWorld {
    public static int add(int a, int b) {
        return a + b;
    }
}
```

我们可以通过运行`HelloWorldFrameCore`类，来查看`add(int, int)`方法的初始Frame：

```
[int, int] []
```

在上面的结果中，第一个`[]`中存放的是local variables的数据，在第二个`[]`中存放的是operand stack的数据。

在`HelloWorldFrameTree`类当中，使用`print(owner, mn, 0)`可以输出以下内容：

```
add:(II)I
┌───────────────────────────────────────────────────────────┐
│                                                           │   locals: 2
│    operand stack                                          │   stacks: 0
│    ┌───────────────────────────┐                          │
│    │                           │      local variable      │   0: I
│    ├───────────────────────────┤      ┌─────┬─────┐       │   1: I
│    │                           │      │  0  │  1  │       │
│    └───────────────────────────┘      └─────┴─────┘       │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

该方法包含的Instruction内容如下（使用`javap -c sample.HelloWorld`命令查看）：

```java
public static int add(int, int);
  Code:
     0: iload_0
     1: iload_1
     2: iadd
     3: ireturn
```

该方法整体的Frame变化如下：

```
add(II)I
[int, int] []
[int, int] [int]
[int, int] [int, int]
[int, int] [int]
[] []
```

或者：

```
                               // {int, int} | {}
0000: iload_0                  // {int, int} | {int}
0001: iload_1                  // {int, int} | {int, int}
0002: iadd                     // {int, int} | {int}
0003: ireturn                  // {} | {}
```

### 2.2. non-static方法

假设`HelloWorld`当中有一个非静态`add(int, int)`方法，如下所示：

```java
public class HelloWorld {
    public int add(int a, int b) {
        return a + b;
    }
}
```

我们可以通过运行`HelloWorldFrameCore`类，来查看`add(int, int)`方法的初始Frame：

```
[sample/HelloWorld, int, int] []
```

在`HelloWorldFrameTree`类当中，使用`print(owner, mn, 0)`可以输出以下内容：

```
add:(II)I
┌─────────────────────────────────────────────────────────────┐
│                                                             │   locals: 3
│    operand stack                                            │   stacks: 0
│    ┌───────────────────────────┐                            │
│    │                           │      local variable        │   0: HelloWorld
│    ├───────────────────────────┤      ┌─────┬─────┬─────┐   │   1: I
│    │                           │      │  0  │  1  │  2  │   │   2: I
│    └───────────────────────────┘      └─────┴─────┴─────┘   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

该方法包含的Instruction内容如下：

```
public int add(int, int);
  Code:
     0: iload_1
     1: iload_2
     2: iadd
     3: ireturn
```

该方法整体的Frame变化如下：

```
add(II)I
[sample/HelloWorld, int, int] []
[sample/HelloWorld, int, int] [int]
[sample/HelloWorld, int, int] [int, int]
[sample/HelloWorld, int, int] [int]
[] []
```

或者：

```
add:(II)I
                               // {this, int, int} | {}
0000: iload_1                  // {this, int, int} | {int}
0001: iload_2                  // {this, int, int} | {int, int}
0002: iadd                     // {this, int, int} | {int}
0003: ireturn                  // {} | {}
```

### 2.3. long和double类型

假设`HelloWorld`当中有一个非静态`add(long, long)`方法，如下所示：

```java
public class HelloWorld {
    public long add(long a, long b) {
        return a + b;
    }
}
```

我们可以通过运行`HelloWorldFrameCore`类，来查看`add(long, long)`方法的初始Frame：

```
[sample/HelloWorld, long, top, long, top] []
```

在`HelloWorldFrameTree`类当中，使用`print(owner, mn, 0)`可以输出以下内容：

```
add:(JJ)J
┌───────────────────────────────────────────────────────────────────────────┐
│                                                                           │   locals: 5
│    operand stack                                                          │   stacks: 0
│    ┌───────────────────────────┐                                          │
│    │                           │                                          │   0: HelloWorld
│    ├───────────────────────────┤                                          │   1: J
│    │                           │                                          │   2: .
│    ├───────────────────────────┤                                          │   3: J
│    │                           │      local variable                      │   4: .
│    ├───────────────────────────┤      ┌─────┬─────┬─────┬─────┬─────┐     │
│    │                           │      │  0  │  1  │  2  │  3  │  4  │     │
│    └───────────────────────────┘      └─────┴─────┴─────┴─────┴─────┘     │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
```

该方法包含的Instruction内容如下：

```
public long add(long, long);
  Code:
     0: lload_1
     1: lload_3
     2: ladd
     3: lreturn
```

该方法整体的Frame变化如下：

```
add(JJ)J
[sample/HelloWorld, long, top, long, top] []
[sample/HelloWorld, long, top, long, top] [long, top]
[sample/HelloWorld, long, top, long, top] [long, top, long, top]
[sample/HelloWorld, long, top, long, top] [long, top]
[] []
```

或者：

```
add:(JJ)J
                               // {this, long, top, long, top} | {}
0000: lload_1                  // {this, long, top, long, top} | {long, top}
0001: lload_3                  // {this, long, top, long, top} | {long, top, long, top}
0002: ladd                     // {this, long, top, long, top} | {long, top}
0003: lreturn                  // {} | {}
```

## 3. 总结

本文对方法初始的Frame进行了介绍，内容总结如下：

- 第一点，在JVM当中，每一个方法的调用都会分配一个Stack Frame内存空间；在Stack Frame内存空间当中，有local variables和operand stack两个重要结构；在Java文件进行编译的时候，方法对应的local variables和operand stack的大小就决定了。
- 第二点，如何计算方法的初始Frame。在方法刚开始的时候，Stack Frame中的operand stack是空的，而只需要计算local variables的初始状态；而计算local variables的初始状态，则需要考虑当前方法是否为static方法、是否接收方法参数、方法参数中是否有`long`和`double`类型。