# MySQL 锁那些事

[toc]

## 概述

![image-20220305111843803](https://s2.loli.net/2022/03/05/grqaJ4h6Pz23lQc.png)

![image-20220305111942018](https://s2.loli.net/2022/03/05/8sfve6UHKkJtINA.png)





## 全局锁

![image-20220305112111378](https://s2.loli.net/2022/03/05/AnIMgZEzPt1axR7.png)

![不加全局锁造成数据不一致](https://s2.loli.net/2022/03/05/N9e6Qhc4TCElgv7.png)

如上图所示，如果不全局锁，会造成数据不一致的严重问题。



![image-20220305113035461](https://s2.loli.net/2022/03/05/BGnzRi3S7JOsocq.png)

![image-20220305135917941](https://s2.loli.net/2022/03/05/ITijMGhtZCvOYDw.png)





## 表级锁

![image-20220305140058581](https://s2.loli.net/2022/03/05/BARwLDrmlGfvWiV.png)





### 表锁

对于表锁，又可以分为两类：

- 表共享读锁(read lock)
- 表独占写锁(write lock)



语法：

1. 加锁：lock tables 表名... read/write 。
2. 解锁：unlock tables / 或者是断开客户端连接



**表共享读锁：**是指对于所有客户端来说都只能对加锁的表进行读操作，而不能进行写操作，包括加锁的客户端。

![image-20220305140506845](https://s2.loli.net/2022/03/05/LFv9EwXpiG5Ang6.png)



**表独占写锁：**是指针对于加锁客户端来说可读可写，但是对于其他客户端来说，既不能读也不能写。

![image-20220305140845455](https://s2.loli.net/2022/03/05/4bEHk8VadFwTCgI.png)







### 元数据锁

![image-20220305163110768](https://s2.loli.net/2022/03/05/f9miPnzReHFXuVC.png)

![image-20220305163343175](https://s2.loli.net/2022/03/05/NlUncS1wgoRjprI.png)







### 意向锁

![image-20220305164015626](https://s2.loli.net/2022/03/05/ZjJBN2l7CXV9Qsd.png)

意向锁可以分为以下两种：

1. 意向共享锁(IS)：与表共享锁（read）兼容，与表锁排他锁（write）互斥。
2. 意向排他锁(IX)：与表共享锁（read）及排他锁（write）都互斥。意向锁之间不会互斥。

![image-20220305164725769](https://s2.loli.net/2022/03/05/CsHlX3mZfh174vU.png)



以下是意向锁的执行情况：

![image-20220305164024394](https://s2.loli.net/2022/03/05/NGmMQ7Sdy8eIHYD.png)

![image-20220305164130795](https://s2.loli.net/2022/03/05/3eE8ZksADWPiNrV.png)







## 行级锁

![image-20220305165809439](C:\Users\HK意境\AppData\Roaming\Typora\typora-user-images\image-20220305165809439.png)

![image-20220305165817105](C:\Users\HK意境\AppData\Roaming\Typora\typora-user-images\image-20220305165817105.png)

**间隙锁示意图：**

![image-20220305165859114](https://s2.loli.net/2022/03/05/RAFt4SXEpIk9uzN.png)

**临键锁示意图：**

![image-20220305170054642](https://s2.loli.net/2022/03/05/i8dPVSKzo9pkcwW.png)





### InnoDB 实现的行锁：三级封锁协议

![image-20220305170227507](https://s2.loli.net/2022/03/05/sOuFoAk4qmhpDrB.png)

![image-20220305170417108](https://s2.loli.net/2022/03/05/1Ryn4PvIHZbB9fV.png)

<img src="https://s2.loli.net/2022/03/05/9CAQcNhefiEuaH5.png" alt="image-20220305170526020" style="zoom:80%;" />



![image-20220305171247389](https://s2.loli.net/2022/03/05/4aVe1ZnYXKbl9Rg.png)



### 间隙锁/临键锁

![image-20220305201703520](https://s2.loli.net/2022/03/05/HVlxLkFhEDrOPNq.png)

![image-20220305201816050](https://s2.loli.net/2022/03/05/3tmAa1CchBkOYxi.png)