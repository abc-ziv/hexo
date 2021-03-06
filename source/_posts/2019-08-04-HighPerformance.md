---
title: "《大型网站技术架构》-- 网站的高性能架构"
date: 2019-08-04
comments: true
categories: 架构
tags:
    - watch
    - 大型网站技术架构：核心原理与案例分析
---
# 网站性能测试
## 性能测试指标：
1. 响应时间；
2. 并发数；
3. 吞吐量；
4. 性能计数器；
<!-- more -->

## 性能测试方法：
1. 性能测试；
2. 负载测试；
3. 压力测试；
4. 稳定性测试；

## 性能优化策略：
1. 性能分析：检查请求处理各个环节的日志，分析哪个环节响应时间不合理，检查监控数据分析影响性能的因素；
2. 性能优化：Web前端优化，应用服务器优化，存储服务器优化；

# Web前端性能优化
## 浏览器访问优化：
1. 减少http请求：`因为http是无状态的，每次请求的开销都比较昂贵`（需要建立通信链路、进行数据传输，而服务器端对于每个http请求都需要启动独立的线程去处理）；减少http的主要手段是合并CSS、合并JS、合并图片（CSS精灵，利用偏移定位image）；
2. 使用浏览器缓存：设置http头中Cache-Control和Expires属性；
3. 启用压缩：可以对html、css、js文件启用Gzip压缩，可以达到较高的压缩效率，但是压缩会对服务器及浏览器产生一定的压力；
4. CSS放页面最上面，JS放页面最下面：浏览器会在下载完全部CSS之后才开始对整个页面进行渲染，因此最好将CSS放在页面最上面；而浏览器在加载JS后会立即执行，有可能会阻塞整个页面，造成页面显示缓慢，因此最好将JS放在页面最下面；
5. 减少Cookie传输：一方面，太大的Cookie会严重影响数据传输；另一方面，对于某些静态资源的访问（如CSS、JS等）发送Cookie没有意义；

## CDN加速：
CDN（内容分发网络）仍然是一个缓存，它将数据缓存在离用户最近的地方，便于用户以最快速度获取数据。即所谓的“网络访问第一跳”，如下图所示：
<img src="/images/Framework/012.jpg">
`CDN只将访问频度很高的热点内容（例如：图片、视频、CSS、JS脚本等访问频度很高的内容）进行缓存`，可以极大地加快用户访问速度，减少数据中心负载。

## 反向代理：
反向代理服务器位于网站机房，代理网站Web服务器接收Http请求，对请求进行转发，如下图所示：
<img src="/images/Framework/013.png">
反向代理服务器具有以下功能：
1. 保护网站安全：任何来自Internet的请求都必须先经过代理服务器；
2. 通过配置缓存功能加速Web请求：减轻真实Web服务器的负载压力；
3. 实现负载均衡：均衡地分发请求，平衡集群中各个服务器的负载压力；

# 应用服务器性能优化
## 分布式缓存：

> PS：网站性能优化第一定律：`优先考虑使用缓存优化性能`。缓存是指将数据存储在相对较高访问速度的存储介质中（如内存），以供系统进行快速处理响应用户请求。

1. 缓存本质是一个`内存Hash表`，数据以(Key,Value)形式存储在内存中。
<img src="/images/Framework/014.png">
2. 缓存主要用来存放那些`读写比很高、很少变化`的数据，如商品的类目信息、热门商品信息等。这样，应用程序读取数据时，先到缓存中取，如缓存中没有或失效，再到数据库中取出，重新写入缓存以供下一次访问。因此，可以很好地`改善系统性能，提高数据读取速度，降低存储访问压力`。
3. 分布式缓存架构：一方面是以以JBoss Cache为代表的`互相通信`派；另一方面是以Memcached为代表的`互不通信`派；

JBoss Cache需要将缓存信息同步到集群中的所有机器，代价比较大；而Memcached采用一种集中式的缓存集群管理，缓存与应用分离部署，应用程序通过`一致性Hash算法`选择缓存服务器远程访问缓存数据，缓存服务器之间互不通信，因而集群规模可以轻易地扩容，具有良好的伸缩性。
<img src="/images/Framework/015.png">
Memcached由两个核心组件组成：服务端（ms）和客户端（mc），在一个memcached的查询中，mc先通过计算key的hash值来确定kv对所处在的ms位置。当ms确定后，客户端就会发送一个查询请求给对应的ms，让它来查找确切的数据。因为这之间没有交互以及多播协议，所以 memcached交互带给网络的影响是最小化的。
<img src="/images/Framework/016.jpg">

