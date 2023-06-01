# WebRTC简介

[toc]

## 引言

最近一段时间的主要工作内容是开发一个远程控制手机的功能，其中使用到的一个关键技术就是WebRTC，在此准备分享一下我对这个技术的理解。其他 WebRTC 相关文章均收录于**[贝贝猫的文章目录](https://zhuanlan.zhihu.com/p/360478342)**。

## 什么是WebRTC

WebRTC是一个由Google发起的实时通讯解决方案，其中包含视频音频采集，编解码，数据传输，音视频展示等功能，我们可以通过技术快速地构建出一个音视频通讯应用。 虽然其名为WebRTC，但是实际上它不光支持Web之间的音视频通讯，还支持Android以及IOS端，此外由于该项目是开源的，我们也可以通过编译C++代码，从而达到全平台的互通。

## WebRTC架构



![img](https://pic1.zhimg.com/80/v2-9cfb8117f41f93e8825afb68bc32b384_720w.webp)



## 架构组件

### Your Web App

Web开发者开发的程序，Web开发者可以基于集成WebRTC的浏览器提供的web API开发基于视频、音频的实时通信应用。

### Web API

面向第三方开发者的WebRTC标准API（Javascript），使开发者能够容易地开发出类似于网络视频聊天的web应用，需要注意的是可能在不同浏览器中API接口名会不太一样, 所以推荐使用这个[JS适配器](https://link.zhihu.com/?target=https%3A//github.com/webrtchacks/adapter)来协调各个浏览器的不同接口。 这些API可分成Media API、 RTCPeerConnection、Peer-to-peer Data API三类:

### Media API

MediaStream：MediaStream用来表示一个媒体数据流。 MediaStreamTrack：在浏览器中表示一个媒体源。

### RTCPeerConnection

RTCPeerConnection：一个RTCPeerConnection对象允许用户在两个浏览器之间直接通讯。 SDP: 用来描述当前连接者想要传输的内容，支持的协议类型，支持的编解码类型等。 RTCIceCandidate：表示一个ICE协议的候选者，简单的说，就是目标节点的IP以及端口。 RTCIceServer：表示一个ICE Server，其主要用于当前主机的IP发现，通过和ICE Server通讯，我们会得到一组可供连接使用的IP:Port候选值，双方通过交换ICE候选值来建立起连接。

### Peer-to-peer Data API

DataChannel：数据通道( DataChannel)接口表示一个在两个节点之间的双向的数据通道，该通道可以设置成可靠传输或非可靠传输 。

### WebRTC Native C++ API

本地C++ API层，使浏览器厂商容易实现WebRTC标准的Web API，抽象地对数字信号过程进行处理。

### Transport / Session

传输部分可基于TCP/UDP，会话层组件采用了libjingle库的部分组件实现

### AudioEngine

音频引擎是包含一系列音频多媒体处理的框架，包括从视频采集卡到网络传输端等整个解决方案。

### VideoEngine

视频引擎是包含一系列视频处理的整体框架，从摄像头采集视频到视频信息网络传输再到视频显示整个完整过程的解决方案。

## 通讯内容的确立

首先，两个客户端（Alice & Bob）想要创建连接，一般来说需要有一个双方都能访问的服务器来帮助他们交换连接所需要的信息。有了交换数据的中间人之后，他们首先要交换的数据是SessionDescription（SD），这里面描述了连接双方想要建立怎样的连接。

![img](https://pic2.zhimg.com/80/v2-c9a8c557e778a5ad1fb6a3dcb1a687f9_720w.webp)



### SD 从哪来

一般来说，在建立连接之前连接双方需要先通过API来指定自己要传输什么数据（Audio，Video，DataChannel），以及自己希望接受什么数据，然后Alice调用CreateOffer()方法，获取offer类型的SessionDescription，通过公共服务器传递给Bob，同样地Bob通过调用CreateAnswer()，获取answer类型的SessionDescription，通过公共服务器传递给Alice。 在这个过程中无论是哪一方创建Offer（Answer）都无所谓，但是要保证连接双方创建的SessionDescription类型是相互对应的。Alice=Answer Bob=Offer | Alice=Offer Bob=Answer

### SD 包含什么内容

```text
//版本
v=0
//<username> <sess-id> <sess-version> <nettype> <addrtype> <unicast-address>
o=- 3089712662142082488 2 IN IP4 127.0.0.1
//会话名
s=-
//会话的起始时间和结束时间，0代表没有限制
t=0 0
//表示音频传输和data channel传输共用一个传输通道传输的媒体，通过id进行区分不同的流
a=group:BUNDLE audio data
//WebRTC Media Stream
a=msid-semantic: WMS
//m=audio说明本会话包含音频，9代表音频使用端口9来传输，但是在webrtc中现在一般不使用，如果设置为0，代表不传输音频
//使用UDP来传输RTP包，并使用TLS加密, SAVPF代表使用srtcp的反馈机制来控制通信过程
//111 103 104 9 0 8 106 105 13 110 112 113 126表示支持的编码，和后面的a=rtpmap对应
m=audio 9 UDP/TLS/RTP/SAVPF 111 103 104 9 0 8 106 105 13 110 112 113 126
//表示你要用来接收或者发送音频使用的IP地址, webrtc使用ice传输，不使用这个地址, 关于ICE是什么后面会讲到
c=IN IP4 0.0.0.0
//用来传输rtcp的地址和端口，webrtc中不使用
a=rtcp:9 IN IP4 0.0.0.0
//ice协商过程中的安全验证信息
a=ice-ufrag:ubhd
a=ice-pwd:l82NnsGm5i7pucQRchNdjA6B
//支持trickle，即sdp里面只描述媒体信息, ice候选项的信息另行通知
a=ice-options:trickle
//dtls协商过程中需要的认证信息
a=fingerprint:sha-256 CA:83:D0:0F:3B:27:4C:8F:F4:DB:34:58:AC:A6:5D:36:01:07:9F:2B:1D:95:29:AD:0C:F8:08:68:34:D8:62:A7
a=setup:active
//前面BUNDLE行中用到的媒体标识
a=mid:audio
//指出要在rtp头部中加入音量信息
a=extmap:1 urn:ietf:params:rtp-hdrext:ssrc-audio-level
//当前客户端只接受数据，不发送数据，recvonly,sendonly,inactive,sendrecv
a=recvonly
//rtp,rtcp包使用同一个端口来传输
a=rtcp-mux
//下面都是对m=audio这一行的媒体编码补充说明，指出了编码采用的编号，采样率，声道等
a=rtpmap:111 opus/48000/2
a=rtcp-fb:111 transport-cc
//对opus编码可选的补充说明,minptime代表最小打包时长是10ms，useinbandfec=1代表使用opus编码内置fec特性
a=fmtp:111 minptime=10;useinbandfec=1
a=rtpmap:103 ISAC/16000
a=rtpmap:104 ISAC/32000
a=rtpmap:9 G722/8000
a=rtpmap:0 PCMU/8000
a=rtpmap:8 PCMA/8000
a=rtpmap:106 CN/32000
a=rtpmap:105 CN/16000
a=rtpmap:13 CN/8000
a=rtpmap:110 telephone-event/48000
a=rtpmap:112 telephone-event/32000
a=rtpmap:113 telephone-event/16000
a=rtpmap:126 telephone-event/8000
//下面就是对Data Channel的描述，基本和上面的audio描述类似，使用DTLS加密，使用SCTP传输
m=application 9 DTLS/SCTP 5000
c=IN IP4 0.0.0.0
//可以是CT或AS，CT方式是设置整个会议的带宽，AS是设置单个会话的带宽。缺省带宽是千比特每秒
b=AS:30
a=ice-ufrag:ubhd
a=ice-pwd:l82NnsGm5i7pucQRchNdjA6B
a=ice-options:trickle
a=fingerprint:sha-256 CA:83:D0:0F:3B:27:4C:8F:F4:DB:34:58:AC:A6:5D:36:01:07:9F:2B:1D:95:29:AD:0C:F8:08:68:34:D8:62:A7
a=setup:active
//前面BUNDLE行中用到的媒体标识
a=mid:data
//使用端口5000，一个消息的大小是1024比特
a=sctpmap:5000 webrtc-datachannel 1024
```

以上就是一个SessionDescription的例子，虽然没有video的描述，但是video和audio的描述是十分类似的。 SDP中有关于IP和端口的描述，但是WebRTC技术并没有使用这些内容，那么双方是怎么建立"直接"连接的呢？建立起连接最关键的IP和端口是从哪里来的呢？这就需要ICE框架来完成这部分工作。

## 连接的建立

### 相关概念

### ICE

互动式连接建立（Interactive Connectivity Establishment）提供的是一种框架，使各种NAT穿透技术（STUN，TURN...）可以实现统一。该技术可以让客户端成功地穿透远程用户与网络之间可能存在的各类防火墙。

### NAT

网路地址转换(Network Address Translation)可为你的装置提供公用IP地址。路由器具备公用IP地址，而连上路由器的所有装置则具备私有IP地址。接着针对请求，从装置的私有IP对应到路由器的公用IP与专属的通讯端口。如此一来，各个装置不需占用专属的公用IP，亦可在网路上被清楚识别。

### STUN

NAT 的UDP简单穿越（Simple Traversal of UDP over NATs）是一种网络协议，它允许位于NAT（或多重NAT）后的客户端找出自己的公网地址，查出自己位于哪种类型的NAT之后以及NAT为某一个本地端口所绑定的Internet端端口。这些信息被用来在两个同时处于NAT路由器之后的主机之间建立UDP通信。

![img](https://pic3.zhimg.com/80/v2-f3467aed345c1199ea1e3ddd5928b672_720w.webp)

即使通过 STUN 服务器取得了公用 IP 地址，也不一定能建立连线。因为不同的NAT类型处理传入的UDP分组的方式是不同的。四种主要类型中有三种是可以使用STUN穿透：完全圆锥型NAT、受限圆锥型NAT和端口受限圆锥型NAT。但大型公司网络中经常采用的对称型 NAT（又称为双向NAT）则不能使用，这类路由器会通过 NAT 布署所谓的「Symmetric NAT」限制。也就是说，路由器只会接受你之前连线过的节点所建立的连线。这类网络就需要TURN技术。

### TURN

中继NAT实现的穿透（Traversal Using Relays around NAT）就是通过TURN服务器开启连线并转送所有数据，进而绕过Symmetric NAT的限制。你可通过TURN服务器建立连线，再告知所有端点传送封包至该服务器，最后让服务器转送封包给你。这个方法更耗时且更占频宽，因此在没有其他替代方案时才会使用这个方法。

![img](https://pic4.zhimg.com/80/v2-5926b76eea820b8d89738432822f635f_720w.webp)



### 连接建立过程

介绍完ICE框架中各个独立部分的含义之后，在让我们来看一看整个框架是如何工作的。

![img](https://pic2.zhimg.com/80/v2-c581b3ebbc9f19da20c516e6b1c98e91_720w.webp)

1.连接双方（Peer）通过第三方服务器来交换（Signalling）各自的SessionDescription数据。 2.连接双方（Peer）通过STUN协议从STUN Server那里获取到自己的NAT结构，子网IP和公网IP，端口，这里的IP和端口对我们称之为ICE Candidate。 3.连接双方（Peer）通过第三方服务器来交换（Signalling）各自ICE Candidates，如果连接双方在同一个NAT下那他们仅通过内网Candidate就能建立起连接，反之如果他们处于非对称型NAT下，就需要STUN Server识别出的公网Candidate进行通讯。 4.如果仅通过STUN Server发现的公网Candidate仍然无法建立连接，换句话说就是连接双方（Peer）中至少有一方处于对称NAT下，这就需要处于对称NAT下的客户端（Peer）去寻求TURN Server提供的转发服务，然后将转发形式的Candidate共享（Signalling）给对方（Peer）。 5.连接双方（Peer）向目标IP端口发送报文，通过SessionDescription中涉及的密钥以及期望传输的内容，建立起加密长连接。