# 只知道JWT，那JWE、JWS、JWK、JWA呢？

> 以下文章来源于码农小胖哥 ，作者请关注星标

移动端兴起和**OAuth2**的流行导致**JWT**这几年火得一塌糊涂。今天要介绍另一个规范集**JOSE**[1],全称**Javascript Object Signing and Encryption**，它和**JWT**有莫大的关系。



### JOSE简介

**JOSE**是一个**Javascript**对象签名和加密协议，目的是提供一种在各个通讯方之间安全传输声明（ **claims**，例如授权信息 ）的方法，它特意构建在**JSON**和**BASE64**之上，以便在 Web应用程序中轻松使用。目前该规范还在不断地发展，我们常用的包含以下几个由**RFC**文档定义的概念：

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/zuF5sJGRDCv3ZthiaCI8LtbtCNjLKmFwL49xnFoObWY0zn6tdWTuAeJh4ia1FnmBbsxOvd2rHmuQrS9giajIy8OWw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

❝

**JWT**就可以用**JWS**或**JWE**表示，稍后我会详细介绍这一方面的知识。



#### JWS

**JSON Web签名**，基于**JSON**数据结构、使用数字签名技术或者消息认证码技术保护的内容（**MAC**）都可以称为**JWS**。该规范使用的密码算法和标识符在另一个规范**JWA**中定义。规则是比较多的参见**RFC7515**[2]，这里我们通过序列化来感受一下即可。

##### JWS 序列化

**JWS**的序列化分为**JWS Compact Serialization**和**JWS JSON Serialization**两种。



**JWS Compact Serialization**

该序列化表示为一种URL安全的、紧凑的字符串。格式为：

```javascript
BASE64URL(UTF8(JWS Protected Header)) || '.' ||
      BASE64URL(JWS Payload) || '.' ||
      BASE64URL(JWS Signature)
```

例如：

```json
eyJhbGciOiJFUzI1NiJ9.eyJpc3MiOiJqb2UiLA0KICJleHAiOjEzMDA4MTkzODAsDQogImh0dHA6Ly9leGFtcGxlLmNvbS9pc19yb290Ijp0cnVlfQ.DtEhU3ljbEg8L38VWAfUAqOyKAM6-Xx-F4GawxaepmXFCgfTjDxw5djxLa8ISlSApmWQxfKTUJqPP3-Kg6NU1Q
```

❝

**JWT**通常就是这种格式。



**JWS JSON Serialization**

该序列化表示为一个**JSON**对象，有两种格式。一般格式为：

```json
    {
      "payload":"<payload contents>",
      "signatures":[
       {"protected":"<integrity-protected header 1 contents>",
        "header":"<non-integrity-protected header 1 contents>“,
        "signature":"<signature 1 contents>"},
       {"protected":"<integrity-protected header N contents>",
        "header":"<non-integrity-protected header N contents>",
        "signature":"<signature N contents>"}]
     }
```

平铺格式为：

```json
     {
      "payload":"<payload contents>",
      "protected":"<integrity-protected header contents>",
      "header":"<non-integrity-protected header contents>",
      "signature":"<signature contents>"
     }
```



#### JWE

**JWS** 仅仅是对声明(claims)作了签名，保证了其不被篡改，但是其 **payload(中段负载)** 信息是暴露的。也就是 **JWS** 仅仅能保证数据的完整性而不能保证数据不被泄露。它不适合传递敏感数据。**JWE** 的出现就是为了解决这个问题的。具体的可以看下图：

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/zuF5sJGRDCv3ZthiaCI8LtbtCNjLKmFwLA7qEjuYeo9HNjmxaePYZM9UQ2Q3oUXkbcjmoNwVMvabHXOWpV2g2cw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



从上面可以看出 **JWE** 的生成非常繁琐，作为 Token 可能比较消耗资源和耗时。用作安全的数据传输途径应该不错。举个例子：

```json
     eyJhbGciOiJSU0EtT0FFUCIsImVuYyI6IkEyNTZHQ00ifQ.
     OKOawDo13gRp2ojaHV7LFpZcgV7T6DVZKTyKOMTYUmKoTCVJRgckCL9kiMT03JGe
     ipsEdY3mx_etLbbWSrFr05kLzcSr4qKAq7YN7e9jwQRb23nfa6c9d-StnImGyFDb
     Sv04uVuxIp5Zms1gNxKKK2Da14B8S4rzVRltdYwam_lDp5XnZAYpQdb76FdIKLaV
     mqgfwX7XWRxv2322i-vDxRfqNzo_tETKzpVLzfiwQyeyPGLBIO56YJ7eObdv0je8
     1860ppamavo35UgoRdbYaBcoh9QcfylQr66oc6vFWXRcZ_ZT2LawVCWTIy3brGPi
     6UklfCpIMfIjf7iGdXKHzg.
     48V1_ALb6US04U3b.
     5eym8TW_c8SuK0ltJ3rpYIzOeDQz7TALvtu6UG9oMo4vpzs9tX_EFShS8iB7j6ji
     SdiwkIr3ajwQzaBtQD_A.
     XFBoMYUZodetZdvTiFvSkQ
```