## 异步操作：
1. 使用`消息队列`将调用异步化，可改善网站的扩展性，还可改善网站性能；
<img src="/images/Framework/017.jpg">

2. 消息队列具有`削峰`的作用->将短时间高并发产生的事务消息存储在消息队列中，从而削平高峰期的并发事务；
> PS：任何可以晚点做的事情都应该晚点再做。前提是：这个事儿确实可以晚点再做。

## 使用集群：
1. 在高并发场景下，使用`负载均衡`技术为一个应用构建多台服务器组成的服务器集群；
<img src="/images/Framework/018.jpg">

2. 可以避免单一服务器因负载压力过大而响应缓慢，使用户请求具有`更好的响应延迟特性`；

3. 负载均衡可以采用硬件设备，也可以采用软件负载。商用硬件负载设备（例如出名的F5）成本通常较高（一台几十万上百万很正常），所以在条件允许的情况下我们会采用软负载，软负载解决的两个核心问题是：选谁、转发，其中最著名的是`LVS`（Linux Virtual Server）。
> PS：LVS是四层负载均衡，也就是说建立在OSI模型的第四层——传输层之上，传输层上有我们熟悉的TCP/UDP，LVS支持TCP/UDP的负载均衡。
LVS的转发主要通过修改IP地址（NAT模式，分为源地址修改SNAT和目标地址修改DNAT）、修改目标MAC（DR模式）来实现。有关LVS的详情请参考：[http://www.importnew.com/11229.html](http://www.importnew.com/11229.html)

## 代码优化：
1. 多线程：使用多线程的原因：一是IO阻塞，二是多CPU，都是为了最大限度地利用CPU资源，提高系统吞吐能力，改善系统性能；
2. 资源复用：目的是`减少开销很大的系统资源的创建和销毁`，主要采用两种模式实现：单例（Singleton）和对象池（Object Pool）。例如，在.NET开发中，经常使用到的线程池，数据库连接池等，本质上都是对象池。
3. 数据结构：在不同场合合理使用恰当的数据结构，可以极大优化程序的性能。
4. 垃圾回收：理解垃圾回收机制有助于程序优化和参数调优，以及编写内存安安全的代码。这里主要针对Java（JVM）和C#（CLR）一类的具有GC（垃圾回收机制）的语言。

# 存储性能优化
## 机械硬盘 还是 固态硬盘？
1. 机械硬盘：通过马达驱动磁头臂，带动磁头到指定的磁盘位置访问数据。它能够实现`快速顺序读写，慢速随机读写`。
2. 固态硬盘（又称SSD）：无机械装置，数据存储在可持久记忆的硅晶体上，因此可以像内存一样`快速随机访问`。

在目前的网站应用中，大部分应用访问数据都是随机的，这种情况下SSD具有更好的性能表现，但是性价比有待提升。

## B+树 vs LSM树
1. 传统关系型数据库广泛采用B+树，B+树是对数据排好序后再存储，加快数据检索速度。
> PS：目前大多数DB多采用两级索引的B+树，树的层次最多三层。因此可能需要`5次磁盘访问`才能更新一条记录（三次磁盘访问获得数据索引及行ID，一次数据文件读操作，一次数据文件写操作，终于知道数据库操作有多麻烦多耗时了）

2. NoSQL（例如：HBase）产品广泛采用LSM树：
具体思想是：将对数据的修改增量保持在内存中，达到指定的大小限制后将这些修改操作批量写入磁盘。不过读取的时候稍微麻烦，需要合并磁盘中历史数据和内存中最近的修改操作，所以写入性能大大提升，读取时可能需要先看是否命中内存，否则需要访问较多的磁盘文件。
LSM树的原理是：把一棵大树拆分成N棵小树，它首先写入内存中，随着小树越来越大，内存中的小树会被清除并写入到磁盘中，磁盘中的树定期可以做合并操作，合并成一棵大树，以优化读性能。
LSM树的优势在于：在LSM树上进行一次数据更新不需要磁盘访问，在内存即可完成，速度远快于B+树。

# 总结
归根结底，技术是为业务服务的，技术选型和架构决策依赖业务规划乃至企业战略规划，离开业务发展的支撑和驱动，技术走不远，甚至还会迷路。