---
title: ubuntu16.04LTS+shadowsocks+proxychains全局访问
description: 起因是因为谷歌浏览器登录问题，如果没有配置全局使用代理登录报错
categories: tech
tags: [Linux,Shadowsocks]
abbrlink: 30918
date: 2017-04-07 17:22:15
---

### 前提

[安装shadowsocks服务](http://zhengzy.oschina.io/posts/39609/)

### shadowsocks客户端安装（脚本）

* 配置conf

```
zhengzy@zhengzy-pc:~/md$ cat /etc/shadowsocks.json 
{
    "server":"ip",
    "server_port":port,
    "local_address":"127.0.0.1",
    "local_port":1080,
    "password":"asdfghjk",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open":false
}
```

* 运行脚本

```
#！/bin/bash
#shadow.sh
nohup sslocal -c /etc/shadowsocks.json > sslocal.log 2>&1 & 
```
### shadowsocks客户端安装（qt5客户端）

通过PPA源安装，仅支持Ubuntu 14.04或更高版本。

```
sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt-get update
sudo apt-get install shadowsocks-qt5
```

其他请参考：https://github.com/shadowsocks/shadowsocks-qt5/wiki/%E5%AE%89%E8%A3%85%E6%8C%87%E5%8D%97

### 安装proxychains

```
sudo apt install proxychains
```

### 配置proxychains

编辑/etc/proxychains.conf，最下面有一行socks4 127.0.0.1 9050，把这一行注释掉，添加一行socks5 127.0.0.1 1080

### 测试

```
proxychains curl www.google.com
```

如果能看到一堆输出，说明设置成功，如果一直等待或者无法访问则代表设置失败。

### 使用

用命令行启动软件，在前面加上proxychains，如：

```proxychains chromium-browser```

使用shadowsocks+proxychains代理打开新的谷歌浏览器实现浏览器翻墙。 

也可以通过输入proxychains bash建立一个新的shell，基于这个shell运行的所有命令都将使用代理。



### 版权声明
作者：苏陌年
出处：http://www.zhengzy.top/ 
版权所有，欢迎保留原文链接进行转载：)
