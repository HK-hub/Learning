# NIO 学习笔记

[toc]

## 零拷贝

### 内核态和核心态

![image-20220327210633697](https://s2.loli.net/2022/03/27/BbCEvhlFjP9x3RQ.png)

 

## 五种 I/O 模型

### 阻塞式IO

![image-20220327211309711](https://s2.loli.net/2022/03/27/Vag1Y62U3uyQqKE.png)

![image-20220327211257769](https://s2.loli.net/2022/03/27/dLBq1y5isA8Ow79.png)



### 非阻塞式IO

![image-20220327211634476](https://s2.loli.net/2022/03/27/VxECKtrfDvQmRBa.png)

![image-20220327211750622](https://s2.loli.net/2022/03/27/zBCyHW5cTGqf13j.png)



### IO多路复用模型

![image-20220327213613850](https://s2.loli.net/2022/03/27/Z4CYE5v2GFfknIp.png)

![image-20220327213632301](https://s2.loli.net/2022/03/27/SWV9IYOlXwhJ1Cx.png)

 ![image-20220327214013842](https://s2.loli.net/2022/03/27/jnRbtoUD8Giw2SO.png)

 



### 信号驱动IO模型

 ![image-20220327214441663](https://s2.loli.net/2022/03/27/LBR3wkza2EuFfji.png)

![image-20220327214348571](https://s2.loli.net/2022/03/27/DwIfi1v39SqkjAW.png)







### 异步IO模型



![image-20220327214717870](https://s2.loli.net/2022/03/27/6DAXscHVMBZjE4L.png)

![image-20220327214729652](https://s2.loli.net/2022/03/27/zpcmMgTUt58Wrs6.png)

​	



## IO多路复用模型

 ![image-20220327215113792](https://s2.loli.net/2022/03/27/XdSJIGO1ey5MhbN.png)

![gkapjbu5qTNsLDJ](https://s2.loli.net/2022/04/13/NoAEik1LwMUKz5Z.png)

![image-20220327215403869](https://s2.loli.net/2022/03/27/S39ODod7I4HacnN.png)



### select 实现 IO 多路复用

![image-20220327215702898](https://s2.loli.net/2022/03/27/i6G2EQ8z5ktAebT.png)

![7PgGLUo8xJhcHMO](https://s2.loli.net/2022/04/13/ha1lsC35EwPYeKx.png)

![image-20220327215909643](https://s2.loli.net/2022/03/27/NpAFHWt69vLBQRs.png)

![image-20220327220158462](https://s2.loli.net/2022/03/27/u3YpUWgek48Ocnw.png)

![BC2XqYciDxoeaWJ](https://s2.loli.net/2022/04/13/np5NxoBP6K3CmEQ.png)



### poll 实现 IO 多路复用

![image-20220328212318137](https://s2.loli.net/2022/03/28/p4CcrIgFXS7biEk.png)



### epoll 实现 IO 多路复用

![image-20220328212808715](C:\Users\HK意境\AppData\Roaming\Typora\typora-user-images\image-20220328212808715.png)

![image-20220328212822034](C:\Users\HK意境\AppData\Roaming\Typora\typora-user-images\image-20220328212822034.png)

![image-20220328213210613](C:\Users\HK意境\AppData\Roaming\Typora\typora-user-images\image-20220328213210613.png)

 





## Buffer

![image-20220328214044326](https://s2.loli.net/2022/03/28/iMTLnW6v9PUxZuq.png)

![image-20220328214826062](https://s2.loli.net/2022/03/28/se1TNHZSLFO7gbi.png)

 

![image-20220328214556964](https://s2.loli.net/2022/03/28/WOfmyceJEgrHiwV.png)

![image-20220328214607980](https://s2.loli.net/2022/03/28/a45o3ZVzsPvrn9l.png)

![image-20220328214858135](https://s2.loli.net/2022/03/28/dbJHfkozXDu62jy.png)

![image-20220328215045329](https://s2.loli.net/2022/03/28/V1KLhaOQTvltxBg.png)







