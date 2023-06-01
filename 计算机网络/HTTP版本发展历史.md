# 关于HTTP版本及其发展你都明白嘛？

## 一、HTTP 的历史

早在 HTTP 建立之初，主要就是为了将超文本标记语言(HTML)文档从Web服务器传送到客户端的浏览器。也是说对于前端来说，我们所写的HTML页面将要放在我们的 web 服务器上，用户端通过浏览器访问url地址来获取网页的显示内容，但是到了 WEB2.0 以来，我们的页面变得复杂，不仅仅单纯的是一些简单的文字和图片，同时我们的 HTML 页面有了 CSS，Javascript，来丰富我们的页面展示，当 ajax 的出现，我们又多了一种向服务器端获取数据的方法，这些其实都是基于 HTTP 协议的。同样到了移动互联网时代，我们页面可以跑在手机端浏览器里面，但是和 PC 相比，手机端的网络情况更加复杂，这使得我们开始了不得不对 HTTP 进行深入理解并不断优化过程中。

![图片](http://mmbiz.qpic.cn/mmbiz_png/cmOLumrNib1cfBOtIMQ6JfSibJdd6QkQribgQuEeJaevuN9LRgQ9WR85hRiaVISeia7SDz1aU9hAAgO33XFaJ3FhmhQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



## 二、HTTP 的基本优化

影响一个 HTTP 网络请求的因素主要有两个：**带宽和延迟。**

- **带宽：**如果说我们还停留在拨号上网的阶段，带宽可能会成为一个比较严重影响请求的问题，但是现在网络基础建设已经使得带宽得到极大的提升，我们不再会担心由带宽而影响网速，那么就只剩下延迟了。

- **延迟：**

- - 浏览器阻塞（HOL blocking）：浏览器会因为一些原因阻塞请求。浏览器对于同一个域名，同时只能有 4 个连接（这个根据浏览器内核不同可能会有所差异），超过浏览器最大连接数限制，后续请求就会被阻塞。
    - DNS 查询（DNS Lookup）：浏览器需要知道目标服务器的 IP 才能建立连接。将域名解析为 IP 的这个系统就是 DNS。这个通常可以利用DNS缓存结果来达到减少这个时间的目的。
    - 建立连接（Initial connection）：HTTP 是基于 TCP 协议的，浏览器最快也要在第三次握手时才能捎带 HTTP 请求报文，达到真正的建立连接，但是这些连接无法复用会导致每次请求都经历三次握手和慢启动。三次握手在高延迟的场景下影响较明显，慢启动则对文件类大请求影响较大。



## **三、HTTP1.0和HTTP1.1的一些区别**

HTTP1.0最早在网页中使用是在1996年，那个时候只是使用一些较为简单的网页上和网络请求上，而HTTP1.1则在1999年才开始广泛应用于现在的各大浏览器网络请求中，同时HTTP1.1也是当前使用最为广泛的HTTP协议。 主要区别主要体现在：

1. **缓存处理**，在HTTP1.0中主要使用header里的If-Modified-Since,Expires来做为缓存判断的标准，**HTTP1.1则引入了更多的缓存控制策略**例如Entity tag，If-Unmodified-Since, If-Match, If-None-Match等更多可供选择的缓存头来控制缓存策略。

2. **带宽优化及网络连接的使用**，HTTP1.0中，存在一些浪费带宽的现象，例如客户端只是需要某个对象的一部分，而服务器却将整个对象送过来了，并且<u>不支持断点续传功能</u>，HTTP1.1则在**请求头引入了range头域**，它允许只请求资源的某个部分，即返回码是206（Partial Content），这样就方便了开发者自由的选择以便于充分利用带宽和连接。

3. **错误通知的管理**，在<u>HTTP1.1中新增了24个错误状态响应码</u>，如409（Conflict）表示请求的资源与资源的当前状态发生冲突；410（Gone）表示服务器上的某个资源被永久性的删除。

4. **Host头处理**，在HTTP1.0中认为每台服务器都绑定一个唯一的IP地址，因此，请求消息中的URL并没有传递主机名（hostname）。但随着虚拟主机技术的发展，在一台物理服务器上可以存在多个虚拟主机（Multi-homed Web Servers），并且它们共享一个IP地址。HTTP1.1的请求消息和响应消息都应支持Host头域，且请求消息中如果没有Host头域会报告一个错误（400 Bad Request）。

5. **长连接**，HTTP 1.1支持**长连接**（PersistentConnection）和**请求的流水线（Pipelining）处理**，在一个TCP连接上可以传送多个HTTP请求和响应，减少了建立和关闭连接的消耗和延迟，在HTTP1.1中默认开启Connection： **keep-alive**，一定程度上弥补了HTTP1.0每次请求都要创建连接的缺点。

    

## **四、HTTPS与HTTP的一些区别**

- HTTPS协议需要到CA申请证书，一般免费证书很少，需要交费。
- HTTP协议运行在TCP之上，所有传输的内容都是明文，HTTPS运行在SSL/TLS之上，SSL/TLS运行在TCP之上，所有传输的内容都经过加密的。
- HTTP和HTTPS使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。
- HTTPS可以有效的防止运营商劫持，解决了防劫持的一个大问题。

![图片](http://mmbiz.qpic.cn/mmbiz_png/cmOLumrNib1cfBOtIMQ6JfSibJdd6QkQribXL5PwzkqQdmyY9egu2hpzzMCgz2F5HhhkdSNc5eYJ9UGMDBGjeCGiag/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

## **五、SPDY：HTTP1.x的优化**

2012年google如一声惊雷提出了SPDY的方案，优化了HTTP1.X的请求延迟，解决了HTTP1.X的安全性，具体如下：

1. **降低延迟**，针对HTTP高延迟的问题，SPDY优雅的采取了多路复用（multiplexing）。多路复用通过多个请求stream共享一个tcp连接的方式，解决了HOL blocking的问题，降低了延迟同时提高了带宽的利用率。
2. **请求优先级**（request prioritization）。多路复用带来一个新的问题是，在连接共享的基础之上有可能会导致关键请求被阻塞。SPDY允许给每个request设置优先级，这样重要的请求就会优先得到响应。比如浏览器加载首页，首页的html内容应该优先展示，之后才是各种静态资源文件，脚本文件等加载，这样可以保证用户能第一时间看到网页内容。
3. **header压缩。**前面提到HTTP1.x的header很多时候都是重复多余的。选择合适的压缩算法可以减小包的大小和数量。
4. **基于HTTPS的加密协议传输**，大大提高了传输数据的可靠性。
5. **服务端推送**（server push），采用了SPDY的网页，例如我的网页有一个sytle.css的请求，在客户端收到sytle.css数据的同时，服务端会将sytle.js的文件推送给客户端，当客户端再次尝试获取sytle.js时就可以直接从缓存中获取到，不用再发请求了。SPDY构成图：

![图片](http://mmbiz.qpic.cn/mmbiz_png/cmOLumrNib1cfBOtIMQ6JfSibJdd6QkQribjhshzcKo97UNNVIFgpOYZic95drsxo5TaiadPSSmcYhOI7GYAO99W6Sw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

​	SPDY位于HTTP之下，TCP和SSL之上，这样可以轻松兼容老版本的HTTP协议(将HTTP1.x的内容封装成一种新的frame格式)，同时可以使用已有的SSL功能。



## **六、HTTP2.0性能惊人**

**HTTP/2: the Future of the Internet** https://link.zhihu.com/?target=https://http2.akamai.com/demo 是 Akamai 公司建立的一个官方的演示，用以说明 HTTP/2 相比于之前的 HTTP/1.1 在性能上的大幅度提升。 同时请求 379 张图片，从Load time 的对比可以看出 HTTP/2 在速度上的优势。

![图片](http://mmbiz.qpic.cn/mmbiz_png/cmOLumrNib1cfBOtIMQ6JfSibJdd6QkQribcBaVkwLTtI3YprKtxYXhyYq7N1N70iaiaUxHFzc05wxQlSFrz3ltzrgw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



## 七、HTTP2.0：SPDY的升级版

HTTP2.0可以说是SPDY的升级版（其实原本也是基于SPDY设计的），但是，HTTP2.0 跟 SPDY 仍有不同的地方，如下：
		**HTTP2.0和SPDY的区别：**

1. HTTP2.0 支持明文 HTTP 传输，而 SPDY 强制使用 HTTPS
2. HTTP2.0 消息头的压缩算法采用 **HPACK**，而非 SPDY 采用的 **DEFLATE**



## **八、HTTP2.0和HTTP1.X相比的新特性**

- **新的二进制格式**（Binary Format），HTTP1.x的解析是基于文本。基于文本协议的格式解析存在天然缺陷，文本的表现形式有多样性，要做到健壮性考虑的场景必然很多，二进制则不同，只认0和1的组合。基于这种考虑HTTP2.0的协议解析决定采用二进制格式，实现方便且健壮。
- **多路复用**（MultiPlexing），即连接共享，即每一个request都是是用作连接共享机制的。一个request对应一个id，这样一个连接上可以有多个request，每个连接的request可以随机的混杂在一起，接收方可以根据request的 id将request再归属到各自不同的服务端请求里面。
- **header压缩**，如上文中所言，对前面提到过HTTP1.x的header带有大量信息，而且每次都要重复发送，HTTP2.0使用encoder来减少需要传输的header大小，通讯双方各自cache一份header fields表，既避免了重复header的传输，又减小了需要传输的大小。
- **服务端推送**（server push），同SPDY一样，HTTP2.0也具有server push功能。



## **九、HTTP2.0的升级改造**

- 前文说了HTTP2.0其实可以支持非HTTPS的，但是现在主流的浏览器像chrome，firefox表示还是只支持基于 TLS 部署的HTTP2.0协议，所以要想升级成HTTP2.0还是先升级HTTPS为好。
- 当你的网站已经升级HTTPS之后，那么升级HTTP2.0就简单很多，如果你使用NGINX，只要在配置文件中启动相应的协议就可以了，可以参考**[NGINX白皮书，NGINX配置HTTP2.0官方指南](https://www.nginx.com/blog/nginx-1-9-5/)** 。
- 使用了HTTP2.0那么，原本的HTTP1.x怎么办，这个问题其实不用担心，HTTP2.0完全兼容HTTP1.x的语义，对于不支持HTTP2.0的浏览器，NGINX会自动向下兼容的。



## **十、附注**

**HTTP2.0的多路复用和HTTP1.X中的长连接复用有什么区别？**

- HTTP/1.* 一次请求-响应，建立一个连接，用完关闭；每一个请求都要建立一个连接；
- HTTP/1.1 Pipeling解决方式为，若干个请求排队串行化单线程处理，后面的请求等待前面请求的返回才能获得执行机会，一旦有某请求超时等，后续请求只能被阻塞，毫无办法，也就是人们常说的线头阻塞；
- HTTP/2多个请求可同时在一个连接上并行执行。某个请求任务耗时严重，不会影响到其它连接的正常执行；
    具体如图：

![图片](http://mmbiz.qpic.cn/mmbiz_png/cmOLumrNib1cfBOtIMQ6JfSibJdd6QkQriba5ygCTOOjIQH4wvoJS2iaFBseyEAUfvpJQThHmTjuGuaSspUo8xppiaA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**服务器推送到底是什么？**
		服务端推送能把客户端所需要的资源伴随着index.html一起发送到客户端，省去了客户端重复请求的步骤。正因为没有发起请求，建立连接等操作，所以静态资源通过服务端推送的方式可以极大地提升速度。具体如下：

- 普通的客户端请求过程：

![图片](http://mmbiz.qpic.cn/mmbiz_png/cmOLumrNib1cfBOtIMQ6JfSibJdd6QkQribkZLpDyHlTbAGkEiazqLfjkTSfMgib2UlC0p3Yw0T3iaaHcvLjL22PZWPg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



- 服务端推送的过程：

![图片](http://mmbiz.qpic.cn/mmbiz_png/cmOLumrNib1cfBOtIMQ6JfSibJdd6QkQribq79os9yK2JmEODZqRVBweS7uMP2WWz4Ij6Z1f9TuiaXANOozhwCWljw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**为什么需要头部压缩？**
		假定一个页面有100个资源需要加载（这个数量对于今天的Web而言还是挺保守的）, 而每一次请求都有1kb的消息头（这同样也并不少见，因为Cookie和引用等东西的存在）, 则至少需要多消耗100kb来获取这些消息头。HTTP2.0可以维护一个字典，差量更新HTTP头部，大大降低因头部传输产生的流量。具体参考：HTTP/2 头部压缩技术介绍



**HTTP2.0多路复用有多好？**
		HTTP 性能优化的关键并不在于高带宽，而是低延迟。TCP 连接会随着时间进行自我「调谐」，起初会限制连接的最大速度，如果数据成功传输，会随着时间的推移提高传输的速度。这种调谐则被称为 TCP 慢启动。由于这种原因，让原本就具有突发性和短时性的 HTTP 连接变的十分低效。
HTTP/2 通过让所有数据流共用同一个连接，可以更有效地使用 TCP 连接，让高带宽也能真正的服务于 HTTP 的性能提升。





## 崭新的 HTTP 3.0

HTTP3 代表着充满魅力的未来，它的 HTTP 基础潜能已经被谷歌的那些极客发挥到极致。想要明白HTTP3.0 的产生和解决了什么问题，那就看下文分享吧。

HTTP3 的主要改进在传输层上。传输层不会再有我前面提到的那些繁重的 TCP 连接了。现在，一切都会走 UDP。HTTP 3 放弃了坚持使用 TCP 作为可靠传输的传输层协议，也放弃使用了 SPDY 那一套，而是在SPDY 的基础之上进行改造，继续扩展，最后产出了 QUIC 协议：

QUIC 的意思是“快速 UDP Internet 连接”。协议的这种更改将显著加快连接建立和数据传输的速度。然而，虽说 UDP 肯定更快、更简单，但它不具备 TCP 的可靠性和错误处理能力。所以我们的HTTP3 对UDP 协议进行了改造扩展，能够支持重传，拥塞控制，调整传输节奏等保证可靠传输的能力。

![img](https://img-blog.csdnimg.cn/img_convert/47e40fe175290fbf5465c2c677f29603.png)

TCP 必须进行多次往返，才能以方形且稳定的方式建立连接。UDP 不会顾虑那么多，而且它确实可以快速运行，代价是稳定性下降和丢包的风险。但是，UDP 能大大减少请求中的延迟。到同一服务器的重复连接的延迟几乎为零，因为不需要往返来建立连接。

HTTP3 是 HTTP2 的复用和压缩，协议从 TCP 更改为 UDP。然后，谷歌的那些人在协议中添加了他们做的层，以确保稳定性、数据包接收顺序及安全性。

 **因此，HTTP3 在保持 QUIC 稳定性的同时使用 UDP 来实现高速度**，同时又不会牺牲 TLS 的安全性。是的，在 QUIC 中就有 TLS1.3，你可以用它发起优雅的 SSL。这些层的底层机制是下面这样：

![img](https://img-blog.csdnimg.cn/img_convert/fcf5054382be0354afa9faec733cc242.png)





### TCP 的队头阻塞问题

TCP 再网络传输的过程中，在网络数据报文段发送的时候会把大的数据包拆分为多个小的连续的报文段按照顺序进行排列，并且有序发送；这些数据段通过网络传输来到数据接收端之后，在按照数据段的顺序将这些数据报文段组合成完整的数据报文(原始数据)，这样就完成了数据的传输工程。

但是如果某一个数据报文段没有按照顺序到达，那么我们的数据接收端会保持连接等待我们的数据报文段重传，这时候我们的数据报文段发送方就会阻塞请求。这时候就发生了**TCP 队头阻塞**。

我们都直到在HTTP1.1 引入了我们的TCP 连接复用，使得我们的不用每次HTTP 请求都和服务器重新建立TCP 连接，而是使得一个TCP 连接可以被多个 request 请求复用，但是HTTP/1.1中规定一个域名可以有6个TCP连接。**而HTTP/2中，同一个域名只是用一个TCP连接**，而后更具request 的 id 标识来进行服务端的请求分发。

所以，**在HTTP/2中，TCP队头阻塞造成的影响会更大**，因为HTTP/2的多路复用技术使得多个请求其实是基于同一个TCP连接的，那如果某一个请求造成了TCP队头阻塞，那么多个请求都会受到影响。



### TCP 握手时长

我们都知道**TCP的可靠连接是基于三次握手与四次挥手实现的**。但是问题是三次握手是需要消耗时间的。

TCP三次握手的过程客户端和服务器之间需要交互三次，那么也就是说需要额外消耗1.5 RTT。

> RTT：网络延迟（Round Trip Time）。他是指一个请求从客户端浏览器发送一个请求数据包到服务器，再从服务器得到响应数		据包的这段时间。RTT 是反映网络性能的一个重要指标。

在客户端和服务端距离比较远的情况下，如果一个RTT达到300-400ms，那么我握手过程就会显得很”慢”了。

除此之外，如果你使用的是 HTTPS 协议的话，那么还将额外花费两轮的TCP 握手时间，因为在建立真正的数据传输TCP 连接前，还需要进行`TLS` 的握手



### QUIC 协议

所以，摆在HTTP/3.0面前的就只有一条路，那就是放弃TCP。于是，HTTP/3.0在基于UDP+迪菲赫尔曼算法（Diffie–Hellman）之上实现了QUIC协议（Quick UDP Internet Connections）。

QUIC协议有以下特点：

- 基于**UDP**的传输层协议：它使用UDP端口号来识别指定机器上的特定服务器。

- **可靠性**：虽然UDP是不可靠传输协议，但是QUIC在UDP的基础上做了些改造，使得他提供了和TCP类似的可靠性。它提供了数据包<u>重传、拥塞控制、调整传输节奏</u>以及其他一些TCP中存在的特性。

- **实现了无序、并发字节流**：QUIC的单个数据流可以保证有序交付，但多个数据流之间可能乱序，这意味着单个数据流的传输是按序的，但是多个数据流中接收方收到的顺序可能与发送方的发送顺序不同！

- **快速握手**：QUIC提供0-RTT和1-RTT的连接建立

- 使用TLS 1.3传输层安全协议：与更早的TLS版本相比，TLS 1.3有着很多优点，但使用它的最主要原因是其握手所花费的往返次数更低，从而能降低协议的延迟。



### HTTP 3.0 特性

1. 队头阻塞问题更加彻底

    基于TCP的Http2，尽管在逻辑上相互独立，不会相互影响，但在实际中，数据是 一帧一帧发送和接收，但是一旦某一个流的数据有丢包，则同样会阻塞在它之后的传输流数据传输。而基于UDP的Http3.0就没有这个问题，能够让不同的流之间实现真正的独立。

2. 网络切换时连接保持

    在当前移动端的应用环境，用户网络会经常切换，基于TCP的协议，由于切换网络之后，IP会改变，所以说之前建立的连接会断开。而基于UDP的Http3.0，则可以内建与TCP中不同的连接标识方法，从而在网络完成切换后，恢复之前的服务器连接 

