- 分布式环境的特点

  - 分布性
  - 并发性
    程序运行过程中，并发性操作是很常见的。比如同一个分布式系统中的多个节点，同时访问一个共享资源。数据库、分布式存储 。
  - 无序性
    进程之间的消息通信，会出现顺序不一致问题

- 面临的问题

  - 网络通信
    网络本身不可靠，导致通信不可靠
  - 网络分区(脑裂)
    当网络发生异常导致分布式系统中部分节点之间的网络延时不断增大，最终导致组成分布式架构的所有节点，只有部分节点能够正常通信。
  - 三态
    在分布式架构里面，除了成功、失败，还有超时。
  - 分布式事务
    （ACID (原子性、一致性、隔离性、持久性)）

- 中心化和去中心化

  - 中心化
    ---  领导 --- 冷备 和 热备 ------- 
  - 去中心化
    分布式架构里面，很多的架构思想采用的是：当集群发生故障的时候，集群中的人群会自动“选举”出一个新的领导。最典型的是： zookeeper / etcd。

- 事务--ACID/CAP/BASE理论

  - [ACID](https://monkeysayhi.github.io/2016/11/26/%E4%BA%8B%E5%8A%A1%E7%9A%84ACID%E5%92%8C%E5%9B%9B%E4%B8%AA%E9%9A%94%E7%A6%BB%E7%BA%A7%E5%88%AB/)
    - **原子性（Atomicity）**：一个事务必须被视为一个不可分割的最小工作单元，整个事务中的所有操作要么全部提交成功，要么全部失败回滚，对于一个事务来说，不可能只执行其中的一部分操作。
    - **一致性（Consistency）**：数据库总是从一个一致性的状态转换到另一个一致性的状态。
    - **隔离性（Isolation）**：通常来说，一个事务所做的修改在最终提交以前，对其他事务是不可见的。针对不同的业务需求，*隔离性分为4个级别：读未提交、读已提交、可重复读、串行化。见下*。
    - **持久性（Durability）**：通常来说，一旦事务提交，则其所做的修改会永久保存到数据库（即使系统崩溃，修改的数据也不会丢失）。针对不同的业务需求，持久性也分为多个级别，此处略。
  - CAP
    一致性 Consistency）: 所有节点上的数据，时刻保持一致
    可用性（Availability）：每个请求都能够收到一个响应，无论响应成功或者失败
    分区容错 （Partition-tolerance）：表示系统出现脑裂以后，可能导致某些server与集群中的其他机器失去联系 .
    CAP理论仅适用于原子读写的Nosql场景，不适用于数据库系统
  - BASE
    基于CAP理论，CAP理论并不适用于数据库事务（因为更新一些错误的数据而导致数据出现紊乱，无论什么样的数据库高可用方案都是徒劳） ，虽然XA事务可以保证数据库在分布式系统下的ACID特性，但是会带来性能方面的影响；
    eBay尝试了一种完全不同的套路，放宽了对事务ACID的要求。提出了BASE理论。
    BASE理论：
    Basically available  ： 数据库采用分片模式， 把100W的用户数据分布在5个实例上。如果破坏了其中一个实例，仍然可以保证80%的用户可用
    soft-state：  在基于client-server模式的系统中，server端是否有状态，决定了系统是否具备良好的水平扩展、负载均衡、故障恢复等特性。
    Server端：承诺会维护client端状态数据，这个状态仅仅维持一小段时间, 这段时间以后，server端就会丢弃这个状态，恢复正常状态
    Eventually consistent：数据的最终一致性

- 主流架构模型-SOA 架构和微服务架构

  - SOA服务

    - SOA 全称（Service Oriented Architecture），中文意思为“面向服务的架构”，他是一种设计方法，其中包含多个服务，服务之间通过相互依赖最终提供一系列的功能。一个服务通常以独立的形式存在与操作系统进程中。各个服务之间通过网络调用,

    - 跟 SOA 相提并论的还有一个 ESB（企业服务总线），简单来说 ESB 就是一根管道，用来连接各个服务节点。为了集成不同系统，不同协议的服务，ESB 做了消息的转化解释和路由工作，让不同的服务互联互通；

      ![img](https://img.mubu.com/document_image/22da0a3d-c20f-40a8-b78d-48b475b53ce7-862021.jpg)

    - SOA 所解决的核心问题

      - \1. 系统集成：站在系统的角度，解决企业系统间的通信问题，把原先散乱、无规划的系统间的网状结构，梳理成规整、可治理的系统间星形结构，这一步往往需要引入一些产品，比如 ESB、以及技术规范、服务管理规范；这一步解决的核心问题是【有序】
      - \2. 系统的服务化：站在功能的角度，把业务逻辑抽象成可复用、可组装的服务，通过服务的编排实现业务的快速再生，目的：把原先固有的业务功能转变为通用的业务服务，实现业务逻辑的快速复用；这一步解决的核心问题是【复用】
      - \3. 业务的服务化：站在企业的角度，把企业职能抽象成可复用、可组装的服务；把原先职能化的企业架构转变为服务化的企业架构，进一步提升企业的对外服务能力；“前面两步都是从技术层面来解决系统调用、系统功能复用的问题”。第三步，则是以业务驱动把一个业务单元封装成一项服务。这一步解决的核心问题是【高效】

  - 微服务架构

    - 微服务架构其实和 SOA 架构类似,微服务是在 SOA 上做的升华，微服务架构强调的一个重点是“业务需要彻底的组件化和服务化”，原有的单个业务系统会拆分为多个可以独立开发、设计、运行的小应用。这些小应用之间通过服务完成交互和集成。组件表示一个可以独立更换和升级的单元，就像 PC 中的CPU、内存、显卡、硬盘一样，独立且可以更换升级而不影响其他单元。如果我们把 PC 作为组件以服务的方式构建，那么这台 PC 只需要维护主板和一些必要的外部设备。CPU、内存、硬盘都是以组件方式提供服务，PC 需要调用 CPU 做计算处理，只需要知道 CPU 这个组件的地址即可
    - 微服务的特征
      - \1. 通过服务实现组件化
      - \2. 按业务能力来划分服务和开发团队
      - \3. 去中心化 
      - \4. 基础设施自动化（devops、自动化部署

  - SOA和微服务架构的差别

    - \1. 微服务不再强调传统 SOA 架构里面比较重的 ESB 企业服务总线，同时 SOA 的思想进入到单个业务系统内部实现真正的组件化
    - \2. Docker 容器技术的出现，为微服务提供了更便利的条件，比如更小的部署单元，每个服务可以通过类似 Node或者 Spring Boot 等技术跑在自己的进程中。
    - \3. 还有一个点大家应该可以分析出来，SOA 注重的是系统集成方面，而微服务关注的是完全分离

- 什么是分布式架构下的高可用设计

  - \1. 避免单点故障
    - a) 负载均衡技术（failover/选址/硬件负载/软件负载/去中心化的软件负载（gossip(redis-cluster)））
    - b) 热备（linux HA）
    - c) 多机房（同城灾备、异地灾备）
  - \2. 应用的高可用性
    - a) 故障监控（系统监控（cpu、内存）/链路监控/日志监控） 自动预警
    - b) 应用的容错设计、（服务降级、限流）自我保护能力
    - c) 数据量（数据分片、读写分离）

