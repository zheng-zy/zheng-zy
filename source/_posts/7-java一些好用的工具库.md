---
title: java一些好用的工具库
description: java一些好用的工具库
tags:
  - Java
categories: tech
abbrlink: 22055
date: 2017-04-08 10:38:00
---

### Lombok项目

使用注解来减少Java中的重复代码，比如getter，setters，非空检查，生成的Builder等。

val - 总算有了！无忧的final本地变量。

* @NonNull - 或：我如何学会不再担心并爱上了非空异常（NullPointerException）。

* @Cleanup - 自动的资源管理：安全调用你的close() 方法，无需任何麻烦。

* @Getter / @Setter - 再也不用写 public int getFoo() {return foo;}了。

* @ToString - 无需启动调试器来检查你的字段：就让Lombok来为你生成一个toString方法吧！

* @EqualsAndHashCode - 实现相等的判断变得容易了：它会从你的对象的字段里为你生成hashCode和equals方法的实现。

* @NoArgsConstructor, @RequiredArgsConstructor and @AllArgsConstructor - 定做构造函数：为你生成各种各样的构造函数，包括无参的，每一个final或非空的字段作为一个参数的，或者每一个字段都作为参数的。

* @Data - 所有的都同时生成：这是一个快捷方式，可以为所有字段生成@ToString, @EqualsAndHashCode, @Getter注解，以及为所有非final的字段生成@Setter注解，以及生成@RequiredArgsConstructor！

* @Value - 声明一个不可变类变得非常容易。

* @Builder - … 而且鲍伯是你叔叔：创建对象的无争议且奢华的接口！

* @SneakyThrows - 在以前没有人抛出检查型异常的地方大胆的抛出吧！

* @Synchronized - 正确的实现同步：不要暴露你的锁。

* @Getter(lazy=true) 懒惰是一种美德！

* @Log - 船长日志，星历24435.7： “那一行又是什么呢？”



GitHu：https://github.com/rzwitserloot/lombok

Website：https://projectlombok.org/





### OkHttp

HTTP 是现代应用程序实现网络互联的机制。是进行数据和媒体传输的主要方式。有效使用 HTTP 能让负载更快，节省带宽。

OkHttp 是 HTTP 作为有效代理，默认：

HTTP/2 允许发往同一主机的请求共用一个接口。

连接池能减少请求延迟。 (如果 HTTP/2 不可用).

透明 GZIP 能压缩下载包大小。

响应缓存可以完全避免重复请求的网络。



### 版权声明
作者：苏陌年
出处：http://www.zhengzy.top/ 
版权所有，欢迎保留原文链接进行转载：)
