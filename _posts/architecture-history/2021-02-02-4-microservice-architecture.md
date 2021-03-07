---
layout: post
title:  "微服务时代"
date:   2021-02-02 11:43:25 +0800
categories: 架构
---

* content
{:toc}

本文参考 Fenix Project，在原文基础上做了相应个人整理、思考、补充。
[原文地址](http://icyfenix.cn/architecture/architect-history/microservices.html){:target="_blank"}


概念		{#idea}
=============================

> 微服务时代<br />
> &emsp;&emsp;微服务是一种通过多个小型服务组合来构建单个应用的架构风格，这些服务围绕业务能力而非特定的技术标准来构建。各个服务可以采用不同的编程语言，不同的数据存储技术，运行在不同的进程之中。服务采取轻量级的通信机制和自动化的部署机制实现通信与运维。

&emsp;&emsp;“微服务”这个技术名词最早在2005年就已经被提出，它是由Peter Rodgers博士在2005年度的云计算博览会（Web Services Edge 2005）上首次使用，当时的说法是“Micro-Web-Service”，指的是一种专注于单一职责的、语言无关的、细粒度Web服务（Granular Web Services）。“微服务”一词并不是Peter Rodgers直接凭空创造出来的概念，最初的微服务可以说是SOA发展时催生的产物，就如同EJB推广过程中催生了Spring和Hibernate那样，这一阶段的微服务是作为一种SOA的轻量化的补救方案而被提出的。时至今日，在英文版的维基百科上，仍然将微服务定义为一种SOA的变种形式，所以微服务在最初阶段与SOA、Web Service这些概念有所牵扯也完全可以理解，但现在来看，维基百科对微服务的定义已经颇有些过时了。

> Microservices is a software development technique — a variant of the service-oriented architecture （SOA） structural style.<br />
> 微服务是一种软件开发技术，是一种SOA的变体形式。<br />
> —— Wikipedia，[Microservices](https://en.wikipedia.org/wiki/Microservices) 

&emsp;&emsp;微服务的概念提出后，在将近十年的时间里面，并没有受到太多的追捧。如果只是对现有SOA架构的修修补补，确实难以唤起广大技术人员的更多激情。不过，在这十年时间里，微服务本身也在思考蜕变。2012年，在波兰克拉科夫举行的“33rd Degree Conference”大会上，Thoughtworks首席咨询师James Lewis做了题为《[Microservices - Java, the Unix Way](http://2012.33degree.org/talk/show/67)》的主题演讲，其中提到了单一服务职责、[康威定律](https://en.wikipedia.org/wiki/Conway%27s_law)、自动扩展、领域驱动设计等原则，却只字未提SOA，反而号召应该重拾Unix的设计哲学（As Well Behaved Unix Services），这点仿佛与笔者在前一节所说的“初心与自省”遥相呼应。微服务已经迫不及待地要脱离SOA的附庸，成为一种独立的架构风格，也许，未来还将会是SOA的革命者。

九个核心的业务与技术特征		{#nine}
=============================

&emsp;&emsp;微服务真正的崛起是在2014年，相信阅读此文的大多数读者，也是从Martin Fowler与James Lewis合写的文章《[Microservices: A Definition of This New Architectural Term](https://martinfowler.com/articles/microservices.html)》中首次了解到微服务的。这并不是指各位一定读过这篇文章，应该准确地说——今天大家所了解的“微服务”是这篇文章中定义的“微服务”。在此文中，首先给出了现代微服务的概念：“**微服务是一种通过多个小型服务组合来构建单个应用的架构风格，这些服务围绕业务能力而非特定的技术标准来构建。各个服务可以采用不同的编程语言，不同的数据存储技术，运行在不同的进程之中。服务采取轻量级的通信机制和自动化的部署机制实现通信与运维**。”此外，文中列举了微服务的九个核心的业务与技术特征，下面将其一一列出并解读。

- **围绕业务能力构建**（Organized around Business Capability）。这里再次强调了康威定律的重要性，有怎样结构、规模、能力的团队，就会产生出对应结构、规模、能力的产品。这个结论不是某个团队、某个公司遇到的巧合，而是必然的演化结果。如果本应该归属同一个产品内的功能被划分在不同团队中，必然会产生大量的跨团队沟通协作，跨越团队边界无论在管理、沟通、工作安排上都有更高昂的成本，高效的团队自然会针对其进行改进，当团队、产品磨合调节稳定之后，团队与产品就会拥有一致的结构。
- **分散治理**（Decentralized Governance）。这是要表达“谁家孩子谁来管”的意思，服务对应的开发团队有直接对服务运行质量负责的责任，也应该有着不受外界干预地掌控服务各个方面的权力，譬如选择与其他服务异构的技术来实现自己的服务。这一点在真正实践时多少存有宽松的处理余地，大多数公司都不会在某一个服务使用Java，另一个用Python，下一个用Golang，而是通常会有统一的主流语言，乃至统一的技术栈或专有的技术平台。微服务不提倡也并不反对这种“统一”，只要负责提供和维护基础技术栈的团队，有被各方依赖的觉悟，要有“经常被凌晨3点的闹钟吵醒”的心理准备就好。微服务更加强调的是确实有必要技术异构时，应能够有选择“不统一”的权利，譬如不应该强迫Node.js去开发报表页面，要做人工智能训练模型时，应该可以选择Python，等等。
- **通过服务来实现独立自治的组件**（Componentization via Services）。之所以强调通过“服务”（Service）而不是“类库”（Library）来构建组件，是因为类库在编译期静态链接到程序中，通过本地调用来提供功能，而服务是进程外组件，通过远程调用来提供功能。前面的文章里我们已经分析过，尽管远程服务有更高昂的调用成本，但这是为组件带来隔离与自治能力的必要代价。
- **产品化思维**（Products not Projects）。避免把软件研发视作要去完成某种功能，而是视作一种持续改进、提升的过程。譬如，不应该把运维只看作运维团队的事，把开发只看作开发团队的事，团队应该为软件产品的整个生命周期负责，开发者不仅应该知道软件如何开发，还应该知道它如何运作，用户如何反馈，乃至售后支持工作是怎样进行的。注意，这里服务的用户不一定是最终用户，也可能是消费这个服务的另外一个服务。以前在单体架构下，程序的规模决定了无法让全部人员都关注完整的产品，组织中会有开发、运维、支持等细致的分工的成员，各人只关注于自己的一块工作，但在微服务下，要求开发团队中每个人都具有产品化思维，关心整个产品的全部方面是具有可行性的。
- **数据去中心化**（Decentralized Data Management）。微服务明确地提倡数据应该按领域分散管理、更新、维护、存储，在单体服务中，一个系统的各个功能模块通常会使用同一个数据库，诚然中心化的存储天生就更容易避免一致性问题，但是，同一个数据实体在不同服务的视角里，它的抽象形态往往也是不同的。譬如，Bookstore应用中的书本，在销售领域中关注的是价格，在仓储领域中关注的库存数量，在商品展示领域中关注的是书籍的介绍信息，如果作为中心化的存储，所有领域都必须修改和映射到同一个实体之中，这便使得不同的服务很可能会互相产生影响而丧失掉独立性。尽管在分布式中要处理好一致性的问题也相当困难，很多时候都没法使用传统的事务处理来保证，但是两害相权取其轻，有一些必要的代价仍是值得付出的。
- **强终端弱管道**（Smart Endpoint and Dumb Pipe）。弱管道（Dumb Pipe）几乎算是直接指名道姓地反对SOAP和ESB的那一堆复杂的通信机制。ESB可以处理消息的编码加工、业务规则转换等；BPM可以集中编排企业业务服务；SOAP有几十个WS-*协议族在处理事务、一致性、认证授权等一系列工作，这些构筑在通信管道上的功能也许对某个系统中的某一部分服务是有必要的，但对于另外更多的服务则是强加进来的负担。如果服务需要上面的额外通信能力，就应该在服务自己的Endpoint上解决，而不是在通信管道上一揽子处理。微服务提倡类似于经典UNIX过滤器那样简单直接的通信方式，RESTful风格的通信在微服务中会是更加合适的选择。
- **容错性设计**（Design for Failure）。不再虚幻地追求服务永远稳定，而是接受服务总会出错的现实，要求在微服务的设计中，有自动的机制对其依赖的服务能够进行快速故障检测，在持续出错的时候进行隔离，在服务恢复的时候重新联通。所以“断路器”这类设施，对实际生产环境的微服务来说并不是可选的外围组件，而是一个必须的支撑点，如果没有容错性的设计，系统很容易就会被因为一两个服务的崩溃所带来的雪崩效应淹没。可靠系统完全可能由会出错的服务组成，这是微服务最大的价值所在，也是这部开源文档标题“The Fenix Project”的含义。
- **演进式设计**（Evolutionary Design）。容错性设计承认服务会出错，演进式设计则是承认服务会被报废淘汰。一个设计良好的服务，应该是能够报废的，而不是期望得到长存永生。假如系统中出现不可更改、无可替代的服务，这并不能说明这个服务是多么的优秀、多么的重要，反而是一种系统设计上脆弱的表现，微服务所追求的独立、自治，也是反对这种脆弱性的表现。
- **基础设施自动化**（Infrastructure Automation）。基础设施自动化，如CI/CD的长足发展，显著减少了构建、发布、运维工作的复杂性。由于微服务下运维的对象比起单体架构要有数量级的增长，使用微服务的团队更加依赖于基础设施的自动化，人工是很难支撑成百上千乃至成千上万级别的服务的。

&emsp;&emsp;《Microservices》一文中对微服务特征的描写已经相当具体了，文中除了定义微服务是什么，还专门申明了微服务不是什么——微服务不是SOA的变体或衍生品，应该明确地与SOA划清了界线，不再贴上任何SOA的标签。如此，微服务的概念才算是一种真正丰满、独立的架构风格，为它在未来的几年时间里如明星一般闪耀崛起于技术舞台铺下了理论基础。

> &emsp;&emsp;This common manifestation of SOA has led some microservice advocates to reject the SOA label entirely, although others consider microservices to be one form of SOA , perhaps service orientation done right. Either way, the fact that SOA means such different things means it's valuable to have a term that more crisply defines this architectural style<br />
> &emsp;&emsp;由于与SOA具有一致的表现形式，这让微服务的支持者更加迫切地拒绝再被打上SOA的标签，尽管有一些人坚持认为微服务就是SOA的一种变体形式，也许从面向服务方面这个方面来说是对的，但无论如何，SOA与微服务都是两种不同的东西，正因如此，使用一个别的名称来简明地定义这种架构风格就显得更有必要。<br />
> &emsp;&emsp;—— Martin Fowler / James Lewis，[Microservices](https://martinfowler.com/articles/microservices.html)

代表框架            {#framework}
=======================

dubbo               {#dubbo}
-----------------------
<center><img src="/source/architecture-history/microservice-dubbo-architecture.jpg" alt="dubbo架构图"></center>
<center>dubbo架构图</center>
<center>图片来自apache dubbo的官方文档《
		<a href="https://dubbo.apache.org/zh/docs/v2.7/user/preface/architecture/"
		target="_blank" rel="noopener noreferrer">
			Architecture
		</a>
		》
</center>

**节点角色说明**

节点 | 角色说明
------------ | -------------
Provider | 暴露服务的服务提供方
Consumer | 调用远程服务的服务消费方
Registry | 服务注册与发现的注册中心
Monitor | 统计服务的调用次数和调用时间的监控中心
Container | 服务运行容器

**调用关系说明**

1. 服务容器负责启动，加载，运行服务提供者。
2. 服务提供者在启动时，向注册中心注册自己提供的服务。
3. 服务消费者在启动时，向注册中心订阅自己所需的服务。
4. 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。
5. 服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。
6. 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。


Spring Cloud        {#springcloud}
-----------------------
<center><img src="/source/architecture-history/microservice-springcloud-architecture.svg" alt="Spring Cloud架构图"></center>
<center>Spring Cloud架构图</center>
<center>图片来自spring的官方文档《
		<a href="https://spring.io/microservices"
		target="_blank" rel="noopener noreferrer">
			Architecture
		</a>
		》
</center>

**节点角色说明**

节点 | 角色说明
------------ | -------------
Service Registry | 服务注册，比如:Eureka、Consul、Zookeeper
Config Server | 服务配置，比如:Spring Cloud Config
API Gateway | API网关,比如：Spring Cloud Gateway、zuul
Circuit Breaker | 断路器，比如：Resilience4J、Sentinel、Hystrix.
Spring Cloud Sleuth | 链路追踪配套组件，和zipkin配套使用


双刃剑            {#Double-edged-sword}
=======================

&emsp;&emsp;从以上微服务的定义和特征中，你应该可以明显地感觉到微服务追求的是更加自由的架构风格，摒弃了几乎所有SOA里可以抛弃的约束和规定，提倡以“实践标准”代替“规范标准”。可是，如果没有了统一的规范和约束，以前SOA所解决的那些分布式服务的问题，不也就一下子都重新出现了吗？的确如此，服务的注册发现、跟踪治理、负载均衡、故障隔离、认证授权、伸缩扩展、传输通信、事务处理，等等，这些问题，在微服务中不再会有统一的解决方案，即使只讨论Java范围内会使用到的微服务，光一个服务间远程调用问题，可以列入解决方案的候选清单的就有：RMI（Sun/Oracle）、Thrift（Facebook）、Dubbo（阿里巴巴）、gRPC（Google）、Motan2（新浪）、Finagle（Twitter）、brpc（百度）、Arvo（Hadoop）、JSON-RPC、REST，等等；光一个服务发现问题，可以选择的就有：Eureka（Netflix）、Consul（HashiCorp）、Nacos（阿里巴巴）、ZooKeeper（Apache）、Etcd（CoreOS）、CoreDNS（CNCF），等等。其他领域的情况也是与此类似，总之，完全是八仙过海，各显神通的局面。

&emsp;&emsp;微服务所带来的自由是一把双刃开锋的宝剑，当软件架构者拿起这把宝剑，一刃指向SOA定下的复杂技术标准，将选择的权力夺回的同一时刻，另外一刃也正朝向着自己映出冷冷的寒光。微服务时代中，软件研发本身的复杂度应该说是有所降低。一个简单服务，并不见得就会同时面临分布式中所有的问题，也就没有必要背上SOA那百宝袋般沉重的技术包袱。需要解决什么问题，就引入什么工具；团队熟悉什么技术，就使用什么框架。此外，像Spring Cloud这样的胶水式的全家桶工具集，通过一致的接口、声明和配置，进一步屏蔽了源自于具体工具、框架的复杂性，降低了在不同工具、框架之间切换的成本，所以，作为一个普通的服务开发者，作为一个“螺丝钉”式的程序员，微服务架构是友善的。可是，微服务对架构者是满满的恶意，对架构能力要求已提升到史无前例的程度，笔者在这部文档的多处反复强调过，技术架构者的第一职责就是做决策权衡，有利有弊才需要决策，有取有舍才需要权衡，如果架构者本身的知识面不足以覆盖所需要决策的内容，不清楚其中利弊，恐怕也就无可避免地陷入选择困难症的困境之中。

&emsp;&emsp;微服务时代充满着自由的气息，微服务时代充斥着迷茫的选择。软件架构不会止步于自由，微服务仍不是架构探索终点，如果有下一个时代，笔者希望是信息系统能同时拥有微服务的自由权利，围绕业务能力构建自己的服务而不受技术规范管束，但同时又不必以承担自行解决分布式的问题的责任为代价。管他什么利弊权衡！小孩子才做选择题，成年人全部都要！