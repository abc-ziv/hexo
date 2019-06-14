---
title:  "细说事物(第一天)"
date: 2017-08-20 12:00:00
updated: 2017-08-20 12:00:00
comments: true
categories: 数据库
tags:
    - MySQL
---

# 事务简介
事务就是**锁**和**并发**的结合体.
* **优势**:容易理解
* **劣势**:性能较低

扩展 - `DRDS/TDDL‵
 <!-- more -->

### ACID保证事务的完整性
* 原子性（Atomicity）
* 一致性（Consistency）
* 隔离性（Isolation）
* 持久性（Durability）

### 单个事务单元
* 商品要建立一个基于GMT_Modified的索引
* 从数据库读取一行记录
* 向数据库写入一行记录,同时更新这记录的所有索引
* 删除整张表

### 一组事务单元
* Bob->Smith 100元
* Smith->Joe 100元
* Joe->Bob 100元

# 事务
### 产生的原因
**事务单元之间的Happen-before关系**
* 读写
* 写读
* 读读
* 写写

### 排队法
**序列化读写**
* **优势**:不需要冲突控制
* **劣势**:慢速设备

<table>
<tr>
<td>事务单元</td>
<td>->读</td>
<td>->读</td>
<td>->写</td>
<td>->读</td>
<td>->写</td>
<td>->写</td>
<td>->读</td>
</tr>
</table>

### 排他锁
**针对同一单元的访问进行控制**
<table>
<tr>
<td>事务单元1</td>
<td>->读</td>
<td>->读</td>
<td>->写</td>
<td>->读</td>
<td>->写</td>
<td>->写</td>
<td>->读</td>
</tr>
<tr>
<td>事务单元2</td>
<td>->写</td>
<td>->读</td>
<td>->写</td>
<td>->读</td>
<td>->写</td>
<td>->读</td>
<td>->读</td>
</tr>
</table>

### 读写锁
**针对读读场景进行优化**
<table>
<tr>
<td>事务单元1</td>
<td>->写</td>
<td>->读</td>
<td>->写</td>
<td>->读</td>
<td>->写</td>
<td>->写</td>
<td>->读</td>
</tr>
<tr>
<td>事务单元2</td>
<td></td>
<td>->读</td>
<td></td>
<td>->读</td>
<td></td>
<td></td>
<td>->读</td>
</tr>
</table>


### MVCC
**本质上来说就是copy on write,能够做到写不阻塞读**
<table>
<tr>
<td>事务单元1</td>
<td>->写</td>
<td>->写</td>
<td>->写</td>
<td>->写</td>
<td>->写</td>
<td>->写</td>
<td>->写</td>
</tr>
<tr>
<td>事务单元2</td>
<td></td>
<td>->读</td>
<td></td>
<td>->读</td>
<td></td>
<td>->读</td>
<td>->读</td>
</tr>
<tr>
<td>事务单元3</td>
<td></td>
<td>->读</td>
<td></td>
<td></td>
<td></td>
<td></td>
<td>->读</td>
</tr>
</table>

# 事务处理
### 谁先谁后
**在MMVC中一个读请求应该读哪一个写之后的数据?**
* SCN(Oracle)
* Trx_id(Innodb)

### 故障恢复
* 业务属性不匹配
* 系统崩溃

### 死锁和死锁检测
**死锁产生的原因**
* 两个线程
* 不同方向
* 相同资源

**死锁的解决方法**
* 降低隔离界别
* 碰撞检测
* 等锁超时
