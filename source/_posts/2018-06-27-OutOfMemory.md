---
title: "Apache内存溢出的分析与解决"
date: 2018-06-12 27:00:00
updated: 2018-06-27 12:00:00
comments: true
categories: 服务器
tags:
    - Apache
---
# 总结
Apache长时间占用内存大的问题的根本原因在MySQL查询时间过长。请开启**慢查询**功能，去解决!

修改Apache治标不治本~

 <!-- more -->
 
查看版本
```bash
httpd -v

Server version: Apache/2.4.29 (Unix)
Server built:   Jan 11 2018 22:38:20
```

修改Apache在prefork工作模式参数
```bash
sudo vi /usr/local/httpd/conf/extra/httpd-mpm.conf
```


具体:
```bash
# prefork MPM
<IfModule mpm_prefork_module>
StartServers             5
#apache启动时候默认开始的子进程数
MinSpareServers          5
#最小的闲置子进程数
MaxSpareServers         10
#最大的闲置子进程数
MaxRequestWorkers      250
#MaxRequestWorkers设置了允许同时的最大接入请求数量。任何超过MaxRequestWorkers限制的请求将进入等候队列在apache2.3.1以前的版本MaxRequestWorkers被称为MaxClients旧的名字仍旧被支持。
MaxConnectionsPerChild   100  #重点!
#设置的是每个子进程可处理的请求数。每个子进程在处理了“MaxConnectionsPerChild”个请求后将自动销毁。0意味着无限即子进程永不销毁。虽然缺省设为0可以使每个子进程处理更多的请求但如果设成非零值也有两点重要的好处1、可防止意外的内存泄漏。2、在服务器负载下降的时侯会自动减少子进程数。因此可根据服务器的负载来调整这个值。在Apache2.3.9之前称之为MaxRequestsPerChild。
</IfModule>
```

重启apache
```bash
/etc/init.d/httpd restart
```

## 注1:
MaxRequestWorkers是这些指令中最为重要的一个设定的是 Apache可以同时处理的请求是对Apache性能影响最大的参数。如果请求总数已达到这个值可通过`ps -ef|grep http|wc -l`来确认那么后面的请求就要排队直到某个已处理请求完毕。这就是系统资源还剩下很多而HTTP访问却很慢的主要原因。虽然理论上这个值越大可以处理的请求就越多建议将初始值设为(以Mb为单位的最大物理内存/2),然后根据负载情况进行动态调整。比如一台4G内存的机器那么初始值就是4000/2=2000。
s
## 注2:
prefork 控制进程在最初建立“StartServers”个子进程后为了满足MinSpareServers设置的需要创建一个进程等待一秒钟继续创建两 个再等待一秒钟继续创建四个……如此按指数级增加创建的进程数最多达到每秒32个直到满足MinSpareServers设置的值为止。这种模式 可以不必在请求到来时再产生新的进程从而减小了系统开销以增加性能。MaxSpareServers设置了最大的空闲进程数如果空闲进程数大于这个 值Apache会自动kill掉一些多余进程。这个值不要设得过大但如果设的值比MinSpareServers小Apache会自动把其调整为 MinSpareServers+1。如果站点负载较大可考虑同时加大MinSpareServers和MaxSpareServers。  

## 注3:
ServerLimit和MaxClientsMaxRequestWorkers有什么区别呢?

是因为在apache1时代控制最大进程数只有MaxClients这个参数并且这个参数最大值为256并且是写死了的试图设置为超过256是无效的这是由于apache1时代的服务器硬件限制的。但是apache2时代由于服务器硬件的升级硬件已经不再是限制所以使用ServerLimit这个参数来控制最大进程数ServerLimit值>=MaxClient值才有效。ServerLimit要放在MaxClients之前值要不小于MaxClients。


## 常用命令
查看请求总数
```bash
ps -ef|grep http|wc -l 
```

查看平均负载(loadavg)
```bash
$cat /proc/loadavg 
```

查看TCP连接数
```bash
netstat -ant | grep :80 | wc -l 
```

查看系统运行情况
```bash
top 
shift + M 
```


