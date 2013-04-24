---
layout: post
title: "我也来谈web service"
date: 2008-12-18 11:59
comments: true
categories: web service
---

最近在准备web service的一次讲课。是软件体系结构的一个章节。下面把我所领悟的东西做一个总结。希望朋友们能来一起探讨研究下。
<!--more-->


### web service是什么？###

在说这个问题之前，我想首先要统一两个概念`programmable web`和`human web`。
`human web`可能是很多人对`web`的理解。做一个`web`项目就是为了让人来使用的，web应用里面的内容暂且放一边，对于结果的渲染就是返回html代码。
那么`programmable web`，顾名思义，就是在程序可用的的web应用，换句话说，就是web对外的接口不是html代码，而是另一种计算机可识别的代码，譬如XML。
 
那么它们是不是应该合二为一呢，对于一个web应用来说，无论是`programmable web`还是`human web`，只是观察的角度不同，接口不同。那么，最好的做法是将它们统一起来，如果是程序需要就给它返回XML代码，如果是浏览器需要就返回html，当然还可能返回其他格式的内容(譬如RSS)，那么如果要这样统一，统一点在哪呢？也就是说从什么角度看它们就是统一的呢？`web service`，既然是服务，就要服务的全面，到位，既要服务于人，又要服务于机器。
 
说清楚了这两个概念，那么来看看`web service`的定义：
 
引用W3C定义：

> A Web service is a software system designed to support interoperable machine-to-machine interaction over a network. It has an interface described in a machine-processable format (specifically WSDL). Other systems interact with the Web service in a manner prescribed by its description using SOAP messages, typically conveyed using HTTP with an XML serialization in conjunction with other Web-related standards.

这段话表达了3个意思：

1. web服务是用来支持机器对机器之间通过网络进行互操作的软件系统
2. web服务的接口描述是有机器能够读懂的格式（WSDL：web service description language）
3. 在其他系统跟web服务交互时，这些系统要遵循web服务的接口描述来组织SOAP消息。通常情况下使用HTTP来传递XML格式的内容与其他的web相关标准相关联。
 
### web service的逻辑分层 ###

{% img center /images/posts/webservice1.png %}

从图中可以看到，`web service`分为了5层，分别是数据层、数据访问层、业务逻辑层、业务面和监听者，它们的功能在左边有相应的说明。
 
我觉得如此分层与MVC框架的建立有密切的联系，因为架构约束底层实现。
从上往下来看，对应的事件相当于外界（用户）对`web`应用发出`request`，需要`web`应用返回`response`。
 
监听者，是与外界（用户）离的最近的一层，这一层实现从外部用户得到所有请求需要的内容（譬如方法，参数等）。这一层也就相当于`V(view)`
 
业务层， 将监听者得到的请求内容进行第一层加工。但是不进行具体的业务逻辑处理。相当于`C(controller)`
 
数据访问层，业务逻辑层这二层，用来将物理数据转换为逻辑数据。这也就相当于是`M(model)`的功能。
 
数据层对应着数据库，这一层存放物理数据。

### 从`web service`分类描述中寻求共性。

角度不同，web service的分类也就不同，下面我摘抄一些对于web分类的说法：

* 从功能的角度描述Web 服务,有一种观点认为Web 服务基于TCP/IP,HTTP,XML 等规范而定义,具备如下功能:`Web`上链接文档的浏览、事务的自动调用、服务的动态发现和发布.

* 从组成框架及实现目标的角度描述Web 服务,认为`Web`服务作为一种网络操作,能够利用标准的Web 协议及接口进行应用间的交互.

* 还有从语义的角度描述了基于语义Web 的服务,认为`Web` 服务是语义`Web`的一种应用,由于考虑了语义信息的描述及表示,`Web`服务能够更准确地被执行,服务组合(service composition)能够按所期望的目标进行.

* 还有从网格计算`(grid computing)`的角度、信息检索的角度提出了很多web服务的描述。

* 另外一方面,针对不同的应用背景: 面向企业应用(business-oriented) 的服务、面向消费者(consumer-oriented) 的服务、面向设备(device-oriented)的服务和面向系统(system-oriented)的服务
 
但共性是：

* 应用的分布式.为适应网络应用中分布式的数据源和服务提供者,分布式的服务响应、松散耦合是Web 服务必须具备的特征.在应用中,服务请求者不必关心服务提供者的数据源格式是什么,某一服务请求需调用哪些服务,服务请求在Web 上怎样被执行等,即Web 服务对用户具有分布透明性. 

* 应用到应用的交互.在分布式的环境中,若采用集中控制方式,服务器有较大的负荷,并且系统不具有健壮性.因此应用到应用的交互,使得Web 服务更具可伸缩性. 

* 平台无关性.Web 服务的界面、跨Web 服务的事务、工作流、消息认证、安全机制均采用规范的协议和约定;由于Web 服务采用简单、易理解的标准
Web 协议作为组件接口和协同描述的规范,完全屏蔽了不同软件平台的差异,因此具有可集成能力。
 
* web服务的特点
 
对于外部使用者来说，`web service`是一个对象/组件。
它有5个特点:

  * 完好的封装性：服务既然是一种部署在Web上的对象，自然具备对象的良好封装性，对于使用者而言，他能且仅能看到该对象提供的功能列表。    
  * 松散耦合：这一特征也是源于对象组件技术，当一个Web服务的实现发生变更的时候，调用者是不会感到这一点的，对于调用者来说，只要服务的调用界面不变，服务的实现任何变更对他们来说都是透明的。    
  * 使用协约的规范性：这一特征从对象而来，但相比一般对象其界面规范更加规范化和易于机器理解。    
  * 使用标准协议规范：作为Web服务，其所有公共的协约完全需要使用开放的标准协议进行描述、传输和交换。这些标准协议具有完全免费的规范，以便由任意方进行实现。    
  * 高度可集成能力：由于服务采取简单的、易理解的标准协议作为组件界面描述和协同描述规范，完全屏蔽了不同软件平台的差异，无论是CORBA、 DCOM还是EJB都可以通过这一种标准的协议进行互操作，实现了在当前环境下最高的可集成性。
	
这些特点比较明显，就不再进行讲述了。
 
### web服务的模型：
{% img center /images/posts/webservice2.png %}

可以看到这里有3个角色，3个动作，3个双向箭头，首先解释一下3个角色
 
服务提供者，也就是我们构建的web服务，可以对外提供某种服务，它首先要通过发布（可选）到服务代理，然后被服务请求者查找到，服务请求者从服务代理得到服务提供者的信息之后，和服务提供者绑定，从服务提供者那里得到需要的服务。
 
 
### web服务开发的生命周期

{% img center /images/posts/webservice3.png %}

这个生命周期类似于软件开发的生命周期， 因为web开发也是一种软件开发的过程。

### web服务概念性协议栈
 
{% img center /images/posts/webservice4.png %}

* 发现服务层：是帮助客户端应用解析远程服务的位置，通过UDDI实现。
* 描述服务层：为客户端提供服务端的服务描述，例如web服务能做什么，位置在哪，如何调用等
* 消息格式层：保证客户端与服务端格式设置上保持一致，一般通过SOAP来实现。SOAP定义了C与S之间消息传输规范。SOAP用XML来格式化消息，HTTP来承载。SOAP包括3个部分，SOAP信封（定义了描述信息和如何处理消息的框架的封装）、表达应用程序定义的数据类型实例的编码规则（SOAP编码规则）以及RPC协议
* 编码格式层：为C和S之间提供标准的，独立于平台的数据交换编码格式，一般为XML格式。
* 传输协议层：为C与S之间提供交互的网络通信协议