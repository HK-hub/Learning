# GraphQL  入门简单教程

----

## 1. 什么是GraphQL

​		一种用于 API 的查询语言。GraphQL 既是一种用于 API 的查询语言也是一个满足你数据查询的运行时。 GraphQL 对你的 API 中的数据提供了一套易于理解的完整描述，使得客户端能够准确地获得它需要的数据，而且没有任何冗余，也让 API 更容易地随着时间推移而演进，还能用于构建强大的开发者工具。

​		其中GraphQL是Facebook于2012年创造的一种用于描述CS应用中数据模型的能力和要求的语言。本标准的开发起始于2015年，GraphQL是一种在还在演进的未完成的新语言，其重大功能增强也会在本规范的未来版本中体现。

- 官方网站：[GraphQL 官网](https://graphql.cn/)
- 规范地址：[GraphQL 规范编写](https://spec.graphql.cn/)

![image-20211014152132456](https://i.loli.net/2021/10/14/7ntFEB5u4ZO8LjT.png)

-----



## 2.RestFul  API 存在的问题

​		依照传统的Web 前后端通信架构，通常是采用的一个 RESTful API 的通信风格，通过这种风格实现的前后端通信，数据交互的项目呢，通常会编写大量的 DTO， VO，基本上是每一个需要展示的实体都会编写这些个转换类，这就是REST 风格的交换方式存在个问题：请求的数据过多， 或者请求数据过少，不能满足前端的一个展示需求，而且 RESTFul 请求一次请求的限制还在在于，一次的请求呢，只能返回一个数据，如果一次性需要返回多个数据，则需要进行多次的请求，存在的这些问题，在请求与响应时会造成极大的 IO 开销。

-----



## 3.GraphQL 的特点

​		**1.按需索取数据，避免浪费**

![image-20211014153433694](https://i.loli.net/2021/10/14/CdIt4R5PKW93gVe.png)

![image-20211014153647966](https://i.loli.net/2021/10/14/CPGSz6a7KYegHtB.png)

​	

​	**2.一次查询获取多个数据**

![image-20211014154054761](https://i.loli.net/2021/10/14/XmenTtWvHGgfM62.png)



​	**3.API 演进无需版本划分**

![image-20211014154148902](https://i.loli.net/2021/10/14/XNky7gCIjhJlQzT.png)



**综上所诉啊：Graph QL 可以良好的解决 前后端通信，数据交互之间的资源请求的过多或过少的问题，以及能够一次请求获取多次资源**

-----



## 4.GraphQL 入门

>在接下来的一系列文章中，我们会了解 GraphQL 是什么，它是如何运作以及如何使用它。在找如何搭建 GraphQL 服务的文档？这有一些类库可以帮你用[多种不同语言](https://graphql.cn/code/)实现 GraphQL。通过实用教程深入学习体验，请访问 [How to GraphQL](https://www.howtographql.com/) 全栈教程网站。我们还与 edX 合作创建了免费的在线课程，[探索 GraphQL：一种用于 API 的查询语言](https://www.edx.org/course/exploring-graphql-a-query-language-for-apis)。

### 4.1 概述

​		GraphQL 是一个用户 `API` 的查询语言，是一个使用**基于类型系统**来执行查询的的*服务端*运行时（类型系统有你的数定义）语言。GraphQL 斌没有和任何数据库或者存储引擎绑定，而是依靠你现有的代码和数据支撑。

​		一个GraphQL 服务是通过定义`类型`和类型上的`字段`来创建的，然后给每个类型上的每个字段提供解析函数。例如，一个 Graph QL 服务告诉我们当前登录用户是 me ,这个用户名称可能像这样：

```json
type Query {
	me : User     
}

type User{
    id :ID
    neme :String
}

```

一并的还有每个类型上字段的解析函数：

```json
function Query_me(request){
    return request.auth.user ;
}

function User_me(user){
    return user.getName();
}
```



一旦一个 GraphQL 服务运行起来（通常在Web服务的一个 URL 上），他就能接收 GraphQL 查询，并验证和执行。接收到的查询首先会被**检测确保他只引用了以定义的类型和字段**，然后运行指定的**解析函数**来生成结果。

例如这个查询：

```graphql
{
  me {
    name
  }
}
```

会产生这样的JSON结果：

```json
{
  "me": {
    "name": "Luke Skywalker"
  }
}
```



在这系列文章中，我们会学习更多关于 GraphQL 的知识，包括**查询语言**、**类型系统**、GraphQL 服务的**工作原理**以及使用 GraphQL 解决常见问题的**最佳实践**。

-----



## 5.查询和变更

### 5.1 字段（Fields）

​		简单而言，GraphQL 是关于请求对象上的特定字段。我们以一个非常简单的查询以及其结果为例：

```json
// 查询语句
{
  hero {
    name
  }
}
//查询结果
{
  "data": {
    "hero": {
      "name": "R2-D2"
    }
  }
}
```

你立即就能发现，查询和其结果拥有几乎一样的结构。这是 GraphQL 最重要的特性，因为这样一来，你就总是能得到你想要的数据，而服务器也准确地知道客户端请求的字段。

>值得注意的是，上述查询时可交互的。也就是你可以按照你指定的字段来进行查询。
>
>

在前一例子中，我们请求了我们主角的名字，返回了一个字符串类型（String），但是字段也能指代对象类型（Object）。这个时候，你可以对这个对象的字段进行**次级选择（sub-selection）**。GraphQL 查询能够遍历相关对象及其字段，使得客户端可以一次请求查询大量相关数据，而**不像传统REST 架构中那样需要多次往返查询**。

```json
{
  hero {
    name
    # 查询可以有备注！
    friends {
      name
    }
  }
}

{
  "data": {
    "hero": {
      "name": "R2-D2",
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```

注意这个例子中，`friends` 返回了一个数组的项目，GraphQL 查询会同等看待单个项目或者一个列表的项目，然而我们可以通过 schema 所指示的内容来预测将会得到哪一种。

-----

### 5.2 参数（Arguments）

​		即便我们能够做的仅仅是遍历对象及其字段，GraphQL 就已经是一个非常有用的数据查询语言了。但是当你加入字段传递参数的能力时，事情会变得更加有趣。

```json
//查询字段
{
  human(id: "1000") {
    name
    height
  }
}
//查询结果
{
  "data": {
    "human": {
      "name": "Luke Skywalker",
      "height": 1.72
    }
  }
}
```

​		在类似 REST 的系统中，你只能传递一组简单参数——请求中的 query参数和URL中的地址参数。**但在GrapQL 中，每个字段和嵌套对象都能有自己的一组参数，从而使得 GrapQL 可以完美替代多次 API 获取请求**。甚至你也可以给标量字段传递参数，用于实现先服务端的一次转换，而不用每个客户端分别转换。

```json
{
  human(id: "1000") {
    name
    height(unit: FOOT) # 字段参数
  }
}

{
  "data": {
    "human": {
      "name": "Luke Skywalker",
      "height": 5.6430448
    }
  }
}
```

​		参数可以是多种不同的类型，上面的例子中，我们使用了一个 枚举类型。GraphQL 自带一套`默认类型`，但是GraphQL 服务器可以声明一套自己的`定制类型`，只要能够序列化成你的输出格式即可。

----

### 5.3 别名（Aliases）

​		即便结果中的字段与查询中的能够匹配，但是因为他们并不包含参数，你就无法通过不同参数来查询相同字段。这便是为何你需要**别名**——这可以让你重命名结果中的字段为任意你想得到的名字。

```json
{
  empireHero: hero(episode: EMPIRE) {
    name
  }
  jediHero: hero(episode: JEDI) {
    name
  }
}

# 查询结果
{
  "data": {
    "empireHero": {
      "name": "Luke Skywalker"
    },
    "jediHero": {
      "name": "R2-D2"
    }
  }
}
```

两个 `hero` 字段将会存在冲突，但是因为我们可以将其另取一个别名，我们也就可以在一次请求中得到两个结果。

---

### 5.4 片段（Fragment）

​		在当前的时代潮流下，高可用，复用原则已然成为一个好的语言所必备的，在我们使用 GraphQL 语言查询数据的时候，有些查询语句，需要多次重复的在多个地方利用到，那么此时对应的查询可能就会变得复杂。

​		值得庆幸的是 GraphQL 包含了 **片段** 的可复用单元。片段 使你能够组织一组片段，然后再需要他们的地方引入：

​	<img src="https://i.loli.net/2021/10/16/XhSdOPNjtrVkDo1.png" alt="image-20211016114457440" style="zoom:150%;" />     

你可以看到上面的查询如何漂亮地重复了字段。片段的概念经常用于将复杂的应用数据需求分割成小块，特别是你要将大量不同片段的 UI 组件组合成一个初始数据获取的时候。

---

### 5.6操作名称（Operation Name）

​		这之前，我们都使用了简写语句法，省略了 `query` 关键字和查询名称，但是生产中使用操作名称可以减少我们代码的歧义，提高我们代码的可读性。

​		下面的示例包含了作为**操作类型**的关键字`query` 以及**操作名称** `HeroNameAndFriends`:

```json
# 语法格式： <操作类型> <操作名称>
query HeroNameAndFriends {
  hero {
    name
    friends {
      name
    }
  }
}
# 查询结果
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```

**操作类型** 可以是*query , mutation 或 subscription* ， 描述你打算做什么类型额操作。操作类型是必须要的，除非你使用查询简写语法，在这种情况下，你无法为操作提供名称或变量定义。

**操作名称** 是你的操作有意义和明确的名称。建议大家都使用它来进行规范开发，因为它对于调试和服务器端日志记录非常有用。 当在你的网络或是 GraphQL 服务器的日志中出现问题时，通过名称来从你的代码库中找到一个查询比尝试去破译内容更加容易。同理，GraphQL 的查询和变更名称，以及片段名称，都可以成为服务端侧用来识别不同 GraphQL 请求的有效调试工具。

----

### 5.7 变量（Variables）

​		在 GraphQL 中我们可以将动态变化的查询参数，提取到查询之外，然后作为分离的字典传进去。

使用变量之前我们得做三件事：

1. 使用 `$variableName` 替代查询中的静态值。
2. 声明 `$variableName` 为查询接受的变量之一。
3. 将 `variableName: value` 通过传输专用（通常是 JSON）的分离的变量字典中。

全部做完之后就像这个样子：

```json
# { "graphiql": true, "variables": { "episode": JEDI } }
query HeroNameAndFriends($episode: Episode) {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}
```





#### 5.7.1 变量定义（Variable definitions）

​		变量定义看上去像是上述查询中的 `($episode: Episode)`。其工作方式跟类型语言中函数的参数定义一样。它以列出所有变量，变量前缀必须为 `$`，后跟其类型，本例中为 `Episode`。

​		所有声明的变量都必须是标量、枚举型或者输入对象类型。所以如果想要传递一个复杂对象到一个字段上，你必须知道服务器上其匹配的类型。可以从Schema页面了解更多关于输入对象类型的信息。

​		变量定义可以是可选的或者必要的。我们可以在必要的变量后面加上 `！` 来表明变量是必须的不可缺少的。例如下面的例子：

````json
{
    query getUser($uerID: String ){
        user{
        	id,
        	name,
        	friend{
				name,
        		age
    		}
    	}
    }
}
````



#### 5.7.2 默认变量（Default variables）

​		可以通过在查询中的类型定义后面附带默认值的方式，将默认值赋给变量。

```graphql
query HeroNameAndFriends($episode: Episode = "JEDI") {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}
```

当所有变量都有默认值的时候，你可以不传变量直接调用查询。如果任何变量作为变量字典的部分传递了，它将覆盖其默认值。



### 5.8 指令（Directives）

​		我们上面讨论的变量使得我们可以避免手动字符串插值构建动态查询。传递变量给参数解决了一大堆这样的问题，但是我们可能也需要一个方式使用变量动态地改变我们查询的结构。譬如我们假设有个 UI 组件，其有概括视图和详情视图，后者比前者拥有更多的字段。**我们可以使用指令来对查询语句进行结构的控制比如 if 结构**。

我们来构建一个这种组件的查询：

```
query Hero($episode: Episode, $withFriends: Boolean!) {
  hero(episode: $episode) {
    name
    friends @include(if: $withFriends) {
      name
    }
  }
}
{
  "episode": "JEDI",
  "withFriends": false
}
{
  "data": {
    "hero": {
      "name": "R2-D2"
    }
  }
}
```

​		尝试修改上面的变量，传递 `true` 给 `withFriends`，看看结果的变化。

​		我们用了 GraphQL 中一种称作**指令**的新特性。一个指令**可以附着在字段或者片段包含的字段**上，然后以任何服务端期待的方式来改变查询的执行。GraphQL 的核心规范包含两个指令，其必须被任何规范兼容的 GraphQL 服务器实现所支持：

- `@include(if: Boolean)` 仅在参数为 `true` 时，包含此字段。
- `@skip(if: Boolean)` 如果参数为 `true`，跳过此字段。

指令在你不得不通过字符串操作来增减查询的字段时解救你。服务端实现也可以定义新的指令来添加新的特性。

----



### 5.9 变更（Mutations）

​		GraphQL 的大部分讨论集中在数据获取，但是任何完整的数据平台也都需要一个改变服务端数据的方法，即为修改服务端数据的方法。REST 中，任何请求都可能最后导致一些服务端副作用，但是约定上建议不要使用 `GET` 请求来修改数据。GraphQL 也是类似 —— 技术上而言，任何查询都可以被实现为导致数据写入。然而，建一个约定来规范任何导致写入的操作都应该显式通过变更（mutation）来发送。

​		就如同查询一样，如果任何变更字段返回一个对象类型，你也能请求其嵌套字段。获取一个对象变更后的新状态也是十分有用的。我们来看看一个变更例子：

![image-20211016213021132](https://i.loli.net/2021/10/16/wYSVqKmIbHsDj6d.png)

​		变更中的多个字段（Multiple fields in mutations）：一个变更也能包含多个字段，一如查询。查询和变更之间名称之外的一个重要区别是：

​												**查询字段时，是并行执行，而变更字段时，是线性执行，一个接着一个。**

​		这意味着如果我们一个请求中发送了两个 `incrementCredits` 变更，第一个保证在第二个之前执行，以确保我们不会出现**竞态**，能够保证我们的一个多线程的安全性问题。

​	



​	