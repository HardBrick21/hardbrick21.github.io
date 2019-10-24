---
title: 面试总结（19/10/24）
date: 2019-10-24 12:00:00 +0800
categories: [Blogging, Java]
tags: [面试总结]
seo:
  date_modified: 2019-10-24 23:18:56 +0800
---

## 面试总结（19-10-24）

1. spring 中的 IoC和AOP

> IoC： IoC即控制反转，是一种让框架代替我们管理对象的思想，主要通过依赖注入来实现。Spring中的IoC最重要的是容器，Spring中有两种容器  BeanFectory 和 ApplicationContext  
AOP是面向切面编程，是对于面向对象的补充，主要是通过代理模式来实现



2. Java 中的基本数据类型和所占字节数

|  数据类型   | 字节  |
|  :----  | :----  |
| byte  | 1 |
| short | 2 |
| int   | 4 |
| long  | 8 |
| float | 4 |
| double| 8 |
| boolean|1 |


3. 保证线程安全的方法，CAS了解吗

> synchronized 和 Lock ， Lock使用CAS实现的，CAS算法存在着三个参数，内存值V，旧的预期值A，以及要更新的值B。当且仅当内存值V和预期值B相等的时候，才会将内存值修改为B，否则什么也不做  

CAS的缺点：

* 循环时间长开销很大。
* 只能保证一个共享变量的原子操作。
* ABA问题。  

循环时间长开销很大：  
我们可以看到getAndAddInt方法执行时，如果CAS失败，会一直进行尝试。如果CAS长时间一直不成功，可能会给CPU带来很大的开销。

 

只能保证一个共享变量的原子操作：  
当对一个共享变量执行操作时，我们可以使用循环CAS的方式来保证原子操作，但是对多个共享变量操作时，循环CAS就无法保证操作的原子性，这个时候就可以用锁来保证原子性。

什么是ABA问题？ABA问题怎么解决？  
如果内存地址V初次读取的值是A，并且在准备赋值的时候检查到它的值仍然为A，那我们就能说它的值没有被其他线程改变过了吗？

如果在这段期间它的值曾经被改成了B，后来又被改回为A，那CAS操作就会误认为它从来没有被改变过。这个漏洞称为CAS操作的“ABA”问题。Java并发包为了解决这个问题，提供了一个带有标记的原子引用类“AtomicStampedReference”，它可以通过控制变量值的版本来保证CAS的正确性。因此，在使用CAS前要考虑清楚“ABA”问题是否会影响程序并发的正确性，如果需要解决ABA问题，改用传统的互斥同步可能会比原子类更高效。


4. shiro安全组件的作用

> 1. 验证用户身份  2. 用户访问控制  3. 单点登录

5. rebbitMQ有哪几种模式

![六种工作模式](http://www.iamlintao.com/wp-content/uploads/2018/08/acdc9c51d66be95079f81f484c40f104_906x902.png)  

1、HelloWorld  
这种方式是最简单的一个应用,P为生产方,C为接收方.

2、Work模式  
一个生产者，多个消费者，每个消费者获取到的消息唯一。  

3、订阅模式  
一个生产者发送的消息会被多个消费者获取。  
生产者：可以将消息发送到队列或者是交换机。  
消费者：只能从队列中获取消息。  
如果消息发送到没有队列绑定的交换机上，那么消息将丢失。  

4、路由模式  
1、 发送消息到交换机并且要指定路由key  
2、 消费者将队列绑定到交换机时需要指定路由key   

5、通配符模式  
将路由键和某模式进行匹配，此时队列需要绑定在一个模式上，“#”匹配一个词或多个词，“\*”只匹配一个词。  

5、RPC模式  

远程调用  

6. Druid线程池的配置  

[DRUID连接池的实用 配置详解](https://blog.csdn.net/zhangjinwei417/article/details/92823438)


7. Mybatis默认开启什么缓存

Mybatis 默认一级查询缓存是开启状态，且不能关闭。  
二级缓存也是默认开启的。（想开启就不必做任何配置）

  * 一级缓存  

MyBatis的一级查询缓存（也叫作本地缓存）是基于org.apache.ibatis.cache.impl.PerpetualCache 类的 HashMap本地缓存，其作用域是SqlSession  
在同一个SqlSession中两次执行相同的 sql 查询语句，第一次执行完毕后，会将查询结果写入到缓存中，第二次会从缓存中直接获取数据，而不再到数据库中进行查询，这样就减少了数据库的访问，从而提高查询效率。  
当一个 SqlSession 结束后，该 SqlSession 中的一级查询缓存也就不存在了。  
增删改会清空缓存，无论是否commit 当SqlSession关闭和提交时，会清空一级缓存  

  * 二级缓存  

MyBatis的二级缓存是mapper范围级别的  
SqlSession关闭后才会将数据写到二级缓存区域  
增删改操作，无论是否进行提交commit()，均会清空一级、二级缓存  
二级缓存会使用 Least Recently Used (LRU，最近最少使用的）算法来收回。  
根据时间表（如 no Flush Interval ，没有刷新间隔），缓存不会以任何时间顺序来刷新 。  
缓存会存储集合或对象（无论查询方法返回什么类型的值）的 1024 个引用。
缓存会被视为 read/write （可读／可写）的，意味着对象检索不是共享的，而且可以安全地被调用者修改，而不干扰其他调用者或线程所做的潜在修改 。

```
//在保证二级缓存的全局配置开启的情况下，给某个xml开启二级缓存只需要在xml中添加<cache />即可
// mybatis-config.xml 中配置
<settings>
    默认值为 true。即二级缓存默认是开启的
    <setting name="cacheEnabled" value="true"/>
</settings>

// 具体mapper.xml 中配置
<mapper namespace="cn.itcast.mybatis.mapper.UserMapper">
    <!-- 开启本mapper的namespace下的二级缓存
    type：指定cache接口的实现类的类型，mybatis默认使用PerpetualCache
    要和ehcache整合，需要配置type为ehcache实现cache接口的类型-->

    <cache />

</mapper>
```
