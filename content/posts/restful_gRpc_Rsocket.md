---
title: "Restful、gRpc、Rsocket"
date: 2019-11-16T14:07:16+08:00
draft: false
categories: ["smicroservices"]
tags: ["java","spring boot","smicroservices"]
---



### 什么是Restful?



REST这个词，是[Roy Thomas Fielding](http://en.wikipedia.org/wiki/Roy_Fielding)在他2000年的[博士论文](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)中提出的。

Fielding是一个非常重要的人，他是HTTP协议（1.0版和1.1版）的主要设计者、Apache服务器软件的作者之一、Apache基金会的第一任主席。所以，他的这篇论文一经发表，就引起了关注，并且立即对互联网开发产生了深远的影响。

他这样介绍论文的写作目的：

> "本文研究计算机科学两大前沿----软件和网络----的交叉点。长期以来，软件研究主要关注软件设计的分类、设计方法的演化，很少客观地评估不同的设计选择对系统行为的影响。而相反地，网络研究主要关注系统之间通信行为的细节、如何改进特定通信机制的表现，常常忽视了一个事实，那就是改变应用程序的互动风格比改变互动协议，对整体表现有更大的影响。**我这篇文章的写作目的，就是想在符合架构原理的前提下，理解和评估以网络为基础的应用软件的架构设计，得到一个功能强、性能好、适宜通信的架构。**"
> 
> (This dissertation explores a junction on the frontiers of two 
> research disciplines in computer science: software and networking. 
> Software research has long been concerned with the categorization of 
> software designs and the development of design methodologies, but has 
> rarely been able to objectively evaluate the impact of various design 
> choices on system behavior. Networking research, in contrast, is focused
>  on the details of generic communication behavior between systems and 
> improving the performance of particular communication techniques, often 
> ignoring the fact that changing the interaction style of an application 
> can have more impact on performance than the communication protocols 
> used for that interaction. My work is motivated by the desire to 
> understand and evaluate the architectural design of network-based 
> application software through principled use of architectural 
> constraints, thereby obtaining the functional, performance, and social 
> properties desired of an architecture. )



Fielding将他对互联网软件的架构原则，定名为REST，即Representational State Transfer的缩写。("表现层状态转化")。

如果一个架构符合REST原则，就称它为RESTful架构。

**要理解RESTful架构，最好的方法就是去理解Representational State Transfer这个词组到底是什么意思，它的每一个词代表了什么涵义。** 如果你把这个名称搞懂了，也就不难体会REST是一种什么样的设计。



 总的来说,Restful架构就是：

　　（1）每一个URI代表一种资源；

　　（2）客户端和服务器之间，传递这种资源的某种表现层；

　　（3）客户端通过四个HTTP动词，对服务器端资源进行操作，实现"表现层状态转化"。

### 什么是Rsocket?

> RSocket is a binary protocol for use on byte stream transports such as TCP, WebSockets, and Aeron.
> 
> "RSocket是一种二进制协议，用于TCP、websocket和Aeron等字节流传输。"
> 
> It enables the following symmetric interaction models via async message passing over a single connection:
> 
> "它支持以下对称交互模型通过异步消息传递一个单一的连接:"
> 
> - request/response (stream of 1)
> - request/stream (finite stream of many)
> - fire-and-forget (no response)
> - channel (bi-directional streams)
> 
> It supports session resumption, to allow resuming long-lived streams 
> across different transport connections. This is particularly useful for 
> mobile⬄server communication when network connections drop, switch, and reconnect frequently.
> 
> "它支持会话恢复，允许在不同的传输连接之间恢复长生命周期的流。当网络连接频繁地断开、切换和重新连接时，这对于移动服务器通信尤其有用。"

| 模式                      | 说明                                       |
|:-----------------------:|:----------------------------------------:|
| 请求-响应（request/response） | 这是最典型也最常见的模式。发送方在发送消息给接收方之后，等待与之对应的响应消息。 |
| 请求-响应流（request/stream）  | 发送方的每个请求消息，都对应于接收方的一个消息流作为响应。            |
| 发后不管（fire-and-forget）   | forget） 	发送方的请求消息没有与之对应的响应。              |
| 通道模式（channel）           | 在发送方和接收方之间建立一个双向传输的通道。                   |

### 什么是gRpc？

[gRPC](http://www.grpc.io)是一个高性能、通用的开源RPC框架，其由Google主要面向移动应用开发并基于HTTP/2协议标准而设计，基于ProtoBuf(Protocol
 Buffers)序列化协议开发，且支持众多开发语言。 


gRPC提供了一种简单的方法来精确地定义服务和为iOS、Android和后台支持服务自动生成可靠性很强的客户端功能库。 


客户端充分利用高级流和链接功能，从而有助于节省带宽、降低的TCP链接次数、节省CPU使用、和电池寿命。

gRPC具有以下重要特征：  


强大的IDL特性 RPC使用ProtoBuf来定义服务，ProtoBuf是由Google开发的一种数据序列化协议，性能出众，得到了广泛的应用。  


支持多种语言 支持C++、Java、Go、Python、Ruby、C#、Node.js、Android Java、Objective-C、PHP等编程语言。 3.基于HTTP/2标准设计



### 谷歌机翻时间

> Representational State Transfer (REST) has become the de facto standard 
> for communicating between microservices. That is not a good thing — in 
> fact, it’s a very bad thing.  How did this come to pass? Well, at the 
> time REST emerged, there were even worse options. When Roy Fielding [proposed REST in 2000](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm), REST was a kale sandwich in a field of much worse tasting sandwiches.
> 
> "代表性状态转移（REST）已成为微服务之间进行通信的事实上的标准。 那不是一件好事-实际上，这是一件非常不好的事。 这是怎么发生的？ 
> 好吧，在REST出现时，还有更糟糕的选择。 当Roy 
> Fielding于2000年提出REST时，REST是羽衣甘蓝三明治，在口味更差的三明治领域。"
> 
> People were using SOAP, RMI, CORBA, and EJBs. JSON was a welcome respite
>  from XML. It was easy to use URLs to spit out some text. Plus, 
> JavaScript started to really take off in browsers and it was much easier
>  to deal with REST, than it was SOAP. Unlike the recent microservice 
> trend, most applications were the traditional monolithic 3-tier 
> application. The source of most of the external traffic they were 
> talking with was a browser, so when they had to produce something REST 
> was an easy choice. Many people began to move from bigger commercial 
> offerings like WebSphere to Jetty and Tomcat. These didn’t even have the
>  facilities to deal with EJBs, so REST was a convenient choice.
> 
> "人们使用SOAP、RMI、CORBA和ejb。JSON是XML的一个很好的缓冲。使用url输出一些文本是很容易的。另外，JavaScript开始在浏览器中流行起来，它比SOAP更容易处理REST。与最近的微服务趋势不同，大多数应用程序是传统的单片3层应用程序。他们所谈论的大多数外部流量的来源是一个浏览器，所以当他们不得不产生一些东西时，REST是一个简单的选择。许多人开始从WebSphere这样的大型商业产品转向Jetty和Tomcat。它们甚至没有处理ejb的设施，因此REST是一个方便的选择。"
> 
> What does this have to do with microservices? Early microservice 
> pioneers moved to microservices for a different reason than people are 
> doing it today. They moved to them because they had to deal with massive
>  scale. They started to get so many users that they couldn’t serve 
> everything in a single monolith. And unlike many enterprises today, cost
>  wasn’t the motivating factor — time was.  They needed to get their 
> services out yesterday. As they got more and more users their monolith 
> wasn’t cutting it, so they cut their app up into smaller pieces. They 
> could deploy these applications on thousands of servers, and then 
> eventually virtual machines.
> 
> "这与微服务有什么关系？ 早期的微服务先驱之所以转向微服务，其原因不同于今天的人们。 他们之所以搬到他们这里，是因为他们不得不面对大规模的交易。 
> 他们开始吸引大量用户，以至于无法在一个整体中提供所有服务。 而且与今天的许多企业不同，成本不是动力，而是时间。 他们需要昨天取消服务。 
> 随着他们越来越多的用户，他们的独裁者并没有削减它，因此他们将他们的应用程序分成了较小的部分。 
> 他们可以将这些应用程序部署在数千台服务器上，然后再部署在虚拟机上。"
> 
> Furthermore, they could deploy their applications very quickly. 
> Companies that adopted this model were able to survive. During this race
>  though, there wasn’t much time to consider what they were doing. These 
> early pioneers had to deal with exponential user growth and competition,
>  so it makes sense they would opt for tactical solutions. One of these 
> was using REST to communicate between services.
> 
> "此外，他们可以非常快速地部署应用程序。 采用这种模式的公司能够生存。 不过在这场比赛中，没有太多时间去考虑他们在做什么。 这些早期的开拓者必须应对指数级的用户增长和竞争，因此他们选择战术解决方案是有道理的。 其中之一是使用REST在服务之间进行通信。"



##### Why REST is bad for Microservices

> When programming an application, your programing language eventually 
> ends up as machine code. This is obvious. Even an “interpreted” language
>  like Java or JavaScript does, as well. Instead of compiling directly to
>  machine code, they use a JIT or just-in-time compiler. In some cases, 
> JIT’ed code can be faster than what an engineer can write and tune by 
> hand — VMs are truly a miracle of modern computer science.
> 
> "在编写应用程序时，您的编程语言最终会变成机器码。这是显而易见的。甚至像Java或JavaScript这样的“解释型”语言也可以。他们使用JIT或即时编译器，而不是直接编译成机器码。在某些情况下，JIT代码的速度比工程师手工编写和调优的速度还要快——vm确实是现代计算机科学的一个奇迹。"
> 
> Why then do we waste this miracle? Instead of sending binary messages 
> optimized for machines, on a protocol optimized for services, we send 
> messages optimized for humans. We send around things like JSON and XML 
> using a protocol that was designed for sending books. Think how 
> ridiculous this is! You have a program that is binary, that turns a 
> binary structure to text, sends it over the network in text, to a 
> machine that parses and turns it back into binary structure to be 
> processed in an application.
> 
> "那为什么我们要浪费这个奇迹呢？ 我们不是根据针对服务优化的协议发送针对机器优化的二进制消息，而是发送针对人员优化的消息。 
> 我们使用旨在发送书籍的协议来发送JSON和XML之类的东西。 认为这是多么荒谬！ 
> 您有一个二进制程序，该程序将二进制结构转换为文本，然后通过网络将其以文本形式发送到计算机，该计算机将其解析并转换回二进制结构，以在应用程序中进行处理。"
> 
> Avoiding cache misses on a modern CPU is critical. Unfortunately, 
> parsing tons of JSON and Strings is going to cause cache misses!
> 
> "避免现代CPU上的高速缓存未命中至关重要。 不幸的是，解析大量的JSON和Strings将导致缓存未命中！"
> 
> An often-cited reason to use REST is that it’s easy to debug because its
>  “human readable”. Not being easy to read is a tooling issue. JSON text 
> is only human readable because there are tools that allow you to read it
>  – otherwise it’s just bytes on a wire. Furthermore, half the time the 
> data being sent around is either compressed or encrypted — both of which
>  aren’t human readable. Besides, how much of this can a person “debug” 
> by reading? If you have a service that averages a tiny 10 requests per 
> second with a 1 kilobyte JSON that is the equivalent to 860 megabytes of
>  data a day, or 250 copies of War and Peace every day. There is no one 
> who can read that, so you’re just wasting money.
> 
> 经常使用REST的原因是它易于调试，因为它的“可读性”。 不容易阅读是一个工具问题。 
> JSON文本只有人类可以阅读，因为有一些工具可让您读取JSON文本；否则，它只是线路上的字节。 
> 此外，一半左右的数据被压缩或加密-两者都不是人类可读的。 此外，一个人可以通过阅读“调试”多少？ 
> 如果您有一项服务，平均每秒钟平均有10个请求，并带有1 KB的JSON，则相当于每天860兆字节的数据，或每天250份《战争与和平》。 
> 没有人可以阅读，所以您只是在浪费金钱.
> 
> Then, there is the case where you need to send binary data around, or 
> you want to use a binary format instead of JSON. To do that, you must 
> Base64 encode the data. This means that you essentially serialize the 
> data twice — again, not an efficient way to use modern hardware.
> 
> 然后，在某些情况下，您需要发送二进制数据，或者您想要使用二进制格式而不是JSON。 为此，您必须对数据进行Base64编码。 这意味着您实际上要对数据进行两次序列化，这又不是使用现代硬件的有效方法。
> 
> At the end of the day, REST was implemented as a hack on top of HTTP. 
> And HTTP is being used as a hack to send transport data between 
> services. HTTP was designed to schlep books around the Internet. It 
> shouldn’t be used for services to communicate with one other. Instead, 
> use a format that is optimized for your application — the thing that is 
> processing all the data.
> 
> 归根结底，REST被实现为基于HTTP的黑客。 HTTP被用作在服务之间发送传输数据的黑客。 HTTP的设计目的是使Internet上的书籍不被窃取。 不应将其用于服务之间的通信。 相反，请使用针对您的应用程序优化的格式-处理所有数据的事物。



##### What is good Microservice Communication?

> If we suppose for a moment that REST isn’t the best choice for 
> service to service communication, then what is? Let’s look at some of 
> the things we would want in a protocol designed for microservice 
> communication.
> 
> 如果我们假设REST不是服务到服务通信的最佳选择，那么什么才是呢?让我们来看看为微服务通信设计的协议中需要的一些东西。
> 
> For starters, we want things to be bi-directional. That’s a huge 
> problem with REST — clients can only call servers. When both sides have 
> equal ability to call each other, you can create interactions between 
> applications in a natural manner. Otherwise you are forced to devise 
> clunky workarounds such as long-polling to simulate server-initiated 
> calls. You can partially get around it with HTTP/2, but the call still 
> needs to be initiated by the client. What you want is the ability for 
> clients and servers to be free to call each other as necessary.
> 
> 首先，我们希望事情是双向的。这是REST的一个大问题——客户端只能调用服务器。当双方具有同等的调用能力时，您可以以自然的方式在应用程序之间创建交互。否则，您将被迫设计笨拙的工作区，例如长轮询，以模拟服务器发起的调用。您可以使用HTTP/2部分地绕过它，但是调用仍然需要由客户机发起。您想要的是客户机和服务器能够在必要时自由地相互调用。
> 
> Another requirement is the connection between services must support 
> multiple requests on same connection – at the same time. This is called 
> multiplexing. Now, with a single connection, there needs to be some way 
> to distinguish one request from another. This is unlike HTTP where one 
> request starts when another one ends. With multiplexing, you are going 
> to need keep track of the different requests. A good way to do this is 
> having each request represented with a binary frame. Each frame can hold
>  the request, as well as metadata about the request. Then, it can be 
> used to get the frame to the correct location.
> 
> 另一个要求是服务之间的连接必须同时支持同一连接上的多个请求。这叫做多路复用。现在，对于单个连接，需要有一些方法来区分不同的请求。这与HTTP不同，一个请求在另一个请求结束时开始。使用多路复用，您将需要跟踪不同的请求。实现此目的的一种好方法是用二进制帧表示每个请求。每个帧可以保存请求，以及关于请求的元数据。然后，它可以用来获得帧的正确位置。
> 
> When sending data over a single connection, you need the ability to 
> fragment requests. A large request with a single connection will block 
> all the other requests behind it, aka head-of-the-line blocking. What is
>  needed, instead, is to fragment the requests into smaller sizes and 
> send those over the network. Since data being sent is framed, it can be 
> broken into smaller frame fragments, and then reassembled on the other 
> side. This way, requests can interleave with each other. No longer can a
>  large request block a smaller request. This will create a much more 
> responsive system.
> 
> 当通过单个连接发送数据时，您需要能够分段请求。带有单个连接的大请求将阻塞它后面的所有其他请求，也称为前端阻塞。相反，需要的是将请求分割成更小的大小并通过网络发送。由于发送的数据是有框架的，所以可以将其分解成更小的框架片段，然后在另一端重新组装。这样，请求就可以相互交错。一个大的请求不再能阻塞一个小的请求。这将创建一个响应性更强的系统。
> 
> Also, the ability to exchange metadata about a connection is useful. 
> Sometimes there is data to send that isn’t necessarily part of a 
> business transaction — things like configuring the overall tracing level
>  or exchanging information for dictionary-based compression. These are 
> things that don’t have to do with business logic but could be controlled
>  at a connection level. The ability to exchange metadata would provide 
> for that.
> 
> 另外，交换关于连接的元数据的能力也很有用。有时需要发送的数据不一定是业务事务的一部分—比如配置整个跟踪级别或为基于字典的压缩交换信息。这些事情与业务逻辑无关，但是可以在连接级别进行控制。交换元数据的能力将为此提供支持。
> 
> Often in application code, a function or method will be called that 
> takes a list, returns a list, or both. This happens in microservices all
>  the time, as well. REST doesn’t deal with these situations well and 
> this leads to all sorts of hacks and complexity.
> 
> 通常在应用程序代码中，一个函数或方法将被调用，它接受一个列表，返回一个列表，或者两者兼而有之。这在微服务中也经常发生。REST不能很好地处理这些情况，这导致了各种各样的技巧和复杂性。
> 
> What’s needed is a protocol that can deal with iterative data easily 
> and naturally — like you do in your application. It doesn’t make sense 
> to read an entire list of data, process it and then return a list of 
> data once everything is processed. What you want is the ability to 
> process data as it comes. You want to be able to stream data. If there 
> is a long list of data, you don’t want to wait for that data to be 
> processed — you want to send the data off as it becomes available and 
> get the responses back as they occur.
> 
> 我们需要的是一种协议，它可以轻松而自然地处理迭代数据—就像您在应用程序中所做的那样。读取整个数据列表、处理它并在处理完所有内容后返回数据列表是没有意义的。你想要的是处理数据的能力。您希望能够流数据。如果有一个很长的数据列表，您不希望等待处理数据—您希望在数据可用时发送数据，并在响应出现时将其返回。
> 
> This will create a much more responsive system. It can be used for 
> all sorts of things from reading bytes from a file and streaming it over
>  the network, to returning results from a database query, to feeding 
> browser click-stream data to a back-end. If first-class streaming 
> support is present in the protocol, it’s not necessary to include 
> another system like Spark to do stream processing. Nor is it necessary 
> to include something like Kafka unless you want to store data.
> 
> 这将创建一个响应性更强的系统。它可以用于各种各样的事情，从从文件读取字节并通过网络传输，到从数据库查询返回结果，再到将浏览器点击流数据提供给后端。如果协议中提供了一流的流支持，则不需要包含其他系统(如Spark)来进行流处理。除非您想存储数据，否则也没有必要包含诸如Kafka之类的东西。
> 
> For data that is sent via streams, the next thing needed is 
> application flow control. Byte-level flow control works for something 
> like TCP because everything is the same size, and generally, the same 
> cost to process from the perspective of the network card. However, in an
>  application, not everything is the same cost. There could be a message 
> that is 10 kilobytes that takes 10 milliseconds to process, but another 
> message that is 10 bytes that takes 10 seconds.
> 
> 对于通过流发送的数据，接下来需要的是应用程序流控制。字节级流控制适用于TCP之类的东西，因为所有东西的大小都是相同的，而且从网卡的角度来看，处理的成本通常是相同的。然而，在应用程序中，并不是所有东西的成本都是相同的。可能有一条消息是10千字节，需要10毫秒来处理，但是另一条消息是10字节，需要10秒。
> 
> Another scenario found in microservices is that downstream services 
> process data at slower rates than the data can be processed. This means 
> that TCP buffers are never full. There needs to be some way to control 
> the flow of traffic to keep from overwhelming downstream services in 
> order to keep them responsive.
> 
> 微服务中的另一个场景是，下游服务处理数据的速度比处理数据的速度慢。这意味着TCP缓冲区永远不会满。需要有某种方式来控制流量，以避免下游服务无法承受，从而保持它们的响应能力。
> 
> The application must be able to control the rate that messages can 
> flow independent of the underlying network bytes. For an application 
> developer it is difficult to reason how many bytes a message is 
> especially between languages. On the other hand, it is simple for a 
> developer to reason about how many messages they are sending. This way, 
> the service can arbitrage between the network flow control and the 
> application flow control. Sometimes an application can process data 
> faster than the network, and other times, the network can process data 
> faster than the application. Having application flow control will ensure
>  that tail latency is stable as well — again creating a responsive 
> application. It also prevents the need for unbounded queues, a dangerous
>  hack that is found in other applications.
> 
> 应用程序必须能够控制消息可以独立于底层网络字节流动的速率。对于应用程序开发人员来说，很难推断消息的字节数，尤其是在不同语言之间。另一方面，对于开发人员来说，推断他们发送了多少消息是很简单的。通过这种方式，服务可以在网络流控制和应用程序流控制之间进行仲裁。有时应用程序处理数据的速度比网络快，有时网络处理数据的速度比应用程序快。应用程序流控制将确保尾延迟也是稳定的—再次创建响应型应用程序。它还防止了对无界队列的需要，这是在其他应用程序中发现的一种危险的攻击。
> 
> As mentioned above, a huge drawback of RESTful web services is that 
> they are (de facto) implemented as text-based. To send any binary data 
> requires you Base64 encode the data —and serialize everything twice. 
> What you really want is something that is binary — because it can 
> represent anything — including text. Also, it is significantly more 
> efficient for your application to process binary data than text, 
> especially numbers. Additionally, they are naturally more compact — they
>  don’t have extra braces, curly brackets, or angle brackets in them.  
> Finally, if your data is binary, there is a possibility too for zero 
> copy serialization and deserialization, depending on the format. This is
>  a little out of the scope of this article, but check things out like [Simple Binary Encoding (SBE)](https://github.com/real-logic/simple-binary-encoding), and [Flatbuffers](https://google.github.io/flatbuffers/). They are significantly faster than using JSON.
> 
> 如上所述，rest式web服务的一个巨大缺点是它们(实际上)是作为基于文本的实现的。要发送任何二进制数据，需要使用Base64对数据进行编码，并对所有内容进行两次序列化。你真正想要的是二进制的东西——因为它可以表示任何东西——包括文本。而且，应用程序处理二进制数据的效率要比处理文本(尤其是数字)高得多。此外，它们自然更紧凑——它们没有额外的大括号、花括号或尖括号。最后，如果您的数据是二进制的，也有可能进行零拷贝序列化和反序列化，这取决于格式。这有点超出了本文的范围，但是可以查看简单二进制编码(SBE)和Flatbuffers。它们比使用JSON要快得多。
> 
> Finally, you want to be able to send your requests over different 
> transports. RESTful web services typically use HTTP, which uses only 
> TCP. What you really want is a way to abstract the networking away, so 
> that you only program to a specification and don’t have to worry about 
> the transport. At the same time, if it’s talking to browsers your 
> application should be able to run over WebSocket. You should not have to
>  switch to a new networking toolkit every time you want to change where 
> your application is deployed, it should be easy to swap out transports 
> without any applications changes.
> 
> 最后，您希望能够通过不同的传输传输请求。RESTful 
> web服务通常使用HTTP，而HTTP只使用TCP。您真正想要的是一种抽象网络的方法，这样您就可以只根据规范进行编程，而不必担心传输。同时，如果它与浏览器对话，你的应用程序应该能够在WebSocket上运行。您不应该每次想要更改应用程序的部署位置时都必须切换到新的网络工具包，应该很容易在不更改任何应用程序的情况下交换传输。



#### Which Protocol Fits the Bill?

Some would suggest that REST and HTTP/2 are a better fit. HTTP/2 is 
better than HTTP/1 but if you read the specs, its sole purpose is to 
create a better web browser protocol. It was never designed or intended 
for use in microservices. And that is what it should be used for — 
server HTML to web browsers. Again, it was never intended for 
microservices communication. Furthermore, you still must deal with URLs 
and matching the different HTTP methods to your application — these 
methods were never really intended for server to server communication.

有些人认为REST和HTTP/2是更好的选择。HTTP/2比HTTP/1更好，但如果您阅读规范，它的唯一目的是创建更好的web浏览器协议。它从未被设计或打算用于微服务。这是它应该用于服务器HTML到web浏览器。同样，它也从未打算用于微服务通信。此外，您还必须处理url并将不同的HTTP方法与您的应用程序相匹配——这些方法从未真正用于服务器之间的通信。

HTTP/2 does provide streaming, but it only provides it for server 
push. So, using REST over HTTP/2 requires initiating a request on a 
client and then pushing the data to the server. HTTP/2 flow control is 
byte-based flow control. This is good for a web browser, but not good 
for an application. There is still no way to control the flow of an 
application by the way that work is being done on an application.

HTTP/2确实提供了流，但它只提供了服务器推送。因此，在HTTP/2上使用REST需要在客户机上启动一个请求，然后将数据推送到服务器。HTTP/2流控制是基于字节的流控制。这对于web浏览器很好，但是对于应用程序就不好了。仍然无法通过对应用程序执行工作的方式来控制应用程序流。

There has been a lot of noise lately about using gRPC. gRPC is very 
similar in concept to SOAP. Instead of using XML to define services, it 
uses Protobuf. Like SOAP, it’s a hodge-podge of URL and Header magic — 
this time using HTTP/2. This means gRPC is explicitly tied to HTTP/2, a 
protocol designed for web browsers. And what is worse, it isn’t 
supported in a web browser.

最近有很多关于使用gRPC的噪音。gRPC在概念上与SOAP非常相似。它使用Protobuf而不是XML来定义服务。像SOAP一样，它是URL和报头魔力的大杂烩——这次使用的是HTTP/2。这意味着gRPC被明确地绑定到HTTP/2，一个为web浏览器设计的协议。更糟糕的是，它在web浏览器中不受支持。

Instead you must use a proxy to turn your gRPC calls in to REST 
calls, thus defeating the purpose for using it. This highlights how 
poorly designed gRPC is. Why would you use HTTP/2 for a protocol and not
 make sure it works in a browser? You are forever limited by its 
original purpose, yet not able to use it where it was intended. This 
leads to my next point: the biggest limitation of REST is the fact it’s 
tied to HTTP.

相反，您必须使用代理将gRPC调用转换为REST调用，从而破坏了使用它的目的。这凸显了gRPC的设计有多么糟糕。为什么使用HTTP/2作为协议，却不能确保它在浏览器中工作?你永远被它最初的用途所限制，却不能在它原本的地方使用它。这就引出了我的下一个观点:REST最大的限制是它与HTTP绑定。

What you want is a protocol that is designed for service-to-service 
communication. Using a protocol that is specifically-designed for 
services to talk to each other will create significantly simpler and 
more reliable applications. There will not be any hacks, workarounds, or
 impedance mismatches.

您需要的是为服务到服务通信设计的协议。使用专门为服务相互通信而设计的协议将创建更简单、更可靠的应用程序。不会有任何黑客，工作区，或阻抗不匹配。

Construction materials are a good analogy. Wood is great for building
 small bridges. You can use it to span a small stream or creek and it 
isn’t a problem.

建筑材料是一个很好的类比。木材是建造小桥的好材料。你可以用它来跨越一条小溪，这不是问题。

When engineers started using it to span wider distances things got complicated.

当工程师们开始用它来跨越更宽的距离时，事情变得复杂起来

Wood bridges like this worked. But, they had a very high failure rate
 compared to modern bridges made of better materials. They were also 
very complicated and took much, much longer to build. This why we now 
use steel and concrete. They are easier to maintain, cheaper to build, 
last longer, and can span far greater distances.

这样的木桥很管用。但是，与用更好的材料建造的现代桥梁相比，它们的失败率非常高。它们也很复杂，需要花很长时间来建造。这就是为什么我们现在使用钢铁和混凝土。它们更容易维护，建造成本更低，寿命更长，可以跨越更远的距离。

We need a modern material to replace HTTP for creating modern services. Open source [RSocket](http://rsocket.io/) is designed for services. It is a connection-oriented, message-driven 
protocol with built-in flow control at the application level. It works 
in a browser equally as well as on a server. In fact, a web browser can 
serve traffic to backend microservices. It is also binary. It works 
equally well with text and binary data, and the payloads can be 
fragmented. It models all the interactions that you do in your 
application as network primitives. This means you can stream data or do 
Pub/Sub without having to setup an application queue.

我们需要一种现代材料来代替HTTP来创建现代服务。开源RSocket是为服务而设计的。它是一个面向连接的消息驱动协议，在应用程序级别具有内置的流控制。它在浏览器和服务器上都能工作。实际上，web浏览器可以为后端微服务提供流量。它也是二进制的。它同样适用于文本和二进制数据，并且有效负载可以被分割。它将应用程序中进行的所有交互建模为网络原语。这意味着您可以流数据或做发布/订阅，而不必设置应用程序队列。

REST is a decent solution where it makes sense. One place it doesn’t 
make sense is microservices. Distributed systems are difficult enough on
 their own. The last thing that we need is to make them more complex by 
using something not designed for them.

在有意义的地方，休息是一个不错的解决方案。微服务是一个没有意义的地方。分布式系统本身就够难的了。我们需要做的最后一件事就是使用一些不是为它们设计的东西来让它们变得更复杂。

### 参考链接

[理解RESTful架构](https://www.ruanyifeng.com/blog/2011/09/restful.html)

[ 使用 RSocket 进行反应式数据传输](https://www.ibm.com/developerworks/cn/java/j-using-rsocket-for-reactive-data-transfer/index.html)

[Rsocket.io](http://rsocket.io/)

[gRPC的那些事 - streaming](https://colobu.com/2017/04/06/dive-into-gRPC-streaming/)

[Give REST a Rest with RSocket ](https://www.infoq.com/articles/give-rest-a-rest-rsocket/)
