---
title: docker入门
description: 开发人员必须掌握的docker简单使用
tags:
  - Docker
categories: tech
abbrlink: 37956
date: 2017-04-10 17:21:24
---

### ubuntu16.04安装docker

```
root@VM-64-131-ubuntu:~# sudo apt-get install docker.io
```

### 查看当前版本
```
root@VM-64-131-ubuntu:~# docker version
Client:
Version:      1.12.6
API version:  1.24
Go version:   go1.6.2
Git commit:   78d1802
Built:        Tue Jan 31 23:35:14 2017
OS/Arch:      linux/amd64

Server:
Version:      1.12.6
API version:  1.24
Go version:   go1.6.2
Git commit:   78d1802
Built:        Tue Jan 31 23:35:14 2017
OS/Arch:      linux/amd64
```

### 检索可用的镜像

你可以通过浏览这个网页来查找你想要使用的镜像，或者使用命令行的工具来检索。

[docker镜像检索网站](index.docker.io)

```
root@VM-64-131-ubuntu:~# docker search learn/tutorial
NAME                         DESCRIPTION                          STARS     OFFICIAL   AUTOMATED
learn/tutorial                                                    25                   
danmaq/tutorial-learn-ping   from learn/tutorial + install ping   0  
```

### 下载容器镜像

执行pull命令的时候要写完整的名字，比如"learn/tutorial"。

```
root@VM-64-131-ubuntu:~# docker pull learn/tutorial
Using default tag: latest
latest: Pulling from learn/tutorial
271134aeb542: Pull complete 
Digest: sha256:2933b82e7c2a72ad8ea89d58af5d1472e35dacd5b7233577483f58ff8f9338bd
Status: Downloaded newer image for learn/tutorial:latest
```

### 在docker容器中运行hello world!

docker run命令有两个参数，一个是镜像名，一个是要在镜像中运行的命令。

```
root@VM-64-131-ubuntu:~# docker run learn/tutorial echo "hello"
hello
```

### 在容器中安装新的程序

目标：learn/tutorial镜像里面安装ping程序。

提示：在执行apt-get 命令的时候，要带上-y参数。如果不指定-y参数的话，apt-get命令会进入交互模式，需要用户输入命令来进行确认，但在docker环境中是无法响应这种交互的。

```
root@VM-64-131-ubuntu:~# docker run learn/tutorial apt-get install -y ping
Reading package lists...
Building dependency tree...
The following NEW packages will be installed:
iputils-ping
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 56.1 kB of archives.
After this operation, 143 kB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu/ precise/main iputils-ping amd64 3:20101006-1ubuntu1 [56.1 kB]
debconf: delaying package configuration, since apt-utils is not installed
Fetched 56.1 kB in 2s (19.7 kB/s)
Selecting previously unselected package iputils-ping.
(Reading database ... 7545 files and directories currently installed.)
Unpacking iputils-ping (from .../iputils-ping_3%3a20101006-1ubuntu1_amd64.deb) ...
Setting up iputils-ping (3:20101006-1ubuntu1) ...
```

### 保存对容器的修改

当你对某一个容器做了修改之后（通过在容器中运行某一个命令），可以把对容器的修改保存下来，这样下次可以从保存后的最新状态运行该容器。docker中保存状态的过程称之为committing，它保存的新旧状态之间的区别，从而产生一个新的版本。

目标：首先使用docker ps -l命令获得安装完ping命令之后容器的id。然后把这个镜像保存为learn/ping。
提示：
1. 运行docker commit，可以查看该命令的参数列表。
2. 你需要指定要提交保存容器的ID。(译者按：通过docker ps -l 命令获得)
3. 无需拷贝完整的id，通常来讲最开始的三至四个字母即可区分。（译者按：非常类似git里面的版本号)
4. 使用docker images，可以查看提交的容器。

```
root@VM-64-131-ubuntu:~# docker ps -l
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS                          PORTS               NAMES
9f022f9f4cb2        learn/tutorial      "apt-get install -y p"   About a minute ago   Exited (0) About a minute ago                       small_brahmagupta
root@VM-64-131-ubuntu:~# docker commit 9f0 learn/ping
sha256:95027d996397a731d98a8a7a2b87d34feed9118324c36017fd22253257464db5
root@VM-64-131-ubuntu:~# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
learn/ping          latest              95027d996397        29 seconds ago      139.5 MB
learn/tutorial      latest              a7876479f1aa        3 years ago         128 MB
```

### 运行新的镜像

```
root@VM-64-131-ubuntu:~# docker run learn/ping ping www.qq.com
PING www.qq.com (101.226.103.106) 56(84) bytes of data.
64 bytes from 101.226.103.106: icmp_req=1 ttl=55 time=30.9 ms
64 bytes from 101.226.103.106: icmp_req=2 ttl=55 time=31.3 ms
^C64 bytes from 101.226.103.106: icmp_req=3 ttl=55 time=31.0 ms

--- www.qq.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2002ms
rtt min/avg/max/mdev = 30.947/31.124/31.341/0.260 ms
```

### 检查运行中的镜像

目标：查找某一个运行中容器的id，然后使用docker inspect命令查看容器的信息。
提示：可以使用镜像id的前面部分，不需要完整的id。

```
root@VM-64-131-ubuntu:~/soft# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
41e85a5f867a        learn/ping          "ping www.qq.com"   5 seconds ago       Up 5 seconds                            gigantic_nobel
root@VM-64-131-ubuntu:~/soft# docker inspect 41e
[
    {
        "Id": "41e85a5f867a6a42d9feee2835d294238395ec517088b74b70f312d242e3553a",
        "Created": "2017-03-30T08:03:34.08380137Z",
        "Path": "ping",
        "Args": [
            "www.qq.com"
        ],
```

### 发布自己的镜像

发布docker镜像：现在我们已经验证了新镜像可以正常工作，下一步我们可以将其发布到官方的索引网站。还记得我们最开始下载的learn/tutorial镜像吧，我们也可以把我们自己编译的镜像发布到索引页面，一方面可以自己重用，另一方面也可以分享给其他人使用。

目标：把learn/ping镜像发布到docker的index网站。
提示：
1. docker images命令可以列出所有安装过的镜像。
2. docker push命令可以将某一个镜像发布到官方网站。
3. 你只能将镜像发布到自己的空间下面。这个模拟器登录的是learn帐号。

```
root@VM-64-131-ubuntu:~/soft# docker push learn/ping
The push refers to a repository [docker.io/learn/ping]
14671d39691e: Retrying in 1 second 
ee1ba0cc9b81: Retrying in 2 seconds 
unauthorized: authentication required
```

注意这里需要授权。


### 版权声明
作者：苏陌年
出处：http://www.zhengzy.top/ 
版权所有，欢迎保留原文链接进行转载：)