❝

其实**JWE**也有对应的**JSON**格式，同样具有**JWS**的两种序列化方式，参见**RFC7516**[3]。

#### JWT和JWS、JWE的关系

以下是**RFC7519**[4]对**JWT**的说明：

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/zuF5sJGRDCv3ZthiaCI8LtbtCNjLKmFwL90ogfMlaGB8cbw9cDm8DtuqJlsjSNqmYT8N7kr3cIGgBhnPoObYnBg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

从上面可以得出一些结论：

- **JWT**有特定的 `claims`，这些`claims`以JSON的形式组成`Payload`。
- **JWT**的结构可以是**JWS**或者**JWE**。
- **JWT**的序列化方式只能使用**Compact Serialization**，不能是**JSON Serialization**。

简而言之，**JWT**是包含了特定`claims`的**JWS**或者**JWE**字符串。我们常见的大部分都属于**JWS**。

❝

另外，我们通常读作`J`、`W`、`T`，实际建议读作`jot`（角特），关于**JWT**的定义和规范请参阅**RFC7519**[5]。



#### JWK

**JWK**是本文最重要的知识点，这对我们后面学习资源服务器（**Resource Server**）非常重要。

##### 场景描述

我相信**签名公私钥**这个大家都不陌生。**JWT**本身也要做使用私钥进行签名防止信息被篡改，公钥用来发给下游消费方来验证**JWT**的可靠性。通常情况下，公钥的配置方式为静态文件集成，这有一个弊端，当上游公私钥进行了改动，下游就无法动态进行公钥适配。这就是**JWK**要解决的问题，它对密码算法和标识符进行了规范设计，它紧凑的**JSON**数据结构非常方便在上下游之间传输。

##### JWK 格式

**JWK**是表示加密密钥的**JSON**对象。该对象包含的`key`名称必须是唯一的，在此基础上**JWK**可以包含一些自定义字段。下面是一个**P-256 EC（椭圆曲线离散密码）**密钥的**JWK**表示：

```json
  {"kty":"EC",
      "crv":"P-256",
      "x":"f83OJ3D2xF1Bg8vub9tLe1gHMzV76e8Tus9uPHvRVEU",
      "y":"x_FEzRu9m36HLN_tue659LNpXW6pCyStikYjKIWI5a0",
      "kid":"Public key used in JWS spec Appendix A.3 example"
  }
```



按照**RFC7517**[6]的定义，**JWK JSON**对象可能包含以下属性：

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/zuF5sJGRDCv3ZthiaCI8LtbtCNjLKmFwLswlalVYgr0IV0jJxgQqqpgvwnN4GDniaXU3iayqDHbwaDv7G7yibTyesQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

> ❝
>
> 根据不同的算法**JWK**还可能包含其它的属性。

##### JWK Set

**JWK Set** 表示一组具有不同`kid`的**JWK**，这非常容易理解。它也是一个**JSON**对象，唯一的`key`就是`keys`。举个例子：

```json
  {"keys":
       [
         {"kty":"EC",
          "crv":"P-256",
          "x":"MKBCTNIcKUSDii11ySs3526iDZ8AiTo7Tu6KPAqv7D4",
          "y":"4Etl6SRW2YiLUrN5vfvVHuhp7x8PxltmWWlbbM4IFyM",
          "use":"enc",
          "kid":"1"},

         {"kty":"RSA",
          "n": "0vx7agoebGcQSuuPiLJXZptN9nndrQmbXEps2aiAFbWhM78LhWx
     4cbbfAAtVT86zwu1RK7aPFFxuhDR1L6tSoc_BJECPebWKRXjBZCiFV4n3oknjhMs
     tn64tZ_2W-5JsGY4Hc5n9yBXArwl93lqt7_RN5w6Cf0h4QyQ5v-65YGjQR0_FDW2
     QvzqY368QQMicAtaSqzs8KJZgnYb9c7d0zgdAZHzu6qMQvRL5hajrn1n91CbOpbI
     SD08qNLyrdkt-bFTWhAI4vMQFh6WeZu0fM4lFd2NcRwr3XPksINHaQ-G_xBniIqb
     w0Ls1jF44-csFCur-kEgU8awapJzKnqDKgw",
          "e":"AQAB",
          "alg":"RS256",
          "kid":"2011-04-29"}
       ]
}
```



> ❝
>
> **OAuth2**配置中的**JWK Set URL**就是输出**JWK Set**的端点。

#### JWA

JWA规范规定了哪些算法可以作为JWS和JWE的密码算法。还规定了这些算法对应的**JWK**中的`alg`属性，以及特定算法在JWK包含的属性例如前面**EC**算法中的`crv`、`x`、`y`，这些属性并不是一成不变的，它们会根据算法的迭代进行调整。如果你对**JWA**的细节感兴趣，请参阅**RFC7518**[7]。

> ❝
>
> 你可以通过**JWK生成器**[8]自行使用一些算法生成**JWK**观察不同算法之间的区别。

### 小结

今天对**JOSE**规范进行简单的介绍了解，对你学习**OAuth2**和**OIDC**相关的知识非常有帮助。不要求深入但是一定要了解相关的知识。