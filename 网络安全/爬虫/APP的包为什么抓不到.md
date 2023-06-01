# APP 的包为什么抓不到？

[toc]

## 一、抓包原理

### 1.1、抓包方式

#### 1.1.1、代理抓包

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1659581546523-a635d7ee-20c5-4f17-b005-812d1fce7dc0.png)

代理抓包的工具，这种方式思路相对简单，就是将http协议通过代理服务器进行转发，这样流量会经过代理服务器，因此在代理服务器中可以看到经过的流量包。常用的代理抓包工具有fiddler、Charles。

代理抓包是相对简单的一种抓包方式，这种方式非常容易绕过，很多app在设置代理服务器之后，会提示断网或者数据不经过代理服务器。这样可以使用VPN的方式进行抓包。

#### 1.1.2、VPN抓包

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1659581546613-b6993893-7078-42f5-9a33-b4d662b60f32.png)

了解VPN抓包时可以温故一下网络7模型。

代理抓包是应用层的抓包，这样很容易绕过。但是VPN的方式是一个网络层的抓包。理论上来说VPN这种方式可以抓到更多的数据包。

### 1.2、为什么能抓取https的包？

不知道有没有想过，https不是加密的吗？为啥还能抓取到https的数据呢？事实上，没有绝对的安全。https同样可以绕过。之前写过一个https攻击的思路，有兴趣的小伙伴可以看一看。在抓包工具抓取https数据包时，用到的就是**证书伪造攻击**思路。

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1659581546543-3889c4a4-ca7e-41f9-abe2-50c2a3e58101.png)

如上图，fiddler抓取https数据包大概的流程图。

一般情况来说，在通过fiddler抓取https协议的时候必须要安装fiddler的**根**证书。安装成功之后可在证书列表中查看到如下信息。

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1659581546572-68ac7661-9892-4bbb-9d5b-22fc777fe05d.png)

在第6步时，fiddler会下发一个伪造的证书。因此在百度页面加载完成之后，点击百度的小锁子。可以看到证书的颁发者为fiddler。

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1659581546942-9348ca44-d2d5-4700-b861-0893607fbb34.png)

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1659581547310-6a2550df-2a9a-428c-ac30-17e66d73df77.png)

上面的流程图有点复杂，它可以简化为下图：

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1659581547299-547ddaf5-3207-4f13-8eef-b0539ab7990d.png)

1. fiddler中间人伪造证书。将自己的公钥下发给浏览器。
2. 浏览器加密提交的数据fiddler就可以查看到明文数据。
3. fiddler再讲明文信息通过百度的公钥加密。
4. 这样百度就会误认为是浏览器提交上来的数据。

这里只展示了fiddler抓取https的原理，其实HttpCanary等市面上大部分抓取https都是这一个思路。

## 二、抓包工具介绍

### 2.1、PC端抓包工具

#### 2.1.1、HTTP_Analyzer_V7

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1659581547451-0a84736c-1be1-4969-9709-e4cd9d1860f6.png)

**HTTP_Analyzer_V7**是一个可以抓取进程包的工具。比如java进程如果在发送http请求的时候，fiddler是无法抓到的，这个时候可以通过这个工具进行抓取。

#### 2.1.2、fiddler

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1659581548030-89de92d9-9445-4df9-b768-25a208b686ac.png)

**fiddler**是一个通过**代理**的方式抓取http协议包。这种方式经常用于抓取PC浏览器的数据包。可以认为是谷歌浏览器调试工具的加强版。

在app设置代理之后，也可以抓取app的包，不过现在很多app都设置了默认不走代理，因此这种方式非常容易漏包。这个稍后详细说明。

#### 2.1.3、Charles

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1659581547627-a33372da-03d6-483d-9881-cb01c24227d6.png)

**Charles** 是一个 HTTP 代理/HTTP 监视器/反向代理，它使开发人员能够查看他们的机器和 Internet 之间的所有 HTTP 和 SSL/HTTPS 流量。这包括请求、响应和 HTTP 标头（其中包含 cookie 和缓存信息）。

fiddler和Charles都是PC端的抓包工具，功能类似，但是Charles+postern有特殊的功能，这个下面有说明。

### 2.2、手机端抓包工具

#### 2.2.1、HttpCanary

![img](https://cdn.nlark.com/yuque/0/2022/png/12488211/1659581547900-ec4202f4-31d9-46fe-90ab-858076053094.png)

**HttpCanary** 是一个基于VPN的抓包工具，这是一款功能非常强大的手机抓包软件，可以帮你快速抓取你想要的包，一键快速抓包，轻松简单。

#### 2.2.2、Charles+postern

Charles是一个PC的抓包工具，Postern是Android系统里一款非常流行的代理/ 虚拟专用网络管理程序，是一个Android下的全局代理工具，可以说是安卓版surge，它可以添加规则，屏蔽广告。通过这两个工具的结合，可以让Charles以VPN的方式抓包。

## 三、为什么有的app抓不到包?

### 3.1、不走代理型

在安卓开发时，发送一个http请求强制不走代理。此时没有安卓环境，我以java环境为例子。

 public static void main (String[] args) throws IOException    {//      System.setProperty("http.proxyHost", "localhost");//      System.setProperty("http.proxyPort", "8888");//      // set https proxy//      System.setProperty("https.proxyHost", "localhost");//      System.setProperty("https.proxyPort", "8888");        try {            URL url = new URL("http://www.baidu.com/");            // 创建代理服务器            Proxy p = new Proxy (Proxy.Type.HTTP,new InetSocketAddress ("localhost",8888));            HttpURLConnection conn = (HttpURLConnection)url.openConnection(Proxy.NO_PROXY);            conn.connect ();            InputStream in = conn.getInputStream();            String result = new BufferedReader(new InputStreamReader(in))                    .lines().collect(Collectors.joining("\n"));            System.out.println(result);        } catch (Exception e) {            e.printStackTrace();        }    }

在一个HttpURLConnection对象创建时，可以设置这个请求强制走哪个代理或者强制不走代理。

如果在openConnection方法中设置Proxy.NO_PROXY,那么不管系统如何设置，这段代码都不走代理。

Proxy p = new Proxy (Proxy.Type.HTTP,new InetSocketAddress ("localhost",8888));

设置上面代码，则认为这个请求需要localhost的8888为代理。

如果app只设置了不走代理，那么想抓到这种包很简单，只需要通过一个VPN的抓包工具即可。**HttpCanary**即可轻松抓取。

### 3.2、证书锁定

证书锁定（SSL/TLS Pinning）提供了两种锁定方式：

- Certificate Pinning 证书锁定
- Public Key Pinning 公钥锁定

#### 证书锁定

- 具体操作：将APP代码内置仅接受指定域名的证书，而不接受操作系统或者浏览器内置的CA根证书对应的任何证书。通过这种授权方式，保障了APP与服务端通信的唯一性和安全性，因此移动端APP与服务端（例如API网关）之间的通信可以保证绝对的安全。
- 缺点：CA签发证书存在有效期问题，在证书续期后需要将证书重新内置到APP内。

#### 公钥锁定

- 具体做法：公钥锁定是提前证书中的公钥并内置到移动端APP内，通过与服务器对比公钥值来验证连接的合法性。
- 优点：在制作证书密钥时，公钥在证书续期前后可以保持不变（即密钥对不变），所以可以避免证书有效期问题。

世界上没有绝对的安全，不管是证书锁定和是公钥锁定，只要对对应的锁定方法进行hook即可绕过。

但是要找到对应的方法非常的困难，而且还有一些加固型app、混淆型app、双向验证型app使得更加难以hook。