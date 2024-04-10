# IM即时通讯系统



## 预览地址

[im.gzydong.club](http://im.gzydong.club/)



## 代码地址

- 后端代码地址：https://github.com/HK-hub/HotKeyIM
- 前端代码地址：https://github.com/HK-hub/HotKeyIM-Client ，前端代码 fork 于 https://github.com/gzydong/LumenIM 项目并进行一系列修改和适配，如有需要可以自行fork 开发。



## 开发流程

### 技术准备

- Spring Boot
- MySQL，Redis，MeiliSearch(ElasticSearch)
- Netty
- MinIO，OSS
- RocketMQ
- Flink
- WebRTC
- Node.js
- Nginx

### 中间件

建议把所有中间件安装部署在服务器上，需要准备三台服务器(低性能但是多), 一台作为应用服务器，一台作为中间件服务器，一台作为数据服务器。

需要的中间件大多数可以使用Docker 部署，但是和存储数据相关的(MySQL, Redis 等)建议部署在原生服务器。

需要的中间件大致为：Redis, RocketMQ, MinIO，KKFileView ....  后续用到那些中间件可以再自行部署即可



## 部署流程

搭建中间件服务，

部署后端应用服务，并进行Nginx代理

部署前端服务，并进行Nginx 代理

