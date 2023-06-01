# 谈谈InnoDB引擎

[toc]

## 逻辑存储结构

一下为InnoDB 存储引擎的逻辑存储结构：

![image-20220305210029916](https://s2.loli.net/2022/03/05/sKqXaA7CgopcuY9.png)

![image-20220307202913165](https://s2.loli.net/2022/03/07/LIwGbFM67Jx5Wil.png)



## 架构

MySQL 5.5 版本开始，默认使用InnoDB存储引擎，他擅长处理事务，具有崩溃恢复特性，在日常开发中使用非常广泛。下面是 InnoDB 架构图，左侧为内存架构，右侧为磁盘架构：

![image-20220307203108714](https://s2.loli.net/2022/03/07/2WAhM8TklPsQCYz.png)



### 架构-内存架构

**Buffer Pool 缓冲池：**

![image-20220307203629462](https://s2.loli.net/2022/03/07/UDLS3tVMezr92qc.png)



**Change Buffer  更改缓冲区：**

![image-20220307204046558](https://s2.loli.net/2022/03/07/g7pfnN2hICUi4YA.png)



**Adaptive Hash Index 自适应哈希索引：**

![image-20220307204354903](https://s2.loli.net/2022/03/07/xKiMs8GVCBrA5yk.png)



**Log Buffer 日志缓冲区：**

![image-20220307205653232](https://s2.loli.net/2022/03/07/oR53OVPtvFlWDmH.png)



### 架构-磁盘结构

- System Tablespace：系统表空间
- File-Per-Table Tablespace ：每个表文件的单独表空间
- General Tablespace：通用表空间
- Temporary Tablespace ：临时表空间
- Undo Tablespace ：撤销表空间
- DoublewriteBuffer Files：双写缓冲区
- Redo Log：重做日志文件

![image-20220307205721423](https://s2.loli.net/2022/03/07/aZNRLi4P5dUXyFv.png)

![image-20220307210057408](https://s2.loli.net/2022/03/07/nJwl4MXLapq6Gei.png)

![image-20220307210424800](https://s2.loli.net/2022/03/07/lrweM64vEHz1B7D.png)



### 架构-后台线程

![image-20220307212319579](https://s2.loli.net/2022/03/07/7YHgyLrT9ZaxfMK.png)

![image-20220307212505141](https://s2.loli.net/2022/03/07/hrjEewBAYQR4xZy.png)





## 事务原理

![image-20220307212702512](https://s2.loli.net/2022/03/07/tyzfbivRZSgNIMO.png)

![image-20220307212845491](https://s2.loli.net/2022/03/07/1OKuUiSznDvbF7J.png)



### 持久性：

![image-20220307213133148](https://s2.loli.net/2022/03/07/K5VApgtnlyzBcfX.png)

![image-20220307213511343](https://s2.loli.net/2022/03/07/E3FVxftDXwlJ1p8.png)





### 原子性：

![image-20220307213726344](https://s2.loli.net/2022/03/07/jmbY1BMWQeuZCv9.png)





## MVCC

### 基本概念

![image-20220307214228193](https://s2.loli.net/2022/03/07/TYKHp6QkzmLtbVo.png)



### 实现原理

![image-20220307214839122](C:\Users\HK意境\AppData\Roaming\Typora\typora-user-images\image-20220307214839122.png)





![image-20220307215004862](https://s2.loli.net/2022/03/07/bsOW6evlH2uDdmC.png)

![image-20220307215505991](https://s2.loli.net/2022/03/07/bvKY7dFTSRfz5NP.png)

![image-20220307215526796](https://s2.loli.net/2022/03/07/n3koF45yXCrBPs7.png)





![image-20220307220035440](https://s2.loli.net/2022/03/07/qUYsZVSPu3laRFx.png)

![image-20220307220332498](https://s2.loli.net/2022/03/07/gyLYG6b1ukmA2j9.png)



#### RC 模式下的快照读工作模式：

![image-20220308084105392](https://s2.loli.net/2022/03/08/uabxzmojhAg8t91.png)





#### RR 模式下的快照读工作模式：

![image-20220308083703738](https://s2.loli.net/2022/03/08/P1Lrg2X9bzKExtH.png)



**其实说白了，ReadView 读取到得到 快照读数据，其实就是最近一次已经提交了的事务修改的数据版本。**





### 隔离性和一致性

![image-20220308084230484](https://s2.loli.net/2022/03/08/rXpwQUjVyT4SEv7.png)

