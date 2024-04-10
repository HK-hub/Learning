# Systemd 服务最佳实践

[toc]

在很多时候我们想要自己的Java应用，Spring Boot应用，Jar包应用等部署在服务器上之后，可以通过简单的命令，脚本等就能进行对应用的启动，停止，重启等进行控制，以及希望能够让服务挂了之后自动重启、随系统自动重启。这些都需要系统Service, systemctl 命令的支持。

当然我们也需要设置几个相应的脚本，分别用来对应启动应用，关闭应用，重启应用。

# Systemctl 命令

系统服务管理器指令。**systemctl命令** 是系统服务管理器指令，它实际上将 service 和 chkconfig 这两个命令组合到一起。

**systemctl命令** 是系统服务管理器指令，它实际上将 service 和 chkconfig 这两个命令组合到一起。以下以xxx.service 作为例子进行举例

| 任务                 | 指令                                |
| -------------------- | ----------------------------------- |
| 显示所有已启动的服务 | systemctl list-units --type=service |
| 启动服务             | systemctl start xxx.service         |
| 停止服务             | systemctl stop xxx.service          |
| 重启服务             | systemctl restart xxx.service       |
| 重载服务             | systemctl reload xxx.service        |

### 实例

```shell
systemctl start nfs-server.service . # 启动nfs服务
systemctl enable nfs-server.service # 设置开机自启动
systemctl disable nfs-server.service # 停止开机自启动
systemctl status nfs-server.service # 查看服务当前状态
systemctl restart nfs-server.service # 重新启动某服务
systemctl list-units --type=service # 查看所有已启动的服务
```



# 相关命令

## 任务运行

当我们在Linux终端，执行最基本的`java -jar xxx.jar` 命令时，是在Linux前台运行的命令，此时如果我们`Ctrl + C`, 关闭终端连接，退出当前登录用户等那么都将会使我们运行的Java应用停止。

我们可以在这期间输入 `jobs` 命令察看当前shell下运行的所有程序；带+表示最新的jobs；带-表示次新的jobs；其他jobs不带符号。然后如果我们想将刚才启动的Java应用进程放到后台中运行，我们可以使用`bg %n` 命令，其中`n` 代表该任务在`jobs` 命令列表出来的任务位置。

如果我们想要Java应用启动就在后台运行，那么我们可以采用 `&` 命令，例如`java -jvar xxx.jar & ` 即可。`&`加在一个命令的最后，可以把这个命令放到后台执行。

如果让程序始终在后台执行，即使关闭当前的终端也执行（之前的&做不到），这时候需要nohup。该命令可以在你退出帐户/关闭终端之后继续运行相应的进程。关闭终端后，在另一个终端jobs已经无法看到后台跑得程序了，此时利用ps（进程查看命令）。

所以一般我们启动Java应用的方式都是在后台运行的：`nohup java -jar xxx.jar &` 。

## 进程查看

如果我们没有`jps` 等工具，我们可以采用 `ps -ef | grep java` 来查看Java进程，或者直接通过指定我们应用 jar 包的名字来进行获取进程执行信息，包括进程号PID等。

如果我们找到我们需要观察的Java应用之后，那么我们可以通过`top `命令来查看具体的进程的资源占用情况。例如`top -p 进程号`，来查看指定进程，如果我们想查看你此进程内部线程执行占用资源情况，可以`top -hp 进程号`。

当然很多情况下，我们都是需要对进程占用资源情况进行排序的，我们可以在使用 top 命令之后，按下如下交互命令：

- M：根据驻留内存大小进行排序； 
- P：根据CPU使用百分比大小进行排序；

## 文本查看

简单查看文本内容：cat，head, tail,

实时查看：tail -f 

查找查看：grep

## 标准输出





# 编写应用启动脚本

首先，需要编写 Spring Boot 应用的启动、停止、重启三个脚本，我们的程序 JAR 包是 `/path/to/xxx.jar`。

## 启动脚本

```shell
#!/bin/bash
export JAVA_HOME=/usr/java/jdk1.8.0_131	# 可选
export PATH=$JAVA_HOME/bin:$PATH	# 可选
APP_NAME=xxx
nohup java -jar /path/to/xxx.jar.jar > xxx.log 2>&1 &
echo "$APP_NAME is running"

```

其中，对于是否保留JAVA_HOME, PATH 是可选的，如果您已经配置了环境变量，那么便不需要在这里指定JAVA 环境变量，直接运行即可。但是如果你直接运行出现找不到Java命令，那么建议您先去配置JAVA的环境变量。

## 停止脚本

```shell
#!/bin/bash
APP_NAME=xxx
pid=`ps -ef | grep $APP_NAME | grep -v grep | awk '{print $2}'`
  
if [ -z "${pid}" ]; then
   echo "$APP_NAME is not running"
else
    echo "kill thread...$pid"
    kill -9 $pid
fi
```