- 分布式架构下的可伸缩设计

  - 垂直伸缩 提升硬件能力
  - 水平伸缩 增加服务器

- 加速静态内容访问速度的 CDN

  - CDN 是 Content Delivery Network 的缩写，表示的是内容分发网络。CDN 的作用是把用户需要的内容分发到离用户最近的地方，这样可以是用户能够快熟获取所需要的内容。CDN 其实就是一种网络缓存技术，能够把一些相对稳定的资源放到距离最终用户较近的地方，一方面可以节省整个广域网的贷款消耗，另外一方面可以提升用户的访问速度，改进用户体验。我们一般会把静态的文件（图片、脚本、静态页面）放到 CDN 中

    ![img](https://img.mubu.com/document_image/fcdeca68-af80-47fd-b03e-d70928aea031-862021.jpg)

    - \1. 当用户点击网站页面上的内容 URL，经过本地 DNS 系统解析，DNS系统会最终将域名的解析权交给 CNAME 指向的 CDN 专用 DNS 服务器
    - \2. CDN 的 DNS 服务器将 CDN 的全局负载均衡设备 IP 地址返回用户
    - \3. 用户向 CDN 的全局负载均衡设备发起内容 URL 访问请求
    - \4. CDN全局负载均衡设备根据用户IP地址，以及用户请求的内容URL，选择一台用户所属区域的区域负载均衡设备
    - \5. 区域负载均衡设备会为用户选择一台合适的缓存服务器提供服务，选择的依据包括：根据用户 IP 地址，判断哪一台服务器距用户最近；根据用户所请求的 URL 中携带的内容名称，判断哪一台服务器上有用户所需内容；查询各个服务器当前的负载情况，判断哪一台服务器尚有服务能力。基于以上这些条件的综合分析之后，区域负载均衡设备会向全局负载均衡设备返回一台缓存服务器的 IP 地址
    - \6. 局负载均衡设备把服务器的 IP 地址返回给用户用户向缓存服务器发起请求，缓存服务器响应用户请求，将用户所需内容传送到用户终端。如果这台缓存服务器上并没有用户想要的内容，而区域均衡设备依然将它分配给了用户，那么这台服务器就要向它的上一级缓存服务器请求内容，直至追溯到网站的源服务器将内容拉到本地。

  - 什么情况下用CDN？最适合的是那些不会经常变化的内容，比如图片，JS 文件，CSS 文件，图片文件包括程序模板中的，CSS 文件中用到的背景图片，还有就是作为网站内容组成部分的那些图片，都可以；