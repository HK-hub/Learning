## 谷歌Chrome浏览器访问提示：ERR_UNSAFE_PORT

4010 次浏览 读完需要≈ 3 分钟 [Tomcat](https://codeplayer.vip/tag/tomcat)

使用Google Chrome浏览器访问我搭建的测试网站时，结果无法访问，提示如下：

> **无法访问此网站**
>
> 网址为 **http://xxx.xxx.xxx.xxx:6666/** 的网页可能暂时无法连接，或者它已永久性地移动到了新网址。
>
> **ERR\*UNSAFE\*PORT**

[![Chrome浏览器上提示 ERR_UNSAFE_PORT](https://cdn.codeplayer.vip/public/2019-07/29-20190720-13293336-927699.png!cp)](https://cdn.codeplayer.vip/public/2019-07/29-20190720-13293336-927699.png!cp)Chrome浏览器上提示 ERR_UNSAFE_PORT

我在火狐浏览器上测试，也无法访问，提示：

> **此网址已被限制**
>
> 此网址使用了一个通常用于网络浏览以外目的的端口。出于安全原因，Firefox 取消了该请求。

[![Firefox浏览器提示端口被限制](https://cdn.codeplayer.vip/public/2019-07/29-20190720-133112889-203724.png!cp)](https://cdn.codeplayer.vip/public/2019-07/29-20190720-133112889-203724.png!cp)Firefox浏览器提示端口被限制

我在命令行环境通过*curl*命令是可以正常访问的。请问这个问题该如何解决呢？

评分? 0 0 编辑 举报 标记 收藏 分享 邀请回答

### 1 个回答

![img](https://codeplayer.vip/public/image/default-avatar.png)

[Ready](https://codeplayer.vip/u/ready)· 4年前

正如浏览器的英文"UNSAFE PORT"提示所述，出现这个问题，主要是因为你的Web服务器**使用了不安全的端口**。

我们都知道，在绝大多数操作系统上，一般会有6万多个端口（0~65535）可以供应用程序或服务使用。为了降低不同类型应用程序、通信协议之间出现端口占用的冲突，IANA（互联网号码分配局）提供了一些约定和建议。例如：

- 0~1023 范围内的端口，属于公认的端口，它们中的大多数都紧密绑定与一些服务。例如：80端口常用于HTTP通讯。443端口用于HTTPS（SSL）通讯；22端口用于SSH协议。
- 1024~49151 范围内的端口，属于注册端口，它们松散地绑定于一些服务。也就是说有许多服务绑定于这些端口，这些端口同样用于许多其它目的。例如：许多系统处理动态端口从1024左右开始。
- 49152~65535 范围内的端口，属于动态或私有端口，理论上，服务一般不会注册在这些端口上，可以让我们自己私有使用。

如果想了解更多的服务或传输协议的端口规范，你可以参考IANA提供的[服务或传输协议的端口注册列表](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml)，其中列出了我们常见的应用服务和传输协议应该被**建议**使用的端口号。

现代的浏览器绝不仅仅只是一个简单的HTTP(S)数据传输工具，为了满足用户的需要，它不仅仅只提供HTTP(S)服务，它也会与系统的、内置的、第三方插件的各种服务进行各种通讯交互。

为了避免与其它通讯交互产生冲突，也为了安全考虑，浏览器会限制访问一些明显与其他服务绑定、容易产生冲突的端口号。比如，Chrome浏览器在[源代码](https://chromium.googlesource.com/chromium/src.git/+/refs/heads/master/net/base/port_util.cc)中就限制了使用以下端口号的Web服务器的访问：

复制

全屏

全选

*C++:*<Chrome浏览器在源代码中限制访问的端口号>

```cpp
const int kRestrictedPorts[] = {
    1,       // tcpmux
    7,       // echo
    9,       // discard
    11,      // systat
    13,      // daytime
    15,      // netstat
    17,      // qotd
    19,      // chargen
    20,      // ftp data
    21,      // ftp access
    22,      // ssh
    23,      // telnet
    25,      // smtp
    37,      // time
    42,      // name
    43,      // nicname
    53,      // domain
    77,      // priv-rjs
    79,      // finger
    87,      // ttylink
    95,      // supdup
    101,     // hostriame
    102,     // iso-tsap
    103,     // gppitnp
    104,     // acr-nema
    109,     // pop2
    110,     // pop3
    111,     // sunrpc
    113,     // auth
    115,     // sftp
    117,     // uucp-path
    119,     // nntp
    123,     // NTP
    135,     // loc-srv /epmap
    139,     // netbios
    143,     // imap2
    179,     // BGP
    389,     // ldap
    427,     // SLP (Also used by Apple Filing Protocol)
    465,     // smtp+ssl
    512,     // print / exec
    513,     // login
    514,     // shell
    515,     // printer
    526,     // tempo
    530,     // courier
    531,     // chat
    532,     // netnews
    540,     // uucp
    548,     // AFP (Apple Filing Protocol)
    556,     // remotefs
    563,     // nntp+ssl
    587,     // smtp (rfc6409)
    601,     // syslog-conn (rfc3195)
    636,     // ldap+ssl
    993,     // ldap+ssl
    995,     // pop3+ssl
    2049,    // nfs
    3659,    // apple-sasl / PasswordServer
    4045,    // lockd
    6000,    // X11
    6665,    // Alternate IRC [Apple addition]
    6666,    // Alternate IRC [Apple addition]
    6667,    // Standard IRC [Apple addition]
    6668,    // Alternate IRC [Apple addition]
    6669,    // Alternate IRC [Apple addition]
    6697,    // IRC + TLS
};
```

因此，避免浏览器端口号限制的最简单方式，就是直接**调整Web服务器的端口号**（在上述限制的端口号范围之外）。

当然，如果你当前无法控制Web服务器的话，并且仅限于本机浏览器测试使用，你可以采取如下措施：

- Chrome浏览器：右键桌面上 【Chrome浏览器】的快捷方式，在关联菜单中点击[属性]，在[目标]栏中的追加参数 *--explicitly-allowed-ports=端口号1,端口号2*（端口号之间以英文逗号隔开）。例如将*"C:\Program Files (x86)\Google\Chrome\Application\chrome.exe"*修改为*"C:\Program Files (x86)\Google\Chrome\Application\chrome.exe --explicitly-allowed-ports=6666,7777"*。
- Firefox浏览器：在地址栏输入*about:config*，在列出的选项列表上右键点击，在弹出的关联菜单上点击【新建】 - 【字符串】，在弹出框中填写参数名称*network.security.ports.banned.override*，接着在参数值里面填写你指定的端口号即可解除对该端口号的限制。