停止应用的方式很多，命令也很多，普遍的做法是通过 `ps` 命令查找出对应的进程ID，然后使用 `kill` 命令进行杀死进程。当然也可以通过`fuser` 等命令找到我们的Java应用占用的端口号，然后杀死占用该端口号的进程。不过这种方式存在一定的局限性，需要我们的应用是网络应用，且当占用端口变更的时候，停止脚本需要相应变化。

## 重启脚本

```shell
#!/bin/bash
export JAVA_HOME=/usr/java/jdk1.8.0_131
export PATH=$JAVA_HOME/bin:$PATH
APP_NAME=xxx
pid=`ps -ef | grep $APP_NAME | grep -v grep | awk '{print $2}'`
  
if [ -z "${pid}" ]; then
   echo "$APP_NAME is not running"
else
    echo "kill thread...$pid"
    kill -9 $pid
fi

nohup java -jar /path/to/xxx.jar > xxx.log 2>&1 &
echo "$APP_NAME is running"
```

重启脚本其实也就是停止脚本和启动脚本的融合，简单来说就是如果我们的Java应用如果还在运行那就将它杀死，最后都统一执行启动脚本。



以上三个脚本：

- `start.sh` 是启动脚本，提供 Java 的路径，执行 JAR 文件的启动命令，输出信息。
- `stop.sh` 是停止脚本，通过 `ps` 命令，找到程序对应进程的 PID，如果不存在，说明程序没有运行，不要任何操作，如果存在，则用 `kill` 命令将其杀掉。
- `restart.sh` 是重启脚本，就是将以上两者结合，先终止程序，再启动。

将这三个脚本放在 `/path/to/bin/` 目录中。



# 将应用设置成为系统服务

有了以上三个脚本，现在我们配置系统服务（假设服务命名为 `xxx`）。在 `/etc/systemd/system   ` 或者 `/usr/lib/systemd/system/` 目录中，创建 `xxx.service` 文件，优先推荐在`/etc/systemd/system` 目录。其内容为：

```shell
[Unit]
Description=xxx
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
ExecStart=/path/to/bin/start.sh
ExecReload=/path/to/bin/restart.sh
ExecStop=/path/to/bin/stop.sh
Restart=always
RestartSec=5
StartLimitInterval=0
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

在这个文件中，有三个部分：

- Unit 区块中，包含了描述内容和启动顺序的配置，After 表示在这些服务启动之后启动。
- Service 区块主要配置启动行为，使用了之前的三个脚本文件，分别作为服务的启动、终止、重启的脚本。
- Install 区块配置如何安装这个配置文件，WantedBy=multi-user.target 表示这个服务所在的 Target 是 multi-user.target。

当然对于其中重要的参数需要进行讲解以下：

1. ExecStart: 表示启动需要执行的命令
2. ExecStop: 表示停止时会执行的命令
3. ExecReload: 表示重启时会执行的命令
4. Restart=always: 只要不是通过systemctl stop来停止服务，任何情况下都必须要重启服务，默认值为no
5. RestartSec=5: 重启间隔，比如某次异常后，等待5(s)再进行启动，默认值0.1(s)
6. StartLimitInterval: 无限次重启，默认是10秒内如果重启超过5次则不再重启，设置为0表示不限次数重启



## 案例需求

需求：有个业务，当程序因受到OOM而退出的时候，不希望自动重启（此时需要人工介入排查），其他情况下可以自动重启

分析：OOM就是通过kill -9来杀进程，因此只要找到方法，告诉systemd当该服务遇到kill -9时候不自动重启即可

`RestartPreventExitStatus参数` 

查询man systemd.service发现，systemd的**[Service]**段落里支持一个参数，叫做RestartPreventExitStatus。该参数从字面上看，意思是当符合某些退出状态时不要进行重启。该参数的值支持exit code和信号名2种，可写多个，以空格分隔，例如：

```shell
RestartPreventExitStatus=143 137 SIGTERM SIGKILL
```

表示，当退出情况只要符合以下4种情况中任意一种时候，则不再进行重启

- exit code为143: 表示systemd认为主进程自行退出的，exit code为143

- exit code为137: 若kill -9 最后一个子进程PID，此时主进程状态为 code=exited,status=137

- 信号为TERM: 若kill 主进程PID（kill不带参数），则主进程状态为 code=killed,signal=TERM

- 信号为KILL: 表示systemd认为主进程是被kill的，接收到的信号是SIGKILL





# 控制

有了这个配置文件，就可以使用一下命令来操作这个服务：

```shell
# 启动服务
systemctl start xxx
# 停止服务
systemctl stop xxx
# 重启服务
systemctl restart xxx
# 查看启动状态
systemctl status xxx
```

如果要让服务随系统启动，只需要执行如下命令：

```sh
systemctl enable xxx
```

如果要取消随系统启动：

```sh
systemctl disable xxx
```





# 参考文献

## 教程文献

[systemd.service 中文手册](https://www.jinbuguo.com/systemd/systemd.service.html)

[Linux配置service服务](https://www.cnblogs.com/ggzhangxiaochao/p/15039617.html)

##  问题文献

[Systemd State 'stop-sigterm' timed out](https://superuser.com/questions/1146388/systemd-state-stop-sigterm-timed-out)