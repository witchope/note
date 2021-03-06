# Spring Cloud Dubbo 微服务总结

- [Spring Cloud Dubbo 微服务总结](#spring-cloud-dubbo-微服务总结)
  - [什么是微服务？](#什么是微服务)
  - [Spring Cloud 和 Dubbo 有哪些区别？](#spring-cloud-和-dubbo-有哪些区别)
  - [Spring Boot 和 Spring Cloud 理解](#spring-boot-和-spring-cloud-理解)
  - [Eureka 和 Zookeeper 有什么异同？](#eureka-和-zookeeper-有什么异同)

## 什么是微服务？

微服务化的核心就是将传统的一站式应用，根据业务（可以是其他规则）拆分成一个一个的服务，彻底地去耦合，每一个微服务提供单个业务功能的服务，一个服务做一件事，从技术角度看就是一种小而独立的处理过程，类似进程概念，能够自行单独启动
或销毁，拥有自己独立的数据库。

微服务架构是⼀种架构模式，它提倡将单⼀应⽤程序划分成⼀组⼩的服务，服务之间互相协调、互相配合，为⽤户提供最终价值。每个服务运⾏在其独⽴的进程中 ，服务与服务间采⽤轻量级的通信机制互相协作（通常是基于 HTTP 协议的 RESTful API）。每个服务都围绕着具体业务进⾏构建，并且能够被独⽴的部署到⽣产环境、类⽣产环境等。另外， 应当尽量避免统⼀的、集中式的服务管理机制， 对具体的⼀个服务⽽⾔，应根据业务上下⽂，选择合适的语⾔、⼯具对其进⾏构建。

## Spring Cloud 和 Dubbo 有哪些区别？

Spring Cloud 抛弃了 Dubbo 的 RPC 通信，采用的是基于 HTTP 的 REST 方式。

严格来说，这两种方式各有优劣。虽然从一定程度上来说，后者牺牲了服务调用的性能，但也避免了上面提到的原生 RPC 带来的问题。而且 REST 相比 RPC 更为灵活，服务提供方和调用方的依赖只依靠一纸契约，不存在代码级别的强依赖，这在强调快速演化的微服务环境下，显得更加合适。

Spring Cloud 的功能比 Dubbo 更加强大，涵盖面更广，而且作为 Spring 的拳头项目，它也能够与 Spring Framework、Spring Boot、Spring Data、Spring Batch 等其他 Spring 项目完美融合，这些对于微服务而言是至关重要的。

社区支持与更新力度方面也是 Spring Cloud 更胜一筹。

## Spring Boot 和 Spring Cloud 理解

Spring Boot 专注于快速方便的开发单个个体微服务的脚手架。

Spring Cloud 是关注全局的微服务协调整理治理框架，它将 Spring Boot 开发的一个个单体微服务整合并管理起来，为各个服务之间提供，配置管理、服务发现、断路器、路由、微代理、事件总线、全局锁、决策竞选、分布式会话等等集成服务。

Spring Cloud 依赖于 Spring Boot。

## Eureka 和 Zookeeper 有什么异同？

著名的 CAP 理论指出，一个分布式系统不可能同时满足 C (一致性)、A (可用性)和 P (分区容错性)。由于分区容错性 P 在是分布式系统中必须要保证的，因此我们只能在 A 和 C 之间进行权衡。

因此 Zookeeper 保证的是 CP，Eureka 则是 AP。

在云部署的环境下，因网络问题使得 Zookeeper 集群失去 master 节点是较大概率会发生的事，虽然服务能够最终恢复，但是漫长的选举时间导致的注册长期不可用是不能容忍的。

Eureka各个节点都是平等的，几个节点挂掉不会影响正常节点的工作，剩余的节点依然可以提供注册和查询服务。只不过信息可能滞后（不保证强一致）。

因此， Eureka可以很好的应对因网络故障导致部分节点失去联系的情况，而不会像 Zookeeper 那样使整个注册服务瘫痪。
